---
title: "GitHub 블로그 만들기 (5) - 배포"
date: 2026-02-15 14:40:00 +0900
categories: [Blog, github_blog]
tags: [github-github_blog, jekyll]
---

이제 로컬에서 작성한 markdown post를 실제 블로그에 게시하는 과정.

---

## 1. Git 기본 개념

| 용어 | 설명 |
|------|------|
| **add** | 변경된 파일을 스테이징 영역에 추가 |
| **commit** | 스테이징된 변경사항을 기록 (스냅샷 저장) |
| **push** | 로컬 커밋을 원격 저장소(GitHub)에 업로드 |
| **pull** | 원격 저장소의 변경사항을 로컬로 가져오기 |

```
[파일 수정] → git add → git commit → git push → [GitHub에 반영]
```

---

## 2. 첫 번째 Push

### 2.1. 변경된 파일 확인

```bash
git status
```

추적되지 않은 파일(빨간색)과 수정된 파일의 확인

### 2.2. 파일 스테이징

```bash
# 특정 파일만 추가
git add _posts/2026-02-15-my-first-post.md

# 또는 변경된 모든 파일 추가
git add -A
```

> `git add -A`는 모든 파일을 추가하므로, `.env`나 개인정보가 담긴 파일이 포함되지 않도록 주의할 것. (웬만하면 사용하지 말자) `.gitignore` 파일에 제외할 항목을 추가할 수 있음.
{: .prompt-warning }

### 2.3. 커밋

```bash
git commit -m "Add first blog post"
```

커밋 메시지는 **무엇을 변경했는지** 간단히 설명해줌.

### 2.4. Push

```bash
git push -u origin main
```

> 첫 push에서만 `-u origin main`을 사용. **이후에는 `git push`만으로 충분**
{: .prompt-info }

---

## 3. GitHub Pages 설정

Push 후 GitHub에서 배포 설정을 해야 함.

### 3.1. GitHub Actions 설정

1. GitHub 저장소 페이지로 이동
2. **Settings**
3. 좌측 메뉴에서 **Pages**를 클릭
4. **Source**를 **GitHub Actions**로 변경.

> Chirpy 테마에는 `.github/workflows/pages-deploy.yml` 파일이 이미 포함되어 있음. Source를 "GitHub Actions"로 설정하기만 하면 자동으로 빌드 및 배포가 진행됨.
{: .prompt-tip }

### 3.2. 빌드 확인

1. 저장소의 **Actions** 탭을 클릭
2. "Build and Deploy" 워크플로우가 실행 중인지 확인.
3. 초록색 체크가 나타나면 빌드 성공.

### 3.3. 배포 확인

빌드가 완료되면 브라우저에서 접속하여 확인 (push 후 1~2분이면 자동으로 배포됨):

```
https://username.github.io
```

---

## 4. 빌드 상태 확인 방법

### 터미널에서 확인 (GitHub CLI)

```bash
gh run list --limit 3
```

| 상태 | 의미 |
|------|------|
| `completed` `success` | 빌드 및 배포 성공 |
| `in_progress` | 빌드 진행 중 |
| `completed` `failure` | 빌드 실패 (로그 확인 필요) |

### 빌드 실패 시 로그 확인

```bash
# 최근 실행 ID 확인
gh run list --limit 1

# 로그 확인
gh run view {RUN_ID} --log
```

### 브라우저에서 확인

GitHub 저장소의 **Actions** 탭에서 각 빌드의 상태와 상세 로그 확인 가능.

---

## 5. 자주 발생하는 문제

| 문제 | 원인 | 해결 방법 |
|------|------|----------|
| push 시 인증 오류 | GitHub 인증 미설정 | `gh auth login` 실행 |
| 포스트가 사이트에 안 보임 | 포스트 날짜가 미래 | 날짜를 오늘 이전으로 수정 |
| 빌드 실패 | YAML 문법 오류 | front matter의 들여쓰기 및 콜론 확인 |
| CSS가 깨짐 | `_config.yml`의 `url`이 비어있음 | `url`에 블로그 주소 입력 |

> 포스트 날짜가 현재 시각보다 미래이면 Jekyll은 해당 포스트를 숨김. config에서의 시간대를 잘 확인할 것.
{: .prompt-danger }