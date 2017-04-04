---
title: "Azure SQL Data Warehouse의 T-SQL 뷰 사용 | Microsoft Docs"
description: "솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 Transact-SQL 뷰 사용을 위한 팁"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: b5208f32-8f4a-4056-8788-2adbb253d9fd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 43ab6a2f71ab51c50847b1ba5249f51c48e03fea
ms.openlocfilehash: 902929fba9e2486d97323ab52505ba6db3fe0aab
ms.lasthandoff: 01/24/2017


---
# <a name="views-in-sql-data-warehouse"></a>SQL 데이터 웨어하우스의 뷰
뷰는 SQL 데이터 웨어하우스에서 특히 유용합니다. 여러가지 다양한 방법을 사용하여 솔루션의 품질을 개선할 수 있습니다.  이 문서에서는 고려해야 할 제한 사항 뿐만 아니라 뷰를 통해 솔루션을 보완하는 방법의 예도 중점적으로 설명되어 있습니다.

> [!NOTE]
> `CREATE VIEW`에 대한 구문은 이 문서에서 다루지 않습니다. 이 참조 정보에 대해서는 MSDN의 [CREATE VIEW][CREATE VIEW] 문서를 참조하세요.
> 
> 

## <a name="architectural-abstraction"></a>아키텍처 추상화
매우 일반적인 응용 프로그램 패턴은 CREATE TABLE AS SELECT (CTAS) 뒤에 데이터 로드 중 개체 이름 바꾸기 패턴을 사용하여 테이블을 다시 작성하는 것입니다.

다음 예제에서는 새 날짜 레코드를 날짜 차원에 추가합니다. 먼저 새 테이블 DimDate_New를 만든 다음 이름을 바꾸어 원래 버전의 테이블을 바꾸는 방법을 확인합니다.

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
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

그러나 이 방법을 사용하면 뷰에서 테이블이 표시되었다가 사라질 수 있으며 "테이블이 없습니다." 오류 메시지가 표시될 수 있습니다. 뷰는 원본 개체의 이름을 바꾸는 동안 일관성 있는 프레젠테이션 계층을 제공하는 데 사용할 수 있습니다. 뷰를 통해 데이터에 액세스할 수 있도록 하기 때문에 사용자에게 기본 테이블이 표시되는지 여부는 중요하지 않습니다. 데이터 웨어하우스 설계자가 데이터 모델을 발전시키고 데이터 로드 프로세스 중 CTAS를 사용하여 성능을 극대화할 수 있는지 확인하는 동안 일관된 사용자 환경을 제공합니다.    

## <a name="performance-optimization"></a>성능 최적화
뷰는 테이블 간에 성능 최적화된 조인을 적용하는 데도 활용할 수 있습니다. 예를 들어, 뷰는 데이터 이동을 최소화하는 조인 조건의 일부로 배포 중복 키를 통합할 수 있습니다.  뷰의 또 다른 장점은 특정 쿼리 또는 조인 힌트를 강제 적용할 수 있다는 것입니다. 이 방식으로 뷰를 사용하면 조인이 항상 최적의 방식으로 수행되므로 사용자가 자신의 조인에 대한 올바른 구문을 기억해야 할 필요가 없습니다.

## <a name="limitations"></a>제한 사항
SQL 데이터 웨어하우스의 뷰는 메타데이터 전용입니다.  따라서 다음 옵션을 사용할 수 없습니다.

* 스키마 바인딩 옵션이 없습니다.
* 뷰를 통해 기본 테이블을 업데이트할 수 없습니다.
* 임시 테이블에 대해 뷰를 만들 수 없습니다.
* EXPAND / NOEXPAND 힌트에 대한 지원이 없습니다.
* SQL 데이터 웨어하우스에 인덱싱된 뷰가 없습니다.

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [SQL Data Warehouse 개발 개요][SQL Data Warehouse development overview]를 참조하세요.
`CREATE VIEW` 구문에 대해서는 [CREATE VIEW][CREATE VIEW]를 참조하세요.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREATE VIEW]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->

