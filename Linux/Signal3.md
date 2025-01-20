# Signal (3)

## ✅ Intro

- 지난 시간까지 pending set과 block set에 대해 배웠다.
- 이번 시간에는 sigaction, sigsuspend을 배워볼것이다.
- 그리고 디몬과 시그널이 어떤 관계인지 설명할 예정이다.

## Sigaction

- 특정 시그널에 대한 액션을 검사하거나 변경 가능하게 하는 시스템콜 함수이다.
- sigaction은 구조체이기 때문에 포인터를 사용해야한다.

```cpp
#include <signal.h>
int sigaciton(int signo, struct sigaction* newact, struct sigaction* oldact);
```

```cpp
 struct sigaction {
               void     (*sa_handler)(int);
               void     (*sa_sigaction)(int, siginfo_t *, void *);
               sigset_t   sa_mask;
               int        sa_flags;
               void     (*sa_restorer)(void);
};
```

- sa_handler : 세가지의 기능을 가진다.
  | 설정 값 | 설명 |
  | ------------------------- | ------------------------------------- |
  | `SIG_IGN` | 시그널을 무시함 (Ignore) |
  | `SIG_DFL` | 시그널의 기본 동작 수행 (Default) |
  | `사용자 정의 핸들러 함수` | 시그널이 발생했을 때 실행할 함수 지정 |
- sa_mask : 추가적으로 지정하는 시그널 집합이 들어가는데, SIGKILL과 SIGSTOP은 들어갈 수 없다
- 처리중인데 시그널 보내면 블록된다.

### 예제로 알아보기

- SIGINT 가로채고, 프로그램 종료 전 메세지 출력

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <unistd.h>

// SIGINT 핸들러
void handle_sigint(int signo) {
    printf("\n SIGINT (Ctrl+C)! \n");
    exit(0);
}

int main() {
    struct sigaction sa;

    sa.sa_handler = handle_sigint; // SIGINT 발생 시 실행할 핸들러 설정
    sigemptyset(&sa.sa_mask); // 추가적으로 블록할 시그널 없음
    sa.sa_flags = 0;

    if (sigaction(SIGINT, &sa, NULL) == -1) {
        perror("sigaction");
        exit(EXIT_FAILURE);
    }

    printf("CTRL+C 를 눌러서 SIGINT를 발생시키세요!\n");

    while (1) {
        printf("Running...\n");
        sleep(2);
    }

    return 0;
}

```

## Sigsuspend

- sigprocmask + pause하기 위함
  - 원자적 연산, 즉 한번에 처리하기 위해 sigsuspend를 만듦
  - 특정 시그널 받을때까지 일시중단
    - pause와 같은거 아닌가? 생각할수 있는데 특정 시그널 올때까지 중지

### 📌 특징

- **시그널이 발생하면 원래의 시그널 마스크로 복원됨**
  - `sigprocmask()`와 함께 사용하여 안전하게 시그널을 처리할 수 있음
- **영구적으로 블록하는 것이 아니라 일시적으로 블록을 변경함**
  - 특정 시그널을 기다렸다가 핸들러 실행 후 원래 상태로 복원됨.
- sigsuspend()는 블록시킬 **시그널을 재설정**함과 함께 동시에 캐치할 수 있는 시그널이 도착하거나 종료(SIGINT) 시그널이 도착할 때 까지 **프로세스를 잠시 블록시키는 액션**을 원자적으로 실행하는 시스템콜 함수.
- 다시 정리하면
  - sigprocmask(SIG_SETMASK,&set<NULL);와 pause();를 원자적으로 실행하여 sigprocmask와 pause사이에 발생할 수 있는 시그널을 잃어버리지 않게 한다!

```cpp
sigset_t old_set;
sigset_t sig_set;
sigemptyset(&sig_set);
sigaddset(&sig_srt,SIG_INT); // SIGINT(ctrl+c)를 sig_set에 추가
sigprocmask(SIG_BLOCK,&sig_set,&old_set); // 함수 호출 전의 시그널 마스크 저장하는 변수,
																					// 나중에 원래 시그널로 복원
sigsuspend(&old_set); // 이거 빼면 ctrl+c무시됨
exit(0);
```

## 디몬

- 디몬은 특정 서비스의 요청에 대해 응답하기 위해 오랫동안 실행중인 프로세스
- 메모리에 상주하고 있다.
- 작동방식은
  - 항상 실행대기
  - 클라이언트 요청이 있을때만 실행
- 가장 큰 특징이라고 하면 사용자가 직접 제어할수 없다 → 고로 쉘에 아무것도 찍히지 않는다.
- 자동으로 실행되는 백그라운드 프로세스이다.

### 📌 디몬과 시그널이 무슨 관계인데?

- 디몬도 프로세스이기 때문에 시그널을 받을수 있다
- 디몬이 특정 시그널을 받을때, 그에 대한 동작을 정의하면 시그널을 통해 제어 (종료, 재시작 등등)가 가능하다!

### Recap

- 디몬은 시그널을 통해 종료되거나 동작을 변경할 수 있다
  - 백그라운드에서 실행되다가 SIGTERM을 받으면 종료되는 식으로..
- 시그널을 활용하면 데몬을 더욱 안전하고 유연하게 제어가능하다
  - 고로 실제운영환경에서는 디몬을 만들때 시그널과 관련한 코드가 들어가야한다!

### 실제로 어떻게 사용이 될까?

- 데몬을 사용할때 유용한 프로세스들로는 대표적으로 웹서버와 로깅 시스템이 있는데
  이들을 데몬으로 가동하면, 오류가 발생했을 경우에도 자동으로 재시작이 가능하고 데몬용 계정으로 권한을 할당 할 수 있어서 해킹 등의 피해 발생시 데미지를 최소화 할 수 있다!

- 이런 점들 때문에 웹서버 등의 재시작이 자동으로 돼야하고, 백그라운드에서 계속해서 돌아야하는 프로세스의 경우 데몬을 활용하는 경우가 많다.

## ✅ Outro

- 다음시간에는 디몬을 만들기 위한 실행조건 7가지에 대해 알아볼 예정입니다.
- 저를 괴롭게 했던 리시프 설계과제 3번의 코드를 함께 살펴볼 예정입니다.
