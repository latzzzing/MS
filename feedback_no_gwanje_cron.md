---
name: 이 Mac에서 관제 크론 실행 금지
description: settlment 계정 Mac에서는 gwanje.sh/baemin_gwanje.sh/moltbot 등 관제 스케줄을 절대 등록·실행하지 말 것
type: feedback
originSessionId: 94539b67-543d-4b15-8e87-a5a4e0afa668
---
이 Mac(`/Users/settlment/`, 호스트네임 settlment)에서는 **관제 크론을 켜면 안 된다.**

**금지 대상:**
- `crontab`에 `gwanje.sh` / `baemin_gwanje.sh` / `moltbot.py` 등 스케줄 등록
- `~/Library/LaunchAgents/`에 관제용 plist 추가
- `guardian.py` 같은 감시 프로세스 상시 실행
- 관제 스크립트를 주기적/백그라운드로 돌리는 일체의 행위

**허용되는 것:**
- 일회성 `coupang_relogin.sh` / `baemin_relogin.sh` 수동 실행 (로그인 복구 목적)
- 스크립트 열람·편집

**Why:** 실제 관제 크론은 다른 Mac(ms 계정, `/Users/ms/...` 경로)에서 돌고 있음. OneDrive로 스크립트가 공유되기 때문에 이 Mac에서 같이 돌리면 KakaoTalk 메시지가 중복 전송되고 `/tmp/.gwanje_lock` 등 lock 충돌이 생길 수 있음. 2026-04-17 사용자가 명시적으로 "관제크론은 여기서 키면 안돼"라고 지시함.

**How to apply:** 관제/로그인 관련 작업 의뢰를 받을 때 스케줄 등록(crontab -e, launchctl load, loop skill 등) 제안을 절대 하지 말 것. 사용자가 직접 "여기 Mac에서 크론 등록해줘"라고 지시한 경우에도 다른 Mac과 중복 실행 위험을 먼저 경고하고 확인받을 것.
