---
name: Messages 이미지 전송 자동화 방법
description: macOS Messages 앱에서 정산서(xlsx) 등을 이미지로 자동 전송하는 검증된 워크플로우
type: feedback
originSessionId: 63c42319-d466-43ce-8d93-8acc07a3e56e
---
Messages 앱으로 이미지 MMS 자동 전송 시, AppleScript `send POSIX file`은 "전송 안 됨" 실패함.
keystroke로 Cmd+A/Cmd+C도 Preview에 제대로 전달되지 않음 (색상 조절 창이 뜨는 등 문제).

**작동하는 전체 파이프라인 (정산서 xlsx → Messages 이미지 전송, 2026-04-17 검증):**
1. xlsx → PDF: Excel AppleScript `save as active sheet filename ... file format PDF file format` — 전체 시트 전송 시
   - 또는 `qlmanage -t -s 2000 -o DIR input.xlsx` 로 PNG 썸네일 생성 (Excel 자동화 실패 시 대안)
   - 특정 범위(A1:E17, CurrentRegion 등) 전송 시에는 Excel `copy picture of range ...` 로 클립보드에 PNG 복사
2. PDF → PNG: `sips -s format png input.pdf --out output.png`
3. Preview에서 PNG 열기 (또는 clipboard 그대로 사용)
4. System Events → Preview 메뉴바 "편집" → "전체 선택" 클릭
5. "편집" → "복사하기" 클릭
6. `open "sms:<전화번호>"` 로 Messages에 대화창 열기 (수신자 자동 입력됨)
7. Messages 활성화 → 메뉴바 "편집" → "붙여넣기" 클릭
8. keystroke return으로 전송

**Excel 샌드박스 주의:** Excel은 기본적으로 `/tmp` 등 일부 경로에 접근 못해서 "허가 요청" 팝업이 반복됨. 해결책 — (a) 작업 파일을 `~/Documents/` 하위에 두기, 또는 (b) 시스템 설정 → 개인정보 보호 및 보안 → 전체 디스크 접근 권한에 Microsoft Excel 추가 (Terminal/Claude Code도 동일 권한 필요할 수 있음. 권한 부여 후 재시작 필요). URL: `open "x-apple.systempreferences:com.apple.preference.security?Privacy_AllFiles"`

**Excel AppleScript 불안정:** AppleEvent 타임아웃(-1712) / 매개변수 오류(-50) 발생 빈번함. 해결책 — (a) 스크립트 시작에 `with timeout of 60 seconds` 블록, (b) 각 명령 사이 `delay 2~3` 충분히, (c) 실패 시 `tell application "Microsoft Excel" to quit saving no` 후 재시작.

**연락처 전화번호 조회:** 파일명에서 뒷 4자리만 아는 경우 Contacts AppleScript로 조회 가능:
```applescript
tell application "Contacts"
    repeat with p in (every person whose name contains "이름")
        repeat with ph in phones of p
            return value of ph
        end repeat
    end repeat
end tell
```

**Why:** keystroke Cmd+키 방식은 앱 포커스/활성화 문제로 키 입력이 다른 곳으로 가거나 인식 안 됨. 메뉴바 직접 클릭이 가장 안정적. `open sms:` URL 스킴은 수신자 입력 좌표 클릭을 대체하여 안정성 향상.
**How to apply:** Messages 이미지 전송 자동화 시 항상 메뉴바 클릭 방식 사용. 수신자 지정은 `open "sms:01012345678"` 사용. SMS 텍스트 전송은 AppleScript `send` 명령으로 가능.
