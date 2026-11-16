# 🏆 체험단 보드

무신사 · 카카오 선물하기(그리고 앞으로 29CM) 체험단 신청 내역을 한 화면에서 보는 **모바일 퍼스트 PWA**입니다.

- 배포 주소: <https://kmoris0122-source.github.io/trial-board/>
- 아이폰 사파리에서 열고 **공유 → 홈 화면에 추가**를 하면 앱처럼 전체화면으로 열립니다. (오프라인에서도 마지막 데이터로 열림)

## 화면 구성

| 영역 | 내용 |
|---|---|
| 요약 카드 | 전체 신청 건수 · 가장 빠른 발표 D-Day · 당첨 건수 · 후기 마감 임박(7일 이내) 건수 · 마지막 업데이트 시각 |
| 필터 | 플랫폼(전체 / 무신사 / 카카오 선물하기 / 29CM) · 상태(전체 / 신청완료 / 당첨 / 후기완료 / 미당첨) · 검색 · 정렬 |
| 카드 | 플랫폼 배지, 상태 배지, 제세공과금 배지, 발표 D-Day, 상품명, 선택 옵션, 모집/신청 인원, 당첨 확률, 발표일·후기 기간, 캠페인 바로가기 |

정렬은 **발표 빠른순 → 확률 높은순 → 후기 마감순 → 최근 신청순** 순으로 버튼을 누르면 바뀝니다.

## 개인 기록 (이 기기에만 저장)

- **확인** 체크: 카드가 흐려지며 처리 완료 표시
- **상태** 변경: 신청완료 / 당첨 / 후기완료 / 미당첨 — 요약 카드와 필터 집계에 바로 반영
- **메모**: 카드별 자유 메모 (배송지, 후기 링크, 사이즈 등)
- 테마(라이트/다크) 선택

모두 `localStorage`(`trialboard.v1`)에만 저장되며 서버로 전송되지 않습니다. 브라우저 데이터를 지우면 함께 사라집니다.

## 데이터 갱신

`data/trials.json` 한 파일이 전부입니다.

```json
{
  "updatedAt": "2026-11-16T14:10:00+09:00",
  "items": [
    {
      "id": "musinsa-50084",
      "platform": "musinsa",            // musinsa | kakao | 29cm
      "title": "상품명",
      "option": "BK / 105",
      "appliedAt": "2026-11-16",
      "announceAt": "2026-11-24",       // 발표일 (D-Day 계산 기준)
      "reviewStart": "2026-11-28",      // 후기 없으면 ""
      "reviewEnd": "2026-12-12",
      "winners": 5,                     // 모집 인원
      "applicants": 3522,               // 신청 인원
      "status": "applied",              // applied | won | reviewed | lost
      "taxNotice": true,                // 5만원 초과 제세공과금 22% 대상 여부
      "url": "https://www.musinsa.com/preuser/events/50084?gf=M",
      "note": "이벤트 50084 · 5만원 초과 제세공과금 22% 대상"
    }
  ]
}
```

- 새 신청을 넣으면 `id`(중복 없이), `updatedAt`을 갱신하고 `main`에 커밋·푸시하면 1~2분 안에 반영됩니다.
- 당첨 결과가 나오면 `status`를 `won` / `lost`로 바꾸고, 후기 작성 후 `reviewed`로 바꿉니다. (앱 카드에서 바꾼 상태는 그 기기에만 저장되므로, 모든 기기에 반영하려면 이 파일을 고칩니다.)
- `applicants`는 신청 시점의 값입니다. 나중에 확인한 값으로 갱신해도 됩니다.

## 파일 구조

```
index.html            # 앱 전체 (HTML + CSS + JS, 외부 빌드 없음)
data/trials.json      # 신청 내역 데이터
manifest.webmanifest  # PWA 매니페스트
sw.js                 # 서비스 워커 (오프라인 캐시)
icons/                # 앱 아이콘 (180/192/512)
.nojekyll             # GitHub Pages가 파일을 그대로 서빙
```

## 로컬에서 열기

```bash
cd trial-board
python3 -m http.server 8080
# http://localhost:8080
```

`index.html`을 파일로 직접 열면 `fetch("data/trials.json")`이 차단되므로 위처럼 간단한 서버로 열어 주세요.

## 배포

GitHub Pages (branch `main`, path `/`). `main`에 푸시하면 자동으로 다시 배포됩니다.
