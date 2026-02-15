---
title: "GitHub 블로그 만들기 (2) - Chirpy 테마 설치 및 프로젝트 생성"
date: 2026-02-15 14:10:00 +0900
categories: [Blog, github_blog]
tags: [github-github_blog, jekyll, chirpy]
---

Jekyll blog에는 여러가지 테마가 있는데, 그 중 **Chirpy** 테마의 경우 카테고리 분류, 다크 테마 등의 다양한 기능을 같이 제공해주기 때문에 이 글에서는 Chirpy theme을 기준으로 작성함.

---

## 1. Chirpy Starter 클론

Chirpy 테마의 공식 권장 방식인 **Chirpy Starter** 템플릿을 사용 (I am probie)

```bash
git clone https://github.com/cotes2020/chirpy-starter.git username.github.io
```

> `username.github.io` 부분의 **username**을 본인의 GitHub 사용자명으로 바꿔주세요. (예: `my-name.github.io`)
{: .prompt-warning }

생성된 폴더로 이동합니다:

```bash
cd username.github.io
```

---

## 2. 프로젝트 구조

클론한 폴더의 주요 구조는 다음과 같습니다:

```
username.github.io/
├── _config.yml          # 블로그 전체 설정
├── _data/               # 사이드바 연락처, 공유 버튼 설정
├── _posts/              # 블로그 포스트 저장 폴더
├── _tabs/               # About, Archives, Categories, Tags 페이지
├── assets/              # 이미지, CSS 등 정적 파일
├── .github/workflows/   # GitHub Actions 배포 설정
├── Gemfile              # Ruby 의존성 목록
└── index.html           # 메인 페이지
```

---

## 3. 의존성 설치

Jekyll과 Chirpy 테마가 필요로 하는 Ruby 패키지(gem)들을 설치:

```bash
bundle install
```

---

## 4. Git 원격 저장소 연결

클론한 프로젝트는 Chirpy의 원본 저장소를 가리키고 있으므로, 본인의 GitHub 저장소로 변경해야 함.

### 4.1. 기존 Git 초기화 및 재설정

```bash
# 기존 .git 삭제 후 새로 초기화
rm -rf .git
git init
git branch -M main
```

### 4.2. 본인의 저장소 연결

```bash
git remote add origin https://github.com/username/username.github.io.git
```

> `username`을 본인의 GitHub 사용자명으로 변경해야함
{: .prompt-warning }

---

## 5. 로컬 미리보기

블로그를 GitHub에 올리기 전에, 로컬에서 먼저 확인할 수 있습니다:

```bash
bundle exec jekyll serve
```

브라우저에서 `http://localhost:4000`에 접속하면 블로그를 미리 볼 수 있습니다.

> 로컬 서버를 종료하려면 터미널에서 `Ctrl + C`를 누르세요.
{: .prompt-info }

종료 후에도 다시 `bundle exec jekyll serve`를 실행하면 언제든 미리보기를 할 수 있습니다.
