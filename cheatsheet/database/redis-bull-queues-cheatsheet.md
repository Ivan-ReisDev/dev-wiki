# Redis + Bull Queues Cheatsheet

## Instalação

```bash
npm install bull redis
npm install -D @types/bull  # TypeScript
```

---

## 1. Reuso de Conexão Redis

### Singleton Pattern para Redis

```typescript
// redis-client.ts
import Redis from 'redis';

let redisClient: Redis.RedisClient | null = null;

export function getRedisClient(): Redis.RedisClient {
  if (!redisClient) {
    redisClient = new Redis({
      host: process.env.REDIS_HOST || 'localhost',
      port: parseInt(process.env.REDIS_PORT || '6379'),
      password: process.env.REDIS_PASSWORD,
      retryStrategy: (times) => Math.min(times * 50, 2000),
      enableReadyCheck: false,
      enableOfflineQueue: false,
    });

    redisClient.on('error', (err) => {
      console.error('Redis error:', err);
    });

    redisClient.on('connect', () => {
      console.log('Redis connected');
    });
  }

  return redisClient;
}

export function closeRedisClient(): Promise<void> {
  return new Promise((resolve) => {
    if (redisClient) {
      redisClient.quit(resolve);
      redisClient = null;
    } else {
      resolve();
    }
  });
}
```

### Usando redis com Bull

```typescript
// bull.ts
import Queue from 'bull';
import Redis from 'redis';
import { getRedisClient } from './redis-client';

const redisClient = getRedisClient();

export const emailQueue = new Queue('emails', {
  // Reutiliza a mesma conexão Redis
  createClient: (type: string) => {
    if (type === 'client') {
      return redisClient.duplicate();
    }
    if (type === 'subscriber') {
      return redisClient.duplicate();
    }
    return redisClient.duplicate();
  },
});

export const chatQueue = new Queue('chat', {
  redis: {
    host: process.env.REDIS_HOST || 'localhost',
    port: parseInt(process.env.REDIS_PORT || '6379'),
  },
});
```

### Configuração Avançada com Pool

```typescript
// redis-pool.ts
import Redis from 'redis';

class RedisPool {
  private clients: Map<string, Redis.RedisClient> = new Map();
  private config: Redis.ClientOpts;

  constructor(config: Redis.ClientOpts) {
    this.config = config;
  }

  getClient(name: string = 'default'): Redis.RedisClient {
    if (!this.clients.has(name)) {
      const client = new Redis(this.config);
      this.clients.set(name, client);
    }
    return this.clients.get(name)!;
  }

  async closeAll(): Promise<void> {
    const promises = Array.from(this.clients.values()).map(
      (client) =>
        new Promise<void>((resolve) => {
          client.quit(() => resolve());
        })
    );
    await Promise.all(promises);
    this.clients.clear();
  }
}

export const redisPool = new RedisPool({
  host: process.env.REDIS_HOST || 'localhost',
  port: parseInt(process.env.REDIS_PORT || '6379'),
  password: process.env.REDIS_PASSWORD,
});
```

---

## 2. Configuração de Fila Chat

### Queue Básica

```typescript
// queues/chat.queue.ts
import Queue from 'bull';
import { getRedisClient } from '../redis-client';

interface ChatJobData {
  userId: string;
  message: string;
  roomId: string;
  timestamp: number;
}

export const chatQueue = new Queue<ChatJobData>('chat', {
  defaultJobOptions: {
    attempts: 3,
    backoff: {
      type: 'exponential',
      delay: 2000,
    },
    removeOnComplete: true,
    removeOnFail: false,
  },
  redis: {
    host: process.env.REDIS_HOST || 'localhost',
    port: parseInt(process.env.REDIS_PORT || '6379'),
  },
});

// Processor
chatQueue.process(async (job) => {
  const { userId, message, roomId } = job.data;

  console.log(`Processing chat message from ${userId} in room ${roomId}`);

  // Processar mensagem
  await saveMessageToDatabase(userId, message, roomId);

  return { success: true, messageId: job.id };
});

// Event listeners
chatQueue.on('completed', (job) => {
  console.log(`Chat job ${job.id} completed`);
});

chatQueue.on('failed', (job, err) => {
  console.error(`Chat job ${job.id} failed:`, err.message);
});

chatQueue.on('stalled', (job) => {
  console.warn(`Chat job ${job.id} stalled`);
});
```

### Adicionando Jobs à Fila

```typescript
// services/chat.service.ts
import { chatQueue } from '../queues/chat.queue';

export class ChatService {
  async sendMessage(
    userId: string,
    message: string,
    roomId: string
  ): Promise<string> {
    const job = await chatQueue.add(
      {
        userId,
        message,
        roomId,
        timestamp: Date.now(),
      },
      {
        jobId: `${roomId}-${userId}-${Date.now()}`,
        priority: 10, // Jobs com prioridade alta
        delay: 0, // Sem delay
        attempts: 5, // Tentar 5 vezes
      }
    );

    return job.id;
  }

  async getJobStatus(jobId: string) {
    const job = await chatQueue.getJob(jobId);
    if (!job) return null;

    return {
      id: job.id,
      state: await job.getState(),
      progress: job.progress(),
      attempts: job.attemptsMade,
      failedReason: job.failedReason,
    };
  }
}
```

### Monitoramento da Fila

```typescript
// Contar jobs por estado
chatQueue.count().then((count) => {
  console.log(`Total jobs in queue: ${count}`);
});

// Jobs ativos
chatQueue.getActiveCount().then((count) => {
  console.log(`Active jobs: ${count}`);
});

// Jobs aguardando
chatQueue.getWaitingCount().then((count) => {
  console.log(`Waiting jobs: ${count}`);
});

// Jobs falhados
chatQueue.getFailedCount().then((count) => {
  console.log(`Failed jobs: ${count}`);
});

// Limpar jobs completos (cuidado!)
await chatQueue.clean(5000, 'completed'); // Remove jobs completados há 5s
```

---

## 3. Backoff e Retries

### Estratégias de Backoff

```typescript
// Exponential Backoff
const exponentialQueue = new Queue('exponential', {
  defaultJobOptions: {
    attempts: 5,
    backoff: {
      type: 'exponential',
      delay: 2000, // 2s, 4s, 8s, 16s, 32s
    },
  },
});

// Fixed Backoff
const fixedQueue = new Queue('fixed', {
  defaultJobOptions: {
    attempts: 3,
    backoff: {
      type: 'fixed',
      delay: 5000, // 5s entre cada tentativa
    },
  },
});

// Custom Backoff
const customQueue = new Queue('custom', {
  defaultJobOptions: {
    attempts: 4,
    backoff: {
      type: 'custom',
    },
  },
});

customQueue.process(async (job) => {
  // Custom logic
  throw new Error('Failed');
});

// Calcular delay customizado
customQueue.on('failed', (job, err) => {
  const delay = (job.attemptsMade + 1) * 1000; // 1s, 2s, 3s, 4s
  console.log(`Next retry in ${delay}ms`);
});
```

### Configuração por Job

```typescript
// Retry específico por job
await queue.add(
  { data: 'test' },
  {
    attempts: 10,
    backoff: {
      type: 'exponential',
      delay: 1000,
    },
    // Falhar após 24h
    timeout: 24 * 60 * 60 * 1000,
  }
);
```

### Handling de Retries

```typescript
queue.process(async (job) => {
  const { attemptsMade, data } = job;

  // Lógica diferente baseada em tentativa
  if (attemptsMade === 0) {
    console.log('First attempt');
  } else if (attemptsMade >= 3) {
    // Na 3ª tentativa, usar estratégia diferente
    console.log('Attempt', attemptsMade + 1, '- using backup strategy');
  }

  try {
    return await riskyOperation(data);
  } catch (error) {
    if (attemptsMade >= 2) {
      // Na 3ª tentativa, falhar sem retry
      throw new Error(`Operation failed after ${attemptsMade + 1} attempts`);
    }
    throw error; // Retry
  }
});

// Listener para falhas definitivas
queue.on('failed', (job, err) => {
  if (job.attemptsMade >= job.opts.attempts!) {
    console.error(`Job ${job.id} failed permanently:`, err.message);
    // Enviar notificação, salvar em DB, etc
  }
});
```

---

## 4. Jobs Idempotentes

### Padrão de Idempotência com Key Naming

```typescript
interface ProcessPaymentData {
  orderId: string;
  amount: number;
  customerId: string;
}

export const paymentQueue = new Queue<ProcessPaymentData>('payments', {
  redis: {
    host: 'localhost',
    port: 6379,
  },
});

paymentQueue.process(async (job) => {
  const { orderId, amount, customerId } = job.data;

  // Key idempotente baseada em dados únicos
  const idempotencyKey = `payment:${orderId}:${customerId}:${amount}`;

  // Verificar se já foi processado
  const redisClient = getRedisClient();
  const processed = await redisClient.get(idempotencyKey);

  if (processed) {
    console.log(`Payment already processed: ${idempotencyKey}`);
    return JSON.parse(processed); // Retornar resultado anterior
  }

  try {
    // Processar pagamento
    const result = await paymentService.process({
      orderId,
      amount,
      customerId,
    });

    // Marcar como processado
    await redisClient.setex(
      idempotencyKey,
      86400, // 24 horas
      JSON.stringify(result)
    );

    return result;
  } catch (error) {
    // Não armazenar erro, permitir retry
    throw error;
  }
});
```

### Job Idempotente com Database

```typescript
interface EmailData {
  userId: string;
  email: string;
  type: 'welcome' | 'reset' | 'confirm';
}

export const emailQueue = new Queue<EmailData>('emails', {
  defaultJobOptions: {
    attempts: 5,
    backoff: { type: 'exponential', delay: 2000 },
    removeOnComplete: true,
  },
});

emailQueue.process(async (job) => {
  const { userId, email, type } = job.data;

  // Usar jobId previsível e idempotente
  const jobId = `email:${type}:${userId}:${email}`;

  // Verificar se já processado na DB
  const existingEmail = await db.query(
    'SELECT id FROM emails WHERE idempotency_key = ?',
    [jobId]
  );

  if (existingEmail.length > 0) {
    console.log(`Email already sent: ${jobId}`);
    return { sent: false, reason: 'already_sent' };
  }

  try {
    const result = await emailService.send({
      to: email,
      type,
      userId,
    });

    // Registrar no banco
    await db.query(
      'INSERT INTO emails (user_id, email, type, idempotency_key, sent_at) VALUES (?, ?, ?, ?, ?)',
      [userId, email, type, jobId, new Date()]
    );

    return { sent: true, messageId: result.id };
  } catch (error) {
    throw error; // Permitir retry
  }
});

// Adicionar à fila com jobId previsível
await emailQueue.add(
  { userId: '123', email: 'user@example.com', type: 'welcome' },
  {
    jobId: `email:welcome:123:user@example.com`,
  }
);
```

### Validação de Idempotência

```typescript
// Middleware para validar idempotência
async function validateIdempotency(jobData: any): Promise<string> {
  // Criar hash baseado em dados importantes
  const crypto = require('crypto');

  const idempotencyKey = crypto
    .createHash('sha256')
    .update(JSON.stringify(jobData))
    .digest('hex');

  const redisClient = getRedisClient();

  // Verificar se existe
  const existing = await redisClient.get(`idempotent:${idempotencyKey}`);

  if (existing) {
    throw new Error('Duplicate job detected');
  }

  // Marcar como visto
  await redisClient.setex(`idempotent:${idempotencyKey}`, 3600, '1');

  return idempotencyKey;
}

queue.on('before:add', async (job) => {
  job.data.idempotencyKey = await validateIdempotency(job.data);
});
```

---

## 5. Key Naming Conventions

### Padrões de Nomenclatura

```typescript
// Jobs
const jobKey = 'chat:message:room-123:user-456';
const jobKey = 'email:welcome:user-789';
const jobKey = 'payment:order-321:attempt-1';

// Locks
const lockKey = 'lock:process:user-456';
const lockKey = 'lock:export:report-789';

// Idempotency
const idempotencyKey = 'idempotent:payment:order-123:user-456';
const idempotencyKey = 'idempotent:email:welcome:user-789';

// Rate limiting
const rateLimitKey = 'rate:send-email:user-456:2024-01-04';
const rateLimitKey = 'rate:api-call:ip-192.168.1.1:hourly';

// Cache
const cacheKey = 'cache:user:456:profile';
const cacheKey = 'cache:room:123:messages:page-2';

// Status
const statusKey = 'status:export:report-789';
const statusKey = 'status:sync:user-456';

// Session
const sessionKey = 'session:chat:room-123:user-456';
const sessionKey = 'session:active:user-456';
```

### Implementação de Key Builder

```typescript
// keys.ts
class KeyBuilder {
  static chat = {
    message: (roomId: string, userId: string) =>
      `chat:message:${roomId}:${userId}`,
    processing: (roomId: string) => `chat:processing:${roomId}`,
    lastSeen: (userId: string) => `chat:lastSeen:${userId}`,
  };

  static email = {
    sent: (userId: string, type: string) =>
      `email:sent:${userId}:${type}`,
    bounced: (email: string) => `email:bounced:${email}`,
    queue: (type: string) => `email:queue:${type}`,
  };

  static payment = {
    processing: (orderId: string) => `payment:processing:${orderId}`,
    retry: (orderId: string) => `payment:retry:${orderId}`,
    webhook: (transactionId: string) => `payment:webhook:${transactionId}`,
  };

  static rate = {
    limit: (action: string, userId: string, period: string) =>
      `rate:${action}:${userId}:${period}`,
  };

  static lock = {
    acquire: (resource: string) => `lock:${resource}`,
  };
}

// Uso
const messageKey = KeyBuilder.chat.message('room-123', 'user-456');
const emailKey = KeyBuilder.email.sent('user-789', 'welcome');
const paymentKey = KeyBuilder.payment.processing('order-321');
```

### Validação de Keys

```typescript
function validateKeyFormat(key: string): boolean {
  // Pattern: namespace:type:identifier
  const pattern = /^[a-z]+:[a-z]+:[a-z0-9\-_]+$/;
  return pattern.test(key);
}

// Validar antes de usar
const key = KeyBuilder.chat.message(roomId, userId);
if (!validateKeyFormat(key)) {
  throw new Error(`Invalid key format: ${key}`);
}
```

---

## 6. Exemplo Completo: Chat com Idempotência

```typescript
// types/chat.ts
export interface ChatMessage {
  id: string;
  roomId: string;
  userId: string;
  message: string;
  createdAt: Date;
}

export interface ChatJobData {
  messageId: string; // Identificador único do cliente
  roomId: string;
  userId: string;
  message: string;
  timestamp: number;
}

// queues/chat.queue.ts
import Queue from 'bull';
import { ChatJobData, ChatMessage } from '../types/chat';
import { getRedisClient } from '../redis-client';

export const chatQueue = new Queue<ChatJobData>('chat', {
  defaultJobOptions: {
    attempts: 5,
    backoff: {
      type: 'exponential',
      delay: 1000,
    },
    removeOnComplete: true,
    timeout: 30000,
  },
  redis: {
    host: process.env.REDIS_HOST || 'localhost',
    port: parseInt(process.env.REDIS_PORT || '6379'),
  },
});

// Processor
chatQueue.process(async (job) => {
  const { messageId, roomId, userId, message, timestamp } = job.data;

  // Key para idempotência
  const idempotencyKey = `chat:processed:${roomId}:${messageId}`;
  const redis = getRedisClient();

  // Verificar se já foi processado
  const existing = await redis.get(idempotencyKey);
  if (existing) {
    console.log(`Message already processed: ${idempotencyKey}`);
    return JSON.parse(existing);
  }

  try {
    // Salvar no banco de dados
    const savedMessage = await db.messages.create({
      id: messageId,
      roomId,
      userId,
      message,
      createdAt: new Date(timestamp),
    });

    // Armazenar no cache para idempotência
    await redis.setex(
      idempotencyKey,
      86400, // 24 horas
      JSON.stringify(savedMessage)
    );

    // Notificar usuários na sala via WebSocket
    await notifyRoomUsers(roomId, 'message:new', savedMessage);

    return savedMessage;
  } catch (error) {
    if (error.code === 'DUPLICATE_KEY') {
      // Se é chave duplicada, retornar resultado existente
      const existing = await db.messages.findById(messageId);
      return existing;
    }
    throw error; // Permitir retry
  }
});

// Event listeners
chatQueue.on('completed', (job) => {
  console.log(`✓ Chat message processed: ${job.data.messageId}`);
});

chatQueue.on('failed', (job, err) => {
  console.error(`✗ Chat message failed: ${job.data.messageId}`, err.message);

  if (job.attemptsMade >= job.opts.attempts!) {
    // Notificar usuário que falhou permanentemente
    notifyUser(job.data.userId, 'message:failed', {
      messageId: job.data.messageId,
    });
  }
});

chatQueue.on('stalled', (job) => {
  console.warn(`⚠ Chat message stalled: ${job.data.messageId}`);
});

// services/chat.service.ts
export class ChatService {
  async sendMessage(
    roomId: string,
    userId: string,
    message: string
  ): Promise<ChatMessage> {
    // Gerar ID único para a mensagem (no cliente idealmente)
    const messageId = generateUUID();

    // Adicionar à fila com jobId previsível
    const job = await chatQueue.add(
      {
        messageId,
        roomId,
        userId,
        message,
        timestamp: Date.now(),
      },
      {
        jobId: `chat:${roomId}:${messageId}`, // Job ID previsível
        priority: 10,
      }
    );

    return {
      id: messageId,
      roomId,
      userId,
      message,
      createdAt: new Date(),
    };
  }

  async getJobStatus(jobId: string) {
    const job = await chatQueue.getJob(jobId);
    if (!job) return null;

    return {
      id: job.id,
      state: await job.getState(),
      progress: job.progress(),
      data: job.data,
      failedReason: job.failedReason,
    };
  }
}
```

---

## 7. Best Practices

### Configuração de Produção

```typescript
const productionQueue = new Queue('production', {
  defaultJobOptions: {
    attempts: 5,
    backoff: {
      type: 'exponential',
      delay: 2000,
    },
    removeOnComplete: true,
    removeOnFail: false, // Manter para análise
    timeout: 60000, // 1 minuto
  },
  settings: {
    stalledInterval: 30000, // Verificar jobs travados a cada 30s
    maxStalledCount: 2, // Marcar como travado após 2 vezes
    lockDuration: 30000, // Lock de 30s
    lockRenewTime: 15000, // Renovar lock a cada 15s
    retryProcessDelay: 5000, // Esperar 5s antes de reprocessar
  },
  redis: {
    host: process.env.REDIS_HOST,
    port: parseInt(process.env.REDIS_PORT || '6379'),
    password: process.env.REDIS_PASSWORD,
    db: parseInt(process.env.REDIS_DB || '0'),
    maxRetriesPerRequest: null,
  },
});
```

### Cleanup e Maintenance

```typescript
// Limpar jobs periodicamente
setInterval(async () => {
  // Remover jobs completados após 7 dias
  await chatQueue.clean(7 * 24 * 60 * 60 * 1000, 'completed');

  // Remover jobs falhados após 30 dias
  await chatQueue.clean(30 * 24 * 60 * 60 * 1000, 'failed');
}, 24 * 60 * 60 * 1000); // Uma vez por dia

// Graceful shutdown
process.on('SIGTERM', async () => {
  await chatQueue.close();
  await closeRedisClient();
  process.exit(0);
});
```

### Monitoramento e Logging

```typescript
// Logger customizado
function setupQueueLogging(queue: Queue) {
  queue.on('active', (job) => {
    console.log(`[${queue.name}] Job ${job.id} started`);
  });

  queue.on('completed', (job, result) => {
    console.log(`[${queue.name}] Job ${job.id} completed in ${Date.now() - job.timestamp}ms`);
  });

  queue.on('failed', (job, err) => {
    console.error(`[${queue.name}] Job ${job.id} failed:`, {
      message: err.message,
      attempt: job.attemptsMade,
      nextRetry: job.nextRetryTime(),
    });
  });

  queue.on('error', (err) => {
    console.error(`[${queue.name}] Queue error:`, err);
  });
}
```

---

## 8. Troubleshooting

### Problemas Comuns

| Problema | Causa | Solução |
|----------|-------|---------|
| Jobs não processam | Processador não iniciado | Chamar `queue.process()` |
| Memória crescente | Jobs não removidos | Ativar `removeOnComplete: true` |
| Duplicatas | Sem idempotência | Usar jobId previsível + validação |
| Retries infinitos | Erro que sempre falha | Falhar após N tentativas |
| Queue travada | Lock expirado | Aumentar `lockDuration` |
| Conexão Redis perdida | Rede instável | Adicionar reconnection logic |

### Debugging

```typescript
// Listar todos os jobs
const allJobs = await chatQueue.getJobCounts();
console.log(allJobs);

// Inspecionar job específico
const job = await chatQueue.getJob('job-id');
console.log({
  id: job.id,
  state: await job.getState(),
  progress: job.progress(),
  data: job.data,
  stacktrace: job.stacktrace,
  failedReason: job.failedReason,
});

// Reprocessar job falhado
await job.retry();

// Remover job
await job.remove();
```

---

## Referências

- [Bull Documentation](https://github.com/OptimalBits/bull)
- [Redis Documentation](https://redis.io/docs/)
- [Job Queue Best Practices](https://github.com/OptimalBits/bull/blob/master/docs/guide/queues.md)
