---
title: "Data Factory를 사용하여 Azure SQL Database 간에 데이터 복사 | Microsoft Docs"
description: "지원되는 원본 데이터 저장소에서 Azure SQL Database로 (또는) SQL Database에서 지원되는 싱크 데이터 저장소로 Data Factory를 사용하여 데이터를 복사하는 방법에 대해 알아봅니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: jingwang
ms.openlocfilehash: a5d2994eb1203274454fc31c3ee9bf7a21562f75
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure SQL Database 간 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-azure-sql-connector.md)
> * [버전 2 - 미리 보기](connector-azure-sql-database.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure SQL Database 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 Azure SQL Database 커넥터](v1/data-factory-azure-sql-connector.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

Azure SQL Database에서 모든 지원되는 싱크 데이터 저장소로 데이터를 복사하거나 모든 지원되는 원본 데이터 저장소에서 Azure SQL Database로 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Azure SQL Database 커넥터는 다음을 지원합니다.

- SQL 인증을 사용하여 데이터를 복사합니다.
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용하여 데이터를 검색합니다.
- 싱크로, 대상 테이블에 데이터를 첨부하거나 복사 중에 사용자 지정 논리를 사용하여 저장 프로시저를 호출합니다.

## <a name="getting-started"></a>시작
.NET SDK, Python SDK, Azure PowerShell, REST API 또는 Azure Resource Manager 템플릿을 사용하여 복사 작업으로 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](quickstart-create-data-factory-dot-net.md)를 참조하세요.

다음 섹션에서는 Azure SQL Database 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure SQL Database 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureSqlDatabase** | 예 |
| connectionString |connectionString 속성에 대한 Azure SQL Database 인스턴스에 연결하는 데 필요한 정보를 지정합니다. 기본 인증만 지원됩니다. 이 필드를 SecureString으로 표시합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 개인 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니요 |

> [!IMPORTANT]
> 데이터베이스 서버인 [Azure SQL Database 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)을 구성하여 [Azure 서비스가 서버에 액세스할 수 있도록](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) 해야 합니다. 또한 데이터 팩터리 자체 호스트 Integration Runtime을 사용하여 온-프레미스 데이터 원본을 포함한 Azure 외부에서 Azure SQL Database로 데이터를 복사하는 경우 데이터를 Azure SQL Database로 보내는 컴퓨터에 대한 적절한 IP 주소 범위를 구성합니다.

**예제:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 집합 문서를 참조하세요. 이 섹션에서는 Azure SQL Database 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Azure SQL Database 간에 데이터를 복사하려면 데이터 집합의 type 속성을 **AzureSqlTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성을 **AzureSqlTable**로 설정해야 합니다. | 예 |
| tableName |연결된 서비스가 참조하는 Azure SQL Database 인스턴스에서 테이블 또는 보기의 이름입니다. | 예 |

**예제:**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure SQL Database 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-sql-database-as-source"></a>원본으로 Azure SQL Database

Azure SQL Database에서 데이터를 복사하려면 복사 작업의 원본 형식을 **SqlSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 **SqlSource**로 설정해야 합니다. | 예 |
| SqlReaderQuery |사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `select * from MyTable`. |아니요 |
| sqlReaderStoredProcedureName |원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. |아니요 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |아니요 |

**주의할 사항:**

- **sqlReaderQuery** 이 SqlSource에 지정되면 복사 작업은 데이터를 가져오는 Azure SQL Database 원본에 대해 이 쿼리를 실행합니다. 또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).
- "sqlReaderQuery" 또는 "sqlReaderStoredProcedureName" 중 하나를 지정하지 않으면 JSON 데이터 집합의 "structure" 섹션에 정의된 열은 쿼리(`select column1, column2 from mytable`)를 생성하는 데 사용되어 Azure SQL Database에 대해 실행합니다. 데이터 집합 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.
- **sqlReaderStoredProcedureName**을 사용하는 경우에도 데이터 집합 JSON에서 더미 **tableName** 속성을 지정해야 합니다.

**예: SQL 쿼리 사용**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

### <a name="azure-sql-database-as-sink"></a>싱크로 Azure SQL Database

Azure SQL Database에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SqlSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 형식 속성은 **SqlSink**로 설정해야 합니다. | 예 |
| writeBatchSize |버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10000) |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br/>허용되는 값은 시간 범위입니다. 예: “00:30:00”(30분). |아니요 |
| sqlWriterStoredProcedureName |대상 테이블에 대한 데이터 Upsert(업데이트/삽입)를 수행하는 저장 프로시저의 이름입니다. |아니요 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |아니요 |
| sqlWriterTableType |저장 프로시저에 사용할 테이블 형식 이름을 지정합니다. 복사 작업을 사용하면 이 테이블 형식으로 임시 테이블에서 사용할 수 있는 데이터를 이동시킵니다. 그러면 저장 프로시저 코드가 복사되는 데이터를 기존 데이터와 병합할 수 있습니다. |아니요 |
| preCopyScript |각 실행 시 Azure SQL Database에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. |아니요 |

> [!TIP]
> Azure SQL Database로 데이터를 복사할 때 복사 작업은 기본적으로 싱크 테이블에 데이터를 추가합니다. UPSERT 또는 추가 비즈니스 논리를 수행하려면 SqlSink에서 저장 프로시저를 사용합니다. 자세한 내용은 [SQL 싱크에 대한 저장 프로시저 호출](#invoking-stored-procedure-for-sql-sink)을 참조하세요.

**예 1: 데이터 추가**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

**예 2: upsert에 대해 복사 중 저장 프로시저 호출**

자세한 내용은 [SQL 싱크에 대한 저장 프로시저 호출](#invoking-stored-procedure-for-sql-sink)을 참조하세요.

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

## <a name="identity-columns-in-the-target-database"></a>대상 데이터베이스의 ID 열

이 섹션에서는 ID 열이 없는 원본 테이블에서 ID 열이 있는 대상 테이블로 데이터를 복사하는 예제를 제공합니다.

**원본 테이블:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**대상 테이블:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

대상 테이블에 ID 열이 있는지 확인합니다.

**원본 데이터 집합 JSON 정의**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**대상 데이터 집합 JSON 정의**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

원본 테이블과 대상 테이블의 스키마가 서로 다릅니다(대상에 ID가 포함된 추가 열이 있음). 이 시나리오에서는 ID 열을 포함하지 않는 대상 데이터 집합 정의에서 **structure** 속성을 지정해야 합니다.

## <a name="invoke-stored-procedure-from-sql-sink"></a>SQL 싱크에서 저장된 프로시저 호출

Azure SQL Database로 데이터를 복사할 때 사용자 지정 저장 프로시저를 구성하고 추가 매개 변수로 호출할 수 있습니다.

기본 제공 메커니즘이 용도에 적합하지 않을 때는 저장 프로시저를 사용할 수 있습니다. 보통은 대상 테이블에서 원본 데이터의 최종 삽입 전에 upsert(insert + update, 삽입 + 업데이트) 또는 추가 처리(열 합병, 추가 값 검색, 여러 테이블에 삽입 등)를 수행해야 할 때 저장 프로시저를 사용합니다.

다음 샘플에서는 저장 프로시저를 사용하여 Azure SQL Database 내 테이블에 간단한 삽입을 수행하는 방법을 보여줍니다. 각기 ProfileID, State, Category 등의 세 개 열을 갖는 입력 데이터와 싱크 "Marketing" 테이블을 가정해 보겠습니다. “ProfileID” 열을 기준으로 upsert를 수행하고 특정 범주에 대해서만 적용합니다.

**출력 데이터 집합**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

복사 작업의 "SqlSink" 섹션을 다음과 같이 정의합니다.

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

데이터베이스에서 "SqlWriterStoredProcedureName"과 동일한 이름으로 저장 프로시저를 정의합니다. 지정된 원본에서 입력 데이터를 처리하고 출력 테이블에 병합합니다. 저장 프로시저의 매개 변수 이름은 데이터 집합에 정의된 "tableName"과 동일해야 합니다.

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
      VALUES (source.ProfileID, source.State, source.Category)
END
```

데이터베이스에서 sqlWriterTableType과 동일한 이름으로 테이블 형식을 정의합니다. 테이블 형식의 스키마가 입력 데이터에서 반환된 스키마와 동일해야 합니다.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

저장된 프로시저 기능은 [테이블 값 매개 변수](https://msdn.microsoft.com/library/bb675163.aspx)을 이용합니다.

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database에 대한 데이터 형식 매핑

Azure SQL Database 간에 데이터를 복사하는 경우 Azure SQL Database 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Azure SQL Database 데이터 형식 | 데이터 팩터리 중간 데이터 형식 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| 10진수 |10진수 |
| FILESTREAM 특성(varbinary(max)) |Byte[] |
| Float |Double |
| 이미지 |Byte[] |
| int |Int32 |
| money |10진수 |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |10진수 |
| nvarchar |String, Char[] |
| real |단일 |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |10진수 |
| sql_variant |개체 * |
| 텍스트 |String, Char[] |
| 실시간 |timespan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.