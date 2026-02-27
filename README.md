# 🎓 PassGuard: 온프레미스 기반 수능 OCR 성적 분석 및 합격 예측 플랫폼

![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)
![Kubernetes](https://img.shields.io/badge/Kubernetes-On--Premise-326CE5?logo=kubernetes)
![ArgoCD](https://img.shields.io/badge/GitOps-ArgoCD-EF7B4D?logo=argo)
![k6](https://img.shields.io/badge/k6-Load_Tested-7D64FF?logo=k6)

> **"클라우드 벤더(AWS, GCP) 종속성을 완벽히 탈피한, 100% 온프레미스 고가용성(HA) 입시 플랫폼"**

## 📝 1. 프로젝트 개요 (Overview)

**PassGuard**는 수능 성적표 이미지를 OCR로 인식하여 수험생의 등급과 백분위를 자동 추출하고, 전년도 입결 데이터를 기반으로 합격 확률이 높은 대학을 추천하는 **통합 입시 플랫폼**입니다. 

본 프로젝트는 단순히 애플리케이션을 개발하는 것을 넘어, **클라우드 환경 없이도 엔터프라이즈급 고가용성과 무중단 확장성을 보장하는 인프라를 바닥부터 직접 설계하고 증명**하는 데 목적이 있습니다.

---

## 🔥 2. 핵심 엔지니어링 성과 (Core Engineering Highlights)

* **🚫 AWS-Free 온프레미스 생태계 구축:** * AWS ELB 대신 **MetalLB(L2 Mode)**를 활용하여 고정 VIP 라우팅 구현.
  * AWS EBS 대신 **Longhorn**을 활용하여 물리 노드 장애 시에도 데이터가 복제/유지되는 분산 블록 스토리지 구축.
  * AWS S3 대신 **MinIO**를 구축하여 성적표 원본 이미지의 객체 스토리지 계층 확보.
* **⚡ MSA 기반 리소스 및 병목 최적화:** * I/O 중심의 API 로직(NestJS)과 CPU 연산 집약적인 OCR/ML 분석(FastAPI)을 물리적 Pod으로 완전히 분리하여 시스템 전체의 처리량(Throughput) 극대화.
* **🔄 GitOps 기반 100% 배포 자동화:** * **ArgoCD**를 도입하여 Kubernetes 매니페스트를 선언적 코드로 관리하며, 운영자의 수동 배포(kubectl apply)로 인한 휴먼 에러를 원천 차단.
* **📈 정량적 성능 검증 및 자율 확장 (HPA):** * **k6** 부하 테스트를 통해 동시 접속자 1,000명 투입. CPU 60% 도달 시 **20초 내 Pod 2개 → 6개 자동 확장** 성공 및 평균 응답 속도 **320ms** 방어 입증.

---

## 🛠️ 3. 기술 스택 (Tech Stack)

각 기술의 상세한 도입 배경과 아키텍처 결정 이유는 해당 기술명을 클릭하여 **[Wiki: 기술 선정 근거]**에서 확인하실 수 있습니다.

### 💻 Application Layer
- **Frontend:** [React (Next.js)](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#1-frontend), [TailwindCSS](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#2-tailwindcss)
- **Backend:** [NestJS](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#3-nestjs), [Python FastAPI](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#4-fastapi)

### 🤖 AI & OCR Layer
- **OCR Engine:** [PaddleOCR](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#5-paddleocr)
- **Algorithm:** [XGBoost](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#6-xgboost)

### 📊 Data & Storage Layer
- **Database:** [PostgreSQL](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#7-postgresql), [Redis](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#8-redis)
- **Storage:** [MinIO](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#9-minio), [Longhorn](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#10-longhorn)

### 🚀 Infrastructure & DevOps Layer
- **Orchestration:** [Kubernetes](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#11-kubernetes)
- **GitOps:** [ArgoCD & GitLab CI](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#12-argocd--gitlab-ci)
- **Traffic Control:** [HPA](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#13-hpa), [MetalLB](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#14-metallb), [Ingress-Nginx](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#15-ingress-nginx)

### 📈 Test & Monitoring Layer
- **Testing:** [k6](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#16-k6)
- **Observability:** [Prometheus & Grafana](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#17-prometheus--grafana), [ELK Stack](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack#18-elk-stack)

---

## 🏗️ 4. 인프라 아키텍처 (Infrastructure Architecture)



> *사용자 트래픽(Ingress)부터 백엔드 처리(NestJS), 비동기 워커 연산(FastAPI), 그리고 데이터 영속성 보장(Longhorn/PostgreSQL)까지 온프레미스 내에서 자생적으로 동작하는 아키텍처입니다.*

---

## 📚 5. 상세 문서 및 백서 (Wiki Navigation)

본 프로젝트의 심도 깊은 설계 고민, 트러블슈팅, 그리고 운영 정책은 Wiki에 상세히 기록되어 있습니다.

* [🏠 Wiki Home: 프로젝트 백서 및 시스템 방향성 바로가기](https://github.com/rkskekfk0714/passguard/wiki/Home)
* [📐 Architecture & Tech: 아키텍처 변천사 및 기술 선정 근거](https://github.com/rkskekfk0714/passguard/wiki/Tech-Stack)
* [🗄️ Database & Logic: 파티셔닝 DB 설계 및 연도 자동화(Zero-Ops) 알고리즘](https://github.com/rkskekfk0714/passguard/wiki/System-Design)
* [🔥 Performance & HPA: k6 트래픽 엔지니어링 및 부하 테스트 리포트](https://github.com/rkskekfk0714/passguard/wiki/Performance-Test)
* [🔄 GitOps Pipeline: 무중단 배포 및 자동화 파이프라인 구조](https://github.com/rkskekfk0714/passguard/wiki/GitOps-Pipeline)
* [🛡️ Security & DR: K8s 보안 정책 및 스토리지 장애 자가 복구 시나리오](https://github.com/rkskekfk0714/passguard/wiki/Security-DR)
