# 개발 워크스테이션 구축 미션

## 1) 프로젝트 개요
터미널(리눅스 CLI), Docker, Git/GitHub를 직접 세팅하고 사용해 보며, "내 컴퓨터에서만 돌아가는 문제"를 줄이는 재현 가능한 개발 환경을 구축한다.

- 터미널로 디렉토리/파일/권한 관리
- Docker 설치 점검 및 컨테이너 운영
- Dockerfile 기반 웹 서버 컨테이너화, 포트 매핑, 바인드 마운트, 볼륨 영속성 검증
- Git 설정 및 GitHub/VSCode 연동

## 2) 실행 환경
| 항목 | 값 |
|---|---|
| OS | Windows + WSL2 (Ubuntu 26.04.1 LTS, kernel 6.18.54.1-microsoft-standard-WSL2) |
| Shell / Terminal | bash (WSL Ubuntu 터미널) |
| Docker | Docker Desktop 4.98.0 (Docker Engine/CLI 29.9.2) |
| Git | 2.53.0 |

```bash
$ cat /etc/os-release   # PRETTY_NAME="Ubuntu 26.04.1 LTS"
$ echo $SHELL
/bin/bash
$ docker --version
Docker version 29.9.2, build a82e528
$ git --version
git version 2.53.0
```

> 작업 경로: `/mnt/c/E1-1` (Windows C 드라이브를 WSL에서 마운트한 경로)

## 3) 수행 체크리스트
- [x] 터미널 기본 조작 및 폴더 구성
- [x] 권한 변경 실습 (파일 1, 디렉토리 1)
- [x] Docker 설치/점검
- [x] Docker 기본 운영 명령 (images / ps / logs / stats)
- [x] hello-world 실행
- [x] ubuntu 컨테이너 진입 및 attach/exec 차이 관찰
- [x] 커스텀 이미지 빌드/실행
- [x] 포트 매핑 접속 (2회 이상)
- [x] 바인드 마운트 반영
- [x] 볼륨 영속성
- [ ] Git 설정 + GitHub/VSCode 연동
- [ ] (보너스) Compose / 환경변수 / SSH 키

## 4) 수행 로그

### Step 1. 터미널 조작
작업 위치: WSL Ubuntu의 홈 디렉토리 아래 `~/codyssey/practice` (리눅스 파일시스템). 제출 저장소는 `/mnt/c/E1-1`.

```bash
# 현재 위치 확인 / 디렉토리 생성 / 이동
$ pwd
/mnt/c/E1-1
$ mkdir -p ~/codyssey/practice
$ cd ~/codyssey/practice
$ pwd
/home/khs9805225725/codyssey/practice

# 빈 파일 생성, 내용 쓰기, 내용 확인
$ touch epmty.txt
$ echo "hello.workstation" > hello.txt
$ cat hello.tst                     # 오타: 존재하지 않는 파일
cat: hello.tst: No such file or directory
$ cat hello.txt
hello.workstation

# 숨김 파일 포함 목록 확인
$ touch .hidden
$ ls
epmty.txt  hello.txt
$ ls -la
total 12
drwxr-xr-x 2 khs9805225725 khs9805225725 4096 Nov  9 21:20 .
drwxr-xr-x 3 khs9805225725 khs9805225725 4096 Nov  9 21:19 ..
-rw-r--r-- 1 khs9805225725 khs9805225725    0 Nov  9 21:20 .hidden
-rw-r--r-- 1 khs9805225725 khs9805225725    0 Nov  9 21:19 epmty.txt
-rw-r--r-- 1 khs9805225725 khs9805225725   18 Nov  9 21:20 hello.txt

# 복사, 이동/이름 변경
$ cp hello.txt hello_copy.txt
$ mv hello.copy.txt renamed.txt     # 오타: 파일명이 hello_copy.txt
mv: cannot stat 'hello.copy.txt': No such file or directory
$ mv hello_copy.txt renamed.txt     # 이름 변경 성공
$ mkdir backup
$ mn renamed.txt backup/            # 오타
mn: command not found
$ mv renamed.txt backup/            # 이동 성공
$ ls -la backup
total 12
drwxr-xr-x 2 khs9805225725 khs9805225725 4096 Nov  9 21:22 .
drwxr-xr-x 3 khs9805225725 khs9805225725 4096 Nov  9 21:22 ..
-rw-r--r-- 1 khs9805225725 khs9805225725   18 Nov  9 21:21 renamed.txt

# 디렉토리 삭제
$ rm -r backup
$ ls -la
total 12
drwxr-xr-x 2 khs9805225725 khs9805225725 4096 Nov  9 21:23 .
drwxr-xr-x 3 khs9805225725 khs9805225725 4096 Nov  9 21:19 ..
-rw-r--r-- 1 khs9805225725 khs9805225725    0 Nov  9 21:20 .hidden
-rw-r--r-- 1 khs9805225725 khs9805225725    0 Nov  9 21:19 epmty.txt
-rw-r--r-- 1 khs9805225725 khs9805225725   18 Nov  9 21:20 hello.txt

# 절대 경로 / 상대 경로 이동
$ cd /
$ cd ~/codyssey/practice            # 절대 경로(~ = /home/khs9805225725)
$ cd..                              # 오타: 공백 필요
cd..: command not found
$ cd ..                             # 상대 경로(상위로)
$ pwd
/home/khs9805225725/codyssey
$ cd practice                       # 상대 경로(하위로)
$ pwd
/home/khs9805225725/codyssey/practice
```

```bash
# 파일 삭제 (오타로 만든 epmty.txt 삭제)
$ rm epmty.txt
$ ls -la
total 12
drwxr-xr-x 2 khs9805225725 khs9805225725 4096 Nov  9 21:26 .
drwxr-xr-x 3 khs9805225725 khs9805225725 4096 Nov  9 21:19 ..
-rw-r--r-- 1 khs9805225725 khs9805225725    0 Nov  9 21:20 .hidden
-rw-r--r-- 1 khs9805225725 khs9805225725   18 Nov  9 21:20 hello.txt
```

**오타에서 관찰한 점:** 오타가 나면 `No such file or directory`(대상 없음) 또는 `command not found`(명령 없음)로 에러 원인이 구분된다. 터미널은 오타를 알아서 보정하지 않으므로 에러 메시지를 읽는 습관이 중요하다.

**절대 경로 vs 상대 경로**
- 절대 경로: 루트(`/`)에서 시작하는 전체 경로. 현재 위치와 상관없이 항상 같은 대상을 가리킨다. 예) `/home/khs9805225725/codyssey/practice`, `~/codyssey/practice`(`~`는 홈 디렉토리의 약어)
- 상대 경로: 현재 위치를 기준으로 하는 경로. `.`은 현재, `..`은 상위 디렉토리이며 현재 위치가 달라지면 같은 표기가 다른 대상을 가리킨다. 예) `/home/khs9805225725/codyssey`에서 `cd practice`는 `.../codyssey/practice`로 이동하지만, 다른 위치에서는 에러가 난다.

### Step 2. 권한 실습
**파일 실험 (`hello.txt`)** — 변경 전(644) → 600 → 755 → 644 복원
```bash
$ ls -l hello.txt                    # 변경 전
-rw-r--r-- 1 khs9805225725 khs9805225725 18 Nov  9 21:20 hello.txt
$ ./hello.txt                        # x 권한 없음
-bash: ./hello.txt: Permission denied

$ chmod 600 hello.txt
$ ls -l hello.txt
-rw------- 1 khs9805225725 khs9805225725 18 Nov  9 21:20 hello.txt

$ chmod 755 hello.txt
$ ls -l hello.txt
-rwxr-xr-x 1 khs9805225725 khs9805225725 18 Nov  9 21:20 hello.txt
$ ./hello.txt                        # x 권한 있음 → 실행은 시도됨
./hello.txt: line 1: hello.workstation: command not found

$ chmod 644 hello.txt                # 복원
$ ls -l hello.txt
-rw-r--r-- 1 khs9805225725 khs9805225725 18 Nov  9 21:20 hello.txt
```

**디렉토리 실험 (`testdir`)** — 755 → 000 → 755 복원
```bash
$ mkdir testdir
$ echo "inside" > testdir/a.txt
$ ls -ld testdir                     # 변경 전
drwxr-xr-x 2 khs9805225725 khs9805225725 4096 Nov  9 21:28 testdir

$ chmod 000 testdir
$ ls -ld testdir                     # 변경 후
d--------- 2 khs9805225725 khs9805225725 4096 Nov  9 21:28 testdir
$ ls testdir
ls: cannot open directory 'testdir': Permission denied
$ cd testdir
-bash: cd: testdir: Permission denied

$ chmod 755 testdir                  # 복원
$ ls -ld testdir
drwxr-xr-x 2 khs9805225725 khs9805225725 4096 Nov  9 21:28 testdir
$ ls testdir
a.txt
```

**관찰 결과**
- 파일: 644일 때 `./hello.txt`는 `Permission denied`. 755로 바꾸면 실행이 시도되어 `command not found`가 나왔다. 파일 내용(`hello.workstation`)이 명령어가 아니라서 난 에러이며, 실행 권한(x)이 적용되었다는 증거다.
- 디렉토리: 권한을 000으로 바꾸자 소유자인 나도 목록 조회(`ls`)와 진입(`cd`)이 모두 거부됐다. 권한은 "소유자니까 항상 가능"이 아니라 모드 비트로 결정된다. (root는 예외)

**정리: 파일 권한의 의미 (r/w/x)**

| 기호 | 숫자 | 파일에서의 의미 | 디렉토리에서의 의미 |
|---|---|---|---|
| r (read) | 4 | 내용 읽기 | 목록 조회 (`ls`) |
| w (write) | 2 | 내용 수정 | 안에서 파일 생성/삭제/이름 변경 |
| x (execute) | 1 | 프로그램으로 실행 | 진입(`cd`) 및 내부 항목 접근 |

**표기 해석 규칙**
- `ls -l`의 첫 글자는 종류(`-` 파일, `d` 디렉토리), 이어서 3글자씩 **소유자(user) / 그룹(group) / 그 외(others)** 순서로 총 9글자다.
- 숫자 표기는 각 자리 = r(4)+w(2)+x(1)의 합이며, 자리는 소유자/그룹/그 외 순서다.
  - `755` = 7(rwx) 5(r-x) 5(r-x) → `rwxr-xr-x`: 소유자는 읽기/쓰기/실행, 나머지는 읽기/실행. 실행 파일과 디렉토리에 흔히 쓴다.
  - `644` = 6(rw-) 4(r--) 4(r--) → `rw-r--r--`: 소유자만 쓰기 가능, 나머지는 읽기만. 일반 파일의 기본값이다.
  - `600` = `rw-------`: 소유자만 읽기/쓰기 (비밀 파일용)
  - `000` = `---------`: 아무도 접근 불가 (이번 실험)

> 참고: 권한 실험은 리눅스 파일시스템인 `~/codyssey/practice`에서 수행했다. `/mnt/c/...`(Windows 드라이브) 아래에서는 chmod가 기대대로 반영되지 않을 수 있다.

### Step 3. Docker 설치 및 기본 점검
Docker Desktop(Windows) + WSL integration으로 WSL Ubuntu에서 docker CLI를 사용한다.
설치/연동 과정에서 문제가 있었으며 [트러블슈팅 사례 1](#사례-1-docker-데몬-연결-실패-3단계)에 기록했다.

```bash
$ docker --version
Docker version 29.9.2, build a82e528

$ groups
khs9805225725 adm cdrom sudo dip plugdev users docker

$ docker info   # (Client 플러그인 목록 생략, Server 섹션 발췌)
Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 29.9.2
 Storage Driver: overlayfs
 Cgroup Driver: cgroupfs
 Cgroup Version: 2
 Runtimes: io.containerd.runc.v2 nvidia runc
 Default Runtime: runc
 Kernel Version: 6.18.54.1-microsoft-standard-WSL2
 Operating System: Docker Desktop
 OSType: linux
 Architecture: x86_64
 CPUs: 24
 Total Memory: 15.25GiB
```

- 판정: Server 섹션이 정상 출력되므로 Docker 데몬이 동작 중이다.
- 📸 증거: `docs/images/step3-docker-info.png` (docs/images/step3-docker-info.png)

### Step 4. Docker 기본 운영 명령
```bash
# 이미지 다운로드 / 목록
$ docker pull nginx:alpine
alpine: Pulling from library/nginx
e011f124249b: Pull complete
...(레이어 10개 다운로드, 중간 생략)
Digest: sha256:713fc2ebfd92e8217db451477182ee91944b123d2b1ee9fd5a8c6e91ca6c3a11
Status: Downloaded newer image for nginx:alpine
docker.io/library/nginx:alpine

$ docker images
IMAGE          ID             DISK USAGE   CONTENT SIZE   EXTRA
nginx:alpine   713fc2ebfd92       94.5MB         27.3MB

# 컨테이너 실행(백그라운드) / 목록
$ docker run -d --name web-test nginx:alpine
25e2547453d38b79ab38d1ecaa787b7bc8087f59a4a81641184503de6eb876b6

$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS     NAMES
25e2547453d3   nginx:alpine   "/docker-entrypoint.…"   3 seconds ago   Up 3 seconds   80/tcp    web-test

$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS     NAMES
25e2547453d3   nginx:alpine   "/docker-entrypoint.…"   8 seconds ago   Up 7 seconds   80/tcp    web-test

# 로그 확인 (worker process 시작 로그 반복 부분은 생략)
$ docker logs web-test
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
...
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/11/09 12:30:42 [notice] 1#1: using the "epoll" event method
2026/11/09 12:30:42 [notice] 1#1: nginx/1.31.8
2026/11/09 12:30:42 [notice] 1#1: OS: Linux 6.18.54.1-microsoft-standard-WSL2
2026/11/09 12:30:42 [notice] 1#1: start worker processes
2026/11/09 12:30:42 [notice] 1#1: start worker process 30
...
2026/11/09 12:30:42 [notice] 1#1: start worker process 53

# 리소스 확인
$ docker stats --no-stream
CONTAINER ID   NAME       CPU %     MEM USAGE / LIMIT     MEM %     NET I/O        BLOCK I/O     PIDS
25e2547453d3   web-test   0.00%     20.88MiB / 15.25GiB   0.13%     1.7kB / 126B   0B / 8.19kB   25

# 중지 후 ps / ps -a 비교
$ docker stop web-test
web-test
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS                     PORTS     NAMES
25e2547453d3   nginx:alpine   "/docker-entrypoint.…"   30 seconds ago   Exited (0) 7 seconds ago             web-test

# 컨테이너 삭제
$ docker rm web-test
web-test
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

**관찰 결과**
- `docker ps`는 실행 중인 컨테이너만, `docker ps -a`는 중지된 것까지 보여준다. `docker stop` 후 `ps`에서는 사라지고 `ps -a`에는 `Exited (0)`로 남았으며, `docker rm`을 해야 완전히 삭제된다. 컨테이너를 중지해도 삭제되지 않는다는 점을 확인했다.
- `docker run -d`는 컨테이너를 백그라운드로 실행하고 컨테이너 ID를 출력한다. 이때 `PORTS`가 `80/tcp`로만 보이는 것은 컨테이너 내부에서 80 포트를 쓸 뿐 호스트로 연결(포트 매핑)하지 않았다는 뜻이다. 포트 매핑은 Step 7에서 다룬다.
- `docker logs`로 컨테이너 안의 nginx 시작 과정을 확인할 수 있다. 로그 끝에 시작된 worker process 개수만큼 시작 로그가 반복된다.
- `docker stats --no-stream`으로 한 시점의 CPU/메모리 사용량(메모리 20.88MiB, PIDS 25)을 확인했다.
- 이미지(`nginx:alpine`)는 컨테이너를 삭제해도 남아 있다. 이미지와 컨테이너가 분리되어 있다는 뜻이다.

### Step 5. 컨테이너 실행 실습

**1) hello-world**
```bash
$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
4f55086f7dd0: Pull complete
d5e71e642bf5: Download complete
Digest: sha256:c567b231f65614688711a75f31518a064258a51d442117a3e771a3a8fdba78ef
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
...(이하 안내 메시지 생략)
```
→ 로컬에 이미지가 없어 Docker Hub에서 자동으로 pull한 뒤 실행되었다. 설치와 데몬 연동이 정상이다.

**2) ubuntu 컨테이너 진입 후 간단 명령**
```bash
$ docker run -it --name ubuntu-test ubuntu bash
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
...
Status: Downloaded newer image for ubuntu:latest
root@cd5aac970b34:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@cd5aac970b34:/# echo "hello from ubuntu"
hello from ubuntu
root@cd5aac970b34:/# cat /etc/os-release
PRETTY_NAME="Ubuntu 26.04.1 LTS"
NAME="Ubuntu"
VERSION_ID="26.04"
...
root@cd5aac970b34:/# exit
exit

$ docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS   NAMES
cd5aac970b34   ubuntu        "bash"     51 seconds ago   Exited (0) 6 seconds ago            ubuntu-test
65940609b8f4   hello-world   "/hello"   About a minute ago   Exited (0) About a minute ago   cranky_cartwright
```
→ 컨테이너 안에서 `exit`하자 컨테이너가 `Exited (0)`으로 종료되었다. 컨테이너의 메인 프로세스가 `bash`였기 때문이다. `--name`을 주지 않은 hello-world 컨테이너에는 임의 이름(`cranky_cartwright`)이 붙었다.

**3) exec vs attach 차이 관찰**
```bash
# (a) exec: 실행 중인 컨테이너에 새 셸을 열기
$ docker run -d --name ubuntu-keep ubuntu sleep infinity
78f41067256bddc9d42dbc44c83a67f5cf5b45e457c0bce65f52526fe23930cf
$ docker exec -it ubuntu-keep bash
root@78f41067256b:/# docker exec -it ubuntu-keep bash     # 컨테이너 안에서 docker 명령을 다시 입력함
bash: docker: command not found
root@78f41067256b:/# echo "exec shell"
exec shell
root@78f41067256b:/# exit
exit
$ docker ps
CONTAINER ID   IMAGE     COMMAND            CREATED          STATUS          PORTS   NAMES
78f41067256b   ubuntu    "sleep infinity"   38 seconds ago   Up 37 seconds           ubuntu-keep

# (b) attach: 컨테이너의 메인 프로세스(bash)에 직접 붙기
$ docker run -dit --name ubuntu-attach ubuntu bash
c53167c673d041359f72a5820f83d170264cf2fd4ae7700ff5439c3f7e9e2dc1
$ docker attach ubuntu-attach
root@c53167c673d0:/# echo "attach shell"
attach shell
root@c53167c673d0:/# exit
exit
$ docker ps -a
CONTAINER ID   IMAGE         COMMAND            CREATED          STATUS                          PORTS   NAMES
c53167c673d0   ubuntu        "bash"             17 seconds ago   Exited (0) 4 seconds ago                ubuntu-attach
78f41067256b   ubuntu        "sleep infinity"   About a minute ago   Up About a minute                   ubuntu-keep
cd5aac970b34   ubuntu        "bash"             2 minutes ago    Exited (0) About a minute ago           ubuntu-test
65940609b8f4   hello-world   "/hello"           2 minutes ago    Exited (0) 2 minutes ago                cranky_cartwright

# 정리
$ docker rm -f ubuntu-keep ubuntu-test ubuntu-attach
ubuntu-keep
ubuntu-test
ubuntu-attach
```

**정리: 종료/유지, exec vs attach 차이**

| 구분 | `docker exec -it <컨테이너> bash` | `docker attach <컨테이너>` |
|---|---|---|
| 동작 | 실행 중인 컨테이너 안에 **새 프로세스(셸)** 를 추가로 실행 | 컨테이너의 **메인 프로세스(PID 1)** 의 입출력에 직접 연결 |
| `exit` 후 | 새 셸만 끝나고 **컨테이너는 계속 실행** (`ubuntu-keep`은 `Up` 유지) | 메인 프로세스가 끝나 **컨테이너도 종료** (`ubuntu-attach`는 `Exited (0)`) |
| 용도 | 운영 중인 컨테이너 점검/디버깅 | 메인 프로세스 출력 보기/직접 조작 |

- 컨테이너의 수명은 메인 프로세스(PID 1)에 달려 있다. `sleep infinity`는 끝나지 않으니 컨테이너가 계속 살아 있고, `bash`는 `exit`하면 끝나니 컨테이너도 같이 종료된다.
- 컨테이너 안에서 `docker` 명령을 입력했더니 `docker: command not found`가 나왔다. 컨테이너는 호스트와 격리된 별도 환경이라 호스트에 설치한 Docker CLI가 들어 있지 않다. 명령을 입력한 위치(호스트 vs 컨테이너)를 프롬프트로 구분해야 한다.

### Step 6. 커스텀 이미지 (Dockerfile)
- 선택한 베이스: **방식 (A)** 웹 서버 베이스 이미지 `nginx:alpine`(공식 이미지)에 정적 콘텐츠를 교체하고 메타데이터/환경변수/헬스체크를 추가 
- 커스텀 포인트와 목적: 아래 표 참고

| Dockerfile 줄 | 목적 |
|---|---|
| `FROM nginx:alpine` | 가볍고 검증된 공식 nginx 이미지를 베이스로 사용 |
| `LABEL org.opencontainers.image.title=...` | 이미지 이름 메타데이터 표기 |
| `ENV APP_ENV=dev` | 실행 환경(dev) 구분 변수 주입 |
| `COPY site/ /usr/share/nginx/html/` | nginx 기본 페이지를 내가 만든 정적 콘텐츠로 교체 |
| `EXPOSE 80` | 컨테이너가 80 포트를 사용함을 문서화 (실제 연결은 `-p`로 함) |
| `HEALTHCHECK ...` | 10초마다 `wget`으로 응답 여부를 점검해 컨테이너 상태(healthy)를 표시 |

저장소 구성:
```
E1-1/
├── Dockerfile
├── README.md
└── site/
    └── index.html
``` 

**파일 작성**
```bash
$ cd /mnt/c/E1-1
$ mkdir -p site
$ cat > site/index.html <<'EOF'
<!DOCTYPE html>
<html lang="ko">
<head><meta charset="UTF-8"><title>My Custom Nginx</title></head>
<body>
  <h1>Hello from my custom Docker image!</h1>
  <p>개발 워크스테이션 구축 미션 - Dockerfile 커스텀 이미지</p>
</body>
</html>
EOF
$ cat > Dockerfile <<'EOF'
FROM nginx:alpine
LABEL org.opencontainers.image.title="my-custom-nginx"
ENV APP_ENV=dev
COPY site/ /usr/share/nginx/html/
EXPOSE 80
HEALTHCHECK --interval=10s --timeout=3s --retries=3 \
  CMD wget -qO- http://localhost/ >/dev/null || exit 1
EOF
$ ls -la
total 4
drwxrwxrwx 1 khs9805225725 khs9805225725 4096 Nov  9 21:39 .
drwxrwxrwx 1 khs9805225725 khs9805225725 4096 Nov  9 19:37 ..
-rwxrwxrwx 1 khs9805225725 khs9805225725  242 Nov  9 21:39 Dockerfile
-rwxrwxrwx 1 khs9805225725 khs9805225725 3236 Nov  9 21:02 README.md
drwxrwxrwx 1 khs9805225725 khs9805225725 4096 Nov  9 21:39 site
```
> 참고: `/mnt/c`(Windows 드라이브) 아래 파일은 전부 `rwxrwxrwx`로 보인다. 리눅스 권한이 제대로 반영되지 않는 영역이라 Step 2의 권한 실습은 리눅스 파일시스템(`~/codyssey/practice`)에서 수행했다.

**빌드**
```bash
$ docker build -t my-web:1.0
ERROR: docker: 'docker buildx build' requires 1 argument
```
빌드 컨텍스트(마지막 인자)를 빼먹어서 난 에러다. `.`은 현재 폴더를 빌드 컨텍스트로 쓴다는 뜻이다.
```bash
$ docker build -t my-web:1.0 .
[+] Building 0.9s (7/7) FINISHED                                  docker:default
 => [internal] load build definition from Dockerfile                        0.0s
 => [internal] load metadata for docker.io/library/nginx:alpine             0.0s
 => [internal] load build context                                           0.1s
 => [1/2] FROM docker.io/library/nginx:alpine@sha256:713fc2ebfd92e8217d...  0.5s
 => [2/2] COPY site/ /usr/share/nginx/html/                                 0.0s
 => exporting to image                                                      0.2s
 => => naming to docker.io/library/my-web:1.0                               0.0s

$ docker images
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
hello-world:latest   c567b231f656       25.9kB         9.49kB
my-web:1.0           92d1d923ec70       93.7MB         26.4MB
nginx:alpine         713fc2ebfd92       94.5MB         27.3MB
ubuntu:latest        6a06111c3b0a        163MB         45.9MB
```

**실행 및 검증**
```bash
$ docker run -d -p 8080:80 --name my-web-8080 my-web:1.0
b6bf841e8e7316039f0963358d73d74f9f78fb53966709f4228102583431e161

$ docker ps
CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS                            PORTS                                     NAMES
b6bf841e8e73   my-web:1.0   "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds (health: starting)   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-web-8080

$ docker exec my-web-8080 printenv APP_ENV
dev

$ sleep 15
$ docker ps
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS                    PORTS                                     NAMES
b6bf841e8e73   my-web:1.0   "/docker-entrypoint.…"   34 seconds ago   Up 33 seconds (healthy)   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-web-8080

$ docker inspect --format='{{.State.Health.Status}}' my-web-8080
healthy
```

**관찰 결과**
- 빌드 로그에서 `FROM`(베이스 레이어 가져오기) → `COPY`(내 파일 추가) 순서로 이미지가 만들어진 것을 확인했다. 베이스 이미지 레이어는 Step 4에서 받은 `nginx:alpine` 것을 재사용했다.
- 커스텀 이미지 `my-web:1.0`(93.7MB)은 베이스 `nginx:alpine`(94.5MB)과 크기가 거의 같다. 내 파일은 몇 바이트 수준이라 새로 쌓이는 레이어가 작기 때문이다.
- `docker ps`에서 헬스체크 상태가 `health: starting` → `healthy`로 바뀌어 HEALTHCHECK가 동작함을 확인했다.
- `docker exec ... printenv APP_ENV` 결과가 `dev`라 `ENV`가 이미지에 반영되었다.
- `PORTS`가 `0.0.0.0:8080->80/tcp`로 보여 호스트 8080 포트가 컨테이너 80 포트로 연결되었다. (접속 확인은 Step 7)

### Step 7. 포트 매핑 접속 증거
같은 이미지(`my-web:1.0`)로 컨테이너 2개를 서로 다른 호스트 포트에 매핑해 실행했다.

```bash
# 호스트 8080 → 컨테이너 80 (Step 6에서 실행)
$ curl http://localhost:8080
<!DOCTYPE html>
<html lang="ko">
<head><meta charset="UTF-8"><title>My Custom Nginx</title></head>
<body>
  <h1>Hello from my custom Docker image!</h1>
  <p>개발 워크스테이션 구축 미션 - Dockerfile 커스텀 이미지</p>
</body>
</html>

# 호스트 8081 → 컨테이너 80
$ docker run -d -p 8081:80 --name my-web-8081 my-web:1.0
6da7df6b63f3438a6a2f18e9d96e9cdd81984b5e4bfb3f73f351cc4db981c1a9

$ docker ps
CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS                            PORTS                                     NAMES
6da7df6b63f3   my-web:1.0   "/docker-entrypoint.…"   4 seconds ago   Up 3 seconds (health: starting)   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   my-web-8081
b6bf841e8e73   my-web:1.0   "/docker-entrypoint.…"   2 minutes ago   Up 2 minutes (healthy)            0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-web-8080

$ curl http://localhost:8081
<!DOCTYPE html>
<html lang="ko">
<head><meta charset="UTF-8"><title>My Custom Nginx</title></head>
<body>
  <h1>Hello from my custom Docker image!</h1>
  <p>개발 워크스테이션 구축 미션 - Dockerfile 커스텀 이미지</p>
</body>
</html>
```

**브라우저 접속 증거 (주소창 포함)**

| 8080 | 8081 |
|---|---|
| ![port-8080](docs/images/port-8080.png) | ![port-8081](docs/images/port-8081.png) |

**관찰 결과**
- 두 컨테이너는 같은 이미지에서 만들어졌고 둘 다 컨테이너 내부 80 포트를 쓰지만, 호스트 포트가 다르기(8080, 8081) 때문에 충돌 없이 동시에 실행된다. 이미지 하나에서 컨테이너를 여러 개 만들 수 있다는 것(이미지와 컨테이너의 분리)을 확인했다.
- `PORTS`의 `0.0.0.0:8080->80/tcp`는 "호스트의 모든 인터페이스 8080 포트로 들어온 요청을 컨테이너 80 포트로 전달"한다는 뜻이다.

**정리: 포트 매핑이 필요한 이유**
- 컨테이너는 자체 네트워크 공간을 가진 격리된 환경이라, 컨테이너 안의 80 포트는 기본적으로 호스트(내 컴퓨터)나 외부에서 접근할 수 없다. (Step 4의 `PORTS 80/tcp`는 컨테이너 안에서만 열려 있다는 뜻)
- `-p <호스트포트>:<컨테이너포트>`로 호스트의 포트와 컨테이너의 포트를 연결해야 브라우저나 `curl`로 접속할 수 있다.
- 컨테이너 내부 포트는 같아도 호스트 포트만 다르게 매핑하면 같은 서비스를 여러 개 동시에 띄울 수 있다. 반대로 같은 호스트 포트를 두 번 쓰면 충돌한다.

### Step 8. 바인드 마운트
호스트의 `site/` 폴더를 컨테이너의 `/usr/share/nginx/html`에 직접 연결해, 이미지를 다시 빌드하지 않고 호스트 파일 수정이 반영되는지 확인했다.

```bash
$ cd /mnt/c/E1-1
$ docker run -d -p 8082:80 --name my-web-bind -v "$(pwd)/site":/usr/share/nginx/html my-web:1.0
8e6c598e06b2bdf50fad09c30096d652d84d6bd2f37983896a243598215c81aa

# ── 변경 전 ──
$ cat site/index.html
<!DOCTYPE html>
<html lang="ko">
<head><meta charset="UTF-8"><title>My Custom Nginx</title></head>
<body>
  <h1>Hello from my custom Docker image!</h1>
  <p>개발 워크스테이션 구축 미션 - Dockerfile 커스텀 이미지</p>
</body>
</html>
$ curl http://localhost:8082
...(위와 동일) <h1>Hello from my custom Docker image!</h1>

# ── 호스트에서 파일 수정 (컨테이너 재시작/재빌드 없음) ──
$ sed -i 's/Hello from my custom Docker image!/Hello from bind mount!/' site/index.html

# ── 변경 후 ──
$ cat site/index.html
<!DOCTYPE html>
<html lang="ko">
<head><meta charset="UTF-8"><title>My Custom Nginx</title></head>
<body>
  <h1>Hello from bind mount!</h1>
  <p>개발 워크스테이션 구축 미션 - Dockerfile 커스텀 이미지</p>
</body>
</html>
$ curl http://localhost:8082
...(위와 동일) <h1>Hello from bind mount!</h1>

# ── 비교: COPY로 만든 기존 컨테이너(8080) ──
$ curl http://localhost:8080
...<h1>Hello from my custom Docker image!</h1>      # 변경 없음

# ── 컨테이너 안에서 본 파일 ──
$ docker exec my-web-bind cat /usr/share/nginx/html/index.html
...<h1>Hello from bind mount!</h1>                  # 호스트 변경이 그대로 보임
```

**호스트 변경 전/후 비교**

| 대상 | 변경 전 | 변경 후 |
|---|---|---|
| 호스트 `site/index.html` | `Hello from my custom Docker image!` | `Hello from bind mount!` |
| 8082 (바인드 마운트) | `Hello from my custom Docker image!` | `Hello from bind mount!` ← 즉시 반영 |
| 8080 (COPY로 이미지에 포함) | `Hello from my custom Docker image!` | `Hello from my custom Docker image!` ← 변경 없음 |

**정리**
- 바인드 마운트는 호스트의 폴더를 컨테이너 경로에 직접 연결한다. 컨테이너 안과 호스트가 **같은 파일**을 보기 때문에 재빌드나 재시작 없이 수정이 바로 반영된다. 개발 중 코드 수정 확인에 유용하다.
- `COPY`는 빌드 시점의 파일을 이미지 안에 복사해 두는 방식이라, 이후 호스트 파일을 바꿔도 이미 만들어진 이미지와 컨테이너는 바뀌지 않는다. 반영하려면 다시 빌드해야 한다.
- 바인드 마운트한 경로의 컨테이너 내부 원래 파일(이미지에 `COPY`된 `index.html`)은 마운트 위치에서는 호스트 폴더 내용으로 가려진다.
- 참고: 이 실험으로 호스트의 `site/index.html`이 `Hello from bind mount!`로 바뀐 상태다.

### Step 9. Docker 볼륨 영속성
```bash
# ── 1) 볼륨 생성/확인 ──
$ docker volume create mydata
mydata
$ docker volume ls
DRIVER    VOLUME NAME
local     mydata

# ── 2) 볼륨 연결 후 데이터 쓰기 ──
$ docker run -d --name vol-test -v mydata:/data ubuntu sleep infinity
4106424c1c5528ec7587dcec8b72314b2d5ef9ac6f2e402ede786ddcabe20987
$ docker exec vol-test bash -c "echo 'persist me' > /data/hello.txt && cat /data/hello.txt"
persist me

# ── 3) 컨테이너 삭제 (삭제 전 데이터: persist me) ──
$ docker rm -f vol-test
vol-test
$ docker ps -a                       # vol-test는 사라짐 (아래는 이전 단계 컨테이너)
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS                        PORTS                                     NAMES
8e6c598e06b2   my-web:1.0   "/docker-entrypoint.…"   2 minutes ago    Up About a minute (healthy)   0.0.0.0:8082->80/tcp, [::]:8082->80/tcp   my-web-bind
6da7df6b63f3   my-web:1.0   "/docker-entrypoint.…"   10 minutes ago   Up 10 minutes (healthy)       0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   my-web-8081
b6bf841e8e73   my-web:1.0   "/docker-entrypoint.…"   13 minutes ago   Up 13 minutes (healthy)       0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-web-8080
$ docker volume ls                   # 볼륨은 그대로 남아 있음
DRIVER    VOLUME NAME
local     mydata

# ── 4) 새 컨테이너에 같은 볼륨 연결 (삭제 후 데이터 확인) ──
$ docker run -d --name vol-test2 -v mydata:/data ubuntu sleep infinity
a00cd7e3c3c3c1e741ff361bcddd8d244baeee3fb3d18a265840f5881b243b36
$ docker exec vol-test2 cat /data/hello.txt
persist me
$ docker volume inspect mydata
[
    {
        "CreatedAt": "2026-11-09T12:53:56Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/mydata/_data",
        "Name": "mydata",
        "Options": null,
        "Scope": "local"
    }
]

# ── 5) 비교: 볼륨 없이 쓴 데이터 ──
$ docker run -d --name novol-test ubuntu sleep infinity
a04802a82ef00203ad43a4264b08d14e073fc4f2d81fea9b0d5cf1fb355751b8
$ docker exec novol-test bash -c "echo temp > /tmp/temp.txt && cat /tmp/temp.txt"
temp
$ docker rm -f novol-test
novol-test
$ docker run -d --name novol-test2 ubuntu sleep infinity
7645c07f57c839289299186c1a687a24aa85b93e7fc11a461739d580516c7084
$ docker exec novol-test2 cat /tmp/temp.txt
cat: /tmp/temp.txt: No such file or directory

# ── 6) 정리 ──
$ docker rm -f vol-test2 novol-test2
vol-test2
novol-test2
```

**컨테이너 삭제 전/후 비교**

| 구분 | 삭제 전 | 컨테이너 삭제 | 삭제 후 (새 컨테이너) |
|---|---|---|---|
| 볼륨 사용 (`mydata:/data`) | `persist me` | `docker rm -f vol-test` | `persist me` (유지됨) |
| 볼륨 미사용 (`/tmp/temp.txt`) | `temp` | `docker rm -f novol-test` | `No such file or directory` (사라짐) |

**정리: Docker 볼륨이란?**
- 컨테이너의 쓰기 가능한 계층은 컨테이너 삭제와 함께 사라지는 임시 저장소다. 컨테이너를 지우면 안에 쓴 데이터도 함께 사라진다.
- Docker 볼륨(`docker volume create`)은 Docker가 관리하는 **컨테이너 밖의 저장소**이고 컨테이너와 수명이 분리되어 있다. 컨테이너를 삭제해도 볼륨(`mydata`)은 남고, 새 컨테이너에 같은 볼륨을 연결(`-v mydata:/data`)하면 데이터를 그대로 이어서 쓸 수 있다. DB 데이터처럼 유지해야 하는 데이터에 쓴다.
- 바인드 마운트와의 차이: 바인드 마운트는 **호스트의 특정 폴더 경로**를 직접 연결하고(Step 8), 볼륨은 **Docker가 관리하는 저장 공간**(`/var/lib/docker/volumes/...`)을 이름으로 연결한다. 볼륨은 호스트 경로와 무관해 이식성이 좋고, 바인드 마운트는 호스트 파일을 직접 편집할 수 있어 개발 중 코드 반영에 편하다.
- 볼륨은 `docker rm`으로 컨테이너를 지워도 삭제되지 않으며, `docker volume rm mydata`로 따로 삭제해야 한다.

### Step 10. Git 설정 및 GitHub 연동
```bash
git config --global user.name "..."
git config --global init.defaultBranch main
git config --list
```
- VSCode GitHub 연동 스크린샷: `docs/images/github-vscode.png`
- Git vs GitHub 역할 차이: 

## 5) 검증 방법 요약
| 검증 항목 | 사용 명령 | 결과 위치 |
|---|---|---|
| | | |

## 6) 트러블슈팅 (2건 이상)
### 사례 1. Docker 데몬 연결 실패 (3단계)
`docker info` 실행 시 Server 섹션이 나오지 않는 문제가 세 번 연달아 다른 에러로 나타났다.

**1단계: 엔진 연결 실패**
- 문제: `failed to connect to the docker API at npipe:////./pipe/dockerDesktopLinuxEngine ... The system cannot find the file specified.`
- 원인 가설: Windows에서 Docker Desktop이 실행되지 않아 엔진이 꺼져 있다. (CLI 경로가 `C:\Program Files\Docker\...`로 Windows 설치본을 가리킴)
- 확인: Docker Desktop 상태 확인
- 해결: Docker Desktop을 실행하고 "Engine running" 상태까지 대기

**2단계: WSL 배포판에서 docker 인식 실패**
- 문제: `The command 'docker' could not be found in this WSL 2 distro. We recommend to activate the WSL integration in Docker Desktop settings.`
- 원인 가설: Docker Desktop의 WSL integration이 Ubuntu 배포판에 대해 꺼져 있다.
- 확인: Docker Desktop → Settings → Resources → WSL integration
- 해결: Ubuntu 토글 ON → Apply & restart. 이후 CLI 경로가 `/usr/local/lib/docker/...`로 바뀌고 Context가 `default`가 됨
- 📸 증거: `docs/images/ts1-wsl-integration.png` (docs/images/ts1-wsl-integration.png)

**3단계: 소켓 권한 오류**
- 문제: `permission denied while trying to connect to the docker API at unix:///var/run/docker.sock`
- 원인 가설: 현재 사용자가 `docker` 그룹에 속해 있지 않아 소켓에 접근할 수 없다.
- 확인:
```bash
$ ls -l /var/run/docker.sock
srw-rw---- 1 root docker 0 Nov  9 21:08 /var/run/docker.sock
$ groups
khs9805225725 adm cdrom sudo dip plugdev users
```
소켓은 root:docker만 읽기/쓰기(rw) 가능한데, 내 계정 그룹에 `docker`가 없음 → 가설 확인
- 해결: 사용자를 docker 그룹에 추가하고 새 세션에서 적용
```bash
$ sudo usermod -aG docker $USER
# 그룹 변경은 새 로그인 세션부터 적용되므로 WSL을 재시작(wsl --shutdown, Windows PowerShell에서 실행)
$ groups
khs9805225725 adm cdrom sudo dip plugdev users docker
$ docker info   # Server 섹션 정상 출력
```
- 배운 점: 그룹 변경은 이미 열린 세션에는 반영되지 않는다. `wsl --shutdown`은 Windows 명령이라 Ubuntu 터미널이 아니라 PowerShell에서 실행해야 한다.

### 사례 2
- 문제:
- 원인 가설:
- 확인:
- 해결/대안:

## 7) 보안 주의
토큰, 비밀번호, 개인키, 인증 코드는 로그/스크린샷에서 마스킹했다.
