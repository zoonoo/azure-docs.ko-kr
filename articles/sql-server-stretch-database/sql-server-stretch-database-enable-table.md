<properties
	pageTitle="테이블에 대해 스트레치 데이터베이스를 사용하도록 설정 | Microsoft Azure"
	description="스트레치 데이터베이스에 대해 테이블을 구성하는 방법을 알아봅니다."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# 테이블에 대해 스트레치 데이터베이스를 사용하도록 설정

스트레치 데이터베이스에 대해 테이블을 구성하려면 SQL Server Management Studio에서 테이블에 대해 **스트레치 | 사용**을 선택하여 **스트레치에 테이블 사용** 마법사를 엽니다. 또한 Transact-SQL을 사용하여 기존 테이블에 대해 스트레치 데이터베이스를 사용하도록 설정하거나 스트레치 데이터베이스를 사용하여 새 테이블을 만들 수도 있습니다.

-   콜드 데이터를 별도 테이블에 저장하는 경우 전체 테이블을 마이그레이션할 수 있습니다.

-   테이블에 핫 데이터 및 콜드 데이터가 모두 포함된 경우 필터 조건자를 지정하여 마이그레이션할 행을 선택할 수 있습니다.

**필수 조건**. 데이터베이스에 대해 스트레치 데이터베이스를 사용하도록 설정하지 않은 경우 테이블에 대해 **스트레치 | 사용**을 선택하면 마법사에서 먼저 스트레치 데이터베이스에 대해 데이터베이스를 구성합니다. 이 항목의 단계 대신 [스트레치에 데이터베이스 사용 마법사를 실행하여 시작](sql-server-stretch-database-wizard.md)의 단계를 따르세요.

**사용 권한**. 데이터베이스 또는 테이블에 대해 스트레치 데이터베이스를 사용하도록 설정하려면 db\_owner 권한이 필요합니다. 테이블에서 스트레치 데이터베이스를 사용하도록 설정하려면 테이블에 대한 ALTER 권한이 있어야 합니다.

## <a name="EnableWizardTable"></a>마법사를 사용하여 테이블에서 스트레치 데이터베이스를 사용하도록 설정
**마법사 시작**

1.  SQL Server Management Studio의 개체 탐색기에서 스트레치를 사용하도록 설정할 테이블을 선택합니다.

2.  마우스 오른쪽 단추를 클릭하고 **스트레치**를 선택한 후 **사용**을 선택하여 마법사를 시작합니다.

**소개**

마법사의 용도 및 필수 조건을 검토합니다.

**데이터베이스 테이블 선택**

사용하도록 설정하려는 테이블이 표시되고 선택되었는지 확인합니다.

전체 테이블을 마이그레이션하거나 마법사에서 간단한 필터 조건자를 지정할 수 있습니다. 다른 종류의 필터 조건자를 사용하여 마이그레이션할 행을 선택하려면 다음 중 하나를 수행합니다.

-   마법사를 종료하고 ALTER TABLE 문을 실행하여 테이블에 대한 스트레치를 사용하도록 설정하고 조건자를 지정합니다.

-   마법사를 종료한 후 ALTER TABLE 문을 실행하여 조건자를 지정합니다. 필요한 단계는 [마법사를 실행한 후 필터 조건자 추가](sql-server-stretch-database-predicate-function.md#addafterwiz)를 참조하세요.

ALTER TABLE 구문은 이 항목의 뒷부분에서 설명합니다.

**요약**

입력한 값과 마법사에서 선택한 옵션을 검토합니다. 그런 다음 **마침**을 선택하여 스트레치를 사용하도록 설정합니다.

**결과**

결과를 검토합니다.

## <a name="EnableTSQLTable"></a>Transact-SQL을 사용하여 테이블에서 스트레치 데이터베이스를 사용하도록 설정
또한 Transact-SQL을 사용하여 기존 테이블에 대해 스트레치 데이터베이스를 사용하도록 설정하거나 스트레치 데이터베이스를 사용하여 새 테이블을 만들 수도 있습니다.

### 옵션
CREATE TABLE 또는 ALTER TABLE을 실행하여 테이블에 대해 스트레치 데이터베이스를 사용하도록 설정하려면 다음 옵션을 사용합니다.

-   필요에 따라 `FILTER_PREDICATE = <predicate>` 절을 사용하여 테이블에 핫 데이터와 콜드 데이터가 모두 포함된 경우 마이그레이션할 행을 선택하도록 조건자를 지정합니다. 조건자는 인라인 테이블 값 함수를 호출해야 합니다. 자세한 내용은 [필터 조건자를 사용하여 마이그레이션할 행 선택](sql-server-stretch-database-predicate-function.md)을 참조하세요. 필터 조건자를 지정하지 않으면 전체 테이블이 마이그레이션됩니다.

    >   [AZURE.NOTE] 제대로 수행되지 않는 필터 조건자를 제공하는 경우 데이터 마이그레이션도 제대로 수행되지 않습니다. 스트레치 데이터베이스는 CROSS APPLY 연산자를 사용하여 테이블에 필터 조건자를 적용합니다.

-   `MIGRATION_STATE = OUTBOUND`를 지정하여 데이터 마이그레이션을 즉시 시작하거나, `MIGRATION_STATE = PAUSED`를 지정하여 데이터 마이그레이션 시작을 연기합니다.

### 기존 테이블에 대해 스트레치 데이터베이스를 사용하도록 설정
스트레치 데이터베이스에 대해 기존 테이블을 구성하려면 ALTER TABLE 명령을 실행합니다.

다음은 전체 테이블을 마이그레이션하고 데이터 마이그레이션을 즉시 시작하는 예제입니다.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```
다음은 `dbo.fn_stretchpredicate` 인라인 테이블 반환 함수로 식별된 행만 마이그레이션하고 데이터 마이그레이션을 연기하는 예제입니다. 필터 조건자에 대한 자세한 내용은 [필터 조건자를 사용하여 마이그레이션할 행 선택](sql-server-stretch-database-predicate-function.md)을 참조하세요.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
 GO
```

자세한 내용은 [ALTER TABLE(Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)을 참조하세요.

### 스트레치 데이터베이스를 설정하여 새 테이블 만들기
스트레치 데이터베이스를 설정하여 새 테이블을 만들려면 CREATE TABLE 명령을 실행합니다.

다음은 전체 테이블을 마이그레이션하고 데이터 마이그레이션을 즉시 시작하는 예제입니다.

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```

다음은 `dbo.fn_stretchpredicate` 인라인 테이블 반환 함수로 식별된 행만 마이그레이션하고 데이터 마이그레이션을 연기하는 예제입니다. 필터 조건자에 대한 자세한 내용은 [필터 조건자를 사용하여 마이그레이션할 행 선택](sql-server-stretch-database-predicate-function.md)을 참조하세요.

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
GO  
```

자세한 내용은 [CREATE TABLE(Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)을 참조하세요.


## 참고 항목

[ALTER TABLE(Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

[CREATE TABLE(Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)

<!---HONumber=AcomDC_0622_2016-->