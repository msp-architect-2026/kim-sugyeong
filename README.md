# 🎓 수능 OCR 기반 대학 추천 및 지원 통합 플랫폼 (Web)

## 1. 🎯 프로젝트 개요
본 프로젝트는 수능 성적표 이미지를 OCR로 분석하여 작년도 입결 기준 대학 및 학과를 추천하고, 합격 확률 예측, 실시간 지원 현황 분석 및 실제 지원/결제까지 제공하는 **온프레미스 기반 고가용성 웹 플랫폼**입니다.

### 핵심 목표
* **클라우드 배제 (No AWS):** Ubuntu 기반 Bare-metal 환경에서 Kubernetes 클러스터를 직접 구축 및 운영하여 인프라 자생력 확보.
* **고가용성 검증:** HPA와 k6 부하 테스트를 통해 수능 시즌의 폭발적인 트래픽 대응 능력을 데이터로 증명.
* **데이터 자동화:** OCR 기술을 통한 성적 입력 자동화 및 연도별 입결 데이터 자동 갱신 로직 구현으로 운영 효율 극대화.

---

## 2. 🛠 기술 스택 (Tech Stack)

| 구분 | 기술 스택 |
| :--- | :--- |
| **Frontend** | React (Next.js), Tailwind CSS, Recoil |
| **Backend** | NestJS (Main API), Python FastAPI (OCR/AI Service) |
| **OCR** | PaddleOCR (한글 및 숫자 특화 엔진) |
| **Database** | PostgreSQL (Relational), Redis (Caching) |
| **Storage** | MinIO (On-premise Object Storage - S3 대체) |
| **DevOps** | Kubernetes (kubeadm), ArgoCD (GitOps), MetalLB, Nginx Ingress |
| **Monitoring** | Prometheus, Grafana, ELK Stack (Log Management) |
| **Testing** | k6 (Load Testing), HPA (Auto Scaling) |

---

## 3. ✨ 주요 기능 (Key Features)
* **📸 OCR 성적 분석:** PaddleOCR을 활용하여 성적표에서 등급, 표준점수, 백분위 자동 추출.
* **🎯 대학 3순위 추천:** 전년도 입결 데이터와 매칭하여 **상향/적정/하향 3순위** 대학 및 학과 추천.
* **📅 연도 자동 갱신:** 매년 시스템 날짜를 기준으로 추천 기준 연도를 자동으로 '작년' 데이터로 업데이트.
* **📊 합격 확률 예측:** 대학별 실시간 지원자 수 예측 및 개인별 합격 확률 게이지 시각화.
* **💳 결제 시스템:** 상세 분석 리포트 열람 및 입학 지원 기능을 위한 안전한 결제 모듈 연동.
* **🏆 인기 랭킹:** 플랫폼 내에서 가장 많이 지원한 대학 및 학과 리스트 실시간 순위 노출.

---

## 4. 📱 화면 구성 (UI/UX)
1. **로그인/회원가입:** JWT 기반 보안 인증 시스템.
2. **성적표 업로드:** 이미지 업로드 프리뷰 및 OCR 인식 결과(등급/표수) 확인/수정 인터페이스.
3. **추천 결과 페이지:** 등급 기반 추천 대학 카드 리스트 및 필터링 기능.
4. **결제 페이지:** 상세 분석 권한 획득 및 서비스 이용을 위한 결제 프로세스.
5. **대학 지원 페이지:** 최종 모의 지원서 작성 및 실시간 지원 현황 확인.
6. **실시간 랭킹 보드:** 대학별 지원자 추이 및 인기 학과 시각화 차트.
7. **관리자 대시보드:** 서버 리소스(Prometheus 연동) 및 결제/지원 통계 확인.

---

## 5. 🔌 API 설계 (API Design)

| 분류 | 메서드 | 엔드포인트 | 설명 | 필수 권한 |
| :--- | :--- | :--- | :--- | :--- |
| **인증** | `POST` | `/auth/signup` | 회원가입 및 유저 생성 | None |
| **OCR** | `POST` | `/score/upload` | 성적표 이미지 분석 및 데이터 추출 | User |
| **추천** | `GET` | `/recommendations` | 등급별 3순위 추천 대학 조회 | User |
| **결제** | `POST` | `/payment/request` | 결제 고유 번호 및 요청 생성 | User |
| **지원** | `POST` | `/application` | 대학 입학/모의 지원 신청 접수 | Paid User |
| **랭킹** | `GET` | `/ranking/university` | 실시간 지원자 수 기준 대학 랭킹 | None |

---

## 6. 💾 데이터베이스 설계 (ERD)

<!--

```mermaid
erDiagram
    USER ||--o{ SCORE_CARD : "uploads"
    USER ||--o{ PAYMENT : "makes"
    USER ||--o{ APPLICATION : "applies"
    
    USER {
        int id PK
        string email
        string password
    }
    SCORE_CARD {
        int id PK
        int user_id FK
        string image_url "MinIO Path"
        int grade_korean
        int grade_math
        int grade_english
    }
    UNIVERSITY {
        int id PK
        string univ_name
        string dept_name
        int last_year_cut "작년도 커트라인"
        int target_year "기준 연도"
    }
    APPLICATION {
        int id PK
        int user_id FK
        int univ_id FK
        datetime applied_at
    }
    PAYMENT {
        int id PK
        int user_id FK
        int amount
        string status "READY / PAID / CANCEL"
    }
-->

---

## 🏗 7. 인프라 아키텍처 (Infra Architecture)
아키텍처 설계 및 배치 안내
* **README 배치:** 위 섹션에는 시스템의 전체 흐름을 보여주는 다이어그램 이미지를 첨부합니다.

* **Wiki 배치:** Wiki - Infra Architecture 페이지에는 다음 상세 내용을 작성합니다.

* **Network:** MetalLB를 통한 외부 IP 할당 과정 및 Nginx Ingress Controller 라우팅 룰.

* **Storage:** MinIO의 Persistent Volume Claim(PVC) 설정 및 데이터 영속성 확보 방안.

* **CD:** ArgoCD의 Application 매니페스트 설정 및 GitLab Webhook 연동 구조.

---

## 🚀 8. HPA & 부하 테스트 결과 (Testing)
수능 성적 발표 및 원서 접수 기간의 트래픽 폭주 상황을 시뮬레이션하여 시스템의 안정성을 검증했습니다.

* **테스트 도구:** k6 (Spike Test 시나리오 적용)

* **테스트 시나리오:** 동시 접속자 1,000명이 랭킹 조회 및 OCR 이미지 업로드 API를 지속적으로 호출.

**검증 결과:**

* **HPA 동작:** CPU 점유율이 60%를 초과하는 시점에 즉각적으로 파드(Pod) 수가 2개에서 6개로 자동 확장되는 것을 Grafana로 확인.

* **응답 속도:** 스케일 아웃 이후 시스템 부하가 분산되며 평균 응답속도 320ms를 안정적으로 유지.

* **자원 최적화:** 트래픽 감소 시 다시 파드 수가 줄어드는 것을 확인하여 온프레미스 자원 효율성 검증 완료.



<!--
# 🎓 수능 OCR 기반 대학 추천 및 지원 통합 플랫폼 (Web)

## 1. 프로젝트 개요
수능 성적표 이미지를 OCR로 분석하여 작년도 입결 기준 대학 및 학과를 추천하고, 합격 확률 예측, 실시간 지원 현황 분석 및 실제 지원/결제까지 제공하는 **온프레미스 기반 고가용성 웹 플랫폼**입니다.

### 🎯 프로젝트 핵심 목표
* **클라우드 배제 (No AWS):** Ubuntu 기반 Bare-metal 환경에서 Kubernetes 클러스터를 직접 구축 및 운영
* **고가용성 검증:** HPA와 k6 부하 테스트를 통해 수능 시즌의 폭발적인 트래픽 대응 능력 확보
* **데이터 자동화:** OCR 기술을 통한 성적 입력 자동화 및 연도별 입결 데이터 자동 갱신 로직 구현

---

## 2. 기술 스택 (Tech Stack)

### Frontend
* **React (Next.js):** 반응형 웹 인터페이스 및 SSR(Server-Side Rendering) 구현

### Backend (MSA)
* **NestJS (Node.js):** 메인 API 서버, 인증, 결제 트랜잭션 및 비즈니스 로직 처리
* **Python FastAPI:** PaddleOCR 엔진 구동 및 추천 알고리즘 전용 마이크로서비스

### Database & Storage
* **PostgreSQL:** 유저 정보, 입결 데이터, 지원 내역 등 관계형 데이터 관리
* **Redis:** 실시간 인기 대학 랭킹 캐싱 및 세션 데이터 관리
* **MinIO:** 온프레미스 Object Storage (성적표 이미지 보관용 S3 대체제)

### DevOps & Infrastructure
* **Orchestration:** Kubernetes (kubeadm), ArgoCD (GitOps 기반 무중단 배포)
* **Network:** Nginx Proxy Manager (SSL), MetalLB, NGINX Ingress Controller
* **Testing/Monitoring:** k6 (부하 테스트), Prometheus & Grafana (관제), ELK Stack (로그 분석), HPA

---

## 3. 주요 기능 (Key Features)
* **📸 OCR 성적 분석:** PaddleOCR을 활용하여 성적표에서 과목별 등급, 표준점수, 백분위 자동 추출
* **🎯 대학 3순위 추천:** 전년도 입결 데이터를 기반으로 사용자 성적에 맞는 **상향/적정/하향 3순위** 대학 및 학과 자동 추천
* **📅 기준 연도 자동 갱신:** 시스템 날짜를 기준으로 매년 추천 기준 연도를 자동으로 전년도로 업데이트하는 스케줄러 기능
* **📊 진학 예측:** 대학별 실시간 지원자 수 예측 및 개인별 합격 확률 게이지 시각화
* **💳 결제 시스템:** 상세 분석 리포트 열람 및 대학 입학 지원을 위한 안전한 결제 모듈 연동
* **🏆 실시간 인기 랭킹:** 플랫폼 내에서 가장 많이 지원한 대학 및 학과 리스트 실시간 노출

---

## 4. 화면 구성 (UI/UX)
1. **로그인/회원가입:** JWT 기반 보안 인증 시스템
2. **성적표 업로드:** 이미지 업로드 프리뷰 및 OCR 인식 결과(등급/표수) 확인 및 수정 인터페이스
3. **추천 결과 페이지:** 등급 기반 추천 대학 카드 리스트 및 상향/적정 필터링 기능
4. **결제 페이지:** 서비스 이용권 및 대학 지원료 결제 프로세스
5. **대학 지원 페이지:** 최종 모의 지원서 작성 및 접수 현황 확인
6. **실시간 랭킹 보드:** 대학별 지원자 추이 및 인기 학과 차트 시각화
7. **관리자 대시보드:** 서버 리소스(Prometheus 연동) 및 결제 통계 모니터링

---

## 5. API 설계 (API Design)

| 분류 | 메서드 | 엔드포인트 | 설명 | 필수 권한 |
| :--- | :--- | :--- | :--- | :--- |
| **인증** | `POST` | `/auth/signup` | 회원가입 | None |
| **OCR** | `POST` | `/score/upload` | 성적표 이미지 분석 요청 | User |
| **추천** | `GET` | `/recommendations` | 등급별 3순위 추천 대학 조회 | User |
| **결제** | `POST` | `/payment/request` | 결제 고유 번호 및 요청 생성 | User |
| **지원** | `POST` | `/application` | 대학 입학/모의 지원 신청 | Paid User |
| **랭킹** | `GET` | `/ranking/university` | 실시간 지원자 수 기준 랭킹 조회 | None |

---

## 6. 데이터베이스 설계 (ERD)

```mermaid
erDiagram
    USER ||--o{ SCORE_CARD : "uploads"
    USER ||--o{ PAYMENT : "makes"
    USER ||--o{ APPLICATION : "applies"
    
    USER {
        int id PK
        string email
        string password
    }
    SCORE_CARD {
        int id PK
        int user_id FK
        string image_url
        int grade_info
    }
    UNIVERSITY {
        int id PK
        string univ_name
        string dept_name
        int last_year_cut
        int target_year
    }
    APPLICATION {
        int id PK
        int user_id FK
        int univ_id FK
        datetime applied_at
    }
    PAYMENT {
        int id PK
        int user_id FK
        int amount
        string status
    }

-->







<!--
# 🎓 UniPredict: 수능 OCR 기반 대학 추천 및 지원 플랫폼

[![Wiki](https://img.shields.io/badge/📖_Project_Wiki-Go_to_Wiki-blue?style=for-the-badge)](wiki/Home)
[![On-Premise](https://img.shields.io/badge/Infrastructure-On--Premise-orange?style=for-the-badge)]()
[![Kubernetes](https://img.shields.io/badge/Kubernetes-kubeadm-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)]()

> **작년도 입결 데이터와 AI OCR을 결합한 지능형 대학 진학 컨설팅 및 모의 지원 플랫폼**
> 수능 성적표 이미지를 분석하여 합격 확률을 예측하고, 실제 지원 시뮬레이션 및 실시간 랭킹을 제공합니다. 본 프로젝트는 클라우드 의존 없이 **순수 온프레미스 K8s 환경**에서 구축되었으며, **HPA와 k6**를 통한 트래픽 부하 검증을 완료했습니다.

---

## 📌 1. 프로젝트 개요 (Project Overview)
수험생의 수능 성적표 이미지를 OCR로 자동 분석하여 작년도 입시 결과(입결)를 기준으로 맞춤형 대학 및 학과를 추천합니다. 예측, 실시간 랭킹, 결제, 지원까지 이어지는 원스톱 서비스를 제공합니다.

### ✨ 주요 기능
- 📸 **AI 성적 인식**: 수능 성적표 이미지 업로드 및 OCR 자동 분석
- 🎯 **맞춤형 추천**: 작년도 입결 기준 합격 가능성 높은 대학 3순위 추천
- 📊 **합격 예측**: 지원 희망 학과의 예상 합격 확률 제공
- 💳 **프리미엄 리포트**: 결제 후 상세 분석 데이터 및 석차 공개
- 🏫 **원스톱 지원**: 대학/학과 지원 기능 및 지원자 수 예측
- 🏆 **실시간 트렌드**: 가장 많이 지원(검색)한 대학 인기 랭킹 제공
- 🚀 **인프라 검증**: HPA + k6 기반 대규모 부하 테스트 통과

🔗 **[📖 Wiki: 프로젝트 기획 및 요구사항 정의서 바로가기](wiki/Project-Overview)**

---

## 🛠 2. 기술 스택 (Tech Stack)

| 영역 | 기술 (Technologies) |
| :--- | :--- |
| **Frontend** | React (Next.js) |
| **Backend** | NestJS (Node.js), Python FastAPI (추천 알고리즘) |
| **AI / OCR** | PaddleOCR |
| **Database** | PostgreSQL, Redis, MinIO (S3 대체 객체 스토리지) |
| **DevOps** | Kubernetes (kubeadm), ArgoCD (GitOps) |
| **Monitoring/QA** | Prometheus, Grafana, ELK Stack, HPA, k6 |

🔗 **[📖 Wiki: 기술 스택 선정 배경 및 아키텍처 결정 기록 바로가기](wiki/Tech-Stack)**

---

## 🖥 3. 화면 구성 (UI / UX)

1. **로그인 / 회원가입**: JWT 기반 사용자 인증
2. **성적표 업로드**: Drag & Drop 및 실시간 OCR 진행률 UI
3. **추천 결과 페이지**: 등급/표점 기반 추천 대학 Top 3 카드뷰
4. **결제 페이지**: 프리미엄 상세 분석 잠금 해제
5. **대학 지원 페이지**: 가상 원서 접수 및 경쟁률 시뮬레이터
6. **지원자 랭킹 페이지**: 실시간 지원 핫트렌드 차트 (Redis 캐싱)
7. **관리자 대시보드**: 트래픽, 결제 통계, 인프라 리소스 모니터링

🔗 **[📖 Wiki: 화면 와이어프레임 및 디자인 상세 바로가기](wiki/UI-Design)**

---

## 🔗 4. API 설계 (API Specifications)

| Domain | Method | Endpoint | Description |
| :--- | :--- | :--- | :--- |
| **Auth** | `POST` | `/auth/signup` | 신규 회원 가입 |
| | `POST` | `/auth/login` | 로그인 및 액세스 토큰 발급 |
| **OCR** | `POST` | `/score/upload` | 성적표 이미지 업로드 및 추출 |
| **Predict** | `GET` | `/recommendations` | 사용자 성적 기반 맞춤 대학 추천 |
| **Payment**| `POST` | `/payment/request` | 상세 분석 리포트 결제 요청 |
| | `POST` | `/payment/confirm` | 결제 승인 및 프리미엄 권한 부여 |
| **Apply** | `POST` | `/application` | 특정 대학 및 학과 모의 지원 |
| **Rank** | `GET` | `/ranking/university` | 실시간 누적 지원자 수 기준 랭킹 |

🔗 **[📖 Wiki: API Request/Response 상세 명세서 바로가기](wiki/API-Specs)**

---

## 🗄 5. ERD (Database Diagram)

```mermaid
erDiagram
    USERS ||--o{ SCORES : "uploads"
    USERS ||--o{ PAYMENTS : "makes"
    USERS ||--o{ APPLICATIONS : "submits"
    SCORES ||--|| PREDICTIONS : "generates"
    UNIVERSITIES ||--o{ DEPARTMENTS : "contains"
    DEPARTMENTS ||--o{ APPLICATIONS : "receives"

    USERS {
        uuid user_id PK
        string email
        boolean is_premium
    }
    SCORES {
        int id PK
        uuid user_id FK
        string img_url
        json ocr_data
    }
    DEPARTMENTS {
        int id PK
        int univ_id FK
        string name
        float last_year_cutoff
    }
    -->
