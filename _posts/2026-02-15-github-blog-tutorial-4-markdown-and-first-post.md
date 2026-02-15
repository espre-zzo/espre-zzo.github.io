---
title: "GitHub 블로그 만들기 (4) - Markdown 문법"
date: 2026-02-15 14:30:00 +0900
categories: [Blog, github_blog]
tags: [github_blog, jekyll, markdown]
---

포스트를 올리기 위해서는 Markdown 형식의 파일을 제작해야함. 이 글에서는 post를 만드는데 유용했던 Markdown을 작성하는 방법 (Markdown 문법)을 정리해보려고 함.

---

## 1. 규칙

포스트를 올리기 위해서는 반드시 `_posts/` 폴더에 다음 형식으로 파일을 생성 및 저장해야함:

```
_posts/YYYY-MM-DD-title.md
```

> 파일명에는 **영문, 숫자, 하이픈(`-`)**만 사용할 것. 한글이나 공백은 URL 문제를 일으킬 수 있음. (프로그래밍에서 한글은 꾸러기임)
{: .prompt-warning }

---

## 2. Front Matter

일기장처럼, 모든 포스트 파일의 맨 위에는 **front matter**가 필요함. `---`로 감싸진 YAML 형식의 메타 정보 (글에 대한 내용)임:

```yaml
---
title: "포스트 제목"
date: 2026-02-15 14:00:00 +0900
categories: [대분류, 소분류]
tags: [태그1, 태그2, 태그3]
---
```

### 주요 옵션

| 옵션 | 설명 | 예시 |
|------|------|------|
| `title` | 포스트 제목 (한글 가능) | `"Python 기초 문법"` |
| `date` | 작성 날짜/시간 | `2026-02-15 14:00:00 +0900` |
| `categories` | 카테고리 (최대 2단계) | `[Programming, Python]` |
| `tags` | 태그 (여러 개 가능) | `[python, beginner]` |
| `pin` | 메인에 고정 | `true` |
| `toc` | 목차 표시 (기본 true) | `false` |
| `math` | 수식 사용 | `true` |

> `date`의 시간대(`+0900`)는 `_config.yml`의 `timezone`과 맞춰주는 것이 좋음.
{: .prompt-tip }

---

## 3. Markdown 기본 문법

### 3.1. 제목 (Headings)

```markdown
## 대제목 (H2)
### 중제목 (H3)
#### 소제목 (H4)
```

> 포스트 내에서는 `##`(H2)부터 시작할 것. `#`(H1)은 포스트 제목에 이미 사용되었음.
{: .prompt-tip }

### 3.2. 텍스트 스타일

```markdown
**굵게 (Bold)**
*기울임 (Italic)*
~~취소선 (Strikethrough)~~
`인라인 코드 (Inline Code)`
```

결과: **굵게**, *기울임*, ~~취소선~~, `인라인 코드`

### 3.3. 리스트

**순서 없는 리스트:**

```markdown
- 항목 1
- 항목 2
  - 하위 항목
  - 하위 항목
```

**순서 있는 리스트:**

```markdown
1. 첫 번째
2. 두 번째
3. 세 번째
```

### 3.4. 링크와 이미지

```markdown
[링크 텍스트](https://example.com)
![이미지 설명](/assets/img/photo.jpg)
```

> 사용할 이미지는 `assets/img/` 폴더에 저장하고, 경로를 `/assets/img/파일명`으로 지정.
{: .prompt-info }

### 3.5. 코드 블록

언어를 지정하면 **구문 강조(Syntax Highlighting)**가 적용됩니다:

````markdown
```python
def hello():
    print("Hello, World!")
```
````

````markdown
```bash
sudo apt update
```
````

### 3.6. 표 (Table)

```markdown
| 이름 | 나이 | 직업 |
|------|------|------|
| 홍길동 | 25 | 개발자 |
| 김철수 | 30 | 디자이너 |
```

결과:

| 이름 | 나이 | 직업 |
|------|------|------|
| 홍길동 | 25 | 개발자 |
| 김철수 | 30 | 디자이너 |

### 3.7. 인용문

```markdown
> 인용문
```

> 인용문

### 3.8. 구분선

```markdown
---
```

---

## 4. Chirpy 전용 문법

Chirpy 테마에서만 사용할 수 있는 특별한 프롬프트 블록:

### 팁 (Tip)

```markdown
> 팁
{: .prompt-tip }
```

> 팁
{: .prompt-tip }

### 정보 (Info)

```markdown
> 정보
{: .prompt-info }
```

> 정보
{: .prompt-info }

### 경고 (Warning)

```markdown
> 주의
{: .prompt-warning }
```

> 주의
{: .prompt-warning }

### 위험 (Danger)

```markdown
> 경고
{: .prompt-danger }
```

> 경고
{: .prompt-danger }

---

## 5. 편집 도구 추천

| 도구 | 특징 | 가격 |
|------|------|------|
| **VS Code** | Markdown 미리보기 지원, 확장 프로그램 풍부 | 무료 |
| **Obsidian** | 로컬 파일 기반, 실시간 미리보기 | 무료 |
| **nano** | 터미널 기본 에디터, 간단한 편집에 적합 | 무료 |

> VS Code를 사용한다면 `Cmd + K V`로 편집 화면과 미리보기를 나란히 볼 수 있는 장점이 있음.
{: .prompt-tip }