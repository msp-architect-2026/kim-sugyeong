# 🎓 PassGuard: On-Premise AI Admission Prediction Platform
> **수능 성적표 OCR 분석 및 100% 온프레미스 고가용성 인프라 기반 합격 예측 iOS 플랫폼**

<div align="center">

![Version](https://img.shields.io/badge/version-1.0.0-000000.svg?style=for-the-badge)
![AWS Free](https://img.shields.io/badge/AWS_Free-100%25_Bare--Metal-FF9900?style=for-the-badge&logo=linux&logoColor=white)
![Platform](https://img.shields.io/badge/Platform-iOS_SwiftUI-007AFF?style=for-the-badge&logo=apple)
![Infrastructure](https://img.shields.io/badge/Infra-K8s_Bare--Metal-326CE5?style=for-the-badge&logo=kubernetes)
![GitOps](https://img.shields.io/badge/GitOps-ArgoCD-EF7B4D?style=for-the-badge&logo=argo)

<br>

![iOS](https://img.shields.io/badge/iOS-000000?style=for-the-badge&logo=ios&logoColor=white)
![SwiftUI](https://img.shields.io/badge/SwiftUI-007AFF?style=for-the-badge&logo=swift&logoColor=white)
![NestJS](https://img.shields.io/badge/nestjs-E0234E?style=for-the-badge&logo=nestjs&logoColor=white)
![FastAPI](https://img.shields.io/badge/fastapi-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/rabbitmq-FF6600?style=for-the-badge&logo=rabbitmq&logoColor=white)

<br>

![PostgreSQL](https://img.shields.io/badge/postgresql-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![Longhorn](https://img.shields.io/badge/Longhorn(Block_HA)-00A2D8?style=for-the-badge)
![Prometheus](https://img.shields.io/badge/prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![k6](https://img.shields.io/badge/k6_Load_Test-7D64FF?style=for-the-badge&logo=k6&logoColor=white)

</div>

<br>

> **"수험생에게는 모바일 기반의 정밀한 입시 예측을, 엔지니어링적으로는 퍼블릭 클라우드 종속성을 탈피한 100% 온프레미스 고가용성(HA) 아키텍처를 증명하는 프로젝트입니다."**

---

## 📝 1. 프로젝트 개요 및 주요 기능

**PassGuard**는 수능 성적표 이미지를 스마트폰 카메라로 촬영하여 AI로 분석하고, 전년도 입결 데이터를 기반으로 합격 확률과 지원자 수를 예측하는 **iOS 통합 입시 플랫폼**입니다. 클라우드 비용을 최소화하면서도 베어메탈 환경에서 엔지니어링 안정성을 확보하는 데 주력했습니다.

### 🎯 핵심 서비스 기능
* 📸 **성적표 스마트 스캔:** iOS `AVFoundation` 기반 카메라 연동 및 `PaddleOCR`을 통한 성적표 데이터(등급, 백분위, 점수) 자동 파싱.
* 🎯 **AI 맞춤형 대학 추천:** 추출된 성적과 전년도 합격 데이터셋 대조를 통해 합격 가능성이 높은 대학/학과 TOP 3 정밀 매칭.
* 📊 **XGBoost 합격 예측:** AI 모델을 활용한 구체적인 합격 확률(%) 산출 및 예상 경쟁자 규모 예측.
* 💳 **프리미엄 리포트 결제:** `StoreKit 2` 기반 인앱 결제 및 영수증 검증을 통한 상세 분석 리포트 잠금 해제.
* 🏫 **실시간 지원자 랭킹:** `Redis Sorted Set`을 활용하여 사용자들의 모의 지원 현황을 실시간 인기 대학 랭킹으로 서빙.

---

## 🔥 2. 핵심 엔지니어링 성과

* **🚫 AWS-Free 온프레미스 생태계 구축:** `MetalLB`(L2 로드밸런싱), `Longhorn`(분산 스토리지), `MinIO`(오브젝트 스토리지)를 활용하여 클라우드 서비스 없이 순수 자체 서버만으로 고가용성(HA) 환경 구현.
* **⚡ MSA 기반 워크로드 최적화:** I/O 중심의 `NestJS`(API)와 CPU/GPU 집약적인 `FastAPI`(OCR/ML) 워커 노드를 분리하고 `RabbitMQ`를 통한 비동기 메시징으로 시스템 처리량 극대화.
* **🔄 GitOps 기반 자동화(Zero-Ops):** `ArgoCD`와 `GitLab CI`를 통한 선언적 인프라 관리 및 무중단 배포(Rolling Update) 실현. DB 파티셔닝 전략으로 매년 반복되는 데이터 갱신을 자동화.
* **📈 HPA + k6 성능 방어:** 동시 접속 1,000명 투입 부하 테스트 시 **CPU 60% 임계치 도달에 따른 Pod 자동 확장(2개 → 6개)** 및 트래픽 피크 타임 **평균 응답속도 320ms 유지** 증명.



---

## 🔍 3. Deep Dive: Engineering Challenges & Solutions

### 🛡️ 온프레미스 데이터 가용성 확보 (Storage HA)
- **Challenge**: 물리 서버의 디스크 장애는 데이터 유실 및 서비스 중단으로 직결됨.
- **Solution**: `Longhorn` 분산 블록 스토리지를 도입하여 모든 데이터를 3개의 노드에 복제(3-way Replication) 저장. 특정 노드 다운 시에도 다른 노드의 복제본을 통해 60초 이내에 정상 노드로 Pod를 재스케줄링하여 무중단 서비스를 유지함을 검증.

### ⚡ Heavy Task 비동기 최적화 (Event-Driven Architecture)
- **Challenge**: PaddleOCR 분석은 연산 시간이 길어(Long-running) 동기식 처리 시 API 서버의 타임아웃 및 스레드 고갈 유발.
- **Solution**: `RabbitMQ` 기반의 메시지 큐 시스템을 구축하여 분석 요청을 비동기화. 사용자는 업로드 즉시 응답을 받고, 분석 완료 시점에 `WebSocket` 알림을 받는 구조로 설계하여 API 서버 응답 속도를 기존 대비 70% 개선.



### 🗄️ JSONB 기반 비정형 데이터 검색 고속화
- **Challenge**: OCR 파싱 결과물인 복잡한 JSON 데이터를 PostgreSQL에서 필터링 조회 시 성능 저하 발생.
- **Solution**: PostgreSQL의 **GIN(Generalized Inverted Index)** 인덱스를 적용하여 `JSONB` 타입 내 특정 필드 검색 성능 최적화. 10만 건 데이터 기준 검색 속도를 2.5초에서 150ms로 단축.

### 🔄 Zero-Downtime을 위한 Probes 튜닝
- **Challenge**: HPA 확장 시 머신러닝 모델 로딩 전의 Pod에 트래픽이 유입되어 에러 발생.
- **Solution**: `Readiness Probe`를 정교하게 설계하여 ML 모델이 메모리에 적재된 후(Ready)에만 트래픽을 허용하도록 구성. 무중단 배포 및 자동 확장 과정에서의 에러율 0% 달성.

---

## 🛠️ 4. 기술 스택 (Tech Stack)

| 레이어 | 기술 스택 | 세부 사항 |
| :--- | :--- | :--- |
| **Frontend** | `iOS (SwiftUI)`, `Combine` | 선언적 UI 및 비동기 스트림 처리 |
| **Backend** | `NestJS`, `FastAPI`, `RabbitMQ` | API 서버, ML 워커 및 메시지 브로커 |
| **Data** | `PostgreSQL`, `Redis` | Range Partitioning 및 실시간 랭킹 캐시 |
| **Infra** | `K8s`, `ArgoCD`, `MetalLB` | Bare-metal 오케스트레이션 및 GitOps |
| **Storage** | `MinIO`, `Longhorn` | 객체 스토리지 및 분산 블록 스토리지 |
| **Observability** | `Prometheus`, `Grafana`, `k6` | 실시간 메트릭 수집 및 부하 테스트 |

---

## 🖥️ 5. 화면 구성 및 API 설계

### **📱 주요 화면 흐름 (User Flow)**
1. **로그인/회원가입:** Apple/Google OAuth 연동 및 JWT 세션 관리.
2. **성적표 스캔:** 카메라 가이드를 통한 이미지 캡처 및 OCR 파싱.
3. **추천 프리뷰:** 합격 확률 기반 3순위 대학 요약 리포트 서빙.
4. **상세 분석 리포트:** XGBoost 기반 상세 합격 확률 차트 및 AI 코멘트 확인.
5. **모의 지원 & 랭킹:** 실시간 지원자 트렌드 및 지망 학위 경쟁률 대시보드.

### **🔌 핵심 API Endpoint**
* `POST /v1/score/analyze` : 성적표 이미지 업로드 및 OCR 비동기 분석 요청.
* `GET /v1/recommendations` : 내 성적 기반 대학/학과 추천 데이터 조회.
* `POST /v1/apply` : 특정 대학/학과 모의 지원 트랜잭션 처리.
* `GET /v1/ranking` : Redis 기반 초고속 실시간 지원자 랭킹 서빙.

---

## 🏗️ 6. 시스템 아키텍처 및 ERD



* **Traffic Flow:** iOS App ➔ MetalLB (VIP) ➔ Ingress-Nginx ➔ NestJS API ➔ RabbitMQ ➔ FastAPI Worker.
* **Storage HA:** Longhorn을 통한 노드 장애 시 스토리지 자가 복구 및 데이터 3중 복제 유지.

---

## 📚 7. 상세 문서 및 백서 (Wiki Navigation)

* [🏠 **Wiki Home**: 프로젝트 백서 및 시스템 방향성](#)
* [📐 **Architecture & Tech**: 온프레미스 인프라 선정 근거 및 상세 스펙](#)
* [🗄️ **Database & Logic**: 파티셔닝 ERD 및 연도 자동화 로직](#)
* [📱 **UI/UX & API Spec**: 7단계 모바일 화면 및 REST API 상세 명세](#)
* [🔥 **Performance & HPA**: k6 트래픽 엔지니어링 리포트](#)
