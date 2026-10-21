# 🐼 판다마켓

중고 상품 거래와 자유게시판을 제공하는 웹 서비스입니다.
코드잇 풀스택 스프린트 미션으로 **프론트엔드, 백엔드, AWS 배포까지 혼자** 구현했습니다. (2026.04 ~ 2026.08)

> 원래 코드잇 미션 저장소의 개인 브랜치(`next-하성휘`, `express-하성휘`)에서 작업했고,
> 포트폴리오용으로 커밋 기록을 유지한 채 이 저장소로 옮겼습니다.

<br/>

## 📁 구성

| 폴더 | 내용 | 자세히 |
| --- | --- | --- |
| [`frontend/`](./frontend) | Next.js · TypeScript · TanStack Query · Tailwind CSS | [README](./frontend/README.md) |
| [`backend/`](./backend) | Express 5 · TypeScript · Prisma · PostgreSQL · AWS S3 | [README](./backend/README.md) · [배포 가이드](./backend/DEPLOY.md) |

<br/>

## ✨ 주요 기능

**중고마켓**
- 상품 목록 (검색 · 최신순/좋아요순 정렬 · 페이지네이션), 상품 상세 · 등록 · 수정 · 삭제
- 상품 찜하기, 상품 댓글

**자유게시판**
- 베스트 게시글, 게시글 검색 · 정렬, 작성 · 수정 · 삭제
- 게시글 좋아요, 게시글 댓글

**회원**
- 회원가입 · 로그인 (JWT + bcrypt), 로그인이 필요한 페이지 접근 제어

**이미지**
- S3 업로드 (서버 경유 `multer-s3` + 브라우저 직접 업로드 Presigned URL)

<br/>

## ☁️ 배포 구성

```
브라우저 ──HTTPS──> Vercel (Next.js)
                     └─ /api 프록시 ──HTTP──> EC2 : Nginx → pm2 → Node
                                                 ├─ RDS PostgreSQL (프라이빗, EC2 보안 그룹만 허용)
                                                 └─ S3 (IAM 역할로 접근, 액세스 키 없음)
```

- 프론트엔드는 Vercel, 백엔드는 AWS EC2(t3.micro) + Nginx 리버스 프록시 + pm2
- RDS는 퍼블릭 액세스를 끄고 EC2 보안 그룹에서만 접근 허용
- 앱 포트(3000)는 외부에 열지 않고 Nginx만 노출
- 과제 채점 후 비용 관리를 위해 AWS 리소스를 삭제해 현재 배포 주소는 접속되지 않습니다

<br/>

## 💣 트러블 슈팅

<details>
<summary>HTTPS 사이트에서 HTTP API 호출이 차단되던 문제 (Mixed Content)</summary>

- **문제**: Vercel(HTTPS)에서 EC2(HTTP) API를 직접 호출하자 브라우저가 요청을 차단해 로그인·상품 조회가 모두 실패
- **해결**: 브라우저는 같은 출처(`/api`)로만 요청하고, Next.js `rewrites`로 Vercel 서버가 백엔드에 대신 전달하도록 프록시 구성
- **효과**: 도메인·인증서 없이 동작하고, 백엔드 주소도 브라우저에 노출되지 않음

</details>

<details>
<summary>t3.micro 메모리 1GB에서의 배포 설계</summary>

- Next 빌드와 Node 서버를 한 인스턴스에 올리면 메모리 부족이 생겨 프론트는 Vercel로 분리
- pm2는 cluster 대신 fork 모드 1개로 실행

</details>

<br/>

## 🧪 테스트

- Jest · supertest · jest-mock-extended 기반 API 테스트 121개 (`backend/tests`)
- 인증, 상품, 게시글, 댓글, 업로드, 에러 핸들러

<br/>

## 🚀 로컬 실행

```bash
# 백엔드
cd backend
cp .env.example .env    # 값 채우기
npm ci
npx prisma migrate deploy
npm run dev

# 프론트엔드
cd frontend
cp .env.local.example .env.local
npm ci
npm run dev
```

<br/>

## 🛠 단계별 성장 기록

| 기간 | 프론트엔드 | 백엔드 |
| --- | --- | --- |
| 2026.04 | HTML · CSS | |
| 2026.04 ~ 05 | React | Node · Express · Prisma |
| 2026.06 ~ 07 | Next.js 전환, 자유게시판 | Express API 확장 |
| 2026.08 | TypeScript 마이그레이션, Vercel 배포 | TypeScript 마이그레이션, AWS 배포, 테스트 |

이 저장소에는 Next.js · Express 단계 이후의 기록이 담겨 있습니다.
