# Three Principles

Redux can be described in three fundamental principles:

### Single source of truth (**单一数据源**)

**The [state](../Glossary.md#state) of your whole application is stored in an object tree within a single [store](../Glossary.md#store).**
**应用程序的 [state](../Glossary.md#state)全部存储在[store](../Glossary.md#store).对象的对象树中。**
This makes it easy to create universal apps, as the state from your server can be serialized and hydrated into the client with no extra coding effort. A single state tree also makes it easier to debug or introspect an application; it also enables you to persist your app’s state in development, for a faster development cycle. Some functionality which has been traditionally difficult to implement - Undo/Redo, for example - can suddenly become trivial to implement, if all of your state is stored in a single tree.

从服务器端返回的state可以通过 serialized 和 hydrated 的方式保存在客户端，使得创建 通用的 apps 更加便利。单一状态树可以更方便的调试或者introspect 应用程序。也可以在开发环节通过将应用程序状态持久化，实现快速开发迭代。如果所有的应用程序状态都存储在单一状态树种，某些在传统模式比较难实现的操作都可以快速实现。
```js
console.log(store.getState())

/* Prints
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    }, 
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
*/
```

### State is read-only (**只读模式的state**)

**The only way to mutate the state is to emit an [action](../Glossary.md#action), an object describing what happened.**

**只能够通过触发一个action(保存事件状态),使得state发生变化**

This ensures that neither the views nor the network callbacks will ever write directly to the state. Instead, they express an intent to mutate. Because all mutations are centralized and happen one by one in a strict order, there are no subtle race conditions to watch out for. As actions are just plain objects, they can be logged, serialized, stored, and later replayed for debugging or testing purposes.

这确保了无论views还是network的回调都能够直接写入state.通过以intent的形式触发。由于所有的变化都以集中处理，按照严格的顺序执行，因此不需要检查race conditions。由于是简单对象，因此state可以被记录，序列化，存储或者重新触发以用来调试和测试目的。

```js
store.dispatch({
  type: 'COMPLETE_TODO',
  index: 1
})

store.dispatch({
  type: 'SET_VISIBILITY_FILTER',
  filter: 'SHOW_COMPLETED'
})
```

### Changes are made with pure functions (pure functions 触发变更)

**To specify how the state tree is transformed by actions, you write pure [reducers](../Glossary.md#reducer).**

**实现pure function 则可实现在action调用的过程中对state  tree 进行处理**

Reducers are just pure functions that take the previous state and an action, and return the next state. Remember to return new state objects, instead of mutating the previous state. You can start with a single reducer, and as your app grows, split it off into smaller reducers that manage specific parts of the state tree. Because reducers are just functions, you can control the order in which they are called, pass additional data, or even make reusable reducers for common tasks such as pagination.

```js

function visibilityFilter(state = 'SHOW_ALL', action) {
  switch (action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter
    default:
      return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case 'COMPLETE_TODO':
      return [
        ...state.slice(0, action.index),
        Object.assign({}, state[action.index], {
          completed: true
        }),
        ...state.slice(action.index + 1)
      ]
    default:
      return state
  }
}

import { combineReducers, createStore } from 'redux'
let reducer = combineReducers({ visibilityFilter, todos })
let store = createStore(reducer)
```

That’s it! Now you know what Redux is all about.
