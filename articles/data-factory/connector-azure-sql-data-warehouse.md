---
title: Azure 시냅스 분석에서 데이터 복사 및 변환
description: Azure 시냅스 애널리틱스에서 데이터를 복사하고 데이터 팩터리를 사용하여 Azure 시냅스 분석에서 데이터를 변환하는 방법을 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/25/2020
ms.openlocfilehash: 950bbc17af920f104f31af4d324f5546ff29217e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257957"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 Azure 시냅스 분석(이전의 Azure SQL 데이터 웨어하우스)에서 데이터 복사 및 변환 

> [!div class="op_single_selector" title1="사용 하는 데이터 팩터리 서비스의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [현재 버전](connector-azure-sql-data-warehouse.md)

이 문서에서는 Azure Data Factory에서 복사 활동을 사용하여 Azure 시냅스 분석에서 데이터를 복사하고 데이터 흐름을 사용하여 Azure Data Lake Storage Gen2의 데이터를 변환하는 방법을 설명합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Synapse 분석 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 행렬](copy-activity-overview.md) 테이블로 [활동 복사](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 활동](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

복사 활동의 경우 이 Azure Synapse Analytics 커넥터는 다음 기능을 지원합니다.

- 서비스 주체 또는 Azure 리소스에 대한 관리 ID를 통해 SQL 인증 및 Azure AD(Azure Active Directory) 애플리케이션 토큰 인증을 사용하여 데이터를 복사합니다.
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용하여 데이터를 검색합니다.
- 싱크로 [PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 또는 [COPY 문(미리](#use-copy-statement) 보기) 또는 대량 삽입을 사용하여 데이터를 로드합니다. 더 나은 복사 성능을 위해 PolyBase 또는 COPY 문(미리 보기)을 권장합니다.

> [!IMPORTANT]
> Azure Data Factory 통합 런타임을 사용하여 데이터를 복사하는 경우 Azure 서비스가 서버에 액세스할 수 있도록 [Azure SQL 서버 방화벽을](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) 구성합니다.
> 자체 호스팅 통합 런타임을 사용하여 데이터를 복사하는 경우, 적절한 IP 범위를 허용하도록 Azure SQL Server 방화벽을 구성합니다. 이 범위에는 Azure Synapse 분석에 연결하는 데 사용되는 컴퓨터의 IP가 포함됩니다.

## <a name="get-started"></a>시작

> [!TIP]
> 최상의 성능을 얻으려면 PolyBase를 사용하여 Azure 시냅스 분석에 데이터를 로드합니다. [PolyBase 사용으로 Azure 시냅스 분석 섹션에 데이터를 로드하는](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 세부 정보가 있습니다. 사용 사례가 있는 연습은 [Azure 데이터 팩터리에서 15분 미만의 Azure Synapse 분석에 1TB 로드를](load-azure-sql-data-warehouse.md)참조하십시오.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Synapse Analytics 커넥터와 관련된 데이터 팩터리 엔터티를 정의하는 속성에 대한 세부 정보를 제공합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Synapse 분석 링크된 서비스에 대해 다음 속성이 지원됩니다.

| 속성            | 설명                                                  | 필수                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | 형식 속성은 **AzureSqlDW로**설정해야 합니다.             | yes                                                          |
| connectionString    | **연결String** 속성에 대 한 Azure Synapse 분석 인스턴스에 연결 하는 데 필요한 정보를 지정 합니다. <br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. 암호/서비스 주체 키를 Azure Key Vault에 넣고, SQL 인증인 경우 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 표 아래의 JSON 예제 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | yes                                                          |
| servicePrincipalId  | 애플리케이션의 클라이언트 ID를 지정합니다.                         | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| tenant              | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| connectVia          | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 통합 런타임을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 예                                                           |

다른 인증 형식의 경우, 각각의 필수 조건 및 JSON 샘플에 대한 다음 섹션을 참조하세요.

- [SQL 인증](#sql-authentication)
- Azure AD 응용 프로그램 토큰 인증: [서비스 주체](#service-principal-authentication)
- Azure AD 응용 프로그램 토큰 인증: [Azure 리소스에 대 한 관리되는 ID](#managed-identity)

>[!TIP]
>"UserErrorFailedToConnectToSqlServer" 오류 코드 및 "데이터베이스에 대한 세션 제한이 XXX이고 이에 도달했습니다."와 같은 메시지가 있는 오류가 발생하면 연결 문자열에 `Pooling=false`를 추가하고 다시 시도하세요.

### <a name="sql-authentication"></a>SQL 인증

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL 인증을 사용하는 연결된 서비스 예제

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault의 암호:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>서비스 주체 인증

서비스 주체 기반의 Azure AD 애플리케이션 토큰 인증을 사용하려면 다음 단계를 따르세요.

1. Azure Portal에서 **[Azure Active Directory 애플리케이션을 만듭니다](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**. 애플리케이션 이름 및 연결된 서비스를 정의하는 다음 값을 적어 둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. 아직 수행하지 않은 경우 Azure Portal에서 Azure SQL Server에 대해 **[Azure Active Directory 관리자를 프로비저닝](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** 합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이 될 수 있습니다. 관리 ID를 가진 그룹에 관리자 역할을 부여하는 경우 3단계 및 4단계를 건너뛰세요. 관리자는 데이터베이스에 대한 전체 액세스 권한을 가집니다.

3. 서비스 주체에 대한 **[포함된 데이터베이스 사용자를 만듭니다](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**. 최소한 ALTER ANY USER 권한이 있는 Azure AD ID를 사용하여 SSMS 등의 도구를 통해 데이터를 복사하려는 데이터 웨어하우스에 연결합니다. 다음 T-SQL을 실행합니다.
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 일반적으로 SQL 사용자나 기타 사용자에 대해 수행하듯이 **서비스 주체에 필요한 권한을 부여**합니다. 다음 코드를 실행하거나 [여기에서](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)더 많은 옵션을 참조하십시오. PolyBase를 사용하여 데이터를 로드하려면 [필요한 데이터베이스 사용 권한을](#required-database-permission)알아봅니다.

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Azure 데이터 팩터리에서 Azure 시냅스 분석 연결 서비스를 구성합니다.**


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>서비스 주체 인증을 사용하는 연결된 서비스 예제

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Azure 리소스 인증에 대한 관리되는 ID

데이터 팩터리는 특정 팩터리를 나타내는 [Azure 리소스에 대한 관리 ID](data-factory-service-identity.md)와 연결할 수 있습니다. 이 관리되는 ID를 Azure Synapse 분석 인증에 사용할 수 있습니다. 지정된 팩터리는 이 ID를 사용하여 데이터 웨어하우스의 데이터에 액세스하고 복사할 수 있습니다.

관리되는 ID 인증을 사용하려면 다음 단계를 따르십시오.

1. 아직 수행하지 않은 경우 Azure Portal에서 Azure SQL Server에 대해 **[Azure Active Directory 관리자를 프로비저닝](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** 합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이 될 수 있습니다. 관리 ID를 가진 그룹에 관리자 역할을 부여하는 경우 3단계 및 4단계를 건너뛰세요. 관리자는 데이터베이스에 대한 전체 액세스 권한을 가집니다.

2. 데이터 팩터리 관리 ID에 포함된 **[데이터베이스 사용자를 만듭니다.](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** 최소한 ALTER ANY USER 권한이 있는 Azure AD ID를 사용하여 SSMS 등의 도구를 통해 데이터를 복사하려는 데이터 웨어하우스에 연결합니다. 다음 T-SQL을 실행합니다. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. 일반적으로 SQL 사용자 및 다른 사용자에 대해 수행하는 것처럼 **데이터 팩터리 관리 ID에 필요한 사용 권한이 필요합니다.** 다음 코드를 실행하거나 [여기에서](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)더 많은 옵션을 참조하십시오. PolyBase를 사용하여 데이터를 로드하려면 [필요한 데이터베이스 사용 권한을](#required-database-permission)알아봅니다.

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Azure 데이터 팩터리에서 Azure 시냅스 분석 연결 서비스를 구성합니다.**

**예제:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

Azure Synapse 분석 데이터 집합에 대해 다음 속성이 지원됩니다.

| 속성  | 설명                                                  | 필수                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 데이터 집합의 **형식** 속성은 **AzureSqlDWTable로**설정해야 합니다. | yes                         |
| 스키마 | 스키마의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| 테이블 | 테이블/뷰의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| tableName | 스키마가 있는 테이블/보기의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 `schema` 경우 `table`및 를 사용합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

#### <a name="dataset-properties-example"></a>데이터 세트 속성 예제

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

## <a name="copy-activity-properties"></a>복사 활동 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Synapse 분석 소스 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-synapse-analytics-as-the-source"></a>원본으로 Azure 시냅스 분석

Azure Synapse Analytics의 데이터를 복사하려면 복사 활동 소스의 **형식** 속성을 **SqlDWSource**로 설정합니다. 다음 속성은 활동 복사 **원본** 섹션에서 지원됩니다.

| 속성                     | 설명                                                  | 필수 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 복사 활동 소스의 **형식** 속성은 **SqlDWSource**로 설정해야 합니다. | yes      |
| SqlReaderQuery               | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `select * from MyTable`. | 예       |
| sqlReaderStoredProcedureName | 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. | 예       |
| storedProcedureParameters    | 저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 예       |
| isolationLevel | SQL 원본에 대한 트랜잭션 잠금 동작을 지정합니다. 허용된 값은 **다음과** 같습니다: ReadCommitted(기본값), **ReadUncommitted,** **반복 읽기,** **직렬화 가능**, **스냅샷**. 자세한 내용은 [이 문서를](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) 참조하십시오. | 예 |

**예: SQL 쿼리 사용**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

**샘플 저장 프로시저:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>싱크로 Azure 시냅스 분석

Azure 데이터 팩터리는 SQL 데이터 웨어하우스에 데이터를 로드하는 세 가지 방법을 지원합니다.

![SQL DW 싱크 복사 옵션](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [폴리베이스 사용](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [COPY 명령문 사용(미리 보기)](#use-copy-statement)
- 벌크 인서트를 사용

데이터를 로드하는 가장 빠르고 확장 가능한 방법은 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 또는 [COPY 문(미리](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) 보기)을 통해서입니다.

Azure SQL Data Warehouse에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SqlDWSink**로 설정합니다. 다음 속성은 활동 복사 **싱크** 섹션에서 지원됩니다.

| 속성          | 설명                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | 복사 활동 싱크의 **형식** 속성은 **SqlDWSink로**설정해야 합니다. | yes                                           |
| allowPolyBase     | PolyBase를 사용하여 SQL 데이터 웨어하우스에 데이터를 로드할지 여부를 나타냅니다. `allowCopyCommand`둘 `allowPolyBase` 다 사실이 될 수 없습니다. <br/><br/>제약 조건 및 세부 정보는 [PolyBase를 사용하여 Azure SQL Data Warehouse로 데이터 로드](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 섹션을 참조하세요.<br/><br/>허용된 값은 **참** 및 False(기본값)입니다. **False** | 아니요.<br/>폴리베이스를 사용할 때 적용합니다.     |
| polyBaseSettings  | `allowPolybase` 속성이 **true로**설정될 때 지정할 수 있는 속성 그룹입니다. | 아니요.<br/>폴리베이스를 사용할 때 적용합니다. |
| 허용복사 명령 | [COPY 문(미리](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) 보기)을 사용하여 데이터를 SQL 데이터 웨어하우스에 로드할지 여부를 나타냅니다. `allowCopyCommand`둘 `allowPolyBase` 다 사실이 될 수 없습니다. <br/><br/>COPY 문을 사용하여 제약 조건 및 세부 정보에 대한 [Azure SQL 데이터 웨어하우스 섹션에 데이터를 로드합니다.](#use-copy-statement)<br/><br/>허용된 값은 **참** 및 False(기본값)입니다. **False** | 아니요.<br>COPY를 사용할 때 적용하십시오. |
| 카피명령설정 | 속성이 TRUE로 설정될 `allowCopyCommand` 때 지정할 수 있는 속성 그룹입니다. | 아니요.<br/>COPY를 사용할 때 적용하십시오. |
| writeBatchSize    | **일괄 처리당**SQL 테이블에 삽입할 행 수입니다.<br/><br/>허용되는 값은 **정수**(행 수)입니다. 기본적으로 Data Factory는 행 크기에 따라 적절한 일괄 처리 크기를 동적으로 결정합니다. | 아니요.<br/>벌크 인서트를 사용할 때 는 적용하십시오.     |
| writeBatchTimeout | 시간 초과되기 전에 배치 삽입 작업을 완료하기 위한 대기 시간입니다.<br/><br/>허용된 값은 **시간 범위입니다.** 예: “00:30:00”(30분). | 아니요.<br/>벌크 인서트를 사용할 때 는 적용하십시오.        |
| preCopyScript     | 각 실행 시 Azure SQL Data Warehouse에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리합니다. | 예                                            |
| 테이블 옵션 | 원본 스키마에 따라 싱크 테이블이 없는 경우 싱크 테이블을 자동으로 만들지 여부를 지정합니다. 준비된 복사본이 복사 활동에서 구성될 때 자동 테이블 만들기는 지원되지 않습니다. 허용된 값은 `none` 다음과 `autoCreate`같습니다(기본값) . |예 |
| 비활성화메트릭스컬렉션 | Data Factory는 복사 성능 최적화 및 권장 사항을 위해 SQL 데이터 웨어하우스 DW와 같은 메트릭을 수집합니다. 이 동작과 관련된 경우 `true` 해제하도록 지정합니다. | 아니요(기본값: `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>SQL Data Warehouse 싱크 예제

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드

[PolyBase를](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 사용하면 처리량이 높은 Azure Synapse Analytics에 많은 양의 데이터를 로드할 수 있습니다. 기본 BULKINSERT 메커니즘 대신 PolyBase를 사용하면 처리량이 훨씬 증가합니다. 사용 사례가 있는 연습은 [Azure Synapse 분석에 1TB 로드를](v1/data-factory-load-sql-data-warehouse.md)참조하십시오.

* 원본 데이터가 Azure **Blob, Azure Data Lake 저장소 Gen1 또는 Azure Data Lake Storage Gen2에**있고 **형식이 PolyBase 호환되는**경우 복사 활동을 사용하여 PolyBase를 직접 호출하여 Azure SQL Data 웨어하우스에서 데이터를 가져오도록 할 수 있습니다. 자세한 내용은 **[PolyBase를 사용하여 직접 복사](#direct-copy-by-using-polybase)** 를 참조하세요.
* 원본 데이터 저장소와 형식이 PolyBase에서 원래 지원되지 않는 경우, 대신 **[PolyBase를 사용한 준비된 복사](#staged-copy-by-using-polybase)** 기능을 사용합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. 자동으로 데이터를 PolyBase 호환 형식으로 변환하고 Azure Blob 저장소에 데이터를 저장한 다음 PolyBase를 호출하여 데이터를 SQL 데이터 웨어하우스에 로드합니다.

>[!TIP]
>[PolyBase 를 사용하는 모범 사례에 대해](#best-practices-for-using-polybase)자세히 알아보십시오.

다음 PolyBase 설정은 `polyBaseSettings` 복사 작업에서 지원됩니다.

| 속성          | 설명                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | 쿼리가 실패하기 전에 거부될 수 있는 행의 수 또는 백분율을 지정합니다.<br/><br/>[CREATE EXTERNAL TABLE(Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx)의 인수 섹션에서 PolyBase의 거부 옵션에 대해 자세히 알아봅니다. <br/><br/>허용되는 값은 0(기본값), 1, 2 등입니다. | 예                                            |
| rejectType        | **rejectValue** 옵션이 리터럴 값인지 또는 백분율인지를 지정합니다.<br/><br/>허용된 **Value** 값은 값(기본값) 및 **백분율입니다.** | 예                                            |
| rejectSampleValue | PolyBase가 거부된 행의 백분율을 다시 계산하기 전에 검색할 행 수를 결정합니다.<br/><br/>허용되는 값은 1, 2 등입니다. | **rejectType**이 **percentage**인 경우 예 |
| useTypeDefault    | PolyBase가 텍스트 파일에서 데이터를 검색할 경우 구분된 텍스트 파일에서 누락된 값을 처리하는 방법을 지정합니다.<br/><br/>[외부 파일 서식 만들기(Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)를 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다.<br/><br/>허용된 값은 **참** 및 False(기본값)입니다. **False**<br><br> | 예                                            |

### <a name="direct-copy-by-using-polybase"></a>PolyBase를 사용한 직접 복사

SQL 데이터 웨어하우스 PolyBase는 Azure Blob, Azure 데이터 레이크 저장소 Gen1 및 Azure 데이터 레이크 저장소 Gen2를 직접 지원합니다. 원본 데이터가 이 섹션에 설명된 기준을 충족하는 경우 PolyBase를 사용하여 원본 데이터 저장소에서 Azure Synapse Analytics로 직접 복사합니다. 조건을 충족하지 않는 경우, [PolyBase를 사용한 준비된 복사](#staged-copy-by-using-polybase)를 사용합니다.

> [!TIP]
> 데이터를 SQL 데이터 웨어하우스에 효율적으로 복사하려면 Azure Data Factory에서 자세히 [알아보면 SQL 데이터 웨어하우스를 사용하여 Data Lake Store를 사용할 때 데이터에서 통찰력을 보다 쉽고 편리하게 찾을](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)수 있습니다.

조건을 충족하지 않는 경우, Azure Data Factory는 설정을 확인한 후 데이터 이동을 위해 BULKINSERT 메커니즘으로 자동으로 대체됩니다.

1. **소스 연결 서비스는** 다음과 같은 유형 및 인증 방법을 사용합니다.

    | 지원되는 원본 데이터 저장소 유형                             | 지원되는 소스 인증 유형                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | 계정 키 인증, 관리 되는 ID 인증 |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | 서비스 주체 인증                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | 계정 키 인증, 관리 되는 ID 인증 |

    >[!IMPORTANT]
    >Azure 저장소가 VNet 서비스 끝점으로 구성된 경우 관리되는 ID 인증을 [Impact of using VNet Service Endpoints with Azure storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)사용해야 합니다. [Azure Blob-관리되는 ID 인증](connector-azure-blob-storage.md#managed-identity) 및 Azure Data Lake Storage [Gen2- 관리되는 ID 인증](connector-azure-data-lake-storage.md#managed-identity) 섹션에서 데이터 팩터리에서 필요한 구성을 알아봅니다.

2. **원본 데이터 형식은** 다음과 같은 구성으로 **Parquet,** **ORC**또는 **구분 된 텍스트입니다.**

   1. 폴더 경로에는 와일드카드 필터가 포함되어 있지 않습니다.
   2. 파일 이름이 비어 있거나 단일 파일을 가리킵니다. 복사 활동에 `*` `*.*`와일드카드 파일 이름을 지정하는 경우
   3. `rowDelimiter`**[ 기본값**, **\n**, **\r\n,** 또는 **\r**.
   4. `nullValue`기본값으로 남아 있거나 빈 문자열(""")으로 설정되며 **empty string** `treatEmptyAsNull` 기본값으로 남아 있거나 true로 설정됩니다.
   5. `encodingName`기본값으로 남아 있거나 **utf-8로**설정됩니다.
   6. `quoteChar`및 `escapeChar` `skipLineCount` 지정되지 않았습니다. PolyBase는 ADF에서와 같이 `firstRowAsHeader` 구성할 수 있는 헤더 행을 건너뜁니다.
   7. `compression`압축, **GZip**또는 **수축이**될 수 **없습니다.**

3. 원본이 폴더인 `recursive` 경우 복사 활동이 true로 설정되어야 합니다.

4. `wildcardFolderPath`및 `wildcardFilename` `modifiedDateTimeStart` `modifiedDateTimeEnd` `additionalColumns`

>[!NOTE]
>소스가 폴더인 경우 PolyBase는 폴더와 모든 하위 폴더에서 파일을 검색하고 파일 이름이 밑줄(_) 또는 마침표(.)로 시작되는 파일에서 데이터를 검색하지 [않습니다.](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2)

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>PolyBase를 사용한 준비된 복사

원본 데이터가 PolyBase와 기본적으로 호환되지 않는 경우 중간 스테이징 Azure Blob 저장소 인스턴스를 통해 데이터 복사를 사용하도록 설정합니다(Azure Premium 저장소일 수 있음). 이 경우 Azure Data Factory는 PolyBase의 데이터 형식 요구 사항을 충족하도록 데이터를 자동으로 변환합니다. 그런 다음 PolyBase를 호출하여 데이터를 SQL 데이터 웨어하우스에 로드합니다. 마지막으로, Blob Storage에서 임시 데이터를 정리합니다. 준비 Azure Blob Storage 인스턴스를 통해 데이터를 복사하는 방법에 대한 자세한 내용은 [준비된 복사](copy-activity-performance.md#staged-copy)를 참조하세요.

이 기능을 사용하려면 중간 Blob 저장소가 있는 Azure 저장소 계정을 참조하는 Azure [Blob Storage 링크된 서비스를](connector-azure-blob-storage.md#linked-service-properties) 만듭니다. 그런 다음 `enableStaging` `stagingSettings` 다음 코드에 표시된 대로 복사 활동에 대한 및 속성을 지정합니다.

>[!IMPORTANT]
>스테이징 Azure Storage가 VNet 서비스 끝점으로 구성된 경우 관리되는 ID [Impact of using VNet Service Endpoints with Azure storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)인증을 사용해야 합니다. [Azure Blob - 관리되는 ID 인증에서](connector-azure-blob-storage.md#managed-identity)데이터 팩터리에서 필요한 구성에 대해 알아봅니다.

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>PolyBase를 사용하는 모범 사례

다음 섹션에서는 [Azure Synapse 분석의 모범 사례에](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md)언급된 모범 사례 외에도 모범 사례를 제공합니다.

#### <a name="required-database-permission"></a>필수 데이터베이스 권한

PolyBase를 사용하려면 SQL Data Warehouse에 데이터를 로드하는 사용자에게 대상 데이터베이스에 대한 [“CONTROL” 권한](https://msdn.microsoft.com/library/ms191291.aspx)이 있어야 합니다. 한 가지 방법은 해당 사용자를 **db_owner** 역할의 구성원으로 추가하는 것입니다. [SQL Data Warehouse 개요](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)에서 작업 방법을 알아보세요.

#### <a name="row-size-and-data-type-limits"></a>행 크기 및 데이터 형식 한도

PolyBase는 1MB보다 작은 행으로 제한됩니다. VARCHR(MAX), NVARCHAR(MAX) 또는 VARBINARY(MAX)에 로드하는 데 사용할 수 없습니다. 자세한 내용은 [SQL Data Warehouse 서비스 용량 한도](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)를 참조하세요.

원본 데이터에 1MB보다 큰 행이 있는 경우, 원본 테이블을 여러 개의 작은 테이블로 수직 분할하는 것이 좋습니다. 각 행의 최대 크기가 한도를 초과하지 않는지 확인합니다. 그런 다음 PolyBase를 사용하여 더 작은 테이블을 로드하고 Azure 시냅스 분석에서 함께 병합할 수 있습니다.

또는 이러한 넓은 열이 있는 데이터의 경우 "PolyBase 허용" 설정을 해제하여 비 PolyBase를 사용하여 ADF를 사용하여 데이터를 로드할 수 있습니다.

#### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse 리소스 클래스

가능한 최고 수준까지 처리량을 높이려면 PolyBase를 통해 SQL Data Warehouse에 데이터를 로드하는 사용자에게 더 큰 리소스 클래스를 할당합니다.

#### <a name="polybase-troubleshooting"></a>PolyBase 문제 해결

**소수점 열로 로드**

원본 데이터가 텍스트 형식 또는 기타 PolyBase 호환되지 않는 저장소(준비된 복사본 및 PolyBase 사용)이고 SQL 데이터 웨어하우스 소수점 열에 로드할 빈 값이 포함되어 있는 경우 다음과 같은 오류가 발생할 수 있습니다.

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

해결 방법은 복사 활동 싱크 -> PolyBase 설정에서 **"사용 유형 기본**" 옵션(false)을 선택 취소하는 것입니다. ["USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)"는 PolyBase가 텍스트 파일에서 데이터를 검색할 때 구분된 텍스트 파일에서 누락된 값을 처리하는 방법을 지정하는 PolyBase 네이티브 구성입니다. 

**`tableName`Azure 시냅스 분석에서**

다음 표에서는 JSON 데이터 세트의 **tableName** 속성을 지정하는 방법에 대한 예를 제공합니다. 스키마 및 테이블 이름의 여러 조합을 보여 줍니다.

| DB 스키마 | 테이블 이름 | **tableName** JSON 속성               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable 또는 dbo.MyTable 또는 [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable 또는 [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] 또는 [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

다음 오류가 표시되는 경우, **tableName** 속성에 대해 지정한 값이 문제일 수 있습니다. **tableName** JSON 속성의 값을 지정하는 올바른 방법은 앞의 표를 참조하세요.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**기본값이 있는 열**

현재, Data Factory의 PolyBase 기능은 대상 테이블과 동일한 열 수만 허용합니다. 예를 들어, 네 개의 열이 있고 그 중 한 열이 기본값으로 정의된 테이블이 있다고 가정합니다. 이 경우, 입력 데이터에 네 개의 열이 있어야 합니다. 3열 입력 데이터 세트를 제공하면 다음 메시지와 비슷한 오류가 발생합니다.

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL 값은 특별한 형태의 기본값입니다. 열이 Null을 허용하는 경우, 해당 열에 대한 Blob의 입력 데이터가 비어 있을 수 있습니다. 그러나 입력 데이터 세트에서는 누락할 수 없습니다. PolyBase Azure 시냅스 분석에서 누락된 값에 대해 NULL을 삽입합니다.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>COPY 문을 사용하여 Azure SQL 데이터 웨어하우스에 데이터를 로드(미리 보기)

SQL 데이터 웨어하우스 [COPY 문(미리](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) 보기)은 **Azure Blob 및 Azure Data Lake 저장소 Gen2의**데이터 로드를 직접 지원합니다. 원본 데이터가 이 섹션에 설명된 기준을 충족하는 경우 ADF의 COPY 문을 사용하여 Azure SQL Data Warehouse에 데이터를 로드하도록 선택할 수 있습니다. Azure Data Factory는 설정을 확인하고 조건이 충족되지 않으면 복사 활동 실행에 실패합니다.

>[!NOTE]
>현재 데이터 팩터리는 아래에 언급된 COPY 문 호환 소스의 사본만 지원합니다.

COPY 문을 사용하면 다음 구성을 지원합니다.

1. **소스 링크 된 서비스 및 형식은** 다음과 같은 형식 및 인증 메서드와 함께 있습니다.

    | 지원되는 원본 데이터 저장소 유형                             | 지원되는 형식           | 지원되는 소스 인증 유형                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [구분된 텍스트](format-delimited-text.md)             | 계정 키 인증, 공유 액세스 서명 인증, 서비스 주체 인증, 관리되는 ID 인증 |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | 계정 키 인증, 공유 액세스 서명 인증 |
    | &nbsp;                                                       | [오크](format-orc.md)                        | 계정 키 인증, 공유 액세스 서명 인증 |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [구분된 텍스트](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[오크](format-orc.md) | 계정 키 인증, 서비스 주체 인증, 관리 ID 인증 |

    >[!IMPORTANT]
    >Azure 저장소가 VNet 서비스 끝점으로 구성된 경우 관리되는 ID 인증을 [Impact of using VNet Service Endpoints with Azure storage](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)사용해야 합니다. [Azure Blob-관리되는 ID 인증](connector-azure-blob-storage.md#managed-identity) 및 Azure Data Lake Storage [Gen2- 관리되는 ID 인증](connector-azure-data-lake-storage.md#managed-identity) 섹션에서 데이터 팩터리에서 필요한 구성을 알아봅니다.

2. 형식 설정은 다음과 같습니다.

   1. **마루의**경우 `compression` : **압축,** **스냅,** 또는 **GZip이**될 수 없습니다 .
   2. **ORC의** `compression` 경우 : 압축, **zlib**또는 **스냅이**될 수 **없습니다.**
   3. **구분된 텍스트의**경우 :
      1. `rowDelimiter`명시적으로 단일 **문자** 또는 **"\r\n"로**설정되어 있으며 기본값은 지원되지 않습니다.
      2. `nullValue`기본값으로 남아 있거나 빈 문자열("")으로 설정됩니다. **empty string**
      3. `encodingName`기본값으로 남아 있거나 **utf-8 또는 utf-16으로**설정됩니다.
      4. `escapeChar``quoteChar`과 같아야 하며 비어 있지 않아야 합니다.
      5. `skipLineCount`기본값으로 남아 있거나 0으로 설정됩니다.
      6. `compression`압축 또는 **GZip**이 될 수 **없습니다.**

3. 원본이 폴더인 `recursive` 경우 복사 활동이 true로 설정되어야 합니다.

4. `wildcardFolderPath`및 `wildcardFilename` `modifiedDateTimeStart` `modifiedDateTimeEnd` `additionalColumns`

다음 COPY 문 설정은 `allowCopyCommand` 복사 활동에서 지원됩니다.

| 속성          | 설명                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| 기본값 | SQL DW의 각 대상 열에 대한 기본값을 지정합니다.  속성의 기본값은 데이터 웨어하우스에 설정된 DEFAULT 제약 조건을 덮어쓰고 ID 열에는 기본값이 있을 수 없습니다. | 예 |
| 추가 옵션 | [COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)문의 "With" 절에서 SQL DW COPY 문으로 직접 전달되는 추가 옵션. COPY 문 요구 사항에 맞게 필요에 따라 값을 인용합니다. | 예 |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true, 
                "copyCommandSettings": {
                    "defaultValues": [ 
                        { 
                            "columnName": "col_string", 
                            "defaultValue": "DefaultStringValue" 
                        }
                    ],
                    "additionalOptions": { 
                        "MAXERRORS": "10000", 
                        "DATEFORMAT": "'ymd'" 
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```


## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.

## <a name="getmetadata-activity-properties"></a>GetMetadata 활동 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 활동을](control-flow-get-metadata-activity.md) 선택합니다. 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 대한 데이터 형식 매핑

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

매핑 데이터 흐름에서 데이터를 변환할 때 Azure Synapse Analytics에서 테이블을 읽고 쓸 수 있습니다. 자세한 내용은 매핑 데이터 흐름의 [소스 변환](data-flow-source.md) 및 [싱크 변환을](data-flow-sink.md) 참조하십시오.

### <a name="source-transformation"></a>소스 변환

Azure 시냅스 분석과 관련된 설정은 소스 변환의 **소스 옵션** 탭에서 사용할 수 있습니다. 

**입력:** 소스가 테이블에 ```Select * from <table-name>```해당하는지(와동등한)할지 또는 사용자 지정 SQL 쿼리를 입력할지 선택합니다.

**쿼리**: 입력 필드에서 쿼리를 선택하면 원본에 대한 SQL 쿼리를 입력합니다. 이 설정은 데이터 집합에서 선택한 모든 테이블을 재정의합니다. **Order By** 절은 여기에서 지원되지 않지만 전체 SELECT FROM 문을 설정할 수 있습니다. 사용자 정의 테이블 함수를 사용할 수도 있습니다. ***를 선택 * udfGetData()는** 테이블을 반환하는 SQL의 UDF입니다. 이 쿼리는 데이터 흐름에서 사용할 수 있는 원본 테이블을 생성합니다. 쿼리를 사용하면 테스트또는 조회를 위해 행을 줄일 수 있습니다. 

* SQL 예제:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**일괄 처리 크기**: 큰 데이터를 읽기에 청크하는 일괄 처리 크기를 입력합니다.

**격리 수준**: 매핑 데이터 흐름의 SQL 원본에 대한 기본값은 커밋되지 않은 읽기입니다. 여기서 격리 수준을 다음 값 중 하나로 변경할 수 있습니다.
* 커밋된 읽기
* 커밋되지 않은 읽기
* 반복 가능한 읽기
* 직렬화 가능
* 없음(격리 수준 무시)

![격리 수준](media/data-flow/isolationlevel.png "격리 수준")

### <a name="sink-transformation"></a>싱크 변환

Azure 시냅스 분석과 관련된 설정은 싱크 변환의 **설정** 탭에서 사용할 수 있습니다.

**업데이트 방법:** 데이터베이스 대상에서 허용되는 작업을 결정합니다. 기본값은 삽입만 허용하는 것입니다. 행을 업데이트, upsert 또는 삭제하려면 해당 작업에 대해 행에 태그를 붙이기 위해 행 변경이 필요합니다. 업데이트, upserts 및 deletes의 경우 변경할 행을 결정하려면 키 열 또는 열을 설정해야 합니다.

**테이블 작업:** 쓰기 전에 대상 테이블에서 모든 행을 다시 만들거나 제거할지 여부를 결정합니다.
* 없음: 테이블에 대한 작업이 수행되지 않습니다.
* 다시 만들기: 테이블이 삭제되고 다시 만들어집니다. 새 테이블을 동적으로 만드는 경우 필요합니다.
* 트런케이트: 대상 테이블의 모든 행이 제거됩니다.

**스테이징 사용:** Azure 시냅스 애널리틱스에 쓸 때 [PolyBase사용](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) 여부 결정

**일괄 처리 크기**: 각 버킷에 기록되는 행 수를 제어합니다. 일괄 처리 크기가 클수록 압축 및 메모리 최적화가 향상되지만 데이터를 캐싱할 때 메모리 부족 예외가 발생할 위험이 있습니다.

**SQL 스크립트 이전 및 게시**: Sink 데이터베이스에 데이터가 기록되기 전(사전 처리) 및 후(사후 처리) 데이터가 실행되는 다중 줄 SQL 스크립트를 입력합니다.

![SQL 처리 스크립트 이전 및 사후](media/data-flow/prepost1.png "SQL 처리 스크립트")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Azure 시냅스 분석을 위한 데이터 유형 매핑

Azure Synapse Analytics에서 또는 Azure Synapse Analytics로 데이터를 복사하면 Azure 시냅스 분석 데이터 형식에서 Azure Data Factory 중간 데이터 유형으로 다음 매핑이 사용됩니다. [스키마 및 데이터 유형 매핑을](copy-activity-schema-and-type-mapping.md) 참조하여 복사 활동이 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법을 알아봅니다.

>[!TIP]
>SQL DW 지원 데이터 형식에 대한 [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) 문서의 테이블 데이터 형식과 지원되지 않는 데이터 형식에 대한 해결 방법을 참조하십시오.

| Azure 시냅스 분석 데이터 유형    | Data Factory 중간 데이터 형식 |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | 부울                        |
| char                                  | String, Char[]                 |
| date                                  | DateTime                       |
| DateTime                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM 특성(varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| 이미지                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 활동별로 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식을](copy-activity-overview.md#supported-data-stores-and-formats)참조하십시오.
