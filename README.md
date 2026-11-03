# 개발 워크스테이션 구축 미션

## 1. 프로젝트 개요

이 저장소는 개발 입문 미션인 "내 컴퓨터에 개발자용 작업실 꾸미기" 수행 결과를 정리한 저장소입니다.

터미널, Docker, Git/GitHub를 사용하여 개발 환경을 구성하고, Dockerfile 기반 웹 서버 실행, 포트 매핑, 바인드 마운트, 볼륨 영속성 등을 실습합니다.

## 2. 실행 환경

- OS: Windows
- Shell: PowerShell
- Terminal: VSCode Terminal
- 작업 경로: C:\E1-1\작업실
- Docker: 확인 예정
- Git: 확인 예정

## 3. 수행 체크리스트

- [x] 작업 폴더 생성
- [x] 기본 폴더 구조 생성
- [x] README.md 생성
- [x] 터미널 기본 조작 로그 기록
- [x] 권한 실습
- [ ] Docker 설치 및 기본 점검
- [ ] hello-world 컨테이너 실행
- [ ] ubuntu 컨테이너 실행 및 내부 명령 실행
- [ ] Dockerfile 기반 커스텀 이미지 빌드
- [ ] 포트 매핑 접속 확인
- [ ] 바인드 마운트 반영 확인
- [ ] Docker 볼륨 영속성 확인
- [ ] Git 설정
- [ ] GitHub 저장소 연동

## 4. 폴더 구조

작업실/
├─ README.md
├─ site/
├─ practice/
├─ screenshots/
└─ docs/

## 5. 폴더 역할

| 폴더 | 역할 |
|---|---|
| site | 웹 서버에서 보여줄 HTML 파일 저장 |
| practice | 터미널 명령어, 복사, 이동, 삭제, 권한 실습용 |
| screenshots | Docker 실행, 브라우저 접속, GitHub 연동 증거 이미지 저장 |
| docs | 추가 문서나 로그 저장 |

## 6. 터미널 조작 로그

### 6-1. 현재 폴더 목록 확인

사용한 명령어:

Get-ChildItem -Force

확인 내용:

- docs, practice, screenshots, site 폴더가 생성되었음을 확인했다.
- README.md 파일을 생성했다.
- attendance.csv 파일이 작업 폴더에 있었지만, 개인정보 보호를 위해 내용은 기록하지 않는다.

## 7. Docker 점검 로그

아직 수행 전.

## 8. Git/GitHub 설정 로그

아직 수행 전.

## 9. 트러블슈팅

아직 기록 전.

### 6-2. 터미널 기본 조작 실습

수행한 작업:

- 현재 위치 확인
- practice 폴더로 이동
- terminal-basic.txt 파일 생성
- 파일 목록 확인
- terminal-basic.txt 파일 복사
- moved 폴더 생성
- 복사한 파일을 moved 폴더로 이동
- delete-me.txt 파일 생성 후 삭제
- practice 폴더 전체 구조 확인

확인 결과:

- practice/terminal-basic.txt 파일이 존재한다.
- practice/moved/copied-terminal-basic.txt 파일이 존재한다.
- delete-me.txt 파일은 삭제되어 존재하지 않는다.

사용한 주요 명령어:

```powershell
Get-Location
Set-Location .\practice
Set-Content
Get-ChildItem
Copy-Item
New-Item
Move-Item
Remove-Item
Get-ChildItem -Recurse -Force

그다음 확인용으로 이것도 실행해 주세요.

```powershell
Get-Content README.md -Tail 40
$readme = Get-Content -Raw -Encoding UTF8 README.md

$readme = $readme -replace '- \[ \] 터미널 기본 조작 로그 기록', '- [x] 터미널 기본 조작 로그 기록'
$readme = $readme -replace '- \[ \] 터미널 기본 조작 실습', '- [x] 터미널 기본 조작 실습'

Set-Content -Path README.md -Value $readme -Encoding UTF8

@'

### 6-2. 터미널 기본 조작 실습

수행한 작업:

- 현재 위치 확인
- practice 폴더로 이동
- terminal-basic.txt 파일 생성
- 파일 목록 확인
- terminal-basic.txt 파일 복사
- moved 폴더 생성
- 복사한 파일을 moved 폴더로 이동
- delete-me.txt 파일 생성 후 삭제
- practice 폴더 전체 구조 확인

확인 결과:

- practice/terminal-basic.txt 파일이 존재한다.
- practice/moved/copied-terminal-basic.txt 파일이 존재한다.
- delete-me.txt 파일은 삭제되어 존재하지 않는다.

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



### 6-2. 터미널 기본 조작 실습

수행한 작업:

- 현재 위치 확인
- practice 폴더로 이동
- terminal-basic.txt 파일 생성
- 파일 목록 확인
- terminal-basic.txt 파일 복사
- moved 폴더 생성
- 복사한 파일을 moved 폴더로 이동
- delete-me.txt 파일 생성 후 삭제
- practice 폴더 전체 구조 확인

확인 결과:

- practice/terminal-basic.txt 파일이 존재한다.
- practice/moved/copied-terminal-basic.txt 파일이 존재한다.
- delete-me.txt 파일은 삭제되어 존재하지 않는다.

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


### 6-3. 권한/속성 실습

수행한 작업:

- permission-test.txt 파일 생성
- 파일 속성 확인
- 읽기 전용 속성 설정
- 읽기 전용 속성 해제
- 변경 결과 확인

증거 로그:

```powershell
Set-Content -Path .\practice\permission-test.txt -Value "permission practice" -Encoding UTF8

# 읽기 전용 설정 전
FullName   : C:\E1-1\작업실\practice\permission-test.txt
Attributes : Archive

Set-ItemProperty -Path .\practice\permission-test.txt -Name IsReadOnly -Value $true

# 읽기 전용 설정 후
FullName   : C:\E1-1\작업실\practice\permission-test.txt
Attributes : ReadOnly, Archive

Set-ItemProperty -Path .\practice\permission-test.txt -Name IsReadOnly -Value $false

# 읽기 전용 해제 후
FullName   : C:\E1-1\작업실\practice\permission-test.txt
Attributes : Archive
```

## Git 설치 확인
- 확인 명령어: git --version
- 설치 버전: git version 2.56.0.windows.4
- 결과: 정상 확인 완료
