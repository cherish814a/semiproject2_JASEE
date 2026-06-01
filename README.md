# 🪑 자세히봐 (Fit Me Up)

> **10년 차 물리치료사의 임상 노하우를 딥러닝으로 구현한 사무직 근로자용 자세 관리 서비스**  
> RULA + VDT 취급 근로자 작업관리지침(고시 제2020-17호) 기반 AI 실시간 자세 & 작업환경 측정 솔루션

---

## 📌 프로젝트 개요

VDT(Visual Display Terminal) 증후군 환자 수가 지속적으로 증가하는 가운데, 기존 서비스는 **신체 정렬만 분석**하는 데 그쳤습니다. 올바른 자세를 취해도 작업환경(모니터 높이, 책상 높이, 의자 등받이 등)이 맞지 않으면 다시 나쁜 자세로 돌아가는 구조적 문제가 있습니다.

**자세히봐**는 이 문제를 해결하기 위해 **자세 분석과 작업환경 인식을 동시에 수행**하고, RULA 및 VDT 고시 기반의 객관적 지표를 제공합니다.

---

## ✨ 주요 기능

| 기능 | 설명 |
|---|---|
| 📸 **실시간 자세 측정** | WebRTC 기반 카메라로 20초 안에 CVA·TIA 자동 판정 (GOOD 5초 유지 시 자동 전환) |
| 🖼 **이미지 자세 분석** | 측면 사진 1장 업로드로 7개 지표 즉시 분석 |
| 🏠 **작업환경 인식** | YOLOv8으로 의자·책상·모니터 4개 객체를 감지하여 환경 지표 계산 |
| 📊 **7개 인간공학 지표** | CVA, TIA, 무릎각도, 손목각도, 모니터 시선각, 작업대 높이, 의자 등받이 |
| 💬 **RAG 기반 AI 챗봇** | ChromaDB 벡터 DB + LLM으로 부위별 맞춤 상담 |
| 🧘 **운동 추천** | BAD 부위 기반 RAG 검색 + LLM 개인 맞춤 스트레칭 루틴 |
| 📈 **측정 이력 관리** | 사용자별 자세 점수 추이 차트 및 이미지 저장 |
| 🏆 **바른자세 챌린지** | 팀별 포인트 누적 레이스 (소셜 동기부여 시스템) |
| 🧾 **비급여 예상 영수증** | BAD 항목 기반 예상 치료비 자동 계산 |
| 📄 **근골격계 리포트 PDF** | 법정 유해요인 조사서(제1호~제11호) 포함 자동 생성 |

---

## 🏗 프로젝트 구조

```
📁 JASEE/
├── 📁 Yolo_env/                         ← 작업환경 인식 모델
│   ├── 📄 step1_split.py                ← 데이터 분할 (8:1:1)
│   ├── 📄 step2_augmentation.py         ← 데이터 증강 (~3배)
│   ├── 📄 step3_train.py                ← YOLOv8 학습
│   └── 📁 images_data/runs/posture_v1/weights/
│       └── ⭐ best.pt                   ← 환경인식 모델
│
├── 📁 Yolo_pose/                        ← 자세 분류 모델
│   ├── 📁 01_data_preprocessing/
│   ├── 📁 02_model_comparison/
│   ├── 📁 03_model(attention_MLP)_improve/
│   ├── 📁 04_final_model/
│   │   └── 📁 output/
│   │       └── ⭐ final_attention_mlp.pt ← 자세 분류 모델
│   └── 📁 05_documentation/
│
├── 📄 jasee_core.py                     ← 핵심 엔진 (YOLO + MLP + 각도 계산)
├── 📄 app_mobile.py                     ← 모바일용 Streamlit 앱 (메인 권장)
├── 📄 app_web.py                        ← 웹 브라우저 버전
├── 📄 app_desktop.py                    ← 데스크톱 버전
├── 📄 chatbot.py                        ← RAG 챗봇 (ChromaDB + LLM)
├── 📄 preprocess_jasee.py               ← 운동 지식 데이터 전처리
├── 📄 build_vectordb.py                 ← ChromaDB 벡터 DB 구축
├── ⭐ yolov8n-pose.pt                   ← YOLOv8 포즈 모델
├── 📄 logo_transparent.png
└── 📄 requirements.txt
```

> ⭐ 표시된 모델 파일 3개를 위 경로에 맞게 넣으면 바로 실행됩니다.

---

## ⭐ 모델 파일 설치 (중요)

GitHub에서 클론 후 아래 3개 파일을 직접 경로에 넣어주세요:

| 파일명 | 넣을 경로 |
|--------|---------|
| `yolov8n-pose.pt` | `JASEE/yolov8n-pose.pt` |
| `final_attention_mlp.pt` | `JASEE/Yolo_pose/04_final_model/output/` |
| `best.pt` | `JASEE/Yolo_env/images_data/runs/posture_v1/weights/` |

---

## 📐 측정 지표 및 판정 기준

| # | 지표 | 영문 | 정상 범위 | 근거 |
|---|---|---|---|---|
| 01 | 목굴곡각 | CVA | 0° ~ 20° | RULA Neck Zone |
| 02 | 몸통굴곡각 | TIA | 0° ~ 20° | RULA Trunk Zone |
| 03 | 무릎 각도 | Knee | 85° ~ 100° | VDT 고시 무릎 내각 기준 |
| 04 | 손목 각도 | Wrist | ±15° 이내 | RULA Wrist Zone / VDT 고시 |
| 05 | 모니터 시선각 | Gaze | 하방 10° ~ 15° | VDT 고시 제6조 |
| 06 | 작업대 높이 | Desk | 팔꿈치 기준 ±10% | 수평 정렬 기준 |
| 07 | 의자 등받이 | Chair | 골반너비 20% 이내 | VDT 고시 착석 기준 |

---

## 🧠 기술 스택

### 자세 분석
- **YOLOv8-pose** — 17개 키포인트 추출 (귀, 어깨, 골반, 무릎, 발목, 팔꿈치, 손목)
- **Attention MLP** (input_dim=16) — 자체 학습된 Attention 기반 MLP로 최종 GOOD/BAD 판정
- 각도 계산: CVA, TIA, 무릎·팔꿈치·손목 내각, 시선각, 작업대 높이 비율

### 작업환경 인식
- **YOLOv8** (커스텀 학습) — `chair_back`, `chair_seat`, `desk_surface`, `monitor` 4개 클래스
- 골반·팔꿈치·손목 키포인트 기준 공간 필터링으로 오검출 방지
- **Albumentations** — 데이터 증강 (~3배)

### RAG 챗봇
- **ChromaDB** — 5개 컬렉션 (자세 분석, RULA, 작업환경, 통증 완화, 운동 추천)
- **ko-sroberta-multitask** 임베딩 (`jhgan/ko-sroberta-multitask`)
- Ollama 로컬 LLM (qwen2.5:3b) 또는 외부 LLM 연동

### 프론트엔드 & 기타
- **Streamlit** + `streamlit-webrtc` (실시간 카메라)
- Pretendard 폰트 기반 커스텀 CSS 디자인 시스템
- 모바일 최적화 (430px 앱 뷰, 사이드바 비사용)
- **음성 안내**: pyttsx3
- **GPU**: NVIDIA RTX 4060 (CUDA 12.1) 권장, CPU도 동작

---

## 🚀 설치 및 실행

### 1. 가상환경 생성 (Python 3.10)

```bash
conda create -n Yolo_env python=3.10
conda activate Yolo_env
```

### 2. 패키지 설치

```bash
pip install -r requirements.txt
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
```

### 3. 모델 파일 배치

위 ⭐ 경로에 모델 파일 3개 배치 (GitHub Release 또는 별도 공유본 참조)

### 4. 벡터 DB 구축 (RAG 챗봇 사용 시)

```bash
python preprocess_jasee.py   # 운동 지식 데이터 전처리
python build_vectordb.py     # ChromaDB 인덱싱
```

### 5. 앱 실행

```bash
# 모바일 최적화 버전 (권장)
python -m streamlit run app_mobile.py

# 웹 브라우저 버전
python -m streamlit run app_web.py

# 데스크톱 버전
python -m streamlit run app_desktop.py
```

---

## 🔄 데이터 전처리 & 재학습 (선택)

```bash
python Yolo_env/step1_split.py        # train/val/test 분할 (8:1:1)
python Yolo_env/step2_augmentation.py # 데이터 증강 (~3배)
python Yolo_env/step3_train.py        # YOLOv8 학습
```

---

## 📱 측정 플로우

```
로그인 → 자세측정 탭 선택
    ├── 실시간 분석: 카메라 시작 → 20초 내 GOOD 5초 유지 → 환경 자동 측정 전환
    └── 이미지 분석: 측면 사진 업로드 → AI 분석 실행
            ↓
    7개 지표 결과 대시보드 (오버레이 이미지 + 게이지 바)
            ↓
    맞춤 피드백 + 운동 추천 + 히스토리 저장
```

### 측정 결과 구성
- **오버레이 이미지**: 관절 점·선 색상으로 GOOD(초록) / BAD(빨강) 즉시 파악
- **교정 화살표**: BAD 부위에 파란 화살표 + 한글 안내 텍스트 표시
- **7개 지표 카드**: 측정값, 정상 범위, 게이지 바, 맞춤 피드백 일체 제공
- **종합 점수**: 10점 만점 원형 게이지 + 양호/주의/위험 등급

---

## 📂 주요 파일 설명

| 파일 | 역할 |
|------|------|
| `jasee_core.py` | 핵심 분석 엔진 — 모델 로드, 각도 계산, 판정 기준(`CRITERIA`), 오버레이 렌더링 |
| `chatbot.py` | RAG 챗봇 — ChromaDB 5개 컬렉션 라우팅, `process_rag_query()` |
| `build_vectordb.py` | 벡터 DB 구축 — `ko-sroberta-multitask` 임베딩 커스텀 래핑, 배치 업서트 |
| `preprocess_jasee.py` | 운동 지식 문서 전처리 |
| `app_mobile.py` | 모바일 최적화 메인 앱 |

---

## 📊 비즈니스 가치

- **보건관리 사각지대 해소**: 50인 미만 사업장(보건관리자 선임의무 없음)에 URL 접속만으로 전문가 수준 피드백 제공
- **법정 문서 자동 생성**: 근골격계부담작업 체크리스트(제1호~제11호) PDF 자동 출력
- **예방 비용 가시화**: BAD 항목 기반 비급여 예상 치료비(도수치료·체외충격파·증식치료) 자동 산출
- **행동 변화 시스템**: 팀 챌린지 + 포인트 보상으로 장기적 자세 습관 형성 유도

---

## ⚠️ 주의사항

- 본 서비스는 **자세 개선 참고용**이며 의료적 진단이나 치료를 대체하지 않습니다.
- 실시간 측정은 **HTTPS 또는 localhost** 환경에서만 카메라 접근이 가능합니다.
- 로컬 LLM(Ollama) 미사용 시 챗봇 기능은 제한될 수 있습니다.
