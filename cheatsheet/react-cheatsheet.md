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

```jsx
// Componente básico
function MeuComponente() {
  return <h1>Olá, Mundo!</h1>;
}

// Com props
function Saudacao({ nome, idade }) {
  return (
    <div>
      <h1>Olá, {nome}!</h1>
      <p>Você tem {idade} anos</p>
    </div>
  );
}

// Com destructuring e valores padrão
function Botao({ texto = 'Clique aqui', onClick }) {
  return <button onClick={onClick}>{texto}</button>;
}

// Arrow function
const Card = ({ titulo, descricao }) => (
  <div className="card">
    <h2>{titulo}</h2>
    <p>{descricao}</p>
  </div>
);
```

### Props

```jsx
// Passando props
<Saudacao nome="João" idade={25} />

// Props children
function Container({ children }) {
  return <div className="container">{children}</div>;
}

<Container>
  <h1>Título</h1>
  <p>Conteúdo</p>
</Container>

// Spread props
const props = { nome: 'Maria', idade: 30 };
<Saudacao {...props} />

// PropTypes (validação)
import PropTypes from 'prop-types';

MeuComponente.propTypes = {
  nome: PropTypes.string.isRequired,
  idade: PropTypes.number,
  onClick: PropTypes.func
};
```

## Hooks

### useState - Estado

```jsx
import { useState } from 'react';

function Contador() {
  // Declarar estado
  const [count, setCount] = useState(0);

  // Múltiplos estados
  const [nome, setNome] = useState('');
  const [ativo, setAtivo] = useState(false);

  // Estado com objeto
  const [usuario, setUsuario] = useState({
    nome: '',
    email: ''
  });

  // Atualizar estado
  const incrementar = () => setCount(count + 1);

  // Atualizar com função (recomendado)
  const incrementarSeguro = () => setCount(prev => prev + 1);

  // Atualizar objeto (manter outros campos)
  const atualizarNome = (novoNome) => {
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

```jsx
import { useState, useEffect } from 'react';

function ExemploEffect() {
  const [data, setData] = useState(null);
  const [count, setCount] = useState(0);

  // Executa após cada renderização
  useEffect(() => {
    console.log('Componente renderizado');
  });

  // Executa apenas na montagem (componentDidMount)
  useEffect(() => {
    console.log('Componente montado');
    fetchData();
  }, []); // Array vazio

  // Executa quando count muda
  useEffect(() => {
    console.log('Count mudou:', count);
  }, [count]); // Dependências

  // Cleanup (componentWillUnmount)
  useEffect(() => {
    const timer = setInterval(() => {
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
    const fetchData = async () => {
      try {
        const response = await fetch('https://api.exemplo.com/data');
        const json = await response.json();
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

```jsx
import { createContext, useContext, useState } from 'react';

// Criar contexto
const TemaContext = createContext();

// Provider
function TemaProvider({ children }) {
  const [tema, setTema] = useState('claro');

  const toggleTema = () => {
    setTema(prev => prev === 'claro' ? 'escuro' : 'claro');
  };

  return (
    <TemaContext.Provider value={{ tema, toggleTema }}>
      {children}
    </TemaContext.Provider>
  );
}

// Consumir contexto
function Botao() {
  const { tema, toggleTema } = useContext(TemaContext);

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
function App() {
  return (
    <TemaProvider>
      <Botao />
    </TemaProvider>
  );
}
```

### useReducer - Estado Complexo

```jsx
import { useReducer } from 'react';

// Reducer
function contadorReducer(state, action) {
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

function Contador() {
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

```jsx
import { useRef, useEffect } from 'react';

function ExemploRef() {
  const inputRef = useRef(null);
  const countRef = useRef(0);

  // Focar input na montagem
  useEffect(() => {
    inputRef.current.focus();
  }, []);

  // Armazenar valor sem re-renderizar
  const incrementar = () => {
    countRef.current += 1;
    console.log('Count (não renderiza):', countRef.current);
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={() => inputRef.current.focus()}>
        Focar Input
      </button>
    </div>
  );
}
```

### useMemo - Memoização de Valores

```jsx
import { useMemo, useState } from 'react';

function ExemploMemo() {
  const [count, setCount] = useState(0);
  const [items, setItems] = useState([1, 2, 3, 4, 5]);

  // Cálculo pesado - só recalcula quando items muda
  const total = useMemo(() => {
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

```jsx
import { useCallback, useState } from 'react';

function Lista({ onItemClick }) {
  // Componente filho
  return <div>{/* ... */}</div>;
}

function Parent() {
  const [count, setCount] = useState(0);

  // Sem useCallback, função é recriada a cada render
  // Com useCallback, função é memoizada
  const handleClick = useCallback((item) => {
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

```jsx
// useFetch - Hook customizado
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch(url);
        const json = await response.json();
        setData(json);
        setError(null);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
}

// Usar o hook customizado
function MeuComponente() {
  const { data, loading, error } = useFetch('https://api.exemplo.com/users');

  if (loading) return <p>Carregando...</p>;
  if (error) return <p>Erro: {error}</p>;

  return <div>{JSON.stringify(data)}</div>;
}

// useLocalStorage - Persistir estado
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const item = localStorage.getItem(key);
    return item ? JSON.parse(item) : initialValue;
  });

  const setStoredValue = (newValue) => {
    setValue(newValue);
    localStorage.setItem(key, JSON.stringify(newValue));
  };

  return [value, setStoredValue];
}

// Usar
function App() {
  const [nome, setNome] = useLocalStorage('nome', '');

  return (
    <input
      value={nome}
      onChange={(e) => setNome(e.target.value)}
    />
  );
}
```

## Eventos

```jsx
function Eventos() {
  // Click
  const handleClick = (e) => {
    console.log('Clicou!', e);
  };

  // Com parâmetros
  const handleClickParam = (id) => {
    console.log('ID:', id);
  };

  // Submit de formulário
  const handleSubmit = (e) => {
    e.preventDefault(); // Prevenir reload da página
    console.log('Formulário enviado');
  };

  // Change
  const handleChange = (e) => {
    console.log(e.target.value);
  };

  // Keyboard
  const handleKeyPress = (e) => {
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

```jsx
function Condicional() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [role, setRole] = useState('user');

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
function MensagemStatus({ status }) {
  if (status === 'loading') return <p>Carregando...</p>;
  if (status === 'error') return <p>Erro!</p>;
  if (status === 'success') return <p>Sucesso!</p>;
  return null;
}
```

## Listas e Keys

```jsx
function Listas() {
  const usuarios = [
    { id: 1, nome: 'João', idade: 25 },
    { id: 2, nome: 'Maria', idade: 30 },
    { id: 3, nome: 'Pedro', idade: 35 }
  ];

  return (
    <div>
      {/* Map básico */}
      <ul>
        {usuarios.map(usuario => (
          <li key={usuario.id}>
            {usuario.nome} - {usuario.idade} anos
          </li>
        ))}
      </ul>

      {/* Com componente */}
      {usuarios.map(usuario => (
        <CardUsuario
          key={usuario.id}
          usuario={usuario}
        />
      ))}

      {/* Com index (use apenas se não houver id único) */}
      {usuarios.map((usuario, index) => (
        <li key={index}>{usuario.nome}</li>
      ))}

      {/* Filter + Map */}
      {usuarios
        .filter(u => u.idade > 25)
        .map(u => (
          <li key={u.id}>{u.nome}</li>
        ))
      }
    </div>
  );
}
```

## Formulários

```jsx
function Formulario() {
  const [formData, setFormData] = useState({
    nome: '',
    email: '',
    senha: '',
    aceito: false,
    genero: '',
    pais: 'br'
  });

  // Atualizar campo individual
  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value
    }));
  };

  // Submit
  const handleSubmit = (e) => {
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

```jsx
// CSS Inline
function InlineStyle() {
  const estilo = {
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

function ComModules() {
  return (
    <div className={styles.container}>
      <h1 className={styles.titulo}>Título</h1>
    </div>
  );
}

// Classe condicional
function ClasseCondicional({ ativo }) {
  return (
    <div className={ativo ? 'ativo' : 'inativo'}>
      Conteúdo
    </div>
  );
}

// Múltiplas classes
function MultiClasses({ ativo, erro }) {
  const classes = `botao ${ativo ? 'ativo' : ''} ${erro ? 'erro' : ''}`;

  return <button className={classes}>Botão</button>;
}

// Com classnames (biblioteca)
import classNames from 'classnames';

function ComClassNames({ ativo, erro }) {
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

```jsx
import {
  BrowserRouter,
  Routes,
  Route,
  Link,
  useNavigate,
  useParams,
  Navigate
} from 'react-router-dom';

function App() {
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
function Usuario() {
  const { id } = useParams();
  return <h1>Usuário ID: {id}</h1>;
}

// Navegação programática
function Login() {
  const navigate = useNavigate();

  const handleLogin = () => {
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
function RotaProtegida({ children }) {
  const isAutenticado = true; // verificar autenticação

  if (!isAutenticado) {
    return <Navigate to="/login" replace />;
  }

  return children;
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

```jsx
import axios from 'axios';
import { useState, useEffect } from 'react';

// Configurar instância
const api = axios.create({
  baseURL: 'https://api.exemplo.com',
  timeout: 5000,
  headers: {
    'Content-Type': 'application/json'
  }
});

function ExemploAxios() {
  const [usuarios, setUsuarios] = useState([]);
  const [loading, setLoading] = useState(false);

  // GET
  useEffect(() => {
    const fetchUsuarios = async () => {
      try {
        setLoading(true);
        const response = await api.get('/usuarios');
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
  const criarUsuario = async (novoUsuario) => {
    try {
      const response = await api.post('/usuarios', novoUsuario);
      setUsuarios([...usuarios, response.data]);
    } catch (error) {
      console.error('Erro ao criar:', error);
    }
  };

  // PUT
  const atualizarUsuario = async (id, dadosAtualizados) => {
    try {
      await api.put(`/usuarios/${id}`, dadosAtualizados);
      // Atualizar estado local
    } catch (error) {
      console.error('Erro ao atualizar:', error);
    }
  };

  // DELETE
  const deletarUsuario = async (id) => {
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
  config => {
    // Adicionar token de autenticação
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  error => Promise.reject(error)
);

api.interceptors.response.use(
  response => response,
  error => {
    if (error.response.status === 401) {
      // Redirecionar para login
    }
    return Promise.reject(error);
  }
);
```

## Lazy Loading e Code Splitting

```jsx
import { lazy, Suspense } from 'react';

// Lazy loading de componente
const Dashboard = lazy(() => import('./Dashboard'));
const Perfil = lazy(() => import('./Perfil'));

function App() {
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
```

## Performance

```jsx
import { memo } from 'react';

// React.memo - Evitar re-render desnecessário
const MeuComponente = memo(function MeuComponente({ nome }) {
  console.log('Renderizou');
  return <h1>{nome}</h1>;
});

// Com comparação customizada
const MeuComponenteCustom = memo(
  function MeuComponente({ usuario }) {
    return <h1>{usuario.nome}</h1>;
  },
  (prevProps, nextProps) => {
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
│   │   ├── Button.jsx
│   │   ├── Button.module.css
│   │   └── index.js
│   └── ...
├── pages/             # Páginas/rotas
│   ├── Home/
│   ├── About/
│   └── ...
├── hooks/             # Custom hooks
│   ├── useFetch.js
│   └── useAuth.js
├── context/           # Context providers
│   └── AuthContext.jsx
├── services/          # APIs e serviços
│   └── api.js
├── utils/             # Funções utilitárias
│   └── helpers.js
├── App.jsx
└── main.jsx
```

## Atalhos e Snippets Úteis

```jsx
// Importação rápida (atalho: imr)
import React from 'react';

// useState (atalho: ush)
const [state, setState] = useState(initialState);

// useEffect (atalho: uef)
useEffect(() => {
  // effect
  return () => {
    // cleanup
  };
}, [dependencies]);

// Componente funcional (atalho: rafce)
const ComponentName = () => {
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
