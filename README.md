<div align="center">

# 🎓 수능 AI 대학 입시 예측 플랫폼 — snscore

**수능 성적표 이미지를 OCR로 분석하여 합격 가능 대학·학과를 자동 예측하는 AI 기반 입시 플랫폼**

<br/>

![Status](https://img.shields.io/badge/🚀_1차_데모_시연_완료-지속_고도화_진행_중-success?style=for-the-badge)

<br/>

![Platform](https://img.shields.io/badge/platform-Web%20%7C%20PWA-brightgreen)
![OCR](https://img.shields.io/badge/OCR-PaddleOCR-orange)
![Infra](https://img.shields.io/badge/Infra-On--Premise%20K8s%203Node-blueviolet)
![CI/CD](https://img.shields.io/badge/CI%2FCD-GitLabCI%20%7C%20ArgoCD%20GitOps-red)
![License](https://img.shields.io/badge/license-MIT-blue)
![Load Test](https://img.shields.io/badge/LoadTest-K6%20%7C%20HPA-yellow)

<br/>

> ⚠️ **AWS 서비스 미사용** — 전 구간 On-Premise 구성

</div>

---

## 🔗 프로젝트 핵심 링크

| 항목 | 링크 |
|------|------|
| 📋 **Wiki (설계 문서 전체)** | [Wiki Home](../../wiki) |
| 🗂 **칸반 보드 (이슈/진행 현황)** | [Project Board](../../issues) |
| 📊 **구현 완료 보고서** | [Implementation_Completion_Report.md](docs/Implementation_Completion_Report.md) |
| 🏗 **인프라 아키텍처** | [Wiki → Infra Architecture](../../wiki/Infra-Architecture) |
| 📡 **API 명세** | [Wiki → API 명세](../../wiki/API-명세) |
| 🐛 **트러블슈팅 기록** | [Wiki → 트러블슈팅](../../wiki/트러블슈팅-기록) |

---

## 📌 프로젝트 개요

수험생이 **수능 성적표 사진을 업로드**하면, EasyOCR이 과목별 등급을 자동으로 읽어내고
**전년도 입시 데이터 기반**으로 합격 가능 대학과 학과를 예측해 추천합니다.

| 단계 | 내용 |
|------|------|
| 📸 **이미지 업로드** | 카메라 촬영 또는 갤러리 파일 업로드 (React PWA) |
| 🔍 **OCR 분석** | PaddleOCR로 과목별 등급 자동 추출 |
| 🔓 **무료 제공** | 등급 확인 + 대학명 미리보기 |
| 💳 **결제 후 잠금 해제** | TOP 3 대학 추천 · 합격 확률 · 원서 접수 연동 |
| 📊 **실시간 랭킹** | 가장 많이 지원한 대학 순위 제공 |

---

## 🏗 전체 아키텍처 요약

```
[모바일/PC 브라우저]
        │ http://snscore.172.16.10.11.nip.io:30080
        ▼
[Nginx Ingress — Master 172.16.10.11:30080]
        │
        ├── /api/*  →  FastAPI Pod (EasyOCR 동기 스캔, 추천, 랭킹)
        └──  /*     →  Frontend Pod (React PWA 정적 파일)

[On-Premise K8s 3-Node Cluster]
  Master  172.16.10.11  — Control Plane + Nginx Ingress
  Worker1 172.16.10.12  — App Pods (FastAPI · Spring · Frontend)
  Worker2 172.16.10.13  — Data Pods (PostgreSQL · Harbor)

[DevOps 서버 172.16.10.10 — K8s 클러스터 외부]
  GitLab CE · GitLab Runner (Kaniko) · NFS Storage

[GitOps 자동화 흐름]
  git push develop
    → GitLab CI: 테스트 → Kaniko 이미지 빌드 → Harbor push
    → update-manifest: k8s-manifests sed 태그 교체
    → ArgoCD Auto-Sync: snscore-staging 자동 배포
```

---

## 🛠 기술 스택

### Frontend
| 기술 | 설명 |
|------|------|
| React 18 + Vite | SPA, PWA (홈화면 추가, 카메라 직접 접근) |
| TypeScript | 타입 안전성 |
| react-router-dom | 바텀 탭 네비게이션 + SPA 라우팅 |
| Axios + TanStack Query | API 통신 및 서버 상태 관리 |

### Backend
| 기술 | 역할 및 선택 이유 |
|------|----------------|
| **FastAPI** (Python 3.11) | AI/OCR 처리 — PaddleOCR 표 인식 및 구조 분석(Layout)에 특화됨 |
| **Spring Boot** (Java 17) | 결제·회원 — 트랜잭션 안정성이 중요한 엔터프라이즈 로직 분리 |
| PostgreSQL 16 | Range Partitioning으로 수능 연도별 데이터 분리 |
| Redis | 실시간 랭킹 Sorted Set + 세션 캐시 *(Issue #8 예정)* |
| Kafka (Strimzi) | OCR 비동기 처리 큐 — 트래픽 폭주 완충 *(Issue #9 예정)* |

### OCR & AI
| 기술 | 설명 |
|------|------|
| **PaddleOCR** | 한국어·영어 수능 성적표 텍스트 추출 |
| **PyTorch 2.2.0+cpu** | GPU 없는 K8s 환경 OOM 방지 — CPU-only 경량화 |
| 등급 파싱 엔진 | 정규식 기반 과목명·표준점수·백분위·등급 추출 |

### Infra (On-Premise, No AWS)
| 기술 | 설명 |
|------|------|
| **Kubernetes** (kubeadm) | 3노드 클러스터, Calico CNI |
| **ArgoCD** | GitOps 선언적 배포 (staging: Auto, production: Manual) |
| **Harbor v2.10.2** | 온프레미스 컨테이너 레지스트리 + Trivy 취약점 스캔 |
| **GitLab CI + Kaniko** | 특권 컨테이너 없는 K8s 내 이미지 빌드 |
| **Nginx Ingress** | NodePort 30080/30443, nip.io 기반 모바일 접근 |
| Prometheus + Grafana | 클러스터 모니터링 *(Issue #25~26 예정)* |
| MinIO | 성적표 이미지 오브젝트 스토리지 *(Issue #8 예정)* |
| Cert-Manager | TLS 자동 발급 (snscore-ca-issuer) |

---

## 📋 구현 현황

| 카테고리 | 항목 | 상태 |
|---------|------|------|
| **인프라** | K8s 3노드 클러스터 (Calico CNI) | ✅ 완료 |
| | Nginx Ingress + Cert-Manager | ✅ 완료 |
| | GitLab CE + Runner (Kaniko) | ✅ 완료 |
| | Harbor v2.10.2 + Robot Account | ✅ 완료 |
| | ArgoCD GitOps (staging Auto) | ✅ 완료 |
| | PostgreSQL 16 (Range Partitioning) | ✅ 완료 |
| **핵심 기능** | FastAPI OCR 스캔 (`POST /api/ocr/scan`) | ✅ 완료 |
| | React PWA 카메라/갤러리 업로드 UI | ✅ 완료 |
| | nip.io Ingress (PC + 모바일 동시 접근) | ✅ 완료 |
| **향후 예정** | Kafka 비동기 OCR 큐 | 🔲 Issue #9 |
| | Redis 실시간 랭킹 | 🔲 Issue #8 |
| | Prometheus + Grafana 모니터링 | 🔲 Issue #25~26 |
| | 결제 연동 (토스페이먼츠) | 🔲 예정 |

---

## 📋 프로젝트 문서

| 문서 | 설명 | 링크 |
|------|------|------|
| 📊 **구현 완료 보고서** | 기술 선택 근거 · 트러블슈팅 3건 · 향후 계획 | [Implementation_Completion_Report.md](docs/Implementation_Completion_Report.md) |
| 🖥 화면 구성 | UI Flow · 라우팅 구조 | [Wiki → 화면 구성](../../wiki/화면-구성) |
| 📡 API 명세 | REST API 전체 엔드포인트 | [Wiki → API 명세](../../wiki/API-명세) |
| 🗃 ERD | DB 테이블 구조 | [Wiki → ERD](../../wiki/ERD) |
| 🏗 인프라 아키텍처 | On-Premise K8s 전체 구성도 | [Wiki → Infra Architecture](../../wiki/Infra-Architecture) |
| 🧩 앱 아키텍처 | 서비스 간 통신 · 계층 구조 | [Wiki → Application Architecture](../../wiki/Application-Architecture) |
| 🚀 CI/CD 파이프라인 | GitLab Runner + ArgoCD 배포 전략 | [Wiki → CI/CD](../../wiki/CI-CD-파이프라인) |
| 🐛 트러블슈팅 기록 | 실제 장애 · 원인 분석 · 해결 과정 | [Wiki → 트러블슈팅](../../wiki/트러블슈팅-기록) |

---

<div align="center">
Made with ❤️ · No AWS · Full On-Premise K8s · 지속 고도화 진행 중
</div>








<!--

<div align="center">

# 🎓 수능 AI 대학 입시 예측 플랫폼

**수능 성적표 이미지를 OCR로 분석하여 합격 가능 대학·학과를 자동 예측하는 AI 기반 입시 플랫폼**

<br/>

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Platform](https://img.shields.io/badge/platform-Web%20%7C%20PWA-brightgreen)
![OCR](https://img.shields.io/badge/OCR-PaddleOCR-orange)
![Infra](https://img.shields.io/badge/Infra-On--Premise%20K8s-blueviolet)
![CI/CD](https://img.shields.io/badge/CI%2FCD-GitLabCI%20%7C%20ArgoCD-red)
![Load Test](https://img.shields.io/badge/LoadTest-K6%20%7C%20HPA-yellow)

<br/>

> ⚠️ **AWS 서비스 미사용** — 전 구간 On-Premise 구성

</div>

---

## 📌 프로젝트 개요

수험생이 **수능 성적표 사진을 업로드**하면, OCR이 과목별 등급을 자동으로 읽어내고  
**전년도 입시 데이터 기반**으로 합격 가능 대학과 학과를 예측해 추천합니다.

| 단계 | 내용 |
|------|------|
| 📸 **이미지 업로드** | 성적표 촬영 또는 파일 업로드 |
| 🔍 **OCR 분석** | PaddleOCR로 과목별 등급 자동 추출 |
| 🔓 **무료 제공** | 등급 확인 + 대학명 미리보기 (블러 처리) |
| 💳 **결제 후 잠금 해제** | 대학 TOP 3 추천 · 합격 확률 · 학과별 예측 · 원서 접수 연동 |
| 📊 **실시간 랭킹** | 가장 많이 지원한 대학 순위 제공 |

---

## 🛠 기술 스택

### Frontend
| 기술 | 설명 |
|------|------|
| React + Vite | SPA 구성, PWA 지원 |
| TypeScript | 타입 안전성 확보 |
| TailwindCSS | UI 스타일링 |

### Backend
| 기술 | 설명 |
|------|------|
| FastAPI (Python) | OCR 분석 · 대학 추천 API |
| Spring Boot (Java) | 회원 · 결제 · 원서 접수 |
| PostgreSQL | 메인 데이터베이스 |
| Redis | 캐시 · 실시간 랭킹 |
| Kafka | OCR 비동기 처리 큐 |

### OCR & AI
| 기술 | 설명 |
|------|------|
| PaddleOCR | 한국어 성적표 텍스트 추출 |
| XGBoost | 합격 확률 예측 모델 |

### Infra (On-Premise, No AWS)
| 기술 | 설명 |
|------|------|
| Kubernetes (K8s) | 컨테이너 오케스트레이션 |
| ArgoCD | GitOps 기반 배포 자동화 |
| HPA | 수능 시즌 트래픽 자동 스케일 아웃 |
| K6 | 부하 테스트 및 HPA 임계값 검증 |
| Harbor | 온프레미스 컨테이너 레지스트리 |
| GitLab CI | 빌드 · 테스트 · 이미지 푸시 |
| MinIO | 성적표 이미지 오브젝트 스토리지 |
| Prometheus + Grafana | 메트릭 수집 및 모니터링 |
| Nginx Ingress | 리버스 프록시 · SSL 종료 |
| Vault | 시크릿 중앙 관리 |
| Cert-Manager | TLS 인증서 자동 발급 |

---

## 📋 프로젝트 문서

> 세부 설계 문서는 **[GitHub Wiki](../../wiki)** 에서 확인하세요.

| 문서 | 설명 | 링크 |
|------|------|------|
| 🖥 화면 구성 | UI Flow 및 주요 화면 목록 | [Wiki → 화면 구성](../../wiki/화면-구성) |
| 📡 API 명세 | 전체 REST API 엔드포인트 | [Wiki → API 명세](../../wiki/API-명세) |
| 🗃 ERD | 데이터베이스 테이블 구조 | [Wiki → ERD](../../wiki/ERD) |
| 🏗 인프라 아키텍처 | On-Premise K8s 전체 구성도 | [Wiki → Infra Architecture](../../wiki/Infra-Architecture) |
| 🧩 앱 아키텍처 | 서비스 간 통신 및 계층 구조 | [Wiki → Application Architecture](../../wiki/Application-Architecture) |
| 🚀 CI/CD 파이프라인 | GitLab Runner를 활용한 이미지 빌드 및 ArgoCD 배포 전략 | [CI/CD Pipeline](Deployment-Pipeline) |
| 📊 모니터링 & 테스트 | Prometheus + Grafana 대시보드 및 k6 시나리오 가이드 | [Monitoring & Testing](Monitoring-and-Load-Test) |


---

<div align="center">
Made with ❤️ · No AWS · Full On-Premise
</div>


-->






<!--
# 🎓 수능 AI 대학 입시 예측 플랫폼

<div align="center">

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Platform](https://img.shields.io/badge/platform-Web%20%7C%20PWA-brightgreen)
![OCR](https://img.shields.io/badge/OCR-Tesseract%20%7C%20PaddleOCR-orange)
![Infra](https://img.shields.io/badge/Infra-On--Premise%20%7C%20K8s-blueviolet)
![CI/CD](https://img.shields.io/badge/CI%2FCD-ArgoCD-red)

> **수능 성적표 이미지를 OCR로 분석하여 합격 가능 대학을 자동 예측하는 AI 기반 입시 플랫폼**  
> On-Premise · Kubernetes · ArgoCD · HPA · K6 · OCR

</div>

---

## 📌 목차

- [프로젝트 개요](#-프로젝트-개요)
- [왜 웹(PWA)인가?](#-왜-웹pwa인가)
- [핵심 기능](#-핵심-기능)
- [기술 스택 & 역할](#-기술-스택--역할)
- [화면 구성](#-화면-구성-ui-flow)
- [API 명세](#-api-명세)
- [ERD](#-erd)
- [인프라 아키텍처](#️-인프라-아키텍처)
- [애플리케이션 아키텍처](#-애플리케이션-아키텍처)
- [제출 체크리스트](#-제출-체크리스트)

---

## 🚀 프로젝트 개요

수험생이 수능 성적표 사진을 업로드하면:

1. **OCR**이 성적표에서 과목별 등급을 자동 추출
2. **AI 예측 엔진**이 전년도 입시 데이터 기반으로 합격 가능 대학·학과를 추천
3. **결제 후** 상세 분석(대학별 3순위, 합격 확률, 학과별 예측) 제공
4. **원서 접수** 연동 및 **지원자 수 예측** 제공
5. **인기 대학 랭킹** (가장 많이 지원한 대학 순위) 실시간 제공

---

## 📱 왜 웹(PWA)인가?

| 항목 | 웹(PWA) ✅ | 네이티브 앱 |
|------|----------|------------|
| 배포 속도 | 즉시 (URL 접속) | 스토어 심사 (1~7일) |
| OCR 업로드 | 브라우저 카메라 API 지원 | 네이티브 카메라 필요 |
| 크로스플랫폼 | iOS · Android · PC 동시 | 플랫폼별 별도 개발 |
| 유지보수 | 단일 코드베이스 | 각 플랫폼 별도 유지 |
| 수능 시즌 트래픽 | HPA + K6 부하 대응 용이 | 서버 사이드 동일 |
| 오프라인 지원 | PWA Service Worker | 네이티브 우위 |

> **결론:** 수능 시즌 단기 폭발 트래픽 + 빠른 배포 + 크로스플랫폼 요구사항 → **웹(PWA)** 최적

---

## ✨ 핵심 기능

```
📸 성적표 이미지 업로드
    └─ OCR 자동 추출 (과목별 등급)
        └─ 🔓 무료: 등급 확인 + 대학 미리보기 (블러)
            └─ 💳 결제 후 잠금 해제
                ├─ 대학별 TOP 3 추천 (전년도 기준, 매년 자동 갱신)
                ├─ 합격 확률 % 표시
                ├─ 학과별 세부 예측
                ├─ 경쟁자 지원 인원 예측
                └─ 원서 접수 연동

📊 실시간 통계
    ├─ 가장 많이 지원한 대학 TOP 10 랭킹
    └─ 등급대별 지원 분포 히트맵
```

---

## 🛠 기술 스택 & 역할

### Frontend
| 기술 | 역할 |
|------|------|
| **React + Vite** | SPA 구성, PWA 지원 |
| **TypeScript** | 타입 안전성 |
| **TailwindCSS** | UI 스타일링 |
| **Axios** | API 통신 |

### Backend
| 기술 | 역할 |
|------|------|
| **FastAPI (Python)** | REST API 서버, OCR 파이프라인 연동 |
| **Spring Boot (Java)** | 결제·회원·원서 접수 도메인 |
| **PostgreSQL** | 입시 데이터, 사용자 데이터 |
| **Redis** | 세션 캐시, 실시간 랭킹 |
| **Kafka** | OCR 처리 비동기 메시지 큐 |

### OCR & AI
| 기술 | 역할 |
|------|------|
| **PaddleOCR** | 한국어 성적표 텍스트 추출 (Tesseract 대비 한글 정확도 우수) |
| **Tesseract** | 백업 OCR 엔진 |
| **Scikit-learn / XGBoost** | 합격 확률 예측 모델 |
| **Pandas** | 전년도 입시 데이터 가공 |

### Infra (On-Premise / No AWS)
| 기술 | 역할 |
|------|------|
| **Kubernetes (K8s)** | 컨테이너 오케스트레이션 |
| **ArgoCD** | GitOps 기반 CD 파이프라인 |
| **HPA (Horizontal Pod Autoscaler)** | 수능 시즌 트래픽 자동 스케일 아웃 |
| **K6** | 부하 테스트 및 성능 검증 |
| **Nginx Ingress** | 리버스 프록시, SSL 종료 |
| **Prometheus + Grafana** | 메트릭 수집 및 시각화 |
| **Harbor** | 온프레미스 컨테이너 레지스트리 |
| **GitLab CI** | CI 파이프라인 (빌드·테스트·이미지 푸시) |
| **MinIO** | S3 호환 오브젝트 스토리지 (성적표 이미지 저장) |
| **Vault (HashiCorp)** | 시크릿 관리 (API 키, DB 비밀번호) |
| **Cert-Manager** | TLS 인증서 자동 발급 |

> ⚠️ **AWS 서비스 미사용** - 모든 인프라는 온프레미스로 구성

---

## 🖥 화면 구성 (UI Flow)

```
[랜딩 페이지]
    │
    ├─ [성적표 업로드] → [OCR 처리 중 로딩]
    │       │
    │       └─ [등급 확인 화면] (무료)
    │               │
    │               └─ [결제 모달] ──→ [결제 완료]
    │                                       │
    │                           ┌───────────┼───────────┐
    │                           ▼           ▼           ▼
    │                   [대학 추천 TOP3] [합격확률] [학과별 분석]
    │                           │
    │                           └─ [원서 접수 연동]
    │
    ├─ [실시간 랭킹] - 가장 많이 지원한 대학 TOP 10
    ├─ [로그인 / 회원가입]
    └─ [마이페이지] - 내 분석 이력, 결제 내역
```

### 주요 화면 목록

| 화면 | 설명 |
|------|------|
| `/ ` | 랜딩 (서비스 소개 + 업로드 CTA) |
| `/upload` | 성적표 이미지 업로드 |
| `/result/:id` | OCR 결과 + 무료 등급 확인 |
| `/result/:id/premium` | 결제 후 상세 분석 (대학 추천, 합격률) |
| `/ranking` | 실시간 지원 대학 랭킹 |
| `/apply` | 원서 접수 연동 페이지 |
| `/mypage` | 내 분석 이력 |
| `/admin` | 관리자 (입시 데이터 관리) |

---

## 📡 API 명세

### 인증
| Method | Endpoint | 설명 |
|--------|----------|------|
| POST | `/api/auth/register` | 회원가입 |
| POST | `/api/auth/login` | 로그인 (JWT 발급) |
| POST | `/api/auth/refresh` | 토큰 갱신 |

### OCR & 분석
| Method | Endpoint | 설명 |
|--------|----------|------|
| POST | `/api/ocr/upload` | 성적표 이미지 업로드 & OCR 요청 |
| GET | `/api/ocr/status/:jobId` | OCR 처리 상태 조회 |
| GET | `/api/result/:id` | 분석 결과 조회 (무료: 등급만) |
| GET | `/api/result/:id/premium` | 상세 분석 결과 (결제 필요) |

### 대학 추천
| Method | Endpoint | 설명 |
|--------|----------|------|
| GET | `/api/recommend?year={year}&grade={grade}` | 등급별 대학 추천 (전년도 자동 계산) |
| GET | `/api/recommend/:id/departments` | 학과별 세부 추천 |
| GET | `/api/predict/applicants` | 대학별 지원자 수 예측 |

### 결제
| Method | Endpoint | 설명 |
|--------|----------|------|
| POST | `/api/payment/create` | 결제 세션 생성 (토스페이먼츠) |
| POST | `/api/payment/confirm` | 결제 승인 |
| GET | `/api/payment/history` | 결제 내역 조회 |

### 랭킹
| Method | Endpoint | 설명 |
|--------|----------|------|
| GET | `/api/ranking/universities` | 가장 많이 지원한 대학 TOP N |
| GET | `/api/ranking/departments` | 인기 학과 랭킹 |

### 원서 접수
| Method | Endpoint | 설명 |
|--------|----------|------|
| POST | `/api/apply` | 원서 접수 |
| GET | `/api/apply/:id` | 접수 현황 조회 |

---

## 🗃 ERD

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────────┐
│     USERS        │     │   OCR_RESULTS     │     │   RECOMMENDATIONS   │
├─────────────────┤     ├──────────────────┤     ├─────────────────────┤
│ id (PK)         │────<│ id (PK)           │────<│ id (PK)             │
│ email           │     │ user_id (FK)      │     │ ocr_result_id (FK)  │
│ password_hash   │     │ image_url         │     │ university_id (FK)  │
│ name            │     │ status            │     │ department_id (FK)  │
│ created_at      │     │ raw_text          │     │ rank (1~3)          │
└─────────────────┘     │ created_at        │     │ pass_probability    │
                        └──────────────────┘     │ year                │
                                                  └─────────────────────┘
┌─────────────────┐     ┌──────────────────┐
│   GRADE_SCORES   │     │  UNIVERSITIES    │
├─────────────────┤     ├──────────────────┤
│ id (PK)         │     │ id (PK)          │
│ ocr_result_id   │     │ name             │
│ subject         │     │ region           │
│ grade (1~9)     │     │ type (국립/사립)  │
│ standard_score  │     └──────────────────┘
│ percentile      │              │
└─────────────────┘     ┌──────────────────┐
                        │  DEPARTMENTS     │
┌─────────────────┐     ├──────────────────┤
│   PAYMENTS       │     │ id (PK)          │
├─────────────────┤     │ university_id(FK)│
│ id (PK)         │     │ name             │
│ user_id (FK)    │     │ min_grade_2024   │
│ amount          │     │ min_grade_2025   │
│ status          │     │ applicants_2024  │
│ payment_key     │     │ applicants_2025  │
│ created_at      │     └──────────────────┘
└─────────────────┘
┌─────────────────┐
│  APPLICATIONS    │
├─────────────────┤
│ id (PK)         │
│ user_id (FK)    │
│ university_id   │
│ department_id   │
│ applied_at      │
│ status          │
└─────────────────┘
```

---

## 🏗️ 인프라 아키텍처

```
                        ┌─────────────────────────────────────────┐
                        │          On-Premise K8s Cluster          │
  User Browser          │                                          │
      │                 │  ┌──────────┐    ┌────────────────────┐  │
      │  HTTPS          │  │  Nginx   │    │   GitLab CI        │  │
      └────────────────►│  │  Ingress │    │  (Build + Push)    │  │
                        │  └────┬─────┘    └────────┬───────────┘  │
                        │       │                   │              │
                        │  ┌────▼──────────────┐    │              │
                        │  │   Frontend Pod    │    │ Harbor       │
                        │  │   (React PWA)     │    │ (Registry)   │
                        │  └────────────────── ┘    │              │
                        │                           │              │
                        │  ┌────────────────────┐◄──┘              │
                        │  │     ArgoCD          │                 │
                        │  │  (GitOps CD)        │──► Deploy Pods  │
                        │  └────────────────────┘                  │
                        │                                          │
                        │  ┌──────────┐  ┌──────────┐  ┌────────┐ │
                        │  │ FastAPI  │  │  Spring  │  │  OCR   │ │
                        │  │ (분석)   │  │  Boot    │  │ Worker │ │
                        │  │  + HPA   │  │ (결제)   │  │ + HPA  │ │
                        │  └────┬─────┘  └────┬─────┘  └───┬────┘ │
                        │       │              │             │      │
                        │  ┌────▼──────────────▼─────────────▼───┐ │
                        │  │              Kafka                   │ │
                        │  │         (OCR 비동기 큐)               │ │
                        │  └──────────────────────────────────────┘ │
                        │                                           │
                        │  ┌──────────┐  ┌──────────┐  ┌────────┐  │
                        │  │ PostgreSQL│  │  Redis   │  │ MinIO  │  │
                        │  │  (메인DB) │  │  (캐시)  │  │(이미지)│  │
                        │  └──────────┘  └──────────┘  └────────┘  │
                        │                                           │
                        │  ┌──────────┐  ┌──────────┐              │
                        │  │Prometheus│  │ Grafana  │              │
                        │  │(메트릭)  │  │(대시보드)│              │
                        │  └──────────┘  └──────────┘              │
                        │                                           │
                        │  ┌──────────────────────────────────────┐ │
                        │  │    K6 (부하 테스트 / 수능 시즌 검증)  │ │
                        │  └──────────────────────────────────────┘ │
                        └─────────────────────────────────────────┘
```

### 각 컴포넌트 역할

| 컴포넌트 | 역할 | 이유 |
|----------|------|------|
| **ArgoCD** | Git 저장소와 K8s 상태를 동기화하는 GitOps CD 도구 | 수동 배포 실수 방지, 롤백 용이 |
| **HPA** | CPU/메모리 기준으로 Pod 수를 자동 확장/축소 | 수능 시즌(11월) 폭발 트래픽 자동 대응 |
| **K6** | 시나리오 기반 부하 테스트 (VU 수백~수천) | HPA 임계값 검증, 병목 사전 발견 |
| **Harbor** | 온프레미스 컨테이너 이미지 레지스트리 | AWS ECR 없이 이미지 관리 |
| **Kafka** | OCR 처리를 비동기 큐로 분리 | 이미지 업로드 즉시 응답, 처리 안정성 |
| **MinIO** | S3 호환 오브젝트 스토리지 | AWS S3 없이 이미지 저장 |
| **Vault** | DB 패스워드, API 키 중앙 관리 | 시크릿 코드 노출 방지 |
| **Prometheus + Grafana** | 메트릭 수집 및 실시간 모니터링 | HPA 기준 지표 제공, 장애 알림 |
| **PaddleOCR** | 한국어 성적표 인식 | Tesseract 대비 한글 정확도 높음 |
| **Cert-Manager** | Let's Encrypt TLS 자동 갱신 | HTTPS 유지보수 자동화 |

---

## 🧩 애플리케이션 아키텍처

```
┌─────────────────────────────────────────────────────────┐
│                    Client (PWA)                         │
│   React + Vite │ TailwindCSS │ React Query │ Zustand    │
└────────────────────────┬────────────────────────────────┘
                         │ REST / WebSocket
┌────────────────────────▼────────────────────────────────┐
│                   API Gateway (Nginx)                    │
│         Rate Limiting │ Auth Check │ Routing            │
└───────┬───────────────┬───────────────────┬─────────────┘
        │               │                   │
┌───────▼──────┐ ┌──────▼──────┐ ┌─────────▼──────────┐
│  FastAPI      │ │ Spring Boot │ │   OCR Worker        │
│  분석 서비스  │ │ 결제/회원    │ │  PaddleOCR          │
│              │ │ 원서접수     │ │  → Kafka Consumer   │
└───────┬──────┘ └──────┬──────┘ └─────────┬──────────┘
        │               │                   │
┌───────▼───────────────▼───────────────────▼──────────┐
│                    Data Layer                         │
│   PostgreSQL │ Redis (랭킹/캐시) │ MinIO (이미지)     │
└──────────────────────────────────────────────────────┘
```

### 연간 입시 데이터 자동 갱신 로직

```python
# 매년 자동으로 "전년도" 기준 추천
def get_recommend_year():
    current_year = datetime.now().year
    return current_year - 1  # 항상 전년도 기준

# 예: 2026년 접속 → 2025년 입시 데이터 기준으로 추천
```

---

## ✅ 제출 체크리스트

| 항목 | 링크 | 상태 |
|------|------|------|
| 1. 개인 repo main branch README | [링크 삽입] | ⬜ |
| 2-1. Wiki - 화면 구성 | [링크 삽입] | ⬜ |
| 2-2. Wiki - API 명세 | [링크 삽입] | ⬜ |
| 2-3. Wiki - ERD | [링크 삽입] | ⬜ |
| 2-4. Wiki - Infra Architecture | [링크 삽입] | ⬜ |
| 2-5. Wiki - Application Architecture | [링크 삽입] | ⬜ |
| 3. Project 칸반보드 (마일스톤 + 이슈) | [링크 삽입] | ⬜ |

---

## 📅 마일스톤 (칸반보드 등록용)

```
Milestone 1: 기획 & 설계 (1주)
  ├─ [ ] ERD 설계
  ├─ [ ] API 명세 작성
  ├─ [ ] UI 와이어프레임
  └─ [ ] 인프라 아키텍처 확정

Milestone 2: 환경 구성 (1주)
  ├─ [ ] K8s 클러스터 세팅
  ├─ [ ] ArgoCD 설치 및 연동
  ├─ [ ] Harbor 레지스트리 구성
  ├─ [ ] GitLab CI 파이프라인 작성
  └─ [ ] Vault, MinIO, Kafka 설치

Milestone 3: 백엔드 개발 (2주)
  ├─ [ ] OCR Worker (PaddleOCR + Kafka)
  ├─ [ ] FastAPI 분석 서비스
  ├─ [ ] Spring Boot 결제/회원/원서
  └─ [ ] 입시 데이터 수집 & 모델 학습

Milestone 4: 프론트엔드 개발 (2주)
  ├─ [ ] 성적표 업로드 화면
  ├─ [ ] OCR 결과 & 대학 추천 화면
  ├─ [ ] 결제 플로우 (토스페이먼츠)
  ├─ [ ] 실시간 랭킹 화면
  └─ [ ] 원서 접수 연동

Milestone 5: 부하 테스트 & 최적화 (1주)
  ├─ [ ] K6 시나리오 작성
  ├─ [ ] HPA 임계값 튜닝
  ├─ [ ] Prometheus + Grafana 대시보드
  └─ [ ] 성능 목표 달성 검증

Milestone 6: QA & 배포 (1주)
  ├─ [ ] 통합 테스트
  ├─ [ ] ArgoCD 프로덕션 배포
  └─ [ ] 최종 문서화
```

---

## 👥 팀원

| 이름 | 역할 |
|------|------|
|      | Backend (FastAPI, OCR) |
|      | Backend (Spring Boot, 결제) |
|      | Frontend (React PWA) |
|      | Infra (K8s, ArgoCD, HPA) |

---

<div align="center">

**Made with ❤️ — No AWS, Full On-Premise**

</div>


-->












<!--

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
-->


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
