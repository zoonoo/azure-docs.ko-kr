---
title: 구체화된 뷰를 사용한 성능 조정
description: 구체화 된 뷰를 사용 하 여 쿼리 성능을 향상 시킬 때 알아야 할 권장 사항 및 고려 사항입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 30ca03633b9b0788235439204a3c1926fe6b6a6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429981"
---
# <a name="performance-tuning-with-materialized-views"></a>구체화된 뷰를 사용한 성능 조정

Synapse SQL 풀에서 구체화 된 뷰는 복잡 한 분석 쿼리에 대해 낮은 유지 관리 방법을 제공 하 여 쿼리를 변경 하지 않고 빠른 성능을 얻을 수 있습니다. 이 문서에서는 구체화 된 뷰 사용에 대 한 일반적인 지침을 설명 합니다.

## <a name="materialized-views-vs-standard-views"></a>구체화 된 뷰 및 표준 뷰 비교

SQL 풀은 표준 뷰와 구체화 된 뷰를 모두 지원 합니다.  둘 다 SELECT 식을 사용 하 여 생성 되 고 쿼리를 논리 테이블로 표시 하는 가상 테이블입니다.  뷰는 일반적인 데이터 계산의 복잡성을 표시 하 고 계산 변경에 추상화 계층을 추가 하므로 쿼리를 다시 작성할 필요가 없습니다.  

표준 뷰는 뷰가 사용 될 때마다 데이터를 계산 합니다.  디스크에 저장 된 데이터가 없습니다. 일반적으로 사용자는 데이터베이스에서 논리 개체와 쿼리를 구성 하는 데 도움이 되는 도구로 표준 뷰를 사용 합니다.  표준 뷰를 사용 하려면 쿼리에서 해당 뷰를 직접 참조 해야 합니다.

구체화 된 뷰는 테이블과 마찬가지로 SQL 풀에서 데이터를 미리 계산 하 고 저장 하 고 유지 관리 합니다.  구체화 된 뷰가 사용 될 때마다 다시 계산 기능 필요 하지 않습니다.  따라서 구체화 된 뷰에서 데이터의 일부 또는 전부를 사용 하는 쿼리는 더 빠른 성능을 얻을 수 있습니다.  또한 쿼리에서는 구체화 된 뷰를 직접 참조 하지 않고 사용할 수 있으므로 응용 프로그램 코드를 변경할 필요가 없습니다.  

대부분의 표준 뷰 요구 사항은 구체화 된 뷰에 여전히 적용 됩니다. 구체화 된 뷰 구문 및 기타 요구 사항에 대 한 자세한 내용은 [구체화 된 뷰를 SELECT로 만들기](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조 하세요.

| 비교                     | 보기                                         | 구체화된 뷰
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|정의 보기                 | Azure data warehouse에 저장 됩니다.              | Azure data warehouse에 저장 됩니다.
|콘텐츠 보기                    | 뷰가 사용 될 때마다 생성 됩니다.   | 뷰를 만드는 동안 Azure data warehouse에서 사전 처리 되 고 저장 됩니다. 기본 테이블에 데이터가 추가 될 때 업데이트 됩니다.
|데이터 새로 고침                    | 항상 업데이트 됨                               | 항상 업데이트 됨
|복잡 한 쿼리에서 뷰 데이터를 검색 하는 속도     | 느림                                         | 빠름  
|추가 저장소                   | 아니요                                           | 예
|구문                          | CREATE VIEW                                  | 구체화 된 뷰를 SELECT로 만들기

## <a name="benefits-of-using-materialized-views"></a>구체화 된 뷰 사용의 이점

잘 디자인 된 구체화 된 뷰는 다음과 같은 이점을 제공 합니다.

- 조인 및 집계 함수를 사용 하는 복잡 한 쿼리에 대 한 실행 시간이 줄어듭니다. 쿼리가 복잡할수록 실행 시간 저장 가능성이 높아집니다. 쿼리의 계산 비용이 높고 결과 데이터 집합이 작은 경우 가장 많은 이점을 얻을 수 있습니다.  

- SQL 풀의 최적화 프로그램은 자동으로 배포 된 구체화 된 뷰를 사용 하 여 쿼리 실행 계획을 향상 시킬 수 있습니다.  이 프로세스는 더 빠른 쿼리 성능을 제공 하는 사용자에 게는 투명 하며 구체화 된 뷰에 대 한 직접 참조를 수행 하는 쿼리는 필요 하지 않습니다.

- 뷰에 대해 낮은 유지 관리가 필요 합니다.  구체화 된 뷰는 뷰를 만들 때 초기 데이터에 대 한 클러스터형 columnstore 인덱스와 증분 데이터 변경 내용에 대 한 델타 저장소 라는 두 곳에 데이터를 저장 합니다.  기본 테이블의 모든 데이터 변경 내용은 동기 방식으로 델타 저장소에 자동으로 추가 됩니다.  백그라운드 프로세스 (튜플 이동 기)는 주기적으로 델타 저장소에서 뷰의 columnstore 인덱스로 데이터를 이동 합니다.  이 디자인에서는 구체화 된 뷰를 쿼리하여 기본 테이블을 직접 쿼리 하는 것과 동일한 데이터를 반환할 수 있습니다.
- 구체화 된 뷰의 데이터를 기본 테이블과 다르게 배포할 수 있습니다.  
- 구체화 된 뷰의 데이터는 일반 테이블의 데이터와 동일한 고가용성 및 복원 력 혜택을 얻습니다.  

다른 데이터 웨어하우스 공급자와 비교할 때 SQL 풀에서 구현 된 구체화 된 뷰는 다음과 같은 추가 이점도 제공 합니다.

- 기본 테이블의 데이터 변경 내용으로 자동 및 동기 데이터 새로 고침 추가적인 조치가 필요하지 않습니다.
- 광범위 한 집계 함수 지원. [구체화 된 뷰를 SELECT로 만들기 (transact-sql)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조 하세요.
- 쿼리와 관련 된 구체화 된 뷰 권장 사항을 지원 합니다.  [설명 (transact-sql)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조 하세요.

## <a name="common-scenarios"></a>일반적인 시나리오  

구체화 된 뷰는 일반적으로 다음과 같은 시나리오에서 사용 됩니다.

**크기가 큰 데이터에 대해 복잡 한 분석 쿼리의 성능을 향상 시켜야 합니다.**

복잡 한 분석 쿼리는 일반적으로 더 많은 집계 함수 및 테이블 조인을 사용 하 여 쿼리 실행의 섞 습니다 및 조인과 같은 보다 계산 작업이 많은 작업을 수행 합니다.  따라서 특히 큰 테이블의 경우 이러한 쿼리를 완료 하는 데 시간이 더 오래 걸립니다.  

사용자는 일반적인 쿼리 계산에서 반환 된 데이터에 대 한 구체화 된 뷰를 만들 수 있습니다. 따라서 쿼리에서이 데이터를 필요로 하는 경우에는 다시 계산 기능 필요 하지 않습니다.

**쿼리나 최소 쿼리 변경으로 더 빠른 성능 필요**

일반적으로 일반 ETL 작업 및 보고를 지원 하기 위해 데이터 웨어하우스의 스키마 및 쿼리 변경 내용이 최소한으로 유지 됩니다.  뷰를 통해 발생 하는 비용을 쿼리 성능 향상으로 오프셋할 수 있는 경우 사용자는 쿼리 성능 튜닝을 위해 구체화 된 뷰를 사용할 수 있습니다.

크기 조정 및 통계 관리와 같은 다른 튜닝 옵션과 비교 하 여 구체화 된 뷰를 생성 및 유지 관리 하기 위해 프로덕션 변경을 훨씬 줄이고 잠재적 성능 향상도 더 높습니다.

- 구체화 된 뷰를 만들거나 유지 관리 하는 것은 기본 테이블에 대해 실행 되는 쿼리에 영향을 주지 않습니다.
- 쿼리 최적화 프로그램은 쿼리에서 직접 뷰 참조 없이 배포 된 구체화 된 뷰를 자동으로 사용할 수 있습니다. 이 기능을 통해 성능 튜닝에 대 한 쿼리 변경의 필요성이 줄어듭니다.

**쿼리 성능 향상을 위해 다른 데이터 배포 전략이 필요 합니다.**

Azure data warehouse는 MPP (distributed and 대규모 parallel processing) 시스템입니다.   데이터 웨어하우스 테이블의 데이터는 세 가지 [배포 전략](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (해시, round_robin 또는 복제) 중 하나를 사용 하 여 60 노드 간에 분산 됩니다.  

데이터 분포는 테이블을 만들 때 지정 되며 테이블을 삭제할 때까지 변경 되지 않은 상태로 유지 됩니다. 디스크의 가상 테이블이 되는 구체화 된 뷰는 해시 및 round_robin 데이터 배포를 지원 합니다.  사용자는 기본 테이블과 다른 데이터 배포를 선택할 수 있지만 뷰를 자주 사용 하는 쿼리의 성능에 가장 적합 합니다.  

## <a name="design-guidance"></a>디자인 지침

구체화 된 뷰를 사용 하 여 쿼리 성능을 개선 하는 일반적인 지침은 다음과 같습니다.

**워크 로드 디자인**

구체화 된 뷰를 만들기 전에 쿼리 패턴, 중요도, 빈도 및 결과 데이터 크기를 기준으로 작업을 깊이 이해 하는 것이 중요 합니다.  

사용자는 쿼리 최적화 프로그램에서 권장 하는 구체화 된 뷰에 대 한 WITH_RECOMMENDATIONS <SQL_statement>에 대 한 설명을 실행할 수 있습니다.  이러한 권장 사항은 쿼리 별로 다르므로 단일 쿼리를 활용 하는 구체화 된 뷰가 같은 작업의 다른 쿼리에는 최적이 아닐 수 있습니다.  

작업 요구 사항에 따라 이러한 권장 사항을 평가 합니다.  이상적인 구체화 된 뷰는 워크 로드의 성능에 도움이 되는 뷰입니다.  

**더 빠른 쿼리 및 비용 간의 균형을 염두에 두어야 합니다.**

구체화 된 각 뷰에 대해 데이터 저장 비용 및 보기 유지 관리 비용이 있습니다.  기본 테이블의 데이터가 변경 되 면 구체화 된 뷰의 크기가 늘어나고 물리적 구조도 변경 됩니다.  

쿼리 성능 저하를 방지 하기 위해 구체화 된 각 뷰는 델타 저장소에서 columnstore 인덱스 세그먼트로 행을 이동 하 고 데이터 변경 내용을 통합 하는 등 데이터 웨어하우스 엔진에서 별도로 유지 관리 됩니다.  

구체화 된 뷰 및 기본 테이블 변경 수가 늘어나면 유지 관리 작업은 climbs.   사용자는 모든 구체화 된 뷰에서 발생 한 비용을 쿼리 성능 향상에 따라 오프셋할 수 있는지 확인 해야 합니다.  

데이터베이스의 구체화 된 뷰 목록에 대해이 쿼리를 실행할 수 있습니다.

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

구체화 된 뷰의 수를 줄이는 옵션:

- 작업에서 복잡 한 쿼리에 자주 사용 되는 공통 데이터 집합을 식별 합니다.  최적화 프로그램에서 실행 계획을 만들 때 이러한 데이터 집합을 구성 요소로 사용할 수 있도록 이러한 데이터 집합을 저장 하는 구체화 된 뷰를 만듭니다.  

- 사용량이 낮거나 더 이상 필요 하지 않은 구체화 된 뷰를 삭제 합니다.  비활성화 된 구체화 된 뷰는 유지 관리 되지 않지만 저장소 비용은 계속 발생 합니다.  

- 데이터가 겹치지 않는 경우에도 동일한 기본 테이블 또는 유사한 기본 테이블에 생성 된 구체화 된 뷰를 결합 합니다.  구체화 된 뷰를 결합 하면 별도 보기의 합계 보다 크기가 큰 보기가 생성 될 수 있지만 보기 유지 관리 비용은 줄어듭니다.  다음은 그 예입니다.

```sql
-- Query 1 would benefit from having a materialized view created with this SELECT statement
SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement
SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form
SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**모든 성능 튜닝에서 쿼리를 변경 해야 하는 것은 아닙니다.**

데이터 웨어하우스 최적화 프로그램은 자동으로 배포 된 구체화 된 뷰를 사용 하 여 쿼리 성능을 향상 시킬 수 있습니다.  이 지원은 뷰를 참조 하지 않는 쿼리와 구체화 된 뷰 만들기에서 지원 되지 않는 집계를 사용 하는 쿼리에 투명 하 게 적용 됩니다.  쿼리를 변경할 필요가 없습니다. 쿼리의 예상 실행 계획을 확인 하 여 구체화 된 뷰가 사용 되는지 확인할 수 있습니다.  

**구체화 된 뷰 모니터링**

구체화 된 뷰는 CCI (클러스터형 columnstore 인덱스)가 있는 테이블과 마찬가지로 데이터 웨어하우스에 저장 됩니다.  구체화 된 뷰에서 데이터를 읽으면 인덱스를 검색 하 고 델타 저장소에서 변경 내용을 적용 하는 작업이 포함 됩니다.  델타 저장소의 행 수가 너무 높으면 구체화 된 뷰에서 쿼리를 확인 하는 것이 기본 테이블을 직접 쿼리 하는 것 보다 시간이 더 오래 걸릴 수 있습니다.  쿼리 성능 저하를 방지 하려면 [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 를 실행 하 여 뷰의 overhead_ratio (total_rows/base_view_row)를 모니터링 하는 것이 좋습니다.  Overhead_ratio 너무 높으면 델타 저장소의 모든 행이 columnstore 인덱스로 이동 되도록 구체화 된 뷰를 다시 작성 하는 것이 좋습니다.  

**구체화 된 뷰 및 결과 집합 캐싱**

이러한 두 기능은 쿼리 성능 튜닝을 위해 동시에 SQL 풀에서 도입 되었습니다. 결과 집합 캐싱은 정적 데이터에 대 한 반복적인 쿼리에서 높은 동시성 및 빠른 응답 시간을 달성 하는 데 사용 됩니다.  

캐시 된 결과를 사용 하려면 쿼리를 요청 하는 캐시의 형식이 캐시를 생성 한 쿼리와 일치 해야 합니다.  또한 캐시 된 결과가 전체 쿼리에 적용 되어야 합니다.  구체화 된 뷰를 사용 하면 기본 테이블의 데이터를 변경할 수 있습니다.  구체화 된 뷰의 데이터는 쿼리 조각에 적용 될 수 있습니다.  이 지원을 통해 더 빠른 성능을 위해 일부 계산을 공유 하는 여러 쿼리에서 동일한 구체화 된 뷰를 사용할 수 있습니다.

## <a name="example"></a>예제

이 예제에서는 매장 보다 카탈로그를 통해 더 많은 비용을 소비 하는 고객을 찾는 TPCDS와 유사한 쿼리를 사용 합니다. 또한 기본 설정 된 고객과 원본 국가를 식별 합니다.   이 쿼리에는 SUM ()과 GROUP BY를 포함 하는 세 개의 하위 SELECT 문 조합에서 상위 100 레코드를 선택 하는 작업이 포함 됩니다.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

쿼리의 예상 실행 계획을 확인 합니다.  18 개의 섞 습니다 및 17 조인 작업은 실행 하는 데 더 많은 시간이 걸립니다. 이제 세 개의 하위 SELECT 문에 대해 구체화 된 뷰 하나를 만듭니다.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

원래 쿼리의 실행 계획을 다시 확인 합니다.  이제 조인 수가 17에서 5로 변경 되 고 더 이상 순서 섞기가 발생 하지 않습니다.  계획에서 필터 작업 아이콘을 클릭 합니다. 해당 출력 목록에는 기본 테이블 대신 구체화 된 뷰에서 읽은 데이터가 표시 됩니다.  

 ![Plan_Output_List_with_Materialized_Views](./media/develop-materialized-view-performance-tuning/output-list.png)

구체화 된 뷰를 사용 하면 코드를 변경 하지 않고 동일한 쿼리가 훨씬 빠르게 실행 됩니다.  

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [SYNAPSE SQL development 개요](develop-overview.md)를 참조 하세요.
 