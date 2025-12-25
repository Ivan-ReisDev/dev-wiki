# React Cheatsheet

## Criando um Projeto

### Create React App

```bash
# Criar novo projeto
npx create-react-app meu-app

# Com TypeScript
npx create-react-app meu-app --template typescript

# Iniciar servidor de desenvolvimento
npm start

# Build para produção
npm run build
```

### Vite (Recomendado - Mais Rápido)

```bash
# Criar novo projeto
npm create vite@latest meu-app -- --template react

# Com TypeScript
npm create vite@latest meu-app -- --template react-ts

# Instalar dependências
cd meu-app
npm install

# Iniciar servidor
npm run dev
```

## Componentes

### Componente Funcional

```tsx
// Componente básico
function MeuComponente(): JSX.Element {
  return <h1>Olá, Mundo!</h1>;
}

// Com props
interface SaudacaoProps {
  nome: string;
  idade: number;
}

function Saudacao({ nome, idade }: SaudacaoProps): JSX.Element {
  return (
    <div>
      <h1>Olá, {nome}!</h1>
      <p>Você tem {idade} anos</p>
    </div>
  );
}

// Com destructuring e valores padrão
interface BotaoProps {
  texto?: string;
  onClick: () => void;
}

function Botao({ texto = 'Clique aqui', onClick }: BotaoProps): JSX.Element {
  return <button onClick={onClick}>{texto}</button>;
}

// Arrow function
interface CardProps {
  titulo: string;
  descricao: string;
}

const Card = ({ titulo, descricao }: CardProps): JSX.Element => (
  <div className="card">
    <h2>{titulo}</h2>
    <p>{descricao}</p>
  </div>
);
```

### Props

```tsx
// Passando props
<Saudacao nome="João" idade={25} />

// Props children
interface ContainerProps {
  children: React.ReactNode;
}

function Container({ children }: ContainerProps): JSX.Element {
  return <div className="container">{children}</div>;
}

<Container>
  <h1>Título</h1>
  <p>Conteúdo</p>
</Container>

// Spread props
const props: SaudacaoProps = { nome: 'Maria', idade: 30 };
<Saudacao {...props} />

// TypeScript substitui PropTypes
interface MeuComponenteProps {
  nome: string;
  idade?: number;
  onClick?: () => void;
}

// Uso do componente tipado
function MeuComponente({ nome, idade, onClick }: MeuComponenteProps): JSX.Element {
  return <div>{nome}</div>;
}
```

## Hooks

### useState - Estado

```tsx
import { useState } from 'react';

function Contador(): JSX.Element {
  // Declarar estado (tipo inferido automaticamente)
  const [count, setCount] = useState<number>(0);

  // Múltiplos estados
  const [nome, setNome] = useState<string>('');
  const [ativo, setAtivo] = useState<boolean>(false);

  // Estado com objeto (definir interface)
  interface Usuario {
    nome: string;
    email: string;
  }

  const [usuario, setUsuario] = useState<Usuario>({
    nome: '',
    email: ''
  });

  // Atualizar estado
  const incrementar = (): void => setCount(count + 1);

  // Atualizar com função (recomendado)
  const incrementarSeguro = (): void => setCount(prev => prev + 1);

  // Atualizar objeto (manter outros campos)
  const atualizarNome = (novoNome: string): void => {
    setUsuario(prev => ({ ...prev, nome: novoNome }));
  };

  return (
    <div>
      <p>Contagem: {count}</p>
      <button onClick={incrementar}>+1</button>
    </div>
  );
}
```

### useEffect - Efeitos Colaterais

```tsx
import { useState, useEffect } from 'react';

interface ApiData {
  // Definir estrutura dos dados da API
  id: number;
  nome: string;
}

function ExemploEffect(): JSX.Element {
  const [data, setData] = useState<ApiData | null>(null);
  const [count, setCount] = useState<number>(0);

  // Executa após cada renderização
  useEffect(() => {
    console.log('Componente renderizado');
  });

  // Executa apenas na montagem (componentDidMount)
  useEffect(() => {
    console.log('Componente montado');
  }, []); // Array vazio

  // Executa quando count muda
  useEffect(() => {
    console.log('Count mudou:', count);
  }, [count]); // Dependências

  // Cleanup (componentWillUnmount)
  useEffect(() => {
    const timer: NodeJS.Timeout = setInterval(() => {
      console.log('Timer rodando');
    }, 1000);

    // Função de limpeza
    return () => {
      clearInterval(timer);
      console.log('Limpeza executada');
    };
  }, []);

  // Fetch de dados
  useEffect(() => {
    const fetchData = async (): Promise<void> => {
      try {
        const response = await fetch('https://api.exemplo.com/data');
        const json: ApiData = await response.json();
        setData(json);
      } catch (error) {
        console.error('Erro:', error);
      }
    };

    fetchData();
  }, []);

  return <div>{/* ... */}</div>;
}
```

### useContext - Contexto

```tsx
import { createContext, useContext, useState, ReactNode } from 'react';

// Definir tipo do contexto
interface TemaContextType {
  tema: 'claro' | 'escuro';
  toggleTema: () => void;
}

// Criar contexto com valor padrão
const TemaContext = createContext<TemaContextType | undefined>(undefined);

// Provider
interface TemaProviderProps {
  children: ReactNode;
}

function TemaProvider({ children }: TemaProviderProps): JSX.Element {
  const [tema, setTema] = useState<'claro' | 'escuro'>('claro');

  const toggleTema = (): void => {
    setTema(prev => prev === 'claro' ? 'escuro' : 'claro');
  };

  return (
    <TemaContext.Provider value={{ tema, toggleTema }}>
      {children}
    </TemaContext.Provider>
  );
}

// Hook customizado para usar o contexto
function useTema(): TemaContextType {
  const context = useContext(TemaContext);
  if (!context) {
    throw new Error('useTema deve ser usado dentro de TemaProvider');
  }
  return context;
}

// Consumir contexto
function Botao(): JSX.Element {
  const { tema, toggleTema } = useTema();

  return (
    <button
      onClick={toggleTema}
      className={tema}
    >
      Tema: {tema}
    </button>
  );
}

// Usar no App
function App(): JSX.Element {
  return (
    <TemaProvider>
      <Botao />
    </TemaProvider>
  );
}
```

### useReducer - Estado Complexo

```tsx
import { useReducer } from 'react';

// Definir tipos
interface State {
  count: number;
}

type Action =
  | { type: 'incrementar' }
  | { type: 'decrementar' }
  | { type: 'reset' };

// Reducer
function contadorReducer(state: State, action: Action): State {
  switch (action.type) {
    case 'incrementar':
      return { count: state.count + 1 };
    case 'decrementar':
      return { count: state.count - 1 };
    case 'reset':
      return { count: 0 };
    default:
      throw new Error('Ação desconhecida');
  }
}

function Contador(): JSX.Element {
  const [state, dispatch] = useReducer(contadorReducer, { count: 0 });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'incrementar' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrementar' })}>-</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </div>
  );
}
```

### useRef - Referências

```tsx
import { useRef, useEffect } from 'react';

function ExemploRef(): JSX.Element {
  const inputRef = useRef<HTMLInputElement>(null);
  const countRef = useRef<number>(0);

  // Focar input na montagem
  useEffect(() => {
    inputRef.current?.focus();
  }, []);

  // Armazenar valor sem re-renderizar
  const incrementar = (): void => {
    countRef.current += 1;
    console.log('Count (não renderiza):', countRef.current);
  };

  const focarInput = (): void => {
    inputRef.current?.focus();
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={focarInput}>
        Focar Input
      </button>
      <button onClick={incrementar}>
        Incrementar (sem render)
      </button>
    </div>
  );
}
```

### useMemo - Memoização de Valores

```tsx
import { useMemo, useState } from 'react';

function ExemploMemo(): JSX.Element {
  const [count, setCount] = useState<number>(0);
  const [items, setItems] = useState<number[]>([1, 2, 3, 4, 5]);

  // Cálculo pesado - só recalcula quando items muda
  const total = useMemo<number>(() => {
    console.log('Calculando total...');
    return items.reduce((acc, item) => acc + item, 0);
  }, [items]);

  return (
    <div>
      <p>Total: {total}</p>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Incrementar</button>
    </div>
  );
}
```

### useCallback - Memoização de Funções

```tsx
import { useCallback, useState } from 'react';

interface ListaProps {
  onItemClick: (item: string) => void;
}

function Lista({ onItemClick }: ListaProps): JSX.Element {
  // Componente filho
  return <div>{/* ... */}</div>;
}

function Parent(): JSX.Element {
  const [count, setCount] = useState<number>(0);

  // Sem useCallback, função é recriada a cada render
  // Com useCallback, função é memoizada
  const handleClick = useCallback((item: string): void => {
    console.log('Item clicado:', item);
  }, []); // Dependências vazias = função nunca muda

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
      <Lista onItemClick={handleClick} />
    </div>
  );
}
```

### Custom Hooks

```tsx
// useFetch - Hook customizado
import { useState, useEffect } from 'react';

interface UseFetchResult<T> {
  data: T | null;
  loading: boolean;
  error: string | null;
}

function useFetch<T>(url: string): UseFetchResult<T> {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchData = async (): Promise<void> => {
      try {
        setLoading(true);
        const response = await fetch(url);
        const json: T = await response.json();
        setData(json);
        setError(null);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Erro desconhecido');
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
}

// Usar o hook customizado
interface User {
  id: number;
  name: string;
  email: string;
}

function MeuComponente(): JSX.Element {
  const { data, loading, error } = useFetch<User[]>('https://api.exemplo.com/users');

  if (loading) return <p>Carregando...</p>;
  if (error) return <p>Erro: {error}</p>;

  return <div>{JSON.stringify(data)}</div>;
}

// useLocalStorage - Persistir estado
function useLocalStorage<T>(key: string, initialValue: T): [T, (value: T) => void] {
  const [value, setValue] = useState<T>(() => {
    const item = localStorage.getItem(key);
    return item ? JSON.parse(item) : initialValue;
  });

  const setStoredValue = (newValue: T): void => {
    setValue(newValue);
    localStorage.setItem(key, JSON.stringify(newValue));
  };

  return [value, setStoredValue];
}

// Usar
function App(): JSX.Element {
  const [nome, setNome] = useLocalStorage<string>('nome', '');

  return (
    <input
      value={nome}
      onChange={(e: React.ChangeEvent<HTMLInputElement>) => setNome(e.target.value)}
    />
  );
}
```

## Eventos

```tsx
import React from 'react';

function Eventos(): JSX.Element {
  // Click
  const handleClick = (e: React.MouseEvent<HTMLButtonElement>): void => {
    console.log('Clicou!', e);
  };

  // Com parâmetros
  const handleClickParam = (id: number): void => {
    console.log('ID:', id);
  };

  // Submit de formulário
  const handleSubmit = (e: React.FormEvent<HTMLFormElement>): void => {
    e.preventDefault(); // Prevenir reload da página
    console.log('Formulário enviado');
  };

  // Change
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>): void => {
    console.log(e.target.value);
  };

  // Keyboard
  const handleKeyPress = (e: React.KeyboardEvent<HTMLInputElement>): void => {
    if (e.key === 'Enter') {
      console.log('Enter pressionado');
    }
  };

  return (
    <div>
      {/* Click simples */}
      <button onClick={handleClick}>Clique</button>

      {/* Click com parâmetro */}
      <button onClick={() => handleClickParam(123)}>
        Clique com ID
      </button>

      {/* Formulário */}
      <form onSubmit={handleSubmit}>
        <input type="text" onChange={handleChange} />
        <button type="submit">Enviar</button>
      </form>

      {/* Keyboard */}
      <input onKeyPress={handleKeyPress} />

      {/* Mouse events */}
      <div
        onMouseEnter={() => console.log('Mouse entrou')}
        onMouseLeave={() => console.log('Mouse saiu')}
      >
        Passe o mouse aqui
      </div>
    </div>
  );
}
```

## Renderização Condicional

```tsx
import { useState } from 'react';

type Role = 'admin' | 'user' | 'guest';

function Condicional(): JSX.Element {
  const [isLoggedIn, setIsLoggedIn] = useState<boolean>(false);
  const [role, setRole] = useState<Role>('user');

  // if/else simples com &&
  return (
    <div>
      {isLoggedIn && <p>Bem-vindo!</p>}

      {/* Ternário */}
      {isLoggedIn ? <p>Logado</p> : <p>Não logado</p>}

      {/* Múltiplas condições */}
      {role === 'admin' && <button>Admin Panel</button>}
      {role === 'user' && <button>User Panel</button>}

      {/* Retorno antecipado */}
      {!isLoggedIn && <Login />}
      {isLoggedIn && <Dashboard />}
    </div>
  );
}

// Com componente separado
type Status = 'loading' | 'error' | 'success' | 'idle';

interface MensagemStatusProps {
  status: Status;
}

function MensagemStatus({ status }: MensagemStatusProps): JSX.Element | null {
  if (status === 'loading') return <p>Carregando...</p>;
  if (status === 'error') return <p>Erro!</p>;
  if (status === 'success') return <p>Sucesso!</p>;
  return null;
}
```

## Listas e Keys

```tsx
interface Usuario {
  id: number;
  nome: string;
  idade: number;
}

interface CardUsuarioProps {
  usuario: Usuario;
}

function CardUsuario({ usuario }: CardUsuarioProps): JSX.Element {
  return (
    <div className="card">
      <h3>{usuario.nome}</h3>
      <p>{usuario.idade} anos</p>
    </div>
  );
}

function Listas(): JSX.Element {
  const usuarios: Usuario[] = [
    { id: 1, nome: 'João', idade: 25 },
    { id: 2, nome: 'Maria', idade: 30 },
    { id: 3, nome: 'Pedro', idade: 35 }
  ];

  return (
    <div>
      {/* Map básico */}
      <ul>
        {usuarios.map((usuario: Usuario) => (
          <li key={usuario.id}>
            {usuario.nome} - {usuario.idade} anos
          </li>
        ))}
      </ul>

      {/* Com componente */}
      {usuarios.map((usuario: Usuario) => (
        <CardUsuario
          key={usuario.id}
          usuario={usuario}
        />
      ))}

      {/* Com index (use apenas se não houver id único) */}
      {usuarios.map((usuario: Usuario, index: number) => (
        <li key={index}>{usuario.nome}</li>
      ))}

      {/* Filter + Map */}
      {usuarios
        .filter((u: Usuario) => u.idade > 25)
        .map((u: Usuario) => (
          <li key={u.id}>{u.nome}</li>
        ))
      }
    </div>
  );
}
```

## Formulários

```tsx
import React, { useState } from 'react';

interface FormData {
  nome: string;
  email: string;
  senha: string;
  aceito: boolean;
  genero: string;
  pais: string;
  mensagem: string;
}

function Formulario(): JSX.Element {
  const [formData, setFormData] = useState<FormData>({
    nome: '',
    email: '',
    senha: '',
    aceito: false,
    genero: '',
    pais: 'br',
    mensagem: ''
  });

  // Atualizar campo individual
  const handleChange = (
    e: React.ChangeEvent<HTMLInputElement | HTMLSelectElement | HTMLTextAreaElement>
  ): void => {
    const target = e.target as HTMLInputElement;
    const { name, value, type } = target;
    const checked = target.checked;

    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value
    }));
  };

  // Submit
  const handleSubmit = (e: React.FormEvent<HTMLFormElement>): void => {
    e.preventDefault();
    console.log('Dados:', formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Input text */}
      <input
        type="text"
        name="nome"
        value={formData.nome}
        onChange={handleChange}
        placeholder="Nome"
      />

      {/* Input email */}
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />

      {/* Input password */}
      <input
        type="password"
        name="senha"
        value={formData.senha}
        onChange={handleChange}
        placeholder="Senha"
      />

      {/* Checkbox */}
      <label>
        <input
          type="checkbox"
          name="aceito"
          checked={formData.aceito}
          onChange={handleChange}
        />
        Aceito os termos
      </label>

      {/* Radio */}
      <label>
        <input
          type="radio"
          name="genero"
          value="m"
          checked={formData.genero === 'm'}
          onChange={handleChange}
        />
        Masculino
      </label>
      <label>
        <input
          type="radio"
          name="genero"
          value="f"
          checked={formData.genero === 'f'}
          onChange={handleChange}
        />
        Feminino
      </label>

      {/* Select */}
      <select
        name="pais"
        value={formData.pais}
        onChange={handleChange}
      >
        <option value="br">Brasil</option>
        <option value="pt">Portugal</option>
        <option value="us">EUA</option>
      </select>

      {/* Textarea */}
      <textarea
        name="mensagem"
        value={formData.mensagem}
        onChange={handleChange}
        placeholder="Mensagem"
      />

      <button type="submit">Enviar</button>
    </form>
  );
}
```

## Estilização

```tsx
import React from 'react';

// CSS Inline
function InlineStyle(): JSX.Element {
  const estilo: React.CSSProperties = {
    color: 'blue',
    fontSize: '20px',
    backgroundColor: 'lightgray'
  };

  return (
    <div style={estilo}>
      <h1 style={{ color: 'red', margin: '10px' }}>Título</h1>
    </div>
  );
}

// CSS Modules
import styles from './MeuComponente.module.css';

function ComModules(): JSX.Element {
  return (
    <div className={styles.container}>
      <h1 className={styles.titulo}>Título</h1>
    </div>
  );
}

// Classe condicional
interface ClasseCondicionalProps {
  ativo: boolean;
}

function ClasseCondicional({ ativo }: ClasseCondicionalProps): JSX.Element {
  return (
    <div className={ativo ? 'ativo' : 'inativo'}>
      Conteúdo
    </div>
  );
}

// Múltiplas classes
interface MultiClassesProps {
  ativo: boolean;
  erro: boolean;
}

function MultiClasses({ ativo, erro }: MultiClassesProps): JSX.Element {
  const classes = `botao ${ativo ? 'ativo' : ''} ${erro ? 'erro' : ''}`;

  return <button className={classes}>Botão</button>;
}

// Com classnames (biblioteca)
import classNames from 'classnames';

function ComClassNames({ ativo, erro }: MultiClassesProps): JSX.Element {
  return (
    <button className={classNames('botao', { ativo, erro })}>
      Botão
    </button>
  );
}
```

## React Router

```bash
# Instalar
npm install react-router-dom
```

```tsx
import {
  BrowserRouter,
  Routes,
  Route,
  Link,
  useNavigate,
  useParams,
  Navigate
} from 'react-router-dom';
import { ReactNode } from 'react';

function App(): JSX.Element {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/sobre">Sobre</Link>
        <Link to="/usuarios">Usuários</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/sobre" element={<Sobre />} />
        <Route path="/usuarios" element={<Usuarios />} />
        <Route path="/usuarios/:id" element={<Usuario />} />
        <Route path="/admin" element={<Admin />} />
        <Route path="*" element={<NaoEncontrada />} />
      </Routes>
    </BrowserRouter>
  );
}

// Usar parâmetros da URL
function Usuario(): JSX.Element {
  const { id } = useParams<{ id: string }>();
  return <h1>Usuário ID: {id}</h1>;
}

// Navegação programática
function Login(): JSX.Element {
  const navigate = useNavigate();

  const handleLogin = (): void => {
    // Após login bem-sucedido
    navigate('/dashboard');

    // Navegar com replace (não adiciona ao histórico)
    navigate('/dashboard', { replace: true });

    // Voltar
    navigate(-1);
  };

  return <button onClick={handleLogin}>Login</button>;
}

// Rota protegida
interface RotaProtegidaProps {
  children: ReactNode;
}

function RotaProtegida({ children }: RotaProtegidaProps): JSX.Element {
  const isAutenticado = true; // verificar autenticação

  if (!isAutenticado) {
    return <Navigate to="/login" replace />;
  }

  return <>{children}</>;
}

// Usar rota protegida
<Route
  path="/admin"
  element={
    <RotaProtegida>
      <Admin />
    </RotaProtegida>
  }
/>
```

## Axios (HTTP Requests)

```bash
# Instalar
npm install axios
```

```tsx
import axios, { AxiosError, InternalAxiosRequestConfig, AxiosResponse } from 'axios';
import { useState, useEffect } from 'react';

// Definir tipos
interface Usuario {
  id: number;
  nome: string;
  email: string;
}

// Configurar instância
const api = axios.create({
  baseURL: 'https://api.exemplo.com',
  timeout: 5000,
  headers: {
    'Content-Type': 'application/json'
  }
});

function ExemploAxios(): JSX.Element {
  const [usuarios, setUsuarios] = useState<Usuario[]>([]);
  const [loading, setLoading] = useState<boolean>(false);

  // GET
  useEffect(() => {
    const fetchUsuarios = async (): Promise<void> => {
      try {
        setLoading(true);
        const response = await api.get<Usuario[]>('/usuarios');
        setUsuarios(response.data);
      } catch (error) {
        console.error('Erro:', error);
      } finally {
        setLoading(false);
      }
    };

    fetchUsuarios();
  }, []);

  // POST
  const criarUsuario = async (novoUsuario: Omit<Usuario, 'id'>): Promise<void> => {
    try {
      const response = await api.post<Usuario>('/usuarios', novoUsuario);
      setUsuarios([...usuarios, response.data]);
    } catch (error) {
      console.error('Erro ao criar:', error);
    }
  };

  // PUT
  const atualizarUsuario = async (id: number, dadosAtualizados: Partial<Usuario>): Promise<void> => {
    try {
      await api.put<Usuario>(`/usuarios/${id}`, dadosAtualizados);
      // Atualizar estado local
    } catch (error) {
      console.error('Erro ao atualizar:', error);
    }
  };

  // DELETE
  const deletarUsuario = async (id: number): Promise<void> => {
    try {
      await api.delete(`/usuarios/${id}`);
      setUsuarios(usuarios.filter(u => u.id !== id));
    } catch (error) {
      console.error('Erro ao deletar:', error);
    }
  };

  return <div>{/* Renderizar usuários */}</div>;
}

// Interceptors
api.interceptors.request.use(
  (config: InternalAxiosRequestConfig): InternalAxiosRequestConfig => {
    // Adicionar token de autenticação
    const token = localStorage.getItem('token');
    if (token && config.headers) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error: AxiosError) => Promise.reject(error)
);

api.interceptors.response.use(
  (response: AxiosResponse): AxiosResponse => response,
  (error: AxiosError) => {
    if (error.response?.status === 401) {
      // Redirecionar para login
    }
    return Promise.reject(error);
  }
);
```

## Lazy Loading e Code Splitting

```tsx
import { lazy, Suspense } from 'react';
import { Routes, Route } from 'react-router-dom';

// Lazy loading de componente
const Dashboard = lazy(() => import('./Dashboard'));
const Perfil = lazy(() => import('./Perfil'));

function App(): JSX.Element {
  return (
    <div>
      <Suspense fallback={<div>Carregando...</div>}>
        <Dashboard />
      </Suspense>

      <Suspense fallback={<Spinner />}>
        <Routes>
          <Route path="/perfil" element={<Perfil />} />
        </Routes>
      </Suspense>
    </div>
  );
}

function Spinner(): JSX.Element {
  return <div className="spinner">Carregando...</div>;
}
```

## Performance

```tsx
import { memo } from 'react';

interface MeuComponenteProps {
  nome: string;
}

// React.memo - Evitar re-render desnecessário
const MeuComponente = memo(function MeuComponente({ nome }: MeuComponenteProps): JSX.Element {
  console.log('Renderizou');
  return <h1>{nome}</h1>;
});

// Com comparação customizada
interface Usuario {
  id: number;
  nome: string;
}

interface MeuComponenteCustomProps {
  usuario: Usuario;
}

const MeuComponenteCustom = memo(
  function MeuComponente({ usuario }: MeuComponenteCustomProps): JSX.Element {
    return <h1>{usuario.nome}</h1>;
  },
  (prevProps: MeuComponenteCustomProps, nextProps: MeuComponenteCustomProps): boolean => {
    // Retornar true se props são iguais (não renderizar)
    return prevProps.usuario.id === nextProps.usuario.id;
  }
);
```

## Dicas e Boas Práticas

- **Sempre use keys únicas** em listas (preferir IDs únicos ao invés de index)
- **Evite criar funções inline** em props se o componente filho usar React.memo
- **Use useCallback e useMemo** apenas quando necessário (não otimize prematuramente)
- **Mantenha componentes pequenos** e com responsabilidade única
- **Prefira composição** ao invés de herança
- **Use TypeScript** para type safety em projetos grandes
- **Organize por features** ao invés de tipo de arquivo (components/, hooks/, etc.)
- **Evite prop drilling** excessivo - use Context API ou state management
- **Normalize estado** quando trabalhar com dados relacionados
- **Coloque efeitos colaterais** no useEffect, não diretamente no corpo do componente
- **Use fragmentos** (`<>...</>`) para evitar divs desnecessárias
- **Valide props** com PropTypes ou TypeScript
- **Use Vite** para projetos novos (mais rápido que CRA)
- **Evite useState** para valores que não afetam renderização (use useRef)
- **Cleanup de efeitos** - sempre limpe timers, listeners, subscriptions
- **Nomenclatura** - use "handle" para funções de evento (handleClick, handleSubmit)
- **Componentes controlados** são preferíveis para formulários

## Estrutura de Pastas Recomendada

```
src/
├── components/         # Componentes reutilizáveis
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.module.css
│   │   ├── Button.types.ts
│   │   └── index.ts
│   └── ...
├── pages/             # Páginas/rotas
│   ├── Home/
│   ├── About/
│   └── ...
├── hooks/             # Custom hooks
│   ├── useFetch.ts
│   └── useAuth.ts
├── context/           # Context providers
│   └── AuthContext.tsx
├── services/          # APIs e serviços
│   └── api.ts
├── types/             # Tipos e interfaces globais
│   ├── user.types.ts
│   └── api.types.ts
├── utils/             # Funções utilitárias
│   └── helpers.ts
├── App.tsx
└── main.tsx
```

## Atalhos e Snippets Úteis

```tsx
// Importação rápida (atalho: imr)
import React from 'react';

// useState (atalho: ush)
const [state, setState] = useState<Type>(initialState);

// useEffect (atalho: uef)
useEffect(() => {
  // effect
  return () => {
    // cleanup
  };
}, [dependencies]);

// Componente funcional com TypeScript (atalho: tsrafce)
interface ComponentNameProps {
  // props
}

const ComponentName = ({}: ComponentNameProps): JSX.Element => {
  return (
    <div>

    </div>
  );
};

export default ComponentName;
```

## Ferramentas de Desenvolvimento

| Ferramenta | Descrição |
|------------|-----------|
| React DevTools | Extensão do navegador para debug |
| ESLint | Linter para código JavaScript/React |
| Prettier | Formatador de código |
| Vite | Build tool rápido |
| React Hook Form | Biblioteca para formulários |
| TanStack Query | Gerenciamento de estado assíncrono |
| Zustand | State management leve |

## Links Úteis

| Recurso | URL |
|---------|-----|
| Documentação Oficial | https://react.dev |
| Create React App | https://create-react-app.dev |
| Vite | https://vitejs.dev |
| React Router | https://reactrouter.com |
| React Hook Form | https://react-hook-form.com |
