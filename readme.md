# Redux-State-Sync 2.0

A lightweight middleware to sync your redux state across browser tabs. It will listen to the Broadcast Channel and dispatch exactly the same actions dispatched in other tabs to keep the redux state in sync.


[<img src="https://img.shields.io/travis/AOHUA/redux-state-sync.svg">](https://travis-ci.org/AOHUA/redux-state-sync)
[<img src="https://img.shields.io/npm/dm/redux-state-sync.svg">](https://www.npmjs.com/package/redux-state-sync)

### How to install

Install with npm.

```
npm install --save redux-state-sync
```
Install with yarn
```
yarn add redux-state-sync
```

### How to use

Create the state sync middleware with config:


```javascript
import { createStore, applyMiddleware } from 'redux'
import { createStateSyncMiddleware } from 'redux-state-sync';

const config = {
  // TOGGLE_TODO will not be triggered in other tabs
  blacklist: ['TOGGLE_TODO'],
}
const middlewares = [
  createStateSyncMiddleware(config),
];
 
const store = createStore(rootReducer, {}, applyMiddleware(...middlewares));
```

Init new tabs with existing state:

1. Use initStateWithPrevTab to get existing state from other tabs
```javascript
import { createStore, applyMiddleware } from 'redux'
import { createStateSyncMiddleware, initStateWithPrevTab } from 'redux-state-sync';

const config = {
  // TOGGLE_TODO will not be triggered in other tabs
  blacklist: ['TOGGLE_TODO'],
}
const middlewares = [
  createStateSyncMiddleware(config),
];
 
const store = createStore(rootReducer, {}, applyMiddleware(...middlewares));
// init state with other tabs
initStateWithPrevTab(store);
```
2. Wrap your root reducer with `withReduxStateSync`
```javascript
import { withReduxStateSync } from 'redux-state-sync'
 
const rootReducer = combineReducers({
  todos,
  visibilityFilter
})

export default withReduxStateSync(rootReducer)
```

##### Note: ignore this if you are using `redux-persist`, because you will always inite your app with the state in the storage. However, if you don't want to persist the state in the storage and still want to init new tabs with opening tabs' state, you can follow the example above.

### Config
#### channel
Unique name for Broadcast Channel
type: `String`
default: "redux_state_sync"

```javascript
const config = {
  channel: 'my_broadcast_channel',
}
const middlewares = [
  createStateSyncMiddleware(config),
];
```
#### predicate
A function to let you filter the actions as you wanted.
type: `Function`
default: null

```javascript
const config = {
  // All actions will be triggered in other tabs except 'TOGGLE_TODO'
  predicate: actionType => actionType !== 'TOGGLE_TODO'
}
const middlewares = [
  createStateSyncMiddleware(config),
];
```
#### blacklist
A list of action types that you don't want to be triggered in other tabs.
type: `ArrayOf(<String>)`
default: []

```javascript
const config = {
  // All actions will be triggered in other tabs except 'TOGGLE_TODO'
  blacklist: ['TOGGLE_TODO']
}
const middlewares = [
  createStateSyncMiddleware(config),
];
```
#### whitelist
Only actions in this list will be triggered in other tabs.
type: `ArrayOf(<String>)`
default: []

```javascript
const config = {
  // Only 'TOGGLE_TODO' will be triggered in other tabs
  whitelist: ['TOGGLE_TODO']
}
const middlewares = [
  createStateSyncMiddleware(config),
];
```
##### Warning: You should only use one of the option to filter your actions. if you have all 3 options predicate, blacklist, and whitelist, only one will be effective and the priority is predicate > blacklist > whitelist.

#### broadcastChannelOption
Redux-state-sync is using [BroadcastChannel](https://github.com/pubkey/broadcast-channel) to comunicate between tabs. broadcastChannelOption is the option passed to broadcastChannel when we creating the channel.
type: `Object`
default: null
```javascript
const config = {
  // Only 'TOGGLE_TODO' will be triggered in other tabs
  whitelist: ['TOGGLE_TODO'],
  // enforce a type, oneOf['native', 'idb', 'localstorage', 'node']
  broadcastChannelOption: { type: 'localstorage' },
}
const middlewares = [
  createStateSyncMiddleware(config),
];
```
