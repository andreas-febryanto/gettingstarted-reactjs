# State and Lifecycle



## Adding Local State to a Class

```jsx

class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

Changing local state to a class in 3 steps:

> 1. Replace is {this.props.date.toLocaleTimeString() to this.state.date in the render() method
> 
> 2. Add a class constructor that assigns the initial this.state
> 
> 3. Remove the date prop from the <Clock/> element
> 
> ```jsx
> class Clock extends React.Component {
>   constructor(props) {
>     super(props);
>     this.state = {date: new Date()};
>   }
> 
>   render() {
>     return (
>       <div>
>         <h1>Hello, world!</h1>
>         <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
>       </div>
>     );
>   }
> }
> 
> const root = ReactDOM.createRoot(document.getElementById('root'));
> root.render(<Clock />);
> ```



## Adding Lifecycle Methods to a Class

In applications with many components, it’s very important to free up resources taken by the components when they are destroyed. We can declare special methods on the component class to run some code when a component mounts and unmounts:

> The `componentDidMount()` method runs after the component output has been rendered to the DOM. This is a good place to set up a timer:

```jsx

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }
```



## Using State Correctly

### Do not Modify State Directly

```jsx
// Wrong
this.state.comment = 'Hello'
// Correct
this.setState({comment: 'Hello'});
```

### State Updates May Be Asynchronous

React may batch multiple `setState()` calls into a single update for performance.

Because `this.props` and `this.state` may be updated asynchronously, you should not rely on their values for calculating the next state.

```jsx
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
// Correct
this.setState(function(state, props) {
  return {
    counter: state.counter + props.increment
  };
});

```

### State Updates are Merged

When you call `setState()`, React merges the object you provide into the current state.

```jsx
 constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }
 
  componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  } 
```

The merging is shallow, so `this.setState({comments})` leaves `this.state.posts` intact, but completely replaces `this.state.comments`.



## The Data Flows Down

Neither parent nor child components can know if a certain component is stateful or stateless, and they shouldn’t care whether it is defined as a function or a class. This is why state is often called local or encapsulated. It is not accessible to any component other than the one that owns and sets it.



A component may choose to pass its state down as props to its child components:

```jsx
<FormattedDate date={this.state.date} />

function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}
```

This is commonly called a “top-down” or “unidirectional” data flow. Any state is always owned by some specific component, and any data or UI derived from that state can only affect components “below” them in the tree.

If you imagine a component tree as a waterfall of props, each component’s state is like an additional water source that joins it at an arbitrary point but also flows down.

To show that all components are truly isolated, we can create an `App` component that renders three `<Clock>`s:

```jsx
function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  );
}
```
