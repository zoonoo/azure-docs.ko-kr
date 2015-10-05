<properties
   pageTitle="SQL 데이터 웨어하우스의 뷰 | Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 Transact-SQL 뷰 사용을 위한 팁"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

 
# SQL 데이터 웨어하우스의 뷰

뷰는 SQL 데이터 웨어하우스에서 특히 유용합니다. 여러가지 다양한 방법을 사용하여 솔루션의 품질을 개선할 수 있습니다.

이 문서에는 뷰를 구현하여 솔루션을 보완하는 방법을 보여주는 몇 가지 예를 강조 표시합니다. 다음 제한 사항도 고려해야 합니다.

## 아키텍처 추상화
매우 일반적인 응용 프로그램 패턴은 CREATE TABLE AS SELECT (CTAS) 뒤에 데이터 로드 중 개체 이름 바꾸기 패턴을 사용하여 테이블을 다시 작성하는 것입니다.

다음 예제에서는 새 날짜 레코드를 날짜 차원에 추가합니다. 먼저 새 개체 DimDate\_New를 만든 다음 이름을 바꾸어 원래 버전의 개체를 바꾸는 방법을 확인합니다.

```
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = REPLICATE
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

그러나 테이블 개체가 SSDT SQL Server 개체 탐색기의 사용자 뷰에 표시되거나 표시되지 않을 수 있습니다. 뷰는 원본 개체의 이름을 바꾸는 동안 일관성있는 프레젠테이션 계층을 웨어하우스 데이터 소비자에게 제공하는 데 사용할 수 있습니다. 뷰를 통한 데이터에 대한 액세스 제공은 사용자에게 기본 테이블의 표시 유형이 없어도 됨을 의미합니다. 데이터 웨어하우스 설계자가 데이터 모델을 발전시키고 데이터 로드 프로세스 중 CTAS를 사용하여 성능을 극대화할 수 있는지 확인하는 동안 일관된 사용자 환경을 제공합니다.

## 성능 최적화
뷰는 테이블 간에 성능 최적화된 조인을 적용하는 스마트한 방법입니다. 예를 들어, 뷰는 데이터 이동을 최소화하는 조인 조건의 일부로 배포 중복 키를 통합할 수 있습니다. 또 다른 이유는 특정 쿼리 또는 조인 힌트를 강제 적용할 수 있다는 점입니다. 이렇게 하면 조인은 항상 최적의 방식으로 수행되고 올바르게 조인을 생성하는 데 사용자의 기억에 의존하지 않습니다.

## 제한 사항
SQL 데이터 웨어하우스의 뷰는 메타데이터 전용입니다.

따라서 다음 옵션을 사용할 수 없습니다. 스키마 바인딩 옵션이 없습니다. 뷰를 통해 기본 테이블을 업데이트할 수 없습니다. 임시 테이블에 대해 뷰를 만들 수 없습니다. EXPAND / NOEXPAND 힌트에 대한 지원은 없습니다. SQL 데이터 웨어하우스의 인덱싱된 뷰가 없습니다.


## 다음 단계
더 많은 개발 팁은 [SQL 데이터 웨어하우스 개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[SQL 데이터 웨어하우스 개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Sept15_HO4-->