<properties
   pageTitle="SQL 데이터 웨어하우스에 SQL 코드 마이그레이션| Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스로 SQL 코드를 마이그레이션하기 위한 팁"
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

# SQL 데이터 웨어하우스에 SQL 코드 마이그레이션

코드가 SQL 데이터 웨어하우스와 호환하기 위해 코드 기본 사항을 변경해야 할 수 있습니다. 일부 SQL 데이터 웨어하우스 기능은 원래 분산 방식으로 직접 작동하도록 설계되었기 때문에 크게 성능을 향상시킬 수도 있습니다. 그러나 성능 및 확장을 유지하려면 일부 기능은 사용할 수 없습니다.

## Transact-SQL 코드 변경 내용

다음 목록에서는 Azure SQL 데이터 웨어하우스에서 지원하지 않는 주요 기능을 간략하게 설명합니다.

- 업데이트 시 ANSI 조인
- 삭제 시 ANSI 조인
- 병합 문
- 데이터베이스 간 조인
- [pivot 및 unpivot 문][]
- [커서][]
- [SELECT..INTO][]
- INSERT..EXEC
- output 절
- 인라인 사용자 정의 함수
- 다중 문 함수
- 재귀 공통 테이블 식(CTE)
- CTE를 통한 업데이트
- CLR 함수 및 프로시저
- $partition 함수
- 테이블 변수
- 테이블 값 매개 변수
- 분산된 트랜잭션
- 커밋 / 롤백 작업
- 트랜잭션 저장
- 실행 컨텍스트 (EXECUTE AS)
- [롤업 / 큐브 / 그룹화 집합 옵션을 사용하는 GROUP BY 절][]
- [8를 초과한 중첩 수준][]
- [뷰를 통한 업데이트][]
- [변수 할당을 위한 select 사용][]
- [동적 SQL 문자열에 대한 최대 데이터 형식][]

다행히 대부분의 이러한 제한을 해결할 수 있습니다. 위에서 언급한 개발 관련 문서에 대한 설명이 포함되어 있습니다.

지원하지 않는 일부 시스템 함수도 있습니다. 일반적으로 데이터 웨어하우징에서 사용될 수 있는 일부 기본 함수는 다음과 같습니다.

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT\_BIG
- ERROR\_LINE()

다시 이러한 많은 문제를 해결할 수 있습니다.

예를 들어, 아래의 코드는 @@ROWCOUNT 정보를 검색하는 대체 솔루션입니다.

```
SELECT  SUM(row_count) AS row_count 
FROM    sys.dm_pdw_sql_requests 
WHERE   row_count <> -1 
AND     request_id IN 
                    (   SELECT TOP 1    request_id 
                        FROM            sys.dm_pdw_exec_requests 
                        WHERE           session_id = SESSION_ID() 
                        ORDER BY end_time DESC
                    )
;
``` 

## 다음 단계
코드 개발에 대한 조언은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[pivot 및 unpivot 문]: sql-data-warehouse-develop-pivot-unpivot.md
[커서]: sql-data-warehouse-develop-loops.md
[SELECT..INTO]: sql-data-warehouse-develop-ctas.md
[롤업 / 큐브 / 그룹화 집합 옵션을 사용하는 GROUP BY 절]: sql-data-warehouse-develop-group-by-options.md
[8를 초과한 중첩 수준]: sql-data-warehouse-develop-transactions.md
[뷰를 통한 업데이트]: sql-data-warehouse-develop-views.md
[변수 할당을 위한 select 사용]: sql-data-warehouse-develop-variable-assignment.md
[동적 SQL 문자열에 대한 최대 데이터 형식]: sql-data-warehouse-develop-dynamic-sql.md
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=August15_HO6-->