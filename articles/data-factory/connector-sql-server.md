---
title: SQL Server에서 데이터 복사
description: Azure 데이터 팩터리를 사용하여 온-프레미스 또는 Azure VM에 있는 SQL Server 데이터베이스에서 데이터를 이동하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 063ac32c98d4eb64b676247c0a16f98fa7d1702d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416696"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 SQL Server에서 데이터를 복사합니다.

> [!div class="op_single_selector" title1="사용 하는 Azure 데이터 팩터리의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-sqlserver-connector.md)
> * [현재 버전](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory에서 복사 활동을 사용하여 SQL Server 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 복사 활동에 대한 일반적인 개요를 제공하는 [복사 활동 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 SQL Server 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

SQL Server 데이터베이스의 데이터를 지원되는 싱크 데이터 저장소로 복사할 수 있습니다. 또는 지원되는 원본 데이터 저장소의 데이터를 SQL Server 데이터베이스로 복사할 수 있습니다. 복사 활동에 의해 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하십시오.

특히 이 SQL Server 커넥터는 다음을 지원합니다.

- SQL Server 버전 2016, 2014, 2012, 2008 R2, 2008 및 2005.
- SQL 또는 Windows 인증을 사용한 데이터 복사
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용하여 데이터를 검색합니다.
- 싱크로, 복사 중에 대상 테이블에 데이터를 추가하거나 사용자 지정 논리를 사용하여 저장 프로시저 호출(싱크)

[SQL 서버 익스프레스 LocalDB는](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) 지원되지 않습니다.

>[!NOTE]
>SQL Server [항상 암호화는](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) 현재 이 커넥터에서 지원되지 않습니다. 해결하려면 [일반 ODBC 커넥터와](connector-odbc.md) SQL Server ODBC 드라이버를 사용할 수 있습니다. ODBC 드라이버 다운로드 및 연결 문자열 구성을 사용 하 [고이 지침을](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) 따르십시오.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SQL Server 데이터베이스 커넥터와 관련된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대한 세부 정보를 제공합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SQL Server 연결 서비스에 대해 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **SqlServer**로 설정해야 합니다. | 예 |
| connectionString |SQL 인증 또는 Windows 인증을 사용하여 SQL Server 데이터베이스에 연결하는 데 필요한 **연결String** 정보를 지정합니다. 다음 샘플을 참조하세요.<br/>Azure 키 자격 증명 모음에 암호를 넣을 수도 있습니다. SQL 인증인 경우 연결 `password` 문자열에서 구성을 가져옵니다. 자세한 내용은 Azure Key Vault의 테이블 및 [스토어 자격 증명 다음의](store-credentials-in-key-vault.md)JSON 예제를 참조하십시오. |예 |
| userName |Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. **domainname\\username**을 예로 들 수 있습니다. |예 |
| password |사용자 이름에 대해 지정한 사용자 계정에 대한 암호를 지정합니다. 이 필드를 **SecureString으로** 표시하여 Azure 데이터 팩터리에 안전하게 저장합니다. 또는 Azure [키 자격 증명 모음에 저장된 비밀을 참조할](store-credentials-in-key-vault.md)수 있습니다. |예 |
| connectVia | 이 [Integration Runtime](concepts-integration-runtime.md)은 데이터 저장소에 연결하는 데 사용됩니다. [필수 구성 조건](#prerequisites) 섹션에서 자세히 알아보십시오. 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. |예 |

>[!TIP]
>"UserErrorFailedToConnectToServer"라는 오류 코드와 "데이터베이스의 세션 제한이 XXX이고 도달했습니다"와 같은 메시지가 있는 `Pooling=false` 경우 연결 문자열에 추가하고 다시 시도하십시오.

**예 1: SQL 인증 사용**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제 2: Azure 키 자격 증명 모음에서 암호와 함께 SQL 인증 사용**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예 3: Windows 인증 사용**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 집합을 정의하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 SQL Server 데이터 집합에서 지원하는 속성 목록을 제공합니다.

SQL Server 데이터베이스에서 데이터를 복사하려면 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 형식 속성은 **SqlServerTable**으로 설정해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| 테이블 | 테이블/뷰의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| tableName | 스키마가 있는 테이블/보기의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 `schema` 경우 `table`및 를 사용합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

**예제**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 SQL Server 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="sql-server-as-a-source"></a>SQL 서버를 소스로

SQL Server의 데이터를 복사하려면 복사 작업의 원본 형식을 **SqlSource**로 설정합니다. 복사 작업 source 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 소스의 형식 속성을 **SqlSource**로 설정해야 합니다. | 예 |
| SqlReaderQuery |사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제는 `select * from MyTable`입니다. |예 |
| sqlReaderStoredProcedureName |이 속성은 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. |예 |
| storedProcedureParameters |저장 프로시저용 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름과 대/소문자는 저장된 프로시저 매개 변수의 이름과 대/소문자를 일치해야 합니다. |예 |
| isolationLevel | SQL 원본에 대한 트랜잭션 잠금 동작을 지정합니다. 허용된 값은 **다음과** 같습니다: ReadCommitted(기본값), **ReadUncommitted,** **반복 읽기,** **직렬화 가능**, **스냅샷**. 자세한 내용은 [이 문서를](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) 참조하십시오. | 예 |

**주의할 사항:**

- **sqlSource에**대해 **sqlReaderQuery를** 지정하면 복사 활동이 SQL Server 원본에 대해 이 쿼리를 실행하여 데이터를 가져옵니다. 저장 프로시저가 매개 변수를 사용하는 경우에는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수도 있습니다.
- **sqlReaderQuery** 또는 **sqlReader저장프로시내Name을**지정하지 않으면 데이터 집합 JSON의 "구조" 섹션에 정의된 열이 쿼리를 생성하는 데 사용됩니다. 쿼리는 `select column1, column2 from mytable` SQL Server에 대해 실행됩니다. 데이터 세트 정의에 "structure"가 없는 경우 테이블에서 모든 열이 선택됩니다.

**예: SQL 쿼리 사용**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**예: 저장 프로시저 사용**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**저장 프로시저 정의**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>싱크대로서의 SQL 서버

> [!TIP]
> [SQL Server로 데이터를 로드하는 모범 사례에서](#best-practice-for-loading-data-into-sql-server)지원되는 쓰기 동작, 구성 및 모범 사례에 대해 자세히 알아봅니다.

SQL Server에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SqlSink**로 설정합니다. 복사 작업 sink 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 싱크의 형식 속성을 **SqlSink로**설정해야 합니다. | 예 |
| writeBatchSize |*일괄 처리당*SQL 테이블에 삽입할 행 수입니다.<br/>허용되는 값은 행 수에 해당하는 정수입니다. 기본적으로 Azure Data Factory는 행 크기에 따라 적절한 일괄 처리 크기를 동적으로 결정합니다. |예 |
| writeBatchTimeout |이 속성은 시간이 초과되기 전에 완료하려는 배치 삽입 작업의 대기 시간을 지정합니다.<br/>허용된 값은 시간 범위에 대한 값입니다. 예를 들어 30분 동안 "00:30:00"입니다. 값을 지정하지 않으면 시간 지정이 기본값 "02:00:00"입니다. |예 |
| preCopyScript |이 속성은 SQL Server에 데이터를 작성하기 전에 복사 작업이 실행되도록 SQL 쿼리를 지정합니다. 복사 실행당 한 번만 호출됩니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. |예 |
| sqlWriterStoredProcedureName | 원본 데이터를 대상 테이블에 적용하는 방법을 정의하는 저장 프로시저의 이름입니다. <br/>이 저장 프로시저는 *배치마다 호출*됩니다. 한 번만 실행되고 원본 데이터와 아무 관련이 없는 작업의 경우(예: 삭제 `preCopyScript` 또는 실행)는 속성을 사용합니다. | 예 |
| 저장프로시저 테이블유형매개 변수 이름 |저장 프로시저에 지정된 테이블 유형의 매개 변수 이름입니다.  |예 |
| sqlWriterTableType |저장 프로시저에 사용할 테이블 유형 이름입니다. 복사 작업에서는 이동 중인 데이터를 이 테이블 형식의 임시 테이블에서 사용할 수 있습니다. 그러면 저장 프로시저 코드가 복사 중인 데이터를 기존 데이터와 병합할 수 있습니다. |예 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 및 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 예 |
| 테이블 옵션 | 원본 스키마에 따라 싱크 테이블이 없는 경우 싱크 테이블을 자동으로 만들지 여부를 지정합니다. 싱크가 저장 프로시저를 지정하거나 준비된 복사본이 복사 작업에서 구성될 때 자동 테이블 만들기가 지원되지 않습니다. 허용된 값은 `none` 다음과 `autoCreate`같습니다(기본값) . |예 |

**예 1: 데이터 부속**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**예 2: 복사 하는 동안 저장 프로시저 호출**

자세한 내용은 [SQL 싱크에서 저장 프로시저 호출](#invoke-a-stored-procedure-from-a-sql-sink)을 참조하세요.

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>SQL Server에 데이터를 로드하는 모범 사례

SQL Server에 데이터를 복사할 때 다른 쓰기 동작이 필요할 수 있습니다.

- [부속](#append-data): 원본 데이터에 새 레코드만 있습니다.
- [Upsert](#upsert-data): 원본 데이터에 삽입과 업데이트가 모두 있습니다.
- [덮어쓰기](#overwrite-the-entire-table): 매번 전체 차원 테이블을 다시 로드하려고 합니다.
- [사용자 지정 논리로 쓰기](#write-data-with-custom-logic): 대상 테이블에 최종 삽입하기 전에 추가 처리가 필요합니다.

Azure 데이터 팩터리에서 구성하는 방법 및 모범 사례에 대한 각 섹션을 참조하세요.

### <a name="append-data"></a>데이터 추가

데이터 를 더하는 것은 이 SQL Server 싱크 커넥터의 기본 동작입니다. Azure Data Factory는 테이블에 효율적으로 쓰기 위해 대량 삽입을 수행합니다. 복사 활동에서 소스와 싱크를 구성할 수 있습니다.

### <a name="upsert-data"></a>데이터 Upsert

**옵션 1:** 복사할 데이터가 많은 경우 다음 방법을 사용하여 upsert를 수행합니다. 

- 먼저 임시 [테이블을](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) 사용하여 복사 작업을 사용하여 모든 레코드를 대량 로드합니다. 임시 테이블에 대한 작업은 기록되지 않으므로 몇 초 만에 수백만 개의 레코드를 로드할 수 있습니다.
- Azure 데이터 팩터리에서 저장 프로시저 활동을 실행하여 [병합](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) 또는 INSERT/UPDATE 문을 적용합니다. 임시 테이블을 소스로 사용하여 모든 업데이트 또는 삽입을 단일 트랜잭션으로 수행합니다. 이렇게 하면 왕복 및 로그 작업 수가 줄어듭니다. 저장 프로시저 작업이 끝나면 임시 테이블을 잘리면 다음 upsert 주기에 대비할 수 있습니다.

예를 들어 Azure Data Factory에서 **저장 프로시저 활동과**연결된 **복사 활동이** 있는 파이프라인을 만들 수 있습니다. 이전은 원본 저장소의 데이터를 데이터베이스 임시 테이블(예: **##UpsertTempTable)에**데이터 집합의 테이블 이름으로 복사합니다. 그런 다음 후자는 임시 테이블의 원본 데이터를 대상 테이블로 병합하고 임시 테이블을 정리하는 저장 프로시저를 호출합니다.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

데이터베이스에서 이전 저장 프로시저 작업에서 가리키는 다음 예제와 같이 MERGE 논리를 사용하여 저장 프로시저를 정의합니다. 대상이 **ProfileID,** **상태**및 **범주의**세 개의 열이 있는 **마케팅** 테이블이라고 가정합니다. **ProfileID** 열을 기반으로 upsert를 수행합니다.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**옵션 2:** [복사 활동 내에서 저장된 프로시저를 호출하도록](#invoke-a-stored-procedure-from-a-sql-sink)선택할 수도 있습니다. 이 방법은 대량 삽입을 복사 작업의 기본 접근 방식으로 사용하는 대신 원본 테이블의 각 행을 실행하므로 대규모 upsert에는 적합하지 않습니다.

### <a name="overwrite-the-entire-table"></a>전체 테이블 덮어쓰기

복사 활동 싱크에서 **preCopyScript** 속성을 구성할 수 있습니다. 이 경우 실행되는 각 복사 활동에 대해 Azure Data Factory는 먼저 스크립트를 실행합니다. 그런 다음 복사본을 실행하여 데이터를 삽입합니다. 예를 들어 전체 테이블을 최신 데이터로 덮어쓰려면 원본에서 새 데이터를 대량 로드하기 전에 먼저 모든 레코드를 삭제할 스크립트를 지정합니다.

### <a name="write-data-with-custom-logic"></a>사용자 지정 논리로 데이터 쓰기

사용자 지정 논리로 데이터를 작성하는 단계는 [Upsert 데이터](#upsert-data) 섹션에 설명된 단계와 유사합니다. 원본 데이터를 대상 테이블에 최종 삽입하기 전에 추가 처리를 적용해야 하는 경우 대규모로 다음 두 가지 중 하나를 수행할 수 있습니다. 

- 임시 테이블에 로드한 다음 저장 프로시저를 호출합니다. 
- 복사 하는 동안 저장 프로시저를 호출 합니다.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> SQL 싱크에서 저장 프로시저 호출

SQL Server 데이터베이스에 데이터를 복사할 때 추가 매개 변수를 사용하여 사용자가 지정한 저장 프로시저를 구성하고 호출할 수도 있습니다. 저장 프로시저 기능은 [테이블 값 매개 변수를 활용합니다.](https://msdn.microsoft.com/library/bb675163.aspx)

> [!TIP]
> 저장 프로시저를 호출하면 대량 작업을 사용하는 대신 데이터 행을 행별로 처리하므로 대규모 복사본에는 권장하지 않습니다. [SQL Server로 데이터를 로드하는 모범 사례에서](#best-practice-for-loading-data-into-sql-server)자세히 알아보십시오.

기본 제공 복사 메커니즘이 용도에 적합하지 않은 경우, 저장 프로시저를 사용할 수 있습니다. 예를 들어 원본 데이터를 대상 테이블에 최종 삽입하기 전에 추가 처리를 적용하려는 경우를 들 수 있습니다. 몇 가지 추가 처리 예제는 열을 병합하고, 추가 값을 조회하고, 둘 이상의 테이블에 데이터를 삽입하려는 경우입니다.

다음 샘플에서는 저장 프로시저를 사용하여 SQL Server 데이터베이스 내 테이블에 간단한 삽입을 수행하는 방법을 보여줍니다. 입력 데이터와 싱크 **마케팅** 테이블에 각각 **ProfileID,** **상태**및 **범주의**세 개의 열이 있다고 가정합니다. **ProfileID** 열을 기반으로 upsert를 수행하 고 "ProductA"라는 특정 범주에만 적용 합니다.

1. 데이터베이스에서 **sqlWriterTableType**과 같은 이름으로 테이블 형식을 정의합니다. 테이블 형식의 스키마는 입력 데이터에서 반환된 스키마와 같아야 합니다.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. 데이터베이스에서 **sqlWriter저장프로시프로시이름과**동일한 이름으로 저장된 프로시저를 정의합니다. 지정된 원본의 입력 데이터를 처리하고 출력 테이블에 병합합니다. 저장 프로시저의 테이블 형식의 매개 변수 이름은 데이터 집합에 정의된 **tableName과** 동일합니다.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. Azure 데이터 팩터리에서 복사 활동의 **SQL 싱크** 섹션을 다음과 같이 정의합니다.

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>SQL Server용 데이터 유형 매핑

SQL Server에서 SQL Server로 데이터를 복사하면 SQL Server 데이터 형식에서 Azure Data Factory 중간 데이터 형식에 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SQL Server 데이터 형식 | Azure Data Factory 중간 데이터 형식 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |부울 |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM 특성(varbinary(max)) |Byte[] |
| Float |Double |
| 이미지 |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |xml |

>[!NOTE]
> 10진수 중간 형식으로 매핑되는 데이터 형식의 경우 Azure Data Factory는 현재 최대 28자리의 데이터를 지원합니다. 자릿수가 28자리를 초과하는 데이터가 있으면 SQL 쿼리에서 문자열로 변환하는 것이 좋습니다.

## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.

## <a name="getmetadata-activity-properties"></a>GetMetadata 활동 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 활동을](control-flow-get-metadata-activity.md) 선택합니다. 

## <a name="troubleshoot-connection-issues"></a>연결 문제 해결

1. 원격 연결을 허용하도록 SQL Server 인스턴스를 구성합니다. **SQL Server 관리 스튜디오를**시작하고 서버를 마우스 오른쪽 단추로 클릭하고 **속성을**선택합니다. **server** 목록에서 **연결을** 선택하고 이 서버에 대한 원격 연결 허용 **확인란을** 선택합니다.

    ![원격 연결 사용](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    For detailed steps, see [Configure the remote access server configuration option](https://msdn.microsoft.com/library/ms191464.aspx).

2. **SQL 서버 구성 관리자를**시작합니다. 사용하려는 인스턴스에 대한 **SQL Server 네트워크 구성**을 확장하고 **MSSQLSERVER용 프로토콜**을 선택합니다. 프로토콜이 오른쪽 창에 나타납니다. TCP/IP를 마우스 오른쪽 단추로 클릭하고 **활성화를**선택하여 **TCP/IP를** 활성화합니다.

    ![TCP/IP 사용](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    TCP/IP 프로토콜을 사용하도록 설정하는 방법에 대한 자세한 정보 및 대체 방법은 [서버 네트워크 프로토콜 사용 또는 비활성화를](https://msdn.microsoft.com/library/ms191294.aspx)참조하십시오.

3. 동일한 창에서 **TCP/IP를** 두 번 클릭하여 **TCP/IP 속성** 창을 시작합니다.
4. **IP 주소** 탭으로 전환합니다. 아래로 스크롤하여 **IPAll** 섹션을 확인합니다. **TCP 포트를**적어 둡니다. 기본값은 **1433입니다.**
5. 컴퓨터에 **Windows 방화벽에 대한 규칙** 을 만들어 이 포트를 통해 들어오는 트래픽을 허용합니다. 
6. **연결 확인**: 정규화된 이름을 사용하여 SQL Server에 연결하려면 다른 컴퓨터의 SQL Server 관리 Studio를 사용합니다. 예제는 `"<machine>.<domain>.corp.<company>.com,1433"`입니다.

## <a name="next-steps"></a>다음 단계
Azure Data Factory의 복사 활동에 의해 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소를](copy-activity-overview.md#supported-data-stores-and-formats)참조하십시오.
