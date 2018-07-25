---
title: Azure SQL Data Warehouse 릴리스 정보 2018년 5월 | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 릴리스 정보입니다.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: ddcb985c370baf224d451aa6c1ec9b796e0140de
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38561407"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Azure SQL Data Warehouse의 새로운 기능 2018년 5월 
Azure SQL Data Warehouse는 지속적으로 개선 사항을 수신합니다. 이 문서에서는 2018년 5월에 도입된 새로운 기능과 변경 사항에 대해 설명합니다. 

## <a name="gen-2-instances"></a>Gen2 인스턴스
![alt](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) Azure SQL Data Warehouse Compute에 최적화된 Gen2 계층은 클라우드 데이터 웨어하우징에 대한 새로운 성능 표준을 설정합니다. 고객은 현재 세대에 비해 최대 쿼리 성능이 5배, 동시성이 4배, 컴퓨팅 능력이 5배 향상된 성능을 사용할 수 있습니다. 단일 클러스터에서 128개의 동시 쿼리를 제공할 수 있으며 이는 클라우드 데이터 웨어하우스 서비스 중 최고의 성능입니다.

Azure Data의 부사장인 Rohan Kumar의 [Turbocharge cloud analytics with Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/)(Azure SQL Data Warehouse를 사용하여 Turbocharge 클라우드 분석) 블로그 발표를 참조하세요.

## <a name="auto-statistics"></a>자동 통계
통계는 SQL Data Warehouse의 엔진과 같이 알맞은 비용 기반 최적화 프로그램에서 쿼리 계획 생성을 최적화하는 데 중요합니다. 모든 쿼리가 미리 알려지면 생성해야 하는 통계 개체를 결정할 수 있습니다. 그러나 시스템이 데이터 웨어하우징 워크로드에 일반적으로 발생하는 임시 및 임의 쿼리를 처리하는 경우 시스템 관리자는 생성해야 하는 통계를 예측하지 못하여 쿼리 실행 계획이 최적화되지 못하고 쿼리 응답 시간이 길어질 수 있습니다. 이 문제를 완화하는 한 가지 방법은 모든 테이블 열에서 통계 개체를 미리 만드는 것입니다. 그러나 이 프로세스는 테이블 로드 프로세스 중 통계 개체를 계속 유지해야 하므로 로드 시간이 길어지는 단점이 있습니다.

이제 SQL Data Warehouse는 시스템 관리자 및 개발자의 유연성, 생산성 및 사용 편의성을 향상시키면서 시스템이 양질의 실행 계획 및 최상의 응답 시간을 지속적으로 제공할 수 있도록 통계 개체의 자동 만들기를 지원합니다.

SQL Data Warehouse에서 자동 통계 만들기를 사용하거나 사용하지 않도록 설정하려면 다음 문을 실행합니다.
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

모범 사례 및 지침으로 `AUTO_CREATE_STATISTICS` 옵션을 `ON`으로 설정하는 것이 좋습니다.

> [!NOTE]
> 자동 통계 만들기는 모든 새 데이터 웨어하우스에 대해 *기본적으로 사용 가능*하도록 설정됩니다.
>  

자세한 내용은 [ALTER DATABASE SET 옵션](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) 문서를 참조하세요.

## <a name="rejected-row-support"></a>거부된 행 지원
데이터 로딩의 병렬 특성 및 성능 향상을 위해 [PolyBase(외부 테이블)를 사용하여 데이터](design-elt-data-loading.md)를 SQL Data Warehouse에 로드하는 경우가 많습니다. PolyBase는 [Azure Data Factory](http://azure.com/adf)를 통해 데이터를 로드할 때의 기본 로딩 모델입니다. 

SQL Data Warehouse는 [CREATE EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) 문과 함께 `REJECTED_ROW_LOCATION` 매개 변수를 통해 거부된 행 위치를 정의하는 기능을 추가합니다. 외부 테이블에서 [CTAS(CREATE TABLE AS SELECT)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)를 실행한 후에는 로드할 수 없는 행이 추후 조사를 위해 소스 근처의 파일에 저장됩니다. 

거부된 행 동작에 대한 자세한 내용은 [Load confidently with SQL Data Warehouse PolyBase Rejected Row Location](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/)(SQL Data Warehouse PolyBase로 거부된 행 위치를 사용하여 로드) 블로그를 참조하세요.

다음 예제에서는 거부된 행을 지정하기 위한 새로운 구문을 보여 줍니다.

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

## <a name="alter-view"></a>ALTER VIEW
[ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql)를 사용하면 뷰를 삭제/작성한 다음 권한을 다시 적용하지 않고도 이전에 작성한 뷰를 수정할 수 있습니다. 

다음 예제에서는 이전에 만든 뷰를 수정합니다.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
[CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) 함수는 두 개 이상의 값을 종단 간 방식으로 연결하여 생성된 결과 문자열을 반환합니다. 연결된 값은 첫 번째 인수에 지정된 구분 기호로 구분됩니다. `CONCAT_WS` 함수는 CSV(쉼표로 구분된 값) 출력을 생성하는 데 유용합니다.

다음 예제는 정수 값 집합을 쉼표로 연결하는 방법을 보여 줍니다.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
이 문은 다음과 같은 결과를 반환합니다.
```
result
---------
1,2,3
```
다음 예제는 혼합 데이터 형식 값 집합을 쉼표로 연결하는 방법을 보여 줍니다.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
이 문은 다음과 같은 결과를 반환합니다.
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
[sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) 시스템 저장 프로시저는 현재 환경에서 지원하는 데이터 형식에 대한 정보를 반환합니다. 이 방법은 ODBC 연결을 통해 연결하여 데이터 형식을 조사하는 도구에서 일반적으로 사용됩니다.

다음 예제는 SQL Data Warehouse가 지원하는 모든 데이터 형식의 세부 정보를 검색합니다.

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>ORDER BY가 있는 SELECT INTO의 동작 변경 내용
이제 SQL Data Warehouse는 `ORDER BY` 절을 포함하는 `SELECT INTO` 쿼리를 차단합니다. 이전에 이 작업을 수행할 때는 먼저 메모리의 데이터를 정렬한 다음 대상 테이블에 삽입하여 테이블 모양과 일치하도록 데이터를 다시 정렬해야 했습니다.

### <a name="previous-behavior"></a>이전 동작
다음 문을 사용할 때 오버헤드를 추가 처리해야 했습니다.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>현재 동작
다음 문은 `ORDER BY` 절이 `SELECT INTO` 문에서 지원되지 않음을 나타내는 오류를 발생시킵니다.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
다음 오류 문이 반환됩니다.
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>SET PARSEONLY 쿼리 상태(동작 변경 내용)
`SET PARSEONLY ON` 구문을 사용하면 SQL Data Warehouse 엔진이 각 T-SQL 문의 구문을 검사하고, 구문을 컴파일하거나 실행하지 않고 오류 메시지를 반환할 수 있습니다. 이전에는 [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) 시스템 뷰에서 이러한 문의 상태가 `Running` 상태로 유지되었습니다. 이제 `sys.dm_pdw_exec_requests` 뷰가 상태를 `Complete`로 반환합니다.