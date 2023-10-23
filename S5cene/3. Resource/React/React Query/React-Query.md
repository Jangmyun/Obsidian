## 설치
```zsh
$ npm install react-query
```

## 세팅
### index.js
```js
import {QueryClient, QueryClientProvider} from 'react-query'

const queryClient = new QueryClient();
root.render(
	<StrictMode>
		<QueryClientProvider client={queryClient}>
			<Provider store={store}>
				<BrowserRouter>
					<App />
				</BrowserRouter>
			</Provider>
		</QueryClientProvider>
	</StrictMode>
);
```