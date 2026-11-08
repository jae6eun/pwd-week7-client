# pwd-week7-client

5주차 Ajou Campus Foodmap React 화면에 6주차 실습의 Express 서버 연동을 적용한 클라이언트입니다. 기존 `pwd-week7-client` 저장소와 Vercel 프로젝트를 사용합니다.

## 구현 범위

| 기능 | 데이터 처리 |
| --- | --- |
| 맛집 목록·상세·인기 조회 | 6주차 Express의 `/api/restaurants` 계열 GET API |
| 관리자 화면의 맛집 생성 | `POST /api/restaurants` |
| 제보 작성·조회·상태 변경·삭제 | 기존 브라우저 LocalStorage |
| 제보 승인 | 서버에 맛집 생성 요청 후 브라우저 제보의 승인 상태 변경 |
| 좋아요 | 기존 브라우저 LocalStorage |
| 맛집 수정·삭제 | 6주차 서버에 API가 없어 버튼 비활성화 |

서버 연동 코드는 6주차 README Step 11의 예제를 사용합니다. `src/services/api.ts`가 HTTP 요청을 담당하며, 5주차 원본 `src/services/api.local.ts`는 제보 저장 기능을 제공합니다. Supabase·로그인·추가 기능은 이 프로젝트 범위에 포함하지 않습니다. 인기 목록은 원본 실습대로 평점 내림차순이며, 원본 화면 제목을 유지합니다.

## 로컬 실행

Node.js 24.x를 사용합니다. `package.json`이 있는 이 프로젝트 루트에서 실행합니다.

```sh
npm ci --include=dev
```

`.env.example`을 `.env.local`로 복사하고 로컬 서버 주소를 입력합니다. 파일이 이미 있다면 덮어쓰지 않고 해당 값만 확인합니다.

```dotenv
VITE_API_BASE_URL=http://localhost:3000
```

6주차 Express 서버를 별도로 실행한 뒤 클라이언트를 실행합니다.

```sh
npm run dev
```

코드 검사는 다음 명령입니다.

```sh
npm run typecheck
npm run lint
npm run build
```

## 이미 만들어둔 Vercel 프로젝트 설정

업로드·배포는 사용자가 직접 진행합니다. 아래 절차는 기존 프로젝트 기준이며 새 프로젝트 생성이 필요하지 않습니다.

### 1. GitHub에 클라이언트 소스 반영

`pwd-week7-client` 저장소에 `src/`, `public/`, `index.html`, `package.json`, `package-lock.json`, `tsconfig*.json`, `vite.config.ts`, `eslint.config.ts`, `vercel.json`, `.gitignore`, `.env.example`, README를 반영합니다. 저장소 루트에서 `package.json`이 보여야 합니다. 프로젝트 전체를 한 단계 더 중첩한 폴더나 ZIP 파일 자체를 업로드하지 않습니다.

`node_modules/`, `dist/`, `.env.local`은 `.gitignore`에 의해 제외합니다. Vercel은 GitHub의 소스를 받아 의존성을 설치하고 직접 빌드합니다. 이 프로젝트의 npm 스크립트에는 GitHub Pages용 `deploy`가 없으며 Vercel 대시보드로 배포합니다.

### 2. 기존 프로젝트의 빌드 설정 확인

[Vercel 대시보드](https://vercel.com/dashboard)에서 기존 클라이언트 프로젝트를 선택합니다. 연결된 GitHub 저장소는 `pwd-week7-client`, 배포할 브랜치는 업로드한 브랜치(현재 저장소는 `main`)를 사용합니다.

**Settings → Build and Deployment**에서 다음 값을 확인합니다.

| 항목 | 값 |
| --- | --- |
| Framework Preset | `Vite` |
| Root Directory | `./` — 저장소 루트에 package.json이 있는 경우 |
| Install Command | `npm ci --include=dev` — Override 시 입력 |
| Build Command | `npm run build` |
| Output Directory | `dist` |
| Node.js Version | `24.x` |

기존 `vercel.json`의 framework·buildCommand·outputDirectory·SPA rewrite 설정을 유지했습니다. package.json의 engines.node도 `24.x`입니다. 대시보드의 이전 프로젝트 설정이 남아 있다면 위 값과 맞춥니다.

### 3. 서버가 아직 없는 상태에서 클라이언트 먼저 배포

현재 서버 미배포 상태에서도 클라이언트 빌드는 가능합니다. 이 단계에서는 Vercel의 `VITE_API_BASE_URL`에 localhost나 가상의 Render 주소를 넣지 않습니다. 변수가 없어도 빌드와 홈 화면 확인은 가능하며, 서버 API를 사용하는 목록·상세·인기·관리자 기능은 실제 서버 주소 설정 후 확인합니다.

GitHub에 연결된 브랜치로 코드를 올리면 Vercel이 빌드를 시작합니다. **Deployments**에서 최신 배포의 커밋과 빌드 로그를 확인하고 `Ready`가 되면 해당 배포의 도메인을 엽니다. 수동 재배포는 **Deployments → 대상 배포의 … → Redeploy**입니다. 정식 배포를 확인할 때는 Environment가 `Production`인 항목을 사용합니다.

### 4. Render 서버 배포 후 API 주소 등록

6주차 서버가 배포되어 실제 Render 공개 URL을 얻은 뒤, 브라우저에서 해당 주소의 `/health`와 `/api/restaurants` 응답을 확인합니다. 이후 기존 Vercel 프로젝트의 **Environment Variables** 메뉴를 엽니다. Settings 안에 표시되는 화면에서는 **Settings → Environment Variables**로 이동합니다.

| 입력 항목 | 값 |
| --- | --- |
| Name / Key | `VITE_API_BASE_URL` |
| Value | 실제 Render 공개 HTTPS 기본 URL |
| Environment | `Production`·`Preview` |

예를 들어 실제 서비스 주소가 `https://my-foodmap-api.onrender.com`이면 그 기본 주소만 입력합니다. 이 예시 주소는 복사할 실제 주소가 아닙니다. 따옴표·`/api/restaurants`·`localhost`를 붙이지 않습니다. 로컬 `.env.local`은 배포 환경변수로 자동 전달되지 않습니다.

**Save → Deployments → 대상 배포의 … → Redeploy** 순서로 새 빌드를 실행합니다. 환경변수 변경은 기존 빌드에 소급 적용되지 않으며, 브라우저 새로고침만으로 요청 주소가 바뀌지 않습니다.

### 5. 서버 연동 확인

| 주소 | 확인할 동작 |
| --- | --- |
| `/#/list` | 맛집 목록·카테고리 필터·상세 이동 |
| `/#/restaurant/1` | 서버의 맛집 상세·주소 직접 열기와 새로고침 |
| `/#/popular` | 서버가 반환하는 평점 상위 목록 |
| `/#/admin` | 새 맛집 생성·수정/삭제 버튼 비활성화 |
| `/#/submit` | 제보 작성과 완료 화면 |
| `/#/submissions` | 같은 브라우저의 제보 조회·승인 후 서버 맛집 등록 |

개발자 도구 **Network**에서 목록 GET은 실제 Render 주소로 전송되고 `200`, 맛집 생성 POST는 `201`인지 확인합니다. 제보 저장은 LocalStorage를 사용합니다. 다른 브라우저에서 동일 제보가 보이지 않는 것은 기존 실습의 저장 방식입니다. 서버 재시작·재배포 후 새 맛집이 초기화되는 것도 6주차 메모리 저장의 동작입니다.

## 자주 확인할 설정

| 증상 | 확인 항목 |
| --- | --- |
| package.json을 찾지 못함 | 저장소 루트와 Root Directory |
| tsc·vite를 찾지 못함 | Install Command에 개발 의존성이 포함되는지 확인 |
| API 주소 설정 오류 | Environment Variables에 실제 주소를 추가한 뒤 Redeploy |
| localhost·이전 API 주소로 요청 | 배포 환경의 값과 새 빌드의 커밋 확인 |
| 서버 응답이 늦음 | Render 서버의 `/health` 응답 확인 후 클라이언트 재시도 |
| 주소 직접 접속이 잘못됨 | 기존 HashRouter 경로 `/#/list`, `/#/restaurant/1` 사용 |

## 근거

대시보드 안내는 2026-11-02 공식 문서를 기준으로 정리했습니다.

- [6주차 서버 연동 실습](https://github.com/ajou-hyunseok-oh/pwd-week6#vercel-client)
- [Vercel 빌드 설정](https://vercel.com/docs/builds/configure-a-build)
- [Vercel Node.js 버전](https://vercel.com/docs/functions/runtimes/node-js/node-js-versions)
- [Vercel 환경변수 등록·수정](https://vercel.com/docs/environment-variables/managing-environment-variables)
- [Vercel 재배포](https://vercel.com/docs/deployments/managing-deployments)
