# Linux · WSL · Docker · Git 실습

> **Codyssey E1-1 · Development Environment & Container Fundamentals**  
> Windows에서 WSL Ubuntu 기반의 Linux 명령어를 익히고, Docker 컨테이너와 NGINX 이미지를 직접 실행·빌드하며, Git/GitHub로 결과물을 관리한 기초 인프라 실습입니다.

<p align="center">
  <img alt="Linux" src="https://img.shields.io/badge/Linux-CLI-FCC624?style=flat-square&logo=linux&logoColor=black">
  <img alt="Ubuntu" src="https://img.shields.io/badge/Ubuntu-WSL-E95420?style=flat-square&logo=ubuntu&logoColor=white">
  <img alt="Docker" src="https://img.shields.io/badge/Docker-Container-2496ED?style=flat-square&logo=docker&logoColor=white">
  <img alt="NGINX" src="https://img.shields.io/badge/NGINX-Web_Server-009639?style=flat-square&logo=nginx&logoColor=white">
  <img alt="Git" src="https://img.shields.io/badge/Git-Version_Control-F05032?style=flat-square&logo=git&logoColor=white">
  <img alt="GitHub" src="https://img.shields.io/badge/GitHub-Repository-181717?style=flat-square&logo=github&logoColor=white">
</p>

---

## Summary

| 구분 | 내용 |
|---|---|
| 학습 목표 | Linux 파일 시스템과 권한, Docker 이미지·컨테이너, 포트 매핑과 마운트, Git 원격 저장소 흐름 이해 |
| 실행 환경 | Windows Terminal, WSL Ubuntu, Docker, Git/GitHub |
| 핵심 결과 | Linux 파일 작업, 권한 변경, Docker 실행 확인, Ubuntu 컨테이너 관리, NGINX 커스텀 이미지 빌드, 웹 서비스 실행, 바인드 마운트 반영 |
| 웹 이미지 | `nginx:alpine` 기반 `codyssey-web:1.0` |
| 포트 매핑 | 호스트 `8080` → 컨테이너 `80`; 바인드 마운트 결과는 `8081`에서 확인 |
| 저장소 | [GitHub Repository](https://github.com/qjskffj-code/codyssey_E1-1) |

### 바로가기

- [실습 목표](#실습-목표)
- [검증된 결과](#검증된-결과)
- [Linux 실습](#1-linux-cli와-권한)
- [Docker 실습](#2-docker-이미지와-컨테이너)
- [NGINX 웹 서버](#3-dockerfile과-nginx-웹-서버)
- [트러블슈팅](#troubleshooting)
- [스크린샷 구성](#screenshots)
- [검증 상태](#verification-status)

---

# Overview

이 프로젝트는 개발 도구를 설치하는 데서 끝나지 않고, **호스트 운영체제 → WSL Linux → Docker Engine → 이미지 → 컨테이너 → 웹 브라우저**로 이어지는 실행 구조를 직접 확인하는 데 목적이 있습니다.

Linux에서는 디렉터리와 파일을 만들고 이동·복사·이름 변경·삭제했으며, `chmod`를 사용해 파일과 디렉터리 권한을 변경했습니다. Docker에서는 공식 `hello-world` 이미지로 엔진 동작을 확인한 뒤 Ubuntu 컨테이너의 생성·종료·재시작·접속 흐름을 실습했습니다.

마지막으로 `nginx:alpine`을 기반으로 정적 웹페이지를 포함한 커스텀 이미지를 빌드하고, 호스트 포트와 컨테이너 포트를 연결해 브라우저에서 결과를 확인했습니다. 바인드 마운트 실습에서는 호스트의 HTML 변경이 실행 중인 웹페이지에 반영되는 것도 확인했습니다.

## 실습 목표

1. Linux의 경로와 파일 시스템을 CLI로 다룬다.
2. 사용자·그룹·기타 사용자의 권한 표기를 이해하고 `chmod`로 변경한다.
3. Docker 이미지와 컨테이너의 차이를 실행 과정에서 확인한다.
4. 컨테이너 생성, 조회, 종료, 시작, 재접속의 생명주기를 익힌다.
5. Dockerfile로 재현 가능한 커스텀 이미지를 만든다.
6. 포트 매핑으로 컨테이너의 NGINX를 호스트 브라우저에 공개한다.
7. 바인드 마운트로 호스트 파일과 컨테이너 파일을 연결한다.
8. Git 커밋과 GitHub 원격 저장소 업로드 흐름을 경험한다.

## Tech / Tools

| 영역 | 사용 기술 | 확인 범위 |
|---|---|---|
| Host | Windows, Windows Terminal | 화면에서 확인 |
| Linux | WSL Ubuntu | `wsl -d Ubuntu` 실행 화면에서 확인 |
| Container | Docker | `hello-world`, `docker images`, `docker ps`로 동작 확인 |
| Container OS | Ubuntu 26.04.1 LTS | 컨테이너의 `/etc/os-release` 출력에서 확인 |
| Web Server | NGINX Alpine | Dockerfile과 이미지 빌드 로그에서 확인 |
| Version Control | Git / GitHub | 대화 기록상 원격 업로드 완료; 별도 캡처는 미확인 |

> Docker, Git, Windows 및 WSL의 정확한 버전 번호는 현재 확보한 캡처에서 확인되지 않아 기재하지 않았습니다.

---

# 검증된 결과

| ID | 실습 항목 | 실제 확인 결과 | 증빙 |
|---|---|---|---|
| E-01 | Linux 파일 작업 | 디렉터리 생성·이동, 파일 생성·출력·복사·이름 변경·삭제 확인 | 캡처 |
| E-02 | Linux 권한 | `hello.txt`를 `600`, `permission-test`를 `700`으로 변경 | 캡처 |
| E-03 | Docker 엔진 | `hello-world:latest` 다운로드 및 `Hello from Docker!` 출력 | 캡처 |
| E-04 | Ubuntu 컨테이너 | `codyssey-ubuntu` 실행, 쉘 접속, Ubuntu 26.04.1 LTS 정보 출력 | 캡처 |
| E-05 | 컨테이너 생명주기 | 종료된 컨테이너 조회 후 `docker start`, `docker exec` 실행 | 캡처 |
| E-06 | 커스텀 이미지 | `codyssey-web:1.0` 빌드 완료 및 이미지 목록 등록 | 캡처 |
| E-07 | NGINX 포트 매핑 | `8080:80`으로 컨테이너 실행 및 실행 상태 확인 | 캡처 |
| E-08 | 웹페이지 응답 | `localhost:8080`에서 정적 HTML 페이지 출력 | 캡처 |
| E-09 | 바인드 마운트 | HTML 수정 후 `localhost:8081` 페이지에 변경 내용 반영 | 캡처 |
| E-10 | GitHub 업로드 | 사용자가 원격 저장소 업로드 완료를 확인 | 대화 기록 |

> Docker 볼륨의 생성·재마운트·데이터 영속성은 현재 제공된 캡처나 명령 출력으로 확인되지 않았으므로 완료 항목에 포함하지 않았습니다.

---

# 1. Linux CLI와 권한

## 파일과 디렉터리 작업

홈 디렉터리에서 실습 폴더를 구성하고 다음 명령의 동작을 확인했습니다.

```bash
pwd
ls -la
mkdir
cd
touch
echo
cat
cp
mv
rm
rmdir
```

실습 흐름은 다음과 같습니다.

```text
홈 디렉터리
└─ codyssey-e1-1/
   └─ practice/
      ├─ hello.txt
      └─ permission-test/
```

- `hello.txt`를 만들고 `Hello Codyssey!` 문자열을 저장했습니다.
- `cp`로 사본을 만든 뒤 `mv`로 이름을 변경했습니다.
- 불필요한 파일과 빈 디렉터리를 각각 `rm`, `rmdir`로 제거했습니다.

## 권한 변경

`ls -l`로 권한을 확인한 뒤 숫자 표기법으로 권한을 변경했습니다.

```bash
chmod 600 hello.txt
chmod 700 permission-test
```

| 대상 | 권한 | 의미 |
|---|---|---|
| `hello.txt` | `-rw-------` | 소유자만 읽기·쓰기 가능 |
| `permission-test/` | `drwx------` | 소유자만 조회·생성·이동 가능 |

이 과정에서 파일 권한의 `r`, `w`, `x`가 각각 읽기, 쓰기, 실행/접근을 뜻하며, `600`과 `700`이 소유자에게만 권한을 부여한다는 점을 확인했습니다.

---

# 2. Docker 이미지와 컨테이너

## Docker 실행 확인

공식 `hello-world` 이미지를 실행해 Docker 클라이언트가 데몬에 연결되고, 이미지 다운로드와 컨테이너 실행이 정상적으로 이어지는지 확인했습니다.

```bash
docker run hello-world
docker images
docker ps -a
```

확인 결과 `Hello from Docker!` 메시지가 출력되었고, 실행이 끝난 컨테이너는 `Exited (0)` 상태로 조회되었습니다. 이를 통해 **이미지는 실행에 필요한 불변 템플릿이고, 컨테이너는 그 이미지를 기반으로 생성된 실행 단위**라는 차이를 확인했습니다.

## Ubuntu 컨테이너

Ubuntu 컨테이너를 이름과 함께 대화형으로 실행했습니다.

```bash
docker run -it --name codyssey-ubuntu ubuntu bash
```

컨테이너 내부에서 기본 경로와 운영체제 정보를 확인했습니다.

```bash
pwd
ls
cat /etc/os-release
```

캡처의 `/etc/os-release` 출력에서는 컨테이너가 **Ubuntu 26.04.1 LTS**임을 확인할 수 있습니다.

## 컨테이너 생명주기

종료된 컨테이너를 새로 만들지 않고 다시 시작한 뒤 쉘에 접속했습니다.

```bash
docker ps -a
docker start codyssey-ubuntu
docker exec -it codyssey-ubuntu bash
```

이 실습을 통해 `run`은 이미지에서 새 컨테이너를 만들고 실행하며, `start`는 기존 컨테이너를 다시 실행하고, `exec`는 실행 중인 컨테이너에서 추가 명령을 수행한다는 차이를 익혔습니다.

---

# 3. Dockerfile과 NGINX 웹 서버

## 프로젝트 파일

```text
codyssey-e1-1/
├─ Dockerfile
├─ practice/
└─ site/
   └─ index.html
```

캡처에서 확인한 Dockerfile은 다음 구성입니다.

```dockerfile
FROM nginx:alpine

COPY site/ /usr/share/nginx/html/

EXPOSE 80
```

- `nginx:alpine`을 가벼운 베이스 이미지로 사용했습니다.
- 로컬 `site/`의 정적 파일을 NGINX 기본 문서 경로로 복사했습니다.
- 컨테이너가 80번 포트를 사용한다는 정보를 명시했습니다.

## 이미지 빌드

```bash
docker build -t codyssey-web:1.0 .
docker images
```

빌드 로그의 모든 단계가 완료되었고, `docker images`에서 `codyssey-web:1.0`이 생성된 것을 확인했습니다.

## 포트 매핑과 웹페이지 확인

```bash
docker run -d -p 8080:80 --name codyssey-web codyssey-web:1.0
docker ps
```

포트 매핑 구조는 다음과 같습니다.

```text
Browser
  │  http://localhost:8080
  ▼
Windows Host :8080
  │  Docker port mapping
  ▼
NGINX Container :80
  │
  ▼
/usr/share/nginx/html/index.html
```

브라우저에서 다음 내용이 출력되는 것을 확인했습니다.

```text
Hello Codyssey!
My first Docker web server.
```

---

# 4. 바인드 마운트

이미지에 파일을 복사하는 방식은 HTML을 수정할 때마다 이미지를 다시 빌드해야 합니다. 개발 중에는 호스트의 `site/` 디렉터리를 컨테이너의 NGINX 문서 경로에 연결하는 바인드 마운트를 활용할 수 있습니다.

아래는 동일한 결과를 재현하기 위한 예시 명령입니다. 현재 캡처에서는 실행 결과만 확인되며, 최초 실행 명령 전체는 보이지 않습니다.

```bash
docker run -d \
  -p 8081:80 \
  --name codyssey-bind \
  --mount type=bind,source="$(pwd)/site",target=/usr/share/nginx/html,readonly \
  nginx:alpine
```

`site/index.html`을 수정한 뒤 `localhost:8081`을 새로고침해 다음 변경 내용이 반영된 것을 확인했습니다.

```text
Hello Codyssey! - Updated
Bind mount is working!
```

이 결과를 통해 바인드 마운트는 **호스트 파일을 컨테이너에서 바로 참조**하므로, 정적 파일 수정 결과를 이미지 재빌드 없이 확인할 수 있다는 점을 이해했습니다.

---

# Git / GitHub Workflow

대화 기록상 로컬 프로젝트의 파일을 GitHub 원격 저장소에 업로드했습니다.

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/qjskffj-code/codyssey_E1-1.git
git push -u origin main
```

> 위 명령은 실습 흐름을 정리한 예시입니다. 현재 제공된 캡처에는 실제 커밋 해시, 브랜치 로그, 최종 `git push` 출력이 포함되어 있지 않으므로 해당 세부 정보는 기재하지 않았습니다.

---

# Troubleshooting

## 1. Docker Desktop과 WSL 연동

| 항목 | 내용 |
|---|---|
| 증상 | WSL Ubuntu에서 Docker 명령이 정상적으로 연결되지 않음 |
| 원인 범위 | Docker Desktop의 WSL 연동 설정 또는 배포판 연결 상태 |
| 해결 방향 | Docker Desktop에서 WSL 기반 엔진과 Ubuntu 배포판 연동 상태를 점검하고 WSL 세션을 다시 시작 |
| 검증 | 이후 WSL에서 `docker run hello-world` 성공 |

## 2. Docker 소켓 접근 권한

| 항목 | 내용 |
|---|---|
| 증상 | 일반 사용자로 Docker 데몬 소켓에 접근하지 못함 |
| 원인 범위 | 현재 사용자의 Docker 그룹 권한이 세션에 반영되지 않음 |
| 해결 방향 | Docker 그룹과 사용자 권한을 점검한 뒤 WSL 세션을 다시 시작 |
| 검증 | 일반 사용자 프롬프트에서 `docker images`, `docker ps` 실행 성공 |

> 위 두 항목은 이전 대화에 해결 사례로 기록되어 있으나 오류 원문과 실제 설정 명령은 현재 첨부 자료에서 확인되지 않습니다. 따라서 재현되지 않은 구체 명령은 적지 않았습니다.

## 3. GitHub HTTPS 인증 실패

첫 `git push` 시 계정 비밀번호 방식으로 인증을 시도해 다음 오류가 발생했습니다.

```text
remote: Invalid username or token.
Password authentication is not supported for Git operations.
fatal: Authentication failed
```

GitHub HTTPS 인증에는 계정 비밀번호 대신 저장소 쓰기 권한이 있는 Personal Access Token을 사용했습니다. 인증 정보를 코드나 README에 저장하지 않고, 비밀번호 입력란에 토큰을 입력해 원격 업로드를 완료했습니다.

---

# What I Learned

- Linux에서 상대 경로와 절대 경로를 구분하고 파일 시스템을 CLI로 탐색하는 방법
- 파일과 디렉터리의 소유자 권한을 숫자 표기법으로 변경하는 방법
- Docker 이미지와 컨테이너가 각각 템플릿과 실행 단위라는 차이
- 컨테이너를 새로 생성하는 `run`과 기존 컨테이너를 재사용하는 `start`, `exec`의 역할
- Dockerfile의 명령을 이용해 동일한 실행 환경을 이미지로 재현하는 방법
- 호스트 포트와 컨테이너 포트를 연결해 외부에서 서비스에 접근하는 원리
- 이미지 빌드 시점에 파일을 복사하는 방식과 바인드 마운트로 실시간 연결하는 방식의 차이
- GitHub HTTPS 인증에서 계정 비밀번호 대신 PAT 등 지원되는 인증 수단이 필요하다는 점
- 실습 결과를 포트폴리오로 제시할 때 명령 자체보다 **검증 가능한 출력과 개인정보 관리**가 중요하다는 점

## 한 줄 회고

> 개발 환경은 도구를 설치하는 것으로 완성되는 것이 아니라, **Linux 파일 권한부터 컨테이너 실행, 네트워크 연결, 데이터 연결, 버전 관리까지 각 계층이 어떻게 이어지는지 직접 검증할 때 비로소 이해할 수 있었습니다.**

