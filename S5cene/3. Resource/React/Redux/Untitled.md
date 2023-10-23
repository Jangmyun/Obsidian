# # 리덕스 대충요약



```js
import {configureStore} from '@reduxjs/toolkit'

// store를 생성할 때 넘겨줘야할 reducer
function counterReducer(state, action){
  if(action.type === 'counter/increment'){
    return {
      ...state,
      value: state.value+1
    }
  }
  return state
}

const store = configureStore({
  reducer: counterReducer
})


```

