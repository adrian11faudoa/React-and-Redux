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

Step 2


        <<React
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
  // Add handleChange() and submitMessage() methods here
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

        <<Redux
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

        <<Provider
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
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <Provider store={store}>
        <DisplayMessages/>
      </Provider>
    );
  }
};


        <<Dispatch to Props
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


        <<Connect Redux to React
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

    <<Connect Redux to the Messages App
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

    <<Extract Local State into Redux
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

    <<  
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


