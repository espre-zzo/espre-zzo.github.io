---
title: "GitHub 블로그 만들기 (3) - 설정"
date: 2026-02-15 14:20:00 +0900
categories: [Blog, github_blog]
tags: [github-github_blog, jekyll, chirpy, config]
---

## 1. _config.yml 기본 설정

프로젝트 루트에 있는 `_config.yml` 파일을 텍스트 에디터로 열어서 수정.

```bash
nano _config.yml
```

### 1.1. 언어 및 시간대 (필수는 아님, 사이트를 한국어로 표기하기 위함)

```yaml
lang: ko                     # 한국어로 설정
timezone: Asia/Seoul         # 시간대를 서울로 설정
```

### 1.2. 블로그 제목 및 소개

```yaml
title: My Blog                # 사이드바 상단에 표시되는 블로그 이름
tagline: Welcome to my blog   # 블로그 이름 아래 소개글
description: >-               # SEO 및 RSS feed에 사용되는 설명
  My personal blog about programming and technology.
```

### 1.3. URL 설정

```yaml
url: "https://username.github.io"   # 본인의 블로그 주소
```

---

## 2. 프로필 설정 (띄어쓰기에 유의)

### 2.1. GitHub 사용자명

```yaml
github:
  username: your-username    # GitHub 사용자명
```

### 2.2. 소셜 정보

```yaml
social:
  name: Your Name            # 포스트 작성자 및 푸터에 표시
  email: your@email.com      # 이메일 (선택)
  links:
    - https://github.com/your-username
    # - https://twitter.com/your-username (사용하지 않을 것은 텍스트 처리)
```

---

## 3. 아바타 (프로필 이미지)

사이드바에 표시되는 프로필 이미지.

### 방법 1: 로컬 이미지 사용

이미지를 `assets/img/` 폴더에 넣고 경로를 지정해줘야 함:

```yaml
avatar: /assets/img/avatar.jpg #img 폴더는 없으면 만들면 됨.
```

### 방법 2: GitHub 프로필 사진 사용

```yaml
avatar: https://github.com/your-username.png
```

> 이미지는 원형으로 잘려서 표기되기 때문에, 정사각형을 추천!
{: .prompt-tip }

---

## 4. 테마 모드

라이트/다크 모드 설정:

```yaml
theme_mode:   # 비워두면 시스템 설정을 따름 (자동 전환)
# theme_mode: light   # 항상 라이트 모드
# theme_mode: dark    # 항상 다크 모드
```

---

## 5. About 페이지 수정

사이드바의 **About** 탭을 클릭하면 나오는 자기소개 페이지.

`_tabs/about.md` 파일을 수정해서 자기소개 내용을 변경:

```markdown
---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---

## 안녕하세요!

저는 **Your Name**입니다.

### 관심 분야

- 프로그래밍
- 데이터 분석
- 기술 블로깅

### 연락처

- GitHub: [your-username](https://github.com/your-username)
- Email: your@email.com
```

> front matter(`---` 사이 영역)는 수정하면 안됨.
{: .prompt-warning }

---

## 6. 변경 후 확인

설정을 변경한 후 로컬 브라우저에서 미리 확인:

```bash
bundle exec jekyll serve
```

`http://localhost:4000`에서 변경사항이 반영되었는지 확인필요.

> `_config.yml`을 수정한 경우, 로컬 서버를 **반드시 재시작**해야 반영됨. `Ctrl + C`로 종료 후 다시 실행할 것.
{: .prompt-danger }