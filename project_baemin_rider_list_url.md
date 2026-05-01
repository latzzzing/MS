---
name: 배민 deliverycenter 페이지네이션 size 상한 100
description: deliverycenter.baemin.com 의 페이지 크기 파라미터 상한 및 활용
type: project
originSessionId: 043f3a3c-3164-4ce8-97af-37a24f5a960b
---
**deliverycenter.baemin.com 의 size 파라미터 상한은 100.** 사용자가 "배달현황(`/delivery/history`)" 과 "라이더별 배달내역(`/delivery/rider-history`)" 두 페이지에서 직접 테스트해 확인.
- `size=100` → 100행 표시 ✓
- `size=500` 같이 100 초과 값 → 서버가 무시하고 default(20)로 폴백 → 20행만 표시.
- 100명 초과면 `page=0,1,2,...` 인덱스 올려가며 페이지네이션 필요.

**관련 페이지 (page 파라미터 base 가 다름!):**
- `https://deliverycenter.baemin.com/delivery/history?page=0&size=100&orderName=name&orderBy=asc&...` — 배달현황. **page=0 부터** (0-based).
- `https://deliverycenter.baemin.com/delivery/rider-history?page=1&size=100&fromDate=YYYY-MM-DD&toDate=YYYY-MM-DD` — 라이더별 배달내역. **page=1 부터** (1-based; page=0 넣으면 에러).

**Why:** 사용자가 직접 양쪽 페이지에서 size 값 바꿔 테스트하고 100이 상한이라고 확인. 자동화 코드에서 한 번에 다 가져오려고 size=500 같은 큰 값 넣어두면 조용히 20행으로 잘려서 데이터 누락 발생.

**How to apply:**
- 이 Mac의 텔레그램 봇 `lookup_rider_history` (bot.py): `PAGE_SIZE=100` + page 인덱스 페이지네이션으로 처리. stale 데이터 방지 위해 직전 페이지 첫 행과 비교해 같으면 재시도.
- 다른 Mac (ms 계정, 관제 크론) 작업에서도 deliverycenter 페이지 스크랩 시 동일하게 size=100 + 페이지네이션 패턴 적용.
