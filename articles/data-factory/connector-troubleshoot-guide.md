---
title: Azure Data Factory 커넥터 문제 해결
description: Azure Data Factory의 커넥터 문제를 해결하는 방법을 알아봅니다.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: a7a81a742922d45be965c7f73e3cb910d0ef989a
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109296"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 커넥터에 대한 일반적인 문제 해결 방법을 살펴봅니다.
  
## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>오류 코드:  AzureBlobOperationFailed

- **메시지**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **원인**: Blob 스토리지 작업 중 문제가 발생합니다.

- **권장 사항**:  오류 정보를 확인하세요. Blob 도움말 문서 https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes 를 참조하세요. 도움이 필요하면 스토리지 팀에 문의하세요.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>오류 코드:  AzureBlobServiceNotReturnExpectedDataLength

- **메시지**: `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>오류 코드:  AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **메시지**: `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>오류 코드:  AzureStorageOperationFailedConcurrentWrite

- **메시지**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="invalid-property-during-copy-activity"></a>복사 작업 중에 속성이 잘못 되었습니다.

- **메시지**:  `Copy activity <Activity Name> has an invalid "source" property. The source type is not compatible with the dataset <Dataset Name> and its linked service <Linked Service Name>. Please verify your input against.`

- **원인**: 데이터 집합에 정의 된 형식이 복사 활동에 정의 된 소스/싱크 유형과 일치 하지 않습니다.

- **해결** 방법: 데이터 집합 또는 파이프라인 JSON 정의를 편집 하 여 형식을 일관성 있게 만들고 배포를 다시 실행 합니다.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>오류 메시지: 요청 크기가 너무 큽니다.

- **증상**: 기본 쓰기 배치 크기로 Azure Cosmos DB에 데이터를 복사하면 *"**요청 크기가 너무 큼**"* 오류가 발생합니다.

- **원인**: Cosmos DB는 단일 요청의 크기를 2MB로 제한합니다. 수식은 요청 크기 = 단일 문서 크기 * 쓰기 일괄 처리 크기입니다. 문서 크기가 크면 기본 동작으로 인해 요청 크기가 너무 커집니다. 쓰기 일괄 처리 크기를 조정할 수 있습니다.

- **해결 방법**: 복사 작업 싱크에서 '쓰기 배치 크기' 값을 줄입니다(기본값은 10000).

### <a name="error-message-unique-index-constraint-violation"></a>오류 메시지: 고유한 인덱스 제약 조건 위반

- **증상**: Cosmos DB로 데이터를 복사하는 경우 다음 오류가 발생합니다.

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **원인**: 다음 두 가지 가능한 원인이 있습니다.

    - **Insert** 를 쓰기 동작으로 사용하는 경우 이 오류는 원본 데이터에 동일한 ID의 행/개체가 있음을 의미합니다.

    - **Upsert** 을 쓰기 동작으로 사용하고 컨테이너에 대해 다른 고유 키를 설정하면 이 오류가 발생합니다. 이 오류는 원본 데이터에 ID는 다르지만 정의된 고유 키 값이 동일한 행/개체가 있음을 의미합니다.

- **해결 방법**: 

    - 원인 1의 경우 **Upsert** 를 쓰기 동작으로 설정합니다.
    - 원인 2의 경우 각 문서의 정의된 고유 키 값이 서로 다른지 확인합니다.

### <a name="error-message-request-rate-is-large"></a>오류 메시지: 요청 빈도가 높습니다.

- **증상**: Cosmos DB로 데이터를 복사하는 경우 다음 오류가 발생합니다.

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **원인**: 사용된 요청 단위가 Cosmos DB에서 구성된 사용 가능한 RU보다 큽니다. [여기](../cosmos-db/request-units.md#request-unit-considerations)에서 Cosmos DB가 RU를 계산하는 방법을 알아봅니다.

- **해결 방법**: 두 가지 해결 방법이 있습니다.

    1. Cosmos DB에서 **컨테이너 RU를 더 큰 값으로 늘립니다**. 이렇게 하면 복사 작업 성능은 향상되지만 Cosmos DB 비용이 증가합니다. 

    2. **writeBatchSize** 를 더 작은 값(예: 1000)으로 줄이고 **parallelCopies** 를 더 작은 값(예: 1)으로 설정합니다. 이렇게 하면 복사 실행 성능은 현재보다 저하되지만 Cosmos DB 비용은 증가하지 않습니다.

### <a name="column-missing-in-column-mapping"></a>열 매핑에서 열이 누락되었습니다.

- **증상**: 열 매핑에 대한 Cosmos DB 스키마를 가져오는 경우 일부 열이 누락됩니다. 

- **원인**: ADF는 처음 10개의 Cosmos DB 문서에서 스키마를 유추합니다. 해당 문서에 일부 열/속성의 값이 없는 경우 ADF에서 검색되지 않으므로 표시 되지 않습니다.

- **해결 방법**: 아래와 같이 쿼리를 조정하여 값이 비어 있는 열이 결과 세트에 표시되도록 할 수 있습니다(가정: 처음 10개 문서에 "impossible" 열이 누락됨). 또는 매핑을 위한 열을 수동으로 추가할 수 있습니다.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>오류 메시지: 판독기에 대한 GuidRepresentation이 CSharpLegacy입니다.

- **증상**: Cosmos DB MongoAPI/MongoDB에서 UUID 필드가 있는 데이터를 복사하는 경우 다음 오류가 발생합니다.

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **원인**: BSON에서 UUID를 나타내는 방법에는 UuidStardard 및 UuidLegacy의 두 가지가 있습니다. 기본적으로 UuidLegacy는 데이터를 읽는 데 사용됩니다. MongoDB의 UUID 데이터가 UuidStandard인 경우 오류가 발생합니다.

- **해결 방법**: MongoDB 연결 문자열에서 "**uuIDRepresentation=standard**" 옵션을 추가합니다. 자세한 내용은 [MongoDB 연결 문자열](connector-mongodb.md#linked-service-properties)을 참조하세요.
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>오류 코드:  AdlsGen2OperationFailed

- **메시지**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **원인**: ADLS Gen2에서 작업이 실패했음을 나타내는 오류를 throw합니다.

- **권장 사항**:  ADLS Gen2에서 throw한 자세한 오류 메시지를 확인하세요. 이 오류가 일시적인 문제로 인해 발생한 경우 다시 시도하세요. 추가 지원이 필요한 경우 Azure Storage 지원 서비스에 문의하고 오류 메시지에 요청 ID를 제공하세요.

- **원인**: 오류 메시지에 '사용할 수 없음'이 포함되어 있는 경우 사용하는 서비스 주체 또는 관리 ID에 ADLS Gen2에 액세스할 수 있는 권한이 없을 수 있습니다.

- **권장 사항**:  도움말 문서 https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication 을 참조하세요.

- **원인**: 오류 메시지에 'InternalServerError'가 포함되어 있으면 ADLS Gen2에서 오류를 반환한 것입니다.

- **권장 사항**:  이 오류가 일시적인 문제로 인해 발생한 경우 다시 시도하세요. 문제가 지속되면 Azure Storage 지원 서비스에 문의하고 오류 메시지에 요청 ID를 제공하세요.


### <a name="error-code--adlsgen2invalidurl"></a>오류 코드:  AdlsGen2InvalidUrl

- **메시지**: `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>오류 코드:  AdlsGen2InvalidFolderPath

- **메시지**: `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>오류 코드:  AdlsGen2OperationFailedConcurrentWrite

- **메시지**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code-adlsgen2timeouterror"></a>오류 코드: AdlsGen2TimeoutError

- **메시지**: `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


### <a name="request-to-adls-gen2-account-met-timeout-error"></a>ADLS Gen2 계정에 대 한 요청 시간 초과 오류가 발생 했습니다.

- **메시지**: 오류 코드 = `UserErrorFailedBlobFSOperation` , 오류 메시지 = `BlobFS operation failed for: A task was canceled` .

- **원인**:이 문제는 일반적으로 자체 호스팅 IR 컴퓨터에서 발생 하는 ADLS Gen2 싱크 시간 제한 오류로 인해 발생 합니다.

- **권장 사항**: 

    1. 가능 하면 자체 호스팅 IR 컴퓨터와 대상 ADLS Gen2 계정을 동일한 지역에 두십시오. 이렇게 하면 임의 시간 초과 오류가 발생 하지 않고 성능이 향상 됩니다.

    1. Express 경로와 같은 특수 한 네트워크 설정이 있는지 확인 하 고 네트워크에 충분 한 대역폭이 있는지 확인 합니다. 전체 대역폭이 낮을 때 자체 호스팅 IR 동시 작업 설정을 낮추는 것이 좋습니다 .이 경우를 통해 여러 동시 작업에서 네트워크 리소스 경쟁을 방지할 수 있습니다.

    1. 파일 크기가 보통 또는 작은 경우 이진이 아닌 복사에 작은 블록 크기를 사용 하 여 이러한 시간 제한 오류를 완화할 수 있습니다. [Blob Storage Put 블록](https://docs.microsoft.com/rest/api/storageservices/put-block)을 참조 하세요.

       사용자 지정 블록 크기를 지정 하려면 다음을 수행 합니다. json 편집기에서 속성을 편집할 수 있습니다.
    ```
    "sink": {
        "type": "DelimitedTextSink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings",
            "blockSizeInMB": 8
        }
    }
    ```


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>오류 메시지: 기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대 한 트러스트 관계를 설정할 수 없습니다.

- **증상**: 복사 작업이 실패 하 고 다음 오류가 발생 합니다. 

    ```
    Message: Failure happened on 'Sink' side. ErrorCode=UserErrorFailedFileOperation,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Upload file failed at path STAGING/PLANT/INDIARENEWABLE/LiveData/2020/01/14\\20200114-0701-oem_gibtvl_mannur_data_10min.csv.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Net.WebException,Message=The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.,Source=System,''Type=System.Security.Authentication.AuthenticationException,Message=The remote certificate is invalid according to the validation procedure.,Source=System,'.
    ```

- **원인**: TLS 핸드셰이크 중 인증서 유효성 검사에 실패 했습니다.

- **해결** 방법: 해결 방법: 준비 된 복사를 사용 하 여 ADLS Gen1에 대 한 TLS 유효성 검사를 건너뜁니다. 이 문제를 재현 하 고 netmon 추적을 수집한 다음 네트워크 팀과 협력 하 여 로컬 네트워크 구성을 확인 해야 합니다.

    ![문제 해결 ADLS Gen1](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>오류 메시지: 원격 서버에서 다음과 같은 오류를 반환했습니다. (403) 사용할 수 없음

- **증상**: 다음 오류가 발생하여 복사 작업이 실패합니다. 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **원인**: 한 가지 가능한 원인은 사용자가 사용하는 서비스 주체 또는 관리 ID에 특정 폴더/파일에 대한 액세스 권한이 없기 때문입니다.

- **해결 방법**: 복사해야 하는 모든 폴더와 하위 폴더에 해당 권한을 부여합니다. [이 문서](connector-azure-data-lake-store.md#linked-service-properties)를 참조하세요.

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>오류 메시지: 서비스 주체를 사용하여 토큰에 액세스하지 못했습니다. ADAL 오류: service_unavailable

- **증상**: 다음 오류가 발생하여 복사 작업이 실패합니다.

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **원인**: Azure Active Directory에서 소유하고 있는 STS(서비스 토큰 서버)를 사용할 수 없는 경우(예: 다른 작업 때문에 요청을 처리할 수 없음) HTTP 오류 503을 반환합니다. 

- **해결 방법**: 몇 분 후에 복사 작업을 다시 실행합니다.
                  

## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure Synapse Analytics/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>오류 코드:  SqlFailedToConnect

- **메시지**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **원인**: 오류 메시지에 "SqlException"이 포함되어 있으면 SQL Database는 특정 작업이 실패했음을 나타내는 오류를 throw합니다.

- **권장 사항**: 자세한 내용은이 참조 문서에서 SQL 오류 코드를 검색 https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 합니다. 추가 도움이 필요하면 Azure SQL 지원 서비스에 문의하세요.

- **원인**: Azure SQL Database에 연결하려고 할 경우 오류 메시지로 "IP 주소가 '...'인 클라이언트가 서버에 액세스할 수 없습니다."가 표시되면 일반적으로 Azure SQL Database 방화벽 문제 때문인 것입니다.

- **권장 사항**: 논리 SQL server 방화벽 구성에서 "Azure 서비스 및 리소스에서이 서버에 액세스할 수 있도록 허용" 옵션을 사용 하도록 설정 합니다. 참조 문서: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure


### <a name="error-code--sqloperationfailed"></a>오류 코드:  SqlOperationFailed

- **메시지**: `A database operation failed. Please search error to get more details.`

- **원인**: 오류 메시지에 "SqlException"이 포함되어 있으면 SQL Database는 특정 작업이 실패했음을 나타내는 오류를 throw합니다.

- **권장 사항**:  SQL 오류가 명확하지 않은 경우 데이터베이스를 최신 호환성 수준 '150'으로 변경하세요. 최신 버전의 SQL 오류를 throw할 수 있습니다. [세부 정보 문서](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)를 참조 하세요.

    SQL 문제를 해결하려는 경우 참조 문서 https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 에서 SQL 오류 코드로 검색하여 자세한 내용을 참조하세요. 추가 도움이 필요하면 Azure SQL 지원 서비스에 문의하세요.

- **원인**: 오류 메시지에 "PdwManagedToNativeInteropException"이 포함된 경우 일반적으로 원본 및 싱크 열 크기의 불일치로 인해 발생합니다.

- **권장 사항**:  원본 및 싱크 열의 크기를 확인합니다. 추가 도움이 필요하면 Azure SQL 지원 서비스에 문의하세요.

- **원인**: 오류 메시지에 "InvalidOperationException"이 포함된 경우 일반적으로 잘못된 입력 데이터로 인해 발생합니다.

- **권장 사항**:  문제가 발생하는 행을 식별하려면 복사 작업에서 내결함성 기능을 사용하도록 설정하세요. 이렇게 하면 추가 조사를 위해 문제가 있는 행을 스토리지로 리디렉션할 수 있습니다. 참조 문서: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance



### <a name="error-code--sqlunauthorizedaccess"></a>오류 코드:  SqlUnauthorizedAccess

- **메시지**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **원인**: 자격 증명이 올바르지 않거나 로그인 계정이 SQL Database에 액세스할 수 없습니다.

- **권장 사항**:  로그인 계정에 SQL Database에 액세스할 수 있는 충분한 권한이 있는지 확인합니다.


### <a name="error-code--sqlopenconnectiontimeout"></a>오류 코드:  SqlOpenConnectionTimeout

- **메시지**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **원인**: 일시적인 SQL Database 오류일 수 있습니다.

- **권장 사항**: 더 큰 연결 시간 제한 값을 사용 하 여 연결 된 서비스 연결 문자열 업데이트를 다시 시도 합니다.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>오류 코드:  SqlAutoCreateTableTypeMapFailed

- **메시지**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **원인**: 자동 생성 테이블이 원본 요구 사항을 충족할 수 없습니다.

- **권장 사항**:  '매핑'에서 열 유형을 업데이트하거나 대상 서버에서 싱크 테이블을 수동으로 만드세요.


### <a name="error-code--sqldatatypenotsupported"></a>오류 코드:  SqlDataTypeNotSupported

- **메시지**: `A database operation failed. Check the SQL errors.`

- **원인**: SQL 원본에서 문제가 발생하고 오류가 SqlDateTime 오버플로와 관련된 경우 데이터 값이 논리 형식 범위(1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM)를 초과하는 것입니다.

- **권장 사항**:  원본 SQL 쿼리에서 형식을 문자열로 캐스팅하거나 복사 작업 열 매핑에서 열 형식을 'String'으로 변경합니다.

- **원인**: SQL 싱크에서 문제가 발생하고 오류가 SqlDateTime 오버플로와 관련된 경우 데이터 값이 싱크 테이블의 허용되는 범위를 초과하는 것입니다.

- **권장 사항**:  싱크 테이블에서 해당 열 형식을 'datetime2' 형식으로 업데이트합니다.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>오류 코드:  SqlInvalidDbStoredProcedure

- **메시지**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **원인**: 지정된 저장 프로시저가 잘못되었습니다. 이 오류는 저장 프로시저에서 데이터를 반환하지 않기 때문에 발생할 수 있습니다.

- **권장 사항**:  SQL 도구를 통해 저장 프로시저의 유효성을 검사합니다. 저장 프로시저에서 데이터를 반환할 수 있는지 확인합니다.


### <a name="error-code--sqlinvaliddbquerystring"></a>오류 코드:  SqlInvalidDbQueryString

- **메시지**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **원인**: 지정한 SQL 쿼리가 올바르지 않습니다. 이 오류는 쿼리에서 데이터를 반환하지 않기 때문에 발생할 수 있습니다.

- **권장 사항**:  SQL 도구로 SQL 쿼리의 유효성을 검사합니다. 쿼리가 데이터를 반환할 수 있는지 확인합니다.


### <a name="error-code--sqlinvalidcolumnname"></a>오류 코드:  SqlInvalidColumnName

- **메시지**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **원인**: 열을 찾을 수 없습니다. 구성이 잘못되었을 수 있습니다.

- **권장 사항**:  쿼리의 열, 데이터 세트의 'structure' 및 작업의 'mappings'를 확인합니다.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>오류 코드:  SqlColumnNameMismatchByCaseSensitive

- **메시지**: `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>오류 코드:  SqlBatchWriteTimeout

- **메시지**: `Timeouts in SQL write operation.`

- **원인**: 일시적인 SQL Database 오류일 수 있습니다.

- **권장 사항**: 다시 시도. 문제가 재현되면 Azure SQL 지원 서비스에 문의하세요.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>오류 코드:  SqlBatchWriteTransactionFailed

- **메시지**: `SQL transaction commits failed`

- **원인**: 예외 정보가 트랜잭션 시간 초과를 지속적으로 알리는 경우 Integration Runtime과 데이터베이스 간의 네트워크 대기 시간이 기본 임계값인 30초보다 높습니다.

- **권장 사항**:  120 이상의 '연결 제한 시간' 값으로 SQL 연결된 서비스 연결 문자열을 업데이트하고 작업을 다시 실행하세요.

- **원인**: 예외 세부 정보가 일시적으로 sqlconnection 중단을 알릴 경우 일시적인 네트워크 오류 또는 SQL Database 부작용일 수 있습니다.

- **권장 사항**: 작업을 다시 시도 하 고 SQL Database 측면 메트릭을 검토 합니다.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>오류 코드:  SqlBulkCopyInvalidColumnLength

- **메시지**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **원인**: bcp 클라이언트로부터 잘못된 열 길이를 수신하여 SQL 대량 복사가 실패했습니다.

- **권장 사항**:  문제가 발생하는 행을 식별하려면 복사 작업에서 내결함성 기능을 사용하도록 설정하세요. 이렇게 하면 추가 조사를 위해 문제가 있는 행을 스토리지로 리디렉션할 수 있습니다. 참조 문서: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance


### <a name="error-code--sqlconnectionisclosed"></a>오류 코드:  SqlConnectionIsClosed

- **메시지**: `The connection is closed by SQL Database.`

- **원인**: SQL 연결은 높은 동시 실행 서버가 연결을 종료할 때 SQL Database에 의해 닫힙니다.

- **권장 사항**:  원격 서버에서 SQL 연결을 닫았습니다. 다시 시도 문제가 재현되면 Azure SQL 지원 서비스에 문의하세요.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>오류 코드:  SqlCreateTableFailedUnsupportedType

- **메시지**: `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>오류 메시지: 문자열을 uniqueIDentifier로 변환하지 못했습니다.

- **증상**: 준비 된 복사 및 PolyBase를 사용 하 여 테이블 형식 데이터 원본 (예: SQL Server)에서 Azure Synapse Analytics로 데이터를 복사 하는 경우 다음 오류가 발생 합니다.

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **원인**: Azure Synapse Analytics PolyBase에서 빈 문자열을 GUID로 변환할 수 없습니다.

- **해결 방법**: 복사 작업 싱크의 Polybase 설정에서 "**형식 기본값 사용**" 옵션을 false로 설정합니다.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>오류 메시지: 필요한 데이터 형식: DECIMAL(x,x), 잘못된 값

- **증상**: 준비 된 복사 및 PolyBase를 사용 하 여 테이블 형식 데이터 원본 (예: SQL Server)에서 Azure Synapse Analytics로 데이터를 복사 하는 경우 다음 오류가 발생 합니다.

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **원인**: Azure Synapse Analytics Polybase에서 10 진수 열에 빈 문자열 (null 값)을 삽입할 수 없습니다.

- **해결 방법**: 복사 작업 싱크의 Polybase 설정에서 "**형식 기본값 사용**" 옵션을 false로 설정합니다.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>오류 메시지: Java 예외 메시지: HdfsBridge:: CreateRecordReader

- **증상**: PolyBase를 사용 하 여 Azure Synapse Analytics로 데이터를 복사 하 고 다음 오류를 발생 합니다.

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **원인**: 가능한 원인은 스키마(총 열 너비)가 너무 크기 때문입니다(1MB 초과). 모든 열의 크기를 추가 하 여 대상 Azure Synapse Analytics 테이블의 스키마를 확인 합니다.

    - Int -> 4바이트
    - Bigint -> 8바이트
    - Varchar(n),char(n),binary(n), varbinary(n) -> n바이트
    - Nvarchar(n), nchar(n) -> n*2바이트
    - Date -> 6바이트
    - Datetime/(2), smalldatetime -> 16바이트
    - Datetimeoffset -> 20바이트
    - Decimal -> 19바이트
    - Float -> 8바이트
    - Money -> 8바이트
    - Smallmoney -> 4바이트
    - Real -> 4바이트
    - Smallint -> 2바이트
    - Time -> 12바이트
    - Tinyint-> 1바이트

- **해결 방법**: 열 너비를 1MB 미만으로 줄이세요.

- 또는 Polybase를 사용하지 않도록 설정하여 대량 삽입 방법을 사용합니다.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>오류 메시지: HTTP 조건부 헤더를 사용하여 지정한 조건이 충족되지 않습니다.

- **증상**: SQL 쿼리를 사용 하 여 Azure Synapse Analytics에서 데이터를 가져오고 다음 오류를 적중 합니다.

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **원인**: Azure Storage에서 외부 테이블을 쿼리 하는 동안 Azure Synapse Analytics 적중 문제가 발생 했습니다.

- **해결 방법**: SSMS에서 동일한 쿼리를 실행하고 동일한 결과가 표시되는지 확인합니다. 그렇다면 Azure Synapse Analytics에 대 한 지원 티켓을 열고 Azure Synapse 분석 서버 및 데이터베이스 이름을 제공 하 여 추가로 문제를 해결 합니다.
            

### <a name="low-performance-when-load-data-into-azure-sql"></a>Azure SQL로 데이터를 로드할 때 성능이 낮습니다.

- **증상**: Azure SQL에 데이터를 복사 하는 속도가 느립니다.

- **원인**: 문제의 근본 원인은 주로 Azure SQL 측의 병목 현상에 의해 트리거됩니다. 몇 가지 가능한 원인은 다음과 같습니다.

    1. Azure DB 계층이 충분히 크지 않습니다.

    1. Azure DB DTU 사용량이 100%에 가깝습니다. [성능을 모니터링](https://docs.microsoft.com/azure/azure-sql/database/monitor-tune-overview) 하 고 DB 계층을 업그레이드할 수 있습니다.

    1. 인덱스가 올바르게 설정 되지 않았습니다. 데이터를 로드 하기 전에 모든 인덱스를 제거 하 고 로드가 완료 된 후에 다시 만드십시오.

    1. WriteBatchSize가 스키마 행 크기에 맞게 크지 않습니다. 문제에 대 한 속성을 확장 해 보세요.

    1. 대량 삽입 대신 저장 프로시저를 사용 하 여 성능이 저하 될 것으로 예상 됩니다. 

- **해결** 방법: [복사 작업 성능](https://docs.microsoft.com/azure/data-factory/copy-activity-performance-troubleshooting) 에 대 한 TSG을 참조 하세요.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>성능 계층이 적고 복사 실패가 발생 합니다.

- **증상**: Azure SQL로 데이터를 복사 하는 동안 다음 오류 메시지가 발생 했습니다. `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **원인**: Azure SQL s1이 사용 되며, 이러한 경우 IO 제한에 도달 합니다.

- **해결** 방법: 문제를 해결 하려면 Azure SQL 성능 계층을 업그레이드 하세요. 


### <a name="sql-table-cannot-be-found"></a>SQL 테이블을 찾을 수 없습니다. 

- **증상**: 하이브리드에서 온-프레미스 SQL Server 테이블로 데이터를 복사 하는 동안 오류가 발생 했습니다.`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **원인**: 현재 SQL 계정에 .net SqlBulkCopy에서 발급 한 요청을 실행할 수 있는 권한이 없습니다. WriteToServer.

- **해결** 방법: 더 많은 권한 있는 SQL 계정으로 전환 하세요.


### <a name="string-or-binary-data-would-be-truncated"></a>문자열 또는 이진 데이터가 잘립니다.

- **증상**: 온-프레미스/Azure SQL Server 테이블로 데이터를 복사 하는 동안 오류가 발생 했습니다. 

- **원인**: Cx Sql 테이블 스키마 정의에 예상 보다 길이가 작은 열이 하나 이상 있습니다.

- **해결** 방법: 다음 단계를 수행 하 여 문제를 해결 하십시오.

    1. 오류가 발생 한 행의 문제를 해결 하기 위해 특히 "redirectIncompatibleRowSettings" [내결함성](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)을 적용 합니다.

    1. SQL 테이블 스키마 열 길이를 사용 하 여 리디렉션된 데이터를 다시 확인 하 여 업데이트 해야 하는 열을 확인 합니다.

    1. 테이블 스키마를 적절 하 게 업데이트 합니다.


## <a name="delimited-text-format"></a>구분된 텍스트 형식

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>오류 코드:  DelimitedTextColumnNameNotAllowNull

- **메시지**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **원인**: 작업에서 'firstRowAsHeader'를 설정하는 경우 첫 번째 행이 열 이름으로 사용됩니다. 이 오류는 첫 번째 행에 빈 값이 포함되어 있음을 의미합니다. 예: ' ColumnA, Columna '.

- **권장 사항**:  첫 번째 행을 확인하고 빈 값이 있으면 값을 수정합니다.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>오류 코드:  DelimitedTextMoreColumnsThanDefined

- **메시지**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **원인**: 문제가 있는 행의 열 개수가 첫 번째 행의 열 개수 보다 큽니다. 데이터 문제 또는 잘못된 열 구분 기호/따옴표 문자 설정으로 인한 것일 수 있습니다.

- **권장 사항**: 오류 메시지에서 행 개수를 가져오고 행의 열을 확인 하 고 데이터를 수정 합니다.

- **원인**: 오류 메시지에서 예상 열 개수가 "1" 이면 잘못 된 압축 또는 형식 설정을 지정 했을 수 있습니다. 따라서 ADF에서 파일을 잘못 구문 분석 했습니다.

- **권장 사항**:  형식 설정이 원본 파일과 일치하는지 확인합니다.

- **원인**: 원본이 폴더이면 지정된 폴더에 있는 파일의 스키마가 다를 수 있습니다.

- **권장 사항**:  지정된 폴더에 있는 파일의 스키마가 동일한지 확인합니다.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>오류 코드:  DelimitedTextIncorrectRowDelimiter

- **메시지**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>오류 코드:  DelimitedTextTooLargeColumnCount

- **메시지**: `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>오류 코드:  DelimitedTextInvalidSettings

- **메시지**: `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>오류 코드:  DynamicsCreateServiceClientError

- **메시지**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **원인**: Dynamics 서버 쪽의 일시적 문제입니다.

- **권장 사항**:  파이프라인을 다시 실행합니다. 계속 실패하는 경우 병렬 처리를 줄입니다. 그래도 실패하면 dynamics 지원 서비스에 문의하세요.


### <a name="columns-are-missing-when-previewingimporting-schema"></a>스키마를 미리 보거나 가져올 때 열이 누락 되었습니다.

- **증상**: 스키마를 가져오거나 데이터를 미리 볼 때 일부 열이 누락 될 수 있습니다. 오류 메시지: `The valid structure information (column name and type) are required for Dynamics source.`

- **원인**:이 문제는 기본적으로 설계 되어 있습니다. ADF는 처음 10 개 레코드에 값이 없는 열을 표시할 수 없기 때문입니다. 추가한 열이 올바른 형식 인지 확인 하세요. 

- **권장 사항**: 매핑 탭에서 수동으로 열을 추가 합니다.


## <a name="excel-format"></a>Excel 형식

### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>대량 Excel 파일을 구문 분석할 때 시간 초과 또는 성능 저하

- **증상**:

    1. Excel 데이터 집합을 만들고 연결/저장소에서 스키마를 가져오거나 데이터를 미리 보거나 워크시트를 새로 고칠 때 excel 파일 크기가 큰 경우 시간 초과 오류가 발생할 수 있습니다.

    1. 복사 작업을 사용 하 여 대량 Excel 파일 (>= 100MB)에서 다른 데이터 저장소로 데이터를 복사 하는 경우 성능이 저하 되거나 OOM 문제가 발생할 수 있습니다.

- **원인**: 

    1. 스키마 가져오기, 데이터 미리 보기, excel 데이터 집합의 워크시트 나열 등의 작업을 수행 하려면 시간 제한이 100 및 정적입니다. 대량 Excel 파일의 경우 이러한 작업은 제한 시간 값 내에 완료 되지 않을 수 있습니다.

    2. ADF 복사 작업은 전체 Excel 파일을 메모리로 읽은 다음 지정 된 워크시트와 데이터를 읽을 셀을 찾습니다. 이 동작은 기본 SDK ADF에서 사용 되기 때문에 발생 합니다.

- **해결 방법**: 

    1. 스키마를 가져올 때 원본 파일의 하위 집합인 작은 샘플 파일을 생성 하 고 "연결/저장소에서 스키마 가져오기" 대신 "샘플 파일에서 스키마 가져오기"를 선택할 수 있습니다.

    2. 목록 워크시트의 경우 워크시트 드롭다운에서 "편집"을 클릭 하 고 시트 이름/인덱스를 대신 입력할 수 있습니다.

    3. 대량 excel 파일 (>100MB)을 다른 저장소로 복사 하려면 스포츠 스트리밍이 읽고 수행 하는 데이터 흐름 Excel 원본을 사용할 수 있습니다.


## <a name="hdinsight"></a>HDInsight

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>HDInsight ESP 클러스터를 사용 하 여 ADF 연결 된 서비스에 대 한 SSL 오류

- **메시지**: `Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **원인**:이 문제는 시스템 신뢰 저장소와 관련 된 것일 수 있습니다.

- **해결** 방법: **Microsoft Integration RUNTIME\4.0\SHARED\ODBC Drivers\Microsoft Hive ODBC Driver\lib** 경로로 이동 하 고 DriverConfiguration64.exe를 열어 설정을 변경할 수 있습니다.

    ![시스템 신뢰 저장소 사용 선택을 취소 합니다.](./media/connector-troubleshoot-guide/system-trust-store-setting.png)


## <a name="json-format"></a>JSON 형식

### <a name="error-code--jsoninvalidarraypathdefinition"></a>오류 코드:  JsonInvalidArrayPathDefinition

- **메시지**: `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>오류 코드:  JsonEmptyJObjectData

- **메시지**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>오류 코드:  JsonNullValueInPathDefinition

- **메시지**: `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>오류 코드:  JsonUnsupportedHierarchicalComplexValue

- **메시지**: `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>오류 코드:  JsonConflictPartitionDiscoverySchema

- **메시지**: `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>오류 코드:  JsonInvalidDataFormat

- **메시지**: `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>오류 코드:  JsonInvalidDataMixedArrayAndObject

- **메시지**: `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>오류 코드: ArgumentOutOfRangeException

- **메시지**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **원인**: ADF에서 DateTime 값은 0001-01-01 00:00:00에서 9999-12-31 23:59:59 사이의 범위에서 지원 됩니다. 그러나 Oracle은 더 광범위 한 범위의 DateTime 값 (예: BC 세기 또는 min/sec>59)을 지원 하므로 ADF에서 오류가 발생 합니다.

- **권장 사항**: 

    `select dump(<column name>)`Oracle의 값이 ADF의 범위에 있는지 확인 하려면를 실행 하세요. 

    결과에서 바이트 시퀀스를 확인 하려는 경우를 확인 하세요 https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle .


## <a name="parquet-format"></a>Parquet 형식

### <a name="error-code--parquetjavainvocationexception"></a>오류 코드:  ParquetJavaInvocationException

- **메시지**: `An error occurred when invoking java, message: %javaException;.`

- **원인**: 오류 메시지에 'java.lang.OutOfMemory', 'Java 힙 공간' 및 'doubleCapacity'가 포함된 경우 일반적으로 이전 버전의 Integration Runtime에 메모리 관리 문제가 있는 것입니다.

- **권장 사항**: 자체 호스팅 Integration Runtime를 사용 중이 고 버전이 3.20.7159.1 이전 버전인 경우 최신 버전으로 업그레이드 하는 것이 좋습니다.

- **원인**: 오류 메시지에 'java.lang.OutOfMemory'가 포함된 경우 Integration Runtime에 파일을 처리할 수 있는 충분한 리소스가 없는 것입니다.

- **권장 사항**:  Integration Runtime의 동시 실행을 제한합니다. 자체 호스팅 Integration Runtime의 경우 메모리가 8GB보다 크거나 같은 강력한 머신으로 확장합니다.

- **원인**: 오류 메시지에 'NullPointerReference'가 포함된 경우 일시적인 오류일 수 있습니다.

- **권장 사항**: 다시 시도. 문제가 계속되면 지원 사이트에 문의하세요.


### <a name="error-code--parquetinvalidfile"></a>오류 코드:  ParquetInvalidFile

- **메시지**: `File is not a valid Parquet file.`

- **원인**: Parquet 파일 문제입니다.

- **권장 사항**: 입력이 올바른 Parquet 파일 인지 확인 합니다.


### <a name="error-code--parquetnotsupportedtype"></a>오류 코드:  ParquetNotSupportedType

- **메시지**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **원인**: Azure Data Factory에서는 Parquet 형식이 지원 되지 않습니다.

- **권장 사항**:  원본 데이터를 한번 더 확인합니다. https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs 문서를 참조하세요.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>오류 코드:  ParquetMissedDecimalPrecisionScale

- **메시지**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **원인**: 숫자 전체 자릿수 및 소수 자릿수를 구문 분석하려고 하지만 해당 정보가 제공되지 않았습니다.

- **권장 사항**:  '원본'이 올바른 전체 자릿수 및 소수 자릿수를 반환하지 않습니다. 문제 열 전체 자릿수 및 소수 자릿수를 확인합니다.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>오류 코드:  ParquetInvalidDecimalPrecisionScale

- **메시지**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **원인**: 스키마가 유효하지 않습니다.

- **권장 사항**:  문제 열 전체 자릿수 및 소수 자릿수를 확인합니다.


### <a name="error-code--parquetcolumnnotfound"></a>오류 코드:  ParquetColumnNotFound

- **메시지**: `Column %column; does not exist in Parquet file.`

- **원인**: 원본 스키마가 싱크 스키마와 일치하지 않습니다.

- **권장 사항**:  '작업'의 '매핑'을 확인하세요. 원본 열을 올바른 싱크 열에 매핑할 수 있는지 확인합니다.


### <a name="error-code--parquetinvaliddataformat"></a>오류 코드:  ParquetInvalidDataFormat

- **메시지**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **원인**: 데이터를 mappings.source에 지정된 형식으로 변환할 수 없습니다.

- **권장 사항**:  원본 데이터를 한번 더 확인하거나 복사 작업 열 매핑에서 이 열에 올바른 데이터 형식을 지정합니다. https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs 문서를 참조하세요.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>오류 코드:  ParquetDataCountNotMatchColumnCount

- **메시지**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **원인**: 원본 열 개수 및 싱크 열 개수가 일치하지 않습니다.

- **권장 사항**:  원본 열 수가 '매핑'의 싱크 열 수와 같은지 확인합니다.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>오류 코드:  ParquetDataTypeNotMatchColumnType

- **메시지**: 데이터 형식 %srcType;이(가) 열 '%columnIndex;'의 지정된 열 유형 %dstType;과(와) 일치하지 않습니다.

- **원인**: 원본의 데이터를 싱크에 정의된 형식으로 변환할 수 없습니다.

- **권장 사항**: 매핑 싱크에서 올바른 형식을 지정 하십시오.


### <a name="error-code--parquetbridgeinvaliddata"></a>오류 코드:  ParquetBridgeInvalidData

- **메시지**: `%message;`

- **원인**: 제한 초과 데이터 값

- **권장 사항**: 다시 시도. 문제가 계속되면 지원 팀에 문의하세요.


### <a name="error-code--parquetunsupportedinterpretation"></a>오류 코드:  ParquetUnsupportedInterpretation

- **메시지**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **원인**: 지원되지 않는 시나리오

- **권장 사항**:  'ParquetInterpretFor'는 'sparkSql'이 아니어야 합니다.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>오류 코드:  ParquetUnsupportFileLevelCompressionOption

- **메시지**: `File level compression is not supported for Parquet.`

- **원인**: 지원되지 않는 시나리오

- **권장 사항**:  페이로드에서 'CompressionType'을 제거합니다.


### <a name="error-code--usererrorjniexception"></a>오류 코드: UserErrorJniException

- **메시지**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **원인**: 일부 잘못 된 (전역) 인수가 설정 되었으므로 JVM을 만들 수 없습니다.

- **권장 사항**: 자체 호스팅 IR의 **각 노드** 를 호스트 하는 컴퓨터에 로그인 합니다. 시스템 변수가 다음과 같이 올바르게 설정 되었는지 확인 `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` 합니다. 모든 IR 노드를 다시 시작한 다음 파이프라인을 다시 실행 합니다.


### <a name="arithmetic-overflow"></a>산술 연산 오버플로

- **증상**: Parquet 파일을 복사할 때 오류 메시지가 발생 했습니다. `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **원인**: Oracle에서 Parquet로 파일을 복사 하는 경우에는 현재 정밀도 <= 38 및 정수 부분 <= 20의 길이가 10 진수로만 지원 됩니다. 

- **해결** 방법: 이러한 문제를 해결 하는 열을 VARCHAR2로 변환할 수 있습니다.


### <a name="no-enum-constant"></a>열거형 상수 없음

- **증상**: Parquet 형식으로 데이터를 복사할 때 오류 메시지가 발생 했습니다. `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` 또는: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **원인**: 

    이 문제는 공백이 나 지원 되지 않는 문자 (예:,)로 인해 발생할 수 있습니다. {} Parquet는 이러한 형식을 지원 하지 않으므로 열 이름에 () \n\t =)를 지정 합니다. 

    예를 들어 *contoso (test)* 와 같은 열 이름은 [코드](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) 에서 대괄호로 형식을 구문 분석 합니다 `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . 이러한 "test" 형식이 없기 때문에 오류가 발생 합니다.

    지원 되는 형식을 확인 하려면 [여기](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)에서 확인할 수 있습니다.

- **해결 방법**: 

    1. 싱크 열 이름에 공백이 있는지 다시 한 번 확인 합니다.

    1. 공백이 포함 된 첫 번째 행을 열 이름으로 사용 하는지 여부를 다시 확인 합니다.

    1. OriginalType 형식이 지원 되는지 두 번 확인 합니다. 이러한 특수 기호를 사용 하지 마십시오 `,;{}()\n\t=` . 


## <a name="rest"></a>REST (영문)

### <a name="unexpected-network-response-from-rest-connector"></a>REST 커넥터에서 예기치 않은 네트워크 응답

- **증상**: 끝점이 REST 커넥터에서 예기치 않은 응답 (400/401/403/500)을 수신 하는 경우도 있습니다.

- **원인**: REST 원본 커넥터는 http 요청을 생성할 때 연결 된 서비스/데이터 집합/복사 원본의 URL 및 http 메서드/헤더를 매개 변수로 사용 합니다. 이 문제는 하나 이상의 지정 된 매개 변수에서 일부 실수로 인해 발생 했을 수 있습니다.

- **해결 방법**: 
    - Cmd 창에서 ' 말아 '를 사용 하 여 매개 변수가 원인 인지 여부를 확인 합니다 (**Accept** 및 **사용자 에이전트** 헤더는 항상 포함 되어야 함).
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      명령에서 예기치 않은 동일한 응답을 반환 하는 경우 예상 되는 응답을 반환할 때까지 ' a s e '로 위의 매개 변수를 수정 하세요. 

      또한 명령의 고급 사용을 위해 ' 말아--help '를 사용할 수도 있습니다.

    - ADF REST 커넥터도 예기치 않은 응답을 반환 하는 경우 Microsoft 지원에 문의 하 여 문제를 해결 하세요.
    
    - ' A s s '는 SSL 인증서 유효성 검사 문제를 재현 하는 데 적합 하지 않을 수 있습니다. 일부 시나리오에서는 SSL 인증서 유효성 검사 문제를 해결 하지 않고 ' 말아 ' 명령이 성공적으로 실행 되었습니다. 그러나 동일한 URL이 브라우저에서 실행 되는 경우 클라이언트에서 서버와의 트러스트를 설정 하기 위해 처음에는 SSL 인증서가 실제로 반환 되지 않습니다.

      위의 경우에는 **Postman** 및 **Fiddler** 와 같은 도구를 권장 합니다.


## <a name="sftp"></a>SFTP

### <a name="invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>' SSHPublicKey ' 인증 유형에 대해 제공 된 SFTP 자격 증명이 잘못 되었습니다.

- **증상**: ' SshPublicKey ' 인증 유형에 잘못 된 SFTP 자격 증명이 제공 된 상태에서 SSH 공개 키 인증을 사용 하 고 있습니다.

- **원인**:이 오류는 세 가지 원인으로 인해 발생할 수 있습니다.

    1. 개인 키 콘텐츠는 AKV/SDK에서 인출 되지만 올바르게 인코딩되지 않습니다.

    1. 잘못 된 키 콘텐츠 형식을 선택 했습니다.

    1. 자격 증명 또는 개인 키 콘텐츠가 잘못 되었습니다.

- **해결 방법**: 

    1. **원인 1**:

       개인 키 콘텐츠가 AKV에서 온 것이 고 고객이 SFTP 연결 된 서비스에 직접 업로드 하는 경우 원래 키 파일이 작동 하는 경우

       을 참조 하세요 https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication . privateKey 콘텐츠는 Base64 인코딩 SSH 개인 키 콘텐츠입니다.

       Base64 인코딩을 사용 하 여 **원래 개인 키 파일의 전체 콘텐츠를** 인코딩하고 인코딩된 문자열을 AKV에 저장 하세요. 원본 개인 키 파일은 파일에서 업로드를 클릭 한 경우 SFTP 연결 된 서비스에서 사용할 수 있는 파일입니다.

       문자열을 생성 하는 데 사용 되는 몇 가지 샘플은 다음과 같습니다.

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

       - 타사 base64 변환 도구 사용

         와 같은 도구 https://www.base64encode.org/ 를 권장 합니다.

    1. **원인 2**:

       PKCS # 8 형식 SSH 개인 키를 사용 하는 경우

       PKCS # 8 형식 SSH 개인 키 ("-----시작 암호화 된 개인 키-----")는 현재 ADF의 SFTP 서버에 액세스 하는 데 지원 되지 않습니다. 

       아래 명령을 실행 하 여 키를 기존 SSH 키 형식으로 변환 합니다 ("-----시작 RSA 개인 키-----"로 시작).

       ```
       openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
       chmod 600 traditional_format_key_file
       ssh-keygen -f traditional_format_key_file -p
       ```
    1. **원인 3**:

       WinSCP와 같은 도구를 사용 하 여 키 파일 또는 암호가 올바른지 확인 하세요.


### <a name="incorrect-linked-service-type-is-used"></a>잘못 된 연결 된 서비스 유형이 사용 되었습니다.

- **증상**: FTP/SFTP 서버에 연결할 수 없습니다.

- **원인**: ftp 연결 된 서비스를 사용 하 여 sftp 서버에 연결 하거나 역방향으로 연결 하는 것과 같이 FTP 또는 SFTP 서버에 잘못 된 연결 된 서비스 유형이 사용 됩니다.

- **해결** 방법: 대상 서버의 포트를 확인 하세요. 기본적으로 FTP에서는 포트 21을 사용 하 고 SFTP는 포트 22를 사용 합니다.


### <a name="sftp-copy-activity-failed"></a>SFTP 복사 작업이 실패 했습니다.

- **증상**: 오류 코드: UserErrorInvalidColumnMappingColumnNotFound. 오류 메시지: `Column &apos;AccMngr&apos; specified in column mapping cannot be found in source data.`

- **원인**: 원본에 "AccMngr" 라는 열이 포함 되어 있지 않습니다.

- **해결** 방법: "AccMngr" 열이 있는지 확인 하기 위해 대상 데이터 집합 열을 매핑하여 구성 된 데이터 집합을 두 번 확인 합니다.


### <a name="sftp-server-connection-throttling"></a>SFTP 서버 연결 제한

- **증상**: 서버 응답에 SSH 프로토콜 id가 포함 되어 있지 않으며 복사 하지 못했습니다.

- **원인**: ADF는 sftp 서버에서 동시에 다운로드 하기 위해 여러 연결을 만들며 때로는 sftp 서버 제한에 도달 하는 경우도 있습니다. 실제로 다른 서버는 제한에 도달 하는 경우 다른 오류를 반환 합니다.

- **해결 방법**: 

    SFTP 데이터 집합의 최대 동시 연결 수를 1로 지정 하 고 복사를 다시 실행 하세요. 성공 하면 제한이 원인 임을 확인할 수 있습니다.

    낮은 처리량을 승격 하려면 SFTP 관리자에 게 동시 연결 수 한도를 늘리거나 허용 목록에 다음 IP를 추가 하도록 요청 하세요.

    - 관리 IR을 사용 하는 경우 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 추가 하세요.
      또는 많은 IP 범위 목록을 SFTP 서버 허용 목록에 추가 하지 않으려는 경우 자체 호스팅 IR을 설치할 수 있습니다.

    - 자체 호스팅 IR을 사용 하는 경우 허용 목록에 SHIR을 설치한 컴퓨터 IP를 추가 하세요.


### <a name="error-code-sftprenameoperationfail"></a>오류 코드: SftpRenameOperationFail

- **증상**: 다음 오류가 발생 하 여 파이프라인이 BLOB에서 SFTP로 데이터를 복사 하지 못했습니다. `Operation on target Copy_5xe failed: Failure happened on 'Sink' side. ErrorCode=SftpRenameOperationFail,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException` .

- **원인**: useTempFileRename 옵션은 데이터를 복사할 때 True로 설정 되었습니다. 이렇게 하면 프로세스에서 임시 파일을 사용할 수 있습니다. 전체 데이터를 복사 하기 전에 하나 이상의 임시 파일이 삭제 된 경우이 오류가 트리거됩니다.

- **해결** 방법: useTempFileName의 옵션을 False로 설정 합니다.


## <a name="general-copy-activity-error"></a>일반 복사 작업 오류

### <a name="error-code--jrenotfound"></a>오류 코드:  JreNotFound

- **메시지**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **원인**: 자체 호스팅 Integration Runtime에서는 Java 런타임을 찾을 수 없습니다. Java 런타임은 특정 원본을 읽는 데 필요합니다.

- **권장 사항**:  Integration Runtime 환경을 확인하고 참조 문서 https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime 을 참조하세요.


### <a name="error-code--wildcardpathsinknotsupported"></a>오류 코드:  WildcardPathSinkNotSupported

- **메시지**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **원인**: 싱크 데이터 세트는 와일드카드를 지원하지 않습니다.

- **권장 사항**:  싱크 데이터 세트를 확인하고 와일드카드 값을 사용하지 않고 경로를 수정합니다.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>오류 코드:  MappingInvalidPropertyWithEmptyValue

- **메시지**: `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>오류 코드:  MappingInvalidPropertyWithNamePathAndOrdinal

- **메시지**: `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>오류 코드:  MappingDuplicatedOrdinal

- **메시지**: `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>오류 코드:  MappingInvalidOrdinalForSinkColumn

- **메시지**: `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 질문 페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
