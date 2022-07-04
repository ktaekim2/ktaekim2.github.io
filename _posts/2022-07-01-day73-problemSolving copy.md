---
layout: single
title: "73일차-스프링부트(10)-MemberBoard(3)-문제해결"
---

# update쿼리가 나가면 delete쿼리도 같이 나가는 문제
## 문제
- 회원 정보를 수정하면, 그 회원이 쓴 모든 글이 DB에서 사라짐.
- 게시글을 수정하면, 해당 게시글의 모든 댓글이 DB에서 사라짐(다른 게시글의 내 댓글은 안사라짐).

## 원인
Entity에서 연관관계를 맺는 컬럼 코드에서 on delete cascade 설정을 하면 해당 문제가 발생함.   
`cascade = CascadeType.ALL, orphanRemoval = true`   
부모객체에 변경이 발생하게 되면 자식은 연관관계가 끊어진 것으로 간주하여 고아객체(orphan)로 인식하고 삭제를 해버리는 것.  

## 해결1
부모가 삭제됐을 때만 자식이 삭제 되도록 설정(부모 삭제시 자식도 삭제)

### MemberEntity.java
```java
@OneToMany(mappedBy = "memberEntity", cascade = CascadeType.REMOVE, orphanRemoval = true, fetch = FetchType.LAZY)
private List<BoardEntity> boardEntityList = new ArrayList<>();

@OneToMany(mappedBy = "memberEntity", cascade = CascadeType.REMOVE, orphanRemoval = true, fetch = FetchType.LAZY)
private List<CommentEntity> commentEntityList = new ArrayList<>();
```

### BoardEntity.java
```java
@OneToMany(mappedBy = "boardEntity", cascade = CascadeType.REMOVE, orphanRemoval = true, fetch = FetchType.LAZY)
private List<CommentEntity> commentEntityList = new ArrayList<>();
```

## 해결2

### MemberEntity.java
부모 삭제시 자식의 부모값은 null   
```java
@OneToMany(mappedBy = "memberEntity", cascade = CascadeType.PERSIST, orphanRemoval = false, fetch = FetchType.LAZY)
private List<BoardEntity> boardEntityList = new ArrayList<>();

@OneToMany(mappedBy = "memberEntity", cascade = CascadeType.PERSIST, orphanRemoval = false, fetch = FetchType.LAZY)
private List<CommentEntity> commentEntityList = new ArrayList<>();

@PreRemove
private void preRemove() {
    boardEntityList.forEach(board -> board.setMemberEntity(null));
    commentEntityList.forEach(board -> board.setMemberEntity(null));
}
```

### BoardEntity.java
BoardEntity-CommentEntity 관계는 게시글 삭제시 댓글은 무조건 삭제   
```java
@OneToMany(mappedBy = "boardEntity", cascade = CascadeType.REMOVE, orphanRemoval = true, fetch = FetchType.LAZY)
private List<CommentEntity> commentEntityList = new ArrayList<>();
```



