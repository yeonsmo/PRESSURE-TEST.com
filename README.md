# 압력시험.com

현재 운영 중인 명함 페이지와 문의 API를 보관하고, 앞으로 사이트를 개편하기 위한 기준 저장소입니다. 복구 기준일은 **2026-11-10, Asia/Seoul**입니다.

- 사이트: https://압력시험.com
- 현재 내용이 있는 페이지: https://압력시험.com/bizcard/
- GitHub: https://github.com/yeonsmo/PRESSURE-TEST.com

## 보관한 내용

| 위치 | 내용 |
|---|---|
| `public/` | 공개 응답에서 복구한 HTML, 3D 로고, 폰트, 공유 이미지, 원본 `_headers` |
| `workers/hpe-bizcard/` | 정적 파일 Worker의 재구성한 Wrangler 설정 |
| `workers/hpe-bizcard-inquiry/src/worker.js` | 배포 소스맵에서 추출한 편집용 원본 문의 처리 코드 |
| `workers/hpe-bizcard-inquiry/wrangler.jsonc` | 문의 API의 라우트·이메일 바인딩 설정 |
| `snapshots/deployed/` | Cloudflare에서 내려받은 실제 배포 JS와 소스맵 |
| `snapshots/cloudflare/` | DNS·라우트·런타임 설정·활성 버전·배포 이력 |
| `snapshots/http/` | 수집 URL, 응답 상태, 파일 해시, 외부 의존성, 원서버 첫 화면 |
| `docs/` | 복구 범위, 재배포 방법, 개편 전 확인 사항 |
| `scripts/` | 공개 파일 수집 도구와 백업 무결성 검증 도구 |

정적 파일 6개를 확보했습니다. `/bizcard/` HTML, 3D 로고 HTML, Pretendard 폰트, 공유 이미지, SPA fallback용 `index.html`, 원본 `_headers`입니다. 문의 API는 작성 소스와 배포본을 함께 보관했습니다.

## 복구 범위

문의 Worker의 원본은 소스맵의 `sourcesContent`와 일치합니다. HTML은 공개 HTTP 응답을 그대로 보관했으므로 Cloudflare가 응답에 주입한 분석 스크립트를 포함할 수 있습니다. 새로 만든 Wrangler 설정과 `package.json`은 조회한 배포 정보를 바탕으로 재구성한 파일이며, 과거 제작 프로젝트의 원본 파일이라고 주장하지 않습니다.

Cloudflare에는 전체 정적 자산 목록을 내려받는 문서화된 조회 API가 없어, **페이지에서 참조하지 않는 파일과 배포 전 제작 원본까지 전부 복구되었다고 증명할 수는 없습니다.** 별도 원서버의 서버 코드·제작 파일도 확보한 대상에 포함되지 않습니다. 자세한 범위는 [docs/RECOVERY.md](docs/RECOVERY.md)를 참고하세요.

## 로컬 확인

Node.js와 Python 3를 사용합니다.

```bash
npm ci --ignore-scripts
npm run check
npm run preview:static
```

정적 미리보기는 `http://127.0.0.1:4173/bizcard/`에서 확인합니다. HTML 주소 끝의 `/`를 유지하세요. 문의 API는 Python 정적 서버에서 동작하지 않습니다.

Cloudflare 설정과 코드의 배포 패키지만 검사하려면 다음 명령을 사용합니다. 실제 배포는 수행하지 않습니다.

```bash
npm run dry-run:site
npm run dry-run:inquiry
```

## 운영 경로

| 요청 경로 | 처리 대상 |
|---|---|
| `압력시험.com/bizcard`, `/bizcard/*` | `hpe-bizcard` 정적 파일 Worker |
| `압력시험.com/bizcard/api/*` | `hpe-bizcard-inquiry` 문의 Worker |
| `압력시험.com/` | DNS가 가리키는 별도 원서버 |

첫 화면의 원서버 응답은 목적지가 비어 있는 `http://` 리다이렉트 문서였습니다. 이를 `snapshots/http/live-root.html`에 기록했습니다. Worker의 SPA fallback으로 복구한 `public/index.html`과는 서로 다른 문서입니다.

이 저장소에 올리는 작업으로 Cloudflare 배포·DNS·이메일 설정을 변경하지 않았습니다. 자동 배포도 연결하지 않았습니다. 수동 재배포 절차는 [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md)에 있습니다.

## 개편 시작점

우선 도메인 첫 화면을 정상적인 사이트 진입점으로 구성한 뒤 회사 소개, 수압·기압시험 서비스, 시험 의뢰 절차, 시설·장비·실적, 견적 문의로 확장할 수 있습니다. 현재 명함 페이지와 문의 처리를 보존하면서 실제 사업 정보와 증빙 자료를 기준으로 설계합니다. 아직 새 디자인이나 새 운영 배포를 적용한 상태는 아닙니다.
