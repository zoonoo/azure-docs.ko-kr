---
title: SQL 데이터 웨어하우스 | Microsoft Docs
description: 솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 동시성 및 워크로드 관리를 이해합니다.
services: sql-data-warehouse
documentationcenter: NA
author: sonyam
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 09/27/2016
ms.author: sonyama;barbkess;jrj

---
# SQL 데이터 웨어하우스의 동시성 및 워크로드 관리
예측 가능한 성능을 광범위하게 제공하려는 경우 Microsoft Azure SQL Data Warehouse를 사용하면 동시성 수준 및 리소스 할당(예: 메모리, CPU 우선 순위 지정)을 제어할 수 있습니다. 이 문서에서는 동시성 및 워크로드 관리 개념을 소개하며, 두 기능 모두 구현된 방법 및 데이터 웨어하우스에서 이들을 제어할 수 있는 방법을 설명합니다. SQL 데이터 웨어하우스 워크로드 관리는 다중 사용자 환경을 지원합니다. 다중 테넌트 워크로드용은 아닙니다.

## 동시성 한도
SQL 데이터 웨어하우스를 사용하여 최대 1,024개의 동시 연결을 할 수 있습니다. 1,024개의 모든 연결을 통해 동시에 쿼리를 제출할 수 있습니다. 그러나 SQL Data Warehouse는 처리량을 최적화하기 위해 각 쿼리가 최소한의 메모리를 부여받도록 일부 쿼리를 대기시킬 수 있습니다. 쿼리 실행 시 큐에 대기하기가 발생합니다. SQL 데이터 웨어하우스는 동시성 한도에 이른 경우 쿼리를 큐에 대기하도록 하여 활성 쿼리가 긴급하게 필요한 메모리 리소스에 액세스할 수 있게 하여 총 처리량을 늘릴 수 있습니다.

동시성 한도는 *동시 쿼리 수* 및 *동시성 슬롯 수*라는 두 개념에 의해 제어됩니다. 쿼리가 실행하려면 쿼리 동시성 한도 및 동시성 슬롯 할당 내에서 실행해야 합니다.

* 동시 쿼리 수란 동시에 실행하는 쿼리 수입니다. SQL 데이터 웨어하우스는 더 큰 DWU 크기에서 최대 32개의 동시 쿼리를 지원합니다.
* 동시성 슬롯은 DWU를 기준으로 할당됩니다. 100개의 DWU마다 4개의 동시성 슬롯을 제공합니다. 예를 들어 DW100은 4개의 동시성 슬롯을 할당하고, DW1000은 40개를 할당합니다. 각 쿼리는 쿼리의 [리소스 클래스](#resource-classes)에 따라 하나 이상의 동시성 슬롯을 사용합니다. smallrc 리소스 클래스에서 실행되는 쿼리는 하나의 동시성 슬롯을 사용합니다. 상위 리소스 클래스에서 실행되는 쿼리는 더 많은 동시성 슬롯을 사용합니다.

다음 테이블에는 다양한 DWU 크기의 동시 쿼리와 동시성 슬롯의 한도가 나와 있습니다.

### 동시성 한도
| DWU | 최대 동시 쿼리 수 | 할당된 동시성 슬롯 수 |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

이러한 임계값 중 하나가 충족되면 새 쿼리가 큐에 추가되고 선입선출 방식으로 실행됩니다. 쿼리가 완료되고 쿼리 및 슬롯의 수가 한도 밑으로 떨어지면 큐에 저장된 쿼리가 릴리스됩니다.

> [!NOTE]
> DMV(동적 관리 뷰)에서 단독으로 실행하는 *Select* 쿼리 또는 카탈로그 뷰는 어떠한 동시성 한도에 의해서도 제어되지 않습니다. 시스템에서 실행하는 쿼리 수에 관계없이 시스템을 모니터링할 수 있습니다.
> 
> 

## 리소스 클래스
리소스 클래스는 쿼리에 지정된 메모리 할당 및 CPU 사이클을 제어하는 데 도움을 줍니다. 네 가지 리소스 클래스를 *데이터베이스 역할*의 형태로 사용자에게 할당할 수 있습니다. 네 가지 리소스 클래스에는 **smallrc**, **mediumrc**, **largerc** 및 **xlargerc**가 있습니다. Smallrc의 사용자에게 더 작은 양의 메모리가 주어져 더 높은 동시성을 활용할 수 있습니다. 반면, xlargerc에 할당된 사용자는 많은 양의 메모리가 주어져 더 적은 쿼리가 동시에 실행될 수 있습니다.

기본적으로 각 사용자는 작은 리소스 클래스인 smallrc의 멤버입니다. `sp_addrolemember` 절차는 리소스 클래스를 늘리는 데 사용되고 `sp_droprolemember` 절차는 리소스 클래스를 줄이는 데 사용됩니다. 예를 들어 이 명령은 loaduser의 리소스 클래스를 largerc로 늘립니다.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

사용자의 리소스 클래스를 변경하기보다는 사용자를 리소스 클래스에 영구적으로 할당하는 것이 좋습니다. 예를 들어 클러스터형 Columnstore 테이블에 대한 로드 시에는 리소스를 많이 할당할수록 품질이 보다 뛰어난 인덱스가 작성됩니다. 로드가 더 높은 메모리에 액세스할 수 있도록 하려면 특별히 데이타 로드에 대한 사용자를 만들고 이 사용자를 더 높은 리소스 클래스에 영구적으로 할당합니다.

더 큰 메모리를 할당해도 별로 도움이 되지 않는 일부 쿼리 형식이 있습니다. 시스템은 리소스 클래스 할당을 무시하고, 대신 이러한 쿼리를 항상 작은 리소스 클래스에서 실행합니다. 이러한 쿼리가 항상 작은 리소스 클래스에서 실행되는 경우 동시성 슬롯이 압박 하에서 실행될 수 있으며 필요한 것보다 더 많은 수의 슬롯을 사용하지 않게 됩니다. 자세한 내용은 [리소스 클래스 예외](#query-exceptions-to-concurrency-limits)를 참조하세요.

리소스 클래스에 대한 추가적인 몇 가지 자세한 정보.

* 사용자의 리소스 클래스를 변경하려면 *역할 변경* 권한이 필요합니다.
* 사용자가 하나 이상의 더 높은 리소스 클래스에 추가될 수 있지만 할당된 가장 높은 리소스 클래스의 특성을 갖게 됩니다. 즉, 사용자가 mediumrc와 largerc에 할당된다면, 더 높은 리소스 클래스인 largerc가 적용될 리소스 클래스입니다.
* 시스템 관리 사용자의 리소스 클래스는 변경할 수 없습니다.

자세한 예제는 [사용자 리소스 클래스 변경 예제](#changing-user-resource-class-example)를 참조하세요.

## 메모리 할당
사용자의 리소스 클래스를 늘리는 데 따른 장단점이 있습니다. 사용자에 대한 리소스 클래스를 늘리면 쿼리가 더 많은 메모리에 액세스할 수 있게 되고 이렇게 되면 쿼리가 더 빨리 실행됩니다. 하지만 리소스 클래스가 높아질수록 동시에 실행될 수 있는 쿼리의 수도 줄어듭니다. 즉, 단일 쿼리에 많은 양의 메모리를 할당하거나 동시 실행을 위해 역시 메모리 할당이 필요한 다른 쿼리를 허용하는 방식 중에서 하나를 고려해야 합니다. 한 명의 사용자가 쿼리에 대한 메모리 할당을 높게 받는 경우 다른 사용자들은 쿼리를 실행할 목적으로 동일한 메모리에 액세스할 수 없습니다.

다음 표는 DWU 및 리소스 클래스에 의해 각 배포에 할당된 메모리를 매핑합니다.

### 배포 당 메모리 할당(MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1,600 |
| DW500 |100 |400 |800 |1,600 |
| DW600 |100 |400 |800 |1,600 |
| DW1000 |100 |800 |1,600 |3,200 |
| DW1200 |100 |800 |1,600 |3,200 |
| DW1500 |100 |800 |1,600 |3,200 |
| DW2000 |100 |1,600 |3,200 |6,400 |
| DW3000 |100 |1,600 |3,200 |6,400 |
| DW6000 |100 |3,200 |6,400 |12,800 |

이전 테이블에서, xlargerc 리소스 클래스의 DW2000에서 실행되는 쿼리는 배포된 60개의 각 데이터베이스 내에서 6,400MB의 메모리에 액세스하게 됩니다. SQL 데이터 웨어하우스에는 배포가 60개 있습니다. 따라서, 주어진 리소스 클래스 내의 쿼리에 대한 총 메모리 할당을 계산하려면, 위의 값에 60을 곱해야 합니다.

### 시스템 전체 메모리 할당(GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

시스템 전체 메모리 할당 테이블에서 보면 xlargerc 리소스 클래스의 DW2000에서 실행되는 쿼리에는 전체 SQL Data Warehouse에 대해 총 375GB(6,400MB * 60개 배포/1,024, 이 결과를 GB로 변환)의 메모리가 할당됩니다.

## 동시성 슬롯 사용량
SQL 데이터 웨어하우스는 더 많은 리소스 클래스에서 실행되는 쿼리에 더 많은 메모리를 부여합니다. 메모리는 고정된 리소스입니다. 따라서 쿼리당 할당되는 메모리가 많을수록 동시에 실행할 수 있는 쿼리의 수가 줄어듭니다. 다음 테이블에서는 앞에서 설명한 모든 개념을 단일 보기로 다시 제공합니다. 이 보기에는 DWU에서 사용 가능한 동시성 슬롯의 수와 각 리소스 클래스가 사용하는 슬롯 수가 나와 있습니다.

### 동시성 슬롯의 할당 및 사용량
| DWU | 최대 동시 쿼리 수 | 할당된 동시성 슬롯 수 | smallrc에서 사용되는 슬롯 | mediumrc에서 사용되는 슬롯 | largerc에서 사용되는 슬롯 | xlargerc에서 사용되는 슬롯 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

이 테이블에서 볼 수 있는 것처럼 DW1000으로 실행되는 SQL 데이터 웨어하우스는 최대 32개의 동시 쿼리 및 총 40개의 동시성 슬롯을 할당합니다. 모든 사용자가 smallrc에서 실행하는 경우 각 쿼리가 동시성 슬롯 1개를 소비하므로 32개의 동시성 쿼리가 허용됩니다. DW1000의 모든 사용자가 mediumrc에서 실행하는 경우, 각 쿼리에는 총 메모리 할당량 47GB에서 배포당 800MB가 할당되고 동시성은 5명의 사용자에게 제한됩니다(40개 동시성 슬롯/mediumrc 사용자당 8개 슬롯).

## 쿼리 중요도
SQL 데이터 웨어하우스는 워크로드 그룹을 사용하여 리소스 클래스를 구현합니다. 다양한 DWU 크기의 리소스 클래스 동작을 제어하는 총 8개의 워크로드 그룹이 있습니다. DWU의 경우 SQL 데이터 웨어하우스는 8개의 워크로드 그룹 중 4개만 사용합니다. 각 워크로드 그룹은 4가지 리소스 클래스 smallrc, mediumrc, largerc 또는 xlargerc 중 하나에 할당되기 때문에 이렇게 하는 것이 합리적입니다. 이들 워크로드 그룹의 일부가 더 높은 *중요도*로 설정된다는 점 때문에 워크로드 그룹을 이해하는 것이 중요합니다. 중요도가 CPU 예약에 사용됩니다. 높은 중요도를 갖고 실행되는 쿼리는 중간 중요도의 쿼리보다 3배 더 많은 CPU 사이클을 받게 될 것입니다. 따라서 동시성 슬롯 매핑은 또한 CPU 우선 순위를 결정합니다. 쿼리가 16개 이상의 슬롯을 사용할 경우 중요도 높음으로 실행됩니다.

다음 테이블에서는 각 워크로드 그룹에 대한 중요도 매핑을 보여 줍니다.

### 동시성 슬롯 및 중요도에 대한 워크로드 그룹 매핑
| 워크로드 그룹 | 동시성 슬롯 매핑 | MB/배포 | 중요도 매핑 |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |중간 |
| SloDWGroupC01 |2 |200 |중간 |
| SloDWGroupC02 |4 |400 |중간 |
| SloDWGroupC03 |8 |800 |중간 |
| SloDWGroupC04 |16 |1,600 |높음 |
| SloDWGroupC05 |32 |3,200 |높음 |
| SloDWGroupC06 |64 |6,400 |높음 |
| SloDWGroupC07 |128 |12,800 |높음 |

**동시성 슬롯의 할당 및 사용량** 차트에서는 DW500이 smallrc, mediumrc, largerc 및 xlargerc 각각에 대해 1, 4, 8 또는 16의 동시성 슬롯을 사용한다는 확인할 수 있습니다. 위의 차트에서 이러한 값을 확인하여 각 리소스 클래스에 대한 중요도를 알 수 있습니다.

### DW500의 리소스 클래스와 중요도 관계
| 리소스 클래스 | 워크로드 그룹 | 사용된 동시성 슬롯 수 | MB/배포 | 중요도 |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |중간 |
| mediumrc |SloDWGroupC02 |4 |400 |중간 |
| largerc |SloDWGroupC03 |8 |800 |중간 |
| xlargerc |SloDWGroupC04 |16 |1,600 |높음 |

다음 DMV 쿼리는 리소스 관리자의 관점에서 메모리 리소스 할당의 차이점을 자세히 살펴보거나, 또는 문제를 해결할 때 워크로드 그룹의 활성 및 사용 기록을 분석하는 데도 사용할 수 있습니다.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## 동시성 한도를 적용하는 쿼리
대부분의 쿼리는 리소스 클래스에 의해 제어됩니다. 이러한 쿼리는 동시 쿼리 및 동시성 슬롯 임계값 둘 다를 벗어나지 않아야 합니다. 사용자는 동시성 슬롯 모델에서 쿼리를 제외하도록 선택할 수 없습니다.

다시 한 번 강조하지만 다음 문은 리소스 클래스를 인식합니다.

* INSERT-SELECT
* UPDATE
* 삭제
* SELECT(사용자 테이블을 쿼리하는 경우)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* 데이터 로드
* DMS(데이터 이동 서비스)에서 수행하는 데이터 이동 작업

## 동시성 제한에 대한 쿼리 예외
일부 쿼리는 사용자가 할당된 리소스 클래스를 인식하지 않습니다. 동시성 제한에 대한 이러한 예외는 특정 명령에 필요한 메모리 리소스가 부족할 때(종종 명령이 메타데이터 작업이므로) 나타납니다. 이러한 예외는 필요하지 않은 더 큰 양의 메모리가 쿼리에 할당되는 것을 방지하기 위한 것입니다. 이러한 경우에는 사용자에게 할당된 실제 리소스 클래스와 상관없이 기본 또는 작은 리소스 클래스(smallrc)가 항상 사용됩니다. 예를 들어, `CREATE LOGIN`은 항상 smallrc에서 실행됩니다. 이 작업을 수행하는 데 필요한 리소스가 매우 부족하고 따라서 동시성 슬롯 모델에 쿼리를 포함하는 것이 적합하지 않습니다. 이러한 쿼리는 또한 32명의 사용자 동시성 제한으로 제한되지 않으며, 이러한 쿼리를 개수 제한 없이 1,024개의 세션 제한까지 실행할 수 있습니다.

다음 문은 리소스 클래스를 인식하지 않습니다.

* CREATE 또는 DROP TABLE
* ALTER TABLE ... SWITCH, SPLIT 또는 MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE, UPDATE 또는 DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE, ALTER 또는 DROP USER
* CREATE, ALTER 또는 DROP PROCEDURE
* CREATE 또는 DROP VIEW
* INSERT VALUES
* SELECT(시스템 뷰와 DMV에서)
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## 사용자 리소스 클래스 변경 예제
1. **로그인 만들기:** SQL Data Warehouse 데이터베이스를 호스트하는 SQL Server에서 **마스터** 데이터베이스에 대한 연결을 열고 다음 명령을 실행합니다.
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > Azure SQL Data Warehouse 사용자에 대해 마스터 데이터베이스에 사용자를 만드는 것이 좋습니다. 마스터에서 사용자를 만들면 데이터베이스 이름을 지정하지 않아도 사용자가 SSMS 등의 도구를 사용하여 로그인할 수 있습니다. 또한 개체 탐색기를 사용하여 SQL Server의 모든 데이터베이스를 볼 수 있습니다. 사용자를 만들고 관리하는 방법에 대한 자세한 내용은 [SQL Data Warehouse에서 데이터베이스 보호][SQL Data Warehouse에서 데이터베이스 보호]를 참조하세요.
   > 
   > 
2. **SQL Data Warehouse 사용자 만들기:** **SQL Data Warehouse** 데이터베이스에 대한 연결을 열고 다음 명령을 실행합니다.
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **권한 부여:** 다음 예제에서는 **SQL 데이터 웨어하우스** 데이터베이스에 대한 `CONTROL` 권한을 부여합니다. 데이터베이스 수준의 `CONTROL` 권한은 SQL Server의 db\_owner에 해당합니다.
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **리소스 클래스 증가:** 다음 쿼리를 사용하여 더 높은 워크로드 관리 역할에 사용자를 추가합니다.
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **리소스 클래스 감소:** 다음 쿼리를 사용하여 워크로드 관리 역할에서 사용자를 제거합니다.
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > smallrc에서 사용자를 제거하는 것은 불가능합니다.
   > 
   > 

## 큐에 대기 중인 쿼리 검색 및 다른 DMV
`sys.dm_pdw_exec_requests` DMV는 동시성 큐에 대기 중인 쿼리를 식별하는 데 사용할 수 있습니다. 동시성 슬롯을 대기하는 쿼리는 **일시 중단** 상태가 됩니다.

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

워크로드 관리 역할은 `sys.database_principals`를 사용하여 확인할 수 있습니다.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

다음 쿼리는 각 사용자에게 어떤 역할이 할당되었는지 보여줍니다.

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL 데이터 웨어하우스에 다음과 같은 대기 형식이 있습니다.

* **LocalQueriesConcurrencyResourceType**: 동시성 슬롯 프레임워크 외부에 존재하는 쿼리. `SELECT @@VERSION`과 같은 DMV 쿼리 및 시스템 함수는 로컬 쿼리의 예입니다.
* **UserConcurrencyResourceType**: 동시성 슬롯 프레임워크 내에 존재하는 쿼리. 최종 사용자 테이블에 대한 쿼리는 이 리소스 형식을 사용하는 예를 나타냅니다.
* **DmsConcurrencyResourceType**: 데이터 이동 작업으로 초래된 대기
* **BackupConcurrencyResourceType**: 이 대기는 데이터베이스가 백업 중임을 나타냅니다. 이 리소스 유형에 대한 최대값은 1입니다. 여러 백업을 동시에 요청한 경우 다른 백업 요청은 큐에 저장됩니다.

`sys.dm_pdw_waits` DMV는 요청이 대기 중인 리소스를 알아내는 데 사용할 수 있습니다.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

`sys.dm_pdw_resource_waits` DMV는 지정된 쿼리에 사용되는 리소스 대기만 표시합니다. 리소스 대기 시간은 기본 SQL Server가 CPU에 대해 쿼리를 예약하는 데 소요되는 대기 시간을 나타내는 것과 달리 리소스가 제공될 때까지 대기하는 시간만 측정합니다.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

`sys.dm_pdw_wait_stats` DMV는 기록 추세 분석에 사용할 수 있습니다.

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## 다음 단계
데이터베이스 사용자 및 보안을 관리하는 방법에 대한 자세한 내용은 [SQL 데이터 웨어하우스에서 데이터베이스 보호][SQL 데이터 웨어하우스에서 데이터베이스 보호]를 참조하세요. 더 큰 리소스 클래스가 클러스터된 columnstore 인덱스 품질을 향상할 방법에 대한 자세한 내용은 [인덱스를 다시 빌드하여 세그먼트 품질 개선]을 참조하세요.

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[인덱스를 다시 빌드하여 세그먼트 품질 개선]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[SQL Data Warehouse에서 데이터베이스 보호]: ./sql-data-warehouse-overview-manage-security.md
[SQL 데이터 웨어하우스에서 데이터베이스 보호]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0928_2016-->