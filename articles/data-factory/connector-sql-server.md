---
title: Azure Data Factory를 사용하여 SQL Server 간에 데이터 복사 | Microsoft Docs
description: Azure 데이터 팩터리를 사용하여 온-프레미스 또는 Azure VM에 있는 SQL Server 데이터베이스 간에 데이터를 이동하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: 62845557f33fd9c4f3c2ec4e239213c75101955d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275986"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SQL Server 간 데이터 복사
> [!div class="op_single_selector" title1="사용 하는 Data Factory 서비스 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-sqlserver-connector.md)
> * [현재 버전](connector-sql-server.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 SQL Server 데이터베이스에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

SQL Server 데이터베이스에서 지원되는 싱크 데이터 저장소로, 또는 지원되는 원본 데이터 저장소에서 SQL Server 데이터베이스로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 SQL Server 커넥터는 다음을 지원합니다.

- SQL Server 버전 2016, 2014, 2012, 2008 R2, 2008 및 2005
- **SQL** 또는 **Windows** 인증을 사용하여 데이터를 복사합니다.
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용하여 데이터를 검색합니다.
- 싱크로, 대상 테이블에 데이터를 첨부하거나 복사 중에 사용자 지정 논리를 사용하여 저장 프로시저를 호출합니다.

>[!NOTE]
>SQL Server **[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)** 이제이 커넥터에서 지원 되지 않습니다. 이 문제를 해결 하려면 사용할 수 있습니다 [제네릭 ODBC 커넥터](connector-odbc.md) 및 SQL Server ODBC 드라이버. 따릅니다 [이 지침은](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) ODBC 드라이버 다운로드 하 고 연결 문자열 구성으로 합니다.

## <a name="prerequisites"></a>필수 조건

공개적으로 액세스할 수 없는 SQL Server 데이터베이스에서 데이터 복사를 사용하려면 자체 호스팅 통합 런타임을 설정해야 합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요. 통합 런타임은 기본 제공 SQL Server 드라이버를 제공하므로 SQL Server 간에 데이터를 복사할 때 수동으로 드라이버를 설치할 필요가 없습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SQL Server 데이터베이스 커넥터에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SQL Server 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성을 다음으로 설정해야 합니다. **SqlServer** | 예 |
| connectionString |SQL 인증 또는 Windows 인증을 사용하여 SQL Server 데이터베이스에 연결하는 데 필요한 connectionString 정보를 지정합니다. 다음 샘플을 참조하세요.<br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. 암호를 Azure Key Vault에 넣고, SQL 인증인 경우 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 표 아래의 JSON 예제 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예 |
| userName |Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. 예: **domainname\\username**. |아닙니다. |
| password |userName에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |아닙니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

>[!TIP]
>"UserErrorFailedToConnectToSqlServer" 오류 코드 및 "데이터베이스에 대한 세션 제한이 XXX이고 이에 도달했습니다."와 같은 메시지가 있는 오류가 발생하면 연결 문자열에 `Pooling=false`를 추가하고 다시 시도하세요.

**예 1: SQL 인증 사용**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제 2: Azure Key Vault의 암호를 사용하는 SQL 인증 사용**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
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
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 세트 문서를 참조하세요. 이 섹션에서는 SQL Server 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

SQL Server 데이터베이스에서 데이터를 복사 하려면 다음 속성이 지원 됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **SqlServerTable** | 예 |
| tableName |연결된 서비스가 참조하는 SQL Server 데이터베이스 인스턴스에서 테이블 또는 보기의 이름입니다. | 원본에는 아니요이고 싱크에는 예입니다 |

**예제:**

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 SQL Server 원본 및 싱크에서 지원되는 속성 목록을 제공합니다.

### <a name="sql-server-as-source"></a>원본으로 SQL Server

SQL Server의 데이터를 복사하려면 복사 작업의 원본 형식을 **SqlSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **SqlSource** | 예 |
| sqlReaderQuery |사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `select * from MyTable`. |아니오 |
| sqlReaderStoredProcedureName |원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. |아니오 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |아닙니다. |

**주의할 사항:**

- **sqlReaderQuery**가 SqlSource에 지정되면 복사 작업은 데이터를 가져오는 SQL Server 원본에 대해 이 쿼리를 실행합니다. 또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).
- "sqlReaderQuery" 또는 "sqlReaderStoredProcedureName" 중 하나를 지정하지 않으면 JSON 데이터 세트의 "structure" 섹션에 정의된 열은 쿼리(`select column1, column2 from mytable`)를 생성하는 데 사용되어 SQL Server에 대해 실행합니다. 데이터 세트 정의에 "structure"가 없는 경우 모든 열은 테이블에서 선택됩니다.

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

**저장 프로시저 정의:**

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

### <a name="sql-server-as-sink"></a>싱크로 SQL Server

> [!TIP]
> 지원 되는 쓰기 동작, 구성 및에서 모범 사례에 자세히 알아보세요 [모범 사례를 SQL Server로 데이터를 로드 하기 위한](#best-practice-for-loading-data-into-sql-server)합니다.

SQL Server에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SqlSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 type 속성을 다음으로 설정해야 합니다. **SqlSink** | 예 |
| writeBatchSize |SQL 테이블에 삽입 하는 행 수가 **일괄 처리당**합니다.<br/>허용되는 값은 정수(행 수)입니다. 기본적으로 Data Factory는 행의 크기에 따라 적절 한 일괄 처리 크기를 동적으로 결정 합니다. |아닙니다. |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br/>허용되는 값은 시간 범위입니다. 예제: “00:30:00”(30분) |아닙니다. |
| preCopyScript |SQL Server에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 복사 실행당 한 번만 호출됩니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. |아닙니다. |
| sqlWriterStoredProcedureName |대상 테이블에 원본 데이터를 적용 하는 방법을 정의 하는 저장된 프로시저의 이름입니다.<br/>이 저장 프로시저는 **배치마다 호출**됩니다. 한 번만 실행되고 원본 데이터와 아무런 관련이 없는 작업(예: 삭제/자르기)을 수행하려는 경우 `preCopyScript` 속성을 사용합니다. |아닙니다. |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |아닙니다. |
| sqlWriterTableType |저장 프로시저에 사용할 테이블 형식 이름을 지정합니다. 복사 작업을 사용하면 이 테이블 형식으로 임시 테이블에서 사용할 수 있는 데이터를 이동시킵니다. 그러면 저장 프로시저 코드가 복사되는 데이터를 기존 데이터와 병합할 수 있습니다. |아닙니다. |

**예제 1: 데이터 추가**

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
                "writeBatchSize": 100000
            }
        }
    }
]
```

**복사 중 저장된 프로시저를 호출 하는 예 2:**

자세한 내용은 [SQL 싱크에 대한 저장 프로시저 호출](#invoking-stored-procedure-for-sql-sink)을 참조하세요.

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
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>SQL Server로 데이터를 로드 하기 위한 모범 사례

SQL Server로 데이터를 복사할 때 다른 쓰기 동작이 필요할 수 있습니다.

- **[추가](#append-data)** : 원본 데이터에만 새 레코드가;에 있는
- **[Upsert](#upsert-data)** : 내 원본 데이터에 삽입 및 업데이트
- **[덮어쓰기](#overwrite-entire-table)** : 각 시간 전체 차원 테이블을 다시 로드 하려고 하는 경우
- **[사용자 지정 논리를 사용 하 여 작성할](#write-data-with-custom-logic)** : 대상 테이블에 최종 삽입 전에 추가 처리가 필요합니다.

참조 된 ADF 및 모범 사례를 구성 하는 방법에 각각 섹션입니다.

### <a name="append-data"></a>데이터를 추가 합니다.

이 SQL Server 싱크 연결선의 기본 동작이 며 ADF 수행 **대량 삽입** 테이블에 효율적으로 작성 합니다. 단순히 소스를 구성할 수 있으며 그에 따라 싱크 복사 활동의 합니다.

### <a name="upsert-data"></a>데이터 Upsert

**I 옵션** (있는 경우 특히 큰 데이터 복사를 제안): 합니다 **대부분의 성능이 뛰어난 방법을** upsert를 수행 하는 다음: 

- 첫째, 활용을 [임시 테이블](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) 대량 복사 작업을 사용 하 여 모든 레코드를 로드 하 합니다. 임시 테이블에 대 한 작업은 기록 되지 대로 초에서 수백만 개의 레코드를 로드할 수 있습니다.
- 저장 프로시저 작업을 적용 하는 ADF에서 실행을 [병합](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (또는 삽입/업데이트) 문 및 temp 왕복을 줄이는 모든을 수행 하는 소스를 단일 트랜잭션으로 삽입 하거나 업데이트 하는 대로 테이블 및 작업 기록 사용 합니다. 저장 프로시저 작업의 끝 다음 upsert 주기에 대 한 준비로 임시 테이블을 자를 수 있습니다. 

예를 들어 Azure Data Factory를 만들 수 있습니다 사용 하는 파이프라인을 **복사 활동** 연계 하 여를 **저장 프로시저 작업** 성공 합니다. 이전의 복사 데이터 원본 저장소에서 데이터베이스 임시 테이블 예를 들어 " **##UpsertTempTable**" 데이터 집합의 테이블 이름으로 다음 두 번째 호출 대상 테이블로 임시 테이블에서 원본 데이터를 병합 하는 저장 프로시저 및 임시 테이블을 정리 합니다.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

데이터베이스에 위의 저장 프로시저 활동에서 가리키는 다음과 같은 병합 논리를 사용 하 여 저장 프로시저를 정의 합니다. 대상 가정 **마케팅** 세 열이 있는 테이블: **ProfileID**, **상태**, 및 **범주**를 기준으로 upsert를 수행 합니다 **ProfileID** 열입니다.

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

**옵션 II:** 또는를 선택할 수 있습니다 [복사 작업 내에서 저장된 프로시저 호출](#invoking-stored-procedure-for-sql-sink),이 이렇게 대량을 활용 하는 대신 원본 테이블의 각 행에 대해 실행 되는 참고 하는 동안 기본 접근 방식으로 삽입 따라서 복사 활동의 대규모 upsert에 대해 맞지 않는 것입니다.

### <a name="overwrite-entire-table"></a>전체 테이블 덮어쓰기

구성할 수 있습니다 **preCopyScript** 속성 복사 활동의 sink, 이때 각 복사 작업 실행에 대 한 ADF 스크립트를 실행 먼저 데이터를 삽입 하려면 복사본을 실행 합니다. 예를 들어 최신 데이터를 사용하여 전체 테이블을 덮어쓰려면 원본에서 새 데이터를 대량으로 로드하기 전에 먼저 스크립트를 지정하여 모든 레코드를 삭제할 수 있습니다.

### <a name="write-data-with-custom-logic"></a>사용자 지정 논리를 사용 하 여 데이터를 작성 합니다.

에 설명 된 것과 유사 [Upsert 데이터](#upsert-data) 섹션에서는 대상 테이블에 원본 데이터의 최종 삽입 전에 추가 처리를 적용 해야 할 수 있습니다는) 대규모 임시 테이블로 로드 한 후 저장 된 호출 프로시저 또는 b) 복사 중 저장된 프로시저를 호출 합니다.

## <a name="invoking-stored-procedure-for-sql-sink"></a> SQL 싱크에서 저장된 프로시저 호출

SQL Server 데이터베이스로 데이터를 복사 하는 경우 구성 수 및 추가 매개 변수를 사용 하 여 사용자 지정 저장된 프로시저를 호출 해야 합니다.

> [!TIP]
> 저장된 프로시저를 호출 하는 데이터에서 행 대규모 복사본에 대 한 추천 하지 않습니다는 대량 작업을 대신 처리 합니다. 자세히 알아보세요 [모범 사례를 SQL Server로 데이터를 로드 하기 위한](#best-practice-for-loading-data-into-sql-server)합니다.

기본 제공 메커니즘이 용도 적합 하지 않습니다 하는 경우에 저장된 프로시저를 사용할 수 있습니다 예를 들어 대상 테이블에 원본 데이터의 최종 삽입 전에 추가 처리를 적용 합니다. 몇 가지 추가 처리 예제로 열 병합, 추가 값 조회, 두 개 이상의 테이블에 삽입 등이 있습니다.

다음 샘플에서는 저장 프로시저를 사용하여 SQL Server 데이터베이스 내 테이블에 간단한 삽입을 수행하는 방법을 보여줍니다. 각기 다음과 같은 세 개 열을 갖는 입력 데이터와 싱크 **Marketing** 테이블을 가정해 보겠습니다. **ProfileID**, **State** 및 **Category**. **ProfileID** 열을 기준으로 upsert(업데이트/삽입)를 수행하고 특정 범주에 대해서만 적용합니다.

**출력 데이터 집합:** "tableName" 저장된 프로시저 (저장된 프로시저 스크립트 아래 참조)의 동일한 테이블 형식 매개 변수 이름 이어야 합니다.

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
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

정의 된 **SQL 싱크** 다음과 같이 복사 활동의 섹션입니다.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

데이터베이스에서 **SqlWriterStoredProcedureName**과 동일한 이름의 저장 프로시저를 정의합니다. 지정된 원본의 입력 데이터를 처리하고 출력 테이블에 병합합니다. 저장 프로시저에서 테이블 형식의 매개 변수 이름은 데이터 세트에 정의된 **tableName**과 동일해야 합니다.

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

데이터베이스에서 sqlWriterTableType과 동일한 이름으로 테이블 형식을 정의합니다. 테이블 형식의 스키마가 입력 데이터에서 반환된 스키마와 동일해야 합니다.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

저장된 프로시저 기능은 [테이블 값 매개 변수](https://msdn.microsoft.com/library/bb675163.aspx)을 이용합니다.

## <a name="data-type-mapping-for-sql-server"></a>SQL Server에 대한 데이터 형식 매핑

SQL Server 간에 데이터를 복사하는 경우 SQL Server 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SQL Server 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| 텍스트 |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> 데이터 형식이 10진수 중간 형식으로 매핑되는 경우 ADF는 현재 최대 28 자릿수의 데이터를 지원합니다. 28보다 큰 자릿수의 데이터가 있는 경우 SQL 쿼리에서 문자열로 변환하는 것이 좋습니다.

## <a name="troubleshooting-connection-issues"></a>연결 문제 해결

1. 원격 연결을 허용하도록 SQL Server를 구성합니다. **SQL Server Management Studio**를 시작하고 **서버**를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. 목록에서 **연결**을 선택하고 **서버에 대한 원격 연결 허용**을 선택합니다.

    ![원격 연결 사용](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    자세한 단계를 보려면 [원격 액세스 서버 구성 옵션 구성](https://msdn.microsoft.com/library/ms191464.aspx) 을 참조하세요.

2. **SQL Server 구성 관리자**를 시작합니다. 사용하려는 인스턴스에 대한 **SQL Server 네트워크 구성**을 확장하고 **MSSQLSERVER용 프로토콜**을 선택합니다. 오른쪽 창에 프로토콜이 표시됩니다. **TCP/IP**를 마우스 오른쪽 단추로 클릭하고 **사용**을 클릭하여 TCP/IP를 사용하도록 설정합니다.

    ![TCP/IP 사용](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    TCP/IP 프로토콜을 사용하는 다른 방법 및 자세한 내용은 [서버 네트워크 프로토콜 사용 또는 사용 안 함](https://msdn.microsoft.com/library/ms191294.aspx) 을 참조하세요.

3. 같은 창에서 **TCP/IP**를 두 번 클릭하여 **TCP/IP 속성** 창을 시작합니다.
4. **IP 주소** 탭으로 전환합니다. 아래로 스크롤하여 **IPAll** 섹션을 확인합니다. **TCP 포트**(기본값은 **1433**)를 기록해 둡니다.
5. 컴퓨터에 **Windows 방화벽에 대한 규칙** 을 만들어 이 포트를 통해 들어오는 트래픽을 허용합니다.  
6. **연결 확인**: 정규화된 이름을 사용하여 SQL Server에 연결하려면 다른 머신의 SQL Server Management Studio를 사용합니다. 예: `"<machine>.<domain>.corp.<company>.com,1433"`

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
