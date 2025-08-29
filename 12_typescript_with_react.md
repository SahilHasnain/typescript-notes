# Lesson 12: TypeScript with React

## TypeScript aur React Integration

React aur TypeScript dono ko saath use karna modern frontend development mein bahut common hogaya hai. TypeScript React applications mein type safety, better tooling, aur clear component interfaces provide karta hai.

## Setup: React App with TypeScript

### Option 1: Create React App with TypeScript Template

```bash
# Create a new React project with TypeScript
npx create-react-app my-app --template typescript

# Or using newer methods
npx create-react-app@latest my-app --template typescript
```

### Option 2: Add TypeScript to Existing React Project

```bash
# Install TypeScript and type definitions
npm install --save typescript @types/node @types/react @types/react-dom

# Create a tsconfig.json file
npx tsc --init
```

## File Extensions in TypeScript React

React with TypeScript mein different file extensions use hote hain:

- `.tsx`: React components with JSX
- `.ts`: Pure TypeScript files (no JSX)

## Typing React Components

### Function Components

React mein function components ko TypeScript ke saath is tarah define karte hain:

```tsx
import React from 'react';

// Props type definition
interface GreetingProps {
  name: string;
  age?: number; // Optional prop
  isLoggedIn: boolean;
}

// Function component with typed props
const Greeting: React.FC<GreetingProps> = ({ name, age, isLoggedIn }) => {
  return (
    <div>
      {isLoggedIn ? (
        <h1>Hello, {name}! {age && `You are ${age} years old.`}</h1>
      ) : (
        <h1>Please log in</h1>
      )}
    </div>
  );
};

// Alternative way of typing function components
function Greeting2(props: GreetingProps) {
  const { name, age, isLoggedIn } = props;
  // Component code
  return (
    <div>
      {isLoggedIn ? (
        <h1>Hello, {name}! {age && `You are ${age} years old.`}</h1>
      ) : (
        <h1>Please log in</h1>
      )}
    </div>
  );
}

// Usage
export default function App() {
  return (
    <div>
      <Greeting name="Ahmed" isLoggedIn={true} />
      <Greeting name="Sara" age={28} isLoggedIn={true} />
    </div>
  );
}
```

### Class Components

Class components bhi TypeScript ke saath type kar sakte hain:

```tsx
import React, { Component } from 'react';

// Props type
interface CounterProps {
  initialCount: number;
  stepSize?: number;
}

// State type
interface CounterState {
  count: number;
}

// Class component with typed props and state
class Counter extends Component<CounterProps, CounterState> {
  // Default props
  static defaultProps = {
    stepSize: 1
  };

  constructor(props: CounterProps) {
    super(props);
    this.state = {
      count: props.initialCount
    };
  }

  increment = () => {
    this.setState(prevState => ({
      count: prevState.count + (this.props.stepSize || 1)
    }));
  };

  decrement = () => {
    this.setState(prevState => ({
      count: prevState.count - (this.props.stepSize || 1)
    }));
  };

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
        <button onClick={this.decrement}>Decrement</button>
      </div>
    );
  }
}

export default Counter;
```

## Children Props in TypeScript

React components mein children props ko type karna:

```tsx
import React from 'react';

// With React.FC
interface CardProps {
  title: string;
  children: React.ReactNode;
}

const Card: React.FC<CardProps> = ({ title, children }) => {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div className="card-content">
        {children}
      </div>
    </div>
  );
};

// Alternative approach
interface ContainerProps {
  children: React.ReactNode; // Accept any valid React node
  width?: string;
}

function Container({ children, width = "100%" }: ContainerProps) {
  return (
    <div style={{ width }}>
      {children}
    </div>
  );
}

// Using the components
function App() {
  return (
    <Container width="80%">
      <Card title="Welcome">
        <p>This is a card with children content</p>
      </Card>
    </Container>
  );
}
```

## Event Handling in TypeScript React

TypeScript ke saath event handling mein type safety add karna:

```tsx
import React, { useState, ChangeEvent, FormEvent } from 'react';

interface FormData {
  username: string;
  email: string;
}

const Form: React.FC = () => {
  const [formData, setFormData] = useState<FormData>({
    username: '',
    email: ''
  });

  // Typed event handler for input changes
  const handleInputChange = (e: ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData({
      ...formData,
      [name]: value
    });
  };

  // Typed event handler for form submission
  const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    console.log('Form submitted:', formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="username">Username:</label>
        <input
          type="text"
          id="username"
          name="username"
          value={formData.username}
          onChange={handleInputChange}
        />
      </div>
      <div>
        <label htmlFor="email">Email:</label>
        <input
          type="email"
          id="email"
          name="email"
          value={formData.email}
          onChange={handleInputChange}
        />
      </div>
      <button type="submit">Submit</button>
    </form>
  );
};

export default Form;
```

## React Hooks with TypeScript

### useState Hook

```tsx
import React, { useState } from 'react';

// Simple primitive types
const Counter: React.FC = () => {
  const [count, setCount] = useState<number>(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
};

// Complex types with useState
interface User {
  id: number;
  name: string;
  email: string;
}

const UserProfile: React.FC = () => {
  const [user, setUser] = useState<User | null>(null);
  
  const fetchUser = () => {
    // Simulate API call
    setUser({
      id: 1,
      name: 'Ahmed',
      email: 'ahmed@example.com'
    });
  };
  
  return (
    <div>
      {user ? (
        <div>
          <h2>{user.name}</h2>
          <p>Email: {user.email}</p>
        </div>
      ) : (
        <button onClick={fetchUser}>Load User</button>
      )}
    </div>
  );
};
```

### useEffect Hook

```tsx
import React, { useState, useEffect } from 'react';

interface Post {
  id: number;
  title: string;
  body: string;
}

const PostList: React.FC = () => {
  const [posts, setPosts] = useState<Post[]>([]);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    // Fetch posts from API
    const fetchPosts = async () => {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts');
        
        if (!response.ok) {
          throw new Error('Failed to fetch posts');
        }
        
        const data: Post[] = await response.json();
        setPosts(data.slice(0, 5)); // Get first 5 posts only
        setLoading(false);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'An error occurred');
        setLoading(false);
      }
    };

    fetchPosts();

    // Cleanup function
    return () => {
      // Any cleanup code here
    };
  }, []); // Empty dependency array = run once on mount

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map(post => (
          <li key={post.id}>
            <h3>{post.title}</h3>
            <p>{post.body}</p>
          </li>
        ))}
      </ul>
    </div>
  );
};
```

### useRef Hook

```tsx
import React, { useRef, useEffect } from 'react';

const AutoFocusInput: React.FC = () => {
  // Type annotation for DOM element refs
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => {
    // Focus the input on component mount
    if (inputRef.current) {
      inputRef.current.focus();
    }
  }, []);

  return (
    <div>
      <label htmlFor="auto-focus">This input will auto-focus:</label>
      <input
        ref={inputRef}
        id="auto-focus"
        type="text"
        placeholder="I will be focused on load"
      />
    </div>
  );
};

// For storing mutable values
const Timer: React.FC = () => {
  // For values we don't want to trigger re-renders
  const timerRef = useRef<number | null>(null);

  const startTimer = () => {
    if (timerRef.current !== null) return;
    
    timerRef.current = window.setInterval(() => {
      console.log('Timer tick');
    }, 1000);
  };

  const stopTimer = () => {
    if (timerRef.current === null) return;
    
    clearInterval(timerRef.current);
    timerRef.current = null;
  };

  // Clean up on unmount
  useEffect(() => {
    return () => {
      if (timerRef.current !== null) {
        clearInterval(timerRef.current);
      }
    };
  }, []);

  return (
    <div>
      <button onClick={startTimer}>Start Timer</button>
      <button onClick={stopTimer}>Stop Timer</button>
    </div>
  );
};
```

### useReducer Hook

```tsx
import React, { useReducer } from 'react';

// Define state type
interface CounterState {
  count: number;
}

// Define action types
type CounterAction = 
  | { type: 'increment'; payload: number }
  | { type: 'decrement'; payload: number }
  | { type: 'reset' };

// Initial state
const initialState: CounterState = { count: 0 };

// Reducer function
function counterReducer(state: CounterState, action: CounterAction): CounterState {
  switch (action.type) {
    case 'increment':
      return { count: state.count + action.payload };
    case 'decrement':
      return { count: state.count - action.payload };
    case 'reset':
      return initialState;
    default:
      return state;
  }
}

const CounterWithReducer: React.FC = () => {
  const [state, dispatch] = useReducer(counterReducer, initialState);

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment', payload: 1 })}>
        Add 1
      </button>
      <button onClick={() => dispatch({ type: 'increment', payload: 5 })}>
        Add 5
      </button>
      <button onClick={() => dispatch({ type: 'decrement', payload: 1 })}>
        Subtract 1
      </button>
      <button onClick={() => dispatch({ type: 'reset' })}>
        Reset
      </button>
    </div>
  );
};
```

## Custom Hooks with TypeScript

TypeScript ke saath custom hooks create karna:

```tsx
import { useState, useEffect } from 'react';

// Generic type parameter for flexible data types
function useLocalStorage<T>(key: string, initialValue: T): [T, (value: T) => void] {
  // State to store our value
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      // Get from local storage by key
      const item = window.localStorage.getItem(key);
      // Parse stored json or if none return initialValue
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      // If error also return initialValue
      console.error(error);
      return initialValue;
    }
  });

  // Return a wrapped version of useState's setter function that ...
  // ... persists the new value to localStorage.
  const setValue = (value: T) => {
    try {
      // Save state
      setStoredValue(value);
      // Save to local storage
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
}

// Usage example
function App() {
  // Store a string
  const [name, setName] = useLocalStorage<string>('name', 'Ahmed');
  // Store an object
  const [user, setUser] = useLocalStorage<{ id: number; name: string }>('user', {
    id: 1,
    name: 'Ahmed'
  });

  return (
    <div>
      <input
        type="text"
        value={name}
        onChange={e => setName(e.target.value)}
        placeholder="Enter your name"
      />
      <button
        onClick={() =>
          setUser({
            id: user.id + 1,
            name
          })
        }
      >
        Update User
      </button>
      <div>User: {JSON.stringify(user)}</div>
    </div>
  );
}
```

## Typing Props with Default Values

Props ke default values set karna TypeScript ke saath:

```tsx
import React from 'react';

interface ButtonProps {
  label: string;
  primary?: boolean;
  size?: 'small' | 'medium' | 'large';
  onClick?: () => void;
}

// Approach 1: Using defaultProps
const Button: React.FC<ButtonProps> = ({ 
  label, 
  primary, 
  size, 
  onClick 
}) => {
  return (
    <button
      className={`btn ${primary ? 'btn-primary' : 'btn-secondary'} btn-${size}`}
      onClick={onClick}
    >
      {label}
    </button>
  );
};

Button.defaultProps = {
  primary: false,
  size: 'medium',
  onClick: () => {}
};

// Approach 2: Using default parameters (recommended)
function Button2({ 
  label, 
  primary = false, 
  size = 'medium', 
  onClick = () => {} 
}: ButtonProps) {
  return (
    <button
      className={`btn ${primary ? 'btn-primary' : 'btn-secondary'} btn-${size}`}
      onClick={onClick}
    >
      {label}
    </button>
  );
}

// Usage
function App() {
  return (
    <div>
      <Button label="Click Me" />
      <Button label="Primary Button" primary={true} size="large" />
      <Button2 label="Button with default params" />
    </div>
  );
}
```

## Typing Component API Interactions

API ke saath interact karne wale components ko type karna:

```tsx
import React, { useState, useEffect } from 'react';

// Types for API data
interface Product {
  id: number;
  title: string;
  price: number;
  description: string;
  category: string;
  image: string;
}

interface FetchProductsResponse {
  products: Product[];
  total: number;
  skip: number;
  limit: number;
}

// Types for component props
interface ProductListProps {
  limit?: number;
  onProductSelect?: (product: Product) => void;
}

const ProductList: React.FC<ProductListProps> = ({ 
  limit = 5, 
  onProductSelect 
}) => {
  const [products, setProducts] = useState<Product[]>([]);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchProducts = async () => {
      try {
        setLoading(true);
        const response = await fetch(`https://dummyjson.com/products?limit=${limit}`);
        
        if (!response.ok) {
          throw new Error('Failed to fetch products');
        }
        
        const data: FetchProductsResponse = await response.json();
        setProducts(data.products);
        setLoading(false);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'An error occurred');
        setLoading(false);
      }
    };

    fetchProducts();
  }, [limit]);

  if (loading) return <div>Loading products...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div className="product-list">
      <h2>Products</h2>
      <div className="products">
        {products.map(product => (
          <div 
            key={product.id} 
            className="product-card"
            onClick={() => onProductSelect && onProductSelect(product)}
          >
            <img src={product.image} alt={product.title} />
            <h3>{product.title}</h3>
            <p>${product.price}</p>
            <p>{product.description.substring(0, 100)}...</p>
          </div>
        ))}
      </div>
    </div>
  );
};

export default ProductList;
```

## Typing Third-Party Library Components

Third-party libraries ke saath TypeScript use karna:

```tsx
import React, { useState } from 'react';
// Assuming these are from a UI library
import { Button, Modal, Select } from 'ui-library';
import { Option } from 'ui-library/lib/types';

// Types from the third-party library
interface ButtonProps {
  variant: 'primary' | 'secondary' | 'danger';
  size?: 'small' | 'medium' | 'large';
  onClick: () => void;
}

interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  title: string;
}

interface SelectProps {
  options: Option[];
  value: string | string[];
  onChange: (value: string | string[]) => void;
  isMulti?: boolean;
}

// Your component using the library
const ProductForm: React.FC = () => {
  const [isModalOpen, setIsModalOpen] = useState(false);
  const [selectedCategory, setSelectedCategory] = useState<string>('');
  
  const categories: Option[] = [
    { value: 'electronics', label: 'Electronics' },
    { value: 'clothing', label: 'Clothing' },
    { value: 'books', label: 'Books' }
  ];
  
  const handleCategoryChange = (value: string | string[]) => {
    // Handle as string since isMulti is not set
    setSelectedCategory(value as string);
  };

  return (
    <div>
      <Button 
        variant="primary" 
        onClick={() => setIsModalOpen(true)}
      >
        Add New Product
      </Button>
      
      <Modal
        isOpen={isModalOpen}
        onClose={() => setIsModalOpen(false)}
        title="Add New Product"
      >
        <form>
          {/* Form fields */}
          <div>
            <label>Category</label>
            <Select
              options={categories}
              value={selectedCategory}
              onChange={handleCategoryChange}
            />
          </div>
          
          <Button 
            variant="secondary" 
            onClick={() => setIsModalOpen(false)}
          >
            Cancel
          </Button>
          <Button 
            variant="primary" 
            onClick={() => {
              // Submit logic
              setIsModalOpen(false);
            }}
          >
            Save
          </Button>
        </form>
      </Modal>
    </div>
  );
};
```

## Best Practices for TypeScript in React

1. **Create Reusable Interfaces**: Related props ko group karein ek interface mein aur reuse karein.

2. **Prefer Explicit Types Over Any**: Jab bhi mumkin ho, explicit types ka use karein `any` ki jagah.

3. **Use Function Components and Hooks**: Modern React applications mein function components aur hooks ka use karna preferable hai.

4. **Export Your Types**: Types ko export karein taake dusre components unko reuse kar saken.

5. **Type Your Event Handlers**: Event handlers ke liye specific event types use karein.

6. **Use TypeScript with State Management**: Redux, Context API, etc., ke saath TypeScript ka use karein for type safety.

7. **Keep Props Interface Close to Components**: Component ke saath hi uski props interface define karein, better readability ke liye.

## Next Steps
[Context API in TypeScript](13_context_api_typescript.md) ko padhein. 