---
title: "Unix Terminal 기초 - 터미널 명령어 가이드"
date: 2026-02-11 10:00:00 +0900
categories: [Programming, Terminal]
tags: [unix, terminal, linux, bash, beginner]
---

## Terminal Terminology

터미널에서 작업할 때 기본 용어를 알아두면 문제를 설명하거나 도움을 요청할 때 유용합니다.

![terminal_intro.jpg](https://github.com/zzo-joe/ProbieGeek/assets/82928940/b3ddd321-a7dd-4821-ada9-8b8d3dddb994)

- **Prompt**: 명령어를 입력하는 위치 옆의 텍스트. 호스트명이나 현재 폴더 위치 등의 정보를 포함할 수 있습니다.
- **Command**: 실행하려는 함수 또는 스크립트
- **Argument**: 출력을 변경하기 위해 명령어에 추가하는 인자. 명령어와 인자 사이에는 항상 공백이 있습니다.
- **Standard Out**: 명령어의 결과 출력
- **Standard Error**: 실패한 명령어의 에러 출력

---

## Getting Started

터미널을 열고 기본적인 파일/디렉토리 탐색을 시작해봅시다.

| *Command* | *Function*              | *Syntax/example usage*               |
|-----------|-------------------------|--------------------------------------|
| `mkdir`   | make directory          | `mkdir` DIRECTORY                    |
| `pwd`     | print working directory | `pwd`                                |
| `cd`      | change directory        | `cd` ~ 또는 `cd` (홈 디렉토리)        |
|           |                         | `cd` .. (상위 디렉토리)               |
| `ls`      | list contents           | `ls` [OPTIONS] DIRECTORY             |

### mkdir - Make Directory Command

새로운 디렉토리(폴더)를 만들어 봅시다. `mkdir`(**m**a**k**e **dir**ectory) 명령어를 사용합니다.

```bash
mkdir unixTutorial
```

> Unix에서는 디렉토리 이름에 공백을 넣지 않는 것이 좋습니다. 대신 언더스코어(`_`)를 사용하세요.
{: .prompt-tip }

이미 존재하는 디렉토리를 다시 만들려고 하면 에러가 발생합니다:

```bash
$ mkdir unixTutorial
mkdir: cannot create directory 'unixTutorial': File exists
```

### pwd - Path of Working Directory

현재 위치를 확인하는 명령어입니다.

```bash
$ pwd
/Users/daeho
```

`User` 디렉토리 안의 `daeho`라는 디렉토리에 있다는 뜻입니다.

### cd - Change Directory

위에서 만든 `unixTutorial` 디렉토리로 이동해봅시다.

```bash
$ cd unixTutorial
$ pwd
/Users/daeho/unixTutorial
```

상위 디렉토리로 돌아가려면:

```bash
$ cd ..
```

> 현재 디렉토리는 `.`(dot), 상위 디렉토리는 `..`(dot dot)으로 표현됩니다.
{: .prompt-info }

> 디렉토리 이름의 처음 몇 글자를 입력한 후 `Tab` 키를 누르면 자동 완성됩니다!
{: .prompt-tip }

---

## File Creation and Editing

### ls (list) command

디렉토리의 내용을 확인하는 명령어입니다.

```bash
$ ls
$ ls .
$ ls DIRECTORY
```

### nano - GUI와 유사한 텍스트 에디터

| *Command*    | *Function*              |
|--------------|-------------------------|
| `ctrl+o`     | 파일 저장               |
| `ctrl+x`     | 파일 닫기               |
| `ctrl+/`     | 파일 끝으로 이동         |
| `ctrl+a`     | 줄의 시작으로 이동       |
| `ctrl+e`     | 줄의 끝으로 이동         |
| `ctrl+c`     | 줄 번호 표시             |
| `ctrl+_`     | 특정 줄 번호로 이동      |
| `ctrl+w`     | 단어 검색               |
| `alt+w`      | 다음 검색 결과           |
| `ctrl+\`     | 찾기 및 바꾸기           |

nano로 파일을 만들어봅시다:

```bash
$ nano myFirstfile.txt
```

텍스트를 붙여넣은 후 `Ctrl+X` → `Y` → `Enter`로 저장합니다.

### VIM

`vim`은 더 강력한 텍스트 에디터이지만, 처음에는 다소 어려울 수 있습니다.

| Vim      | Very Basic Usage                                            |
|----------|-------------------------------------------------------------|
| `esc`    | 원래 상태로 돌아가기                                          |
| `i`      | insert 모드: 텍스트 추가/편집 가능                            |
| `esc:wq` | escape 후 `:wq` 입력하면 저장 후 종료                        |

---

## Viewing File Contents

> 파일 내용을 확인하는 명령어는 이렇게 외우세요: **A `cat` has a `head` and a `tail`, `more` or `less`**
{: .prompt-tip }

| Command | Function                       | Usage      |
|---------|--------------------------------|------------|
| `cat`   | 파일 내용 전체 출력              | `cat` FILE |
| `head`  | 파일의 처음 몇 줄 출력           | `head` FILE |
| `tail`  | 파일의 마지막 몇 줄 출력         | `tail` FILE |
| `more`  | 파일 보기 (제한된 옵션)          | `more` FILE |
| `less`  | 파일 보기 (더 많은 옵션)         | `less` FILE |

### head & tail 사용 예시

```bash
$ head numSeq.txt        # 처음 10줄
$ head -n 5 numSeq.txt   # 처음 5줄
$ tail numSeq.txt        # 마지막 10줄
$ tail -n 5 numSeq.txt   # 마지막 5줄
```

### less 네비게이션

| Command            | Function        |
|--------------------|-----------------|
| `q`                | 종료            |
| `u`                | 한 화면 위로     |
| `d` or `space bar` | 한 화면 아래로   |
| `g`NUM             | NUM번 줄로 이동  |

---

## Renaming, Copying and Deleting

| Command | Function               | Syntax                 |
|---------|------------------------|------------------------|
| `touch` | 빈 파일 생성            | `touch` FILE           |
| `mkdir` | 디렉토리 생성           | `mkdir` DIRECTORY      |
| `rmdir` | 빈 디렉토리 삭제        | `rmdir` DIRECTORY      |
| `rm`    | 파일 삭제               | `rm` FILE              |
| `cp`    | 파일/디렉토리 복사       | `cp` SOURCE DESTINATION|
| `mv`    | 파일/디렉토리 이동/이름변경 | `mv` SOURCE DESTINATION|

> Unix에는 **되돌리기(undo) 기능이 없습니다**. 파일을 삭제하면 복구할 수 없으니 주의하세요!
{: .prompt-danger }

### touch - 빈 파일 생성

```bash
$ touch AA.txt BB.txt CC.txt
$ touch 1.txt 2.txt 3.txt
$ ls
1.txt 2.txt 3.txt AA.txt BB.txt CC.txt
```

### mv - 파일 이동 및 이름 변경

```bash
# 파일 이동
$ mv 1.txt 2.txt 3.txt Numbers

# 파일 이름 변경
$ mv GG.txt FF.txt
```

### cp - 파일 복사

```bash
$ cp myFirstFile.txt mySecondfile.txt

# 폴더 복사 (-r 옵션 필요)
$ cp -r Letters Letters_copy
```

### rm - 파일/디렉토리 삭제

```bash
$ rm file.txt           # 파일 삭제
$ rm -r Deleteme        # 디렉토리와 내용물 전체 삭제
```

---

## Counting, Sorting and Redirecting Output

이 부분부터 Unix의 진정한 강력함이 발휘됩니다. 명령어들을 조합하여 복잡한 작업을 수행할 수 있습니다.

### Pipes & Redirects

| Command          | Function                    |
|------------------|-----------------------------|
| cmd `<` file     | 파일을 입력으로 사용          |
| cmd `>` file     | 출력을 파일에 쓰기 (덮어쓰기) |
| cmd `>>` file    | 출력을 파일에 추가            |
| cmd1 `\|` cmd2   | cmd1의 출력을 cmd2로 전달    |

### wc - word count

```bash
$ wc numSeq.txt       # 줄 수, 단어 수, 문자 수
$ wc -l numSeq.txt    # 줄 수만
$ wc -w numSeq.txt    # 단어 수만
$ wc -c numSeq.txt    # 문자 수만
```

### tr - translate

문자를 다른 문자로 변환합니다.

```bash
$ tr '\n' ',' < numSeq.txt
1,2,3,4,5,6,7,...,100,
```

### sort & uniq

```bash
# 일반 정렬
$ sort numSeqRandom.txt | head

# 숫자 정렬 (-n)
$ sort -n numSeqRandom.txt | head

# 과학적 표기법 포함 정렬 (-g)
$ sort -g numSeqRandom.txt | head

# 정렬 후 중복 제거
$ sort -g numSeqRandom.txt | uniq | wc -l

# 중복 횟수 세기
$ sort -g numSeqRandom.txt | uniq -c | head
```

### 실전 예제: 가장 많이 사용된 단어 찾기

명령어를 하나씩 추가하며 파이프라인을 구성하는 방법입니다:

```bash
$ cat myFirstfile.txt | tr ' ' '\n' | sort | uniq -c | sort -n | tail -n 4
  4 is
  4 space
  5 the
  5 to
```

> Unix의 긴 한 줄 명령(one-liner)은 이렇게 하나씩 쌓아가며 만듭니다. 각 단계의 출력을 확인하면서 원하는 결과가 나올 때까지 조정하세요.
{: .prompt-info }

---

## Find and Replace

### grep - global regular expression print

파일에서 패턴을 검색합니다.

```bash
$ grep "space" myFirstFile.txt         # 해당 단어가 포함된 줄 출력
$ grep -o "space" myFirstFile.txt      # 매칭된 단어만 출력
$ grep -c "space" myFirstFile.txt      # 매칭된 줄 수 출력
$ grep -i "space" myFirstFile.txt      # 대소문자 무시
$ grep -v "space" myFirstFile.txt      # 매칭되지 않는 줄 출력
$ grep -n "space" myFirstFile.txt      # 줄 번호와 함께 출력
```

### history - 명령어 히스토리

이전에 사용한 명령어를 검색할 수 있습니다:

```bash
$ history | grep myFirstFile | tail -n 5
$ history | grep -i myfirst | grep -v space | grep uniq
```

> `↑` / `↓` 화살표 키로도 이전 명령어를 불러올 수 있습니다.
{: .prompt-tip }

### Wildcards: `?`와 `*`

- `*` : 여러 문자를 대체
- `?` : 한 문자를 대체

```bash
$ ls ?.txt     # 한 글자 이름의 txt 파일 (1.txt, 2.txt ...)
$ ls ??.txt    # 두 글자 이름의 txt 파일 (AA.txt, BB.txt ...)
$ mv ??.txt Letters
$ mv ?.txt Numbers
```

### sed - stream editor

패턴을 찾아 치환할 때 사용합니다:

```bash
# 기본 사용법
$ sed 's/galaxies/universes/g' myFirstfile.txt

# 새 파일로 저장
$ sed 's/galaxies/universes/g' myFirstfile.txt > myFirstfile2.txt

# 파일 직접 수정 (-i)
$ sed -i 's/galaxies/universes/g' myFirstfile.txt
```

---

## The Unix Manual and Soft Links

### man - manual

명령어의 설명과 사용 가능한 매개변수를 확인할 수 있습니다. `q`를 눌러 종료합니다.

```bash
$ man ls
$ man cat
```

### ln - symbolic link

심볼릭 링크는 파일을 복사하지 않고도 여러 위치에서 같은 파일에 접근할 수 있게 해줍니다.

```bash
$ ln -s ../myFirstFile.txt
$ ls -l
```

> `rm`으로 심볼릭 링크를 삭제해도 원본 파일에는 영향을 주지 않습니다.
{: .prompt-info }

---

## Further Reading

- [Summary of Unix commands](https://bioinformaticsworkbook.org/Appendix/Unix/UnixCheatSheet.html#gsc.tab=0)
