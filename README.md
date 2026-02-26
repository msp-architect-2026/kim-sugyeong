# 🎓 PassGuard: 온프레미스 기반 수능 OCR 성적 분석 및 합격 예측 플랫폼

![Kubernetes](https://img.shields.io/badge/Kubernetes-On--Premise-326CE5?logo=kubernetes)
![ArgoCD](https://img.shields.io/badge/GitOps-ArgoCD-EF7B4D?logo=argo)
![k6](https://img.shields.io/badge/k6-Load_Tested-7D64FF?logo=k6)


## 1. 프로젝트 개요 (Overview)
**PassGuard**는 수능 성적표 이미지를 OCR로 인식하여 수험생의 등급/백분위를 자동 추출하고, 전년도 입결 데이터를 기반으로 합격 확률이 높은 대학을 추천하는 **통합 입시 플랫폼**입니다. 클라우드 벤더(AWS 등)에 종속되지 않는 100% 온프레미스 환경에서 고가용성을 유지하도록 설계되었습니다.

---

## 2. 핵심 엔지니어링 성과 (Core Engineering Highlights)
* **AWS-Free 온프레미스 구축**: MetalLB, Longhorn, MinIO를 활용하여 순수 자체 인프라만으로 고가용성(HA) 아키텍처 구현.
* **MSA 기반 리소스 최적화**: I/O 중심의 Backend(NestJS)와 CPU 집약적인 OCR/ML(FastAPI)을 분리하여 시스템 처리량(Throughput) 극대화.
* **GitOps 기반 배포 자동화**: ArgoCD를 통해 모든 인프라를 선언적 코드로 관리하며 배포의 휴먼 에러 원천 차단.
* **정량적 성능 검증**: k6 부하 테스트를 통해 동시 접속자 1,000명 상황에서의 HPA 오토스케일링 성능 입증.

---

## 🛠️ 3. 기술 스택 (Tech Stack)

각 기술 스택의 상세한 도입 배경과 엔지니어링 의사결정 과정은 **[Wiki: Tech Stack Justification]**에서 확인하실 수 있습니다. (항목 클릭 시 이동)

### 💻 [Application Layer](https://github.com/사용자계정/repo명/wiki/Tech-Stack#1-application-layer)
- **Frontend:** React (Next.js)
- **Backend:** NestJS (Node.js), Python FastAPI

### 🤖 [AI & OCR Layer](https://github.com/사용자계정/repo명/wiki/Tech-Stack#2-ai--ocr-layer)
- **OCR Engine:** PaddleOCR
- **Algorithm:** XGBoost

### 📊 [Data & Storage Layer](https://github.com/사용자계정/repo명/wiki/Tech-Stack#3-data--storage-layer)
- **Database:** PostgreSQL, Redis
- **Storage:** MinIO, Longhorn

### 🚀 [Infrastructure & DevOps Layer](https://github.com/사용자계정/repo명/wiki/Tech-Stack#4-infrastructure--devops-layer)
- **Orchestration:** Kubernetes (kubeadm)
- **GitOps:** ArgoCD
- **Traffic Control:** HPA, MetalLB, Ingress-Nginx

### 📈 [Test & Monitoring Layer](https://github.com/사용자계정/repo명/wiki/Tech-Stack#5-test--monitoring-layer)
- **Testing:** k6
- **Observability:** Prometheus, Grafana, ELK Stack

---

## 4. 인프라 아키텍처 (Infrastructure Architecture)



## 5. 상세 문서 (Wiki)
본 프로젝트의 심도 깊은 설계 고민은 Wiki에 기록되어 있습니다.
- [Wiki Home: 프로젝트 백서 바로가기](./wiki/Home)




<!--
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

## 4. 인프라 아키텍처 (Infrastructure Architecture)



---













## 🏗️ 3. 시스템 아키텍처

- **AWS-Free On-Premise**: MetalLB와 Ingress를 통한 사설망 VIP 할당 및 라우팅.
- **Microservices**: 비즈니스 로직(NestJS)과 이미지 처리(FastAPI)의 분리를 통한 처리량 최적화.

---

## 🚀 4. 성능 검증 결과
- **Load Test**: k6를 이용한 동시 접속자 1,000명 스트레스 테스트 수행.
- **Auto Scaling**: CPU 60% 도달 시 **HPA를 통해 Pod 2개 → 6개 자동 확장**.
- **안정성**: 피크 타임 평균 응답 속도 **320ms** 유지 및 에러율 0.3% 미만.

---

## 📖 5. 상세 문서 (Wiki)
- [1. 화면 구성 및 UI/UX Flow](./Wiki-Screen-Design)
- [2. API 명세서](./Wiki-API-Specification)
- [3. 데이터베이스 설계 (ERD)](./Wiki-ERD)
- [4. 연도 자동 갱신 알고리즘 설계](./Wiki-Dynamic-Year-Sync)
- [5. 성능 테스트 및 부하 분석 리포트](./Wiki-Performance-Test)  -->
