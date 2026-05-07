---
name: 배민 배달처리비 일일 정산 요약 절차
description: 배민 관리자 사이트에서 배달처리비를 다운로드 → 복호화 → 빈 금액 검증 (필요 시 10분 간격 재다운로드) → 라이더별 요약 시트 생성하는 자동화 절차
type: project
originSessionId: 043f3a3c-3164-4ce8-97af-37a24f5a960b
---
배민 배달처리비 데이터를 기반으로 매일/주기적으로 수행하는 정산 보조 프로세스.

## 1. 다운로드

- **사이트**: `deliverycenter.baemin.com/center/delivery-fee-download`
- **로그인**: 필요 시 `~/send_batch/baemin_login.sh` 호출 (Chrome + 2FA 자동)
- **권역**: 미추홀A (`DP2512026025`) + 부천C (`DP2512023878`) — 협력사 변경 select.value 변경 + "선택 완료"
- **기간 선택**: "1개월" 라디오 후 date picker 에서 일자 클릭. **단일일** 받으려면 같은 날짜 두 번 클릭(range 단일).
- **다운로드 모달 사유**: 반드시 **"정산"** 입력
- **버튼 클릭**: `dispatchEvent(MouseEvent)` 또는 `.click()`. 단 first-attempt 의 modal 다운로드는 isTrusted 체크로 막히는 경우 있음 → Chrome 의 "다중 파일 다운로드 허용" 한 번 켜두면 OK

## 2. 빈 금액 검증 + 재다운로드 (중요)

복호화 후 `배달처리비` 컬럼이 **비어있는 행이 있으면**:

1. **원본 .xlsx 즉시 삭제** (그대로 두면 잘못된 합산이 나갈 위험)
2. **10분 대기** 후 재다운로드 (즉시 재다운로드는 같은 빈 값 옴)
3. 다시 복호화 + 검증
4. 모든 행이 채워질 때까지 반복

권역 한 쪽만 빈 경우 그 권역 파일만 재다운로드하면 됨. **합산·이동 작업은 검증 통과 후에만** 진행.

**Why**: 배민 서버가 일정 시점 전엔 정산 데이터를 미반영 상태로 제공하는 경우가 있음. 10분 간격이 검증된 폴링 주기.

## 3. 복호화

- **비밀번호**: 계정 ID (현재 `flatform845`)
- **도구**: `msoffcrypto-tool` — 자세한 절차는 `project_excel_password_removal.md` 참조

## 4. 정산 요약 시트 생성 (Python)

- **입력**: 복호화 .xlsx + `~/send_batch/riders.json` (연락처 매핑)
- **로직**:
    1. `라이더명`, `User ID`, `배달처리비` 컬럼 추출
    2. `(권역, User ID, 라이더명)` 키로 group by → 건수(COUNT), 금액(SUM)
    3. `riders.json` 의 `baemin_id` 로 `phone` 매칭
    4. 헤더: **권역, 아이디, 이름, 전화번호, 건수, 금액**
    5. 일자별 요청이면 시트당 하루치, 단일일이면 한 시트

## 5. 저장 위치 및 경로

- **마스터 데이터**: `/Users/settlment/send_batch/riders.json`
- **로그인 환경변수**: `/Users/settlment/Library/CloudStorage/OneDrive-개인/프로젝트/telegram_claude_bot/.env`
- **결과물 보관**: `/Users/settlment/Library/CloudStorage/OneDrive-개인(2)/공유/원본정산서/` (사용자가 이동 지시 시)

## How to apply

사용자가 "배민 배달처리비 다운로드" 또는 "정산 요약" 류 요청 시:
1. 미추홀A → 일자 선택 → 사유 정산 → 다운로드
2. 부천C 전환 → 동일 절차
3. 두 파일 복호화 → **금액 빈 행 검증** → 빈 행 있으면 §2 재다운로드 루프
4. 통과 후 라이더별 요약 시트 생성 → 저장
5. 원본은 사용자 지시에 따라 정리/이동
