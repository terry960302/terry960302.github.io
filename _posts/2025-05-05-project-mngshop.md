---
layout: post
title: "[포트폴리오] MNG샵(mngshop) 서비스"
description: 프로모션 세일 공동구매 커머스 플랫폼
date: 2020-12-01 17:01:28 -0400
tags: [typescript, nest.js, react.js, azure, mysql, bootpay-sdk, graphql, public-cloud]
categories: [프로젝트, (주)프레젤글로벌커넥션그룹]
---

# MNG샵 - 프로모션 세일 공동구매 커머스

<div text-align="center">
    <img src="/assets/img/2025-05-05-project-mngshop/mngshop-logo.png" alt="Mngshop Logo" width="300px">
</div>

> **프로모션 세일** 중심의 폐쇄형 공동구매 커머스 웹 서비스


## 기간
- `7개월` 2020.12 ~ 2021.06 


## 기여도
- 팀구성 : 총 6인(기획자1, 영업1, 디자이너1, 개발3) 
- 전체 프로젝트 기여도: 약 `67%` (프론트엔드 60%, 백엔드 40%, 데브옵스 100%)

## 운영이력
- ASUS 노트북 제휴를 통한 `억단위 매출` 기록
- `Pre-A 단계(3억)` 투자유치
- 1,600명 사용자 확보 및 운영

<!-- ## 문제해결과정
> [🔗 커머스 재고 동시성 문제를 해결하며...]({% post_url 2025-05-06-problem-solving-mngshop1 %})

> [🔗 백오피스로 커머스 업무 자동화하기]({% post_url 2025-05-07-problem-solving-mngshop2 %}) -->

## 시연 이미지
> 실제 진행했던 학교별 프로모션 예시 이미지입니다.

![Mngshop Promotion1](/assets/img/2025-05-05-project-mngshop/promotion1.png)
![Mngshop Promotion2](/assets/img/2025-05-05-project-mngshop/promotion2.png)


## 1. 프로젝트 개요

> 프로모션 세일 공동구매 커머스 플랫폼
> 
> 
> 고객용 SPA와 관리자용 SPA로 구성된 웹 서비스이며, 주문, 결제, 관리 기능을 Azure 상에서 완전하게 운영.
> 
- 프론트: React 기반 SPA (고객/관리자)
- 백엔드: NestJS (모놀리식 + 모듈화)
- 주요 기능: 주문/결제/프로모션 관리, SMS 발송, Excel 다운로드
- 클라우드 인프라: Azure 기반 PaaS 최적화 구조

---

## 2. 데이터 흐름 아키텍처 (서비스 레이어 중심)
![Mngshop Data Flow](/assets/img/2025-05-05-project-mngshop/mngshop-data-flow.png)
### 고객 플로우

- 이메일/비밀번호로 회원가입 → JWT 발급 (HTTP-only 쿠키)
- 회원가입 시 선택한 ‘대학교’ 기반으로 상품/프로모션 필터링
- Bootpay SDK로 결제 → 결제 토큰을 백엔드로 전달하여 검증
- 주문 성공 시 Slack 알림 발송
- `/api/orders/history`로 주문 내역 조회

### 관리자 플로우

- 관리자 JWT 인증 → RBAC 기반 접근 제어
- 상품/옵션 등록, 대학교 기반 프로모션 설정
- 사용자 조회 → 선택 사용자에게 SMS 전송
- 주문 목록 JSON 응답 → SheetJS로 Excel 생성 (서버는 파일 미생성)

### 외부 연동

- **Bootpay SDK**: 결제 UI 및 토큰 검증
- **SMS API**: 관리자 요청 시 발송
- **Sentry + Slack**: 에러 및 주문 성공 이벤트 실시간 알림
- **Amplitude / Google Analytics**: 프론트 이벤트 및 페이지뷰 분석

---

## 3. 클라우드 인프라 아키텍처 (Azure PaaS 기반)

![Mngshop Cloud Architecture](/assets/img/2025-05-05-project-mngshop/mngshop-azure-architecture.png)

### 인프라 구조 개요

```
[Customer SPA] + [Admin SPA]
 → Azure Blob Static Website + Azure CDN

[NestJS Unified API]
 → Azure WebApp (per env: dev/prod)
 → App Service Plan (공유)

[MySQL]
 → Azure Database for MySQL (dev/prod 구분)

[Storage]
 → Azure Blob (이미지, 백업)
 → Azure Function (SAS 토큰 발급)

[SMS 처리]
 → Azure Logic App (SMS API 호출)

[모니터링]
 → Sentry → Slack 연동

[분석]
 → Amplitude, GA (프론트 직접 호출)
```

---

### 리소스 상세 정리

| 구성 요소 | 설명 |
| --- | --- |
| **SPA 정적 자산** | Blob Static Website + CDN으로 서빙 (WebApp 제거) |
| **API 서버** | NestJS 기반, `/api/customer`, `/api/admin`으로 라우팅 구분 |
| **App Service Plan** | 환경(dev/prod)별 1개만 사용하여 비용 최적화 |
| **DB** | Azure Database for MySQL 사용 (운영/개발 분리) |
| **Storage** | Blob에 이미지 및 DB 백업 저장 |
| **Function** | SAS 토큰 발급용 Azure Function (선택적 사용) |
| **SMS 처리** | 기존 WebApp 제거, Azure Logic App으로 대체 |
| **모니터링/알림** | Sentry 에러 → Slack 알림, 주문 성공 이벤트도 Slack으로 전송 |
| **분석 도구** | GA/Amplitude 모두 프론트에서 직접 송신 |

---

### 최적화 포인트 요약

| 최적화 항목 | 조치 내용 | 효과 |
| --- | --- | --- |
| SPA WebApp 제거 | 정적 호스팅 전환 (Blob+CDN) | App Service 비용 완전 제거 |
| API 서버 통합 | Admin/Customer 라우팅 구분 운영 | WebApp 수 절감, 유지보수 단순화 |
| App Service Plan 공유 | dev/prod 각각 하나만 사용 | 서버 수 감소 → 비용 절감 |
| SMS WebApp 제거 | Logic App 대체 | 서버 유지 비용 제거, 구성 간단화 |

---

## 핵심 요약

- 모놀리식 구조지만 기능은 명확히 분리되어 유지보수 효율적
- Azure 자원 과다 사용 없이, PaaS 자원 최적 활용
- 핵심 기능(결제, 알림, Excel, SMS)을 모두 실제 서비스 흐름에 기반해 구현
- 클라우드 운영비 최적화와 구조적 안정성을 동시에 확보 