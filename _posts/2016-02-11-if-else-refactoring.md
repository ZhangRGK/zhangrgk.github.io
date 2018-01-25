---
layout: post
title: IF-ELSE 重构
category: program
---

终于追上了前端的步伐，开始尝试 react.

干脆一口气就连 redux 也用起来。

redux 里的 reducer 如果用传统的 if-else 写会比较丑。

~~~
const addTodo = function(state, action) {
  //...
}
const editTodo = function(state, action) {
  //...
}
const removeTodo = function(state, action) {
  //...
}
const def = function() {
  return state
}

const initialState = [{
  //...
}]

const todoReducer = function(state=initialState, action) {
  if(action.type == 'ADD_TODO') {
    return addTodo(state, action)
  } else if(action.type == 'EDIT_TODO') {
    return editTodo(state, action)
  } else {
    return def()
  }
} 
~~~
{: .javascript}

或者用 switch 看起来也很丑

~~~
const todoReducer = function(state=initialState, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return addTodo()

    case 'EDIT_TODO':
      return editTodo()

    default:
      return def()
  }
}
~~~
{: .javascript}

而且以上两种写法每次增加新的 Reducer 都需要增加一段代码，应该重构成以下的方式:

~~~
export default function projects(state = initialState, action) {
  let f = {
    ADD_TODO: addProject, 
    EDIT_TODO: removeProject,
  };
  return (f[action.type] || def)();
}
~~~
{: .javascript}

代码短了很多，而且让 Reducer 和 action.type 的对应关系更加清楚。