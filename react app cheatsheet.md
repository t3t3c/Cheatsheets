# Reminder:

## If calling bind anoys you:

1. Public class field syntax to correctly bind callbacks:
   (use arrow functions)

```js
class LoggingButton extends React.Component {
  // This syntax ensures `this` is bound within handleClick.
  handleClick = () => {
    console.log('this is:', this);
  };
  render() {
    return <button onClick={this.handleClick}>Click me</button>;
  }
}
```

## Passing arguments to Event Handlers

```js
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

in both cases the e argument will be passed after id. With arrow function it is explicitly with bind it is implicitly.

## Lists and Keys

In React:
You can build collections of elements and include them in JSX using curly braces.

```js
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) => <li>{number}</li>);
```

and then we can include the entire listItems array inside `<ul>` element.

```js
<ul>{listItems}</ul>
```

When you run this code, you'll be given a warinng that a key should be provided!

Let's assign a key:

```js
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) => (
    <li key={number.toString()}>{number}</li>
  ));
  return <ul>{listItems}</ul>;
}
```

## Controlled Components

in HTML Form elements like

```html
<input>
<textarea>
<select>
```

mantain their own state.
In React state is typically kept in the state property of components.

in Combined components we are making REact state be the "single source of truth".

**the React component that renders a form also controls what happens in that form on subsequent user input.**

```js
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = { value: '' };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({ value: event.target.value });
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input
            type="text"
            value={this.state.value}
            onChange={this.handleChange}
          />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

## Data Change without Mutation for Objects

```js
var player = { score: 1, name: 'Jeff' };

var newPlayer = Object.assign({}, player, { score: 2 });
// Now player is unchanged, but newPlayer is {score: 2, name: 'Jeff'}

// Or if you are using object spread syntax, you can write:
// var newPlayer = {...player, score: 2};
```

## Lifecycle methods

# React hooks WebDevSimplified

## Everytime you modify state, make sure you use the function version:

```js
//wrong
function decrementCount() {
  setCount(count - 1);
}
```

```js
// GOOD
function decrementCount() {
  setCount((prevCount) => prevCount - 1);
}
// returns prevCount - 1 and sets this value for count
```

# ROUTING

## 1. install with `npm i react-router-dom`

## 2. Congifure the Router

1. `import { BrowserRouter } from "react-router-dom"`
2. Wrap your app in that router:

```js
<React.StrictMode>
  <BrowserRouter>
    <App />
  </BrowserRouter>
</React.StrictMode>
```

3. Defining Routes
   This is generally done at the top level of your application, such as in the App component, but can be done anywhere you want.

```js
import { Route, Routes } from 'react-router-dom';
import { Home } from './Home';
import { BookList } from './BookList';

export function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/books" element={<BookList />} />
    </Routes>
  );
}
```

4. Handling Navigation

React Router uses its own custom Link component to handle navigation.

```js
import { Route, Routes, Link } from 'react-router-dom';
import { Home } from './Home';
import { BookList } from './BookList';

export function App() {
  return (
    <>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/books">Books</Link>
          </li>
        </ul>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/books" element={<BookList />} />
      </Routes>
    </>
  );
}
```

## Advanced Route Definitions

1. Dynamic routing:

Can be useful for a specific instance of a category

Use :id

```js
import { Route, Routes, Link } from 'react-router-dom';
import { Home } from './Home';
import { BookList } from './BookList';

export function App() {
  return (
    <>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/books">Books</Link>
          </li>
        </ul>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/books" element={<BookList />} />
      </Routes>
    </>
  );
}
```

Inside of Element:

```js
import { useParams } from 'react-router-dom';

export function Book() {
  const { id } = useParams();

  return <h1>Book {id}</h1>;
}
```

Matching anything (for example for 404 page):

```js
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/books" element={<BookList />} />
  <Route path="/books/:id" element={<Book />} />
  <Route path="/books/new" element={<NewBook />} />
  <Route path="*" element={<NotFound />} />
</Routes>
```

2. Nested Routes:

```js
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/books">
    <Route index element={<BookList />} />
    <Route path=":id" element={<Book />} />
    <Route path="new" element={<NewBook />} />
  </Route>
  <Route path="*" element={<NotFound />} />
</Routes>
```

Fajowe

3. Shared Layouts:

If you don't want to create an element in every one of The childs element of a route (I would though?) then you can use shared layouts:

```js
<Routes>
  <Route path="/" element={<Home />} />
  /* Route parent gets an element that will be rendered in each of it children */
  <Route path="/books" element={<BooksLayout />}>
    <Route index element={<BookList />} />
    <Route path=":id" element={<Book />} />
    <Route path="new" element={<NewBook />} />
  </Route>
  <Route path="*" element={<NotFound />} />
</Routes>
```

Child:

The element of parent element lol will be passed where the `<Outlet />` is defined. It is a placeholder component.

```js
import { Link, Outlet } from 'react-router-dom';

export function BooksLayout() {
  // prettier-ignore
  return (
    <>
      <nav>
        <ul>
          <li><Link to="/books/1">Book 1</Link></li>
          <li><Link to="/books/2">Book 2</Link></li>
          <li><Link to="/books/new">New Book</Link></li>
        </ul>
      </nav>

      <Outlet />
    </>
  )
}
```

Sharing elements between routes that don't have a similar path:

```js
// you just don't describe the path element
<Route element={<OtherLayout />}>
  <Route path="/contact" element={<Contact />} />
  <Route path="/about" element={<About />} />
</Route>
```

# Full React Tutorial - The Net Ninja

## useEffect Hook

