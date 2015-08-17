<properties
   pageTitle="SQL 데이터 웨어하우스용 솔루션 개발 | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스를 사용하여 솔루션을 구축하기 위한 개발 기술 및 모범 사례"
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
   ms.date="06/25/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL 데이터 웨어하우스용 솔루션 개발

SQL 데이터 웨어하우스에 대한 개발 원칙, 모범 사례 및 코딩 기술의 이해에 도움이 필요하다면 개발 문서를 살펴보십시오.

## 주요 개념
다음 문서는 SQL 데이터 웨어하우스를 사용하여 분산된 데이터 웨어하우스를 개발하기 위해 이해해야 하는 일부 주요 개념과 설계 결정을 요약합니다.

- [연결][]
- [동시성][]
- [트랜잭션][]
- [사용자 정의 스키마][]
- [테이블 디자인][]
- [해시 배포 키][]
- [테이블 파티션][]
- [CTAS][]
- [통계][]

## 개발 기술
이들 문서는 SQL 데이터 웨어하우스를 사용하여 솔루션을 개발하기 위한 구체적인 코딩 기술, 팁 및 모범 사례를 요약합니다.

- [저장 프로시저][]
- [레이블][]
- [뷰][]
- [임시 테이블][]
- [동적 SQL][]
- [반복][]
- [개체 이름 바꾸기][]
- [데이터 피벗][]
- [옵션으로 그룹화][]
- [변수 할당][]

## 다음 단계
개발 문서들을 살펴본 후에는 [Transact-SQL 참조][] 페이지에서 SQL 데이터 웨어하우스에 대해 지원되는 구문에 대한 자세한 내용을 보십시오.

<!--Image references-->

<!--Article references-->
[동시성]: sql-data-warehouse-develop-concurrency.md
[연결]: sql-data-warehouse-develop-connections.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[동적 SQL]: sql-data-warehouse-develop-dynamic-sql.md
[옵션으로 그룹화]: sql-data-warehouse-develop-group-by-options.md
[해시 배포 키]: sql-data-warehouse-develop-hash-distribution-key.md
[레이블]: sql-data-warehouse-develop-label.md
[반복]: sql-data-warehouse-develop-loops.md
[데이터 피벗]: sql-data-warehouse-develop-pivot-unpivot.md
[개체 이름 바꾸기]: sql-data-warehouse-develop-rename.md
[통계]: sql-data-warehouse-develop-statistics.md
[저장 프로시저]: sql-data-warehouse-develop-stored-procedures.md
[테이블 디자인]: sql-data-warehouse-develop-table-design.md
[테이블 파티션]: sql-data-warehouse-develop-table-partitions.md
[임시 테이블]: sql-data-warehouse-develop-temporary-tables.md
[트랜잭션]: sql-data-warehouse-develop-transactions.md
[사용자 정의 스키마]: sql-data-warehouse-develop-user-defined-schemas.md
[변수 할당]: sql-data-warehouse-develop-variable-assignment.md
[뷰]: sql-data-warehouse-develop-views.md

[Transact-SQL 참조]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=August15_HO6-->