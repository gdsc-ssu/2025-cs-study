## Signal (2)

⭐️ 표시가 들어있다면 중요한 개념, 없으면 그냥 이런게 있구나~ 느끼고 가시면 됩니다.

### ✅ Intro

- 저번 시간에는 signal이 무엇인지 알아봤다.
- 마지막에 signal이 무시될 수도 있다고 말했고, signal 집합이 있다고 말했다.
- 이번 시간은 signal 집합인 sigset_t를 알아볼 것이고, 이게 어떤 set들로 구성되어있는지 살펴볼것이다.
- 특히 sigprocmask에서 정신을 잃을수 있으니 조심할것.

### ⭐️sigset_t

- 신호를 관리하기 위한 집합
- 사용 예시

```cpp
sigset_t set;
sigset(&set);
```

- sigset_t는 두가지로 이루어짐
  - pending된 set
  - block된 set
  - 디폴트 값은 Pending set

### ✅ block set

- block : 시그널을 보내도 안받겠다
- 차단된 신호는 즉시 처리되지 않고 대기 상태로 유지
- 후술할 sigprocmask 함수들을 사용해 block set을 설정하거나 수정 가능
- 특정 신호를 일시적으로 무시하거나 처리하지 않도록 설정시 사용

```cpp
#include <signal.h>
#include <stdio.h>

int main() {
    sigset_t blockSet;
    sigemptyset(&blockSet);
    sigaddset(&blockSet, SIGINT); // SIGINT 신호를 블록 세트에 추가

    if (sigprocmask(SIG_BLOCK, &blockSet, NULL) == -1) {
        perror("sigprocmask");
        return 1;
    }

    printf("SIGINT 신호가 블록되었습니다.\n");
    // 여기서 SIGINT 신호는 처리되지 않고 대기 상태로 유지

    return 0;
}

```

### ✅ pending set

- pending : 바빠서 처리를 못함, 차단된 상태에서 도착하여 대기중
- **block set 에 의해 처리되지 못한 신호들이 여기에 저장**
- 블록된 신호들이 언제 도착했는지 추적 가능
- 블록이 해제될 때 pending set에 있는 신호들 처리 가능

```cpp
#include <signal.h>
#include <stdio.h>

int main() {
    sigset_t pendingSet;

    // 현재 펜딩 세트를 가져옴
    if (sigpending(&pendingSet) == -1) {
        perror("sigpending");
        return 1;
    }

    // SIGINT 신호가 펜딩 세트에 있는지 확인
    if (sigismember(&pendingSet, SIGINT)) {
        printf("SIGINT 신호가 펜딩 세트에 있습니다.\n");
    } else {
        printf("SIGINT 신호가 펜딩 세트에 없습니다.\n");
    }

    return 0;
}

```

### ⭐️ sigprocmask

```cpp
int sigprocmask(int how,sigset_t *newset,sigset_t *oldset);
```

- 현재의 시그널 마스크를 검사하거나 변경하는 시스템콜 함수
- 시그널 발생 시 나중에 시그널 처리하기 위해 시그널을 블록시킨다.
- 특정 작업 실행하기 위해 시그널을 블록시키고, 특정 작업 실행 마치고 난 후 블록해제하는데 사용

**how에 들어가는 인자**

| 이름        | 역할                                                                                                          |
| ----------- | ------------------------------------------------------------------------------------------------------------- |
| SIG_BLOCK   | 현재 시그널 마스크와 시그널 집합의 합집합이 프로세스의 새 프로세스 마스크                                     |
| SIG_UNBLOCK | 블록된 시그널에 대해 블록 해제시 사용                                                                         |
| SIG_SETMASK | 이전 블록된 시그널 집합을 모두 지우고, set이 가리키는 시그널 집합 값이 프로세스의 새로운 시그널 마스크로 설정 |

### sigemptyset

```cpp
int sigemptyset(sigset_t *set);
```

- set인자가 가리키는 시그널 집합을 빈집합으로

### sigaddset

```cpp
int sigaddset(sigset_t *set, int signo);
```

- 인자로 받는 signo에 대한 시그널을 시그널 집합에 추가

### sigdelset

```cpp
int sigdelset(sigset_t *set,int signo);
```

- 인자로 받는 signo에 대한 시그널을 시그널 집합에서 제거

### 예제

```cpp
#include <signal.h>

sigset_t set;
sigemptyset(&set);           // 초기화
sigaddset(&set, SIGINT);     // SIGINT 신호 추가

// SIGINT 신호를 차단
if (sigprocmask(SIG_BLOCK, &set, NULL) == -1) {
    perror("sigprocmask");
}

```

### ⭐️ sigpending

```cpp
#include <signal.h>
int sigpending(sigset *set);
```

- block 중이거나 pending 중일때 시그널이 오고 싶은걸 알고 싶을때 사용
- 특히 block 중일때 시그널이 온것을 알고 싶을 때 사용
- pending set은 sigpending과 관련있고, block set은 sigsuspend와 관려있다.
- set인자가 가리키는 곳에 호출한 프로세스의 시그널이 블록되어 있으며 현재 pending중인 시그널 집합들이 저장됨.
- sigpending 예제는 sigaction과 같이 봐야 이해가 되기에 다음주에 함께 첨부할 예정

### ✅ Outro

- 이번 장부터 시그널이 매우 어려워지기 시작했다.
- 다만 시그널의 매력은 디몬에서 나오는 것 같다! 좀만 더 기다려달라구!
