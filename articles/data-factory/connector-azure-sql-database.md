---
title: Azure SQL 데이터베이스에서 데이터 복사 및 변환
description: Azure SQL Database에서 데이터를 복사하고 Azure 데이터 팩터리를 사용하여 Azure SQL 데이터베이스에서 데이터를 변환하는 방법을 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 52928b9a4d77a99f3d8b160713c7b4a7cade2d4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238764"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 Azure SQL 데이터베이스에서 데이터 복사 및 변환

> [!div class="op_single_selector" title1="사용 하는 Azure 데이터 팩터리의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-azure-sql-connector.md)
> * [현재 버전](connector-azure-sql-database.md)

이 문서에서는 Azure 데이터 팩터리에서 복사 활동 사용 하 여 Azure SQL 데이터베이스에서 데이터를 복사 하 고 데이터 흐름을 사용 하 여 Azure SQL 데이터베이스에서 데이터를 변환 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure SQL Database 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 행렬](copy-activity-overview.md) 테이블로 [활동 복사](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 활동](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

복사 활동의 경우 이 Azure SQL Database 커넥터는 다음 기능을 지원합니다.

- SQL 인증 및 Azure Active Directory(Azure AD) 응용 프로그램 토큰 인증을 Azure 리소스에 대한 서비스 주체 또는 관리되는 ID로 사용하여 데이터를 복사합니다.
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용하여 데이터를 검색합니다.
- 싱크로, 대상 테이블에 데이터를 적용하거나 복사 하는 동안 사용자 지정 논리와 저장 된 프로시저를 호출 합니다.

>[!NOTE]
>Azure SQL Database [항상 암호화된](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) 이 커넥터에서 지원되지 않습니다. 해결하려면 자체 호스팅 통합 런타임을 통해 [일반 ODBC 커넥터와](connector-odbc.md) SQL Server ODBC 드라이버를 사용할 수 있습니다. ODBC 드라이버 다운로드 및 연결 문자열 구성을 사용 하 [고이 지침을](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) 따르십시오.

> [!IMPORTANT]
> Azure Data Factory 통합 런타임을 사용하여 데이터를 복사하는 경우 Azure 서비스가 서버에 액세스할 수 있도록 [Azure SQL Server 방화벽을](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) 구성합니다.
> 자체 호스팅 통합 런타임을 사용하여 데이터를 복사하는 경우 적절한 IP 범위를 허용하도록 Azure SQL Server 방화벽을 구성합니다. 이 범위에는 Azure SQL 데이터베이스에 연결하는 데 사용되는 컴퓨터의 IP가 포함됩니다.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure SQL Database 커넥터와 관련된 Azure Data Factory 엔터티를 정의하는 데 사용되는 속성에 대한 세부 정보를 제공합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure SQL Database 연결된 서비스에 대해 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **type** 속성은 **AzureSqlDatabase**로 설정해야 합니다. | yes |
| connectionString | **연결String** 속성에 대 한 Azure SQL Database 인스턴스에 연결 하는 데 필요한 정보를 지정 합니다. <br/>Azure 키 자격 증명 모음에 암호 또는 서비스 주체 키를 넣을 수도 있습니다. SQL 인증인 경우 연결 `password` 문자열에서 구성을 가져옵니다. 자세한 내용은 Azure Key Vault의 테이블 및 [스토어 자격 증명 다음의](store-credentials-in-key-vault.md)JSON 예제를 참조하십시오. | yes |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예. 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString으로** 표시하여 Azure 데이터 팩터리에 안전하게 저장하거나 [Azure 키 자격 증명 모음에 저장된 비밀을 참조합니다.](store-credentials-in-key-vault.md) | 예. 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 |
| tenant | 응용 프로그램이 있는 도메인 이름 또는 테넌트 ID와 같은 테넌트 정보를 지정합니다. Azure 포털의 오른쪽 위 모서리에 마우스를 가져가서 검색합니다. | 예. 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 |
| connectVia | 이 [Integration Runtime](concepts-integration-runtime.md)은 데이터 저장소에 연결하는 데 사용됩니다. 데이터 저장소가 개인 네트워크에 있는 경우 Azure 통합 런타임 또는 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. | 예 |

다른 인증 형식의 경우, 각각의 필수 조건 및 JSON 샘플에 대한 다음 섹션을 참조하세요.

- [SQL 인증](#sql-authentication)
- [Azure AD 애플리케이션 토큰 인증: 서비스 주체](#service-principal-authentication)
- [Azure AD 애플리케이션 토큰 인증: Azure 리소스용 관리 ID](#managed-identity)

>[!TIP]
>"UserErrorFailedToConnectToServer"라는 오류 코드와 "데이터베이스의 세션 제한이 XXX이고 도달했습니다"와 같은 메시지가 있는 `Pooling=false` 경우 연결 문자열에 추가하고 다시 시도하십시오.

### <a name="sql-authentication"></a>SQL 인증

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL 인증을 사용하는 연결된 서비스 예제

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

**Azure 키 볼트의 암호** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

1. Azure Portal에서 [Azure Active Directory 애플리케이션을 만듭니다](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). 애플리케이션 이름 및 연결된 서비스를 정의하는 다음 값을 적어 둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. 아직 수행하지 않은 경우 Azure SQL Server에 Azure [Active Directory 관리자를 프로비전합니다.](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이어야 하지만 서비스 주체일 수는 없습니다. 이 단계가 수행되면, 이후 단계에서 Azure AD ID를 사용하여 서비스 주체에 대한 포함된 데이터베이스 사용자를 만들 수 있습니다.

3. 서비스 주체에 대한 [포함된 데이터베이스 사용자를 만듭니다](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities). SQL Server 관리 Studio와 같은 도구를 사용하여 데이터를 복사하려는 데이터베이스에 연결하여 최소한 사용자 권한을 변경하는 Azure AD ID를 사용합니다. 다음 T-SQL을 실행합니다. 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 일반적으로 SQL 사용자나 기타 사용자에 대해 수행하듯이 서비스 주체에 필요한 권한을 부여합니다. 다음 코드를 실행합니다. 자세한 옵션은 [이 문서](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)를 참조하세요.

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Azure Data Factory에서 Azure SQL Database 연결된 서비스를 구성합니다.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>서비스 주체 인증을 사용하는 연결된 서비스 예제

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. 이 관리되는 ID를 Azure SQL Database 인증에 사용할 수 있습니다. 지정된 팩터리는 이 ID를 사용하여 데이터베이스에 액세스하고 해당 데이터베이스에 대해 데이터를 복사할 수 있습니다.

관리되는 ID 인증을 사용하려면 다음 단계를 따르십시오.

1. 아직 수행하지 않은 경우 Azure SQL Server에 Azure [Active Directory 관리자를 프로비전합니다.](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹일 수 있습니다. 관리 ID를 가진 그룹에 관리자 역할을 부여하는 경우 3단계 및 4단계를 건너뛰세요. 관리자는 데이터베이스에 대한 전체 액세스 권한을 가수 있습니다.

2. Azure 데이터 팩터리 관리 ID에 대해 [포함된 데이터베이스 사용자를 만듭니다.](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) SQL Server 관리 Studio와 같은 도구를 사용하여 데이터를 복사하려는 데이터베이스에 연결하여 최소한 사용자 권한을 변경하는 Azure AD ID를 사용합니다. 다음 T-SQL을 실행합니다. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. 일반적으로 SQL 사용자 및 다른 사용자에 대해 수행하는 것처럼 데이터 팩터리 관리 ID에 필요한 사용 권한을 부여합니다. 다음 코드를 실행합니다. 자세한 옵션은 [이 문서](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)를 참조하세요.

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Azure Data Factory에서 Azure SQL Database 연결된 서비스를 구성합니다.

**예제**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

데이터 집합을 정의하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)을 참조하십시오. 

Azure SQL Database 데이터 집합에 대해 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 **type** 속성을 **AzureSqlTable**로 설정해야 합니다. | yes |
| 스키마 | 스키마의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| 테이블 | 테이블/뷰의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| tableName | 스키마가 있는 테이블/보기의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 `schema` 경우 `table`및 를 사용합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

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
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 이 섹션에서는 Azure SQL Database 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database가 원본인 경우

Azure SQL Database에서 데이터를 복사하려면 복사 활동 **원본** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 원본의 **형식** 속성을 **AzureSqlSource**로 설정해야 합니다. "SqlSource" 형식은 이전 버전과의 호환성을 위해 계속 지원됩니다. | yes |
| SqlReaderQuery | 이 속성은 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제는 `select * from MyTable`입니다. | 예 |
| sqlReaderStoredProcedureName | 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. | 예 |
| storedProcedureParameters | 저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름과 대/소문자는 저장된 프로시저 매개 변수의 이름과 대/소문자를 일치해야 합니다. | 예 |
| isolationLevel | SQL 원본에 대한 트랜잭션 잠금 동작을 지정합니다. 허용된 값은 **다음과** 같습니다: ReadCommitted(기본값), **ReadUncommitted,** **반복 읽기,** **직렬화 가능**, **스냅샷**. 자세한 내용은 [이 문서를](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) 참조하십시오. | 예 |

**주의할 사항:**

- **azureSqlSource에**대해 **sqlReaderQuery를** 지정하면 복사 활동이 Azure SQL Database 원본에 대해 이 쿼리를 실행하여 데이터를 가져옵니다. 저장 프로시저가 매개 변수를 사용하는 경우에는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수도 있습니다.
- **sqlReaderQuery** 또는 **sqlReader저장프로시내Name을**지정하지 않으면 데이터 집합 JSON의 "구조" 섹션에 정의된 열이 쿼리를 생성하는 데 사용됩니다. 쿼리는 `select column1, column2 from mytable` Azure SQL Database에 대해 실행됩니다. 데이터 세트 정의에 "structure"가 없는 경우 테이블에서 모든 열이 선택됩니다.

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
                "type": "AzureSqlSource",
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
                "type": "AzureSqlSource",
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
> [Azure SQL Database에 데이터를 로드하는 모범 사례에서](#best-practice-for-loading-data-into-azure-sql-database)지원되는 쓰기 동작, 구성 및 모범 사례에 대해 자세히 알아봅니다.

Azure SQL Database에 데이터를 복사하려면 복사 활동 **싱크** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 싱크의 **형식** 속성을 **AzureSqlSink로**설정해야 합니다. "SqlSink" 형식은 이전 버전과의 호환성을 위해 계속 지원됩니다. | yes |
| writeBatchSize | *일괄 처리당*SQL 테이블에 삽입할 행 수입니다.<br/> 허용되는 값은 **정수**(행 수)입니다. 기본적으로 Azure Data Factory는 행 크기에 따라 적절한 일괄 처리 크기를 동적으로 결정합니다. | 예 |
| writeBatchTimeout | 시간 초과되기 전에 배치 삽입 작업을 완료하기 위한 대기 시간입니다.<br/> 허용된 값은 **시간 범위입니다.** 예를 들어 "00:30:00"(30분)입니다. | 예 |
| preCopyScript | Azure SQL Database에 데이터를 작성하기 전에 실행할 복사 활동에 대한 SQL 쿼리를 지정합니다. 복사 실행당 한 번만 호출됩니다. 이 속성을 사용하여 미리 로드된 데이터를 정리합니다. | 예 |
| sqlWriterStoredProcedureName | 원본 데이터를 대상 테이블에 적용하는 방법을 정의하는 저장 프로시저의 이름입니다. <br/>이 저장 프로시저는 *배치마다 호출*됩니다. 한 번만 실행되고 원본 데이터와 아무 관련이 없는 작업의 경우(예: 삭제 `preCopyScript` 또는 실행)는 속성을 사용합니다. | 예 |
| 저장프로시저 테이블유형매개 변수 이름 |저장 프로시저에 지정된 테이블 유형의 매개 변수 이름입니다.  |예 |
| sqlWriterTableType |저장 프로시저에 사용할 테이블 유형 이름입니다. 복사 작업에서는 이동 중인 데이터를 이 테이블 형식의 임시 테이블에서 사용할 수 있습니다. 그러면 저장 프로시저 코드가 복사 중인 데이터를 기존 데이터와 병합할 수 있습니다. |예 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 및 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 예 |
| 테이블 옵션 | 원본 스키마에 따라 싱크 테이블이 없는 경우 싱크 테이블을 자동으로 만들지 여부를 지정합니다. 싱크가 저장 프로시저를 지정하거나 준비된 복사본이 복사 작업에서 구성될 때 자동 테이블 만들기가 지원되지 않습니다. 허용된 값은 `none` 다음과 `autoCreate`같습니다(기본값) . |예 |
| 비활성화메트릭스컬렉션 | Data Factory는 복사 성능 최적화 및 권장 사항을 위해 Azure SQL Database DT와 같은 메트릭을 수집합니다. 이 동작과 관련된 경우 `true` 해제하도록 지정합니다. | 아니요(기본값: `false`) |

**예 1: 데이터 부속**

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
                "type": "AzureSqlSink",
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
                "type": "AzureSqlSink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Azure SQL 데이터베이스에 데이터를 로드하는 모범 사례

Azure SQL Database에 데이터를 복사할 때 다른 쓰기 동작이 필요할 수 있습니다.

- [부속](#append-data): 원본 데이터에 새 레코드만 있습니다.
- [Upsert](#upsert-data): 원본 데이터에 삽입과 업데이트가 모두 있습니다.
- [덮어쓰기](#overwrite-the-entire-table): 매번 전체 차원 테이블을 다시 로드하려고 합니다.
- [사용자 지정 논리로 쓰기](#write-data-with-custom-logic): 대상 테이블에 최종 삽입하기 전에 추가 처리가 필요합니다.

Azure 데이터 팩터리에서 구성하는 방법과 모범 사례에 대한 각 섹션을 참조하십시오.

### <a name="append-data"></a>데이터 추가

데이터 부화는 이 Azure SQL Database 싱크 커넥터의 기본 동작입니다. Azure Data Factory는 테이블에 효율적으로 쓰기 위해 대량 삽입을 수행합니다. 복사 활동에서 소스와 싱크를 구성할 수 있습니다.

### <a name="upsert-data"></a>데이터 Upsert

**옵션 1:** 복사할 데이터가 많은 경우 다음 방법을 사용하여 upsert를 수행합니다. 

- 먼저 데이터베이스 [범위가 조정된 임시 테이블을](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) 사용하여 복사 작업을 사용하여 모든 레코드를 대량 로드합니다. 데이터베이스 범위의 임시 테이블에 대한 작업은 기록되지 않으므로 몇 초 만에 수백만 개의 레코드를 로드할 수 있습니다.
- Azure 데이터 팩터리에서 저장 프로시저 활동을 실행하여 [병합](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) 또는 INSERT/UPDATE 문을 적용합니다. 임시 테이블을 소스로 사용하여 모든 업데이트 또는 삽입을 단일 트랜잭션으로 수행합니다. 이렇게 하면 왕복 및 로그 작업 수가 줄어듭니다. 저장 프로시저 작업이 끝나면 임시 테이블을 잘리면 다음 upsert 주기에 대비할 수 있습니다.

예를 들어 Azure Data Factory에서 **저장 프로시저 활동과**연결된 **복사 활동이** 있는 파이프라인을 만들 수 있습니다. 이전은 원본 저장소의 데이터를 Azure SQL Database 임시 테이블(예: **##UpsertTempTable)에**데이터 집합의 테이블 이름으로 복사합니다. 그런 다음 후자는 임시 테이블의 원본 데이터를 대상 테이블로 병합하고 임시 테이블을 정리하는 저장 프로시저를 호출합니다.

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

- 데이터베이스 범위의 임시 테이블에 로드한 다음 저장 프로시저를 호출합니다. 
- 복사 하는 동안 저장 프로시저를 호출 합니다.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> SQL 싱크에서 저장 프로시저 호출

Azure SQL Database에 데이터를 복사할 때 추가 매개 변수를 사용하여 사용자 지정 저장 프로시저를 구성하고 호출할 수도 있습니다. 저장 프로시저 기능은 [테이블 값 매개 변수를 활용합니다.](https://msdn.microsoft.com/library/bb675163.aspx)

> [!TIP]
> 저장 프로시저를 호출하면 대량 작업을 사용하는 대신 데이터 행을 행별로 처리하므로 대규모 복사본에는 권장하지 않습니다. [Azure SQL 데이터베이스에 데이터를 로드하는 모범 사례에서](#best-practice-for-loading-data-into-azure-sql-database)자세히 알아봅니다.

기본 제공 복사 메커니즘이 용도에 적합하지 않은 경우, 저장 프로시저를 사용할 수 있습니다. 예를 들어 원본 데이터를 대상 테이블에 최종 삽입하기 전에 추가 처리를 적용하려는 경우를 들 수 있습니다. 몇 가지 추가 처리 예제는 열을 병합하고, 추가 값을 조회하고, 둘 이상의 테이블에 삽입하려는 경우입니다.

다음 샘플에서는 저장 프로시저를 사용하여 Azure SQL Database의 테이블에 upsert(업데이트/삽입)를 수행하는 방법을 보여 줍니다. 입력 데이터와 싱크 **마케팅** 테이블에 각각 **ProfileID,** **상태**및 **범주의**세 개의 열이 있다고 가정합니다. **ProfileID** 열을 기반으로 upsert를 수행하 고 "ProductA"라는 특정 범주에만 적용 합니다.

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
        "type": "AzureSqlSink",
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

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

매핑 데이터 흐름에서 데이터를 변환할 때 Azure SQL Database에서 테이블을 읽고 쓸 수 있습니다. 자세한 내용은 매핑 데이터 흐름의 [소스 변환](data-flow-source.md) 및 [싱크 변환을](data-flow-sink.md) 참조하십시오.

### <a name="source-transformation"></a>소스 변환

Azure SQL 데이터베이스와 관련된 설정은 원본 변환의 **소스 옵션** 탭에서 사용할 수 있습니다. 

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

Azure SQL 데이터베이스와 관련된 설정은 싱크 변환의 **설정** 탭에서 사용할 수 있습니다.

**업데이트 방법:** 데이터베이스 대상에서 허용되는 작업을 결정합니다. 기본값은 삽입만 허용하는 것입니다. 행을 업데이트, upsert 또는 삭제하려면 해당 작업에 대해 행에 태그를 붙이기 위해 행 변경이 필요합니다. 업데이트, upserts 및 deletes의 경우 변경할 행을 결정하려면 키 열 또는 열을 설정해야 합니다.

![키 열](media/data-flow/keycolumn.png "키 열")

여기에서 키로 선택한 열 이름은 ADF에서 후속 업데이트의 일부로 사용되며, upsert, 삭제됩니다. 따라서 싱크 매핑에 있는 열을 선택 해야 합니다. 이 키 열에 값을 쓰지 않으려면 "키 열 쓰기 건너뛰기"를 클릭합니다.

**테이블 작업:** 쓰기 전에 대상 테이블에서 모든 행을 다시 만들거나 제거할지 여부를 결정합니다.
* 없음: 테이블에 대한 작업이 수행되지 않습니다.
* 다시 만들기: 테이블이 삭제되고 다시 만들어집니다. 새 테이블을 동적으로 만드는 경우 필요합니다.
* 트런케이트: 대상 테이블의 모든 행이 제거됩니다.

**일괄 처리 크기**: 각 버킷에 기록되는 행 수를 제어합니다. 일괄 처리 크기가 클수록 압축 및 메모리 최적화가 향상되지만 데이터를 캐싱할 때 메모리 부족 예외가 발생할 위험이 있습니다.

**SQL 스크립트 이전 및 게시**: Sink 데이터베이스에 데이터가 기록되기 전(사전 처리) 및 후(사후 처리) 데이터가 실행되는 다중 줄 SQL 스크립트를 입력합니다.

![SQL 처리 스크립트 이전 및 사후](media/data-flow/prepost1.png "SQL 처리 스크립트")

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database에 대한 데이터 형식 매핑

Azure SQL Database에서 또는 Azure SQL Database로 데이터를 복사하면 Azure SQL Database 데이터 형식에서 Azure Data Factory 중간 데이터 형식에 대한 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Azure SQL Database 데이터 형식 | Azure Data Factory 중간 데이터 형식 |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |xml |

>[!NOTE]
> 10진수 중간 형식으로 매핑되는 데이터 형식의 경우 Azure Data Factory는 현재 최대 28자리의 데이터를 지원합니다. 정밀도가 28보다 큰 데이터가 있는 경우 SQL 쿼리에서 문자열로 변환하는 것이 좋습니다.

## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.

## <a name="getmetadata-activity-properties"></a>GetMetadata 활동 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 활동을](control-flow-get-metadata-activity.md) 선택합니다. 

## <a name="next-steps"></a>다음 단계
Azure Data Factory의 복사 활동에 의해 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식을](copy-activity-overview.md#supported-data-stores-and-formats)참조하십시오.
