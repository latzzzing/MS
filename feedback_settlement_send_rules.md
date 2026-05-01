---
name: 정산서 전송 범위 규칙
description: 쿠팡/배민 정산서 xlsx를 Messages로 전송할 때 캡처 범위, 흰색 채움, 2장 한 메시지 규칙
type: feedback
originSessionId: 94539b67-543d-4b15-8e87-a5a4e0afa668
---
정산서 xlsx → Messages 이미지 전송 규칙:

**플랫폼별 범위:**
- 쿠팡: A1:E17, 비고 흰색 D6:D16
- 배민: A1:E18, 비고 흰색 D6:D17

**흰색 채움 (RGB 직접 지정):**
- 비고 영역 + 처리건수(E4): `set color of interior object of range to {255, 255, 255}`
- `color index 2`는 테마 색상(ci=-6) 파일에서 못 덮음 → RGB {255,255,255} 사용 필수 (2026-04-24 3주차에서 확인)

**G2 규칙 (2장 한 메시지):**
1. A1:E17(18) 캡처 → Messages 붙여넣기 (**return 안 침**)
2. G2에 데이터 있으면 → CurrentRegion 캡처 → 붙여넣기 → **return** (2장 동시 전송)
3. G2 없으면 → 1장만 붙여넣기 → return

**공통:**
- `copy picture ... appearance printer` 사용
- 캡처 전 `select range "Z30"` (선택 하이라이트 방지)
- 저장 안 하고 워크북 닫기

**Why:** 2장을 한 메시지로 보내면 기사가 정산서 + 추가내역을 한 번에 확인 가능. RGB 직접 지정은 테마 색상 파일 대응.
**How to apply:** `~/send_batch/send_one.sh`에 반영됨. platform 인자(coupang/baemin)로 범위 분기.
