Subject:

**React** is a **view library** that you provide with data, 
then it renders the view in an efficient, predictable way. 

**Redux** is a **state management framework** that you can use to simplify the management of your application's state. 

Typically, in a **React Redux app**, you create a single Redux store that manages the state of your entire app.

Your **React components** subscribe to only the pieces of data in the store that are relevant to their role. 

Then, you dispatch actions directly from **React components**, which then trigger store updates.

Because Redux is not designed to work with React out of the box, you need to use the **react-redux package** 

It provides a way for you to pass Redux state and dispatch to your React components as props

Example Code: Step 1
```
  class DisplayMessages extends React.Component {
    constructor(props){
      super(props);
      this.state = {
        input: '',
        messages: []
      }
    }
    render() {
      return <div />
    }
  };
```


Example Code: Step 2
```
  class DisplayMessages extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        input: '',
        messages: []
      }
      this.handleChange = this.handleChange.bind(this);
      this.submitMessage = this.submitMessage.bind(this);
    }
    handleChange(e){
      this.setState({
        input: e.target.value
      })
    }
    submitMessage(){
      let inputVar = this.state.input;
      let arr = [...this.state.messages, inputVar];
      this.setState({
        input: "",
        messages: arr
      })
      return
    }
    render() {
      let items = this.state.messages.map(item => <li>{item}</li>);
      return (
        <div>
          <h2>Type in a new Message:</h2>
          <input onChange={this.handleChange} value={this.state.input}></input>
          <button onClick={this.submitMessage}>Click Me</button>
          <ul>
            <li>{items}</li>
          </ul>
        </div>
      );
    }
  };
```


Example Code: Step 3
```
  const ADD = "ADD";
  const addMessage = (message) => {
    return {
      type: ADD,
      message
    }
  }
  const messageReducer = (state = [], action) => {
    switch(action.type) {
        case ADD:
          return [...state, action.message]
        default:
          return state;
      }
  }
  const store = Redux.createStore(messageReducer)
```


**React Redux** provides a small API with two key features: **Provider** and **connect**.

The **Provider** is a wrapper component from React Redux that wraps your **React app**. 

This wrapper then allows you to access the Redux store and dispatch functions throughout your component tree.

**Provider** takes two props, the Redux store and the child components of your app. 

Example Code: Defining the Provider for an App
```
  <Provider store={store}>
    <App/>
  </Provider>
```

Example Code: Step 4
```
  // Redux:
  const ADD = 'ADD';
  const addMessage = (message) => {
    return {
      type: ADD,
      message
    }
  };
  const messageReducer = (state = [], action) => {
    switch (action.type) {
      case ADD:
        return [
          ...state,
          action.message
        ];
      default:
        return state;
    }
  };
  const store = Redux.createStore(messageReducer);

  // React:
  class DisplayMessages extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        input: '',
        messages: []
      }
      this.handleChange = this.handleChange.bind(this);
      this.submitMessage = this.submitMessage.bind(this);
    }
    handleChange(event) {
      this.setState({
        input: event.target.value
      });
    }
    submitMessage() {  
      this.setState((state) => {
        const currentMessage = state.input;
        return {
          input: '',
          messages: state.messages.concat(currentMessage)
        };
      });
    }
    render() {
      return (
        <div>
          <h2>Type in a new Message:</h2>
          <input
            value={this.state.input}
            onChange={this.handleChange}/><br/>
          <button onClick={this.submitMessage}>Submit</button>
          <ul>
            {this.state.messages.map( (message, idx) => {
                return (
                  <li key={idx}>{message}</li>
                )
              })
            }
          </ul>
        </div>
      );
    }
  };
  const Provider = ReactRedux.Provider;
  class AppWrapper extends React.Component {
    render() {
      return (
        <Provider store={store}>
          <DisplayMessages/>
        </Provider>
      );
    }
  };
```
**Note**: React Redux is available as a **global variable** here, so you can access the Provider with dot notation. 
The code in the editor takes advantage of this and sets it to a **constant Provider** for you to use in the **AppWrapper render method**.


The Provider component allows you to provide **state** and **dispatch** to your React components, but you must specify exactly what state and actions you want. 

This way, you make sure that each component only has access to the state it needs. 

You accomplish this by creating two functions: **mapStateToProps()** and **mapDispatchToProps()**

In these functions, you declare **what pieces of state you want** to have access to and **which action creators you need** to be able to dispatch. 

**Note**: Behind the scenes, React Redux uses the **store.subscribe()** method to implement **mapStateToProps()**

Example Code: Step 5
```
  const state = [];
  const mapStateToProps = (state) => {
    return {
      messages: state
    }
  }
```


The **mapDispatchToProps()** function is used to provide specific **action creators** to your **React components** so they can dispatch actions against the Redux store. 

It returns an object that **maps dispatch actions to property names**, which become component props. 

However, instead of returning a piece of state, each property returns a function that calls dispatch with an action creator and any relevant action data. 

You have access to this dispatch because it's passed in to **mapDispatchToProps()** as a parameter when you define the function

Behind the scenes, React Redux is using Redux's **store.dispatch()** to conduct these **dispatches** with **mapDispatchToProps()**

For example, you have a **loginUser() action creator** that takes a username as an action payload. 

Example Code:
```
  {
    submitLoginUser: function(username) {
      dispatch(loginUser(username));
    }
  }
```

Example Code: Step 6
```
  const addMessage = (message) => {
    return {
      type: 'ADD',
      message: message
    }
  };
  const mapDispatchToProps = (dispatch) => {
    return {
      submitNewMessage: function(message) {
        dispatch(addMessage(message))
      }
    }
  }
```


The **connect** method from React Redux takes two optional arguments, **mapStateToProps()** and **mapDispatchToProps()**

They are optional because you may have a component that only needs access to state but doesn't need to dispatch any actions, or vice versa.

To use this method, pass in the functions as arguments, and immediately call the result with your component. 

Example Code:
```
  connect(mapStateToProps, mapDispatchToProps)(MyComponent)
```

**Note**: If you want to omit one of the arguments to the connect method, you pass null in its place.

Example Code: Step 7
```
  const addMessage = (message) => {
    return {
      type: 'ADD',
      message: message
    }
  };
  const mapStateToProps = (state) => {
    return {
      messages: state
    }
  };
  const mapDispatchToProps = (dispatch) => {
    return {
      submitNewMessage: (message) => {
        dispatch(addMessage(message));
      }
    }
  };
  class Presentational extends React.Component {
    constructor(props) {
      super(props);
    }
    render() {
      return <h3>This is a Presentational Component</h3>
    }
  };
  const connect = ReactRedux.connect;
  const ConnectedComponent = connect(mapStateToProps, mapDispatchToProps)(Presentational)
```


**Presentational**. 
This term generally refers to React components that are not directly connected to Redux. 

They are simply responsible for the presentation of UI and do this as a function of the props they receive. 

By contrast, **container components** are connected to Redux. 

These are typically responsible for dispatching actions to the store and often pass store state to child components as props.

Example Code: Step 8
```
  // Redux:
  const ADD = 'ADD';
  const addMessage = (message) => {
    return {
      type: ADD,
      message: message
    }
  };
  const messageReducer = (state = [], action) => {
    switch (action.type) {
      case ADD:
        return [
          ...state,
          action.message
        ];
      default:
        return state;
    }
  };
  const store = Redux.createStore(messageReducer);
  
  // React:
  class Presentational extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        input: '',
        messages: []
      }
      this.handleChange = this.handleChange.bind(this);
      this.submitMessage = this.submitMessage.bind(this);
    }
    handleChange(event) {
      this.setState({
        input: event.target.value
      });
    }
    submitMessage() {
      this.setState((state) => {
        const currentMessage = state.input;
        return {
          input: '',
          messages: state.messages.concat(currentMessage)
        };
      });
    }
    render() {
      return (
        <div>
          <h2>Type in a new Message:</h2>
          <input
            value={this.state.input}
            onChange={this.handleChange}/><br/>
          <button onClick={this.submitMessage}>Submit</button>
          <ul>
            {this.state.messages.map( (message, idx) => {
                return (
                  <li key={idx}>{message}</li>
                )
              })
            }
          </ul>
        </div>
      );
    }
  };
  
  // React-Redux:
  const mapStateToProps = (state) => {
    return { messages: state }
  };
  const mapDispatchToProps = (dispatch) => {
    return {
      submitNewMessage: (newMessage) => {
        dispatch(addMessage(newMessage))
      }
    }
  };
  const Provider = ReactRedux.Provider;
  const connect = ReactRedux.connect;
  const Container = connect(mapStateToProps, mapDispatchToProps)(Presentational)
  class AppWrapper extends React.Component {
    constructor(props) {
      super(props);
    }
    render() {
      return (
        <Provider store={store}>
          <Container/>
        </Provider>
        );
    }
  };
```


Now that **Redux is connected**, you need to extract the state management out of the Presentational component and into Redux

Once these changes are made, the app will continue to function the same, except Redux manages the state. 

This example also illustrates how a component may have local state: your component still tracks user **input** locally in its own state

Example Code: Step 9
```
  // Redux:
  const ADD = 'ADD';
  const addMessage = (message) => {
    return {
      type: ADD,
      message: message
    }
  };
  const messageReducer = (state = [], action) => {
    switch (action.type) {
      case ADD:
        return [
          ...state,
          action.message
        ];
      default:
        return state;
    }
  };
  const store = Redux.createStore(messageReducer);
  
  // React:
  const Provider = ReactRedux.Provider;
  const connect = ReactRedux.connect;
  class Presentational extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        input: ''
      }
      this.handleChange = this.handleChange.bind(this);
      this.submitMessage = this.submitMessage.bind(this);
    }
    handleChange(event) {
      this.setState({
        input: event.target.value
      });
    }
    submitMessage() {
      this.props.submitNewMessage(this.state.input)
      this.setState((state) => ({
        input: ''
      }));
    }
    render() {
      return (
        <div>
          <h2>Type in a new Message:</h2>
          <input
            value={this.state.input}
            onChange={this.handleChange}/><br/>
          <button onClick={this.submitMessage}>Submit</button>
          <ul>
            {this.props.messages.map( (message, idx) => {
                return (
                  <li key={idx}>{message}</li>
                )
              })
            }
          </ul>
        </div>
      );
    }
  };
  
  const mapStateToProps = (state) => {
    return {messages: state}
  };
  
  const mapDispatchToProps = (dispatch) => {
    return {
      submitNewMessage: (message) => {
        dispatch(addMessage(message))
      }
    }
  };
    
  //diff way to write mapDispatchToProps = {submitNewMessage: addMessage};

  const Container = connect(mapStateToProps, mapDispatchToProps)(Presentational);
  class AppWrapper extends React.Component {
    render() {
      return (
        <Provider store={store}>
          <Container/>
        </Provider>
      );
    }
  };
```


Typically, you won't write React apps in a code editor like this. 

This challenge gives you a glimpse of what the syntax looks like if you're working with a file system on your own machine

Example Code: Step 10
```
  /*
  import React from 'react'
  import ReactDOM from 'react-dom'
  import { Provider, connect } from 'react-redux'
  import { createStore, combineReducers, applyMiddleware } from 'redux'
  import thunk from 'redux-thunk'
  import rootReducer from './redux/reducers'
  import App from './components/App'
  const store = createStore(
    rootReducer,
    applyMiddleware(thunk)
  );
  ReactDOM.render(
    <Provider store={store}>
      <App/>
    </Provider>,
    document.getElementById('root')
  );
  */
```


