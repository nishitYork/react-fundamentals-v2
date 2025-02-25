# React Fundamentals: State, Props & Lifecycle Methods

---

## Session Outline
- Introduction
- State and Props
- Controlled vs Uncontrolled Components
- Prop Drilling vs Context API
- Lifecycle Methods
- Class Components vs Hooks
- useEffect Hook

- Conclusion
- Q&A

---

## Introduction

Welcome to our React fundamentals session! Today we'll cover these core concepts:
- State & Props (data management)
- Lifecycle Methods (component phases)
- useEffect Hook (side effects in functional components)
- Prop Drilling vs Context API (data sharing patterns)
- Controlled vs Uncontrolled Components (form handling)

These concepts are foundational to building effective React applications.

---

## State and Props

### What is State?

> 💡 **State is like a component's memory**

- Built-in object that allows components to hold and manage **dynamic data**
- Used for:
  - Managing form inputs
  - Handling API responses
  - Controlling component behavior
- **Mutable** - can be updated using state setters
- **Local** to a component (cannot be accessed by other components)
- **Changing state triggers re-renders**

## Controlled vs Uncontrolled Components

When working with forms in React, you have two approaches to manage form data: controlled and uncontrolled components. Understanding the difference is crucial for effective form handling.

### Controlled Components

> 💡 **In controlled components, React state is the "single source of truth"**

In a controlled component, form data is handled by React state. The component controls what happens in the form on user input.

#### How Controlled Components Work:

1. Form element values are set by React state
2. Input changes update state via event handlers
3. State updates trigger re-renders with new values

#### Example of a Controlled Component:

```jsx
import React, { useState } from 'react';

function ControlledForm() {
  // State to hold form values
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    comment: ''
  });
  
  // Handle input changes
  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData({
      ...formData,
      [name]: value
    });
  };
  
  // Handle form submission
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form submitted with:', formData);
    
    // Access form values directly from state
    const { username, email, comment } = formData;
    
    // Process data, send to API, etc.
    submitToAPI({ username, email, comment });
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
          onChange={handleChange}
        />
      </div>
      
      <div>
        <label htmlFor="email">Email:</label>
        <input
          type="email"
          id="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
        />
      </div>
      
      <div>
        <label htmlFor="comment">Comment:</label>
        <textarea
          id="comment"
          name="comment"
          value={formData.comment}
          onChange={handleChange}
        />
      </div>
      
      <button type="submit">Submit</button>
      
      {/* Real-time form data display */}
      <div>
        <h3>Form Preview:</h3>
        <p>Username: {formData.username}</p>
        <p>Email: {formData.email}</p>
        <p>Comment: {formData.comment}</p>
      </div>
    </form>
  );
}

// Simulated API function
function submitToAPI(data) {
  // In a real app, this would make an API call
  console.log('Sending to API:', data);
}
```

#### Advantages of Controlled Components:
- Direct access to form data at any time
- Ability to validate input on change
- Immediate UI updates based on input
- Enforcing input formats
- Disabling submit button until form is valid
- Conditional rendering based on input values

#### Disadvantages of Controlled Components:
- More code required for simple forms
- State management for each input field
- Potential performance issues with many inputs

### Uncontrolled Components

> 💡 **In uncontrolled components, form data is handled by the DOM itself**

Uncontrolled components rely on DOM references to access form values, rather than storing them in React state.

#### How Uncontrolled Components Work:

1. Form elements maintain their own state in the DOM
2. Refs are used to access current values when needed
3. No state updates on every input change

#### Example of an Uncontrolled Component:

```jsx
import React, { useRef } from 'react';

function UncontrolledForm() {
  // Create refs for form elements
  const usernameRef = useRef();
  const emailRef = useRef();
  const commentRef = useRef();
  
  // Handle form submission
  const handleSubmit = (e) => {
    e.preventDefault();
    
    // Access form values using refs
    const formData = {
      username: usernameRef.current.value,
      email: emailRef.current.value,
      comment: commentRef.current.value
    };
    
    console.log('Form submitted with:', formData);
    
    // Process data, send to API, etc.
    submitToAPI(formData);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="username">Username:</label>
        <input
          type="text"
          id="username"
          name="username"
          ref={usernameRef}
          defaultValue=""
        />
      </div>
      
      <div>
        <label htmlFor="email">Email:</label>
        <input
          type="email"
          id="email"
          name="email"
          ref={emailRef}
          defaultValue=""
        />
      </div>
      
      <div>
        <label htmlFor="comment">Comment:</label>
        <textarea
          id="comment"
          name="comment"
          ref={commentRef}
          defaultValue=""
        />
      </div>
      
      <button type="submit">Submit</button>
    </form>
  );
}

// Simulated API function
function submitToAPI(data) {
  // In a real app, this would make an API call
  console.log('Sending to API:', data);
}
```

#### Advantages of Uncontrolled Components:
- Less code for simple forms
- No state updates on every keystroke
- Better performance for large forms
- Can be useful when integrating with non-React code
- Easier migration from traditional HTML forms

#### Disadvantages of Uncontrolled Components:
- Less control over form input behavior
- More difficult to validate on input
- Cannot easily respond to input changes
- Form data only available after submission
- Harder to implement dynamic form behavior

### Getting Values from Both Approaches

#### From Controlled Components:
- Direct access from state at any time
- Available during typing, submission, or any lifecycle

```jsx
// Getting values in event handler
const handleSubmit = (e) => {
  e.preventDefault();
  
  // Values already in state
  console.log('Username:', formData.username);
  console.log('Email:', formData.email);
  
  // Access anywhere in component
  processFormData(formData);
};

// Getting values elsewhere in component
useEffect(() => {
  if (formData.email.includes('@company.com')) {
    setIsEmployee(true);
  }
}, [formData.email]);
```

#### From Uncontrolled Components:
- Access via refs, primarily during submission
- Form data collected only when needed

```jsx
// Getting values in event handler
const handleSubmit = (e) => {
  e.preventDefault();
  
  // Get current values from refs
  const username = usernameRef.current.value;
  const email = emailRef.current.value;
  
  console.log('Username:', username);
  console.log('Email:', email);
  
  processFormData({ username, email });
};

// Getting a value elsewhere (less common)
const checkEmail = () => {
  const email = emailRef.current.value;
  return email.includes('@company.com');
};
```

### Which Approach to Choose?

**Use Controlled Components when:**
- You need immediate validation
- You want to disable the submit button based on input
- You need to enforce input formats (e.g., formatting phone numbers as typed)
- The form needs to be dynamically generated based on user input
- You're building complex forms with interdependent fields

**Use Uncontrolled Components when:**
- Building simple forms
- Integrating with third-party DOM libraries
- Converting a traditional HTML form to React
- Performance is a concern with many form fields
- Form data is only needed on submission


### Best Practices

1. **Be consistent** - Stick with one approach throughout a form
2. **Use controlled components for most cases** - They provide more control and predictability
3. **Consider performance** for large forms with many inputs
4. **Use form libraries** like Formik or React Hook Form for complex needs
5. **Validate appropriately** - Client-side validation is easier with controlled components

---

### What is Props?

> 💡 **Props are like arguments you pass to a function**

- Used to pass data from **parent** to **child** components
- **Read-only** - cannot be modified by the child component
- Enable component reusability and composition

### How to Use State

```jsx
import React, { useState } from 'react';

function Counter() {
    // useState returns: [current state, function to update state]
    const [count, setCount] = useState(0); // Initial value: 0

    const increment = () => {
        setCount(count + 1);
    };

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={increment}>Increment</button>
        </div>
    );
}
```

### How to Use Props

```jsx
// Parent component
function App() {
    return <Greeting name="React Developer" />;
}

// Child component
function Greeting(props) {
    return (
        <div>
            <p>Hello, {props.name}!</p>
        </div>
    );
}
```

---

## Prop Drilling vs Context API

When building React applications, you'll often need to share data between components. Let's explore two approaches to solve this problem: Prop Drilling and the Context API.

### Prop Drilling

> 💡 **Prop Drilling is passing props through multiple levels of components**

Prop Drilling involves passing props from a parent component down through multiple levels of children until it reaches the component that needs the data. The intermediate components act as "tunnels" that just pass the props along.

#### Example of Prop Drilling

```jsx
// Top-level component with data
function App() {
  const [user, setUser] = useState({ name: "John", isAdmin: true });
  
  return (
    <div>
      <Header user={user} />
    </div>
  );
}

// Intermediate component that doesn't use user but passes it down
function Header({ user }) {
  return (
    <div>
      <h1>My App</h1>
      <Navbar user={user} />
    </div>
  );
}

// Component that receives the data
function Navbar({ user }) {
  return (
    <nav>
      <span>Hello, {user.name}</span>
      {user.isAdmin && <AdminPanel />}
    </nav>
  );
}
```

#### Advantages of Prop Drilling
- Simple and explicit data flow
- No additional setup required
- Easy to trace where data comes from

#### Disadvantages of Prop Drilling
- Becomes cumbersome with deeply nested components
- Creates "props pass-through" components that don't use the props
- Makes refactoring difficult as the app grows
- Can lead to "props pollution" in intermediate components

### Context API

> 💡 **Context API allows you to share data without passing props through every level**

![Context API]

Context provides a way to share data between components without having to explicitly pass props through every level of the component tree. It creates a "shortcut" that allows distant components to directly access the data they need.

#### Example of Context API

```jsx
// Create a context
const UserContext = React.createContext();

// Provider component at a high level
function App() {
  const [user, setUser] = useState({ name: "John", isAdmin: true });
  
  return (
    <UserContext.Provider value={user}>
      <div>
        <Header />
      </div>
    </UserContext.Provider>
  );
}

// Intermediate component doesn't need to receive or pass user
function Header() {
  return (
    <div>
      <h1>My App</h1>
      <Navbar />
    </div>
  );
}

// Consumer component gets data directly from context
function Navbar() {
  const user = useContext(UserContext);
  
  return (
    <nav>
      <span>Hello, {user.name}</span>
      {user.isAdmin && <AdminPanel />}
    </nav>
  );
}
```

#### Advantages of Context API
- Avoids prop drilling through intermediate components
- Simplifies component composition
- Keeps component interfaces clean
- Makes it easier to share global or semi-global state

#### Disadvantages of Context API
- Can make component reuse more difficult
- Adds complexity for simple applications
- Less explicit data flow (harder to trace)
- Performance considerations with frequent updates

### When to Use Each Approach

**Use Prop Drilling when:**
- Your component tree is relatively shallow
- The data only needs to pass through 1-2 levels
- You want to maintain explicit data flow
- The props are used by most intermediate components

**Use Context API when:**
- You have deeply nested components
- Multiple components need the same data
- Passing props becomes unwieldy
- You have "global" data like themes, user info, or preferences

### Best Practices

1. **Start with props** for simple data flow
2. **Introduce Context** when prop drilling becomes problematic
3. **Create specific contexts** for different types of data
4. **Combine with useReducer** for more complex state management
5. **Consider performance** - Context is not optimized for high-frequency updates

---

## Lifecycle Methods

### Component Lifecycle Phases

1. **Mounting** - Component is created and inserted into the DOM
2. **Updating** - Component is updated when props or state change
3. **Unmounting** - Component is removed from the DOM

---

## Class Components vs Hooks

### Class Component Lifecycle Methods

```jsx
import React, { Component } from 'react';

class DataFetcherClass extends Component {
  constructor(props) {
    super(props);
    this.state = {
      data: null,
      isLoading: true,
      error: null
    };
  }

  // Called after component is inserted into the DOM
  componentDidMount() {
    fetch('https://api.example.com/data')
      .then(response => response.json())
      .then(data => this.setState({ 
        data, 
        isLoading: false 
      }))
      .catch(error => this.setState({ 
        error, 
        isLoading: false 
      }));
  }

  // Called after state or props update
  componentDidUpdate(prevProps) {
    // Only fetch if id changed
    if (prevProps.id !== this.props.id) {
      this.setState({ isLoading: true });
      fetch(`https://api.example.com/data/${this.props.id}`)
        .then(response => response.json())
        .then(data => this.setState({ 
          data, 
          isLoading: false 
        }))
        .catch(error => this.setState({ 
          error, 
          isLoading: false 
        }));
    }
  }

  // Called right before component is removed from DOM
  componentWillUnmount() {
    // Clean up resources, cancel requests, etc.
    console.log('Component is unmounting');
  }

  render() {
    const { data, isLoading, error } = this.state;
    
    if (isLoading) return <p>Loading...</p>;
    if (error) return <p>Error: {error.message}</p>;
    if (!data) return <p>No data found</p>;
    
    return <div>{JSON.stringify(data)}</div>;
  }
}
```

### Functional Component with Hooks (Equivalent)

```jsx
import React, { useState, useEffect } from 'react';

function DataFetcherHooks({ id }) {
  const [data, setData] = useState(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  // Combines componentDidMount, componentDidUpdate, and componentWillUnmount
  useEffect(() => {
    let isMounted = true;
    setIsLoading(true);
    
    fetch(`https://api.example.com/data/${id}`)
      .then(response => response.json())
      .then(data => {
        if (isMounted) {
          setData(data);
          setIsLoading(false);
        }
      })
      .catch(error => {
        if (isMounted) {
          setError(error);
          setIsLoading(false);
        }
      });
    
    // Clean up function (equivalent to componentWillUnmount)
    return () => {
      isMounted = false;
      console.log('Component is unmounting');
    };
  }, [id]); // Dependency array (only re-run if id changes)
  
  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;
  if (!data) return <p>No data found</p>;
  
  return <div>{JSON.stringify(data)}</div>;
}
```

### Lifecycle Methods Comparison Table

| Purpose | Class Component | Functional Component |
|---------|----------------|----------------------|
| **Initialize state** | `constructor()` | `useState()` |
| **After mount** | `componentDidMount()` | `useEffect(() => {}, [])` |
| **On props/state change** | `componentDidUpdate(prevProps, prevState)` | `useEffect(() => {}, [dependencies])` |
| **Before unmount** | `componentWillUnmount()` | `useEffect(() => { return () => {} }, [])` |
| **Handle errors** | `componentDidCatch()` | `ErrorBoundary component` (no hook equivalent) |
| **Should component update** | `shouldComponentUpdate()` | `React.memo()` + `useMemo()` |

---

## useEffect Hook

### What is useEffect?

> 💡 **useEffect allows you to perform side effects in functional components**

Side effects include:
- Fetching data from APIs
- Setting up event listeners
- Updating document title
- Managing subscriptions
- Cleaning up resources

### How to Use useEffect

```jsx
import React, { useState, useEffect } from 'react';

function DataFetcher() {
    const [data, setData] = useState(null);

    useEffect(() => {
        // Side effect: Fetch data from API
        fetch('https://api.example.com/data')
            .then(response => response.json())
            .then(data => setData(data));
        
        // Cleanup function (runs before component unmounts)
        return () => {
            // Clean up resources, cancel requests, etc.
            console.log('Component is unmounting');
        };
    }, []); // Empty dependency array = run once after initial render

    return (
        <div>
            {data ? <p>{data}</p> : <p>Loading...</p>}
        </div>
    );
}
```

### Dependency Array

```jsx
// Runs once after initial render
useEffect(() => {
    console.log('Component mounted');
}, []);

// Runs after initial render and when count changes
useEffect(() => {
    console.log('Count changed:', count);
}, [count]);

// Runs after every render
useEffect(() => {
    console.log('Component rendered');
});
```

### You Might Not Need an Effect

> 💭 **Removing unnecessary Effects makes your code cleaner and more efficient**

❌ **Avoid: redundant state and unnecessary Effect**
```jsx
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');

  // 🔴 Avoid: redundant state and unnecessary Effect
  const [fullName, setFullName] = useState('');
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);
  // ...
}
```

✅ **Better: Calculate during rendering**
```jsx
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  
  // ✅ Good: calculated during rendering
  const fullName = firstName + ' ' + lastName;
  // ...
}
```

```jsx
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');

  // 🔴 Avoid: redundant state and unnecessary Effect
  const [visibleTodos, setVisibleTodos] = useState([]);
  useEffect(() => {
    setVisibleTodos(getFilteredTodos(todos, filter));
  }, [todos, filter]);

  // ...
}
```

```jsx
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  // ✅ This is fine if getFilteredTodos() is not slow.
  const visibleTodos = getFilteredTodos(todos, filter);
  // ...
}
```

```jsx
import { useMemo, useState } from 'react';

function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  const visibleTodos = useMemo(() => {
    // ✅ Does not re-run unless todos or filter change
    return getFilteredTodos(todos, filter);
  }, [todos, filter]);
  // ...
}
```

## Conclusion

- **State** and **props** are essential for data management in React
- **Class components** use lifecycle methods for side effects
- **Functional components** use hooks for the same purposes
- **useEffect** combines multiple lifecycle methods in a cleaner API
- **Prop Drilling** is simple but can be cumbersome in deep component trees
- **Context API** provides a solution for sharing data across distant components
- **Controlled components** manage form data with React state
- **Uncontrolled components** use refs to access DOM values when needed
- Best practices:
  - Prefer functional components with hooks for new code
  - Calculate values during rendering when possible
  - Use dependencies correctly in useEffect
  - Clean up resources to prevent memory leaks
  - Choose the right data sharing pattern for your application's needs
  - Select the appropriate form handling strategy based on your requirements

---

## Q&A

*Note: Prepare some common questions and answers to facilitate discussion if needed*

---

## Key Takeaway

> 🚀 **Understanding both class lifecycle methods and hooks, along with efficient data sharing patterns and form handling approaches, will help you maintain legacy code and build more efficient new React applications**
