---
title: "Transfer data via rclone to google drive"
date: 2026-03-31
categories: [Programming, Ubuntu Server]
tags: [ubuntu, rclone, google-drive, shared-drive]
---

## Overview

 rclone으로 연결된 폴더에 `mv`, `cp` 등의 명령어로 파일을 옮기니 연결도 느리고, 진행 상황을 알 수 없어서 답답했는데, 찾아보니 rclone 기본 명령어로 파일을 옮길 수 있었다.

## Usage

-P (또는 --progress) 옵션을 추가하면 진행상황을 확인할 수 있다.

``` shell
rclone copy [원본_폴더_경로] [리모트_이름]:[목적지_폴더_경로] -P
```

구글 드라이브의 특성에 맞춰 전송 속도를 끌어올릴 수 있는 최적화 명령어라고 한다 (얼마나 최적화가 되는지 모르겠지만, 인터넷이 짱짱하다면 사용해보는걸로).

``` shell
rclone copy /home/user/data gdrive:backup -P --transfers 8 --drive-chunk-size 64M
```

## Option description

- `-P` (또는 `--progress`): 실시간 전송 속도, 진행률(%), 남은 시간(ETA), 전송된 데이터 양을 화면에 표기해줌

- `--transfers 8`: 동시에 전송할 파일의 개수를 지정. 기본값은 4개. 역시나 인터넷이 짱짱하다면 늘려서 전송하면 정신건강에 이롭다.

- `--drive-chunk-size 64M`: 구글 드라이브로 업로드할 때 데이터를 나누는 청크(Chunk) 크기. 기본값은 8M. 용량이 큰 파일을 전송할 때 이 값을 32M, 64M, 128M 등으로 늘려주면 업로드 속도가 눈에 띄게 빨라진다고 한다 (단, RAM 사용량도 함께 증가함). 