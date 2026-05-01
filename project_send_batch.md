---
name: 쿠팡/배민 정산서 일괄 발송 시스템
description: ~/send_batch/ 내 파이프라인 구성, 실행 방법, 주의사항
type: project
originSessionId: 94539b67-543d-4b15-8e87-a5a4e0afa668
---
**경로**: `~/send_batch/`
- `send_batch.py` — 메인 파이프라인 (Contacts 덤프 → 파일 파싱 → 번호 조회 → send_one.sh 호출 → 로그)
- `send_one.sh` — 파일 1개 처리 (Excel 캡처 → Messages 발송). platform=coupang/baemin 분기.
- `dump_contacts.applescript` — Contacts 전체 덤프
- `contacts_dump.txt` — 덤프 캐시
- `send_batch_log.txt` — 발송 로그 (SENT 파일 자동 스킵)
- `run_output.log` — 실행 콘솔 출력

**실행 방법:**
- `python3 ~/send_batch/send_batch.py` (Contacts 덤프 갱신 후 발송)
- `python3 ~/send_batch/send_batch.py --no-refresh` (기존 덤프 사용, 더 빠름)
- `python3 ~/send_batch/send_batch.py --no-refresh --dry-run` (매칭 확인만)

**주의사항:**
- **launchd 예약 실행 불가** — Downloads/Contacts 접근 권한 부족. 직접 실행만 안전. (2026-04-24 확인)
- **Contacts 덤프 전 Contacts 앱 실행 상태 확인** — 앱 꺼져있으면 0줄 덤프 → 대부분 FAIL
- **--no-refresh 권장** — 덤프를 미리 foreground에서 뜬 후 --no-refresh로 실행하면 덤프 실패 위험 회피
- **주차별 FOLDERS 경로 수정 필요** — send_batch.py 내 FOLDERS 변수
- **send_batch_log.txt 초기화** — 새 주차 시작 시 이전 로그 비우기

**HARDCODE_BY_FILENAME**: 파일명 직접 매핑 (번호 변경/비정형 파일 대응). 사용자가 번호 변경 알려주면 여기 추가.

**SKIP_NUMBERS**: 발송 제외 번호 세트 (현재 비어있음).

**Contacts 업데이트 규칙**: 기존 연락처 보존, 신규만 추가. 번호 변경은 사용자 수동 지시 시만 HARDCODE 고정.

**발송 실적:**
- 2주차 (2026-04-17): 쿠팡 370 + 배민 92 = 459건 (에러 0)
- 3주차 (2026-04-24): 쿠팡 362 + 배민 95 = 457건 (에러 1 → 재발송 완료)
