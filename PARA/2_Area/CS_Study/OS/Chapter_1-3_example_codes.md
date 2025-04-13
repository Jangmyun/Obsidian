# exec 계열

`execlp()`

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

int main() {
    pid_t pid = fork();

    if (pid < 0) {
        perror("fork 실패");
        return 1;
    }

    if (pid == 0) {
        // 자식 프로세스
        execlp("ls", "ls", "-l", NULL);
        perror("execlp 실패"); // execlp가 실패하면 이 줄이 실행됨
    } else {
        // 부모 프로세스
        wait(NULL);
        printf("자식 프로세스 종료됨\n");
    }

    return 0;
}
```

`execvp()`
```c
char *args[] = {"ls", "-a", NULL};
        execvp("ls", args);
        perror("execvp 실패");
```

