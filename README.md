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

각 기술 스택의 상세한 도입 배경과 엔지니어링 의사결정 과정(Why)은 [Wiki: 기술 선정 근거 및 아키텍처 상세](https://github.com/사용자계정/passguard/wiki/Tech-Stack)에서 확인하실 수 있습니다. (항목 클릭 시 이동)

### 💻 [Application Layer](https://github.com/사용자계정/passguard/wiki/Tech-Stack#1-application-layer)
- **Frontend:** React (Next.js), TailwindCSS (SSR 기반 빠른 초기 렌더링 및 실시간 UI)
- **Backend:** NestJS (Node.js 메인 서버), Python FastAPI (비동기 처리 특화 워커)

### 🤖 [AI & OCR Layer](https://github.com/사용자계정/passguard/wiki/Tech-Stack#2-ai--ocr-layer)
- **OCR Engine:** PaddleOCR (한국어 인식률 및 온프레미스 자원 효율 최적화)
- **Algorithm:** XGBoost (입결 데이터 기반 합격 확률 예측)

### 📊 [Data & Storage Layer](https://github.com/사용자계정/passguard/wiki/Tech-Stack#3-data--storage-layer)
- **Database:** PostgreSQL (연도별 파티셔닝 RDB), Redis (실시간 랭킹 서빙용 인메모리 캐시)
- **Storage:** MinIO (S3 호환 Object Storage), Longhorn (고가용성 분산 Block Storage)

### 🚀 [Infrastructure & DevOps Layer](https://github.com/사용자계정/passguard/wiki/Tech-Stack#4-infrastructure--devops-layer)
- **Orchestration:** Kubernetes (kubeadm v1.28+)
- **GitOps:** ArgoCD, GitLab CI
- **Traffic Control:** HPA, MetalLB, Ingress-Nginx

### 📈 [Test & Monitoring Layer](https://github.com/사용자계정/passguard/wiki/Tech-Stack#5-test--monitoring-layer)
- **Testing:** k6 (부하 및 스트레스 테스트)
- **Observability:** Prometheus, Grafana, ELK Stack (중앙 집중형 로그 수집)

---

## 🏗️ 4. 인프라 아키텍처 (Infrastructure Architecture)



> *사용자 트래픽(Ingress)부터 백엔드 처리(NestJS), 비동기 워커 연산(FastAPI), 그리고 데이터 영속성 보장(Longhorn/PostgreSQL)까지 온프레미스 내에서 자생적으로 동작하는 아키텍처입니다.*

---

## 📚 5. 상세 문서 및 백서 (Wiki Navigation)

본 프로젝트의 심도 깊은 설계 고민, 트러블슈팅, 그리고 운영 정책은 Wiki에 상세히 기록되어 있습니다.

* [🏠 Wiki Home: 프로젝트 백서 및 시스템 방향성 바로가기](./wiki/Home)
* [📐 Architecture & Tech: 아키텍처 변천사 및 기술 선정 근거](./wiki/Tech-Stack)
* [🗄️ Database & Logic: 파티셔닝 DB 설계 및 연도 자동화(Zero-Ops) 알고리즘](./wiki/System-Design)
* [🔥 Performance & HPA: k6 트래픽 엔지니어링 및 부하 테스트 리포트](./wiki/Performance-Test)
