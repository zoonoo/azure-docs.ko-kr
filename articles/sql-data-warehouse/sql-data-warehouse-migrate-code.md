<properties
   pageTitle="SQL 데이터 웨어하우스에 SQL 코드 마이그레이션| Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스로 SQL 코드를 마이그레이션하기 위한 팁"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama;jrj"/>

# SQL 데이터 웨어하우스에 SQL 코드 마이그레이션

다른 데이터베이스에서 SQL 데이터 웨어하우스로 코드를 마이그레이션하는 경우 코드 기본 사항을 변경해야 할 수 있습니다. 일부 SQL 데이터 웨어하우스 기능은 원래 분산 방식으로 작동하도록 디자인되었기 때문에 크게 성능을 향상시킬 수 있습니다. 그러나 성능 및 확장을 유지하려면 일부 기능은 사용할 수 없습니다.

## 일반적인 T-SQL 제한 사항

다음 목록에서는 Azure SQL 데이터 웨어하우스에서 지원하지 않는 가장 일반적인 기능을 간략하게 설명합니다. 링크를 따라 이동하면 지원되지 않는 기능에 대한 대안을 확인할 수 있습니다.

- [업데이트 시 ANSI 조인][]
- [삭제 시 ANSI 조인][]
- [병합 문][]
- 데이터베이스 간 조인
- [커서][]
- [SELECT..INTO][]
- [INSERT..EXEC][]
- output 절
- 인라인 사용자 정의 함수
- 다중 문 함수
- [공통 테이블 식](#Common-table-expressions)
- [재귀 공통 테이블 식(CTE)](#Recursive-common-table-expressions-(CTE)
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

다행히 대부분의 이러한 제한을 해결할 수 있습니다. 위에서 언급한 개발 관련 문서에 대한 설명이 제공됩니다.

## 지원되는 CTE 기능

CTE(공용 테이블 식)는 SQL 데이터 웨어하우스에서 부분적으로 지원됩니다. 현재 지원되는 CTE 기능은 다음과 같습니다.

- SELECT 문에서 CTE를 지정할 수 있습니다.
- CREATE VIEW 문에서 CTE를 지정할 수 있습니다.
- (CTAS)CREATE TABLE AS SELECT 문에서 CTE를 지정할 수 있습니다.
- (CRTAS)CREATE REMOTE TABLE AS SELECT 문에서 CTE를 지정할 수 있습니다.
- (CETAS)CREATE EXTERNAL TABLE AS SELECT 문에서 CTE를 지정할 수 있습니다.
- CTE로부터 원격 테이블을 참조할 수 있습니다.
- CTE로부터 외부 테이블을 참조할 수 있습니다.
- CTE에 여러 개의 CTE 쿼리 정의를 정의할 수 있습니다.

## CTE 제한 사항

공용 테이블 식은 SQL 데이터 웨어하우스에서 다음을 포함하여 몇 가지 제한 사항이 있습니다.

- CTE는 단일 SELECT 문 뒤에 와야 합니다. INSERT, UPDATE, DELETE 및 MERGE 문은 지원되지 않습니다.
- 자체에 대한 참조를 포함하는 공통 테이블 식(재귀 공통 테이블 식)은 지원되지 않습니다(아래 섹션 참조).
- CTE에 둘 이상의 WITH 절을 지정할 수 없습니다. 예를 들어 CTE\_query\_definition에 하위 쿼리가 포함된 경우 해당 하위 쿼리에는 다른 CTE를 정의하는 중첩된 WITH 절을 포함할 수 없습니다.
- TOP 절이 지정된 경우를 제외하고 ORDER BY 절은 CTE\_query\_definition에서 사용할 수 없습니다.
- 일괄 처리의 일부인 문에서 CTE가 사용되는 경우 그 전의 문 뒤에 세미콜론이 와야 합니다.
- Sp\_prepare에 의해 준비된 문에서 사용할 경우 CTE는 PDW에서 다른 SELECT 문과 동일한 방식으로 작동합니다. 그러나 sp\_prepare를 통해 준비된 CETAS의 일부로 CTE를 사용할 경우 sp\_prepare에 대해 구현되는 바인딩 방식이 다르기 때문에 동작이 SQL Server 및 다른 PDW와 다를 수 있습니다. CTE를 참조하는 SELECT 문이 CTE에 없는 잘못된 열을 사용할 경우 오류를 감지하지 않고 sp\_prepare를 통과합니다. 대신 sp\_execute 동안 오류가 throw됩니다.

## 재귀 CTE

재귀 CTE는 SQL 데이터 웨어하우스에서 지원되지 않습니다. 재귀 CTE의 마이그레이션은 완료될 수는 있으나 가장 좋은 프로세스는 여러 단계로 분할하는 것입니다. 재귀 중간 쿼리를 반복할 때 일반적으로 루프를 사용하여 임시 테이블을 채울 수 있습니다. 임시 테이블을 채우고 나면 데이터를 단일 결과 집합으로 반환할 수 있습니다. [롤업/큐브/그룹화 집합 옵션을 사용하여 절에 따라 그룹화][] 문서에서 `GROUP BY WITH CUBE` 해결에 사용한 것과 비슷한 방법입니다.

## 시스템 함수

지원하지 않는 일부 시스템 함수도 있습니다. 일반적으로 데이터 웨어하우징에서 사용될 수 있는 일부 기본 함수는 다음과 같습니다.

- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT\_BIG
- ERROR\_LINE()

다시 이러한 많은 문제를 해결할 수 있습니다.

예를 들어, 아래의 코드는 @@ROWCOUNT 정보를 검색하는 대체 솔루션입니다.

```sql
SELECT  SUM(row_count) AS row_count
FROM    sys.dm_pdw_sql_requests
WHERE   row_count <> -1
AND     request_id IN
                    (   SELECT TOP 1    request_id
                        FROM            sys.dm_pdw_exec_requests
                        WHERE           session_id = SESSION_ID()
                        AND             resource_class IS NOT NULL
                        ORDER BY end_time DESC
                    )
;
```

## 다음 단계
지원되는 모든 T-SQL 문의 전체 목록은 [Transact-SQL 항목][]을 참조하세요.

<!--Image references-->

<!--Article references-->
[업데이트 시 ANSI 조인]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[삭제 시 ANSI 조인]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[병합 문]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL 항목]: ./sql-data-warehouse-reference-tsql-statements.md

[커서]: ./sql-data-warehouse-develop-loops.md
[SELECT..INTO]: ./sql-data-warehouse-develop-ctas.md#selectinto
[롤업 / 큐브 / 그룹화 집합 옵션을 사용하는 GROUP BY 절]: ./sql-data-warehouse-develop-group-by-options.md
[롤업/큐브/그룹화 집합 옵션을 사용하여 절에 따라 그룹화]: ./sql-data-warehouse-develop-group-by-options.md
[8를 초과한 중첩 수준]: ./sql-data-warehouse-develop-transactions.md
[뷰를 통한 업데이트]: ./sql-data-warehouse-develop-views.md
[변수 할당을 위한 select 사용]: ./sql-data-warehouse-develop-variable-assignment.md
[동적 SQL 문자열에 대한 최대 데이터 형식]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0706_2016-->