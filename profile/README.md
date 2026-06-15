# 🏭 AUTONIX
### Auto + Innovation — 자동차 생산 공장 스마트 팩토리 시스템

> 주문부터 출고까지, 전 공정을 데이터로 연결합니다.

<br>

## 📌 프로젝트 소개

**AUTONIX**는 자동차 생산 공장의 주문 생성부터 출고까지 전 과정을 실시간으로 모니터링하고 관리하는 MSA 기반 스마트 팩토리 시스템입니다.

관리자가 생산 주문을 등록하면 시뮬레이터가 자동으로 공정을 진행하고,  
라인 장애·재고 부족 등 이상 상황은 SSE를 통해 대시보드에 실시간 알림으로 전달됩니다.

```
주문 생성 → 시뮬레이터 가동 → 차체 → 도장 → 조립 → 품질검사 → 출고
```

<br>

## 🏆 수상

> **LGCNS AM Inspire 4기 미니프로젝트2 최우수상** 🥇

<br>

## 🎬 데모 영상

> 📺 [YouTube 데모 영상]()  
> 📺 [Rolling Update 테스트 영상]()

<!-- 링크 추가 예정 -->

<br>

## 🖼️ 스크린샷

<!-- 스크린샷 추가 예정 -->

<br>

## ⚙️ 주요 기능

| 기능 | 설명 |
|------|------|
| 생산 주문 관리 | 차종·수량·색상·마감기한 입력으로 주문 생성 및 생산 시작 |
| 공정 시뮬레이션 | 차체→도장→조립→품질검사→출고 자동 진행 |
| 실시간 대시보드 | 전체 공정 차량 흐름 실시간 모니터링 (SSE) |
| 라인 모니터링 | 라인별 상태·가동률·할당 차량 현황 조회 |
| 재고 관리 | 스테이션 이동 시 부품 자동 차감 및 재고 부족 알림 |
| 배송 관리 | QC 통과 차량 자동 출고 등록 및 배송 상태 추적 |
| 통합 알림 | 라인 장애(line.fault)·재고 부족(stock.low) 실시간 푸시 |

<br>

## 🏗️ 시스템 아키텍처

### 마이크로서비스 구성

```
Client (React/TypeScript)
    │
    ▼
[AWS EKS - Nginx Ingress Controller]
    ├── /        →  client (React 정적 서빙)
    └── /api/*   →  api-gateway (BFF + JWT 검증 + SSE)
                        │
                        ├── OpenFeign →  order-service
                        ├── OpenFeign →  line-service
                        ├── OpenFeign →  inventory-service
                        ├── OpenFeign →  shipping-service
                        └── Kafka Consumer ← line/inventory (SSE 푸시)

[simulator-service]
    ├── OpenFeign →  line-service      (차량 위치 업데이트)
    ├── OpenFeign →  shipping-service  (QC 통과 시 출고 등록)
    └── Kafka     →  inventory-service (재고 차감)

[AWS RDS] ← 서비스별 독립 DB / [AWS ECR] ← 이미지 저장소
```

### 통신 방식

| 방식 | 사용 케이스 |
|------|------------|
| **동기 (OpenFeign)** | 차량 위치 업데이트, 배송 등록, BFF 대시보드 병렬 집계 |
| **비동기 (Kafka)** | 시뮬레이션 시작, 재고 차감, 라인 장애·재고 부족 이벤트 |
| **SSE** | 차량 공정 흐름 실시간 푸시, 장애·알림 실시간 브로드캐스트 |

### Kafka Topics

| Topic | 발행 | 수신 |
|-------|------|------|
| `production.started` | order-service | simulator-service |
| `inventory.deduct` | simulator-service | inventory-service |
| `line.fault` | line-service | gateway-service |
| `stock.low` | inventory-service | gateway-service |

<br>

## 🛠️ 기술 스택

### Backend
![Java](https://img.shields.io/badge/Java-ED8B00?style=flat-square&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=flat-square&logo=springboot&logoColor=white)
![Spring Cloud](https://img.shields.io/badge/Spring_Cloud_Config-6DB33F?style=flat-square&logo=spring&logoColor=white)
![Kafka](https://img.shields.io/badge/Apache_Kafka-231F20?style=flat-square&logo=apachekafka&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat-square&logo=mysql&logoColor=white)

### Frontend
![React](https://img.shields.io/badge/React-61DAFB?style=flat-square&logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)

### Infrastructure
![AWS EKS](https://img.shields.io/badge/AWS_EKS-FF9900?style=flat-square&logo=amazoneks&logoColor=white)
![AWS RDS](https://img.shields.io/badge/AWS_RDS-527FFF?style=flat-square&logo=amazonrds&logoColor=white)
![AWS ECR](https://img.shields.io/badge/AWS_ECR-FF9900?style=flat-square&logo=amazonaws&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx_Ingress-009639?style=flat-square&logo=nginx&logoColor=white)

<br>

## 📦 레포지토리 구성

| 레포지토리 | 설명 | 담당 |
|-----------|------|------|
| [api-gateway](https://github.com/Autonix-Project/api-gateway) | BFF + JWT 인증 + SSE + 라우팅 | 이준혁 |
| [simulator-service](https://github.com/Autonix-Project/simulator-service) | 생산 공정 자동 시뮬레이션 | 김규빈 |
| [order-service](https://github.com/Autonix-Project/order-service) | 생산 주문 생성 및 관리 | 유강현 |
| [line-service](https://github.com/Autonix-Project/line-service) | 생산라인 및 차량 공정 실시간 추적 | 나영은 |
| [inventory-service](https://github.com/Autonix-Project/inventory-service) | 부품 재고 관리 및 차감 | 김가인 |
| [shipping-service](https://github.com/Autonix-Project/shipping-service) | 출고 차량 배송 등록 및 관리 | 김다윤 |
| [client](https://github.com/Autonix-Project/client) | React/TypeScript 프론트엔드 | 나영은 |
| [msa-config](https://github.com/Autonix-Project/msa-config) | Spring Cloud Config 설정 파일 저장소 | - |

<br>

## 🗂️ ERD 요약

각 서비스는 **Database per Service** 패턴을 엄격히 적용합니다.  
서비스 간 직접 DB 참조는 금지하며, 모든 데이터 교환은 API 또는 Kafka 이벤트를 통해서만 이루어집니다.

| DB | 주요 테이블 |
|----|------------|
| `line_db` | lines, vehicles, process_history, line_stats, notifications |
| `order_db` (production_db) | orders, member |
| `inventory_db` | parts, inventory_transactions |
| `shipping_db` | shippings |

<br>

## 👥 팀원

| 이름 | GitHub | 담당 서비스 |
|------|--------|------------|
| 이준혁 | [@LeeJuneHyuck](https://github.com/LeeJuneHyuck) | api-gateway |
| 김규빈 | [@kkbeen](https://github.com/kkbeen) | simulator-service |
| 유강현 | [@1KH9999](https://github.com/1KH9999) | order-service |
| 나영은 | [@Yeongeunn](https://github.com/Yeongeunn) | line-service, client |
| 김가인 | [@rlarkdls123](https://github.com/rlarkdls123) | inventory-service |
| 김다윤 | [@kdayun](https://github.com/kdayun) | shipping-service |

<br>

## 🤝 협업 방식

- **브랜치 전략**: Git Flow (`main` / `develop` / `feature`)
- **커밋 컨벤션**: `feat` / `fix` / `refactor` 등 통일
- **일정 관리**: Notion (매일 회의록 + To-do)
- **실시간 소통**: Discord
- **통합 테스트**: Docker Compose로 전체 6개 서비스 로컬 통합 테스트 후 EKS 배포

<br>

---

<div align="center">
  <sub>5tomatic — LGCNS AM Inspire K-Digital Training 2025</sub>
</div>
