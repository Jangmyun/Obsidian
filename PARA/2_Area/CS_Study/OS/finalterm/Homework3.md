
### FULL CODE
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

#include <unistd.h>
#include <wait.h>
#include <errno.h>
#include <signal.h>

#define MAX_LEN 512
#define MAX_PATH 256
#define MAX_ARG 256

#define TRUE 1
#define FALSE 0

#define	DEBUG			// commenting out this line disables debugging messages
#define DEBUG_FOR_TEST

int SplitArgs(char *input, char *args[], int max_arg);

int main()
{
	printf("Welcome to my_shell1!\n");

	char prompt[32] = "my_shell $";
	char input[MAX_LEN] = "";
	char *args[MAX_ARG] = { NULL };

	while(1){
		printf("%s ", prompt);
		fgets(input, MAX_LEN, stdin);
		int len = strlen(input) - 1;
		input[len] = 0;

#ifdef	DEBUG
		printf("input command = \"%s\" (len = %d)\n", input, len);
#endif	//	DEBUG

		int no_arg = SplitArgs(input, args, MAX_ARG);
	
#ifdef	DEBUG
		printf("no_arg = %d\n", no_arg);
		for(int i = 0; i < no_arg; i++)
			printf("args[%d] = \"%s\"\n", i, args[i]);
		printf("args[%d] = %p\n", no_arg, args[no_arg]);
#endif	//	DEBUG

		if(no_arg == 0)
			continue;

		int wait_for_child = TRUE;
		// TO DO: if the last argument is "&", remove it and set wait_for_child to zero
		if(!strcmp(args[no_arg-1], "&")) {
			wait_for_child = FALSE;
			args[no_arg-1] = NULL;
		}
		
		// TO DO: if the command is "exit", break the loop
		if(!strcmp(args[0], "exit")) break;

		// TO DO: if the command is "cd", change directory by calling chdir()
			// on failure, display an error message using strerror(errno)
		if(!strcmp(args[0], "cd")){
			if(chdir(args[1]) != 0)	{
				printf("\033[31m" " Error:" "\033[0m" " %s\n", strerror(errno));
				continue;
			}
#ifdef DEBUG_FOR_TEST
			printf("Directory Changed: %s\n", args[1]);
#endif	//	DEBUG_FOR_TEST
			continue;
		}

		// TO DO: otherwise, run the command using fork() and execvp()
			// if wait_for_child is one, the parent should wait for the child
			// on failure, display error message using strerror(errono) and terminate the child
		pid_t child_pid = fork();

		if(child_pid < 0) {
			printf("\033[31m" " Error:" "\033[0m" "%s\n", strerror(errno));
			exit(1);
		}else if(child_pid == 0) {
#ifdef DEBUG_FOR_TEST
			printf("Child PID: %d\n", child_pid);

#endif	// DEBUG_FOR_TEST
			execvp(args[0], args);
			printf("\033[31m" " Error:" "\033[0m"  " %s\n", strerror(errno));
			exit(1);
		}else {
			if(wait_for_child == TRUE) wait(NULL);
			else signal(SIGCHLD, SIG_IGN);
		}
	}

	printf("Bye!\n");

	return 0;
}

int SplitArgs(char *input, char *args[], int max_arg)
// Bonus Problem (optional): extend this function to handle quoted arguments
//		- handle quoted (" or ') arguments (e.g., echo "Wecome to HGU!")
//		- handle escape character '\\' (e.g., echo Welcome\ to\ HGU!)
{
	int len = strlen(input);
	int no_arg = 0;

	for(int i = 0; i < len; i++){
		while(isspace(input[i]))
			i++;

		if(input[i] == 0)
			break;

		args[no_arg++] = &input[i];

		for(; i < len && !isspace(input[i]); i++);

		input[i] = 0;

		if(no_arg >= max_arg - 1)		// -1 is for the NULL pointer
			break;
	}

	args[no_arg] = NULL;

	return no_arg;
}
```

### `SplitArgs`

```c
int SplitArgs(char *input, char *args[], int max_arg)
{
	int len = strlen(input);
	int no_arg = 0;

	for(int i = 0; i < len; i++){
		// 1. 공백 스킵
		while(isspace(input[i]))
			i++;

		if(input[i] == 0)
			break;

		// 2. argument 시작 위치 저장
		args[no_arg++] = &input[i];

		// 3. 인자의 끝까지 이동
		for(; i < len && !isspace(input[i]); i++);

		// 4. 다음 인자 시작을 위해 현재 토큰 끝에 null문자 삽입
		input[i] = 0;

		// 5. argument 개수 초과 방지
		if(no_arg >= max_arg - 1)		// -1 is for the NULL pointer
			break;
	}

	// args 마지막에 NULL pointer 넣어서 execv 함수와 호환
	args[no_arg] = NULL;

	return no_arg;
}
```

### `SplitArgs` quoted, escape character allowed version

```c
#include <stdio.h>
#include <string.h>
#include <ctype.h>

int SplitArgs(char *input, char *args[], int max_arg)
{
	int len = strlen(input);
	int no_arg = 0;
	int i = 0;

	while (i < len) {
		// 1. 공백 건너뛰기
		while (isspace(input[i])) i++;
		if (input[i] == 0) break;

		// 2. 인자 시작
		if (no_arg >= max_arg - 1)
			break;

		char *start = &input[i];
		args[no_arg++] = start;

		int j = i;
		int quoted = 0;
		char quote_char = 0;

		while (j < len) {
			if (!quoted && (input[j] == '"' || input[j] == '\'')) {
				// 따옴표 시작x
				quoted = 1;
				quote_char = input[j];
				// 따옴표는 결과 문자열에 포함하지 않음: 문자열을 앞으로 당김
				memmove(&input[j], &input[j + 1], len - j);
				len--;
				continue;
			}
			else if (quoted && input[j] == quote_char) {
				// 따옴표 닫힘
				quoted = 0;
				memmove(&input[j], &input[j + 1], len - j);
				len--;
				continue;
			}
			else if (input[j] == '\\') {
				// 이스케이프 처리: \다음 문자를 앞으로 당김
				memmove(&input[j], &input[j + 1], len - j);
				len--;
				j++;
				continue;
			}
			else if (!quoted && isspace(input[j])) {
				break;
			}
			j++;
		}

		if (j < len)
			input[j++] = 0; // null-terminate current token

		i = j;
	}

	args[no_arg] = NULL;
	return no_arg;
}

```



