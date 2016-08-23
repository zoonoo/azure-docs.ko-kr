<properties
   pageTitle="SQL 데이터 웨어하우스로 스키마 마이그레이션| Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스로 스키마를 마이그레이션하기 위한 팁"
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
   ms.date="08/08/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL 데이터 웨어하우스로 스키마 마이그레이션#

다음 요약은 SQL Server와 SQL 데이터 웨어하우스 간의 차이점을 이해하여 데이터베이스를 마이그레이션하는 데 도움이 됩니다.

### 테이블 기능
SQL 데이터 웨어하우스는 이 기능을 사용하지 않거나 지원하지 않습니다.

- 기본 키
- 외래 키
- Check 제약 조건
- 고유 제약 조건
- 고유 인덱스
- 계산된 열
- 스파스 열
- 사용자 정의 유형
- 인덱싱된 뷰
- ID
- 시퀀스
- 트리거
- 동의어


### 데이터 형식 차이
SQL 데이터 웨어하우스는 일반적인 비즈니스 데이터 형식을 지원합니다.

- bigint
- binary
- bit
- char
- date
- datetime
- datetime2
- datetimeoffset
- decimal
- float
- int
- money
- nchar
- nvarchar
- numeric
- real
- smalldatetime
- smallint
- smallmoney
- sysname
- 실시간
- tinyint
- varbinary
- varchar
- uniqueidentifier

이 쿼리를 사용하여 호환되지 않는 형식을 포함하는 데이터 웨어하우스에서 열을 식별할 수 있습니다.

```sql
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'sql_variant'
                ,   'text'
                ,   'timestamp'
                ,   'xml'
                )
AND  y.[is_user_defined] = 1
;

```

쿼리는 지원하지 않는 모든 사용자 정의 데이터 형식을 포함합니다.

데이터베이스에 지원되지 않는 형식이 있는 경우, 걱정하지 마세요. 대신 사용할 수 있는 대체 방법이 다음에 제시됩니다.

다음 위치 대신

- **geometry**, varbinary 형식 사용
- **geography**, varbinary 형식 사용
- **hierarchyid**, 이 CLR 유형이 지원되지 않음
- **image**, **text**, **ntext**, varchar/nvarchar 사용(작을 수록 더 좋음)
- **sql\_variant**, 열을 강력한 형식의 열로 분할
- **table**, 임시 테이블로 변환
- **timestamp**, datetime2 및 `CURRENT_TIMESTAMP` 함수를 사용하도록 코드 재작업. current\_timestamp를 기본 제약 조건으로 사용할 수 없으며 값은 자동으로 업데이트되지 않습니다. rowversion 값을 타임스탬프 형식의 열에서 마이그레이션해야 하는 경우, NOT NULL 또는 NULL 행 버전 값으로 binary(8) 또는 varbinary(8)을 사용합니다.
- **사용자 정의 형식**, 가능한 경우 해당 네이티브 형식으로 다시 변환
- **xml**, 더 나은 성능을 위해 varchar(max) 이하 사용 필요한 경우 열에서 분할

성능을 향상하려면 다음을 사용합니다.

- nvarchar(max), 더 나은 성능을 위해 nvarchar(4000) 이하 사용
- varchar(max), 더 나은 성능을 위해 varchar(8000) 이하 사용

일부 지원:

- 기본 제약 조건은 리터럴 및 상수만 지원합니다. `GETDATE()` 또는 `CURRENT_TIMESTAMP`와 같은 명확하지 않은 식 또는 함수는 지원되지 않습니다.

> [AZURE.NOTE] Polybase를 사용하여 테이블을 로드하려면 가변 길이 열의 전체 길이를 포함하여 가능한 최대 행 크기가 32,767바이트를 초과하지 않도록 테이블을 정의합니다. 이 숫자를 초과할 수 있는 가변 길이 데이터로 행을 정의할 수 있고 BCP를 사용하여 행을 로드할 수 있는 반면 이 데이터를 로드하는 데 아직은 Polybase를 사용할 수 없습니다. 넓은 행에 대한 Polybase 지원이 곧 추가될 예정입니다. 또한 실행 중인 쿼리에 대한 더 나은 처리량을 위해 변수 길이 열의 크기를 제한합니다.

## 다음 단계
SQLDW로 데이터베이스 스키마를 성공적으로 마이그레이션한 후에 다음 문서 중 하나를 진행할 수 있습니다.

- [데이터 마이그레이션][]
- [코드 마이그레이션][]

더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[코드 마이그레이션]: sql-data-warehouse-migrate-code.md
[데이터 마이그레이션]: sql-data-warehouse-migrate-data.md
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0810_2016-->