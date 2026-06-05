---
title: "Supabase Auth 핵심 개념 정리 — 주니어 개발자를 위한 가이드"
date: 2026-06-05 14:00:00 +0900
categories: [Backend, Auth]
tags: [supabase, jwt, nextjs, rls, postgresql]
---

## 1. 세션 기반 로그인 vs 토큰(JWT) 기반 로그인

과거에는 서버 메모리에 로그인 상태를 저장하는 **세션(Session)** 방식을 많이 썼지만, Supabase를 포함한 현대 API 기반 서비스들은 대부분 **토큰(JWT)** 방식을 사용한다.

### 핵심 키워드

- **JWT (JSON Web Token)**: 사용자 정보를 담은 암호화된 토큰
- **Access Token**: 로그인 후 발급되는 '입장권'. 유효기간이 짧다
- **Refresh Token**: Access Token이 만료되었을 때 자동 재발급에 사용

### 흐름 요약

```
사용자 로그인
  → Access Token + Refresh Token 발급
  → API 요청 시 Access Token을 헤더에 포함
  → Access Token 만료 시 Refresh Token으로 재발급
  → Refresh Token까지 만료되면 재로그인
```

> Access Token의 유효기간이 짧은 이유: 토큰이 탈취되더라도 피해를 최소화하기 위한 보안 설계다.
{: .prompt-info }

---

## 2. Next.js SSR과 Supabase 인증의 관계

Next.js에서 Supabase Auth를 쓸 때 **가장 헷갈리는 부분**이다.

### Client Component (브라우저)

- `supabase.auth.signInWithPassword()` 호출하면 끝
- 토큰이 브라우저의 **LocalStorage**에 자동 저장됨
- 비교적 단순하다

### Server Component (서버)

- 서버에서는 브라우저의 LocalStorage를 **읽을 수 없다**
- 따라서 토큰을 **쿠키(Cookie)**에 심어서 서버와 클라이언트가 공유해야 한다
- Supabase의 `@supabase/ssr` 패키지가 이 역할을 해준다

### 쿠키 기반 SSR 인증 흐름

```
[브라우저] 로그인 → 토큰을 쿠키에 저장
                ↓
[서버] 요청 수신 → 쿠키에서 토큰 읽기 → 유저 확인
                ↓
[Middleware] 토큰 유효? → 보호된 페이지 접근 허용
             토큰 없음? → 로그인 페이지로 리다이렉트
```

> 이 개념을 이해해야 Next.js에서 **로그인한 유저만 접근 가능한 페이지**를 Middleware로 보호할 수 있다.
{: .prompt-warning }

---

## 3. PostgreSQL과 RLS (Row Level Security)

Supabase는 내부적으로 **PostgreSQL**을 사용한다. 로그인 처리를 넘어, 유저가 **자기 데이터만** 보고 수정하도록 제어해야 한다.

### 핵심 키워드

- **외래키 (Foreign Key)**: 테이블 간의 관계를 정의하는 제약
- **RLS (Row Level Security)**: 행(row) 단위로 접근 권한을 제어하는 PostgreSQL 기능

### 데이터 연결 구조

```
auth.users 테이블          my_data 테이블
┌──────────────┐          ┌──────────────────┐
│ id (uid)     │◄────────│ user_id (FK)      │
│ email        │          │ content           │
│ ...          │          │ created_at        │
└──────────────┘          └──────────────────┘
```

### RLS 정책 예시

```sql
-- 로그인한 유저가 자신의 데이터만 조회 가능
CREATE POLICY "Users can view own data"
  ON my_data FOR SELECT
  USING (auth.uid() = user_id);

-- 로그인한 유저가 자신의 데이터만 수정 가능
CREATE POLICY "Users can update own data"
  ON my_data FOR UPDATE
  USING (auth.uid() = user_id);
```

> RLS는 **백엔드 코딩 없이** DB 레벨에서 보안을 유지하는 Supabase의 핵심 기능이다. 공식 문서 정독을 추천한다.
{: .prompt-tip }

---

## 추천 학습 순서

1. **Supabase 공식 문서**의 "Next.js Quickstart" 따라하기 — `@supabase/ssr` 패키지 활용법이 코드로 나와 있다
2. **유튜브**에서 "Next.js Supabase Auth" 검색 — 30분~1시간짜리 튜토리얼을 배속으로 보며 흐름을 눈으로 익히기
3. **실전 적용** — 개발 서버에서 직접 로그인/로그아웃 구현해보기

> 처음부터 두꺼운 데이터베이스 책을 볼 필요는 없다. 실전형으로 부딪혀보자.
{: .prompt-tip }
