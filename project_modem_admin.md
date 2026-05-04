---
name: 메인 모뎀 (LG U+ GAPM-7500R) admin 접근 정보
description: CCTV 포트포워딩 등 메인 모뎀 설정 변경에 사용. 다른 Mac/wifi 에서도 동일.
type: project
originSessionId: 043f3a3c-3164-4ce8-97af-37a24f5a960b
---
**모델:** LG U+ GAPM-7500R (LGU_B229 SSID)
**Admin URL:** http://192.168.219.1/ (`/start.asp` 로 redirect)
**Admin 비밀번호:** `902622B9M#`
**현재 외부 IP (수시 변경):** 112.147.185.56 (2026-05-03 시점)

**자주 가는 메뉴:**
- 네트워크 설정 → NAT 설정 → 포트포워딩 설정 — CCTV 8602 토글 ON/OFF
- 상태 정보 → 인터넷 연결 정보 — 현재 외부 IP 확인
- 네트워크 설정 → DHCP 서버 설정 → 고정 IP 할당 — TRDELIVERY Deco 의 WAN IP 고정 (현재 192.168.219.41 으로 예약)

**현재 등록된 포트포워딩 룰:**
- 8602 → 192.168.219.43 (TRDELIVERY Deco) → 내부 192.168.68.57 (CCTV) — 외부에서 CCTV 접근용

**주의:** 메인 모뎀이 죽으면 wifi 도 다 끊김. admin 에서 reset/reboot 시 신중히.
