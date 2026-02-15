---
title: "GitHub 블로그 만들기 (6) - 기타"
date: 2026-02-15 14:50:00 +0900
categories: [Blog, github_blog]
tags: [github_blog, jekyll, chirpy]
---

## 1. 카테고리와 태그

Chirpy 테마에서 카테고리와 태그는 포스트의 front matter에 작성하면 **자동으로 생성**됨.

### 카테고리 구조

카테고리는 최대 **2단계**(대분류, 소분류)까지 지원됨.

```yaml
categories: [대분류, 소분류]
```

**일관된 카테고리를 유지하는 것이 중요.** 너무 많으면 정리하기 힘듦.

| 추천 예시 | 설명 |
|----------|------|
| `[Programming, Python]` | 프로그래밍 > 파이썬 |
| `[Programming, R]` | 프로그래밍 > R |
| `[DevOps, Linux]` | DevOps > 리눅스 |
| `[Blog, Tutorial]` | 블로그 > 튜토리얼 |

### 태그 규칙

- 소문자 + 하이픈 사용: `data-analysis`, `machine-learning`
- 너무 세분화하지 않기

---

## 2. 포스트 날짜 주의사항 (중요)

Jekyll은 **미래 날짜의 포스트를 자동으로 숨김**.

> 포스트가 사이트에 보이지 않는 가장 흔한 원인이 바로 **미래 날짜**. front matter의 `date`가 현재 시각 이전인지 반드시 확인할 것!
{: .prompt-danger }

### 예약 포스트 사용하기

미래 날짜로 포스트를 예약하고 싶다면, GitHub Actions에 **매일 자동 빌드**를 설정하면 편함.

`.github/workflows/pages-deploy.yml`에 다음을 추가:

```yaml
on:
  push:
    branches:
      - main

  # 매일 14:00 KST (05:00 UTC)에 자동 빌드
  schedule:
    - cron: "0 5 * * *"

  workflow_dispatch:
```

이렇게 설정하면 매일 지정된 시각에 사이트가 리빌드되어, 해당 날짜의 포스트가 자동으로 공개됨 (시리즈로 게시할 때 편리, 1-2개씩 포스트하면 그냥 바로 push 하도록 하자).

---

## 3. 이미지 관리

### 이미지 저장 위치

`assets/img/` 폴더에 저장하고, 포스트에서 참조(hyperlink):

```markdown
![설명](/assets/img/my-image.png)
```

### 이미지 크기 최적화

블로그 로딩 속도를 위해 이미지를 최적화하는 것이 좋음:

- **PNG**: 스크린샷, 다이어그램에 적합
- **JPG**: 사진에 적합 (파일 크기 작음)
- **WebP**: 최신 형식, 파일 크기가 가장 작음

### 포스트별 이미지 정리

포스트가 많아지면 이미지 관리가 어려워지므로, 하위 폴더를 만들어 정리할 것 (난장판을 좋아하면 그냥 두도록 하자):

```
assets/img/
├── posts/
│   ├── 2026-02-15-tutorial/
│   │   ├── screenshot1.png
│   │   └── screenshot2.png
│   └── 2026-02-16-python/
│       └── example.png
└── avatar.jpg
```

---

## 4. Chirpy 테마 업데이트

Chirpy 테마에 새로운 기능이나 버그 수정이 있을 수 있음 (Thank you). `Gemfile`에서 테마 버전을 업데이트 하도록 하자:

```bash
bundle update jekyll-theme-chirpy
```

> 업데이트 후 로컬에서 정상 작동하는지 반드시 확인한 뒤 push할 것.
{: .prompt-warning }

---

## 5. 유용한 명령어 모음

| 명령어 | 용도 |
|--------|------|
| `bundle exec jekyll serve` | 로컬 미리보기 시작 |
| `bundle exec jekyll serve --drafts` | 초안 포함 미리보기 |
| `git status` | 변경된 파일 확인 |
| `git add -A` | 모든 변경사항 스테이징 |
| `git commit -m "메시지"` | 커밋 |
| `git push` | GitHub에 업로드 |
| `gh run list --limit 3` | 최근 빌드 상태 확인 |
| `gh run view {ID} --log` | 빌드 로그 확인 |
