---
layout: post
title: "[문제해결] 커머스 백오피스를 만들게 된 이유"
description: 프로모션 세일 공동구매 커머스 플랫폼에서
date: 2021-07-01 17:01:28 -0400
tags: []
categories: [문제해결, 자동화]
published: false
---
화
# 커머스 백오피스 업무 자동화

![Banner](https://t4.ftcdn.net/jpg/01/91/91/57/360_F_191915740_dShLi9gi8iTrp6d5QPaxWH6Rtunn5ZpZ.jpg)


## **문제**

- 백오피스 운영팀은 가입자 목록을 조회하고 특정 가입자에게 SMS나 카카오톡을 통해 안내 메시지를 전송하는 작업을 개발팀에 지속적으로 요청해야 했음.
- 상품 검색과 재고량 조회, 학교별 판매 물품 확인이 수작업으로 진행되어 비효율적인 상황.
- 주문 정보를 확인하고 발주를 위해 엑셀 문서를 생성하는 과정도 반복적인 수작업이 필요.
- 이러한 비효율적인 업무 프로세스로 인해 개발팀은 운영팀의 요청을 지속적으로 처리해야 했고, 운영팀은 업무를 신속하게 처리하지 못하는 문제가 여러차례 발생.

---

## **원인**

1. **수작업 프로세스 의존**: 기존 시스템은 운영팀이 직접 가입자 목록을 조회하고 메시지를 발송할 수 있는 기능이 없었으며, 개발팀에 요청하여 처리.
2. **재고 및 판매 물품 확인의 불편함**: 학교별 판매 물품과 재고를 쉽게 조회할 수 있는 기능이 부족하여 불필요한 데이터 요청과 반복적인 확인 작업이 발생.
3. **주문 데이터 관리의 비효율성**: 주문 정보를 확인하고 발주를 위한 엑셀 문서를 생성하는 과정이 자동화되지 않아, 운영팀이 수작업으로 데이터를 정리해야 했음.

---

## **대안**

1. **수작업 기반 프로세스 유지**: 운영팀이 기존과 동일하게 가입자 목록을 조회하고 메시지를 발송하는 작업을 개발팀에 요청하는 방식 유지. 이 방법은 별도 시스템 개발 없이 유지보수 비용이 적다는 장점이 있지만, 개발팀과 운영팀 간의 지속적인 커뮤니케이션이 필요하고, 업무 지연이 발생할 가능성이 큼.
2. **엑셀 기반 관리 시스템 활용**: 가입자 목록과 상품 데이터를 엑셀 파일로 관리하고, 수작업으로 데이터를 갱신하는 방식. 특정 가입자에게 메시지를 보내기 위해 필터링과 수동 입력을 활용할 수 있음. 이 방식은 사용법이 익숙하다는 장점이 있지만, 실시간 데이터 반영이 어렵고, 데이터의 일관성과 정확성이 저하될 위험이 있음.
3. **외부 SaaS 서비스 도입**: 기존 백오피스 시스템 대신 외부 SaaS 서비스를 활용하여 메시지 전송 및 재고 관리를 수행하는 방법. 이 방식은 도입이 빠르고 초기 개발 비용이 낮지만, 운영팀이 원하는 커스텀 기능을 추가하기 어려우며, 장기적으로 서비스 비용이 증가할 가능성이 있음.

---

## **해결**

1. **가입자 목록 조회 및 메시지 발송 자동화**
    - 운영팀이 로그인 후 가입자 목록을 조회할 수 있도록 UI를 개선함.
    - SMS 및 카카오톡 API를 활용하여 운영팀이 직접 메시지를 발송할 수 있도록 기능을 추가함.
    - 메시지 전송 내역을 로그로 기록하여 추적이 가능하도록 설정함.
2. **상품 검색 및 재고 조회 기능 개선**
    - 상품 및 재고량을 실시간으로 조회할 수 있도록 API를 개선하고, 검색 기능을 강화함.
    - 학교별 판매 물품을 한눈에 확인할 수 있는 UI를 추가하여 데이터 접근성을 높였음.
3. **주문 관리 자동화**
    - 주문 정보를 필터링 및 검색할 수 있는 기능을 추가함.
    - 주문 데이터를 다중 선택하여 발주용 엑셀 파일을 자동 생성할 수 있도록 기능을 구현함.
    - 결제 정보, 배송 상태, 주문 내역 등의 데이터를 한 화면에서 직관적으로 확인할 수 있도록 UI를 개선함.

---

## **고찰**

이번 백오피스 자동화 프로젝트를 통해 운영팀이 개발팀의 개입 없이도 가입자 관리, 상품 검색, 주문 처리 등의 업무를 원활하게 수행할 수 있도록 개선되었습니다. 특히, 메시지 발송 자동화와 주문 데이터 엑셀 생성 기능은 운영팀의 업무 효율성을 크게 향상시켰습니다.

이러한 개선을 통해 운영팀과 개발팀 간의 **불필요한 요청과 응답 과정을 줄일 수** 있었고, 운영팀이 **더욱 신속하게 업무를 처리**할 수 있도록 도왔습니다. 하지만, 시스템 자동화가 진행되면서 운영팀의 업무 방식이 변화해야 하는 점과 새로운 기능 사용에 대한 학습 곡선이 존재한다는 점도 고려해야 했습니다. 이를 위해 기능별 가이드를 제공하고, 운영팀과의 **지속적인 피드백을 통해 시스템을 최적화**하는 것이 중요했습니다.

이번 경험을 통해 시스템 자동화가 단순한 기능 추가가 아니라, **실제 업무 흐름을 고려한 설계와 운영팀의 피드백**이 함께 반영될 때 비로소 성공적인 도입이 가능하다는 점을 다시 한번 깨닫게 되었습니다.