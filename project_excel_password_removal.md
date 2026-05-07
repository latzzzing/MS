---
name: Excel 비밀번호 제거 방법
description: msoffcrypto-tool을 사용하여 암호화된 .xlsx 파일의 비밀번호를 제거하는 절차
type: project
---
배달 플랫폼(미추홀A 등)에서 다운로드한 "배달처리비" Excel 파일은 보통 비밀번호가 걸려 있음. 이를 자동화 프로세스에서 읽기 위해 비밀번호를 제거하는 방법임.

**사용 도구:** `msoffcrypto-tool` (Python 라이브러리)

**설치:**
```bash
python3 -m pip install msoffcrypto-tool --user
```

**제거 절차 (Python 스크립트):**
```python
import msoffcrypto

input_file = "encrypted.xlsx"
output_file = "decrypted.xlsx"
password = "비밀번호"

with open(input_file, "rb") as f:
    office_file = msoffcrypto.OfficeFile(f)
    office_file.load_key(password=password)
    with open(output_file, "wb") as decrypted:
        office_file.decrypt(decrypted)
```

**CLI 사용 시:**
라이브러리가 `~/Library/Python/3.9/bin` 등에 설치되어 PATH에 없을 수 있으므로 Python 스크립트 방식을 권장함.

**Why:** 비밀번호가 걸린 파일은 `openpyxl`이나 `pandas`에서 직접 열 수 없음. 먼저 복호화하여 덮어쓴 후 작업을 진행해야 함.
**How to apply:** 새로운 주차의 배달처리비 파일이 들어왔을 때, 사용자가 알려준 비밀번호(예: `flatform845`)로 위 로직을 실행하여 원본 파일을 복호화된 파일로 교체함.
