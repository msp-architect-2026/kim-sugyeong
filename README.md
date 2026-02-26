# 🎓 PassGuard: 온프레미스 기반 수능 OCR 성적 분석 및 합격 예측 플랫폼

![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)
![Kubernetes](https://img.shields.io/badge/Kubernetes-On--Premise-326CE5?logo=kubernetes)
![ArgoCD](https://img.shields.io/badge/GitOps-ArgoCD-EF7B4D?logo=argo)
![LoadTest](https://img.shields.io/badge/k6-Load_Tested-7D64FF?logo=k6)

---

# 🎓 수능 OCR 기반 대학 추천·지원 플랫폼

## 1. 프로젝트 개요

수능 성적표 이미지를 OCR로 분석하여 작년도 입결 기준 대학 및 학과를 추천하고,
합격 확률 예측, 지원자 수 예측, 실제 지원 및 결제까지 제공하는 플랫폼입니다.

### 주요 기능

- 📸 수능 성적표 이미지 업로드
- 🔍 OCR 자동 성적 분석
- 🎯 작년도 기준 대학 3순위 추천
- 📊 합격 확률 예측
- 💳 결제 후 상세 분석 공개
- 🏫 대학 지원 기능
- 📈 대학별 지원자 수 예측
- 🏆 가장 많이 지원한 대학 랭킹 제공
- 🚀 HPA + k6 부하 테스트 검증

---

## 🛠️ 2. 기술 스택 (Tech Stack)

각 기술의 상세한 도입 배경과 아키텍처 결정 이유는 [Wiki: 기술 스택 도입 배경](https://github.com/사용자계정/passguard/wiki/Tech-Stack)에서 확인하실 수 있습니다.

### [Frontend](https://github.com/사용자계정/passguard/wiki/Tech-Stack#1-frontend)
- React (Next.js)
- TailwindCSS

### [Backend](https://github.com/사용자계정/passguard/wiki/Tech-Stack#2-backend)
- NestJS (Node.js)
- Python FastAPI (AI 및 OCR 워커 서버)

### [AI / OCR](https://github.com/사용자계정/passguard/wiki/Tech-Stack#3-ai--ocr)
- PaddleOCR
- XGBoost (합격 예측 알고리즘)

### [Database & Storage](https://github.com/사용자계정/passguard/wiki/Tech-Stack#4-database--storage)
- PostgreSQL (RDB)
- Redis (In-Memory Cache)
- MinIO (S3 대체 오브젝트 스토리지)

### [DevOps & Infra](https://github.com/사용자계정/passguard/wiki/Tech-Stack#5-devops--infra)
- Kubernetes (kubeadm)
- ArgoCD (GitOps 자동 배포)
- MetalLB & Ingress-Nginx
- HPA (Horizontal Pod Autoscaler)

### [Test & Monitoring](https://github.com/사용자계정/passguard/wiki/Tech-Stack#6-test--monitoring)
- k6 (부하 테스트)
- Prometheus + Grafana
- ELK Stack (로그 수집 및 분석)

---

