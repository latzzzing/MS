---
name: 라이더 마스터 JSON
description: ~/send_batch/riders.json 빌더와 봇 통합. 라이더 이름 → 플랫폼·권역·폰 매핑.
type: project
originSessionId: 043f3a3c-3164-4ce8-97af-37a24f5a960b
---
**위치**: `/Users/settlment/send_batch/riders.json` (511명 기준 — 배민 162, 쿠팡 399, 양쪽 50)

**빌더**: `/Users/settlment/send_batch/build_riders_json.py`

**입력 (3개 source)**:
- `/tmp/baemin_riders.txt`     (미추홀A): `이름|배민ID|폰`
- `/tmp/baemin_bucheon.txt`    (부천C):   `이름|배민ID|폰`
- `/tmp/coupang_riders_uniq.txt`:         `이름|쿠팡권역|폰`

**출력 구조**:
```json
{
  "riders": [{"name", "aliases", "phone", "platforms": [{"platform", "region", "baemin_id?"}]}],
  "by_name": {"이름": ["폰1", "폰2"]},
  "by_phone": {"010-XXXX-XXXX": idx}
}
```

폰 번호로 cross-platform 머지 (같은 사람이 배민+쿠팡 둘 다인 경우 한 entry로 합쳐짐). HARDCODE_BY_FILENAME 의 한글 파일명을 외국이름 라이더의 한글 alias 로 자동 주입 (CUICHUNZHE→최춘철, JIN MINGXUE→김명학 등 6명).

**봇 통합** (bot.py):
- 신규 도구 `lookup_rider_region` (이름 → 플랫폼·권역 조회). JSON 문자열로 반환 (dict 직접 반환 시 Anthropic API tool_result 거부).
- system prompt: "권역을 모르면 lookup_rider_region 으로 먼저 찾고 → check_rider_performance(쿠팡)/lookup_rider_history(배민) 호출"

**Why**: "이유신 수락율" 같이 사용자가 권역 안 말하고 이름만 던지는 경우 봇이 어느 사이트에서 조회해야 할지 모름. JSON 한 번 조회로 해결.

**How to apply**: 매주 contacts 동기화 (라이더 명단 스크랩) 후 `python3 ~/send_batch/build_riders_json.py` 실행해 갱신. 갱신 안 하면 신규 라이더는 봇이 못 찾음.
