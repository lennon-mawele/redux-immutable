# redux-immutable

[![Travis build status](http://img.shields.io/travis/gajus/redux-immutable/master.svg?style=flat-square)](https://travis-ci.org/gajus/redux-immutable)
[![NPM version](http://img.shields.io/npm/v/redux-immutable.svg?style=flat-square)](https://www.npmjs.org/package/redux-immutable)

This package provides a single function `combineReducers`. `combineReducers` is equivalent to the redux [`combineReducers`](http://gaearon.github.io/redux/docs/api/combineReducers.html) function, except that it expects state to be an [Immutable.js](https://facebook.github.io/immutable-js/) object.

When using redux-immutable together with [react-redux](https://www.npmjs.com/package/react-redux) use `mapStateToProps` callback of the `connect` method to transform `Immutable` object to a regular JavaScript object before passing it to the selectors, e.g.

```js
import React from 'react';

import {
    connect
} from 'react-redux';

/**
 * @param {Immutable}
 * @return {Object} state
 * @return {Object} state.ui
 * @return {Array} state.locations
 */
let selector = (state) => {
    state = state.toJS();

    // Selector logic ...

    return state;
};

class App extends React.Component {
    render () {
        return <div></div>;
    }
}

export default connect(selector)(App);
```

## Using with [webpack](https://github.com/webpack/webpack) and [Babel](https://github.com/babel/babel)

The files in `./src/` are written using ES6 features. Therefore, you need to use a source-to-source compiler when loading the files. If you are using webpack to build your project and Babel, make an exception for the `redux-immutable` source, e.g.

```js
var webpack = require('webpack');

module.exports = {
    module: {
        loaders: [
            {
                test: /\.js$/,
                exclude: [
                    /node_modules(?!\/redux\-immutable)/
                ],
                loader: 'babel'
            }
        ]
    }
};
```

## Example

### `store.js`

```js
import {
    createStore
} from 'redux';

import {
    combineReducers
} from 'redux-immutable';

import * as reducers from './reducers';

import Immutable from 'immutable';

let app,
    store,
    state = {};

state.ui = {
    activeLocationId: 1
};

state.locations = [
    {
        id: 1,
        name: 'Foo',
        address: 'Foo st.',
        country: 'uk'
    },
    {
        id: 2,
        name: 'Bar',
        address: 'Bar st.',
        country: 'uk'
    }
];

state = Immutable.fromJS(state);

app = combineReducers(reducers);
store = createStore(app, state);

export default store;
```

### `reducers.js`

```js
/**
 * @param {Immutable} state
 * @param {Object} action
 * @param {String} action.type
 * @param {Number} action.id
 */
export let ui = (state, action) => {
    switch (action.type) {
        case 'ACTIVATE_LOCATION':
            state = state.set('activeLocationId', action.id);
            break;
    }

    return state;
};

/**
 * @param {Immutable} state
 * @param {Object} action
 * @param {String} action.type
 * @param {Number} action.id
 */
export let locations = (state, action) => {
    switch (action.type) {
        // @param {String} action.name
        case 'CHANGE_NAME_LOCATION':
            let locationIndex;

            locationIndex = state.findIndex(function (location) {
                return location.get('id') === action.id;
            });

            state = state.update(locationIndex, function (location) {
                return location.set('name', action.name);
            });
            break;
    }

    return state;
};
```

### `app.js`

```js
import React from 'react';

import {
    connect
} from 'react-redux';

/**
 * @param {Immutable}
 * @return {Object} state
 * @return {Object} state.ui
 * @return {Array} state.locations
 */
let selector = (state) => {
    state = state.toJS();

    // Selector logic ...

    return state;
};

class App extends React.Component {
    render () {
        return <div></div>;
    }
}

export default connect(selector)(App);
```
