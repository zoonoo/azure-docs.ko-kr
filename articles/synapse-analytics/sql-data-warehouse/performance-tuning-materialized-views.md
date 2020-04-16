---
title: 구체화된 뷰를 사용한 성능 조정
description: 쿼리 성능을 향상시키기 위해 구체화된 보기를 사용할 때 알아야 할 권장 사항 및 고려 사항입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 6a3235d5edc5249bbbdc2e79dac8575ad26fd5e1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417036"
---
# <a name="performance-tuning-with-materialized-views"></a>구체화된 뷰를 사용한 성능 조정

Synapse SQL 풀의 구체화된 뷰는 복잡한 분석 쿼리가 쿼리를 변경하지 않고 빠른 성능을 얻을 수 있도록 낮은 유지 관리 방법을 제공합니다. 이 문서에서는 구체화된 뷰 사용에 대한 일반적인 지침에 대해 설명합니다.

SQL 풀의 구체화된 뷰는 복잡한 분석 쿼리가 쿼리를 변경하지 않고 빠른 성능을 얻을 수 있도록 낮은 유지 관리 방법을 제공합니다. 이 문서에서는 구체화된 뷰 사용에 대한 일반적인 지침에 대해 설명합니다.

## <a name="materialized-views-vs-standard-views"></a>구체화된 뷰와 표준 뷰

SQL 풀은 표준 및 구체화된 뷰를 지원합니다.  둘 다 SELECT 식으로 만든 가상 테이블이며 쿼리에 논리적 테이블로 제공됩니다.  뷰는 일반적인 데이터 계산의 복잡성을 캡슐화하고 계산 변경 사항에 추상화 계층을 추가하여 쿼리를 다시 작성할 필요가 없습니다.  

표준 뷰는 뷰를 사용할 때마다 데이터를 계산합니다.  디스크에 저장된 데이터가 없습니다. 사람들은 일반적으로 표준 뷰를 데이터베이스의 논리 적 개체와 쿼리를 구성하는 데 도움이되는 도구로 사용합니다.  표준 뷰를 사용하려면 쿼리를 직접 참조해야 합니다.

구체화된 뷰는 테이블과 마찬가지로 SQL 풀에서 데이터를 미리 계산, 저장 및 유지 관리합니다.  구체화된 뷰를 사용할 때마다 다시 계산할 필요가 없습니다.  따라서 구체화된 뷰에서 데이터의 전부 또는 하위 집합을 사용하는 쿼리는 더 빠른 성능을 얻을 수 있습니다.  더 좋은 것은 쿼리가 직접 참조하지 않고도 구체화된 뷰를 사용할 수 있으므로 응용 프로그램 코드를 변경할 필요가 없습니다.  

표준 뷰의 대부분의 요구 사항은 여전히 구체화된 뷰에 적용됩니다. 구체화된 뷰 구문 및 기타 요구 사항에 대한 자세한 내용은 [구체화된 뷰 만들기 를 선택으로](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 참조하십시오.

| 비교                     | 보기                                         | 구체화된 뷰
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|정의 보기                 | SQL 풀에 저장됩니다.              | SQL 풀에 저장됩니다.
|콘텐츠 보기                    | 뷰를 사용할 때마다 생성됩니다.   | 뷰를 만드는 동안 SQL 풀에 미리 처리되고 저장됩니다. 데이터가 기본 테이블에 추가될 때 업데이트됩니다.
|데이터 새로 고침                    | 항상 업데이트                               | 항상 업데이트
|복잡한 쿼리에서 보기 데이터를 검색하는 속도     | 느림                                         | 빠름  
|추가 스토리지                   | 예                                           | 예
|구문                          | CREATE VIEW                                  | 구체화된 뷰를 선택으로 작성

## <a name="benefits-of-using-materialized-views"></a>구체화된 뷰 사용의 이점

적절하게 설계된 구체화된 뷰는 다음과 같은 이점을 제공합니다.

- JOIN 및 집계 함수를 사용하면 복잡한 쿼리의 실행 시간을 줄일 수 있습니다. 쿼리가 복잡할수록 실행 시간 절약 가능성이 높아지습니다. 쿼리의 계산 비용이 높고 결과 데이터 집합이 작을 때 가장 큰 이점을 얻을 수 있습니다.  
- SQL 풀의 최적화 프로그램은 배포된 구체화된 뷰를 자동으로 사용하여 쿼리 실행 계획을 개선할 수 있습니다.  이 프로세스는 더 빠른 쿼리 성능을 제공하는 사용자에게 투명하며 구체화된 뷰를 직접 참조하기 위해 쿼리가 필요하지 않습니다.
- 뷰에 대한 낮은 유지 관리가 필요합니다.  기본 테이블의 모든 증분 데이터 변경 사항은 동기 식으로 구체화된 뷰에 자동으로 추가됩니다.  이 디자인을 사용하면 구체화된 뷰를 쿼리하여 기본 테이블을 직접 쿼리하는 것과 동일한 데이터를 반환할 수 있습니다.
- 구체화된 뷰의 데이터는 기본 테이블과 다르게 배포할 수 있습니다.  
- 구체화된 뷰의 데이터는 일반 테이블의 데이터와 동일한 고가용성 및 복원력 이점을 제공합니다.  

SQL 풀에서 구현된 구체화된 뷰는 다음과 같은 추가 이점을 제공합니다.

다른 데이터 웨어하우스 공급자와 비교하여 Azure SQL Data Warehouse에서 구현된 구체화된 보기는 다음과 같은 추가 이점을 제공합니다.

- 기본 테이블의 데이터 변경으로 자동 및 동기 데이터 새로 고침 추가적인 조치가 필요하지 않습니다.
- 광범위한 집계 함수 지원. [구체화된 보기 만들기를 선택(거래-SQL)으로 클릭합니다.](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- 쿼리별 구체화된 뷰 권장 사항에 대한 지원입니다.  [설명(거래-SQL)을](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)참조하십시오.

## <a name="common-scenarios"></a>일반적인 시나리오  

구체화된 뷰는 일반적으로 다음 시나리오에서 사용됩니다.

**대규모 데이터에 대한 복잡한 분석 쿼리의 성능을 개선해야 합니다.**

복잡한 분석 쿼리는 일반적으로 더 많은 집계 함수와 테이블 조인을 사용하므로 셔플 및 쿼리 실행에 조인과 같은 계산이 많은 작업이 더 많이 발생합니다.  따라서 이러한 쿼리를 완료하는 데 특히 큰 테이블에서 완료하는 데 시간이 오래 걸릴 수 있습니다.  

사용자는 쿼리의 일반적인 계산에서 반환된 데이터에 대한 구체화된 뷰를 만들 수 있으므로 쿼리에 이 데이터가 필요할 때 다시 계산할 필요가 없으므로 계산 비용이 절감되고 쿼리 응답속도가 빨라집니다.

**최소한의 쿼리 변경 없이 더 빠른 성능 필요**

SQL 풀의 스키마 및 쿼리 변경 은 일반적으로 정기적인 ETL 작업 및 보고를 지원하기 위해 최소한으로 유지됩니다.  뷰에 의해 발생하는 비용이 쿼리 성능 향상으로 상쇄될 수 있는 경우 사용자는 쿼리 성능 조정을 위해 구체화된 뷰를 사용할 수 있습니다.

크기 조정 및 통계 관리와 같은 다른 튜닝 옵션과 비교할 때 구체화된 뷰를 만들고 유지하기 위한 생산 변경의 영향이 훨씬 적으며 잠재적인 성능 향상도 더 높습니다.

- 구체화된 뷰를 만들거나 유지 관리해도 기본 테이블에 대해 실행되는 쿼리에는 영향을 미치지 않습니다.
- 쿼리 최적화 프로그램은 쿼리에서 직접 보기 참조 없이 배포된 구체화된 뷰를 자동으로 사용할 수 있습니다. 이 기능은 성능 튜닝에서 쿼리 를 변경할 필요가 줄어듭니다.

**더 빠른 쿼리 성능을 위해 다양한 데이터 배포 전략이 필요합니다.**

SQL 풀은 MPP(대규모 병렬 처리) 시스템으로 분산되어 있습니다.   SQL 풀테이블의 데이터는 세 가지 [배포 전략(해시,](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) round_robin 또는 복제)중 하나를 사용하여 60개 노드에 분산됩니다.  

데이터 분포는 테이블 생성 시간에 지정되며 테이블이 삭제될 때까지 변경되지 않습니다. 디스크의 가상 테이블이 되는 구체화된 뷰는 해시및 round_robin 데이터 분포를 지원합니다.  사용자는 기본 테이블과 는 다르지만 뷰를 가장 많이 사용하는 쿼리의 성능에 최적인 데이터 분포를 선택할 수 있습니다.  

## <a name="design-guidance"></a>디자인 지침

다음은 쿼리 성능을 향상시키기 위해 구체화된 보기를 사용하는 방법에 대한 일반적인 지침입니다.

**워크로드를 위한 설계**

구체화된 뷰를 만들기 전에 쿼리 패턴, 중요도, 빈도 및 결과 데이터의 크기 측면에서 워크로드를 심층적으로 이해하는 것이 중요합니다.  

사용자는 쿼리 최적화 WITH_RECOMMENDATIONS 권장하는 구체화된 보기에 대해 설명 <SQL_statement> 실행할 수 있습니다.  이러한 권장 사항은 쿼리에 따라 다르므로 단일 쿼리에 이점을 제공하는 구체화된 보기는 동일한 워크로드의 다른 쿼리에 최적이 아닐 수 있습니다.  

워크로드 요구 사항을 염두에 두고 이러한 권장 사항을 평가합니다.  이상적인 구체화된 뷰는 워크로드의 성능에 도움이 되는 뷰입니다.  

**더 빠른 쿼리와 비용 간의 장단점에 유의하세요.**

구체화된 각 보기에는 데이터 저장소 비용과 뷰 유지 관리 비용이 있습니다.  기본 테이블의 데이터가 변경됨에 따라 구체화된 뷰의 크기가 증가하고 물리적 구조도 변경됩니다.  쿼리 성능 저하를 방지하기 위해 구체화된 각 보기는 SQL 풀 엔진에서 별도로 유지 관리됩니다.  

구체화된 뷰 수와 기본 테이블 변경 수가 증가하면 유지 관리 워크로드가 증가합니다.   사용자는 모든 구체화된 뷰에서 발생하는 비용을 쿼리 성능 향상으로 상쇄할 수 있는지 확인해야 합니다.  

데이터베이스에서 구체화된 보기 목록에 대해 이 쿼리를 실행할 수 있습니다.

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

구체화된 뷰 수를 줄이는 옵션:

- 워크로드의 복잡한 쿼리에서 자주 사용하는 공통 데이터 집합을 식별합니다.  구체화된 뷰를 만들어 이러한 데이터 집합을 저장하므로 최적화 프로그램이 실행 계획을 작성할 때 이를 구성 요소로 사용할 수 있습니다.  

- 사용량이 적거나 더 이상 필요하지 않은 구체화된 뷰를 삭제합니다.  비활성화된 구체화된 뷰는 유지 관리되지 않지만 저장소 비용이 계속 발생합니다.  

- 데이터가 겹치지 않더라도 동일하거나 유사한 기본 테이블에서 만든 구체화된 뷰를 결합합니다.  구체화된 뷰를 결합하면 별도의 뷰의 합계보다 크기가 커질 수 있지만 뷰 유지 관리 비용은 줄어듭니다.  다음은 그 예입니다.

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

**모든 성능 튜닝에 쿼리 변경이 필요한 것은 아닙니다.**

SQL 풀 최적화 프로그램은 배포된 구체화된 뷰를 자동으로 사용하여 쿼리 성능을 향상시킬 수 있습니다.  이 지원은 구체화된 뷰 만들기에서 지원되지 않는 집계를 사용하는 뷰 및 쿼리를 참조하지 않는 쿼리에 투명하게 적용됩니다.  쿼리를 변경할 필요가 없습니다. 쿼리의 예상 실행 계획을 확인하여 구체화된 보기가 사용되는지 확인할 수 있습니다.  

**구체화된 뷰 모니터링**

구체화된 뷰는 클러스터된 열저장소 인덱스(CCI)가 있는 테이블과 마찬가지로 SQL 풀에 저장됩니다.  구체화된 뷰에서 데이터를 읽는 에는 CCI 인덱스 세그먼트를 스캔하고 기본 테이블에서 증분 변경 사항을 적용하는 것이 포함됩니다. 증분 변경 수가 너무 많으면 구체화된 뷰에서 쿼리를 해결하는 데 기본 테이블을 직접 쿼리하는 것보다 더 오래 걸릴 수 있습니다.  

쿼리 성능 저하를 방지하려면 뷰의 overhead_ratio(total_rows/최대(1, base_view_row))를 모니터링하기 위해 [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 실행하는 것이 좋습니다.  overhead_ratio 너무 높으면 구체화된 뷰를 다시 작성해야 합니다.

**구체화된 뷰 및 결과 세트 캐싱**

이러한 두 기능은 쿼리 성능 튜닝을 위해 거의 동시에 SQL 풀에 도입됩니다.  결과 집합 캐싱은 정적 데이터에 대한 반복쿼리에서 높은 동시성과 빠른 응답을 얻는 데 사용됩니다.  

캐시된 결과를 사용하려면 쿼리를 요청하는 캐시의 형태가 캐시를 생성한 쿼리와 일치해야 합니다.  또한 캐시된 결과는 전체 쿼리에 적용되어야 합니다.  

구체화된 뷰를 사용하면 기본 테이블의 데이터를 변경할 수 있습니다.  구체화된 뷰의 데이터는 쿼리의 한 부분에 적용할 수 있습니다.  이 지원을 통해 더 빠른 성능을 위해 일부 계산을 공유하는 여러 쿼리에서 동일한 구체화된 뷰를 사용할 수 있습니다.

## <a name="example"></a>예제

이 예제에서는 TPCDS와 같은 쿼리를 사용하여 매장보다 카탈로그를 통해 더 많은 비용을 지출하는 고객을 찾아선호하는 고객과 출신 국가를 식별합니다.   쿼리에는 SUM() 및 GROUP BY와 관련된 세 개의 하위 SELECT 문의 UNION에서 TOP 100 레코드를 선택하는 작업이 포함됩니다.

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

쿼리의 예상 실행 계획을 확인합니다.  18개의 셔플과 17개의 조인 작업이 있으며, 실행하는 데 더 많은 시간이 소요됩니다. 이제 세 개의 하위 SELECT 문 각각에 대해 하나의 구체화된 뷰를 만들어 보겠습니다.

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

원래 쿼리의 실행 계획을 다시 확인합니다.  이제 조인 수가 17개에서 5개로 변경되고 더 이상 셔플이 없습니다.  계획에서 필터 작업 아이콘을 클릭하면 출력 목록에서 데이터가 기본 테이블 대신 구체화된 뷰에서 읽혀지는 것을 보여 주었습니다.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

구체화된 뷰를 사용하면 코드 변경 없이 동일한 쿼리가 훨씬 빠르게 실행됩니다.  

## <a name="next-steps"></a>다음 단계

자세한 개발 팁은 [Synapse SQL 풀 개발 개요를](sql-data-warehouse-overview-develop.md)참조하십시오.
