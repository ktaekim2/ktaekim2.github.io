---
layout: single
title: "생활코딩-git2-버전관리"
---

깃헙데스크탑 같은 GUI(Graphical User Interface)를 사용해도 좋지만, 이런 프로그램들은 모든 기능을 사용할 수 없으므로, 프로그래머들은 CLI(Command Line Interface)를 통해 git을 사용하는 방법을 알아야 한다.

### Working tree

- 버전으로 만들어지기 전 단계
- 수정한 파일들

### Staging Area

- 버전으로 만드려는 파일들

### Repository

- 버전이 저장되어 있는 곳.
- .git 디렉토리

## git bash

powershell과 비슷한데 명령어가 조금 다르다. 하지만 git관련 명령어는 같은듯?

- ~: 현재 디렉토리 표시
- cd [디렉토리명]: 특정 디렉토리로 이동.
- ls -al: 디렉토리 내 목록 표시.(파워셀의 dir)
- nano [파일명.확장자]: 파일을 만듬, 이미 있다면 수정.
- cat [파일명.확장자]: 파일 내용을 보여줌.

## git 명령어

- git init: 현재 디렉토리를 버전관리 시작
- git add [파일명.확장자]: 파일을 stage에 올림
- git add .: 해당 디렉토리 전부 올림.
- git commit -m "[commit이름]": 커밋함.
- git add -am "[commit이름]": 해당 디렉토리 전부 add와 동시에 커밋함. 단, 처음 만들어진 파일인 untracked상태의 파일은 add가 안됨. untracked파일을 실수로 추적하는 실수를 방지하기 위함.
- git commit: default git editor로 commit을 편집함.
- git config \--global core.editor "nano": nano편집기를 default로 바꿈
- git log: 로그를 보여줌.
- git log \--stat: 로그의 각각 커밋마다 파일들이 변화가 있었는지 보여줌.
- git diff: 마지막 version과 working tree사이의 차이점을 파악.
- git reset \--hard: 작업한 내용 리셋
- git log -p: log \--stat보다 더 디테일한 정보를 보여줌. 어떤 글이 추가되고 삭제되었는지.
- git [알고싶은 명령어] \--help: 그 명령어에 대한 도움말 열기

<img src="..\assets\images\Untitled-2022-04-24-1745.svg">
<img src="..\assets\images\Untitled-2022-04-24-1820.svg">

## checkout

- git checkout [commit id]: 커밋 id의 버전으로 돌아감

<img src="..\assets\images\Untitled-2022-04-24-1929.svg">

- git checkout [branch]: [branch]로 돌아감. main을 써서 최신상태로 돌아감

## git reset

- git reset \--hard [commit id]: commit id의 버전으로 리셋하겠다(그 버전이 되겠다)
- 협업 시 다른 사람과 이미 공유된 버전에 대해서는 리셋하면 안됨.
- 공유되기 전 단계에서만 리셋해야 함.
- checkout은 잠시 그 버전으로 돌아가는 거라면 이것은 내 main branch를 영구적으로 해당 commit으로 바꾸는 것임.

## git revert(되돌리기)

 - 기존의 커밋을 보존하면서 그 이전 단계로 돌아가는 명령어

<img src="..\assets\images\Untitled-2022-04-24-2030.svg">

- 만약 훨씬 전의 단계로 돌아가고 싶다면 순서대로 revert해야 함. 단계를 건너뛰고 revert를 하게 되면 충돌 일어남.