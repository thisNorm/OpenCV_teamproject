# :memo: 멀티프레임 글레어 제거 문서 보정 스캐너

> **Multi-frame Glare Removal & Document Scanner** > 2조 (無色無光) - 반사광(Glare) 제거 및 OCR 성능 극대화 프로젝트

![Python](https://img.shields.io/badge/Python-3.9+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![OpenCV](https://img.shields.io/badge/OpenCV-Image_Processing-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-Backend-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![PaddleOCR](https://img.shields.io/badge/PaddleOCR-KR_Support-000000?style=for-the-badge)
![React](https://img.shields.io/badge/React-Frontend-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)


<br/>


## 👥 Team Info
- **팀명**: 2조 (無色無光 - 없을 무, 빛 색, 없을 무, 빛 광)
- **팀원**: 이진우 · 고성운 · 황규범
- **발표 자료**: [프레젠테이션](https://docs.google.com/presentation/d/1ApCFfZzf_D2mYTSFcA1GCaDK9Jv3D6v9RlgkbTWBFfI/edit?usp=sharing)<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Canva](https://www.canva.com/design/DAGzxtezNq8/bdiYyEcsSGlipfPmrBvcYQ/edit?utm_content=DAGzxtezNq8&utm_campaign=designshare&utm_medium=link2&utm_source=sharebutton)


<br/>


## 📌 Project Overview

본 프로젝트는 유광 문서나 화이트보드 촬영 시 발생하는 **빛 반사(Glare)와 그림자를 제거**하고, 왜곡된 문서를 보정한 뒤 **PaddleOCR**을 통해 텍스트 인식률을 극대화하는 스캐너 시스템입니다.

단일 프레임 처리뿐만 아니라, 여러 장의 사진을 합성하여 반사광을 제거하는 **멀티프레임(Multi-frame) 병합 기술**을 적용했습니다.


<br/>


## 🌟 Key Features

### 1. ✨ Advanced Image Preprocessing (OpenCV)
- **Glare Removal**: HSV 색상 공간 분석을 통한 반사광 마스크 생성 및 Inpainting.
- **Multi-frame Merging**: 연속 촬영된 프레임 간의 차이를 이용해 반사광 영역을 깨끗한 픽셀로 대체.
- **Illumination Flattening**: 조명 불균형을 해소하여 문서의 가독성 향상.

### 2. 📐 Document Warping & Optimization
- **Perspective Correction**: 문서의 외곽선(Contour)을 검출하여 정면에서 본 것처럼 투시 변환(Warping).
- **Grid Cell Detection**: 표나 격자 구조가 있는 문서(책 표지 등)의 경우, 개별 셀 단위로 분할하여 인식 정확도 향상.

### 3. 📝 High-Performance OCR
- **PaddleOCR Integration**: 한글 인식률이 뛰어난 PaddleOCR 서버 모델 탑재.
- **Multi-pass Strategy**: 원본, 회전, 이진화, 업스케일링 등 다양한 전처리 이미지를 앙상블하여 최적의 텍스트 추출.
- **Domain Rule**: SSID, PASS 등 특정 패턴에 대한 후처리 보정 로직 적용.


<br/>


## 🚀 System Architecture

```mermaid
graph TD
    User[Client (React)] -->|Upload Images| Server[Backend (FastAPI)]
    
    subgraph "Image Processing Pipeline (OpenCV)"
        Server -->|Input| Check{Multi-frame?}
        Check -->|Yes| Merge[Merge Frames & Remove Glare]
        Check -->|No| Single[Single Frame Glare Removal]
        
        Merge --> Warp[Document Detection & Warping]
        Single --> Warp
        
        Warp --> Boost[Image Enhancement & Sharpening]
    end
    
    subgraph "OCR Engine (PaddleOCR)"
        Boost --> Grid{Grid Detected?}
        Grid -->|Yes| CellOCR[Run OCR on Each Cell]
        Grid -->|No| FullOCR[Run OCR on Full Image]
        
        CellOCR --> Parser[Text Parsing & Post-processing]
        FullOCR --> Parser
    end
    
    Parser -->|JSON Result| User
```


<br/>


## 🛠️ Tech Stack

| Category | Technology | Description |
| :--- | :--- | :--- |
| **Backend** | Python, FastAPI | RESTful API Server |
| **Computer Vision** | OpenCV, NumPy | Image Processing & Geometry Transformation |
| **OCR Model** | PaddleOCR | Deep Learning based Text Recognition |
| **Frontend** | React, Vite | Responsive Web UI |
| **Deployment** | Uvicorn | ASGI Server implementation |


<br/>


## 📂 Directory Structure

```text
root/
├── backend/                # FastAPI Server & Logic
│   ├── main.py             # API Entry Point
│   ├── processing.py       # [Core] OpenCV Image Pipeline
│   ├── ocr.py              # [Core] PaddleOCR Wrapper & Post-processing
│   ├── schemas.py          # Pydantic Data Models
│   └── requirements.txt    # Backend Dependencies
│
├── frontend/               # React Client
│   ├── src/
│   │   ├── components/     # UI Components (Upload, ResultViewer)
│   │   ├── api.js          # API Communication
│   │   └── App.jsx         # Main App Component
│   ├── vite.config.js      # Vite Proxy Setup
│   └── package.json        # Frontend Dependencies
```


<br/>


## 🚀 Getting Started

### 1. Backend Setup
```bash
cd backend

# Install dependencies (Requires Python 3.8+)
pip install -r requirements.txt

# Run FastAPI Server
# Server will run at http://localhost:8000
uvicorn main:app --reload
```

### 2. Frontend Setup
```bash
cd frontend

# Install dependencies
npm install

# Run Development Server
# Client will run at http://localhost:5173
npm run dev
```

### 3. Usage
1. 웹 브라우저에서 `http://localhost:5173` 접속.
2. 문서 사진(단일 또는 다중)을 드래그 앤 드롭.
3. **[처리 시작]** 버튼 클릭 시 반사광 제거 및 OCR 결과 확인.


<br/>


## 📅 Development Schedule (2025.09)

| Day | Activity | Note |
|:---:|:---|:---|
| **1일차** | 주제 선정 및 Git 초기화 | 프로젝트 기획 |
| **2일차** | OCR 모델 비교 검증 | Tesseract vs PaddleOCR |
| **3일차** | UI 디자인 및 백엔드 구축 | React + FastAPI 연동 |
| **4일차** | 글레어 제거 알고리즘 고도화 | OpenCV 파이프라인 최적화 |
| **5일차** | 최종 테스트 및 시연 영상 제작 | 통합 테스트 |
| **6일차** | 프로젝트 발표 | 최종 결과물 공유 |
