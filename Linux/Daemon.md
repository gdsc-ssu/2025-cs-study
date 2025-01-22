# Daemon

### 디몬이 작동하는 과정

- 쉘이나 /etc/rc 스크립터에 의한 백그라운드 수행 명령 없이 백그라운드에서 수행
- 자식 프로세스를 생성하여 실제 작업은 자식 프로세스에 전달하고 부모는 수행 종료
- 부모는 /etc/init이 된다!

### 디몬 실행 조건 6가지

**1번 : 사용자가 직접 제어하지 않는다. 자동으로 실행되는 백그라운드 프로세스이다.**

```cpp
if((pid=fork())<0){
	exit(1);

}else if(pid!=0){
	exit(0);
}
```

- 부모 프로세스를 죽이고 pid 1번이 된다.

**2번,4번 : 제어터미널이 없다**

```cpp
fd = open("/dev/null",O_RDWR); // 제어터미널 없애는
dup(0);
dup(0);
signal(SIGTTIN,SIG_IGN);
signal(SIGTTON,SIG_IGN);
signal(SIGTSTP,SIG_IGN); // 제어터미널이 없으니 시그널도 없다
```

**3번 : 독자적인 세션을 가진다**

```cpp
setsid();
```

- 독자적인 세션이 아닐경우, 부모가 죽으면 자식도 죽는다.

**5번 : 파일마스크 금지**

```cpp
umask(0);
```

**6번 : 루트로 프로세스 실행위치 옮기기**

```cpp
chdir("/");
```

**7번 : 오픈 파일 디스크립터 닫기**

```cpp
for(fd=0;fd<maxfd;fd++){
	close();
}
```

### syslog

- 에러는 syslog에 들어간다!
- `/var/log` → 모든 로그 기록
- `/etc/syslog.conf` → 로그 파일의 위치지정
- `cron` → 특정 시간에 특정 작업하는 패턴

### 실제로는 어떻게 쓸까?

- 간략한 과제 명세
  - 디몬을 통해 백그라운드로 프로세스를 돌리고, 쉘에 인자로 주어진 경로와 그 하위 경로에 해당하는 파일에 수정이 발생시 ⇒ 파일을 백업하고, 디몬 로그를 남겨라

```cpp
volatile sig_atomic_t signalRecv = 0;

// 시그널 핸들러 함수는 하나의 매개변수를 가져야 하며,
// 그 매개변수는 받은 시그널의 번호 따라서, 시그널 핸들러는 자동적으로 시그널 번호를 매개변수로 받게 됨
void signalHandler(int signum)
{
    char signalerrorlog[PATH_MAX];
    sprintf(signalerrorlog, "Received signal %d (%s)\n", signum, strsignal(signum));
    log_error(signalerrorlog);
    if (signum == SIGUSR1)
    {
        log_error("Received SIGUSR1 signal\n");
        signalRecv = 1;
    }
}

int createDaemon(void)
{
    pid_t pid;
    int fd, maxfd;

    // 자식 프로세스 생성
    if ((pid = fork()) < 0)
    {
        fprintf(stderr, "ERROR: fork error\n");
        log_error("ERROR: fork error\n");
        exit(1);
    }
    // 부모 종료
    else if (pid != 0)
        exit(0);

    pid = getpid();
    setsid();

    signal(SIGTTIN, SIG_IGN);
    signal(SIGTTOU, SIG_IGN);
    signal(SIGTSTP, SIG_IGN);
    signal(SIGINT, SIG_IGN);
    signal(SIGUSR1, signalHandler);

    maxfd = getdtablesize();
    for (fd = 0; fd < maxfd; fd++)
        close(fd);

    umask(0);
    // chdir("/");

    fd = open("/dev/null", O_RDWR);
    dup(0);
    dup(0);

    return getpid();
}

// 중략

int initDaemon(char *path, int option)
{


   // 자식 프로세스 생성
    if ((pid = fork()) < 0)
    {
        fprintf(stderr, "ERROR: fork error\n");
        log_error("ERROR: fork error");
    }
    // 자식을 데몬으로
    else if (pid == 0)
    {

        if ((daemon_pid = createDaemon()) < 0)
        {
            fprintf(stderr, "ERROR: create daemon error\n");
            log_error("ERROR: create daemon error");
            exit(1);
        }
        char monitorbuf[PATH_MAX * 2];
        int len;

        // monitor_list에 로그 써주기
        if ((monitorList_fd = open(monitorLogPATH, O_WRONLY | O_APPEND | O_CREAT)) < 0)
        {
            fprintf(stderr, "ERROR: monitor log list open error\n");
            log_error("ERROR: monitor log list open error");
        }
        sprintf(monitorbuf, "%d : %s\n", daemon_pid, path);
        if (write(monitorList_fd, monitorbuf, strlen(monitorbuf)) == -1)
        {
            fprintf(stderr, "ERROR: monitor log list write error\n");
            log_error("ERROR: monitor log list  write error");
        }

        // $(pid).log파일 생성
        sprintf(daemonLogPath, "%s/%d.log", backupPATH, daemon_pid);

}

```
