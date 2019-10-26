---
title: Azure SQL Data Warehouse의 T-SQL 뷰 사용 | Microsoft Docs
description: 솔루션 개발을 위한 Azure SQL Data Warehouse의 T-SQL 뷰 사용을 위한 팁입니다.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 1fd406243f0f2f5339c4170c4ec17286fcf2ef6d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901720"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse의 뷰
뷰를 여러 가지 다양한 방법으로 사용하여 솔루션의 품질을 개선할 수 있습니다. 

Azure SQL Data Warehouse은 표준 및 구체화 된 뷰를 지원 합니다. 둘 다 SELECT 식을 사용 하 여 생성 되 고 쿼리를 논리 테이블로 표시 하는 가상 테이블입니다. 뷰는 일반적인 데이터 계산의 복잡성을 캡슐화 하 고 계산 변경에 추상화 계층을 추가 하므로 쿼리를 다시 작성할 필요가 없습니다.

## <a name="standard-view"></a>표준 뷰
표준 뷰는 뷰가 사용 될 때마다 데이터를 계산 합니다. 디스크에 저장 된 데이터가 없습니다. 일반적으로 사용자는 데이터베이스에서 논리 개체와 쿼리를 구성 하는 데 도움이 되는 도구로 표준 뷰를 사용 합니다. 표준 뷰를 사용 하려면 쿼리에서 해당 뷰를 직접 참조 해야 합니다. 자세한 내용은 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql)를 참조하세요.

SQL Data Warehouse의 뷰는 메타데이터 전용으로 저장됩니다. 따라서 다음 옵션을 사용할 수 없습니다.
* 스키마 바인딩 옵션이 없습니다.
* 뷰를 통해 기본 테이블을 업데이트할 수 없습니다.
* 임시 테이블에 대해 뷰를 만들 수 없습니다.
* EXPAND / NOEXPAND 힌트에 대한 지원이 없습니다.
* SQL Data Warehouse에 인덱싱된 뷰가 없습니다.

표준 뷰를 활용 하 여 테이블 간에 성능 최적화 조인을 적용할 수 있습니다. 예를 들어, 뷰는 데이터 이동을 최소화하는 조인 조건의 일부로 배포 중복 키를 통합할 수 있습니다. 뷰의 또 다른 장점은 특정 쿼리 또는 조인 힌트를 강제 적용할 수 있다는 것입니다. 이 방식으로 뷰를 사용하면 조인이 항상 최적의 방식으로 수행되므로 사용자가 자신의 조인에 대한 올바른 구문을 기억해야 할 필요가 없습니다.

## <a name="materialized-view"></a>구체화된 뷰
구체화 된 뷰는 테이블 처럼 Azure SQL Data Warehouse 데이터를 미리 계산 하 고 저장 하 고 유지 관리 합니다. 구체화 된 뷰가 사용 될 때마다 다시 계산 기능 필요 하지 않습니다. 데이터가 기본 테이블로 로드 될 때 Azure SQL Data Warehouse는 구체화 된 뷰를 동기적으로 새로 고칩니다.  쿼리에서 뷰가 참조 되지 않은 경우에도 쿼리 최적화 프로그램은 자동으로 배포 된 구체화 된 뷰를 사용 하 여 쿼리 성능을 향상 시킵니다.  구체화 된 뷰에서 가장 기능과 쿼리는 작은 결과 집합을 생성 하는 큰 테이블에 대 한 복잡 한 쿼리 (일반적으로 조인 및 집계가 포함 된 쿼리)입니다.  

구체화 된 뷰 구문 및 기타 요구 사항에 대 한 자세한 내용은 [구체화 된 뷰를 SELECT로 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)를 참조 하세요.  

쿼리 튜닝 지침은 [구체화 된 뷰로 성능 조정](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)을 참조 하세요. 

## <a name="example"></a>예제
일반적인 애플리케이션 패턴은 CREATE TABLE AS SELECT (CTAS) 뒤에 데이터 로드 중 개체 이름 바꾸기 패턴을 사용하여 테이블을 다시 작성하는 것입니다.  다음 예제에서는 새 날짜 레코드를 날짜 차원에 추가합니다. 먼저 새 테이블 DimDate_New를 만든 다음, 이름을 바꾸어 원래 버전의 테이블을 바꾸는 방법을 확인합니다.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```
그러나 이 방법을 사용하면 뷰에서 테이블이 표시되었다가 사라질 수 있으며 "테이블이 없습니다." 오류 메시지가 표시될 수 있습니다. 뷰는 원본 개체의 이름을 바꾸는 동안 일관성 있는 프레젠테이션 계층을 제공하는 데 사용할 수 있습니다. 뷰를 통해 데이터에 대한 액세스를 제공하면 사용자에게 기본 테이블에 대한 가시성이 필요하지 않습니다. 이 계층은 일관된 사용 환경을 제공하는 동시에 데이터 웨어하우스 디자이너가 데이터 모델을 발전시킬 수 있도록 보장합니다. 기본 테이블을 발전시킬 수 있으면 설계자가 CTAS를 사용하여 데이터 로드 프로세스 중에 성능을 최대화 할 수 있습니다.   

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [SQL Data Warehouse 개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.


