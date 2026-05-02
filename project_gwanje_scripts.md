---
name: 쿠팡/배민 관제 및 로그인 스크립트 위치
description: OneDrive/프로젝트/telegram_claude_bot 내 관제·재로그인 스크립트 경로와 호출 체인
type: project
originSessionId: 94539b67-543d-4b15-8e87-a5a4e0afa668
---
관제 자동화 스크립트는 OneDrive에 공유되어 있고 **실제 크론은 다른 Mac(ms 계정)에서 돌고 있음.** 이 Mac은 일회성 수동 실행만 허용.

**경로:** `~/Library/CloudStorage/OneDrive-개인/프로젝트/telegram_claude_bot/`
이 Mac에서는 `/Users/ms` → `/Users/settlment` 심링크로 스크립트 내부 하드코딩 경로 해결 (심링크는 `sudo ln -s` 로 생성됨, 2026-04-17 적용).

**스크립트:**
- `gwanje.sh` — 쿠팡이츠 관제 (partner.coupangeats.com 탭에서 권역별 잔여 물량 수집 → KakaoTalk 전송). 끝에서 `baemin_gwanje.sh` 자동 호출.
- `baemin_gwanje.sh` — 배민 관제 (deliverycenter.baemin.com). 세션 만료 시 `baemin_relogin.sh` 호출.
- `coupang_relogin.sh` — 쿠팡 재로그인. 2FA는 Gmail Mail 앱의 쿠팡 메일에서 6자리 코드 추출. 끝에서 `baemin_relogin.sh` 자동 호출.
- `baemin_relogin.sh` — 배민 재로그인. ID=`dlvmf`, 2FA는 Messages 앱 DB(chat.db)에서 발신자 16000987 SMS로 6자리 추출.
- `.env` — COUPANG_ID/PW, BAEMIN_ID/PW, TELEGRAM_TOKEN, ANTHROPIC_API_KEY 보관.

**의존 요건 (이 Mac 기준):**
- Chrome의 "보기 → 개발자 정보 → Apple Events의 자바스크립트 허용" 체크 필요 (수동 토글, defaults로는 반영 지연됨).
- Mail 앱에 tr4401104@gmail.com 계정 추가 필요. **주의**: 이 Mac에선 계정 이름이 `Google`로 저장되어 있어서 `account "tr4401104@gmail.com"` 직접 참조 시 osascript -1728 에러. 영향받는 파일들:
  - `~/send_batch/coupang_login.sh` → `"Google"` 로 수정 완료 (2026-04-25)
  - `telegram_claude_bot/bot.py` `_coupang_do_2fa` → `"Google"` 로 수정 완료 (2026-05-02). 사용자가 텔레그램에서 `check_coupang_dashboard` 같이 쿠팡 세션을 트리거하는 요청 시 발생하던 에러.
  - `telegram_claude_bot/coupang_relogin.sh` 는 그대로 두기 (다른 Mac, ms 계정 쪽에서 도는 스크립트라 거기 Mail 계정 이름이 정확히 맞을 가능성).

**Why:** 관제는 권역별 물량 잔여를 시간대 피크별로 KakaoTalk 공지방에 자동 전송하는 업무임. 로그인 세션은 하루 단위로 만료되어 재로그인 필요.
**How to apply:** 로그인 복구만 요청받았을 때 `coupang_relogin.sh` 또는 `baemin_relogin.sh`를 일회성으로 실행. 크론/launchd 등록은 이 Mac에서 절대 하지 말 것 (feedback_no_gwanje_cron 참조).
