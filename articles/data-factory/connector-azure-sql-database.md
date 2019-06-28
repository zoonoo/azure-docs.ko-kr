---
title: Data Factory를 사용하여 Azure SQL Database에서 데이터 복사 | Microsoft Docs
description: Data Factory를 사용하여 지원되는 원본 데이터 저장소에서 Azure SQL Database로 또는 SQL Database에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 90126a607065bdb10e2ff81ce6ab52809ecc3f36
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273751"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure SQL Database 간 데이터 복사
> [!div class="op_single_selector" title1="Data Factory 서비스 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-azure-sql-connector.md)
> * [현재 버전](connector-azure-sql-database.md)

이 문서에서는 Azure SQL Database에서 데이터를 복사 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure SQL Database 커넥터는 다음 작업에 대 한 지원 됩니다.

- [복사 활동](copy-activity-overview.md) 사용 하 여 [원본/싱크 행렬 지원](copy-activity-overview.md) 테이블
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

특히 이 Azure SQL Database 커넥터는 다음 함수를 지원합니다.

- 서비스 주체 또는 Azure 리소스에 대한 관리 ID를 통해 SQL 인증 및 Azure AD(Azure Active Directory) 애플리케이션 토큰 인증을 사용하여 데이터를 복사합니다.
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용하여 데이터를 검색합니다.
- 싱크로, 대상 테이블에 데이터를 추가하거나 복사하는 동안 사용자 지정 논리로 저장 프로시저를 호출합니다.

>[!NOTE]
>Azure SQL Database **[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current)** 이제이 커넥터에서 지원 되지 않습니다. 이 문제를 해결 하려면 사용할 수 있습니다 [제네릭 ODBC 커넥터](connector-odbc.md) 및 자체 호스팅 통합 런타임을 통해 SQL Server ODBC 드라이버. 따릅니다 [이 지침은](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) ODBC 드라이버 다운로드 하 고 연결 문자열 구성으로 합니다.

> [!IMPORTANT]
> Azure Data Factory Integration Runtime을 사용하여 데이터를 복사하는 경우, Azure 서비스가 서버에 액세스할 수 있도록 [Azure SQL Server 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)을 구성합니다.
> 자체 호스팅 통합 런타임을 사용하여 데이터를 복사하는 경우, 적절한 IP 범위를 허용하도록 Azure SQL Server 방화벽을 구성합니다. 이 범위에는 Azure SQL Database에 연결하는 데 사용되는 머신 IP가 포함됩니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure SQL Database 커넥터와 관련된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure SQL Database 연결된 서비스에 대해 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **type** 속성은 **AzureSqlDatabase**로 설정해야 합니다. | 예 |
| connectionString | Azure SQL Database 인스턴스에 연결하는 데 필요한 정보를 **connectionString** 속성에 대해 지정합니다. <br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. 암호/서비스 주체 키를 Azure Key Vault에 넣고, SQL 인증인 경우 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 표 아래의 JSON 예제 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 데이터 저장소가 프라이빗 네트워크에 있는 경우, 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 아니오 |

다른 인증 형식의 경우, 각각의 필수 조건 및 JSON 샘플에 대한 다음 섹션을 참조하세요.

- [SQL 인증](#sql-authentication)
- [Azure AD 애플리케이션 토큰 인증: 서비스 주체](#service-principal-authentication)
- [Azure AD 애플리케이션 토큰 인증: Azure 리소스에 대한 관리 ID](#managed-identity)

>[!TIP]
>"UserErrorFailedToConnectToSqlServer" 오류 코드 및 "데이터베이스에 대한 세션 제한이 XXX이고 이에 도달했습니다."와 같은 메시지가 있는 오류가 발생하면 연결 문자열에 `Pooling=false`를 추가하고 다시 시도하세요.

### <a name="sql-authentication"></a>SQL 인증

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL 인증을 사용하는 연결된 서비스 예제

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

**Azure Key Vault의 암호:** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

### <a name="service-principal-authentication"></a>서비스 주체 인증

서비스 주체 기반의 Azure AD 애플리케이션 토큰 인증을 사용하려면 다음 단계를 따르세요.

1. Azure Portal에서 **[Azure Active Directory 애플리케이션을 만듭니다](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** . 애플리케이션 이름 및 연결된 서비스를 정의하는 다음 값을 적어 둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. Azure Portal에서 Azure SQL Server에 대한 **[Azure Active Directory 관리자를 프로비전](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** (아직 하지 않은 경우)합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이어야 하지만 서비스 주체일 수는 없습니다. 이 단계가 수행되면, 이후 단계에서 Azure AD ID를 사용하여 서비스 주체에 대한 포함된 데이터베이스 사용자를 만들 수 있습니다.

3. 서비스 주체에 대한 **[포함된 데이터베이스 사용자를 만듭니다](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** . 최소한 ALTER ANY USER 권한이 있는 Azure AD ID를 사용하여 SSMS 등의 도구를 통해 데이터를 복사하려는 데이터베이스에 연결합니다. 다음 T-SQL을 실행합니다. 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 일반적으로 SQL 사용자나 기타 사용자에 대해 수행하듯이 **서비스 주체에 필요한 권한을 부여**합니다. 다음 코드를 실행 하거나 더 많은 옵션을 참조 하십시오 [여기](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)합니다.

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Azure Data Factory에서 **Azure SQL Database 연결된 서비스를 구성**합니다.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>서비스 주체 인증을 사용하는 연결된 서비스 예제

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
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

### <a name="managed-identity"></a>Azure 리소스 인증용 관리 ID

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. Azure SQL Database 인증에 대 한 관리 되는이 id를 사용할 수 있습니다. 지정된 팩터리는 이 ID를 사용하여 데이터베이스의 데이터에 액세스하고 복사할 수 있습니다.

관리 되는 id 인증을 사용 하려면 다음이 단계를 수행 합니다.

1. Azure Portal에서 Azure SQL Server에 대한 **[Azure Active Directory 관리자를 프로비전](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** (아직 하지 않은 경우)합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹일 수 있습니다. 관리 되는 id 관리자 역할을 사용 하 여 그룹에 부여 하면 3-4 단계를 건너뜁니다. 관리자는 데이터베이스에 대한 모든 권한을 갖습니다.

2. **[포함 된 데이터베이스 사용자를 만듭니다](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  Data Factory 관리 서비스 Id에 대 한 합니다. 최소한 ALTER ANY USER 권한이 있는 Azure AD ID를 사용하여 SSMS 등의 도구를 통해 데이터를 복사하려는 데이터베이스에 연결합니다. 다음 T-SQL을 실행합니다. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Data Factory 관리 서비스 Id에 필요한 권한을 부여** 하듯이 일반적으로 SQL 사용자 및 다른 사용자에 대 한 합니다. 다음 코드를 실행 하거나 더 많은 옵션을 참조 하십시오 [여기](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)합니다.

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Azure Data Factory에서 **Azure SQL Database 연결된 서비스를 구성**합니다.

**예제:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) 문서를 참조하세요. 이 섹션에서는 Azure SQL Database 데이터 세트에서 지원하는 속성 목록을 제공합니다.

또는 Azure SQL Database로 데이터를 복사 하는 다음 속성이 지원 됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 **type** 속성을 **AzureSqlTable**로 설정해야 합니다. | 예 |
| tableName | 연결된 서비스가 참조하는 Azure SQL Database 인스턴스의 테이블 또는 뷰 이름입니다. | 원본에는 아니요이고 싱크에는 예입니다 |

#### <a name="dataset-properties-example"></a>데이터 세트 속성 예제

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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure SQL Database 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database가 원본인 경우

Azure SQL Database에서 데이터를 복사하려면 복사 작업 원본의 **type** 속성을 **SqlSource**로 설정합니다. 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성을 **SqlSource**로 설정해야 합니다. | 예 |
| sqlReaderQuery | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `select * from MyTable`. | 아니오 |
| sqlReaderStoredProcedureName | 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. | 아니오 |
| storedProcedureParameters | 저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 아니오 |

### <a name="points-to-note"></a>주의할 사항

- **sqlReaderQuery**가 **SqlSource**에 대해 지정된 경우, 복사 작업은 Azure SQL Database 원본에 대해 이 쿼리를 실행하여 데이터를 가져옵니다. 또는 저장 프로시저를 지정할 수 있습니다. 저장 프로시저가 매개 변수를 사용하는 경우, **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정합니다.
- **sqlReaderQuery** 또는 **sqlReaderStoredProcedureName** 중 하나를 지정하지 않는 경우, 데이터 세트 JSON의 **structure** 섹션에 정의된 열이 쿼리()를 생성하는 데 사용됩니다. `select column1, column2 from mytable`은 Azure SQL Database에 대해 실행됩니다. 데이터 세트 정의에 **structure**가 없는 경우, 테이블에서 모든 열이 선택됩니다.

#### <a name="sql-query-example"></a>SQL 쿼리 예제

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

#### <a name="stored-procedure-example"></a>저장 프로시저 예제

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

### <a name="stored-procedure-definition"></a>저장 프로시저 정의

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database가 싱크인 경우

> [!TIP]
> 지원 되는 쓰기 동작, 구성 및에서 모범 사례에 자세히 알아보세요 [모범 사례를 Azure SQL Database로 데이터를 로드 하기 위한](#best-practice-for-loading-data-into-azure-sql-database)합니다.

Azure SQL Database에 데이터를 복사하려면 복사 작업 싱크의 **type** 속성을 **SqlSink**로 설정합니다. 복사 작업 **sink** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 **type** 속성은 **SqlSink**로 설정해야 합니다. | 예 |
| writeBatchSize | SQL 테이블에 삽입 하는 행 수가 **일괄 처리당**합니다.<br/> 허용되는 값은 **정수**(행 수)입니다. 기본적으로 Data Factory는 행의 크기에 따라 적절 한 일괄 처리 크기를 동적으로 결정 합니다. | 아닙니다. |
| writeBatchTimeout | 시간 초과되기 전에 배치 삽입 작업을 완료하기 위한 대기 시간입니다.<br/> 허용되는 값은 **시간 범위**입니다. 예제: “00:30:00”(30분) | 아닙니다. |
| preCopyScript | Azure SQL Database에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 복사 실행당 한 번만 호출됩니다. 이 속성을 사용하여 미리 로드된 데이터를 정리합니다. | 아닙니다. |
| sqlWriterStoredProcedureName | 원본 데이터를 대상 테이블에 적용하는 방법을 정의하는 저장 프로시저의 이름입니다. <br/>이 저장 프로시저는 **배치마다 호출**됩니다. 만 한 번 실행 하 고 원본 데이터를 사용 하 여 예를 들어, 삭제 하거나 자를 마십시오 관련이 없는 하는 작업을 사용 하 여는 `preCopyScript` 속성입니다. | 아닙니다. |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 및 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 아닙니다. |
| sqlWriterTableType | 저장 프로시저에 사용할 테이블 형식 이름을 지정합니다. 복사 작업을 사용하면 이동 중인 데이터를 이 테이블 형식의 임시 테이블에서 볼 수 있습니다. 그러면 저장 프로시저 코드가 복사되는 데이터를 기존 데이터와 병합할 수 있습니다. | 아닙니다. |

**예제 1: 데이터 추가**

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

**복사 중 저장된 프로시저를 호출 하는 예 2:**

자세한 내용은 [SQL 싱크에서 저장 프로시저 호출](#invoking-stored-procedure-for-sql-sink)을 참조하세요.

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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Azure SQL Database로 데이터를 로드 하기 위한 모범 사례

Azure SQL Database로 데이터를 복사할 때 다른 쓰기 동작이 필요할 수 있습니다.

- **[추가](#append-data)** : 원본 데이터에만 새 레코드가;에 있는
- **[Upsert](#upsert-data)** : 내 원본 데이터에 삽입 및 업데이트
- **[덮어쓰기](#overwrite-entire-table)** : 각 시간 전체 차원 테이블을 다시 로드 하려고 하는 경우
- **[사용자 지정 논리를 사용 하 여 작성할](#write-data-with-custom-logic)** : 대상 테이블에 최종 삽입 전에 추가 처리가 필요합니다.

참조 된 ADF 및 모범 사례를 구성 하는 방법에 각각 섹션입니다.

### <a name="append-data"></a>데이터를 추가 합니다.

이 Azure SQL Database 싱크 연결선의 기본 동작이 며 ADF 수행 **대량 삽입** 테이블에 효율적으로 작성 합니다. 단순히 소스를 구성할 수 있으며 그에 따라 싱크 복사 활동의 합니다.

### <a name="upsert-data"></a>데이터 Upsert

**I 옵션** (있는 경우 특히 큰 데이터 복사를 제안): 합니다 **대부분의 성능이 뛰어난 방법을** upsert를 수행 하는 다음: 

- 첫째, 활용을 [데이터베이스 범위 임시 테이블](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) 대량 복사 작업을 사용 하 여 모든 레코드를 로드 하 합니다. 데이터베이스에 대해 작업 범위 임시 테이블 로깅되지 않습니다, 초에서 수백만 개의 레코드를 로드할 수 있습니다.
- 저장 프로시저 작업을 적용 하는 ADF에서 실행을 [병합](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) (또는 삽입/업데이트) 문 및 temp 왕복을 줄이는 모든을 수행 하는 소스를 단일 트랜잭션으로 삽입 하거나 업데이트 하는 대로 테이블 및 작업 기록 사용 합니다. 저장 프로시저 작업의 끝 다음 upsert 주기에 대 한 준비로 임시 테이블을 자를 수 있습니다. 

예를 들어 Azure Data Factory를 만들 수 있습니다 사용 하는 파이프라인을 **복사 활동** 연계 하 여를 **저장 프로시저 작업** 성공 합니다. 이전의 복사 데이터 원본 저장소에서 Azure SQL Database 임시 테이블 예를 들어 " **##UpsertTempTable**" 데이터 집합의 테이블 이름으로 다음 두 번째 호출 대상에 대 한 임시 테이블에서 원본 데이터를 병합 하는 저장 프로시저 테이블 및 임시 테이블을 정리 합니다.

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

에 설명 된 것과 유사 [Upsert 데이터](#upsert-data) 섹션에서는 대상 테이블에 원본 데이터의 최종 삽입 전에 추가 처리를 적용 해야 할 수 있습니다는) 대규모 데이터베이스 범위가 지정 된 임시 테이블에 로드 한 다음 호출 저장된 프로시저 또는 b) 복사 중 저장된 프로시저를 호출 합니다.

## <a name="invoking-stored-procedure-for-sql-sink"></a> SQL 싱크에서 저장된 프로시저 호출

Azure SQL Database로 데이터를 복사할 때 구성 수 및 추가 매개 변수를 사용 하 여 사용자 지정 저장된 프로시저를 호출 해야 합니다.

> [!TIP]
> 저장된 프로시저를 호출 하는 데이터에서 행 대규모 복사본에 대 한 추천 하지 않습니다는 대량 작업을 대신 처리 합니다. 자세히 알아보세요 [모범 사례를 Azure SQL Database로 데이터를 로드 하기 위한](#best-practice-for-loading-data-into-azure-sql-database)합니다.

기본 제공 메커니즘이 용도 적합 하지 않습니다 하는 경우에 저장된 프로시저를 사용할 수 있습니다 예를 들어 대상 테이블에 원본 데이터의 최종 삽입 전에 추가 처리를 적용 합니다. 몇 가지 추가 처리 예제로 열 병합, 추가 값 조회, 두 개 이상의 테이블에 삽입 등이 있습니다.

다음 샘플에서는 저장 프로시저를 사용하여 Azure SQL Database의 테이블에 upsert(업데이트/삽입)를 수행하는 방법을 보여 줍니다. 각기 다음과 같은 세 개 열을 갖는 입력 데이터와 싱크 **Marketing** 테이블을 가정해 보겠습니다. **ProfileID**, **State** 및 **Category**. **ProfileID** 열을 기준으로 upsert(업데이트/삽입)를 수행하고 특정 범주에 대해서만 적용합니다.

**출력 데이터 집합:** "tableName" 저장된 프로시저 (저장된 프로시저 스크립트 아래 참조)의 동일한 테이블 형식 매개 변수 이름 이어야 합니다.

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

데이터베이스에서 **sqlWriterTableType**과 동일한 이름의 테이블 형식을 정의합니다. 테이블 형식의 스키마가 입력 데이터에서 반환된 스키마와 동일해야 합니다.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

저장된 프로시저 기능은 [테이블 값 매개 변수](https://msdn.microsoft.com/library/bb675163.aspx)을 이용합니다.

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

자세한 내용은 [소스 변환](data-flow-source.md) 하 고 [변환 싱크](data-flow-sink.md) 매핑 데이터 흐름에서.

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database에 대한 데이터 형식 매핑

Azure SQL Database에서 데이터를 복사하는 경우, Azure SQL Database 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Azure SQL Database 데이터 형식 | Data Factory 중간 데이터 형식 |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> 데이터 형식이 10진수 중간 형식으로 매핑되는 경우 ADF는 현재 최대 28 자릿수의 데이터를 지원합니다. 28보다 큰 자릿수의 데이터가 있는 경우 SQL 쿼리에서 문자열로 변환하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md##supported-data-stores-and-formats)을 참조하세요.
