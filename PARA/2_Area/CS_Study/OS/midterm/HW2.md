# HW2-2

### 디렉토리 관련

`DIR* open(char* path)`


```c
DIR *dir directory = NULL;
struct *dirent entry = NULL;
struct stat st;
```


#### directory인지 체크
```c
S_ISDIR(st.st_mode);
```


#### tokenizer

```c
char** tokenizeLin(char *line){
	int token_no = 0;
	const char* token_delimiter = " \n\a\t\r";

		char** tokens = (char**)malloc(sizeof(char*)*BUFFER_SIZE);
		char* token=NULL;

		if(!tokens){
			exit(1);
		}

		token = strtok(line, token_delimiter);

		while(token != NULL) {
			tokens[token_no] = token;
			token_no++;

			token = strtok(NULL, token_delimiter);
		}

		tokens[i] = NULL;
		return tokens;
}
```