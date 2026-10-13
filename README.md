# CaptureApp — 가벼운 Windows 화면 캡쳐 도구

트레이에 상주하면서 단축키 한 번으로 화면을 캡쳐하는 프로그램입니다.
캡쳐 결과를 화면에 떠 있는 창(플로팅)으로 보여주고, 그 위에 바로 주석을 그리거나
화면의 색을 추출할 수 있으며, 클립보드 복사·파일 저장을 자동으로 처리합니다.

> **Windows 전용**입니다. Win32 API(DWM, RegisterHotKey, 저수준 훅 등)를 직접 사용하므로 macOS/Linux에서는 동작하지 않습니다.

## 주요 기능

### 캡쳐
- **영역 캡쳐** — 드래그로 원하는 영역 선택 (기본 `Ctrl+Shift+1`)
- **전체화면 캡쳐** — 모든 모니터를 합친 화면 (기본 `Ctrl+Shift+2`)
- **활성창 캡쳐** — 마지막으로 쓰던 창을 자동 인식 (기본 `Ctrl+Shift+3`)
  - 바탕화면 등 활성 창이 없을 땐 **커서가 있는 모니터**를 캡쳐
- 단축키는 메인 창에서 바꿀 수 있습니다 (설정은 `%APPDATA%\CaptureApp\settings.json`에 저장).

### 플로팅 창 + 주석
캡쳐 결과가 화면 위에 떠 있는 창으로 뜨고, 바로 위 작은 툴바로 주석을 그릴 수 있습니다.
- 도구: **박스 · 화살표 · 펜(자유 그리기) · 글자**, **색상/굵기** 선택, **실행취소**(`Ctrl+Z`)
- **짧은 클릭** = 이동 ↔ 직전 그리기 도구 전환 / **드래그** = 그리기·창 이동 / **Tab** = 도구 순환
- 휠: 확대·축소 / `0`: 원본 크기 / 더블클릭·`Esc`: 닫기 / 우클릭: 복사·저장 메뉴
- 그린 주석은 **복사(`Ctrl+C`)·저장(`Ctrl+S`) 시 이미지에 함께 포함**됩니다.
- 캡쳐를 다른 앱에 **붙여넣으면(`Ctrl+V`)** 플로팅 창이 자동으로 닫힙니다.

### 색상 추출 (스포이드)
- 툴바의 💧 버튼 → **화면 어디서나 클릭**해 그 픽셀 색을 클립보드로 복사
- 멀티모니터·모니터별 DPI 배율이 달라도 정확 (모니터별 오버레이 방식)
- 메인 창에서 **16진수(`#RRGGBB`) / RGB** 형식 선택

### 기타
- 클립보드 자동 복사 / 파일 자동 저장 / 저장 폴더 지정
- Windows 시작 시 자동 실행 옵션 (트레이로 조용히 시작)
- 모니터별 배율(DPI)이 다른 멀티모니터 환경 지원
- 메인 창 제목·트레이 툴팁에 현재 버전 표시

## 설치 및 실행 (소스)

```powershell
# 1. 가상환경 생성 + 활성화
python -m venv .venv
.venv\Scripts\Activate.ps1

# 2. 의존성 설치
pip install -r requirements.txt

# 3. 실행 (콘솔 없이 실행하려면 pythonw)
python main.py --show
```

- 기본은 트레이 아이콘으로만 시작합니다. `--show` 인자를 주면 메인 창이 함께 뜹니다.
- 저장 폴더 기본값: `내 사진\CaptureApp`

## 빌드 (PyInstaller)

```powershell
pip install pyinstaller
pyinstaller --noconsole --onefile --name CaptureApp main.py --exclude-module numpy
```

- `dist\CaptureApp.exe` 하나로 배포할 수 있습니다.
- `--exclude-module numpy` — 쓰지 않는 numpy를 빼서 실행 파일 크기를 줄입니다 (약 65MB → 54MB).

## 파일 구성

| 파일 | 역할 |
|---|---|
| `main.py` | 메인 창·트레이·단축키·캡쳐 흐름·설정 |
| `capture.py` | 화면 캡쳐 (mss + Win32, 멀티모니터 DPI 처리) |
| `region_selector.py` | 드래그 영역 선택 오버레이 |
| `floating_window.py` | 캡쳐 결과 플로팅 창 + 주석 툴바 |
| `color_picker.py` | 색상 추출(스포이드) 오버레이 (모니터별 분리) |
| `hotkeys.py` | 전역 단축키 (RegisterHotKey) |
| `keyboard_watcher.py` | 전역 `Ctrl+V` 감시 (붙여넣기 → 플로팅 닫기) |
| `autostart.py` | Windows 시작 시 자동 실행 등록 |

## 라이선스

© 2026 모래속에사는고래 — **무료 사용 · 재배포 금지 · 유료화 금지**

- 개인·업무 용도로 **자유롭게 사용**할 수 있습니다.
- 원본 또는 수정본을 **재배포**(재업로드·재공유·2차 배포 등)하는 것을 금지합니다.
- **유료 판매 등 상업적 이용**을 금지합니다.

블로그: [계정과 코드](https://blog.naver.com/sandwhale688)
