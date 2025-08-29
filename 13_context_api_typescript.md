# Lesson 13: Context API in TypeScript

## Context API Kya Hai?

React Context API ek tarika hai state ko globally application mein share karne ka, bina props drilling ke. TypeScript ke saath Context API use karna ek important skill hai modern React applications mein.

## Basic Context Setup in TypeScript

Ek simple context create karna TypeScript mein:

```tsx
// ThemeContext.tsx
import React, { createContext, useState, useContext, ReactNode } from 'react';

// Define the shape of the context state
interface ThemeContextType {
  isDarkMode: boolean;
  toggleTheme: () => void;
}

// Create context with an initial default value
// The context value will be undefined until Provider is used
const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

// Props for ThemeProvider component
interface ThemeProviderProps {
  children: ReactNode;
}

// Provider component
export const ThemeProvider: React.FC<ThemeProviderProps> = ({ children }) => {
  const [isDarkMode, setIsDarkMode] = useState<boolean>(false);

  const toggleTheme = () => {
    setIsDarkMode(prevMode => !prevMode);
  };

  // Actual value that will be available in the context
  const value: ThemeContextType = {
    isDarkMode,
    toggleTheme
  };

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
};

// Custom hook to consume the context
export const useTheme = (): ThemeContextType => {
  const context = useContext(ThemeContext);
  
  if (context === undefined) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  
  return context;
};
```

## Using the Context in Components

Components mein context ko use karna:

```tsx
// App.tsx
import React from 'react';
import { ThemeProvider } from './ThemeContext';
import ThemedButton from './ThemedButton';
import ThemedHeader from './ThemedHeader';

const App: React.FC = () => {
  return (
    <ThemeProvider>
      <div className="app">
        <ThemedHeader />
        <main>
          <h2>Context API with TypeScript</h2>
          <p>This example demonstrates how to use Context API with TypeScript</p>
          <ThemedButton />
        </main>
      </div>
    </ThemeProvider>
  );
};

export default App;

// ThemedButton.tsx
import React from 'react';
import { useTheme } from './ThemeContext';

const ThemedButton: React.FC = () => {
  const { isDarkMode, toggleTheme } = useTheme();
  
  return (
    <button
      onClick={toggleTheme}
      style={{
        backgroundColor: isDarkMode ? '#333' : '#f0f0f0',
        color: isDarkMode ? '#fff' : '#000',
        padding: '10px 20px',
        border: 'none',
        borderRadius: '4px',
        cursor: 'pointer'
      }}
    >
      Toggle {isDarkMode ? 'Light' : 'Dark'} Mode
    </button>
  );
};

export default ThemedButton;

// ThemedHeader.tsx
import React from 'react';
import { useTheme } from './ThemeContext';

const ThemedHeader: React.FC = () => {
  const { isDarkMode } = useTheme();
  
  return (
    <header
      style={{
        backgroundColor: isDarkMode ? '#222' : '#eee',
        color: isDarkMode ? '#fff' : '#000',
        padding: '1rem',
        marginBottom: '1rem'
      }}
    >
      <h1>My Themed App</h1>
      <p>Current theme: {isDarkMode ? 'Dark' : 'Light'}</p>
    </header>
  );
};

export default ThemedHeader;
```

## Complex Context with Multiple Values

Zyada complex state management ke liye context:

```tsx
// UserContext.tsx
import React, { createContext, useContext, useReducer, ReactNode } from 'react';

// Types for user data
interface User {
  id: number;
  name: string;
  email: string;
  isAuthenticated: boolean;
}

// Types for context state
interface UserState {
  user: User | null;
  loading: boolean;
  error: string | null;
}

// Action types using discriminated unions
type UserAction =
  | { type: 'LOGIN_START' }
  | { type: 'LOGIN_SUCCESS'; payload: User }
  | { type: 'LOGIN_FAILURE'; payload: string }
  | { type: 'LOGOUT' }
  | { type: 'UPDATE_USER'; payload: Partial<User> };

// Context type
interface UserContextType {
  state: UserState;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
  updateUser: (userData: Partial<User>) => void;
}

// Initial state
const initialState: UserState = {
  user: null,
  loading: false,
  error: null
};

// Reducer function
const userReducer = (state: UserState, action: UserAction): UserState => {
  switch (action.type) {
    case 'LOGIN_START':
      return {
        ...state,
        loading: true,
        error: null
      };
    case 'LOGIN_SUCCESS':
      return {
        ...state,
        user: action.payload,
        loading: false,
        error: null
      };
    case 'LOGIN_FAILURE':
      return {
        ...state,
        loading: false,
        error: action.payload
      };
    case 'LOGOUT':
      return {
        ...state,
        user: null
      };
    case 'UPDATE_USER':
      return {
        ...state,
        user: state.user ? { ...state.user, ...action.payload } : null
      };
    default:
      return state;
  }
};

// Create context
const UserContext = createContext<UserContextType | undefined>(undefined);

// Props for provider
interface UserProviderProps {
  children: ReactNode;
}

// Provider component
export const UserProvider: React.FC<UserProviderProps> = ({ children }) => {
  const [state, dispatch] = useReducer(userReducer, initialState);

  // Login action
  const login = async (email: string, password: string) => {
    dispatch({ type: 'LOGIN_START' });
    
    try {
      // Mock API call
      await new Promise(resolve => setTimeout(resolve, 1000));
      
      // Simulate successful login
      if (email === 'user@example.com' && password === 'password') {
        const user: User = {
          id: 1,
          name: 'Test User',
          email: email,
          isAuthenticated: true
        };
        dispatch({ type: 'LOGIN_SUCCESS', payload: user });
      } else {
        dispatch({ type: 'LOGIN_FAILURE', payload: 'Invalid credentials' });
      }
    } catch (error) {
      dispatch({ 
        type: 'LOGIN_FAILURE', 
        payload: error instanceof Error ? error.message : 'An error occurred' 
      });
    }
  };

  // Logout action
  const logout = () => {
    dispatch({ type: 'LOGOUT' });
  };

  // Update user action
  const updateUser = (userData: Partial<User>) => {
    dispatch({ type: 'UPDATE_USER', payload: userData });
  };

  // Context value
  const value: UserContextType = {
    state,
    login,
    logout,
    updateUser
  };

  return (
    <UserContext.Provider value={value}>
      {children}
    </UserContext.Provider>
  );
};

// Custom hook
export const useUser = (): UserContextType => {
  const context = useContext(UserContext);
  
  if (context === undefined) {
    throw new Error('useUser must be used within a UserProvider');
  }
  
  return context;
};
```

## Consuming Complex Context

Complex context ko components mein use karna:

```tsx
// LoginForm.tsx
import React, { useState } from 'react';
import { useUser } from './UserContext';

const LoginForm: React.FC = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  
  const { state, login } = useUser();
  const { loading, error } = state;

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    await login(email, password);
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>Login</h2>
      
      {error && <div className="error">{error}</div>}
      
      <div>
        <label htmlFor="email">Email:</label>
        <input
          type="email"
          id="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
        />
      </div>
      
      <div>
        <label htmlFor="password">Password:</label>
        <input
          type="password"
          id="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          required
        />
      </div>
      
      <button type="submit" disabled={loading}>
        {loading ? 'Logging in...' : 'Login'}
      </button>
      
      <p className="hint">Try: user@example.com / password</p>
    </form>
  );
};

// UserProfile.tsx
import React, { useState } from 'react';
import { useUser } from './UserContext';

const UserProfile: React.FC = () => {
  const { state, updateUser, logout } = useUser();
  const { user } = state;
  
  const [name, setName] = useState(user?.name || '');

  const handleUpdateName = () => {
    if (user) {
      updateUser({ name });
    }
  };

  if (!user) {
    return <div>Please login to view your profile</div>;
  }

  return (
    <div className="profile">
      <h2>User Profile</h2>
      <p>Email: {user.email}</p>
      
      <div>
        <label htmlFor="name">Name:</label>
        <input
          type="text"
          id="name"
          value={name}
          onChange={(e) => setName(e.target.value)}
        />
        <button onClick={handleUpdateName}>Update Name</button>
      </div>
      
      <button onClick={logout} className="logout-btn">
        Logout
      </button>
    </div>
  );
};

// App.tsx
import React from 'react';
import { UserProvider } from './UserContext';
import { ThemeProvider } from './ThemeContext';
import LoginForm from './LoginForm';
import UserProfile from './UserProfile';
import ThemedButton from './ThemedButton';

const App: React.FC = () => {
  return (
    <ThemeProvider>
      <UserProvider>
        <div className="app">
          <h1>Context API with TypeScript</h1>
          <LoginForm />
          <UserProfile />
          <ThemedButton />
        </div>
      </UserProvider>
    </ThemeProvider>
  );
};

export default App;
```

## Multiple Contexts ko Consume Karna

Multiple contexts ko ek component mein consume karna:

```tsx
// Dashboard.tsx
import React from 'react';
import { useTheme } from './ThemeContext';
import { useUser } from './UserContext';

const Dashboard: React.FC = () => {
  // Consume theme context
  const { isDarkMode, toggleTheme } = useTheme();
  
  // Consume user context
  const { state, logout } = useUser();
  const { user } = state;

  if (!user) {
    return <div>Please login to view the dashboard</div>;
  }

  return (
    <div
      className="dashboard"
      style={{
        backgroundColor: isDarkMode ? '#333' : '#f9f9f9',
        color: isDarkMode ? '#fff' : '#333',
        padding: '20px',
        borderRadius: '8px'
      }}
    >
      <div className="dashboard-header">
        <h2>Welcome, {user.name}!</h2>
        <div className="controls">
          <button onClick={toggleTheme}>
            Switch to {isDarkMode ? 'Light' : 'Dark'} Mode
          </button>
          <button onClick={logout}>Logout</button>
        </div>
      </div>
      
      <div className="dashboard-content">
        <p>This is your personalized dashboard.</p>
        <p>Theme: {isDarkMode ? 'Dark' : 'Light'}</p>
        <p>User ID: {user.id}</p>
        <p>Email: {user.email}</p>
      </div>
    </div>
  );
};
```

## Context with Default Values

Default values ke saath context create karna:

```tsx
// SettingsContext.tsx
import React, { createContext, useContext, useState, ReactNode } from 'react';

interface Settings {
  fontSize: number;
  language: 'en' | 'ur' | 'hi';
  notifications: boolean;
}

interface SettingsContextType {
  settings: Settings;
  updateSettings: (newSettings: Partial<Settings>) => void;
  resetSettings: () => void;
}

// Default settings
const defaultSettings: Settings = {
  fontSize: 16,
  language: 'en',
  notifications: true
};

// Create context with default value
// Using non-null assertion (!) since we're providing default value right away
const SettingsContext = createContext<SettingsContextType>({
  settings: defaultSettings,
  updateSettings: () => {},
  resetSettings: () => {}
});

interface SettingsProviderProps {
  children: ReactNode;
  initialSettings?: Partial<Settings>;
}

export const SettingsProvider: React.FC<SettingsProviderProps> = ({ 
  children, 
  initialSettings = {} 
}) => {
  const [settings, setSettings] = useState<Settings>({
    ...defaultSettings,
    ...initialSettings
  });

  const updateSettings = (newSettings: Partial<Settings>) => {
    setSettings(prev => ({
      ...prev,
      ...newSettings
    }));
  };

  const resetSettings = () => {
    setSettings(defaultSettings);
  };

  return (
    <SettingsContext.Provider
      value={{
        settings,
        updateSettings,
        resetSettings
      }}
    >
      {children}
    </SettingsContext.Provider>
  );
};

// With default context value, we don't need to check for undefined
export const useSettings = () => useContext(SettingsContext);
```

## Context Value Ko Update Karna with useMemo

Context value ko performance optimization ke liye useMemo ke saath use karna:

```tsx
// OptimizedContext.tsx
import React, { createContext, useContext, useState, useMemo, ReactNode } from 'react';

interface User {
  id: number;
  name: string;
}

interface OptimizedContextType {
  user: User | null;
  setUser: React.Dispatch<React.SetStateAction<User | null>>;
  isLoggedIn: boolean;
  login: (userData: User) => void;
  logout: () => void;
}

const OptimizedContext = createContext<OptimizedContextType | undefined>(undefined);

interface OptimizedProviderProps {
  children: ReactNode;
}

export const OptimizedProvider: React.FC<OptimizedProviderProps> = ({ children }) => {
  const [user, setUser] = useState<User | null>(null);
  
  // Derived value
  const isLoggedIn = user !== null;
  
  // Functions that could be recreated on each render
  const login = (userData: User) => {
    setUser(userData);
  };
  
  const logout = () => {
    setUser(null);
  };
  
  // Memoize the context value to prevent unnecessary re-renders
  const value = useMemo(() => ({
    user,
    setUser,
    isLoggedIn,
    login,
    logout
  }), [user]);  // Only recreate when user changes
  
  return (
    <OptimizedContext.Provider value={value}>
      {children}
    </OptimizedContext.Provider>
  );
};

export const useOptimized = () => {
  const context = useContext(OptimizedContext);
  
  if (context === undefined) {
    throw new Error('useOptimized must be used within an OptimizedProvider');
  }
  
  return context;
};
```

## Nested Contexts

Nested contexts ko create aur use karna:

```tsx
// AppContexts.tsx
import React, { ReactNode } from 'react';
import { ThemeProvider } from './ThemeContext';
import { UserProvider } from './UserContext';
import { SettingsProvider } from './SettingsContext';

interface AppProvidersProps {
  children: ReactNode;
}

// Combine all providers in one component
export const AppProviders: React.FC<AppProvidersProps> = ({ children }) => {
  return (
    <ThemeProvider>
      <UserProvider>
        <SettingsProvider>
          {children}
        </SettingsProvider>
      </UserProvider>
    </ThemeProvider>
  );
};

// Usage in main App component
// App.tsx
import React from 'react';
import { AppProviders } from './AppContexts';
import Dashboard from './Dashboard';

const App: React.FC = () => {
  return (
    <AppProviders>
      <Dashboard />
    </AppProviders>
  );
};
```

## Context with localStorage Integration

Context values ko localStorage mein persist karna:

```tsx
// PersistentContext.tsx
import React, { createContext, useContext, useEffect, useState, ReactNode } from 'react';

interface AppState {
  darkMode: boolean;
  sidebarOpen: boolean;
  fontSize: 'small' | 'medium' | 'large';
}

interface PersistentContextType {
  state: AppState;
  updateState: <K extends keyof AppState>(key: K, value: AppState[K]) => void;
  resetState: () => void;
}

// Default state
const defaultState: AppState = {
  darkMode: false,
  sidebarOpen: true,
  fontSize: 'medium'
};

// Context
const PersistentContext = createContext<PersistentContextType | undefined>(undefined);

// Storage key
const STORAGE_KEY = 'app_state';

interface PersistentProviderProps {
  children: ReactNode;
}

export const PersistentProvider: React.FC<PersistentProviderProps> = ({ children }) => {
  // Initialize state from localStorage or default
  const [state, setState] = useState<AppState>(() => {
    try {
      const storedState = localStorage.getItem(STORAGE_KEY);
      return storedState ? JSON.parse(storedState) : defaultState;
    } catch (error) {
      console.error('Failed to parse stored state:', error);
      return defaultState;
    }
  });

  // Persist state changes to localStorage
  useEffect(() => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
  }, [state]);

  // Update a single state property
  const updateState = <K extends keyof AppState>(key: K, value: AppState[K]) => {
    setState(prevState => ({
      ...prevState,
      [key]: value
    }));
  };

  // Reset to default state
  const resetState = () => {
    setState(defaultState);
  };

  return (
    <PersistentContext.Provider
      value={{
        state,
        updateState,
        resetState
      }}
    >
      {children}
    </PersistentContext.Provider>
  );
};

export const usePersistentState = () => {
  const context = useContext(PersistentContext);
  
  if (context === undefined) {
    throw new Error('usePersistentState must be used within a PersistentProvider');
  }
  
  return context;
};
```

## Best Practices for TypeScript Context API

1. **Explicit Type Definitions**: Context values aur actions ke liye clear types define karein.

2. **Error Handling**: Custom hooks mein check karein ke context undefined to nahi hai.

3. **Organize Related State**: Related state values ko ek hi context mein rakhen.

4. **Minimize Context Size**: Context mein sirf woh values rakhen jo actual mein multiple components mein share karne hain.

5. **Use useReducer for Complex State**: Complex state logic ke liye useReducer use karein.

6. **Memoize Context Value**: useMemo ka use karein context value ko memoize karne ke liye taki unnecessary re-renders se bacha ja sake.

7. **Split Contexts**: App ko multiple logical contexts mein divide karein rather than ek large global context use karna.

## Context vs Redux

Context API aur Redux ke beech comparison:

| Feature | Context API | Redux |
|---------|-------------|-------|
| Setup Complexity | Low - Built into React | High - External library with boilerplate |
| Bundle Size | No extra size | Adds to bundle size |
| DevTools | Limited | Advanced devtools |
| Performance | Good for low-frequency updates | Optimized for frequent updates |
| Middleware | Manual implementation | Built-in support |
| Time Travel Debugging | Not available | Available |
| Suitability | Small-medium apps, simple state | Large apps, complex state |

## Next Steps
[Type Inference & Literal Types](14_type_inference_literal_types.md) ko padhein. 