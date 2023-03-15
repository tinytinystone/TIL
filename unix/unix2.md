# UNIX 명령어 (2)

## 지난 시간 복습

- 막간 퀴즈
    - 다음 파일의 실행 순서는 어떻게 될까요?
        - `~/.zshrc` , `etc/zshenv` , `~/.zprofile` , `~/.zshenv`
    - `ls -l test.js` 명령어의 결과인 `-rw-r--r--    1 jeesoohong  staff       2106 Nov 13 10:34 test.js` 의 의미를 해석해보세요.
        - 접근권한을 8진수 3자리로 나타내면 어떻게 될까요?
        - 그룹과 다른 사용자에게 쓰기 권한을 부여하는 명령어는 무엇일까요?

---

## 명령줄 편하게 써보기

### **터미널 명령줄 자동완성**

- 명령어 치고 tab

### **과거 명령어**

- ctrl+P (또는 위에 화살표): 바로 전에 입력한 명령어
- ctrl+N (또는 아래 화살표): 바로 다음에 입력한 명령어
- `!a` a로 시작하는 가장 최근에 실행한 명령어를 재실행
- `!!` 직전에 실행한 명령어 실행
- `!-n` n번 전의 명령어 실행

- ctrl+U: 썼던 명령어 다 지우기

### **터미널 명령줄 검색**

- ctrl+R: 사용했던 명령어 검색 (제일 최근에 검색한 것만 나옴)c
- `history` 과거에 실행한 명령어 이력 (숫자는 이력번호)
    - 명령줄에 입력한 이력은 다 남게 되어 있으니, 중요한 정보가 담겨있는 경우 항상 주의하세요!
- `!이력번호` 이력번호 명령어를 실행
    - 보통 이력파일은  `~/.bash_history`이며 해당 파일에 저장됨

### 단축어 지정

- `alias` 현재 단축어 모두 표시
- `alias ls='ls -FG'` 단축어 지정
- `\ls` alias를 해제하지 않아도 원래 명령어 사용 가능
- `unalias gitadd` 단축어 해제

- 미션 도전!
    - alias 설정 후 새 터미널을 켜보세요. 설정한 alias 명령어가 계속 남아있나요?
        - 남아있지 않다면, 어떻게 해야 계속 유지할 수 있을까요?

---

## **셸 변수와 환경 변수**

고유 변수를 사용하여 특정 동작을 규정하거나 실행에 필요한 정보를 설정할 수 있음

- 변수값 표시: `echo $HOME`
    
    ```bash
    # $로 시작하는 경우 현재 셸에서 사용되는 변수로 판단합니다
    echo $HOME
    echo ${HOME}  # 위 명령어와 사실 같음
    
    echo HOME
    ```
    
- `env` (혹은 `printenv` 혹은 `export`) : 환경변수 조회
    - `printenv HOME`
- HISTSIZE: 저장할 이력 개수
- HISTFILE: 이력 저장할 파일
- USER: 사용자명
- HOME: 사용자 홈 디렉터리
- SHELL: 사용 중인 기본 셸
- UID: 현재 사용자의 ID
- PWD: 현재 경로 (Print Working Directory)
- PATH: 명령어 검색 경로
    - 사용자가 수행하는 명령이 경로 중 상응하는 실행 프로그램을 찾을 수 있어야 함
    - 그렇지 않다면 경로를 사용해 명령을 표시해야 함(builtin 제외)
    - 경로와 경로는 `:` 으로 구분
    - 대부분의 사용자 명령은 `/bin`, `/usr/bin` , `/usr/local/bin` 에 존재함
    - 관리자 명령은 `/sbin`, `/usr/sbin` 에 존재

- `set` (혹은 `declare`) : 현재 셸에서 사용 중인 모든 셸 변수 조회

- 셸 변수
    - 사용자 정의 변수 (지역변수)
    - 변수 지정 셸에서만 유효
- 환경 변수
    - 명령어를 실행한 셸과 셸에서 실행한 자식 프로세스에도 유효
        - 자식 프로세스는 셸에서 실행된 프로그램 (혹은 명령어)
    - 서브 셸에서도 사용 가능

- 변수 지정 및 해제

```bash
FOO=bar
printenv FOO
unset FOO
```

- 환경변수 설정

```bash
FOO=bar
export FOO
# 혹은 export FOO=bar로 써도 됨 
```

```bash
# 문자열 외에 숫자, 배열 등도 저장되며 명령어도 저장이 가능함
GETLIST=ls
$GETLIST
```

- 미션 도전!
    - 셸 내부에서 서브셸을 사용하면, 지정된 변수가 유지되나요?
    - 새로운 터미널을 켜서 새 세션을 시작하는 경우에는 지정한 변수가 유지되나요?
        - 새 세션에서도 환경변수를 유지하고 싶다면, 어떻게 해야 할까요?

- node.js에서 환경변수에 접근할 수 있음

```bash
node
> process.env
> .exit
```

- `process.env`에서 셸 변수일 때와 환경 변수일 때의 차이
    
    ```bash
    NODE_ENV=production
    env
    node
    > process.env.NODE_ENV
    > .exit
    echo $NODE_ENV
    
    export NODE_ENV=production
    env
    node
    > process.env.NODE_ENV
    > .exit
    echo $NODE_ENV
    ```
    
- 1회성으로만 환경변수를 설정해주고, node 프로세스가 종료된 이후에는 사용하지 않고 싶을 때
    
    ```bash
    
    NODE_ENV=staging node
    > process.env.NODE_ENV
    > .exit
    echo $NODE_ENV
    ```
    

- package.json에서 script 실행할 때 환경변수를 포함해 실행할 수 있다 ([코드 예시](https://github.com/teamdable/ad-dashboard/blob/master/package.json#L15))
    - BUT! Mac과 Linux에서는 동작하지만 윈도우에서는 사용할 수 없음
        - 그래서 등장한 cross-env
            
            [cross-env](https://www.npmjs.com/package/cross-env)
            
            [GitHub - kentcdodds/cross-env: 🔀 Cross platform setting of environment scripts](https://github.com/kentcdodds/cross-env#other-solutions)
            

---

## 스크립트 실행하기

- `~/.zshrc` 수정 후 터미널을 새로 켜지 않으면 수정한 내용이 반영되지 않음
- 터미널을 새로 켜지 않고 반영하려면 파일을 실행해주면 됨

- `zsh 파일이름` : 명령어 해석기인 zsh 명령어에 스크립트 파일 이름을 인수로 실행
- `source 파일이름` 혹은 `. (경로포함)파일이름`
- `(경로포함)파일이름` : 단, 스크립트 파일의 접근권한 중 ‘실행권한’을 가지고 있어야 가능

```bash
# HOME 환경변수를 출력하는 스크립트 파일 만들기
$ echo "echo $HOME" > test.sh 

# 스크립트 파일 내용 확인
$ cat test.sh

# 스크립트 파일 실행
$ zsh test.sh
$ source test.sh
$ . ~/test.sh     # zsh에서는 경로까지 표시해줘야 실행이 가능함
$ ~/test.sh       # 왜 실행이 안될까?
```

- 

- 미션 도전!
    - test.sh가 실행이 되도록 바꿔보세요.
    - 아래는 nvm이 실행될 수 있도록 하는 스니펫입니다. 어떤 내용인지 대충 유추해보세요.
        
        ```bash
        # ~/zshrc에 저장된 nvm 관련 스니펫
        export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
        [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
        ```
        
        - HINT 1: `[-z string]` 은 조건을 나타내며, string 길이가 0이면 참임
        - HINT 2: `[-s file]` 은 file이 존재하고 파일 크기가 0이 아니면 참임

---