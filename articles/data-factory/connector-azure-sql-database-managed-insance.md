---
title: Azure Data Factory를 사용하여 Azure SQL Database Managed Instance 간에 데이터 복사 | Microsoft Docs
description: Azure Data Factory를 사용하여 Azure SQL Database Managed Instance 간에 데이터를 이동하는 방법을 알아봅니다.
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
ms.openlocfilehash: 9208ceeb760bba97c12b23a1b6e5bdff7efc9020
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274825"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure SQL Database Managed Instance 간에 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure SQL Database Managed Instance 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure SQL Database Managed Instance에서 지원되는 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Managed Instance에 복사할 수도 있습니다. 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Azure SQL Database Managed Instance 커넥터는 다음을 지원합니다.

- SQL 인증을 사용 하 여 데이터를 복사 합니다.
- 쿼리 또는 저장 프로시저를 사용하여 데이터 검색(원본)
- 싱크로, 복사 중에 대상 테이블에 데이터를 추가하거나 사용자 지정 논리를 사용하여 저장 프로시저 호출(싱크)

>[!NOTE]
>Azure SQL Database Managed Instance **[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current)** 이제이 커넥터에서 지원 되지 않습니다. 이 문제를 해결 하려면 사용할 수 있습니다 [제네릭 ODBC 커넥터](connector-odbc.md) 및 자체 호스팅 통합 런타임을 통해 SQL Server ODBC 드라이버. 따릅니다 [이 지침은](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) ODBC 드라이버 다운로드 하 고 연결 문자열 구성으로 합니다.

>[!NOTE]
>서비스 주체와 관리 되는 id 인증 현재 지원 되지 않습니다 즉시 사용할 수 있도록 계획 및이 커넥터에서. 문제를 해결 하려면 현재 Azure SQL Database 커넥터 및 수동으로 관리 되는 인스턴스 서버를 지정할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

Azure SQL Database Managed Instance 액세스할  **[공용 끝점](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)** 를 관리 하는 ADF Azure IR.를 사용할 수 있습니다 뿐만 아니라 공용 엔드포인트를 사용 하도록 설정 해도 ADF에 따라 데이터베이스에 연결할 수 있도록 네트워크 보안 그룹에서 공용 끝점 트래픽을 허용 하는지 확인 [이 지침은](../sql-database/sql-database-managed-instance-public-endpoint-configure.md)합니다.

Azure SQL Database Managed Instance 액세스할 **개인 끝점**설정는 [자체 호스팅 통합 런타임](create-self-hosted-integration-runtime.md) 데이터베이스에 액세스할 수 있는 합니다. 관리 되는 인스턴스와 같은 가상 네트워크에서 자체 호스팅된 integration runtime을 프로 비전 하는 경우에 통합 런타임 컴퓨터에 다른 관리 되는 인스턴스 서브넷에 있는지 확인 합니다. Managed Instance와 다른 가상 네트워크에서 자체 호스팅 통합 런타임을 프로비전하는 경우에는 가상 네트워크 피어링 또는 가상 네트워크 간 연결을 사용할 수 있습니다. 자세한 내용은 [애플리케이션을 Azure SQL Database Managed Instance에 연결](../sql-database/sql-database-managed-instance-connect-app.md)을 참조하세요.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure SQL Database Managed Instance 커넥터 관련 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure SQL Database Managed Instance 연결된 서비스에서 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **SqlServer**로 설정해야 합니다. | 예. |
| connectionString |이 속성에는 SQL 인증을 사용 하 여 관리 되는 인스턴스에 연결 하는 데 필요한 connectionString 정보를 지정 합니다. 자세한 내용은 다음 예제를 참조하세요. <br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. 암호를 Azure Key Vault에 넣고, SQL 인증인 경우 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 표 아래의 JSON 예제 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예. |
| connectVia | 이 [Integration Runtime](concepts-integration-runtime.md)은 데이터 저장소에 연결하는 데 사용됩니다. (공용 엔드포인트가 하 고 ADF 액세스를 허용 하는 관리 되는 인스턴스) 경우 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예. |

**예제 1: SQL 인증을 사용 하 여** 기본 포트는 1433입니다. SQL 관리 되는 인스턴스를 공용 끝점을 사용 하 여 사용 하는 경우 포트 3342 명시적으로 지정 합니다.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제 2: Azure Key Vault에 암호를 사용 하 여 SQL 인증을 사용 하 여** 기본 포트는 1433입니다. SQL 관리 되는 인스턴스를 공용 끝점을 사용 하 여 사용 하는 경우 포트 3342 명시적으로 지정 합니다.

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 세트 문서를 참조하세요. 이 섹션에서는 Azure SQL Database Managed Instance 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

에 Azure SQL Database Managed Instance에서 데이터를 복사 하려면 다음 속성이 지원 됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **SqlServerTable**로 설정해야 합니다. | 예. |
| tableName |이 속성은 연결된 서비스가 참조하는 데이터베이스 인스턴스의 테이블이나 뷰 이름입니다. | 값은 원본의 경우 No이고 싱크의 경우 Yes입니다. |

**예제**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure SQL Database Managed Instance 원본 및 싱크에서 지원하는 속성의 목록을 제공합니다.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Azure SQL Database Managed Instance(원본)

Azure SQL Database Managed Instance에서 데이터를 복사하려면 복사 작업의 원본 형식을 **SqlSource**로 설정합니다. 복사 작업 source 섹션에서 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **SqlSource**로 설정해야 합니다. | 예. |
| sqlReaderQuery |이 속성은 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예는 `select * from MyTable`입니다. |아니요. |
| sqlReaderStoredProcedureName |이 속성은 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. |아니요. |
| storedProcedureParameters |저장 프로시저용 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대/소문자는 저장 프로시저 매개변수의 이름 및 대/소문자와 일치해야 합니다. |아니요. |

다음 사항에 유의하세요.

- **SqlSource**에 대해 **sqlReaderQuery**가 지정되어 있으면 복사 작업은 Managed Instance 원본에 대해 이 쿼리를 실행하여 데이터를 가져옵니다. 저장 프로시저가 매개 변수를 사용하는 경우에는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수도 있습니다.
- **sqlReaderQuery** 또는 **sqlReaderStoredProcedureName** 중 하나를 지정하지 않는 경우, 데이터 세트 JSON의 "structure" 섹션에 정의된 열이 쿼리를 생성하는 데 사용됩니다. `select column1, column2 from mytable` 쿼리는 Managed Instance에 대해 실행됩니다. 데이터 세트 정의에 "structure"가 없는 경우 테이블에서 모든 열이 선택됩니다.

**예제: SQL 쿼리 사용**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

**예제: 저장 프로시저 사용**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Azure SQL Database Managed Instance(싱크)

> [!TIP]
> 지원 되는 쓰기 동작, 구성 및에서 모범 사례에 자세히 알아보세요 [모범 사례 데이터를 Azure SQL Database Managed Instance 로드](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)합니다.

Azure SQL Database Managed Instance에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SqlSink**로 설정합니다. 복사 작업 sink 섹션에서 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 type 속성은 **SqlSink**로 설정해야 합니다. | 예. |
| writeBatchSize |SQL 테이블에 삽입 하는 행 수가 **일괄 처리당**합니다.<br/>허용되는 값은 행 수에 해당하는 정수입니다. 기본적으로 Data Factory는 행의 크기에 따라 적절 한 일괄 처리 크기를 동적으로 결정 합니다.  |아닙니다. |
| writeBatchTimeout |이 속성은 시간이 초과되기 전에 완료하려는 배치 삽입 작업의 대기 시간을 지정합니다.<br/>허용되는 값은 시간 범위입니다. 예를 들어 "00:30:00"(30분)을 지정할 수 있습니다. |아니요. |
| preCopyScript |이 속성은 Managed Instance에 데이터를 쓰기 전에 실행할 복사 작업용 SQL 쿼리를 지정하며 복사 실행당 한 번만 호출됩니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. |아니요. |
| sqlWriterStoredProcedureName |원본 데이터를 대상 테이블에 적용하는 방법을 정의하는 저장 프로시저의 이름입니다. <br/>이 저장 프로시저는 *배치마다 호출*됩니다. 한 번만 실행되며 원본 데이터와 아무런 관련이 없는 작업(예: 삭제/자르기)을 수행하려는 경우 `preCopyScript` 속성을 사용합니다. |아니요. |
| storedProcedureParameters |저장 프로시저에 사용되는 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대/소문자는 저장 프로시저 매개변수의 이름 및 대/소문자와 일치해야 합니다. |아니요. |
| sqlWriterTableType |이 속성은 저장 프로시저에 사용할 테이블 형식 이름을 지정합니다. 복사 작업에서는 이동 중인 데이터를 이 테이블 형식의 임시 테이블에서 사용할 수 있습니다. 그러면 저장 프로시저 코드가 복사 중인 데이터를 기존 데이터와 병합할 수 있습니다. |아니요. |

**예제 1: 데이터 추가**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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

자세한 내용은 [SQL 싱크에서 저장 프로시저 호출](#invoking-stored-procedure-for-sql-sink)을 참조하세요.

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance로 데이터를 로드 하기 위한 모범 사례

Azure SQL Database Managed Instance로 데이터를 복사할 때 다른 쓰기 동작이 필요할 수 있습니다.

- **[추가](#append-data)** : 원본 데이터에만 새 레코드가;에 있는
- **[Upsert](#upsert-data)** : 내 원본 데이터에 삽입 및 업데이트
- **[덮어쓰기](#overwrite-entire-table)** : 각 시간 전체 차원 테이블을 다시 로드 하려고 하는 경우
- **[사용자 지정 논리를 사용 하 여 작성할](#write-data-with-custom-logic)** : 대상 테이블에 최종 삽입 전에 추가 처리가 필요합니다.

참조 된 ADF 및 모범 사례를 구성 하는 방법에 각각 섹션입니다.

### <a name="append-data"></a>데이터를 추가 합니다.

이 Azure SQL Database Managed Instance 싱크 연결선의 기본 동작이 며 ADF 수행 **대량 삽입** 테이블에 효율적으로 작성 합니다. 단순히 소스를 구성할 수 있으며 그에 따라 싱크 복사 활동의 합니다.

### <a name="upsert-data"></a>데이터 Upsert

**I 옵션** (있는 경우 특히 큰 데이터 복사를 제안): 합니다 **대부분의 성능이 뛰어난 방법을** upsert를 수행 하는 다음: 

- 첫째, 활용을 [임시 테이블](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) 대량 복사 작업을 사용 하 여 모든 레코드를 로드 하 합니다. 임시 테이블에 대 한 작업은 기록 되지 대로 초에서 수백만 개의 레코드를 로드할 수 있습니다.
- 저장 프로시저 작업을 적용 하는 ADF에서 실행을 [병합](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (또는 삽입/업데이트) 문 및 temp 왕복을 줄이는 모든을 수행 하는 소스를 단일 트랜잭션으로 삽입 하거나 업데이트 하는 대로 테이블 및 작업 기록 사용 합니다. 저장 프로시저 작업의 끝 다음 upsert 주기에 대 한 준비로 임시 테이블을 자를 수 있습니다. 

예를 들어 Azure Data Factory를 만들 수 있습니다 사용 하는 파이프라인을 **복사 활동** 연계 하 여를 **저장 프로시저 작업** 성공 합니다. 이전의 복사 데이터 원본 저장소에서 임시 테이블을 예를 들어 " **##UpsertTempTable**" 데이터 집합의 테이블 이름으로 다음 두 번째 호출 임시 테이블의 원본 데이터를 대상 테이블로 병합 하 고 정리 하는 저장 프로시저 임시 테이블입니다.

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

## <a name="invoking-stored-procedure-for-sql-sink"></a> SQL 싱크에서 저장 프로시저 호출

Azure SQL Database Managed Instance로 데이터를 복사할 때 구성 수 및 추가 매개 변수를 사용 하 여 사용자 지정 저장된 프로시저를 호출 해야 합니다.

> [!TIP]
> 저장된 프로시저를 호출 하는 데이터에서 행 대규모 복사본에 대 한 추천 하지 않습니다는 대량 작업을 대신 처리 합니다. 자세히 알아보세요 [모범 사례 데이터를 Azure SQL Database Managed Instance 로드](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)합니다.

기본 제공 메커니즘이 용도 적합 하지 않습니다 하는 경우에 저장된 프로시저를 사용할 수 있습니다 예를 들어 대상 테이블에 원본 데이터의 최종 삽입 전에 추가 처리를 적용 합니다. 몇 가지 추가 처리 예제로 열 병합, 추가 값 조회, 두 개 이상의 테이블에 삽입 등이 있습니다.

다음 샘플에서는 저장 프로시저를 사용하여 SQL Server 데이터베이스 내 테이블에 간단한 삽입을 수행하는 방법을 보여줍니다. 각기 다음과 같은 세 개 열을 갖는 입력 데이터와 싱크 **Marketing** 테이블을 가정해 보겠습니다. **ProfileID**, **State** 및 **Category**. **ProfileID** 열을 기준으로 upsert(업데이트/삽입)를 수행하고 특정 범주에 대해서만 적용합니다.

**출력 데이터 집합:** "tableName" 저장된 프로시저 (저장된 프로시저 스크립트 아래 참조)의 동일한 테이블 형식 매개 변수 이름 이어야 합니다.

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

데이터베이스에서 sqlWriterTableType과 동일한 이름으로 테이블 형식을 정의합니다. 테이블 형식의 스키마는 입력 데이터에서 반환된 스키마와 같아야 합니다.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

저장 프로시저 기능은 [테이블 반환 매개 변수](https://msdn.microsoft.com/library/bb675163.aspx)을 활용합니다.

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에 대한 데이터 형식 매핑

Azure SQL Database Managed Instance 간에 데이터를 복사할 때는 Managed Instance 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로의 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Azure SQL Database Managed Instance 데이터 형식 | Azure Data Factory 중간 데이터 형식 |
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
> 10진수 중간 형식으로 매핑되는 데이터 형식의 경우 Azure Data Factory는 현재 최대 28자리의 데이터를 지원합니다. 자릿수가 28자리를 초과하는 데이터가 있으면 SQL 쿼리에서 문자열로 변환하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
