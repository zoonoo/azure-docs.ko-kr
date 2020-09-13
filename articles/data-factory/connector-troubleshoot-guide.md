---
title: Azure Data Factory 커넥터 문제 해결
description: Azure Data Factory의 커넥터 문제를 해결하는 방법을 알아봅니다.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 62a5f3b18d4b8329c4a15086bc23d09805b786ab
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89668895"
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

    - **Insert**를 쓰기 동작으로 사용하는 경우 이 오류는 원본 데이터에 동일한 ID의 행/개체가 있음을 의미합니다.

    - **Upsert**을 쓰기 동작으로 사용하고 컨테이너에 대해 다른 고유 키를 설정하면 이 오류가 발생합니다. 이 오류는 원본 데이터에 ID는 다르지만 정의된 고유 키 값이 동일한 행/개체가 있음을 의미합니다.

- **해결 방법**: 

    - 원인 1의 경우 **Upsert**를 쓰기 동작으로 설정합니다.
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

    2. **writeBatchSize**를 더 작은 값(예: 1000)으로 줄이고 **parallelCopies**를 더 작은 값(예: 1)으로 설정합니다. 이렇게 하면 복사 실행 성능은 현재보다 저하되지만 Cosmos DB 비용은 증가하지 않습니다.

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


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>오류 메시지: 기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대 한 트러스트 관계를 설정할 수 없습니다.

- **증상**: 복사 작업이 실패 하 고 다음 오류가 발생 합니다. 

    ```
    Message: Failure happened on 'Sink' side. ErrorCode=UserErrorFailedFileOperation,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Upload file failed at path STAGING/PLANT/INDIARENEWABLE/LiveData/2020/01/14\\20200114-0701-oem_gibtvl_mannur_data_10min.csv.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Net.WebException,Message=The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.,Source=System,''Type=System.Security.Authentication.AuthenticationException,Message=The remote certificate is invalid according to the validation procedure.,Source=System,'.
    ```

- **원인**: TLS 핸드셰이크 중 인증서 유효성 검사에 실패 했습니다.

- **해결**방법: 해결 방법: 준비 된 복사를 사용 하 여 ADLS Gen1에 대 한 TLS 유효성 검사를 건너뜁니다. 이 문제를 재현 하 고 netmon 추적을 수집한 다음 네트워크 팀과 협력 하 여 로컬 네트워크 구성을 확인 해야 합니다.

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
                  

## <a name="azure-synapse-analytics-formerly-sql-data-warehouseazure-sql-databasesql-server"></a>Azure Synapse Analytics (이전의 SQL Data Warehouse)/azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>오류 코드:  SqlFailedToConnect

- **메시지**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **원인**: 오류 메시지에 "SqlException"이 포함되어 있으면 SQL Database는 특정 작업이 실패했음을 나타내는 오류를 throw합니다.

- **권장 사항**: 자세한 내용은이 참조 문서에서 SQL 오류 코드를 검색 https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 합니다. 추가 도움이 필요하면 Azure SQL 지원 서비스에 문의하세요.

- **원인**: Azure SQL Database에 연결하려고 할 경우 오류 메시지로 "IP 주소가 '...'인 클라이언트가 서버에 액세스할 수 없습니다."가 표시되면 일반적으로 Azure SQL Database 방화벽 문제 때문인 것입니다.

- **권장 사항**: 논리 SQL server 방화벽 구성에서 "Azure 서비스 및 리소스에서이 서버에 액세스할 수 있도록 허용" 옵션을 사용 하도록 설정 합니다. 참조 문서: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure


### <a name="error-code--sqloperationfailed"></a>오류 코드:  SqlOperationFailed

- **메시지**: `A database operation failed. Please search error to get more details.`

- **원인**: 오류 메시지에 "SqlException"이 포함되어 있으면 SQL Database는 특정 작업이 실패했음을 나타내는 오류를 throw합니다.

- **권장 사항**:  SQL 오류가 명확하지 않은 경우 데이터베이스를 최신 호환성 수준 '150'으로 변경하세요. 최신 버전의 SQL 오류를 throw할 수 있습니다. [세부 정보 문서](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)를 참조 하세요.

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
            

## <a name="delimited-text-format"></a>구분된 텍스트 형식

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>오류 코드:  DelimitedTextColumnNameNotAllowNull

- **메시지**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **원인**: 작업에서 'firstRowAsHeader'를 설정하는 경우 첫 번째 행이 열 이름으로 사용됩니다. 이 오류는 첫 번째 행에 빈 값이 포함되어 있음을 의미합니다. 예: ' ColumnA,, Columna '.

- **권장 사항**:  첫 번째 행을 확인하고 빈 값이 있으면 값을 수정합니다.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>오류 코드:  DelimitedTextMoreColumnsThanDefined

- **메시지**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **원인**: 문제가 있는 행의 열 개수가 첫 번째 행의 열 개수보다 큽니다. 데이터 문제 또는 잘못된 열 구분 기호/따옴표 문자 설정으로 인한 것일 수 있습니다.

- **권장 사항**: 오류 메시지에서 행 개수를 가져오고 행의 열을 확인 하 고 데이터를 수정 합니다.

- **원인**: 예상 열 개수가 오류 메시지에 "1"로 표시되면 잘못된 압축 또는 형식 설정을 지정했기 때문에 ADF에서 파일을 잘못 구문 분석하게 되는 것일 수 있습니다.

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

- **메시지**: `File is not a valid parquet file.`

- **원인**: Parquet 파일 문제입니다.

- **권장 사항**:  입력이 유효한 parquet 파일인지 확인합니다.


### <a name="error-code--parquetnotsupportedtype"></a>오류 코드:  ParquetNotSupportedType

- **메시지**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **원인**: Parquet 형식은 Azure Data Factory에서 지원되지 않습니다.

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

- **메시지**: `The given interpretation '%interpretation;' of parquet format is not supported.`

- **원인**: 지원되지 않는 시나리오

- **권장 사항**:  'ParquetInterpretFor'는 'sparkSql'이 아니어야 합니다.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>오류 코드:  ParquetUnsupportFileLevelCompressionOption

- **메시지**: `File level compression is not supported for Parquet.`

- **원인**: 지원되지 않는 시나리오

- **권장 사항**:  페이로드에서 'CompressionType'을 제거합니다.



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
*  [Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
            
