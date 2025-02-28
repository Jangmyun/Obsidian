### 먼저 import할 필요 없는 컴포넌트를 필요할때 import하기
```jsx
import {lazy, Suspense} from 'react'

const Component = lazy(()=>{
	import('./compoenent/Component.js')
})

function App(){
	return(
		<Suspense fallback={<div>로딩중</div>}>
			<Component/>
		</Suspense>
	)
}
```
