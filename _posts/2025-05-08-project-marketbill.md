---
layout: post
title: "[포트폴리오] 마켓빌(MarketBill) 서비스"
description: 화훼 도소매상 거래 플랫폼
date: 2022-07-01 17:01:28 -0400
tags: [spring-boot, java, graphql, dgs-graphql, python, golang, aws]
categories: [포트폴리오, 사이드프로젝트]
carousels:
  - images: 
    - image: /assets/img/2025-05-06-project-monegi/appstore-1.png
      alt: 앱스토어1
    - image: /assets/img/2025-05-06-project-monegi/appstore-2.png
      alt: 앱스토어2
    - image: /assets/img/2025-05-06-project-monegi/appstore-3.png
      alt: 앱스토어3
    - image: /assets/img/2025-05-06-project-monegi/appstore-4.png
      alt: 앱스토어4
    - image: /assets/img/2025-05-06-project-monegi/appstore-5.png
      alt: 앱스토어5
  - images: 
    - image: /assets/img/2025-05-06-project-monegi/home1.jpeg
      alt: 홈화면1
    - image: /assets/img/2025-05-06-project-monegi/home2.jpeg
      alt: 홈화면2
    - image: /assets/img/2025-05-06-project-monegi/post.jpeg
      alt: 게시글화면
    - image: /assets/img/2025-05-06-project-monegi/post_comment.jpeg
      alt: 댓글화면
    - image: /assets/img/2025-05-06-project-monegi/write-post.jpeg
      alt: 다다로그 작성
    - image: /assets/img/2025-05-06-project-monegi/soso-log.jpeg
      alt: 소소로그 작성
    - image: /assets/img/2025-05-06-project-monegi/search.jpeg
      alt: 검색화면
    - image: /assets/img/2025-05-06-project-monegi/scrap.jpeg
      alt: 스크랩화면
    - image: /assets/img/2025-05-06-project-monegi/mypage.jpeg
      alt: 마이페이지 화면
    - image: /assets/img/2025-05-06-project-monegi/notification.jpeg
      alt: 알림화면

---

# 모내기(Monegi) - 취향 기반 추천 커뮤니티

![Monegi Logo](/assets/img/2025-05-06-project-monegi/monegi-logo.png)

> **취향 기반 추천**을 중심으로 한, 공동구매 연결 커뮤니티 앱


## 기간
- `11개월` 2021.07 ~ 2022.05 


## 기여도
- 팀구성 : 총 8인(기획자1, 영업1, 마케터1, 디자이너1, 개발4) 
- 전체 프로젝트 기여도: 약 `85%` (프론트엔드 50%, 백엔드 30%, 데브옵스 100%)

## 문제해결과정
> [🔗 SQL 튜닝으로 성능 400배 개선]({% post_url 2025-05-07-problem-solving-monegi %})


## 시연이미지(스토어)

{% include carousel.html number="1" height="600" unit="px" max_width="350px" img_fit="contain" %}

## 시연영상
<div style="position: relative; display: flex; justify-content: center; align-items: center;">
<iframe width="350px" height="600px" src="https://youtube.com/embed/puZ1_88EqPo?feature=share" title="모내기 앱 시연영상" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>

## 시연이미지(작동)

{% include carousel.html number="2" height="600" unit="px" max_width="350px" img_fit="contain" %}


## 아키텍처 도식화
> Blue-green 자동 배포 파이프라인 구축으로 revision 버전에 따른 빠른 롤백이 가능한 구조.

![Architecture Diagram](/assets/img/2025-05-06-project-monegi/monegi-cloud-architecture.png)

## 1. 프로젝트 개요

> 장소 또는 제품을 주제로 한 후기 콘텐츠를 작성하고 공유할 수 있는 모바일 중심의 커뮤니티 플랫폼

- 앱 기반 커뮤니티 (Flutter)
- 다다로그/소소로그로 구분된 리뷰 유형
- 사용자 피드백을 바탕으로 고도화된 검색, 알림, 스크랩 구조
- 관리자용 React 어드민과 백엔드 통합 배포 구조 운영

## 2. 주요 기능

- JWT 기반 로그인/회원가입 (Naver Cloud SMS 인증 연동)
- 제품/장소 후기 업로드 (텍스트 + 이미지)
- 인기순(협업 필터링), 최신순, 팔로잉 탭 기반 홈 피드
- 댓글, 좋아요, 스크랩, 딥링크 공유, 인앱 브라우저 연결
- 사용자/리뷰/장소/제품 통합 검색 (Algolia)
- 관리자 리뷰 생성/관리 기능 포함
- 마이페이지: 작성 리뷰 및 스크랩 콘텐츠 정리

## 3. 데이터 흐름 아키텍처

> 사용자가 작성한 리뷰가 서버로 전달되어 이미지 처리 및 검색 인덱싱까지 완료되는 전체 흐름

- Flutter 앱에서 이미지 포함 리뷰 작성
- 이미지 → Cloud Run(media-file-process) 서버 → Cloud Storage 저장
- 게시물 본문 및 메타 → Cloud SQL 저장
- 동시에 Algolia에 인덱싱되어 검색 가능 상태로 전환
- 좋아요/댓글 발생 시 → FCM 푸시 알림
- 인앱 딥링크 공유, 장소/제품 외부 링크 이동 포함

## 4. 클라우드 인프라 아키텍처 (GCP 기반)
```
[Flutter Mobile App] + [React Admin Dashboard]
→ GCP 기반

[NestJS API Server]
→ Cloud Run (dev/prod 분리)
→ Prisma 기반 도메인 설계

[Media Processor]
→ Cloud Run 서비스 (해상도별 리사이징)
→ GCS 저장

[Database]
→ Cloud SQL for MySQL (운영/개발 분리)

[Storage & CDN]
→ Cloud Storage + Cloud CDN

[Notification]
→ Firebase Cloud Messaging (FCM)

[Search]
→ Algolia 인덱싱 + 키워드 정제

[CI/CD]
→ Cloud Build + Artifact Registry + Cloud Run

[External API]
→ Naver Cloud SMS 인증
```


## 5. 기술스택

- **Backend**: TypeScript, NestJS, Prisma, MySQL, Algolia SDK
- **Infra**: GCP (Cloud Run, Cloud SQL, Cloud Storage, Cloud CDN, Cloud Build)
- **Search**: Algolia
- **Notification**: FCM
- **Frontend**: Flutter (App), React.js (Admin)
- **Etc**: Deep Link, Naver Cloud API

## 6. 설계 철학

- **모놀리식 + 도메인 중심 설계**: Admin/유저 기능을 NestJS 모듈화로 분리
- **검색 최적화 분리**: RDB는 정형데이터, Algolia는 비정형 검색 용도로 활용
- **DevOps 자동화**: Cloud Run 리비전 기반 Blue-Green 배포
- **운영 복잡도 최소화**: 관리자와 유저 API 서버 통합 운영
- **실제 사용자 피드백 기반 UI/UX 및 기능 고도화**

## 7. 운영 이력

- 앱스토어 및 플레이스토어에 실제 배포 및 운영
- 약 `1,000명 규모`의 실제 사용자 대상 사용성 테스트 완료
- 사용자 리뷰/알림/검색 관련 피드백을 통해 기능 개선 및 장애 대응 경험 보유
