# 개발 워크스테이션 구축 미션

## 1. 프로젝트 개요

이 저장소는 개발 입문 미션인 **"내 컴퓨터에 개발자용 작업실 꾸미기"** 수행 결과를 정리한 저장소입니다.

터미널, Docker, Git/GitHub를 사용하여 개발 환경을 구성하고,  
**Dockerfile 기반 웹 서버 실행**, **포트 매핑**, **바인드 마운트**, **볼륨 영속성** 등을 실습했습니다.

---

## 2. 실행 환경

- **OS**: Windows
- **Shell**: PowerShell
- **Terminal**: VS Code Terminal
- **작업 경로**: `C:\E1-1\작업실`
- **WSL**: `3.0.3.0`
- **Docker Desktop**: `29.9.2`
- **Git**: `2.56.0.windows.4`

---

## 3. 수행 체크리스트

- [x] 작업 폴더 생성
- [x] 기본 폴더 구조 생성
- [x] `README.md` 작성
- [x] 터미널 기본 조작 로그 기록
- [x] 권한/속성 실습
- [x] WSL 설치 확인
- [x] Docker 설치 및 버전 확인
- [x] `hello-world` 컨테이너 실행
- [x] `ubuntu` 컨테이너 실행 및 내부 명령 실행
- [x] Dockerfile 기반 커스텀 이미지 빌드
- [x] 포트 매핑 접속 확인
- [x] 바인드 마운트 반영 확인
- [x] Docker 볼륨 영속성 확인
- [x] Git 설치 및 사용자 설정
- [x] GitHub 저장소 생성 및 로컬 연결
- [x] 로컬/원격 저장소 동기화

---

## 4. 폴더 구조

```text
작업실/
├─ README.md
├─ Dockerfile
├─ site/
│  └─ index.html
├─ practice/
├─ screenshots/
└─ docs/
```


## 5. 폴더 역할
site : 웹 서버에서 보여줄 HTML 파일 저장
practice : 터미널 명령어, 복사, 이동, 삭제, 권한 실습용
screenshots : Docker 실행, 브라우저 접속, GitHub 연동 증거 이미지 저장
docs : 추가 문서나 로그 저장
screenshots: Docker 실행, 브라우저 접속, GitHub 연동 증거 이미지 저장
docs: 추가 문서나 로그 저장

## 6. 터미널 조작 로그

6-1. 현재 폴더 목록 확인
사용한 명령어:
Get-ChildItem -Force

확인 내용:
docs, practice, screenshots, site 폴더가 생성되었음을 확인했다.
README.md 파일이 생성되었음을 확인했다.

6-2. 터미널 기본 조작 실습
수행한 작업:
현재 위치 확인
practice 폴더로 이동
terminal-basic.txt 파일 생성
파일 목록 확인
terminal-basic.txt 파일 복사
moved 폴더 생성
복사한 파일을 moved 폴더로 이동
delete-me.txt 파일 생성 후 삭제
practice 폴더 전체 구조 확인

확인 결과:
practice/terminal-basic.txt 파일이 존재한다.
practice/moved/copied-terminal-basic.txt 파일이 존재한다.
delete-me.txt 파일은 삭제되어 존재하지 않는다.

사용한 주요 명령어:
Get-Location
Set-Location .\practice
Set-Content
Get-ChildItem
Copy-Item
New-Item
Move-Item
Remove-Item
Get-ChildItem -Recurse -Force

6-3. 권한/속성 실습
수행한 작업:
permission-test.txt 파일 생성
파일 속성 확인
읽기 전용 속성 설정
읽기 전용 속성 해제
변경 결과 확인

증거 로그:
Set-Content -Path .\practice\permission-test.txt -Value "permission practice" -Encoding UTF8
Set-ItemProperty -Path .\practice\permission-test.txt -Name IsReadOnly -Value $true
Set-ItemProperty -Path .\practice\permission-test.txt -Name IsReadOnly -Value $false

## 7. Docker 점검 로그
Docker Desktop 설치 및 실행 확인
hello-world 컨테이너 실행 확인
ubuntu 컨테이너 실행 및 기본 명령 실습 완료

## 8. Docker 웹 서버 컨테이너 실습
8-1. 실습 목표
Dockerfile을 이용해 웹 서버 이미지를 생성한다.
컨테이너를 실행하고 브라우저에서 정상 동작을 확인한다.
Bind Mount를 이용해 로컬 파일 수정 사항이 컨테이너에 즉시 반영되는지 확인한다.
Docker Volume을 이용해 컨테이너 삭제 후에도 데이터가 유지되는지 확인한다.

8-2. 프로젝트 구성
Dockerfile
site/index.html

8-3. Docker 이미지 빌드
Dockerfile을 작성한 뒤 아래 명령어로 이미지를 빌드하였다.
docker build -t my-web:latest .

빌드가 정상적으로 완료되면 my-web:latest 이미지가 생성된다.

8-4. 웹 서버 컨테이너 실행 및 확인
생성한 이미지를 이용하여 웹 서버 컨테이너를 실행하고, 브라우저에서 접속을 확인하였다.

실행 명령어:
docker run -d -p 8080:80 my-web:latest

브라우저 접속 주소:
http://localhost:8080
정상적으로 웹 페이지가 출력되는 것을 확인하였다.

8-5. Bind Mount 적용
로컬의 site 폴더를 컨테이너 내부의 /usr/share/nginx/html 경로에 연결하여 Bind Mount를 적용하였다.

실행 명령어:
docker run -d --name my-web-bind -p 8080:80 -v ${PWD}/site:/usr/share/nginx/html nginx

이후 로컬의 site/index.html 파일을 수정한 뒤 브라우저를 새로고침하여 변경 내용이 즉시 반영되는 것을 확인하였다.

Bind Mount 확인 결과:
로컬 파일을 수정하면 컨테이너 내부 웹 페이지에도 바로 반영되었다.
이를 통해 Bind Mount가 정상적으로 동작함을 확인하였다.

8-6. Docker Volume 생성 및 연결
Docker Volume을 생성한 후, 해당 볼륨을 웹 서버 컨테이너에 연결하였다.

사용한 명령어:
docker volume create webdata
docker run -d --name my-web-volume -p 8081:80 -v webdata:/usr/share/nginx/html my-web:latest

이후 컨테이너 내부의 /usr/share/nginx/html/index.html 파일을 수정하였다.

수정 후 브라우저 접속 주소:
http://localhost:8081
변경된 HTML 내용이 정상적으로 출력되는 것을 확인하였다.

수정한 내용:
Docker Volume Test
Data survives container deletion.

8-7. 데이터 영속성 검증
Docker Volume의 영속성을 확인하기 위해, 기존 컨테이너를 삭제한 뒤 동일한 Volume을 새 컨테이너에 다시 연결하여 데이터를 확인하였다.
웹 서버 실습과 별도로, Ubuntu 컨테이너를 사용하여 같은 개념의 영속성을 추가 검증하였다.

사용한 명령어:
docker rm -f my-web-volume
docker run -d --name my-web-volume-2 -p 8081:80 -v webdata:/usr/share/nginx/html my-web:latest

이후 다시 브라우저에서 아래 주소로 접속하였다.
http://localhost:8081

이전 컨테이너에서 수정했던 HTML 내용이 그대로 유지되는 것을 확인하였다.

영속성 검증 결과:
컨테이너를 삭제해도 Volume에 저장된 데이터는 사라지지 않았다.
동일한 Volume을 새 컨테이너에 연결하자 이전 데이터가 그대로 유지되었다.
이를 통해 Docker Volume의 데이터 영속성을 확인하였다.

8-8. 실습 결과 요약
이번 실습을 통해 다음 내용을 확인하였다.
Dockerfile을 이용해 웹 서버 이미지를 생성할 수 있다.
생성한 이미지를 기반으로 컨테이너를 실행하고 웹 브라우저에서 확인할 수 있다.
Bind Mount를 사용하면 로컬 파일 수정 사항이 컨테이너에 즉시 반영된다.
Docker Volume을 사용하면 컨테이너를 삭제해도 데이터가 유지된다.

8-9. 결론
이번 실습에서는 Docker를 이용하여 웹 서버 컨테이너를 구축하고, Bind Mount와 Volume의 차이를 직접 확인하였다.
Bind Mount는 로컬 개발 환경과 컨테이너를 연결할 때 유용하다.
Volume은 컨테이너 삭제 이후에도 데이터를 유지해야 할 때 유용하다.
특히 Volume을 이용한 실습에서, 컨테이너를 삭제한 뒤에도 HTML 파일 수정 내용이 그대로 유지되는 것을 확인함으로써 Docker Volume의 영속성 특징을 명확히 이해할 수 있었다.

8-10. 참고 사항
컨테이너 실행 시 동일한 이름의 컨테이너가 이미 존재하면 아래와 같은 오류가 발생할 수 있다.

The container name "..." is already in use
이 경우 기존 컨테이너를 삭제한 뒤 다시 실행하면 된다.
docker rm -f 컨테이너이름

## 9. Git/GitHub 설정 로그
Git 설치 확인 완료
로컬 저장소 초기화 완료
원격 저장소 연결 완료
로컬/원격 저장소 동기화 완료

## 10. 트러블슈팅
10-1
README 한글 깨짐 문제
증상:
PowerShell에서 Get-Content README.md 실행 시 한글이 깨져 보였다.

원인:
터미널 코드페이지와 PowerShell 출력 인코딩이 UTF-8로 맞지 않아 발생했다.

해결:
chcp 65001
[Console]::InputEncoding = [System.Text.UTF8Encoding]::new()
[Console]::OutputEncoding = [System.Text.UTF8Encoding]::new()
$OutputEncoding = [Console]::OutputEncoding
Get-Content -Encoding UTF8 .\README.md

결과:
README.md의 한글이 정상 출력되었다.
10-2
 컨테이너 이름 충돌 오류
증상:
이미 존재하는 컨테이너 이름으로 docker run 명령을 실행할 때 충돌 오류가 발생했다.

오류 예시:
Conflict. The container name "/my-web-bind" is already in use

원인:
동일한 이름의 컨테이너가 이미 생성되어 있었기 때문이다.

해결 방법:
기존 컨테이너를 삭제하거나
다른 이름으로 새 컨테이너를 생성하면 된다.

예시:
docker rm -f my-web-bind

## 11. 실행 화면 / 스크린샷

다음 항목들을 캡처하여 screenshots 폴더에 저장하였다.

Docker 이미지 빌드 성공 화면
docker ps 실행 결과
http://localhost:8080 접속 화면
Bind Mount 적용 전/후 화면
Docker Volume 생성 및 실행 화면
Volume 영속성 확인 화면
GitHub 업로드 결과 화면
 

## 12. 최종 정리
이번 미션을 통해 Windows 환경에서 개발에 필요한 기본 작업 공간을 직접 구성해 보았다.
터미널 기본 조작, 파일 및 속성 관리, Docker 컨테이너 실행, 웹 서버 이미지 빌드, 포트 매핑, Bind Mount, Volume 영속성, Git/GitHub 연동까지 개발 환경의 핵심 요소를 단계적으로 실습할 수 있었다.

특히 Docker 실습을 통해 단순히 컨테이너를 실행하는 것을 넘어서,
개발 중에는 Bind Mount가 유용하고, 데이터 유지가 필요할 때는 Volume이 적합하다는 점을 직접 확인할 수 있었다.

이 과정을 통해 로컬 개발 환경을 스스로 구성하고 관리하는 기본 흐름을 익힐 수 있었다.
