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
  🧩 block된 시그널들은 블록해제 될 동안 busy-waiting할까?
  - block된 시그널은 sigprocmask를 통해 블록이 해제되기 까지 기다려야
  - busy-waiting처럼 CPU를 의미없이 소비하면서 시그널을 확인하는게 아닌 기다리는 동안 다른 일을 하거나 대기가능!

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

🧩 시그널마스크는 뭘까?

- mask == block
- 시그널 마스크 => 처리할 시그널을 거르는 필터
- sigrpocmask => 필터의 상태를 변경해 시그널을 프로세스가 처리 가능하게 하거나 블락하게 함

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

### 예제2

```cpp
#include <stdio.h>
#include <signal.h>
#include <unistd.h>

// 시그널이 처리되었는지 확인하기 위한 전역 변수
volatile sig_atomic_t gotSIGINT = 0;

// SIGINT(CTRL + C) 시그널 핸들러
void handler(int signo) {
if (signo == SIGINT) {
gotSIGINT = 1;
write(STDOUT_FILENO, "\nSIGINT Received\n", 17); // 비동기 시그널 핸들러 내 출력
}
}

int main() {
// 1. SIGINT에 대한 시그널 핸들러 등록
struct sigaction act;
act.sa_handler = handler;
sigemptyset(&act.sa_mask);
act.sa_flags = 0;
sigaction(SIGINT, &act, NULL);

    // 2. sigsuspend()가 대기할 시그널 집합 구성
    //    모든 시그널을 막은 뒤(SIG_FILLSET), SIGINT만 허용하도록 제외
    sigset_t set;
    sigfillset(&set);
    sigdelset(&set, SIGINT);

    // 3. SIGINT을 기다리는 루프
    while (!gotSIGINT) {
        printf("SIGINT 대기 중...\n");
        // sigsuspend()는 지정한 시그널 집합에 들어오지 않는 시그널이 발생할 때까지 실행을 멈춤.
        // 여기서는 SIGINT가 들어오면 핸들러 호출 후 반환.
        sigsuspend(&set);
    }

    printf("SIGINT 처리 완료. 프로그램 종료.\n");
    return 0;

}
```

1.시그널 핸들러를 먼저 만든다. 2. fillset으로 모든 시그널 블록킹 3. delset으로 블록킹 안할 시그널 추려내기 4. SIGINT 시그널 발생 전까지 while 무한루프에 갇혀있음(블록킹 당하기때문!) 5. SIGINT 발생 후 -> while문 탈출!

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
