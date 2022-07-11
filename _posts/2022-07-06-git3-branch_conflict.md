---
layout: single
title: "생활코딩-GIT CLI-Branch & Conflict"
---

## Branch
최초의 main branch를 여러 가지로 나눠서 필요에 의해 따로 작업 하다가, 서로 다른 브랜치를 merge하기도 하고 그 과정에서 겹치는 data의 내용이 동일하면 문제 없지만, 만약 두 브랜치의 겹치는 data의 같은 위치의 내용이 겹친다면 이 때, conflict 충돌이 일어남.

<img src="..\assets\images\2022-07-06-2217.excalidraw.svg">

- `git log --all --graph --oneline`: 로그의 모든 브랜치, 시각적으로 표현, 버전울 한 줄로 볼 수 있게
- `git branch`: 현재 브랜치 목록을 보여줌
- `git` + 브랜치 이름: 새로운 브랜치 만듬
- `HEAD`: 현재 속해있는 브랜치

<img src="..\assets\images\2022-07-06-2229.excalidraw.svg">

- `git checkout` + 브랜치 이름: 해당 브랜치 버전으로 변경

<img src="..\assets\images\2022-07-06-2240.excalidraw.svg">


## 병합 : 서로 다른 파일 병합
- 두 브랜치를 합쳐서 새로운 브랜치를 만듬
- base: 합치려고 하는 브랜치들의 공통조상
- merge commit: 병합된 커밋 

### 명령어
- `git commit --amend`: 커밋 이름 변경
- `git merge` + 병합하고 싶은 브랜치 이름: merge

### main branch에 o2 branch를 병합하고 싶다면?
우선 main branch가 된 후 병합하고 싶은 브랜치를 merge명령어로 지정
<img src="..\assets\images\2022-07-06-2319.excalidraw.svg">

## 병합 : 같은파일, 다른부분 병합
merge 시, 충돌 없이 합쳐짐

## 병합 : 같은파일, 같은부분 병합
- 충돌이 일어난 부분을 branch 이름을 경계로 보여줌.
- 이 부분을 수정하고 다시 working tree에 add하면 충돌이 고쳐졌다는 문구가 나옴.
- commit하면 끝

## 3 way merge

branch1|base|branch2|2 way merge|3 way merge
---|---|---|---|---
A|A|A|A|A
H|B|B|?|H
C|C|T|?|T
H|D|T|?|?

## checkout
- `checkout` + 브랜치이름: 그 브랜치의 최신 버전으로 감
- `checkout` + 버전이름: 해당 버전으로 감(detached)

## checkout vs reset
- checkout: HEAD가 해당 branch를 가리키게 함
- reset: 내 branch의 버전을 해당 branch가 가리키고 있는 버전으로 바꿈(기존 버전과의 연결은 끊기면서 삭제됨)