# 블로그 사용 가이드

## 글 올리는 법

### 1단계: 글 쓰기

`_posts/` 폴더에 마크다운 파일을 만든다.

**파일명 규칙**: `YYYY-MM-DD-제목.md`

```
_posts/
  └── 2026-06-05-supabase-auth-core-concepts.md
  └── 2026-06-10-my-new-post.md
```

**파일 내용**:

```markdown
---
title: "글 제목"
date: 2026-06-10 14:00:00 +0900
categories: [Backend, Database]
tags: [supabase, postgresql]
---

여기부터 마크다운으로 본문 작성.

## 소제목

- 리스트
- **굵은 글씨**
```

### 2단계: 푸시

```bash
git add _posts/2026-06-10-my-new-post.md
git commit -m "새 글 추가"
git push
```

push하면 GitHub Actions가 자동으로 빌드 → 배포한다. 1~2분 후 사이트에 반영.

---

## 카테고리 & 태그 규칙

카테고리는 `[상위, 하위]` 2단계까지 지원. 태그는 소문자, 개수 제한 없음.

| 상위 카테고리 | 하위 예시 | 용도 |
|---|---|---|
| Frontend | React, NextJS | UI, 컴포넌트, SSR/CSR |
| Backend | Auth, Database, API | 인증, DB, 서버 로직 |
| DevOps | Deploy, Git | 배포, CI/CD |
| CS | Network, OS | 기초 CS 지식 |
| TIL | _(없이 사용)_ | 짧은 삽질 기록 |

---

## 프로젝트 구조

```
taehyunahn.github.io/
│
├── _posts/              ← 글 쓰는 곳 (여기만 자주 건드림)
├── _tabs/about.md       ← 자기소개 페이지
├── _config.yml          ← 블로그 설정 (제목, URL 등)
├── _data/               ← 작성자 정보, 연락처 설정
├── assets/img/          ← 글에 넣을 이미지 저장
│
├── _layouts/            ┐
├── _includes/           │ 테마 엔진 (건드릴 필요 없음)
├── _sass/               │
├── _javascript/         ┘
│
└── .github/workflows/   ← 자동 배포 설정 (건드릴 필요 없음)
```

---

## 자주 쓰는 작업

### 프로필 이미지 추가
1. `assets/img/avatar.jpg`에 이미지 파일 추가
2. `_config.yml`에서 `avatar: "/assets/img/avatar.jpg"` 설정

### About 페이지 수정
`_tabs/about.md` 파일을 편집한다.

### 블로그 설정 변경
`_config.yml`에서 제목(`title`), 부제목(`tagline`), 설명(`description`) 등을 수정한다.
