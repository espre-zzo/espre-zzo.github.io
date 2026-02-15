---
title: "GitHub 블로그 만들기 (1) - 사전 준비"
date: 2026-02-15 14:00:00 +0900
categories: [Blog, github_blog]
tags: [github_blog, jekyll]
---

Github blog 만들어서 공부하거나 찾아두었던 것들 정리해야지 했는데, 블로그 만드는 것 부터 잘 안되었어서 만드는 방법부터 정리해두려고 함.

---

## 1. GitHub 계정 만들기

1. [github.com](https://github.com)에 접속
2. **Sign up**
3. 이메일, 비밀번호, **사용자명(username)**을 입력.

> **사용자명 (username)**은 블로그 주소에 직접 사용됩니다. 예를 들어 username이 `my-name`이면 블로그 주소는 `my-name.github.io`가 됩니다.
{: .prompt-warning }

### 저장소(Repository) 생성

1. GitHub에 로그인 후 **New repository** 클릭
2. Repository name에 `username.github.io` 입력 (username은 본인의 GitHub 사용자명)
3. **Public** 선택
4. **Create repository** 클릭

> 저장소 이름은 반드시 `username.github.io` 형식이어야 함. **다른 이름을 사용하면 GitHub Pages가 정상 작동하지 않기 때문**
{: .prompt-danger }

---

## 2. Homebrew 설치 (macOS)

macOS에서 개발 도구를 쉽게 설치할 수 있는 패키지 관리자. github 페이지에 들어가지 않고 로컬에서 파일을 만들어서 올리기 위한 사전작업.

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

설치 완료 후 확인:

```bash
brew --version
```

---

## 3. Ruby 설치 (rbenv)

Jekyll은 Ruby로 만들어진 도구이기 때문에 Ruby 설치가 필수. macOS의 경우 기본 Ruby가 설치되어 있지만 버전이 낮아 Jekyll 4.x를 사용할 수 없기 때문에 **rbenv**를 사용하여 최신 Ruby를 설치하여야 함.

### 3.1. rbenv 설치

```bash
brew install rbenv ruby-build
```

### 3.2. shell 설정에 rbenv 추가

사용하는 shell에 따라 설정 파일이 다릅니다:

```bash
# zsh 사용자 (macOS 기본)
echo 'eval "$(rbenv init - zsh)"' >> ~/.zshrc
source ~/.zshrc

# bash 사용자
echo 'eval "$(rbenv init - bash)"' >> ~/.bashrc
source ~/.bashrc
```

### 3.3. Ruby 설치

사용 가능한 버전을 확인한 후 설치:

```bash
# 설치 가능한 버전 확인
rbenv install -l

# Ruby 3.3.x 설치 (최신 안정 버전 확인 후 x 수정)
rbenv install 3.3.x

# 시스템 기본 Ruby로 설정
rbenv global 3.3.x
```

### 3.4. 설치 확인

```bash
ruby --version
# ruby 3.3.x ... 이 출력되면 성공
```

> `ruby --version`을 실행했을 때 시스템 Ruby(2.6.x)가 나온다면, 터미널 재시작 후 확인.
{: .prompt-tip }

---

## 4. Jekyll & Bundler 설치

Ruby설치 완료 후 Jekyll과 Bundler를 설치.

```bash
gem install jekyll bundler
```

설치 확인:

```bash
jekyll --version
bundler --version
```

---

## 5. Git 확인

macOS에는 Git이 기본 설치되어있음.

```bash
# git 설치 여부 확인
git --version
```

만약 설치되어 있지 않다면:

```bash
brew install git
```

### Git 사용자 정보 설정

커밋에 표시될 이름과 이메일을 설정합니다:

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

---

## 6. GitHub CLI (gh) 설치

GitHub CLI를 사용하면 터미널에서 GitHub 인증, 저장소 관리 등을 편리하게 할 수 있습니다.

### 6.1. 설치

```bash
brew install gh
```

### 6.2. GitHub 로그인

```bash
gh auth login
```

1. **GitHub.com** 선택
2. **HTTPS** 선택
3. **Login with a web browser** 선택
4. 화면에 표시된 코드를 복사하여 브라우저에서 인증

인증 상태 확인:

```bash
gh auth status
```

---

## 설치 요약 체크리스트

| 도구 | 확인 명령어 | 용도 |
|------|-----------|------|
| Homebrew | `brew --version` | 패키지 관리자 |
| rbenv | `rbenv --version` | Ruby 버전 관리 |
| Ruby 3.3+ | `ruby --version` | Jekyll 실행 환경 |
| Jekyll | `jekyll --version` | 정적 사이트 생성기 |
| Bundler | `bundler --version` | Ruby 의존성 관리 |
| Git | `git --version` | 버전 관리 |
| GitHub CLI | `gh --version` | GitHub 인증/관리 |