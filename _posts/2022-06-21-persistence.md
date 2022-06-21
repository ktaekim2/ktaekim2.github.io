---
layout: single
title: "영속성/SQL Mapper/ORM"
---

## 영속성(Persistence)?
- 데이터를 생성한 프로그램이 종료되더라도 사라지지 않는 데이터의 특성
- 영속성을 갖지 않는 데이터는 단지 메모리에서만 존재하기 때문에 프로그램을 종료하면 모두 잃어버리게 된다. 때문에 파일 시스템, 관계형 데이터베이스 혹은 객체 데이터베이스 등을 활용하여 데이터를 영구하게 저장하여 영속성을 부여한다.

### Persistence Layer
- 프로그램에의 아키텍처에서, 데이터에 영속성을 부여해주는 계층
- JDBC를 이용하여 직접 구현할 수 있지만 Persistence Framework를 이용한 개발이 많이 이루어진다.

### Persistence Framework
- JDBC 프로그래밍의 복잡함이나 번거로움 없이 간단한 작업만으로 데이터베이스와 연동되는 시스템을 빠르게 개발할 수 있으며 안정적인 구동을 보장한다.
- Persistence Framework는 SQL Mapper와 ORM으로 나눌 수 있다.

### SQL Mapper vs ORM
- ORM은 데이터베이스 객체를 자바 객체로 매핑함으로써 객체 간의 관계를 바탕으로 SQL을 자동으로 생성해주지만 SQL Mapper는 SQL을 명시해줘야 한다.
- ORM은 관계형 데이터베이스의 ‘관계’를 Object에 반영하자는 것이 목적이라면, SQL Mapper는 단순히 필드를 매핑시키는 것이 목적이라는 점에서 지향점의 차이가 있다

### SQL Mapper
SQL <—매핑—> Object 필드
- SQL Mapper는 SQL 문장으로 직접 데이터베이스 데이터를 다룬다.
- 즉, SQL Mapper는 SQL을 명시해줘야 한다.
- Ex) Mybatis, JdbcTempletes 등

## ORM
Object Relational Mapping, 객체-관계 매핑
- 객체와 관계형 데이터베이스의 데이터를 자동으로 매핑(연결)해주는 것
  - 객체 지향 프로그래밍은 클래스를 사용하고, 관계형 데이터베이스는 테이블을 사용한다.
  - 객체 모델과 관계형 모델 간에 불일치가 존재한다.
  - ORM을 통해 객체 간의 관계를 바탕으로 SQL을 자동으로 생성하여 불일치를 해결한다.
  - ex) Entity에서 @Column String testColumn2를 선언해도 test_column2로 변환되어 DB에 저장됨
- 데이터베이스 데이터 <-매핑-> Object 필드
  - 객체를 통해 간접적으로 데이터베이스 데이터를 다룬다.
- Persistant API 라고도 할 수 있다.
  - ex) JPA, Hibernate 등

## JDBC(Java Database Connectivity)
- JDBC는 DB에 접근할 수 있도록 Java에서 제공하는 API이다.
- 모든 Java의 Data Access 기술의 근간
- 즉, 모든 Persistence Framework는 내부적으로 JDBC API를 이용한다.
- JDBC는 데이터베이스에서 자료를 쿼리하거나 업데이트하는 방법을 제공한다.

## JPA(Java Persistent API)


출처: https://gmlwjd9405.github.io/