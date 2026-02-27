# 🎓 PassGuard: 온프레미스 기반 수능 OCR 성적 분석 및 합격 예측 플랫폼

<div align="center">

![Version](https://img.shields.io/badge/version-1.0.0-000000.svg?style=for-the-badge)
![AWS Free](https://img.shields.io/badge/AWS_Free-100%25_Bare--Metal-FF9900?style=for-the-badge&logo=linux&logoColor=white)
![Architecture](https://img.shields.io/badge/Architecture-Event--Driven_MSA-8A2BE2?style=for-the-badge)
![Zero Ops](https://img.shields.io/badge/Zero_Ops-Automated_Partitioning-success?style=for-the-badge)

<br>

![Kubernetes](https://img.shields.io/badge/kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![ArgoCD](https://img.shields.io/badge/argocd-EF7B4D?style=for-the-badge&logo=argo&logoColor=white)
![GitLab CI](https://img.shields.io/badge/gitlab_ci-FC6D26?style=for-the-badge&logo=gitlab&logoColor=white)
![MetalLB](https://img.shields.io/badge/MetalLB-BGP_Routing-02569B?style=for-the-badge)
![NGINX Ingress](https://img.shields.io/badge/nginx_ingress-009639?style=for-the-badge&logo=nginx&logoColor=white)

<br>

![NestJS](https://img.shields.io/badge/nestjs-E0234E?style=for-the-badge&logo=nestjs&logoColor=white)
![FastAPI](https://img.shields.io/badge/fastapi-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/rabbitmq-FF6600?style=for-the-badge&logo=rabbitmq&logoColor=white)
![Python](https://img.shields.io/badge/python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![XGBoost](https://img.shields.io/badge/XGBoost-Prediction_ML-12B5CB?style=for-the-badge&logo=scikit-learn&logoColor=white)
![PaddleOCR](https://img.shields.io/badge/PaddleOCR-Vision_AI-FF4500?style=for-the-badge)

<br>

![PostgreSQL](https://img.shields.io/badge/postgresql-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/redis(Rate_Limiter)-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![MinIO](https://img.shields.io/badge/minio(Object)-C7202C?style=for-the-badge&logo=minio&logoColor=white)
![Longhorn](https://img.shields.io/badge/Longhorn(Block_HA)-00A2D8?style=for-the-badge)

<br>

![Prometheus](https://img.shields.io/badge/prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white)
![k6](https://img.shields.io/badge/k6_Load_Test-7D64FF?style=for-the-badge&logo=k6&logoColor=white)
![ElasticSearch](https://img.shields.io/badge/elasticsearch-005571?style=for-the-badge&logo=elasticsearch&logoColor=white)

<br>

![Next.js](https://img.shields.io/badge/next.js-000000?style=for-the-badge&logo=nextdotjs&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/tailwindcss-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)

</div>
<!--
> **"수험생에게는 정보 비대칭을 허무는 정밀한 입시 예측을, 비즈니스적으로는 AI 파싱-데이터 분석-결제 트랜잭션이 선순환하는 통합 플랫폼 생태계를, 엔지니어링적으로는 퍼블릭 클라우드의 안락함을 버리고 극한의 부하를 견뎌낸 100% 온프레미스(Bare-Metal) 고가용성(HA) 아키텍처를 증명합니다."**
-->

> **"수험생에게는 데이터 기반의 정확한 입시 예측을, 엔지니어링적으로는 클라우드 종속성을 탈피한 100% 온프레미스 고가용성(HA) 아키텍처를 증명하는 프로젝트입니다."**

## 📝 1. 프로젝트 개요 및 주요 기능

수능 성적표 이미지를 OCR로 분석하여 작년도 입결 기준 대학 및 학과를 추천하고, 합격 확률 예측, 지원자 수 예측, 실제 모의 지원 및 결제까지 제공하는 **통합 입시 플랫폼**입니다.

### 🎯 핵심 서비스 기능
- 📸 **수능 성적표 이미지 업로드 및 OCR 자동 성적 분석**
- 🎯 **작년도 입결 데이터 기준 합격 유력 대학 3순위 추천**
- 📊 **XGBoost 알고리즘 기반 합격 확률 및 지원자 수 예측**
- 💳 **PG 연동 결제 후 상세 분석 리포트 공개**
- 🏫 **대학 모의 지원 및 실시간 가장 많이 지원한 대학 랭킹 제공**

---

## 🔥 2. 핵심 엔지니어링 성과

* **🚫 AWS-Free 온프레미스 생태계 구축:** MetalLB, Longhorn, MinIO를 활용하여 순수 자체 인프라만으로 고가용성(HA) 환경 구현.
* **⚡ MSA 기반 리소스 최적화:** I/O 중심의 Backend(NestJS)와 CPU 집약적인 OCR/ML 워커(FastAPI)를 분리하여 시스템 처리량 극대화.
* **🔄 GitOps 기반 자동화:** ArgoCD를 통해 모든 인프라를 선언적 코드로 관리(Zero-Ops).
* **📈 HPA + k6 부하 테스트 검증:** 동시 접속 1,000명 투입 시 **CPU 60% 초과 상황에서 Pod 2개 → 6개 자동 증가** 및 **평균 응답속도 320ms 유지** 증명.

---

## 🛠️ 3. 기술 스택 (Tech Stack)

각 기술의 상세한 도입 배경과 아키텍처 결정 이유는 **[👉 Wiki: 기술 선정 근거 및 아키텍처 상세]**에서 확인하실 수 있습니다. (항목 클릭 시 이동)

### 💻 [Application Layer](https://github.com/msp-architect-2026/kim-sugyeong/wiki/Tech-Stack#-application-layer)
- **Frontend:** React (Next.js), TailwindCSS
- **Backend:** NestJS (Node.js), Python FastAPI (OCR 및 추천 알고리즘 서버)

### 🤖 [AI & OCR Layer](https://github.com/msp-architect-2026/kim-sugyeong/wiki/Tech-Stack#-ai--ocr-layer)
- **OCR Engine:** PaddleOCR
- **Algorithm:** XGBoost

### 📊 [Data & Storage Layer](https://github.com/msp-architect-2026/kim-sugyeong/wiki/Tech-Stack#-data--storage-layer)
- **Database:** PostgreSQL (RDB), Redis (In-Memory Cache)
- **Storage:** MinIO (S3 대체), Longhorn (분산 Block Storage)

### 🚀 [Infrastructure & DevOps Layer](https://github.com/msp-architect-2026/kim-sugyeong/wiki/Tech-Stack#-infrastructure--devops-layer)
- **Orchestration:** Kubernetes (kubeadm)
- **GitOps & CI/CD:** ArgoCD, GitLab CI
- **Traffic Control:** HPA, MetalLB, Ingress-Nginx (Nginx Proxy Manager 연동)

### 📈 [Test & Monitoring Layer](https://github.com/msp-architect-2026/kim-sugyeong/wiki/Tech-Stack#-test--monitoring-layer)
- **Testing:** k6
- **Observability:** Prometheus, Grafana, ELK Stack

---

## 🖥️ 4. 화면 구성 및 API 설계

서비스의 핵심 비즈니스 로직을 처리하는 화면과 RESTful API 명세입니다. 세부 파라미터 및 설계 의도는 [👉 Wiki: 화면 및 API 설계서](https://github.com/msp-architect-2026//kim-sugyeong/wiki/Service%E2%80%90Design)에 명세되어 있습니다.

**[주요 화면 흐름]**
1. 로그인/회원가입 ➔ 2. 성적표 업로드 ➔ 3. 추천 결과(요약) 페이지 ➔ 4. 결제 페이지 ➔ 5. 결제 후 상세 분석 리포트 및 대학 지원 ➔ 6. 지원자 실시간 랭킹 ➔ 7. 관리자 대시보드

**[핵심 API Endpoint]**
* `POST /score/upload` : 성적표 이미지 업로드 및 OCR 파싱 요청
* `GET /recommendations` : 합격 확률 기반 3순위 대학 추천
* `POST /payment/confirm` : 결제 검증 및 상세 데이터 열람 권한 부여
* `POST /application` : 특정 대학/학과 모의 지원 트랜잭션 처리
* `GET /ranking/university` : Redis 기반 실시간 지원자 랭킹 서빙

---

## 🏗️ 5. 시스템 아키텍처 및 ERD

(추후 추가 예정)
<!--
[Image of On-premise Kubernetes Architecture for OCR Admission System with ArgoCD and HPA]

* **Nginx Proxy Manager (SSL 종료):** 외부 HTTPS 트래픽의 암복호화를 전담하여 클러스터 내부 부하 경감.
* **Microservices:** Frontend Pod, Backend Pod(비즈니스), OCR Pod(이미지 파싱), ML Recommendation Pod(합격 예측)의 완벽한 역할 분리.
-->

---

## 📚 6. 상세 문서 및 백서 (Wiki Navigation)

본 프로젝트의 심도 깊은 설계 고민과 트러블슈팅, 운영 정책은 Wiki에 상세히 기록되어 있습니다.

* [🏠 Wiki Home: 프로젝트 백서 및 시스템 방향성](https://github.com/msp-architect-2026/kim-sugyeong/wiki/Home)
* [📐 Architecture & Tech: 아키텍처 및 기술 선정 근거](https://github.com/msp-architect-2026/kim-sugyeong/wiki/Tech-Stack)
* [🗄️ Database & Logic: 파티셔닝 ERD 및 연도 자동화(Zero-Ops)](https://github.com/msp-architect-2026/kim-sugyeong/wiki/System-Design)
* [📱 UI/UX & API Spec: 7단계 화면 구성 및 REST API 명세서](https://github.com/msp-architect-2026/kim-sugyeong/wiki/Service-Design)
* [🔥 Performance & HPA: k6 트래픽 엔지니어링 및 320ms 방어 리포트](https://github.com/msp-architect-2026/kim-sugyeong/wiki/Performance-Test)
* [🔄 GitOps Pipeline: 무중단 배포 및 자동화 파이프라인 구조](https://github.com/msp-architect-2026/kim-sugyeong/wiki/GitOps-Pipeline)
* [🛡️ Security & DR: K8s 보안 정책 및 스토리지 장애 자가 복구 시나리오](https://github.com/msp-architect-2026/kim-sugyeong/wiki/Security-DR)
