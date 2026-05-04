---
name: Chrome AppleScript JS 토글
description: Chrome "Apple Events의 자바스크립트 허용" 옵션이 꺼지면 모든 사이트 로그인 자동화 실패. 자동 토글 불가, 수동 클릭 필요.
type: feedback
originSessionId: 043f3a3c-3164-4ce8-97af-37a24f5a960b
---
**증상**: osascript JS 실행 시
```
Google Chrome에 오류 발생: AppleScript를 통한 자바스크립트 실행 기능이 꺼져 있습니다.
```
이 메시지가 뜨면 baemin/coupang/모든 사이트 자동 로그인 스크립트가 깨짐.

**해결**: Chrome 메뉴 → **보기 → 개발자 정보 → "Apple Events의 자바스크립트 허용"** 토글 ON

**Why**: macOS UI scripting 제한으로 menu item click 자동화가 토글 안 됨. `defaults write com.google.Chrome AppleScriptForceJavaScript -bool true` 도 효과 없음. 사용자가 직접 메뉴에서 한 번 클릭하는 게 유일한 해결책.

**언제 꺼지는지**: Chrome 업데이트 시, 또는 한국 보안 플러그인(Veraport/Delfino/nProtect) 설치 시 꺼지는 것으로 추정. 보안 플러그인 제거 후에도 자동 복구 안 됨.

**How to apply**: 봇/관제 자동 로그인이 갑자기 실패하면 가장 먼저 이 옵션을 의심. 검증: `osascript -e 'tell application "Google Chrome" to execute active tab of front window javascript "1+1"'` → 성공 시 "2", 꺼지면 위 오류 메시지.
