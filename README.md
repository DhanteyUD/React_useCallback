# React-useCallback
 The React `useCallback` Hook returns a memoized callback function. Think of memoization as caching a value so that it does not need to be recalculated. This allows us to isolate resource intensive functions so that they will not automatically run on every render.
 
The `useCallback` Hook only runs when one of its dependencies update. 

This can improve performance.

The `useCallback` and `useMemo` Hooks are similar. The main difference is that `useMemo` returns a memoized value and `useCallback` returns a memoized function. You can learn more about useMemo in the useMemo chapter.

## Problem

One reason to use `useCallback` is to prevent a component from re-rendering unless its props have changed.

In this example, you might think that the `Todo` component will not re-render unless the `todos` change:

> This is a similar example to the one in the React.memo repo.

### Example:

`index.js`

```js
import { useState } from "react";
import ReactDOM from "react-dom/client";
import Todo from "./Todo";

const App = () => {
  const [count, setCount] = useState(0);
  const [todos, setTodos] = useState([]);

  const increment = () => {
    setCount((count) => count + 1);
  };
  
  const addTodo = () => {
    setTodos((todo) => [...todo, "New Todo"]);
  };

  return (
    <>
      <Todos todos={todos} addTodo={addTodo} />
      <hr />
      <div>
        Count: {count}
        <button onClick={increment}>+</button>
      </div>
    </>
  );
};

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

`Todo.js`

```js
import { memo } from "react";

const Todo = ({ todos, addTodo }) => {
  return (
    <>
      <h2>My Todos</h2>
      {todos.map((todo, index) => {
        return <p key={index}>{todo}</p>;
      })}
      <button onClick={addTodo}>Add Todo</button>
    </>
  );
};

export default memo(Todo);
```

When you run this and click the count increment button, you will notice that the `Todo` component re-renders even when the `todos` do not change.

Why does this not work? We are using `memo`, so the `Todo` component should not re-render since neither the `todos` state nor the `addTodo` function are changing when the count is incremented.

This is because of something called **"referential equality"**.

Every time a component re-renders, its functions get recreated. Because of this, the `addTodo` function has actually changed.

## Solution

To fix this, you can use the `useCallback` hook to prevent the function from being recreated unless necessary.

- [x] Use the `useCallback` Hook to prevent the `Todo` component from re-rendering needlessly:

### Example:

`index.js`

```js
import { useState, useCallback } from "react";
import ReactDOM from "react-dom/client";
import Todo from "./Todo";

const App = () => {
  const [count, setCount] = useState(0);
  const [todos, setTodos] = useState([]);

  const increment = () => {
    setCount((count) => count + 1);
  };
  
  const addTodo = useCallback(() => {
    setTodos((todo) => [...todo, "New Todo"]);
  }, [todos]);

  return (
    <>
      <Todos todos={todos} addTodo={addTodo} />
      <hr />
      <div>
        Count: {count}
        <button onClick={increment}>+</button>
      </div>
    </>
  );
};

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

`Todo.js`

```js
import { memo } from "react";

const Todo = ({ todos, addTodo }) => {
  return (
    <>
      <h2>My Todos</h2>
      {todos.map((todo, index) => {
        return <p key={index}>{todo}</p>;
      })}
      <button onClick={addTodo}>Add Todo</button>
    </>
  );
};

export default memo(Todo);
```
Now the `Todo` component will only re-render when the `todos` prop changes.


