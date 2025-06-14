---
layout: post
title: "[포트폴리오] 마켓빌(MarketBill) 서비스"
description: 화훼시장 도소매상 거래 플랫폼
date: 2022-07-01 17:01:28 -0400
tags: [spring-boot, java, graphql, dgs-graphql, python, golang, aws, public-cloud]
categories: [프로젝트, 사이드프로젝트]
---

# 마켓빌(MarketBill) - 화훼시장 도소매상 거래 플랫폼

![Monegi Logo](/assets/img/2025-05-08-project-marketbill/marketbill-banner.png)

> 양재 꽃시장 회장님과의 대면 인터뷰를 통해 파악한 꽃집 사장님들의 일련의 거래 활동을 웹으로 옮겼습니다.


## 기간
- `7개월` 2022.10 ~ 2023.04 


## 기여도
- 팀구성 : 총 4인(기획자1, 디자이너1, 개발2) 
- 개발 기여도: 약 `67%` (프론트엔드 0%, 백엔드 100%, 데브옵스 100%)

## 운영이력
- 실제 양재 꽃시장에서 회장님의 도움을 통해 테스트 베드를 진행했었습니다. 

## 시연이미지

{% include carousel.html number="1" height="600" unit="px" max_width="350px" img_fit="contain" %}


## 아키텍처 도식화

![Architecture Diagram](/assets/img/2025-05-08-project-marketbill/marketbill-cloud-architecture.png)

## 데이터베이스 ERD
![ERD](/assets/img/2025-05-08-project-marketbill/marketbill-erd.png)

## 1. 프로젝트 개요

> MarketBill은 화훼 상점 간 도·소매 거래 구조를 디지털화한 서비스입니다.
복잡한 종이 주문서와 구두 정산 과정을 전자화하여, 도매상-소매상 간의 주문, 정산, 영수증 발행, 알림 발송까지의 흐름을 하나의 플랫폼으로 통합했습니다.

- 소매상/도매상은 화훼 경매 데이터를 연동하여 실제 화훼 시장에서 거래되는 꽃정보 확인이 가능합니다.
- 소매상(플로리스트)는 도매상을 정하여 꽃에 대하여 '장바구니 > 주문'이 가능합니다.
- 도매상은 그날 들어온 주문을 합산하여 거래 명세서를 발급할 수 있습니다.
- 소매상은 도매상과 비즈니스 관계를 맺을 수 있습니다.

## 2. 주요 기능

### 주문 및 정산 기능
- GraphQL 기반 API를 통해 소매상이 도매상에게 주문 요청
- 품목, 수량, 등급 기반의 상세 주문 항목 작성 및 수정
- 주문서 단위로 도매상이 가격 입력 및 정산 진행

###  영수증 PDF 자동 생성
- 주문 완료 시 Lambda에서 PDF 영수증 자동 생성
- PDF는 S3에 업로드되어 클라이언트에서 다운로드 가능

###  경매 데이터 연동
- 공공 화훼 경매 API로부터 매일 실시간 데이터 수집
- 품목·품종·등급별로 정제된 데이터 기반으로 주문 가능

###  사용자 권한 관리
- 도매상(사장/직원), 소매상 구분
- 도매상만 가격 입력 및 영수증 발행 권한 부여
- 직원 계정은 사장 계정에 종속되어 기능 일부 제한

## 3. 데이터 흐름 아키텍처

###  1) 주문 생성 플로우
1. 클라이언트(React SPA)는 CloudFront를 통해 로드됨
2. 사용자는 ALB를 통해 연결된 GraphQL API(ECS 상 Spring Boot)에 요청
3. 주문 요청은 shopping_sessions, cart_items, order_sheets, order_items 등에 저장됨

###  2) 영수증(PDF) 생성 및 저장
1. 주문 완료 후 클라이언트에서 API Gateway로 Lambda(file-process-service) 직접 호출
2. Lambda가 PDF 영수증을 생성 후 S3에 저장
3. 생성된 영수증의 메타정보는 RDS(order_sheet_receipts)에 저장됨
4. 클라이언트는 GraphQL 응답 또는 S3 경로를 통해 PDF에 접근

###  3) 주문 알림 전송
1. 주문 완료 후 클라이언트에서 API Gateway를 통해 Lambda(messaging-service) 호출
2. Lambda가 SMS API를 통해 알림을 전송
3. 전송 결과는 RDS(send_sms_logs)에 기록됨

###  4) 경매 데이터 수집 및 매핑
1. EventBridge (cron 기반) → Lambda(scheduler-service) 실행
2. 외부 화훼 경매 API로부터 데이터를 수집
3. 수집된 원시 데이터는 public_biding_flowers 테이블에 저장
4. 이후 flower_types, flowers, bidding_flowers 테이블로 가공/매핑 처리

>  Lambda 호출은 모두 API Gateway를 통해 수행되며, 이벤트 기반 트리거는 경매 데이터 수집용 스케줄링에만 사용됩니다.


## 4. 클라우드 인프라 아키텍처 (AWS 기반)

### 클라이언트
- Frontend: React 기반 SPA
- 호스팅 방식: S3 Static Website Hosting
- 전송 경로: CloudFront를 통해 글로벌 CDN 배포

### 백엔드 (Core GraphQL API)
- 실행 환경: AWS ECS (Fargate)
- 서비스 구조: Java + Spring Boot + Netflix DGS (GraphQL)
- 트래픽 라우팅: ALB (Application Load Balancer)를 통해 ECS로 전달
- 보안 설정: ALB Security Group으로 인바운드 제어

### PDF 처리 (영수증 생성)
- 구성: Python + FastAPI (file-process-service)
- 호출 경로: API Gateway → Lambda → PDF 생성
- 출력 결과: S3에 저장, 메타데이터는 RDS에 기록

### 메시징 (SMS 알림)
- 구성: Golang + Echo (messaging-service)
- 호출 경로: API Gateway → Lambda → 외부 SMS API 호출
- 결과 기록: send_sms_logs 테이블에 저장

### 스케줄러 (경매 데이터 수집)
- 구성: Golang 기반 Lambda (scheduler-service)
- 트리거: AWS EventBridge (cron 스케줄링)
- 동작: 외부 공공 API에서 경매 데이터 수집 후 가공 및 저장

### 데이터베이스
- RDS (PostgreSQL): 전체 도메인 데이터 저장
  - users, orders, flowers, receipts, logs 등 관리

### CI/CD
- CodeBuild + CodePipeline + ECR
  - ECS 및 Lambda의 컨테이너 빌드 및 배포 자동화

### 정적 파일 및 CDN
- S3: 정적 자산 및 PDF 저장
- CloudFront: SPA 및 영수증 다운로드용 글로벌 캐싱

※ 모든 Lambda 서비스는 API Gateway를 통해 클라이언트와 연결되며, EventBridge는 스케줄링 용도로만 사용됩니다.



## 5. 기술스택

### Framework / Library
- Spring Boot 2.7.5
- Netflix DGS (GraphQL)
- FastAPI
- Echo (Golang 경량 웹 프레임워크)

### Database
- PostgreSQL (RDS)

### Infrastructure
- AWS ECS (Fargate)
- AWS Lambda
- API Gateway
- ALB (Application Load Balancer)
- S3 + CloudFront (정적 파일 및 CDN)
- RDS (PostgreSQL)
- EventBridge (스케줄링)
- CodeBuild + CodePipeline + ECR (CI/CD 파이프라인)

### 기타
- REST + GraphQL 혼합 구조
- PDFKit, ReportLab 등 PDF 처리 라이브러리 (Python 내 사용 가능성 있음)

## 6. 설계 철학

### 1. 도메인 분리를 통한 구조적 명확성
- 영수증 처리, 메시지 발송, 경매 데이터 수집을 각각 독립된 서비스로 분리하여, 핵심 API 서버(Core Server)는 오직 GraphQL 기반 요청 처리에 집중할 수 있도록 설계했습니다.

### 2. 비동기 이벤트보다 명시적 API 호출 지향
- 호출 흐름의 명확성과 운영 디버깅 편의성을 위해 Lambda 서비스들은 EventBridge가 아닌 **API Gateway를 통해 직접 호출**되도록 구성했습니다.

### 3. 운영 효율과 비용 절감을 고려한 인프라 선택
- Lambda를 영수증 처리 및 메시징에 활용하여 **사용량 기반 과금**, **무상 구간 활용** 등 초기 비용 부담을 최소화했습니다.
- ECS + ALB를 활용한 Core 서버 구성은 배포 유연성과 확장성 확보에 기여했습니다.

### 4. 실사용자 환경을 고려한 간결한 기능 구조
- 실제 꽃시장 상인을 위한 서비스라는 특성상, 복잡한 UI 없이 최소한의 요청 → 주문 → 영수증 → 알림 흐름만으로 구성
- SMS, PDF, 경매 데이터 외 불필요한 기능은 철저히 배제

### 5. 배포/확장 중심의 CI/CD 기반 운영
- 모든 컴포넌트는 **CodePipeline + CodeBuild 기반 자동화된 빌드·배포**로 관리
- Lambda 및 ECS 서비스 모두 ECR 기반 이미지 배포 구조 채택
