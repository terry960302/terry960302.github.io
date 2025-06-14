---
layout: post
title: "[문제해결] EXPLAIN ANALYZE로 SQL 튜닝으로 400배 개선"
description: 취향기반 추천알고리즘을 만들며
date: 2021-07-01 17:01:28 -0400
tags: []
categories: [문제해결, SQL튜닝]
published: false
---

# 추천알고리즘 SQL 튜닝

![Banner](https://velog.velcdn.com/images/hyunspace/post/bf2d95a6-9aca-4d5d-b4da-a08898059364/image.jpg)

### **문제**

- 여러 논문을 기반으로 연구([참고링크](https://blog.naver.com/PostList.naver?blogId=ljk041180&from=postList&categoryNo=38))를 해보니 인프라 세팅이 필요함을 인지하였음, 그러나 추천알고리즘 구현하는데 **인프라 확장할 시간이 충분**하지 않았고  **추가 비용에 대한 부담**이 있었음.
- 추천알고리즘은 보통 머신러닝의 영역인데 **MLOps에 대한 지식이 부족**한 상황
- 추천 알고리즘을 변경하는 것이 아니라, 기존 기획팀이 정의한 추천 방식의 **결과를 유지하면서 속도를 극대화**하는 것이 목표.
- 기존 인프라에서 MySQL 로 구현이 가능한지 여러 안을 내보던 상황에서, 만들었던 **쿼리가 성능부하**가 걸리고 속도가 너무 안나오는 문제가 발생.
- 아래는 기존에 작성했던 사용자 맞춤형 **협업필터링 알고리즘** 기반 리뷰 추천 SQL
    
    ```sql
    select re.id as reviewId, re.userId as userId
          from (select re.id, re.userId, re.createdAt, re.boardId, re.deletedAt from review as re) as re
          inner join (select diffs.id, allDate.readDate, allDate.likedDate, allDate.diffDate 
          from (select rr.id, MIN(ABS(IFNULL(timestampdiff(second, lr.likedDate, rr.readDate), 0))) as diffDate
          from (select re.id, rr.readDate from review as re 
          left join (select rr.reviewId as reviewId, MIN(rr.createdAt) as readDate
          from read_review as rr where rr.userId = ${userId} group by rr.reviewId, rr.userId) as rr on rr.reviewId = re.id) as rr
          left join (select lr.reviewId as reviewId, lr.createdAt as likedDate from follow_user as fu
          inner join like_review as lr on lr.userId = fu.followingId
          where followerId = ${userId}) as lr on lr.reviewId = rr.id
          group by rr.id
          having MIN(ABS(IFNULL(timestampdiff(second, lr.likedDate, rr.readDate), 0))) > 0) as diffs
          INNER JOIN (select rr.id, rr.readDate, lr.likedDate,
          ABS(IFNULL(timestampdiff(second, lr.likedDate, rr.readDate), 0)) as diffDate
          from (select re.id, rr.readDate from review as re 
          left join (select rr.reviewId as reviewId, MIN(rr.createdAt) as readDate
          from read_review as rr where rr.userId = ${userId} group by rr.reviewId, rr.userId) as rr on rr.reviewId = re.id) as rr
          left join (select lr.reviewId as reviewId, lr.createdAt as likedDate from follow_user as fu
          inner join like_review as lr on lr.userId = fu.followingId
          where followerId = ${userId}) as lr on lr.reviewId = rr.id) as allDate on allDate.id = diffs.id AND allDate.diffDate = diffs.diffDate
          
          union
          
          select diffs.id, allDate.readDate, MAX(allDate.likedDate) as likedDate, allDate.diffDate as diffDate from 
          (select rr.id, MIN(ABS(IFNULL(timestampdiff(second, lr.likedDate, rr.readDate), 0))) as diffDate
          from (select re.id, rr.readDate from review as re 
          left join (select rr.reviewId as reviewId, MIN(rr.createdAt) as readDate
          from read_review as rr where rr.userId = ${userId} group by rr.reviewId, rr.userId) as rr on rr.reviewId = re.id) as rr
          left join (select lr.reviewId as reviewId, lr.createdAt as likedDate from follow_user as fu
          inner join like_review as lr on lr.userId = fu.followingId
          where followerId = ${userId}) as lr on lr.reviewId = rr.id
          group by rr.id
          having MIN(ABS(IFNULL(timestampdiff(second, lr.likedDate, rr.readDate), 0))) <= 0) as diffs
          INNER JOIN (select rr.id, rr.readDate, lr.likedDate,
          ABS(IFNULL(timestampdiff(second, lr.likedDate, rr.readDate), 0)) as diffDate
          from (select re.id, rr.readDate from review as re 
          left join (select rr.reviewId as reviewId, MIN(rr.createdAt) as readDate
          from read_review as rr where rr.userId = ${userId} group by rr.reviewId, rr.userId) as rr on rr.reviewId = re.id) as rr
          left join (select lr.reviewId as reviewId, lr.createdAt as likedDate from follow_user as fu
          inner join like_review as lr on lr.userId = fu.followingId
          where followerId = ${userId}) as lr on lr.reviewId = rr.id) as allDate on allDate.id = diffs.id
          group by diffs.id, allDate.diffDate) as followedLikedReview on followedLikedReview.id = re.id
          ${whereClause}
          order by
            case  when re.userId = ${userId} then re.createdAt
                  when followedLikedReview.readDate IS NULL AND followedLikedReview.likedDate IS NULL then re.createdAt
                  when followedLikedReview.readDate IS NOT NULL AND followedLikedReview.likedDate IS NULL then re.createdAt
                  when followedLikedReview.readDate IS NULL AND followedLikedReview.likedDate IS NOT NULL then followedLikedReview.likedDate
                  when followedLikedReview.readDate < followedLikedReview.likedDate then re.createdAt
                  else followedLikedReview.likedDate end desc
    ```
    

---

## **원인**

- ML 모델을 시행할 **서버 리소스에 제한**이 있었고, 시간이 촉박했으며, 추가 인프라를 확장하고 관리할만한 **MLOps 인력이 부족**한 상황
- `EXPLAIN ANALYZE` 실행 결과상 쿼리에 `성능 저하 요소` 발견
    1. **정렬 (Sort) 연산 비용이 큼**
        
        > **원인**: `CASE WHEN`으로 동적 정렬을 수행하면서 **인덱스를 활용하지 못하고 전체 정렬 수행** 중.
        > 
        
        ```
        Sort: (CASE WHEN ... END) DESC (actual time=44.9..45) <- (이 부분에서 정렬하는 데 많은 시간이 걸림)
        ```
        
    2. **Nested Loop Join 성능 저하**
        
        > **원인**: `followedLikedReview` 테이블에서 **테이블 스캔 발생**, 조인 비용이 높음.
        > 
        
        ```
        Nested loop inner join (cost=179849 rows=0) (actual time=38.6..42 rows=1214 loops=1)
        Table scan on followedLikedReview (cost=85816..85816 rows=0) (actual time=38.6..38.8 rows=1214 loops=1)
        ```
        
    3. **중복된** `LIKE_REVIEW` **접근**
        
        > **원인**: `Nested Loop Join`에서 `like_review` 조회가 반복적으로 수행됨.
        > 
        
        ```
        Single-row index lookup on lr using uniqueField (userId = fu.followingId, reviewId = re.id) (cost=0.25 rows=1) (never executed)
        ```
        
    4. **임시 테이블 (`Materialize` & `Table scan on <temporary>`) 사용**
        
        > **원인**: `GROUP BY`에서 서브쿼리를 활용하며 MySQL이 임시 테이블을 만들어 사용.
        > 
        
        ```
        Materialize (cost=0..0 rows=0) (actual time=19.4..19.4 rows=0 loops=1)
        ```
        

---

## **대안**

- MLOps를 통한 추천 모델 구축
    - `문제점` : 위에서도 언급했듯이 **인프라 세팅을 위한 시간과 추가 인력**이 필요함. 써드파티를 사용하더라도 **AI 분야 Saas의 높은 비용**으로 어려움.
- 캐싱을 통한 임시 데이터스토어(Redis) 사용.
    - `문제점` : 캐싱을 사용한 방법도 있었지만, 하나의 액션이 여러 사용자의 캐시에 영향을 미치는 관계(연쇄반응)로 **캐시 무효화 로직 설계에 시간소요**가 크다고 판단.

---

## **해결**

- 불필요한 임시 테이블 생성과 IO를 최소화한 버전의 SQL
    
    1. `ORDER BY` **인덱스 활용**
    
    - `CASE WHEN` 정렬을 줄이고, `createdAt`, `readDate`, `likedDate`를 단순 정렬 기준으로 변경하여 기존 인덱스를 탈 수 있도록 함.
    
    2. `Nested Loop Join`을 **`Hash Join` 또는 `Derived Table`로 변경**
    
    - `followedLikedReview` 서브쿼리를 미리 연산하여 `JOIN`을 최적화.
    
    3. `LIKE_REVIEW` **테이블 접근 최적화**
    
    - `LIMIT 1`을 활용하여 `reviewId` 별 가장 가까운 `likedDate`를 가져옴.
    
    4. `GROUP BY`에서 `HAVING`을 제거하고, **사전 필터링**
    
    - `HAVING MIN(ABS(...))`을 사전에 필터링하여 임시 테이블 사용을 줄임.
    1. `WITH (CTE)`를 통한 **메모리 효율화**
        - 같은 데이터를 여러 번 조회하지 않고 **서브쿼리를 한 번만 실행한 후 결과를 메모리에 저장하여 재사용하여** JOIN 성능을 향상
    
    ```sql
    WITH ReadReview AS (
        -- 사용자가 읽은 리뷰 중 가장 오래된 `readDate` 가져오기
        SELECT rr.reviewId, MIN(rr.createdAt) AS readDate
        FROM read_review rr
        WHERE rr.userId = @userId
        GROUP BY rr.reviewId
    ),
    LikedReview AS (
        -- 사용자가 팔로우한 사람이 좋아요를 누른 시간 중 가장 가까운 likedDate 가져오기
        SELECT lr.reviewId, MIN(lr.createdAt) AS likedDate
        FROM follow_user fu
        INNER JOIN like_review lr ON lr.userId = fu.followingId
        WHERE fu.followerId = @userId
        GROUP BY lr.reviewId
    )
    SELECT 
        re.id AS reviewId, 
        re.userId AS userId,
        rr.readDate, 
        lr.likedDate,
        ABS(IFNULL(TIMESTAMPDIFF(SECOND, lr.likedDate, rr.readDate), 0)) AS diffDate
    FROM review re
    LEFT JOIN ReadReview rr ON rr.reviewId = re.id
    LEFT JOIN LikedReview lr ON lr.reviewId = re.id
    ORDER BY re.createdAt DESC, rr.readDate DESC, lr.likedDate DESC;
    ```
    
- `EXPLAIN ANALYZE` 실행 결과 기반 성능 개선 수치 비교표
    
    
    | **항목** | **기존 쿼리** | **최적화된 쿼리** | **성능 개선** |
    | --- | --- | --- | --- |
    | **정렬 (Sort)** | `actual time=44.9..45` | `actual time=5..5.07` | **약 9배 빠름** |
    | **Stream Results** | `actual time=38.7..44` | `actual time=0.924..4.36` |  **약 10배 향상** |
    | **Nested Loop Join** | `actual time=38.6..42` | `actual time=0.767..3.43` |  **약 10배 빠름** |
    | **Table Scan (review 테이블)** | `cost=147 rows=1226 (actual time=1.38..2.23)` | `cost=147 rows=1226 (actual time=0.58..2.37)` | **약간 개선** |
    | **테이블 스캔 (read_review 테이블)** | `cost=2.5 rows=0 (actual time=15.2..19.3)` | `cost=2.5 rows=0 (actual time=0.00638..0.00638)` | **3000배 이상 개선** |
    | **LIKE_REVIEW 조회** | `cost=0.25 rows=1 (never executed)` | `cost=0.25..0.9 rows=3.6 (actual time=392e-6..392e-6)` | **조회 안정성 개선** |
    | **임시 테이블 사용 (`Materialize`)** | `actual time=19.4..19.4` | `actual time=0.0467..0.0467` | **약 400배 개선** |

---

## **고찰**

1. 데이터 접근 방식을 최적화하는 것이 가장 중요

> 기존 쿼리는 모든 데이터를 가져온 후 `HAVING`을 사용해 필터링하는 방식이었기 때문에 성능이 저하되었습니다.
> 
> 
> `WITH (CTE)`를 활용해 `read_review`와 `like_review` 데이터를 미리 그룹화한 후 `JOIN`을 수행하니 불필요한 데이터 스캔이 줄어들어 실행 속도가 9배 이상 개선되었습니다.
> 
1. `EXPLAIN ANALYZE`를 활용한 정밀한 분석이 필수
    
    > 단순히 실행 시간을 확인하는 것이 아니라, `rows_examined`, `filtered`, `cost` 등의 지표를 통해 정확한 성능 병목을 분석했습니다.
    > 
    > 
    > 이를 통해 불필요한 테이블 스캔을 줄이고, 인덱스를 적극 활용하도록 최적화할 수 있었습니다.
    > 

3. 데이터베이스 엔진 이해의 중요성

> SQL 작성자가 데이터베이스 엔진의 내부 동작 원리를 이해할 때 훨씬 효율적인 쿼리를 설계할 수 있습니다. 쿼리 실행 계획, 인덱스 활용, 조인 최적화 등 엔진의 작동 방식에 대한 지식이 성능 차이를 만드는 핵심 요소입니다.
> 

이번 최적화를 통해 SQL 최적화는 단순한 코드 변경이 아니라, 데이터 접근 방식을 설계하는 과정이라는 점을 깨달았습니다. 그리고 쿼리를 작성할 때부터 최적화를 고려하고, 아래 원칙을 사용하도록 하는 방향성을 수립할 수 있었습니다.

필요한 데이터만 미리 필터링한 후 `JOIN`을 수행할 것 / Nested Loop Join을 줄이고, `JOIN` 순서를 최적화할 것 / `ORDER BY`에서 인덱스를 활용할 수 있도록 설계할 것 / `EXPLAIN ANALYZE`를 적극 활용해 성능 병목을 분석할 것.