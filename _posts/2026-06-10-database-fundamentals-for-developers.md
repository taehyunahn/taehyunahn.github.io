---
title: "개발자를 위한 데이터베이스 기초 — 어디서부터 시작할까?"
date: 2026-06-10 14:00:00 +0900
categories: [Backend, Database]
tags: [database, sql, postgresql, nosql]
---

## 데이터베이스가 왜 중요한가

어떤 앱을 만들든 데이터를 **저장하고, 꺼내고, 수정하고, 삭제**하는 작업은 반드시 필요하다. 이 네 가지를 **CRUD**(Create, Read, Update, Delete)라 부르며, 데이터베이스는 이 CRUD의 기반이 된다.

프론트엔드만 하더라도 API를 통해 DB와 소통하게 되므로, DB의 기본 구조를 이해하면 디버깅이 빨라지고 설계 판단력이 생긴다.

---

## 관계형 데이터베이스(RDB) vs NoSQL

### 관계형 데이터베이스 (RDB)

데이터를 **테이블(표)** 형태로 저장한다. 행(row)과 열(column)로 구성되며, 테이블 간의 **관계(Relation)**를 정의할 수 있다.

```
users 테이블
┌────┬──────────┬─────────────────┐
│ id │ name     │ email           │
├────┼──────────┼─────────────────┤
│ 1  │ Taehy    │ dev@example.com │
│ 2  │ Alice    │ alice@test.com  │
└────┴──────────┴─────────────────┘
```

대표적인 RDB: **PostgreSQL**, MySQL, SQLite

### NoSQL

테이블 대신 **문서(Document)**, **키-값(Key-Value)** 등 유연한 형태로 저장한다. 스키마가 자유롭다.

```json
{
  "id": "1",
  "name": "Taehy",
  "email": "dev@example.com",
  "skills": ["React", "Next.js"]
}
```

대표적인 NoSQL: **MongoDB**, Redis, Firebase Firestore

### 어떤 걸 먼저 배워야 할까?

**RDB(PostgreSQL)부터 시작하는 것을 추천한다.** 이유는 간단하다:

- SQL 문법은 어떤 RDB에서든 거의 동일하게 쓰인다
- 데이터 관계, 정규화, 제약조건 등 핵심 개념을 체계적으로 배울 수 있다
- Supabase, AWS RDS 등 실무에서 가장 많이 쓰는 서비스가 RDB 기반이다

> NoSQL은 RDB의 기본기를 익힌 후 필요할 때 배워도 늦지 않다.
{: .prompt-tip }

---

## SQL 핵심 문법 5가지

SQL(Structured Query Language)은 데이터베이스와 대화하는 언어다. 아래 5가지만 알면 대부분의 기본 작업이 가능하다.

### 1. SELECT — 데이터 조회

```sql
-- 전체 조회
SELECT * FROM users;

-- 특정 컬럼만 조회
SELECT name, email FROM users;

-- 조건부 조회
SELECT * FROM users WHERE name = 'Taehy';
```

### 2. INSERT — 데이터 추가

```sql
INSERT INTO users (name, email)
VALUES ('Bob', 'bob@example.com');
```

### 3. UPDATE — 데이터 수정

```sql
UPDATE users
SET email = 'newemail@example.com'
WHERE id = 1;
```

> `WHERE` 없이 UPDATE를 실행하면 **모든 행이 수정**된다. 항상 WHERE를 확인하자.
{: .prompt-warning }

### 4. DELETE — 데이터 삭제

```sql
DELETE FROM users WHERE id = 2;
```

### 5. JOIN — 테이블 연결

```sql
SELECT users.name, posts.title
FROM users
JOIN posts ON users.id = posts.user_id;
```

두 테이블을 **공통 키(외래키)**로 연결해서 한 번에 조회한다. 이것이 "관계형"의 핵심이다.

---

## 반드시 알아야 할 핵심 개념

### 기본키 (Primary Key)

각 행을 **고유하게 식별**하는 컬럼. 보통 `id`를 사용한다.

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL
);
```

### 외래키 (Foreign Key)

다른 테이블의 기본키를 참조하여 **테이블 간 관계**를 만든다.

```sql
CREATE TABLE posts (
  id SERIAL PRIMARY KEY,
  title VARCHAR(200),
  user_id INTEGER REFERENCES users(id)
);
```

```
users                          posts
┌────┬────────┐               ┌────┬────────────┬─────────┐
│ id │ name   │               │ id │ title      │ user_id │
├────┼────────┤               ├────┼────────────┼─────────┤
│ 1  │ Taehy  │◄─────────────│ 1  │ 첫 번째 글  │ 1       │
│ 2  │ Alice  │◄─────────────│ 2  │ 두 번째 글  │ 2       │
└────┴────────┘               │ 3  │ 세 번째 글  │ 1       │
                               └────┴────────────┴─────────┘
```

### 인덱스 (Index)

자주 검색하는 컬럼에 인덱스를 걸면 조회 속도가 빨라진다. 책의 목차와 같은 역할이다.

```sql
CREATE INDEX idx_users_email ON users(email);
```

### 트랜잭션 (Transaction)

여러 SQL을 **하나의 작업 단위**로 묶는다. 중간에 실패하면 전부 취소(ROLLBACK)된다.

```sql
BEGIN;
  UPDATE accounts SET balance = balance - 10000 WHERE id = 1;
  UPDATE accounts SET balance = balance + 10000 WHERE id = 2;
COMMIT;
```

> 은행 송금을 생각하면 된다. 출금은 됐는데 입금이 실패하면 안 되니까, 둘 다 성공하거나 둘 다 취소되어야 한다.
{: .prompt-info }

---

## 추천 학습 순서

1. **SQL 기본 문법 익히기** — [SQLBolt](https://sqlbolt.com/) 에서 브라우저로 바로 연습 가능
2. **PostgreSQL 설치 & 실습** — Supabase 대시보드의 SQL Editor를 활용하면 설치 없이 실습 가능
3. **테이블 설계 연습** — 간단한 블로그(users, posts, comments)를 직접 설계해보기
4. **ORM 맛보기** — Prisma 등의 ORM으로 코드에서 DB를 다루는 방법 체험

> 처음부터 완벽한 설계를 하려 하지 말고, 만들어보고 → 부족한 점을 느끼고 → 개선하는 사이클이 가장 빠르다.
{: .prompt-tip }
