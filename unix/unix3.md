# UNIX 명령어 (3)

## 지난 시간 복습

- 막간 퀴즈
    1. 과거에 실행한 명령어 이력들과 그 이력번호를 조회하는 명령어는?
    2. 환경변수를 조회할 수 있는 명령어는?
    3. 환경변수를 설정하려면 어떻게 해야 할까요?
        1. `export NAME=jeesoo`
    4. 스크립트를 실행하는 방법에는 3가지가 있습니다. 3가지는 무엇일까요?
    5. 다음 명령어의 출력은? 사용자의 홈 디렉토리는 /home/linuxuser라 가정합니다.
        
        ```bash
        echo $HOME
        ```
        
        1. $HOME
        2. /home/linuxuser
    6. 참/거짓을 이야기해보세요.
        1. `!` 은 바로 전에 실행한 명령이다.
        2. 루트(`/`) 디렉터리는 하나만 존재한다.
        3. 현재 경로를 나타내는 환경변수는 PATH이다.
        4. 셸에서 사용되는 변수는 꼭 대문자가 아니어도 된다.
    - 아래는 nvm이 실행될 수 있도록 하는 스니펫입니다. 어떤 내용인지 대충 유추해보세요.
        - HINT 1: `[-z string]` 은 조건을 나타내며, string 길이가 0이면 참임
        - HINT 2: `[-s file]` 은 file이 존재하고 파일 크기가 0이 아니면 참임
        
        ```bash
        # ~/zshrc에 저장된 nvm 관련 스니펫
        export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
        [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
        ```
        

---

## 명령의 연결과 확장

### 표준 입력, 표준 출력, 표준 에러 출력

![IMG_2078B2B51A88-1.jpeg](./IMG_2078B2B51A88-1.jpeg)

- 주로 명령어는 입력을 키보드로 받고, 화면에 그 결과를 출력
- 어디서 입력을 받아서 어디로 출력을 보낼지 여부를 명령줄에서 전환하는 것이 가능
    - 명령어 입력 양이 많다면, 키보드가 아니라 파일에서 혹은 다른 입출력 명령에서 입력을 받을 수 있음
    - 마찬가지로 출력 결과를 저장하고 싶거나 하는 목적이 있다면, 터미널 화면이 아니라 파일로 혹은 다른 입출력 명령으로 출력을 보낼 수 있음
- 입출력을 파일로 전환하면 → 리다이렉트 (`<` , `>` …)
- 다른 명령어 입출력으로 전환하면 → 파이프 (`|`)

### 명령어 입출력을 파일로 전환

- `>` 또는 `<` : 입출력 리다이렉션에서, 즉 파일로부터 입력받을 때 혹은 파일로 출력할 때 사용
    - 명령어 양이 많을 때
    - 출력한 결과를 파일로 저장하고 싶을 때
- `>>` 또는 `<<`: 표준 출력을 파일의 끝에 덧붙일 때 사용

```bash
$ mkdir redirection
$ cd redirection

$ man ssh > ssh-manual
$ cat ssh-manual
$ cat < ssh-manual

$ which ssh >> ssh-manual

$ man sssh > sssh-manual      # No manual entry for sssh
$ man sssh 2> sssh-manual
$ man sssh > sssh-manual 2>&1
```

```yaml
$ cat > sh-manual
this is sh manual.
# Ctrl+C 빠져나오기

$ cat >> sh-manual
it will help.
# Ctrl+C 빠져나오기

$ cat > sh-manual
it is not a manual.
# Ctrl+C 빠져나오기
```

- 미션 도전!
    - `~/.zshrc` 의 내용을 수정하고 싶은데, 백업해두고 수정하고 싶습니다. 어떻게 하면 될까요?

### 명령어 조합하기

- `|` (파이프) : 앞 명령의 출력을 다음 명령의 표준 입력으로 연결

```bash
$ history | grep which

$ ls -l | grep ssh-manual
$ ls -l | grep ssh-manual | wc -l
```

- `grep` : 결과물에서 내용 검색하기
    - `grep [options] pattern [files]`
- `wc` : 텍스트 파일 길이 (줄, 단어, 문자)
    - `wc [options] [files]`
    - `wc -l` : 줄 수
    - `wc -w` : 단어 수
    - `wc -c` : 문자 수

- 미션 도전!
    
    ```bash
    # volta 설치 가이드입니다. 어떤 의미일까요?
    curl https://get.volta.sh | bash
    ```
    
    - `curl [options] <url>`  : url에 요청(post, get 등)을 보내는 명령어
        - `curl https://www.google.com/index.html`
    - HINT 1: 먼저 `curl https://get.volta.sh` 만 실행해보세요
    - [스크립트 실행하기 파트](https://www.notion.so/UNIX-2-7ecce26f1f0f47409175d36ba4b99f5b)를 참조하세요

```bash
$ lsof | more
$ lsof | head
$ lsof | tail
```

- `more` : 페이지 단위로 파일 보여주기
    - 스페이스 바: 다음 페이지
    - b: 이전 페이지
    - 화살표 위아래: 이전/다음 한 줄
    - q: 화면 빠져나가기
    - /검색키워드: 검색
- `head` : 위에서부터 n줄만 출력 (기본 10줄)
    - `head -n` : n줄만 출력
- `tail` : 아래에서부터 n줄만 출력 (기본 10줄)
    - `-f` 옵션: 파일 변화를 감시하며 실시간으로 변경 내용 출력(-f 뒤에 파일 이름 전달)
- `lsof` : list open files의 약자로, 시스템에서 열린 파일 목록을 출력
    - 목록 살펴보기
        - COMMAND: 실행한 명령어
        - PID: 프로세스 ID
        - USER: 실행 사용자
        - FD: File Descriptor
        - TYPE: 파일 종류 (direcotry, regular file, …)
        - DEVICE: 장치 번호
        - SIZE/OFF: 파일 크기
        - NODE: 노드 번호
        - NAME: 파일명
    - 옵션
        - `-i` : 특정 프로토콜(TCP, UDP…) 및 포트를 사용하고 있는 프로세스 정보 확인 가능. 인자가 없으면, 열려있는 모든 네트워크 포트 정보 확인 가능
            - `lsof -i :3000`
        - `-t` : 프로세스 id만 가져옴
- 프로세스는 여러 변화를 겪는, 실행 중인 프로그램
    - pid로 구별되며 자신만의 실행 환경을 확보해야 함
    - 제한된 자원을 가지고 멀티태스킹을 하는 운영체제 상에서는, 생성/준비/실행/보류/종료 등의 상태를 겪게 됨

```bash
$ lsof -t -i :3000
$ kill <pid>

$ lsof -t -i :3000 | xargs kill
```

- `kill <pid>`  : 프로세스에 다양한 시그널 보내기. 인수에 프로세스 id를 지정.
- `xargs` : 실행 결과를 특정 명령어의 인수로 사용하고 싶을 때 사용
    - `|` 는 앞의 결과를 ‘인자’가 아니라 ‘표준입력’으로 연결
    - 앞에서의 표준출력을 다음 프로그램의 ‘인자’로 넘기려면 `xargs` 를 사용해야 함
        
        ```bash
        mkdir xargstest
        cd xargstest
        
        cat > file1
        this is file no1.
        # Ctrl+C 빠져나오기
        
        cat > file2
        this is file no2.
        # Ctrl+C 빠져나오기
        
        # 차이점을 확인해보세요.
        ls | cat
        ls | xargs cat
        ```
        
        - 인수는 아래와 같이 넘길 수도 있음
            
            ```bash
            $ kill $(lsof -t -i :3000)
            ```
            
    

```bash
$ ps
$ ps a
$ ps u
$ ps x
$ ps aux

USER      PID  %CPU %MEM   VSZ    RSS   TT    STAT STARTED  TIME COMMAND
ubuntu    4772  0.0  0.0  14220   920 pts/1    S+   00:41   0:00 grep --color=auto nginx
root     30271  0.0  0.1 154376  1524 ?        Ss   Jan09   0:00 nginx: master process nginx
www-data 30272  0.9  3.1 182192 31488 ?        S    Jan09  14:17 nginx: worker process
```

- `ps` : 실행 중인 프로세스(프로그램) 상태 표시
    - 옵션
        - a: 모든 사용자(all)의 모든 프로세스
        - u: 사용자명 등 정보를 사용자 친화적으로(user-friendly) 자세히 출력
        - x: 현재 사용자가 소유한 모든 프로세스. 데몬 프로세스처럼 터미널과 연결되어 있지 않은 프로세스도 출력
        - f (또는 --forest): 프로세스 트리(forest) 출력
    - 주요 표시 항목
        - USER: 사용자명
        - PID: 프로세스 id
            - PPID: 부모 프로세스 id
        - %CPU, %MEM: CPU, Memory 이용률
        - VSZ: 가상메모리 사용량
        - RSS: 실제 메모리
        - TT, TTY: 제어 터미널 (없다면 ?로 표시됨)
        - STAT: 프로세스 상태 (R: 실행가능상태, S: 슬립상태, T: 정지상태, Z: 좀비상태, D: 인터럽트 불가능한 수면상태로 대개 디스크 입출력 상태, <: 높은 우선순위, N: 낮은 우선순위(nice))
        - COMMAND: 명령어
        - TIME: 누적 CPU 사용 시간

```bash
ubuntu@ip-172-31-3-93:~$ ps aux | grep nginx
USER      PID  %CPU %MEM   VSZ    RSS   TT    STAT STARTED  TIME COMMAND
ubuntu    4772  0.0  0.0  14220   920 pts/1    S+   00:41   0:00 grep --color=auto nginx
root     30271  0.0  0.1 154376  1524 ?        Ss   Jan09   0:00 nginx: master process nginx
www-data 30272  0.9  3.1 182192 31488 ?        S    Jan09  14:17 nginx: worker process
ubuntu@ip-172-31-3-93:~$ sudo kill -9 30271 30272
ubuntu@ip-172-31-3-93:~$ sudo nginx
```

- `kill` 에는 총 9개의 시그널이 있음 (`kill -l` 로 확인 가능)
    - `kill -9` : SIGKILL 강제 종료
    - default는 SIGTERM(15)로, 프로세스에 정리작업 후 스스로 종료할 것을 요청(정상 종료)
        - 프로세스 종료를 보장하지는 못함

---