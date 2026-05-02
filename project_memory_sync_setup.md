---
name: 메모리 동기화 구조 (OneDrive + git 수동 백업)
description: 4대 Mac 간 메모리 공유 셋업 — 실시간은 OneDrive, 스냅샷 백업은 GitHub
type: project
originSessionId: 043f3a3c-3164-4ce8-97af-37a24f5a960b
---
**메모리 실제 위치:** `~/Library/CloudStorage/OneDrive-개인/프로젝트/claude-memory/`
- `~/.claude/projects/-Users-settlment/memory/` 는 위 OneDrive 폴더로 향하는 심링크.
- 다른 3대 Mac 도 동일하게 자기 `~/.claude/projects/<인코딩된cwd>/memory/` 를 OneDrive 폴더에 심링크.
- 4대 Mac 간 동기화는 OneDrive 가 자동으로 처리.

**git 백업 (이 Mac 에서만):**
- Remote: `git@github.com:latzzzing/MS.git` (private, deploy key 인증)
- SSH key: `~/.ssh/id_ed25519` (이 Mac 전용, 비밀키 외부 유출 금지)
- 다른 3대 Mac 은 git 손 안 댐 — SSH 키도 deploy key 도 없음.

**백업/복구 명령 (이 Mac 에서만):**
```bash
# 백업 (수동 push)
cd ~/Library/CloudStorage/OneDrive-개인/프로젝트/claude-memory
git add -A && git commit -m "memory: $(date '+%Y-%m-%d %H:%M')" && git push

# 복구 (GitHub 의 마지막 스냅샷으로 강제 되돌리기 — OneDrive 가 망가졌을 때만)
cd ~/Library/CloudStorage/OneDrive-개인/프로젝트/claude-memory
git fetch && git reset --hard origin/main
```

**Why:** OneDrive 가 가끔 conflict 파일을 만들어 메모리가 꼬일 위험이 있어서 GitHub 에 별도 스냅샷이 안전망. 자동 훅을 안 쓰는 이유는 셋업 단순성 — 사용자가 백업이 필요하다고 느낄 때만 push.

**How to apply:**
- 메모리 추가/수정은 평소처럼 OneDrive 폴더(=심링크)에 그냥 쓰면 OneDrive 가 4대로 전파.
- 의미 있는 변경 후 사용자가 "백업해줘" / "push 해줘" 같이 요청하면 위 push 명령 실행.
- "메모리 복구해줘" / "어제 메모로 되돌려줘" 같은 요청은 위 reset 명령 또는 `git log` 보고 특정 커밋으로 되돌리기.
- 이 Mac 외에선 git 명령 절대 실행 안 함 (deploy key 가 없어 push 도 안 되지만 OneDrive 의 .git 폴더만 건드려 꼬일 수 있음).

**OneDrive 백업 (별도):** `claude_memory_backup` (4월 24 스냅샷), `memory.local-backup-20260502-040812` (OneDrive 심링크 직전 로컬 사본) 보존 중. 안정 확인 후 정리 가능.
