# react-redux-store //Learning notes

Redux Notes:
Action: The only way that app can interact with the store.
•	Carry some info from your app to the redux store.
•	Plain JS Objects - that have 'type' property and indicates the type of action being performed.
Reducers: Specifies how the app's state changes in response to actions sent to the store.
•	Function that accepts state and action as an arguments and returns the next state of the application.
•	(previousState,action) => newState  // this function is like pure-reducers that implements purecomponents for prevState comparisions
Redux Store:  one store for entire application
•	Holds app state
•	Allows access to state via getState().
•	Allows state to be updated via dispatch(action).
•	Register listeners via subscribe(listener).
•	Handles unregister of listeners via funtion returned by subscribe(listener).

CAKE SHOP EXAMPLE WITH CODE:
const redux = require("redux");
const createStore = redux.createStore;

const BUY_CAKE = "BUY_CAKE"; //this is react app

//creating action and type object for action
function buyCake() {
  return {
    type: BUY_CAKE,
    info: "First redux action"
  };
}

//(previousState,action) => newState
//here when the application is started the initial state of the app is passed in
//as arguement to the reducer func

const initialState = {
  numOfCakes: 10
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case BUY_CAKE:
      return {
        ...state, // by this only copy of data will bechanged
        numOfCakes: state.numOfCakes - 1 // the direct state is changed
      };
    default:
      return state;
  }
};

const store = createStore(reducer); //code execution starts from here
console.log("initial state", store.getState());
const unsubscribe = store.subscribe(() =>
  console.log("update state", store.getState())
);
//dispatch actions to update store
store.dispatch(buyCake());
store.dispatch(buyCake());
store.dispatch(buyCake());
//to unsubscribe the app jus like clear state and set initial
unsubscribe();

const redux = require("redux");
const createStore = redux.createStore;

const BUY_CAKE = "BUY_CAKE"; //this is react app
const BUY_ICECREAM = "BUY_ICECREAM";
const combineReducers = redux.combineReducers;

//creating action and type object for action
function buyCake() {
  return {
    type: BUY_CAKE,
    info: "First redux action"
  };
}

function buyIcecream() {
  return {
    type: BUY_ICECREAM
  };
}

//(previousState,action) => newState
//here when the application is started the initial state of the app is passed in
//as arguement to the reducer func

// const initialState = {
//   numOfCakes: 10,
//   numOfIcecreams: 20
// };
const initialCakeState = {
  numOfCakes: 10
};
const initialIcecreamState = {
  numOfIcecreams: 20
};

const cakeReducer = (state = initialCakeState, action) => {
  switch (action.type) {
    case BUY_CAKE:
      return {
        ...state, // by this only copy of data will bechanged
        numOfCakes: state.numOfCakes - 1 // the direct state is changed
      };
    default:
      return state;
  }
};

const icecreamReducer = (state = initialIcecreamState, action) => {
  switch (action.type) {
    case BUY_ICECREAM:
      return {
        ...state,
        numOfIcecreams: state.numOfIcecreams - 1
      };
    default:
      return state;
  }
};

const rootReducer = combineReducers({
  cake: cakeReducer,
  icecream: icecreamReducer
});

const store = createStore(rootReducer); //code execution starts from here
console.log("initial state", store.getState());
const unsubscribe = store.subscribe(() =>
  console.log(
    "update state for CAKE:",
    store.getState().cake.numOfCakes +
    " update state for ICECREAM:",
    store.getState().icecream.numOfIcecreams
  )
);
//dispatch actions to update store
store.dispatch(buyCake());
store.dispatch(buyCake());
store.dispatch(buyCake());
store.dispatch(buyIcecream());
store.dispatch(buyIcecream());
store.dispatch(buyIcecream());

//to unsubscribe the app jus like clear state and set initial
unsubscribe();


PIZZA STORE:

Index.js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";
import { Provider } from "react-redux";
import { createStore, applyMiddleware } from "redux";
import thunk from "redux-thunk";
//import * as serviceWorker from './serviceWorker';
import reducer from "./Redux/Reducer";

const store = createStore(reducer, applyMiddleware(thunk));

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);

Action.js

export function getPizza() {
  return (dispatch) => {
    return dispatch({
      type: "GET_PIZZA"
    });
  };
}

export function addPizza(data) {
  return (dispatch) => {
    return dispatch({
      type: "ADD_PIZZA",
      payload: data
    });
  };
}

export function editPizza(data) {
  return (dispatch) => {
    return dispatch({
      type: "EDIT_PIZZA",
      payload: data
    });
  };
}

export function buyPizza(pizzaId) {
  return (dispatch) => {
    return dispatch({
      type: "BUY_PIZZA",
      payload: pizzaId
    });
  };
}


App.js

import React, { Component } from "react";
//import logo from "./logo.svg";
//import "./App.css";
import PropTypes from "prop-types";
import { getPizza, addPizza, editPizza, buyPizza } from "./Redux/Action";
import { connect } from "react-redux";

class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      id: 0,
      pizzaName: ""
    };
  }

  static propTypes = {
    pizzaList: PropTypes.array.isRequired,
    getPizza: PropTypes.func.isRequired,
    addPizza: PropTypes.func.isRequired,
    editPizza: PropTypes.func.isRequired,
    buyPizza: PropTypes.func.isRequired
  };

  componentDidMount() {
    this.props.getPizza();
  }

  submitData = () => {
    if (this.state.pizzaName && !this.state.id) {
      const newPizza = {
        id: Math.floor(Math.random() * (999 - 100 + 1) + 100),
        pizzaName: this.state.pizzaName
      };

      this.props.addPizza(newPizza);
    } else if (this.state.pizzaName && this.state.id) {
      const updatedDetails = {
        id: this.state.id,
        pizzaName: this.state.pizzaName
      };

      this.props.editPizza(updatedDetails);
    } else {
      alert("Enter Pizza Name.");
    }

    this.clearData();
  };

  editDetails = (data) => {
    this.setState({
      id: data.id,
      pizzaName: data.pizzaName
    });
  };

  buyPizza = (id) => {
    this.clearData();
    if (window.confirm("Are you sure?")) {
      this.props.buyPizza(id);
    }
  };

  handleNameChange = (e) => {
    this.setState({
      pizzaName: e.target.value
    });
  };

  clearData = () => {
    this.setState({
      id: 0,
      pizzaName: ""
    });
  };

  render() {
    return (
      <div className="App">
        <header className="App-header">
          {/* <img src={logo} className="App-logo" alt="logo" /> */}
          <h1 className="App-title">List of available PIZZA:</h1>
        </header>
        <p className="App-intro">
          <div className="leftsection">
            Pizza Name :{" "}
            <input
              onChange={this.handleNameChange}
              value={this.state.pizzaName}
              type="text"
              placeholder="Pizza name"
            />{" "}
            <br />
            {this.state.id ? (
              <button onClick={this.submitData}>UPDATE</button>
            ) : (
              <button onClick={this.submitData}>ADD</button>
            )}{" "}
            <button onClick={this.clearData}>CLEAR</button>
          </div>
          <div className="rightsection">
            <table>
              <thead>
                <tr>
                  <th>ID</th>
                  <th>Pizza Name</th>
                  <th>Action(s)</th>
                </tr>
              </thead>
              <tbody>
                {this.props.pizzaList &&
                  this.props.pizzaList.map((data, index) => {
                    return (
                      <tr key={index + 1}>
                        <td>{index + 1}</td>
                        <td>{data.pizzaName}</td>
                        <td>
                          <button onClick={() => this.editDetails(data)}>
                            EDIT
                          </button>{" "}
                          <button onClick={() => this.buyPizza(data.id)}>
                            BUY
                          </button>{" "}
                        </td>
                      </tr>
                    );
                  })}
              </tbody>
            </table>
          </div>
        </p>
      </div>
    );
  }
}

const mapStateToProps = (state) => ({
  pizzaList: state.pizzaList
});

export default connect(mapStateToProps, {
  getPizza,
  addPizza,
  editPizza,
  buyPizza
})(App);



Reducer.js

const initialstate = {
  pizzaList: [
    { id: 1, pizzaName: "Paneer Pizza" },
    { id: 2, pizzaName: "Corn Pizza" },
    { id: 3, pizzaName: "Mexican mix-veg Pizza" }
  ]
};

const reducer = (state = initialstate, action) => {
  switch (action.type) {
    case "GET_PIZZA":
      return {
        ...state
      };
    case "ADD_PIZZA":
      return {
        ...state,
        pizzaList: state.pizzaList.concat(action.payload)
      };
    case "EDIT_PIZZA":
      return {
        ...state,
        pizzaList: state.pizzaList.map((content, i) =>
          content.id === action.payload.id
            ? {
                ...content,
                pizzaList: action.payload.pizzaList
              }
            : content
        )
      };
    case "BUY_PIZZA":
      return {
        ...state,
        pizzaList: state.pizzaList.filter((item) => item.id !== action.payload)
      };
    default:
      return state;
  }
};

export default reducer;


SANDBOX LINK:
https://codesandbox.io/s/wizardly-bird-kcosu?file=/src/App.js:2040-2045
