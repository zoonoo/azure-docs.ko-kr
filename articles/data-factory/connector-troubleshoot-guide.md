---
title: Azure Data Factory 커넥터 문제 해결
description: Azure Data Factory의 커넥터 문제를 해결하는 방법을 알아봅니다.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/08/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 63a690ffaaefc506de296d43e7de13020fbd584a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366928"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 커넥터 관련 문제를 해결 하는 일반적인 방법을 설명 합니다.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>오류 코드: AzureBlobOperationFailed

- **메시지**: "Blob 작업이 실패 했습니다. ContainerName:% containerName;, 경로:% path;. "

- **원인**: Blob Storage 작업에 문제가 있습니다.

- **권장 사항**: 오류 정보를 확인 하려면 [Blob Storage 오류 코드](https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes)를 참조 하세요. 자세한 도움말을 보려면 Blob Storage 팀에 문의 하세요.


### <a name="invalid-property-during-copy-activity"></a>복사 작업 중에 속성이 잘못 되었습니다.

- **메시지**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **원인**: 데이터 집합에 정의 된 형식이 복사 작업에 정의 된 원본 또는 싱크 형식과 일치 하지 않습니다.

- **해결** 방법: 데이터 집합 또는 파이프라인 JSON 정의를 편집 하 여 형식을 일관 되 게 만든 다음 배포를 다시 실행 합니다.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>오류 메시지: 요청 크기가 너무 큽니다.

- **증상**: 기본 쓰기 일괄 처리 크기를 사용 하 여 Azure Cosmos DB에 데이터를 복사 하는 경우 다음과 같은 오류가 표시 됩니다. `Request size is too large.`

- **원인**: Azure Cosmos DB 단일 요청의 크기를 2mb로 제한 합니다. 수식은 *요청 크기 = 단일 문서 크기 \* 쓰기 일괄 처리 크기* 입니다. 문서 크기가 크면 기본 동작으로 인해 요청 크기가 너무 커집니다. 쓰기 일괄 처리 크기를 조정할 수 있습니다.

- **해결** 방법: 복사 작업 싱크에서 *쓰기 일괄 처리 크기* 값을 줄입니다 (기본값은 1만).

### <a name="error-message-unique-index-constraint-violation"></a>오류 메시지: 고유한 인덱스 제약 조건 위반

- **증상**: Azure Cosmos DB에 데이터를 복사 하는 경우 다음과 같은 오류가 표시 됩니다.

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **원인**: 다음 두 가지 가능한 원인이 있습니다.

    - 원인 1: write 동작으로 **Insert** 를 사용 하는 경우이 오류는 원본 데이터에 동일한 ID를 가진 행 또는 개체가 있음을 의미 합니다.
    - 원인 2: **Upsert** 를 쓰기 동작으로 사용 하 고 컨테이너에 다른 고유 키를 설정 하는 경우이 오류는 원본 데이터에 id가 다른 행 이나 개체가 있지만 정의 된 고유 키에 대해 동일한 값을 갖고 있음을 의미 합니다.

- **해결 방법**: 

    - 원인 1의 경우 **Upsert** 을 쓰기 동작으로 설정 합니다.
    - 원인 2의 경우 각 문서에 정의 된 고유 키에 대해 서로 다른 값이 있는지 확인 합니다.

### <a name="error-message-request-rate-is-large"></a>오류 메시지: 요청 빈도가 높습니다.

- **증상**: Azure Cosmos DB에 데이터를 복사 하는 경우 다음과 같은 오류가 표시 됩니다.

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **원인**: 사용 된 rus (요청 단위) 수가 Azure Cosmos DB에 구성 된 사용 가능한 rus 보다 큽니다. Azure Cosmos DB RUs를 계산 하는 방법을 알아보려면 [Azure Cosmos DB의 요청 단위](../cosmos-db/request-units.md#request-unit-considerations)를 참조 하세요.

- **해결** 방법: 다음 두 가지 솔루션 중 하나를 시도 합니다.

    - *컨테이너 RUs* 번호를 Azure Cosmos DB의 더 큰 값으로 늘립니다. 이 솔루션은 복사 작업 성능을 향상 시키지만 Azure Cosmos DB에 더 많은 비용이 발생 합니다. 
    - *Writebatchsize* 를 1000와 같은 더 낮은 값으로 줄이고 *parallelCopies* 을 1과 같이 더 낮은 값으로 줄입니다. 이 솔루션은 복사 실행 성능을 단축 하지만 Azure Cosmos DB에 더 많은 비용이 발생 하지는 않습니다.

### <a name="columns-missing-in-column-mapping"></a>열 매핑의 열이 누락 되었습니다.

- **증상**: 열 매핑에 대 한 Azure Cosmos DB 스키마를 가져오는 경우 일부 열이 누락 됩니다. 

- **원인**: Data Factory는 처음 10 개의 Azure Cosmos DB 문서에서 스키마를 유추 합니다. 일부 문서 열 또는 속성에 값이 없는 경우 스키마는 Data Factory에서 검색 되지 않으므로 표시 되지 않습니다.

- **해결** 방법: 다음 코드와 같이 쿼리를 조정 하 여 결과 집합에 열 값이 빈 값으로 표시 되도록 할 수 있습니다. 처음 10 개 문서에는 *불가능* 한 열이 누락 된 것으로 가정 합니다. 또는 매핑을 위한 열을 수동으로 추가할 수 있습니다.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>오류 메시지: 판독기에 대한 GuidRepresentation이 CSharpLegacy입니다.

- **증상**: Azure Cosmos DB MongoAPI 또는 MONGODB에서 UUID (범용 고유 식별자) 필드를 사용 하 여 데이터를 복사 하는 경우 다음과 같은 오류가 표시 됩니다.

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **원인**: 이진 JSON (bson)에서 UUID를 표시 하는 방법에는 두 가지 방법이 있습니다. UuidStardard 및 UuidLegacy. 기본적으로 UuidLegacy는 데이터를 읽는 데 사용됩니다. MongoDB의 UUID 데이터가 UuidStandard 인 경우 오류가 표시 됩니다.

- **해결** 방법: MongoDB 연결 문자열에서 *uuidRepresentation = standard* 옵션을 추가 합니다. 자세한 내용은 [MongoDB 연결 문자열](connector-mongodb.md#linked-service-properties)을 참조하세요.
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>오류 코드: CosmosDbSqlApiOperationFailed

- **메시지**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **원인**: CosmosDbSqlApi 작업에 문제가 있습니다.

- **권장 사항**: 오류 세부 정보를 확인 하려면 [Azure Cosmos DB 도움말 문서](../cosmos-db/troubleshoot-dot-net-sdk.md)를 참조 하세요. 자세한 도움말을 보려면 Azure Cosmos DB 팀에 문의 하세요.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>오류 메시지: 기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대 한 트러스트 관계를 설정할 수 없습니다.

- **증상**: 복사 작업이 실패 하 고 다음 오류가 발생 합니다. 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **원인**: TLS 핸드셰이크 중 인증서 유효성 검사에 실패 했습니다.

- **해결** 방법: 해결 방법으로, 준비 된 복사본을 사용 하 여 Azure Data Lake Storage Gen1에 대 한 TLS (전송 계층 보안) 유효성 검사를 건너뜁니다. 이 문제를 재현 하 고 네트워크 모니터 (netmon) 추적을 수집한 다음 네트워크 팀과 협력 하 여 로컬 네트워크 구성을 확인 해야 합니다.

    ![문제 해결을 위한 Azure Data Lake Storage Gen1 연결의 다이어그램입니다.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>오류 메시지: 원격 서버에서 다음과 같은 오류를 반환했습니다. (403) 사용할 수 없음

- **증상**: 다음 오류가 발생하여 복사 작업이 실패합니다. 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **원인**: 사용 하는 서비스 주체 또는 관리 id에 특정 폴더 또는 파일에 대 한 액세스 권한이 없기 때문일 수 있습니다.

- **해결** 방법: 복사 해야 하는 모든 폴더와 하위 폴더에 적절 한 사용 권한을 부여 합니다. 자세한 내용은 [Azure Data Factory를 사용 하 여 Azure Data Lake Storage Gen1 간에 데이터 복사](connector-azure-data-lake-store.md#linked-service-properties)를 참조 하세요.

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>오류 메시지: 서비스 주체를 사용하여 토큰에 액세스하지 못했습니다. ADAL 오류: service_unavailable

- **증상**: 복사 작업이 실패 하 고 다음 오류가 발생 합니다.

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **원인**: Azure Active Directory에서 소유 하 고 있는 STS (서비스 토큰 서버)를 사용할 수 없는 경우 요청을 처리할 수 있는 사용량이 너무 많아서 HTTP 오류 503을 반환 합니다. 

- **해결 방법**: 몇 분 후에 복사 작업을 다시 실행합니다.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>오류 코드: ADLSGen2OperationFailed

- **메시지**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **원인 및 권장 사항**: 다른 원인으로 인해이 오류가 발생할 수 있습니다. 아래 목록에서 가능한 원인 분석 및 관련 권장 사항을 확인 하십시오.

  | 원인 분석                                               | 권장                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Azure Data Lake Storage Gen2 일부 작업이 실패 했음을 나타내는 오류를 throw 합니다.| Azure Data Lake Storage Gen2에서 throw 한 자세한 오류 메시지를 확인 합니다. 일시적인 오류 이면 작업을 다시 시도 하세요. 자세한 도움말을 보려면 Azure Storage 지원에 문의 하 고 오류 메시지에 요청 ID를 제공 하십시오. |
  | 오류 메시지에 "사용 권한 없음" 문자열이 포함 되어 있는 경우 사용 하는 서비스 주체 또는 관리 id에 Azure Data Lake Storage Gen2에 액세스할 수 있는 권한이 없을 수 있습니다. | 이 오류를 해결 하려면 [Azure Data Factory를 사용 하 여 Azure Data Lake Storage Gen2에서 데이터 복사 및 변환](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)을 참조 하세요. |
  | 오류 메시지에 "InternalServerError" 문자열이 포함 되어 있으면 Azure Data Lake Storage Gen2에서 오류를 반환 합니다. | 일시적인 오류로 인해 오류가 발생할 수 있습니다. 그렇다면 작업을 다시 시도합니다. 문제가 계속 되 면 Azure Storage 지원 담당자에 게 문의 하 고 오류 메시지에서 요청 ID를 제공 합니다. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Azure Data Lake Storage Gen2 계정에 대 한 요청으로 인해 시간 초과 오류가 발생 했습니다.

- **Message**: 
  * 오류 코드 = `UserErrorFailedBlobFSOperation`
  * 오류 메시지 = `BlobFS operation failed for: A task was canceled.`

- **원인**:이 문제는 일반적으로 IR (자체 호스팅 Integration Runtime) 컴퓨터에서 발생 하는 Azure Data Lake Storage Gen2 싱크 시간 제한 오류로 인해 발생 합니다.

- **권장 사항**: 

    - 가능 하면 자체 호스팅 IR 컴퓨터와 대상 Azure Data Lake Storage Gen2 계정을 동일한 지역에 두십시오. 이렇게 하면 임의 시간 제한 오류를 방지 하 고 성능을 향상 시킬 수 있습니다.

    - Express 경로와 같은 특수 한 네트워크 설정이 있는지 확인 하 고 네트워크에 충분 한 대역폭이 있는지 확인 합니다. 전체 대역폭이 낮을 때 자체 호스팅 IR 동시 작업 설정을 낮추는 것이 좋습니다. 이렇게 하면 여러 동시 작업에서 네트워크 리소스 경쟁을 방지 하는 데 도움이 될 수 있습니다.

    - 파일 크기가 보통 또는 작은 경우 이진이 아닌 copy에 더 작은 블록 크기를 사용 하 여 이러한 시간 제한 오류를 완화 합니다. 자세한 내용은 [Blob Storage Put 블록](/rest/api/storageservices/put-block)을 참조 하세요.

       사용자 지정 블록 크기를 지정 하려면 다음과 같이 JSON 파일 편집기에서 속성을 편집 합니다.
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Azure Files 저장소

### <a name="error-code-azurefileoperationfailed"></a>오류 코드: AzureFileOperationFailed

- **메시지**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **원인**: Azure Files 저장소 작업에 문제가 있습니다.

- **권장 사항**: 오류 세부 정보를 확인 하려면 [Azure Files 도움말](https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes)을 참조 하세요. 자세한 도움말을 보려면 Azure Files 팀에 문의 하세요.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, Azure SQL Database 및 SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>오류 코드: SqlFailedToConnect

- **메시지**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **원인 및 권장 사항**: 다른 원인으로 인해이 오류가 발생할 수 있습니다. 아래 목록에서 가능한 원인 분석 및 관련 권장 사항을 확인 하십시오.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Azure SQL의 경우 오류 메시지에 "SqlErrorNumber = 47073" 문자열이 포함 되어 있으면 연결 설정에서 공용 네트워크 액세스가 거부 되었음을 의미 합니다. | Azure SQL 방화벽에서 **공용 네트워크 액세스 거부** 옵션을 *아니요* 로 설정 합니다. 자세한 내용은 [AZURE SQL 연결 설정](https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access)을 참조 하세요. |
    | Azure SQL에서 오류 메시지에 "SqlErrorNumber = [errorcode]"와 같은 SQL 오류 코드가 포함 된 경우 Azure SQL 문제 해결 가이드를 참조 하세요. | 권장 사항은 [연결 문제 해결 및 Azure SQL Database 및 AZURE SQL Managed Instance를 사용 하는 기타 오류](https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues)를 참조 하세요. |
    | 포트 1433가 방화벽 허용 목록에 있는지 확인 합니다. | 자세한 내용은 [SQL Server에서 사용 하는 포트](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-)를 참조 하세요. |
    | 오류 메시지에 "SqlException SQL Database" 문자열이 포함 된 경우 오류는 특정 작업이 실패 했음을 나타냅니다. | 자세한 내용은 [데이터베이스 엔진 오류](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors)에서 SQL 오류 코드를 검색 합니다. 추가 도움이 필요 하면 Azure SQL 지원에 문의 하세요. |
    | 일시적인 문제 (예: 네트워크를 사용할 수 있는 네트워크 연결) 인 경우 작업 정책에서 재시도를 추가 하 여 완화할 수 있습니다. | 자세한 내용은 [Azure Data Factory의 파이프라인 및 작업](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy)을 참조하세요. |
    | 오류 메시지에 "IP 주소를 가진 클라이언트 ... ' 문자열이 포함 된 경우 서버에 액세스할 수 없습니다. "라는 메시지가 표시 되 고 Azure SQL Database에 연결 하려고 시도 하는 경우 일반적으로 Azure SQL Database 방화벽 문제로 인해 오류가 발생 합니다. | Azure SQL Server 방화벽 구성에서 **azure 서비스 및 리소스에서이 서버에 액세스할 수 있도록 허용** 옵션을 사용 하도록 설정 합니다. 자세한 내용은 [Azure SQL Database 및 Azure SYNAPSE IP 방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 참조 하세요. |
    
### <a name="error-code-sqloperationfailed"></a>오류 코드: SqlOperationFailed

- **메시지**: `A database operation failed. Please search error to get more details.`

- **원인 및 권장 사항**: 다른 원인으로 인해이 오류가 발생할 수 있습니다. 아래 목록에서 가능한 원인 분석 및 관련 권장 사항을 확인 하십시오.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 오류 메시지에 "SqlException" 문자열이 포함 된 경우 SQL Database 특정 작업이 실패 했음을 나타내는 오류를 throw 합니다. | SQL 오류가 명확 하지 않은 경우 데이터베이스를 최신 호환성 수준 ' 150 '으로 변경 하십시오. 최신 버전의 SQL 오류를 throw 할 수 있습니다. 자세한 내용은 [설명서](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)를 참조하세요. <br/> SQL 문제를 해결 하는 방법에 대 한 자세한 내용은 [데이터베이스 엔진 오류](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors)에서 sql 오류 코드를 검색 하십시오. 추가 도움이 필요 하면 Azure SQL 지원에 문의 하세요. |
    | 오류 메시지에 "PdwManagedToNativeInteropException" 문자열이 포함 된 경우 일반적으로 원본 및 싱크 열 크기가 일치 하지 않기 때문입니다. | 원본 및 싱크 열의 크기를 확인 합니다. 추가 도움이 필요 하면 Azure SQL 지원에 문의 하세요. |
    | 오류 메시지에 "InvalidOperationException" 문자열이 포함 된 경우 일반적으로 잘못 된 입력 데이터로 인해 발생 합니다. | 문제가 발생 한 행을 식별 하려면 복사 작업에서 내결함성 기능을 사용 하도록 설정 합니다. 이렇게 하면 추가 조사를 위해 문제가 있는 행을 저장소로 리디렉션할 수 있습니다. 자세한 내용은 [Azure Data Factory의 복사 작업](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)에 대 한 내결함성을 참조 하세요. |


### <a name="error-code-sqlunauthorizedaccess"></a>오류 코드: SqlUnauthorizedAccess

- **메시지**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **원인**: 자격 증명이 올바르지 않거나 로그인 계정이 SQL 데이터베이스에 액세스할 수 없습니다.

- **권장 사항**: 로그인 계정에 SQL 데이터베이스에 액세스할 수 있는 충분 한 권한이 있는지 확인 합니다.


### <a name="error-code-sqlopenconnectiontimeout"></a>오류 코드: SqlOpenConnectionTimeout

- **메시지**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **원인**:이 문제는 SQL database 일시적 오류가 원인일 수 있습니다.

- **권장 사항**: 연결 된 서비스 연결 문자열을 더 큰 연결 시간 제한 값으로 업데이트 하려면 작업을 다시 시도 하세요.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>오류 코드: SqlAutoCreateTableTypeMapFailed

- **메시지**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **원인**: autocreation 테이블이 원본 요구 사항을 충족할 수 없습니다.

- **권장 사항**: *매핑에서* 열 유형을 업데이트 하거나 대상 서버에 싱크 테이블을 수동으로 만듭니다.


### <a name="error-code-sqldatatypenotsupported"></a>오류 코드: SqlDataTypeNotSupported

- **메시지**: `A database operation failed. Check the SQL errors.`

- **원인**: SQL 원본에서 문제가 발생 하 고 오류가 SqlDateTime 오버플로와 관련 되는 경우 데이터 값은 논리 형식 범위 (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM)를 초과 합니다.

- **권장 사항**: 형식을 원본 SQL 쿼리의 문자열로 캐스팅 하거나 복사 작업 열 매핑에서 열 형식을 *문자열로* 변경 합니다.

- **원인**: SQL 싱크에서 문제가 발생 하 고 오류가 SqlDateTime 오버플로와 관련 된 경우 데이터 값이 싱크 테이블에서 허용 된 범위를 초과 합니다.

- **권장 사항**: 해당 열 유형을 싱크 테이블의 *datetime2* 형식으로 업데이트 합니다.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>오류 코드: Sqlin유효한 Dbstoredprocedure

- **메시지**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **원인**: 지정 된 저장 프로시저가 잘못 되었습니다. 저장 프로시저에서 데이터를 반환 하지 않는 원인이 원인일 수 있습니다.

- **권장 사항**: SQL 도구를 사용 하 여 저장 프로시저의 유효성을 검사 합니다. 저장 프로시저에서 데이터를 반환할 수 있는지 확인 합니다.


### <a name="error-code-sqlinvaliddbquerystring"></a>오류 코드: Sqlin유효한 Dbquerystring

- **메시지**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **원인**: 지정한 SQL 쿼리가 잘못 되었습니다. 쿼리에서 데이터를 반환 하지 않는 원인이 있을 수 있습니다.

- **권장 사항**: sql 도구를 사용 하 여 sql 쿼리의 유효성을 검사 합니다. 쿼리가 데이터를 반환할 수 있는지 확인 합니다.


### <a name="error-code-sqlinvalidcolumnname"></a>오류 코드: Sqlin유효한 Columnname

- **메시지**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **원인**: 구성이 잘못 되었을 수 있으므로 열을 찾을 수 없습니다.

- **권장 사항**: 쿼리의 열, 데이터 집합의 *구조* 및 활동의 *매핑을* 확인 합니다.


### <a name="error-code-sqlbatchwritetimeout"></a>오류 코드: SqlBatchWriteTimeout

- **메시지**: `Timeouts in SQL write operation.`

- **원인**:이 문제는 SQL database 일시적인 오류로 인해 발생할 수 있습니다.

- **권장 사항**: 작업을 다시 시도 하세요. 문제가 지속 되 면 Azure SQL 지원에 문의 하세요.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>오류 코드: SqlBatchWriteTransactionFailed

- **메시지**: `SQL transaction commits failed.`

- **원인**: 예외 정보가 지속적으로 트랜잭션 시간 제한을 나타내는 경우 통합 런타임과 데이터베이스 간의 네트워크 대기 시간이 기본 임계값 인 30 초 보다 큽니다.

- **권장 사항**: 120 보다 크거나 같은 *연결 시간 제한* 값을 사용 하 여 SQL 연결 된 서비스 연결 문자열을 업데이트 하 고 작업을 다시 실행 하십시오.

- **원인**: 예외 세부 정보가 간헐적으로 sql 연결이 끊어지는 것으로 표시 되는 경우 일시적인 네트워크 오류 또는 sql database 쪽 문제일 수 있습니다.

- **권장 사항**: 작업을 다시 시도 하 고 SQL database 쪽 메트릭을 검토 합니다.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>오류 코드: SqlBulkCopyInvalidColumnLength

- **메시지**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **원인**: bcp (대량 복사 프로그램 유틸리티) 클라이언트에서 잘못 된 열 길이를 받았기 때문에 SQL 대량 복사가 실패 했습니다.

- **권장 사항**: 문제가 발생 한 행을 확인 하려면 복사 작업에서 내결함성 기능을 사용 하도록 설정 합니다. 이렇게 하면 추가 조사를 위해 문제가 있는 행을 저장소로 리디렉션할 수 있습니다. 자세한 내용은 [Azure Data Factory의 복사 작업](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)에 대 한 내결함성을 참조 하세요.


### <a name="error-code-sqlconnectionisclosed"></a>오류 코드: SqlConnectionIsClosed

- **메시지**: `The connection is closed by SQL Database.`

- **원인**: 동시 실행이 많고 서버가 연결을 종료 하는 경우 sql 데이터베이스에서 sql 연결을 닫습니다.

- **권장 사항**: 연결을 다시 시도 합니다. 문제가 지속 되 면 Azure SQL 지원에 문의 하세요.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>오류 메시지: 문자열을 uniqueIDentifier로 변환하지 못했습니다.

- **증상**: 준비 된 복사 및 PolyBase를 사용 하 여 테이블 형식 데이터 원본 (예: SQL Server)에서 Azure Synapse Analytics로 데이터를 복사 하는 경우 다음과 같은 오류가 표시 됩니다.

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **원인**: Azure Synapse Analytics PolyBase에서 빈 문자열을 GUID로 변환할 수 없습니다.

- **해결** 방법: 복사 작업 싱크에서 PolyBase 설정 아래에서 **형식 기본값 사용** 옵션을 *false* 로 설정 합니다.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>오류 메시지: 필요한 데이터 형식: DECIMAL(x,x), 잘못된 값

- **증상**: 준비 된 복사 및 PolyBase를 사용 하 여 테이블 형식 데이터 원본 (예: SQL Server)에서 Azure Synapse Analytics로 데이터를 복사 하는 경우 다음과 같은 오류가 표시 됩니다.

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **원인**: Azure Synapse Analytics PolyBase에서 10 진수 열에 빈 문자열 (null 값)을 삽입할 수 없습니다.

- **해결** 방법: 복사 작업 싱크에서 PolyBase 설정 아래에서 **형식 기본값 사용** 옵션을 false로 설정 합니다.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>오류 메시지: Java 예외 메시지: HdfsBridge:: CreateRecordReader

- **증상**: PolyBase를 사용 하 여 Azure Synapse Analytics로 데이터를 복사 하 고 다음 오류를 수신 합니다.

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **원인**: 스키마 (총 열 너비)가 너무 커서 (1mb 보다 큼) 원인일 수 있습니다. 모든 열의 크기를 추가 하 여 대상 Azure Synapse Analytics 테이블의 스키마를 확인 합니다.

    - Int = 4 바이트
    - Bigint = 8 바이트
    - Varchar (n), char (n), binary (n), varbinary (n) = n 바이트
    - Nvarchar (n), nchar (n) = n * 2 바이트
    - 날짜 = 6 바이트
    - Datetime/(2), smalldatetime = 16 바이트
    - Datetimeoffset = 20 바이트
    - Decimal = 19 바이트
    - Float = 8 바이트
    - Money = 8 바이트
    - Smallmoney = 4 바이트
    - Real = 4 바이트
    - Smallint = 2 바이트
    - 시간 = 12 바이트
    - Tinyint = 1 바이트

- **해결 방법**: 
    - 열 너비를 1mb 미만으로 줄입니다.
    - 또는 PolyBase를 사용 하지 않도록 설정 하 여 대량 삽입 방법을 사용 합니다.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>오류 메시지: HTTP 조건부 헤더를 사용하여 지정한 조건이 충족되지 않습니다.

- **증상**: SQL 쿼리를 사용 하 여 Azure Synapse Analytics에서 데이터를 가져오고 다음 오류를 수신 합니다.

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **원인**: Azure Storage에서 외부 테이블을 쿼리 하는 동안 Azure Synapse Analytics에서 문제가 발생 했습니다.

- **해결** 방법: SSMS (SQL Server Management Studio)에서 동일한 쿼리를 실행 하 고 동일한 결과를 얻을 수 있는지 확인 합니다. 이렇게 하려면 Azure Synapse Analytics에 대 한 지원 티켓을 열고 Azure Synapse Analytics 서버 및 데이터베이스 이름을 제공 합니다.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>성능 계층이 적고 복사 실패가 발생 합니다.

- **증상**: Azure SQL Database에 데이터를 복사 하 여 다음 오류를 수신 합니다. `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **원인**: s1 Azure SQL Database i/o (입/출력) 제한에 도달 했습니다.

- **해결** 방법: 문제를 해결 하려면 Azure SQL Database 성능 계층을 업그레이드 합니다. 


### <a name="sql-table-cant-be-found"></a>SQL 테이블을 찾을 수 없습니다. 

- **증상**: 하이브리드에서 온-프레미스 SQL Server 테이블로 데이터를 복사 하 여 다음 오류를 수신 합니다.`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **원인**: 현재 SQL 계정에 .net SqlBulkCopy에서 발급 한 요청을 실행할 수 있는 권한이 없습니다. WriteToServer.

- **해결** 방법: 권한 있는 SQL 계정으로 전환 합니다.


### <a name="error-message-string-or-binary-data-is-truncated"></a>오류 메시지: 문자열 또는 이진 데이터가 잘렸습니다.

- **증상**: 온-프레미스 Azure SQL Server 테이블로 데이터를 복사할 때 오류가 발생 합니다. 

- **원인**: Cx SQL 테이블 스키마 정의에 예상 보다 길이가 작은 열이 하나 이상 있습니다.

- **해결** 방법:이 문제를 해결 하려면 다음을 시도 합니다.

    1. 문제가 있는 행의 문제를 해결 하려면 SQL 싱크 [내결함성](./copy-activity-fault-tolerance.md), 특히 "redirectIncompatibleRowSettings"를 적용 합니다.

        > [!NOTE]
        > 내결함성을 위해 추가 실행 시간이 필요할 수 있으며,이로 인해 비용이 더 높아질 수 있습니다.

    2. SQL 테이블 스키마 열 길이에 대해 리디렉션된 데이터를 두 번 확인 하 여 업데이트 해야 할 열을 확인 합니다.

    3. 테이블 스키마를 적절 하 게 업데이트 합니다.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>오류 코드: AzureTableDuplicateColumnsFromSource

- **메시지**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **원인**: 중복 된 원본 열이 다음 이유 중 하나로 인해 발생할 수 있습니다.
   * 데이터베이스를 원본으로 사용 하 고 테이블 조인을 적용 합니다.
   * 헤더 행에 중복 된 열 이름을 가진 구조화 되지 않은 CSV 파일이 있습니다.

- **권장 사항**: 필요에 따라 원본 열을 두 번 확인 하 고 수정 합니다.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>오류 코드: DB2DriverRunFailed

- **메시지**: `Error thrown from driver. Sql code: '%code;'`

- **원인**: 오류 메시지에 "SQLSTATE = 51002 sqlcode =-805" 문자열이 포함 된 경우 [Azure Data Factory를 사용 하 여 d b 2에서 데이터 복사](https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties)의 "팁"을 따릅니다.

- **권장 사항**: 속성에서 "NULLID"를 설정 해 봅니다 `packageCollection`  .


## <a name="delimited-text-format"></a>구분된 텍스트 형식

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>오류 코드: DelimitedTextColumnNameNotAllowNull

- **메시지**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **원인**: 활동에 ' firstRowAsHeader '가 설정 되어 있으면 첫 번째 행이 열 이름으로 사용 됩니다. 이 오류는 첫 번째 행에 빈 값이 포함 되어 있음을 의미 합니다 (예: ' ColumnA, Columna ').

- **권장 사항**: 첫 번째 행을 확인 하 고 비어 있는 경우 값을 수정 합니다.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>오류 코드: DelimitedTextMoreColumnsThanDefined

- **메시지**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **원인 및 권장 사항**: 다른 원인으로 인해이 오류가 발생할 수 있습니다. 아래 목록에서 가능한 원인 분석 및 관련 권장 사항을 확인 하십시오.

  | 원인 분석                                               | 권장                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 문제가 있는 행의 열 개수가 첫 번째 행의 열 개수 보다 큽니다. 데이터 문제 또는 잘못 된 열 구분 기호 또는 따옴표 문자 설정으로 인해 발생할 수 있습니다. | 오류 메시지에서 행 개수를 가져오고, 행의 열을 확인 하 고, 데이터를 수정 합니다. |
  | 오류 메시지에서 예상 열 개수가 "1" 이면 잘못 된 압축 또는 형식 설정을 지정 했을 수 있습니다 .이로 인해 Data Factory 파일을 잘못 구문 분석 했습니다. | 형식 설정이 원본 파일과 일치 하는지 확인 합니다. |
  | 원본이 폴더인 경우 지정 된 폴더에 있는 파일의 스키마가 다를 수 있습니다. | 지정 된 폴더의 파일이 동일한 스키마를 포함 하는지 확인 합니다. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service 및 Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>오류 코드: DynamicsCreateServiceClientError

- **메시지**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **원인**: Dynamics 서버 쪽에서 일시적인 문제입니다.

- **권장 사항**:  파이프라인을 다시 실행합니다. 다시 실패 하면 병렬 처리를 줄여 보세요. 문제가 지속 되 면 Dynamics 지원에 문의 하세요.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>스키마를 가져오거나 데이터를 미리 볼 때 누락 된 열

- **증상**: 스키마를 가져오거나 데이터를 미리 볼 때 일부 열이 누락 되었습니다. 오류 메시지: `The valid structure information (column name and type) are required for Dynamics source.`

- **원인**: Data Factory에서 처음 10 개 레코드에 값이 포함 되지 않은 열을 표시할 수 없기 때문에이 문제는 의도적으로 발생 합니다. 추가한 열이 올바른 형식 인지 확인 합니다. 

- **권장 사항**: 매핑 탭에서 수동으로 열을 추가 합니다.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>오류 코드: DynamicsMissingTargetForMultiTargetLookupField

- **메시지**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **원인**: 대상 열이 원본 또는 열 매핑에 존재 하지 않습니다.

- **권장 사항**:  
  1. 원본에 대상 열이 포함 되어 있는지 확인 합니다. 
  2. 열 매핑에 대상 열을 추가 합니다. 싱크 열이 *{fieldName} @EntityReference* 형식 인지 확인 합니다.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>오류 코드: DynamicsInvalidTargetForMultiTargetLookupField

- **메시지**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **원인**: 잘못 된 엔터티 이름이 다중 대상 조회 필드의 대상 엔터티로 제공 됩니다.

- **권장 사항**: 다중 대상 조회 필드에 올바른 엔터티 이름을 지정 합니다.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>오류 코드: DynamicsInvalidTypeForMultiTargetLookupField

- **메시지**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **원인**: 대상 열의 값이 문자열이 아닙니다.

- **권장 사항**: 다중 대상 조회 대상 열에 올바른 문자열을 제공 합니다.


### <a name="error-code-dynamicsfailedtorequetserver"></a>오류 코드: DynamicsFailedToRequetServer

- **메시지**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **원인**: Dynamics 서버를 가져오거나 액세스할 수 없거나 네트워크에 문제가 발생 했습니다.

- **권장** 사항: 자세한 내용은 네트워크 연결을 확인 하거나 Dynamics server 로그를 확인 하세요. 자세한 도움말은 Dynamics 지원에 문의 하세요.
  

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>오류 코드: FtpFailedToConnectToFtpServer

- **메시지**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **원인**: ftp 서버에 연결 하기 위해 SFTP (Secure FTP) 연결 된 서비스를 사용 하는 경우와 같이 ftp 서버에 잘못 된 연결 된 서비스 유형이 사용 될 수 있습니다.

- **권장 사항**: 대상 서버의 포트를 확인 합니다. FTP는 포트 21을 사용 합니다.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>오류 코드: HttpFileFailedToRead

- **메시지**: `Failed to read data from http server. Check the error from http server：%message;`

- **원인**:이 오류는 Azure Data Factory http 서버와 통신 하지만 http 요청 작업이 실패 하는 경우에 발생 합니다.

- **권장 사항**: 오류 메시지에서 HTTP 상태 코드를 확인 하 고 원격 서버 문제를 해결 합니다.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>오류 코드: ArgumentOutOfRangeException

- **메시지**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **원인**: Data Factory에서 DateTime 값은 0001-01-01 00:00:00에서 9999-12-31 23:59:59 사이의 범위에서 지원 됩니다. 그러나 Oracle은 BC 세기 또는 min/sec>59와 같은 광범위 한 DateTime 값을 지원 하 여 Data Factory에 오류가 발생 합니다.

- **권장 사항**: 

    Oracle의 값이 Data Factory 범위에 있는지 확인 하려면를 실행 `select dump(<column name>)` 합니다. 

    결과에서 바이트 시퀀스를 배우려면 [Oracle에 날짜를 저장 하는 방법](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)을 참조 하세요.


## <a name="orc-format"></a>ORC 형식

### <a name="error-code-orcjavainvocationexception"></a>오류 코드: OrcJavaInvocationException

- **메시지**: `An error occurred when invoking Java, message: %javaException;.`
- **원인 및 권장 사항**: 다른 원인으로 인해이 오류가 발생할 수 있습니다. 아래 목록에서 가능한 원인 분석 및 관련 권장 사항을 확인 하십시오.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 오류 메시지에 "OutOfMemory", "Java 힙 공간" 및 "doubleCapacity" 문자열이 포함 되어 있는 경우 일반적으로 이전 버전의 integration runtime에서 메모리 관리 문제가 발생 합니다. | 자체 호스팅 Integration Runtime 사용 하는 경우 최신 버전으로 업그레이드 하는 것이 좋습니다. |
    | 오류 메시지에 "OutOfMemory" 문자열이 포함 된 경우 통합 런타임에는 파일을 처리할 수 있는 충분 한 리소스가 없습니다. | 통합 런타임에 대 한 동시 실행을 제한 합니다. 자체 호스팅 IR의 경우 메모리가 8gb 보다 크거나 같은 강력한 컴퓨터로 확장 합니다. |
    |오류 메시지에 "NullPointerReference" 문자열이 포함 된 경우 일시적인 오류일 수 있습니다. | 작업을 다시 시도하세요. 문제가 계속되면 지원에 문의하세요. |
    | 오류 메시지에 "BufferOverflowException" 문자열이 포함 된 경우 일시적인 오류일 수 있습니다. | 작업을 다시 시도하세요. 문제가 계속되면 지원에 문의하세요. |
    | 오류 메시지에 "ClassCastException; serde2를 HiveCharWritable로 캐스팅할 수 없습니다." 라는 문자열이 포함 된 경우 Java 런타임 내에 형식 변환 문제가 있을 수 있습니다 .이 경우에 해당 하는 결과가 발생할 수 있습니다. 일반적으로 Java 런타임에서는 원본 데이터를 효율적으로 처리할 수 없음을 의미 합니다. | 이는 데이터 문제입니다. ORC format 데이터에서 char 또는 varchar 대신 문자열을 사용 하십시오. |

### <a name="error-code-orcdatetimeexceedlimit"></a>오류 코드: OrcDateTimeExceedLimit

- **메시지**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **원인**: datetime 값이 ' 0001-01-01 00:00:00 ' 이면 [율리우스 달력과 양력](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)의 차이로 인해 발생할 수 있습니다.

- **권장 사항**: 틱 값을 확인 하 고 datetime 값 ' 0001-01-01 00:00:00 '을 사용 하지 마세요.


## <a name="parquet-format"></a>Parquet 형식

### <a name="error-code-parquetjavainvocationexception"></a>오류 코드: ParquetJavaInvocationException

- **메시지**: `An error occurred when invoking java, message: %javaException;.`

- **원인 및 권장 사항**: 다른 원인으로 인해이 오류가 발생할 수 있습니다. 아래 목록에서 가능한 원인 분석 및 관련 권장 사항을 확인 하십시오.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 오류 메시지에 "OutOfMemory", "Java 힙 공간" 및 "doubleCapacity" 문자열이 포함 되어 있는 경우 일반적으로 이전 버전의 Integration Runtime에서 메모리 관리 문제가 발생 합니다. | 자체 호스팅 IR을 사용 하는 경우 3.20.7159.1 보다 이전 버전인 경우 최신 버전으로 업그레이드 하는 것이 좋습니다. |
    | 오류 메시지에 "OutOfMemory" 문자열이 포함 된 경우 통합 런타임에는 파일을 처리할 수 있는 충분 한 리소스가 없습니다. | 통합 런타임에 대 한 동시 실행을 제한 합니다. 자체 호스팅 IR의 경우 8gb 보다 크거나 같은 메모리를 사용 하는 강력한 컴퓨터로 확장 합니다. |
    | 오류 메시지에 "NullPointerReference" 문자열이 포함 된 경우 일시적인 오류일 수 있습니다. | 작업을 다시 시도하세요. 문제가 계속되면 지원에 문의하세요. |

### <a name="error-code-parquetinvalidfile"></a>오류 코드: ParquetInvalidFile

- **메시지**: `File is not a valid Parquet file.`

- **원인**: Parquet 파일 문제입니다.

- **권장 사항**: 입력이 유효한 Parquet 파일 인지 여부를 확인 합니다.


### <a name="error-code-parquetnotsupportedtype"></a>오류 코드: ParquetNotSupportedType

- **메시지**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **원인**: Azure Data Factory에서는 Parquet 형식이 지원 되지 않습니다.

- **권장 사항**: [Azure Data Factory에서 복사 작업을 통해 지원 되는 파일 형식 및 압축 코덱](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs)으로 이동 하 여 원본 데이터를 두 번 확인 합니다.


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>오류 코드: ParquetMissedDecimalPrecisionScale

- **메시지**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **원인**: 숫자 전체 자릿수 및 소수 자릿수가 구문 분석 되었지만 이러한 정보가 제공 되지 않았습니다.

- **권장 사항**: 소스가 올바른 전체 자릿수 및 소수 자릿수 정보를 반환 하지 않습니다. 정보에 대 한 문제 열을 확인 합니다.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>오류 코드: ParquetInvalidDecimalPrecisionScale

- **메시지**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **원인**: 스키마가 유효하지 않습니다.

- **권장 사항**: 전체 자릿수 및 소수 자릿수에 대 한 문제 열을 확인 합니다.


### <a name="error-code-parquetcolumnnotfound"></a>오류 코드: ParquetColumnNotFound

- **메시지**: `Column %column; does not exist in Parquet file.`

- **원인**: 소스 스키마가 싱크 스키마와 일치 하지 않습니다.

- **권장 사항**: 작업에서 매핑을 확인 합니다. 원본 열을 올바른 싱크 열에 매핑할 수 있는지 확인 합니다.


### <a name="error-code-parquetinvaliddataformat"></a>오류 코드: ParquetInvalidDataFormat

- **메시지**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **원인**: 데이터를 매핑. source에 지정 된 형식으로 변환할 수 없습니다.

- **권장 사항**: 원본 데이터를 두 번 확인 하거나 복사 작업 열 매핑에서이 열에 올바른 데이터 형식을 지정 하십시오. 자세한 내용은 [Azure Data Factory에서 복사 작업을 통해 지원 되는 파일 형식 및 압축 코덱](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs)을 참조 하세요.


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>오류 코드: ParquetDataCountNotMatchColumnCount

- **메시지**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **원인**: 원본 열 개수와 싱크 열 개수가 일치 하지 않습니다.

- **권장 사항**: 원본 열 개수가 ' 매핑 '의 싱크 열 수와 동일한 지 확인 하려면 두 번 확인 합니다.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>오류 코드: ParquetDataTypeNotMatchColumnType

- **메시지**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **원인**: 원본의 데이터를 싱크에 정의 된 형식으로 변환할 수 없습니다.

- **권장 사항**: 매핑 싱크에서 올바른 형식을 지정 하십시오.


### <a name="error-code-parquetbridgeinvaliddata"></a>오류 코드: ParquetBridgeInvalidData

- **메시지**: `%message;`

- **원인**: 데이터 값이 제한을 초과 했습니다.

- **권장 사항**: 작업을 다시 시도 하세요. 문제가 계속 되 면 microsoft에 문의 하세요.


### <a name="error-code-parquetunsupportedinterpretation"></a>오류 코드: ParquetUnsupportedInterpretation

- **메시지**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **원인**:이 시나리오는 지원 되지 않습니다.

- **권장 사항**:  'ParquetInterpretFor'는 'sparkSql'이 아니어야 합니다.


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>오류 코드: ParquetUnsupportFileLevelCompressionOption

- **메시지**: `File level compression is not supported for Parquet.`

- **원인**:이 시나리오는 지원 되지 않습니다.

- **권장 사항**: 페이로드에서 ' CompressionType '을 제거 합니다.


### <a name="error-code-usererrorjniexception"></a>오류 코드: UserErrorJniException

- **메시지**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **원인**: 일부 잘못 된 (전역) 인수가 설정 되어 있어 JAVA VIRTUAL MACHINE (JVM)을 (를) 만들 수 없습니다.

- **권장 사항**: 자체 호스팅 IR의 *각 노드* 를 호스트 하는 컴퓨터에 로그인 합니다. 다음과 같이 시스템 변수가 올바르게 설정 되었는지 확인 `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` 합니다. 모든 IR 노드를 다시 시작한 다음 파이프라인을 다시 실행 합니다.


### <a name="arithmetic-overflow"></a>산술 연산 오버플로

- **증상**: Parquet 파일을 복사할 때 오류 메시지가 발생 했습니다. `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **원인**: Oracle에서 Parquet로 파일을 복사 하는 경우 현재 <= 38의 소수 자릿수와 정수 부분 <= 20의 길이가 지원 됩니다. 

- **해결** 방법:이 문제가 있는 모든 열을 VARCHAR2로 변환 하 여 해결할 수 있습니다.


### <a name="no-enum-constant"></a>열거형 상수 없음

- **증상**: Parquet 형식 (또는)으로 데이터를 복사할 때 오류 메시지가 발생 했습니다. `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test`

- **원인**: 

    이 문제는 공백이 나 지원 되지 않는 특수 문자 (예:,)로 인해 발생할 수 있습니다. {} Parquet는 이러한 형식을 지원 하지 않으므로 열 이름에 () \n\t =)를 지정 합니다. 

    예를 들어 *contoso (test)* 와 같은 열 이름은 [코드](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) 에서 대괄호로 형식을 구문 분석 합니다 `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . 이러한 "test" 형식이 없기 때문에 오류가 throw 됩니다.

    지원 되는 유형을 확인 하려면 GitHub [apache/parquet-mr 사이트로](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)이동 합니다.

- **해결 방법**: 

    두 번 확인 하 여 다음을 확인 합니다.
    - 싱크 열 이름에 공백이 있습니다.
    - 공백을 포함 하는 첫 번째 행이 열 이름으로 사용 됩니다.
    - OriginalType 형식이 지원 됩니다. 이러한 특수 문자를 사용 하지 않도록 `,;{}()\n\t=` 합니다. 


## <a name="rest"></a>REST (영문)

### <a name="error-code-restsinkcallfailed"></a>오류 코드: RestSinkCallFailed

- **메시지**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **원인**:이 오류는 Azure Data Factory HTTP 프로토콜을 통해 REST 끝점에 대해 통신 하 고 요청 작업이 실패 하는 경우에 발생 합니다.

- **권장 사항**: HTTP 상태 코드 또는 오류 메시지의 메시지를 확인 하 고 원격 서버 문제를 해결 합니다.

### <a name="unexpected-network-response-from-the-rest-connector"></a>REST 커넥터에서 예기치 않은 네트워크 응답이 발생 합니다.

- **증상**: 끝점이 REST 커넥터에서 예기치 않은 응답 (400, 401, 403, 500)을 수신 하는 경우도 있습니다.

- **원인**: REST 원본 커넥터는 http 요청을 생성할 때 연결 된 서비스/데이터 집합/복사 원본의 URL 및 http 메서드/헤더를 매개 변수로 사용 합니다. 이 문제는 하나 이상의 지정 된 매개 변수에서 일부 실수로 인해 발생 했을 수 있습니다.

- **해결 방법**: 
    - 명령 프롬프트 창에서 ' 말아 '를 사용 하 여 매개 변수가 원인 인지 여부를 확인 합니다 (**Accept** 및 **사용자 에이전트** 헤더는 항상 포함 해야 함).
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      명령이 예기치 않은 동일한 응답을 반환 하는 경우 예상 되는 응답을 반환할 때까지 ' a s o '로 이전 매개 변수를 수정 합니다. 

      ' 말아--help '를 사용 하 여 명령의 고급 사용법을 확인할 수도 있습니다.

    - Data Factory REST 커넥터도 예기치 않은 응답을 반환 하는 경우 Microsoft 지원에 문의 하 여 문제를 해결 합니다.
    
    - ' A s s '는 SSL 인증서 유효성 검사 문제를 재현 하는 데 적합 하지 않을 수 있습니다. 일부 시나리오에서는 SSL 인증서 유효성 검사 문제를 발생 시 키 지 않고 ' 말아 ' 명령이 성공적으로 실행 되었습니다. 그러나 동일한 URL이 브라우저에서 실행 되는 경우 클라이언트에서 서버와의 트러스트를 설정 하기 위해 SSL 인증서가 실제로 반환 되지 않습니다.

      **Postman** 및 **Fiddler** 와 같은 도구는 앞의 경우에 권장 됩니다.


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>오류 코드: SftpOperationFail

- **메시지**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **원인**: SFTP 작업에 문제가 있습니다.

- **권장 사항**: SFTP에서 오류 세부 정보를 확인 합니다.


### <a name="error-code-sftprenameoperationfail"></a>오류 코드: SftpRenameOperationFail

- **메시지**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **원인**: SFTP 서버에서 임시 파일의 이름 바꾸기를 지원 하지 않습니다.

- **권장 사항**: 복사 싱크에서 "useTempFileRename"를 false로 설정 하 여 임시 파일에 업로드를 사용 하지 않도록 설정 합니다.


### <a name="error-code-sftpinvalidsftpcredential"></a>오류 코드: Sftpin유효한 Sftcredential

- **메시지**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **원인**: 개인 키 콘텐츠가 Azure 주요 자격 증명 모음 또는 SDK에서 인출 되지만 올바르게 인코딩되지 않습니다.

- **권장 사항**:  

    개인 키 콘텐츠가 주요 자격 증명 모음에 있는 경우 SFTP 연결 된 서비스에 직접 업로드 하는 경우 원래 키 파일이 작동할 수 있습니다.

    자세한 내용은 [Azure Data Factory를 사용 하 여 SFTP 서버에 데이터 복사](https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication)를 참조 하세요. 개인 키 콘텐츠는 b a s e 64로 인코딩된 SSH 개인 키 콘텐츠입니다.

    Base64 인코딩을 사용 하 여 원래 개인 키 파일 *전체* 를 인코딩하고 인코딩된 문자열을 키 자격 증명 모음에 저장 합니다. 원본 개인 키 파일은 파일에서 **업로드** 를 선택 하는 경우 SFTP 연결 된 서비스에서 사용할 수 있는 파일입니다.

    다음은 문자열을 생성 하는 데 사용할 수 있는 몇 가지 샘플입니다.

    - C # 코드 사용:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Python 코드 사용:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - 타사 base64 변환 도구를 사용 합니다. [Base64 형식으로 인코딩 도구를](https://www.base64encode.org/) 권장 합니다.

- **원인**: 잘못 된 키 콘텐츠 형식을 선택 했습니다.

- **권장 사항**:  

    PKCS # 8 형식 SSH 개인 키 ("-----시작 암호화 된 개인 키-----")는 현재 Data Factory의 SFTP 서버에 액세스 하는 데 지원 되지 않습니다. 

    "-----시작 RSA 개인 키-----"부터 키를 기존 SSH 키 형식으로 변환 하려면 다음 명령을 실행 합니다.

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **원인**: 자격 증명 또는 개인 키 콘텐츠가 잘못 되었습니다.

- **권장 사항**: 키 파일 또는 암호가 올바른지 여부를 확인 하려면 winscp와 같은 도구를 사용 하 여 두 번 확인 합니다.

### <a name="sftp-copy-activity-failed"></a>SFTP 복사 작업이 실패 했습니다.

- **증상**: 
  * 오류 코드: UserErrorInvalidColumnMappingColumnNotFound 
  * 오류 메시지: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **원인**: 소스에 "AccMngr" 라는 열이 포함 되어 있지 않습니다.

- **해결** 방법: "AccMngr" 열이 있는지 확인 하려면 대상 데이터 집합 열을 매핑하여 데이터 집합 구성을 두 번 확인 합니다.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>오류 코드: SftpFailedToConnectToSftpServer

- **메시지**: `Failed to connect to SFTP server '%server;'.`

- **원인**: 오류 메시지에 "소켓 읽기 작업이 3만 밀리초 후에 시간 초과 되었습니다." 라는 문자열이 포함 된 경우, 한 가지 가능한 원인은 SFTP 서버에 잘못 된 연결 된 서비스 유형이 사용 되는 것입니다. 예를 들어, FTP 연결 된 서비스를 사용 하 여 SFTP 서버에 연결할 수 있습니다.

- **권장 사항**: 대상 서버의 포트를 확인 합니다. 기본적으로 SFTP는 포트 22를 사용 합니다.

- **원인**: 오류 메시지에 "서버 응답에 SSH 프로토콜 id가 포함 되어 있지 않습니다." 문자열이 포함 된 경우 SFTP 서버에서 연결을 제한 하는 것이 원인일 수 있습니다. Data Factory는 여러 연결을 만들어 SFTP 서버에서 병렬로 다운로드 하 고 때로는 SFTP 서버 제한이 발생할 수 있습니다. 일반적으로 다른 서버는 제한이 발생할 경우 다른 오류를 반환 합니다.

- **권장 사항**:  

    SFTP 데이터 집합의 최대 동시 연결 수를 1로 지정 하 고 복사 작업을 다시 실행 합니다. 작업이 성공 하면 제한이 원인 임을 확인할 수 있습니다.

    낮은 처리량을 승격 하려면 SFTP 관리자에 게 동시 연결 수 제한을 늘리거나 다음 중 하나를 수행할 수 있습니다.

    * 자체 호스팅 IR을 사용 하는 경우 자체 호스팅 IR 컴퓨터의 IP를 허용 목록에 추가 합니다.
    * Azure IR 사용 하는 경우 [AZURE INTEGRATION RUNTIME IP 주소](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)를 추가 합니다. SFTP 서버 허용 목록에 Ip 범위를 추가 하지 않으려는 경우 대신 자체 호스팅 IR을 사용 합니다.

## <a name="sharepoint-online-list"></a>SharePoint Online 목록

### <a name="error-code-sharepointonlineauthfailed"></a>오류 코드: SharePointOnlineAuthFailed

- **메시지**: `The access token generated failed, status code: %code;, error message: %message;.`

- **원인**: 서비스 사용자 ID와 키가 올바르게 설정 되지 않았을 수 있습니다.

- **권장 사항**: 등록 된 응용 프로그램 (서비스 주체 ID) 및 키를 확인 하 여 올바르게 설정 되었는지 확인 합니다.


## <a name="xml-format"></a>XML 형식

### <a name="error-code-xmlsinknotsupported"></a>오류 코드: XmlSinkNotSupported

- **메시지**: `Write data in XML format is not supported yet, choose a different format!`

- **원인**: 복사 작업에서 XML 데이터 집합이 싱크 데이터 집합으로 사용 되었습니다.

- **권장 사항**: 데이터 집합을 싱크 데이터 집합의 다른 형식으로 사용 합니다.


### <a name="error-code-xmlattributecolumnnameconflict"></a>오류 코드: XmlAttributeColumnNameConflict

- **메시지**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **원인**: 특성 접두사가 사용 되었으며이로 인해 충돌이 발생 했습니다.

- **권장 사항**: "attributeprefix" 속성에 대해 다른 값을 설정 합니다.


### <a name="error-code-xmlvaluecolumnnameconflict"></a>오류 코드: XmlValueColumnNameConflict

- **메시지**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **원인**: 자식 요소 이름 중 하나가 요소 값의 열 이름으로 사용 되었습니다.

- **권장 사항**: "valueColumn" 속성에 대해 다른 값을 설정 합니다.


### <a name="error-code-xmlinvalid"></a>오류 코드: XmlInvalid

- **메시지**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **원인**: 입력 XML 파일 형식이 잘못 되었습니다.

- **권장 사항**: XML 파일을 수정 하 여 제대로 구성 되었는지 확인 합니다.


## <a name="general-copy-activity-error"></a>일반 복사 작업 오류

### <a name="error-code-jrenotfound"></a>오류 코드: J\\\ofound

- **메시지**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **원인**: 자체 호스팅 IR에서 Java 런타임을 찾을 수 없습니다. 특정 소스를 읽으려면 Java 런타임이 필요 합니다.

- **권장 사항**: 통합 런타임 환경을 확인 하려면 [자체 호스팅 Integration Runtime 사용](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)을 참조 하세요.


### <a name="error-code-wildcardpathsinknotsupported"></a>오류 코드: WildcardPathSinkNotSupported

- **메시지**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **원인**: 싱크 데이터 집합이 와일드 카드 값을 지원 하지 않습니다.

- **권장 사항**: 싱크 데이터 집합을 확인 하 고 와일드 카드 값을 사용 하지 않고 경로를 다시 작성 합니다.


### <a name="fips-issue"></a>FIPS 문제

- **증상**: FIPS 사용 자체 호스팅 IR 컴퓨터에서 복사 작업이 실패 하 고 다음과 같은 오류 메시지가 나타납니다. `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **원인**: Azure BLOB, SFTP 등의 커넥터를 사용 하 여 데이터를 복사 하는 경우이 오류가 발생할 수 있습니다. 연방 FIPS (정보 처리 표준)는 사용할 수 있는 특정 암호화 알고리즘 집합을 정의 합니다. 컴퓨터에서 FIPS 모드를 사용 하도록 설정 하면 복사 작업이 종속 된 일부 암호화 클래스가 일부 시나리오에서 차단 됩니다.

- **해결** 방법: ["fips 모드"를 더 이상 권장 하지 않는 이유](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)를 알아보고 자체 호스팅 IR 컴퓨터에서 FIPS를 사용 하지 않도록 설정할 수 있는지 여부를 평가 합니다.

    또는 Azure Data Factory FIPS를 바이패스 하 고 작업을 성공적으로 실행 하도록 하려면 다음을 수행 합니다.

    1. 자체 호스팅 IR이 설치 된 폴더를 엽니다. 경로는 일반적으로 *C:\Program Files\Microsoft Integration Runtime \<IR version> \shared* 입니다.

    2. *diawp.exe.config* 파일을 열고 섹션의 끝에 다음과 같이 추가 합니다 `<runtime>` `<enforceFIPSPolicy enabled="false"/>` .

        ![FIPS 사용 안 함을 보여 주는 diawp.exe.config 파일 섹션의 스크린샷](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. 파일을 저장 하 고 자체 호스팅 IR 컴퓨터를 다시 시작 합니다.


## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
