# React Query
### 실시간으로 데이터를 계속 가져와야하는 경우에 사용

## 설치
```zsh
$ npm install react-query
```

### index.js
```js
import {QueryClient, QueryClientProvider} from 'react-query'

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
	<QueryClientProvider>
		<App />
	</QueryClientProvider>
);
```