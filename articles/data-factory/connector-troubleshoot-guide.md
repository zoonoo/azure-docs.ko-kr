---
title: Azure Data Factory 커넥터 문제 해결
description: Azure 데이터 팩터리에서 커넥터 문제를 해결하는 방법을 알아봅니다.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9f3a13a097d7cce87aead4ec2d76ce7cbbb1a206
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778229"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory 커넥터 문제 해결

이 문서에서는 Azure 데이터 팩터리에서 커넥터에 대한 일반적인 문제 해결 방법을 살펴봅니다.
  

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>오류 코드: AzureBlob작업 실패

- **메시지**:`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **원인**: Blob 저장소 작업 적중 문제.

- **권장 사항**: 세부 사항에서 오류를 확인합니다. Blob 도움말 문서를 https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes참조하십시오. 도움이 필요한 경우 저장소 팀에 문의하십시오.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>오류 코드: AzureBlobServiceNotReturnExpectedDataLength

- **메시지**:`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>오류 코드: AzureBlobNotSupportMultipleFilesInto싱글블블블

- **메시지**:`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>오류 코드: AzureStorage작업실패동시쓰기

- **메시지**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>오류 메시지: 요청 크기가 너무 큽

- **증상**: 기본 쓰기 배치 크기로 Azure Cosmos DB에 데이터를 복사하고 오류 ***"요청 크기가 너무 큽니다"***.

- **원인**: 코스모스 DB는 단일 요청의 크기를 2MB로 제한합니다. 수식은 요청 크기 = 단일 문서 크기 * 쓰기 일괄 처리 크기입니다. 문서 크기가 큰 경우 기본 동작으로 인해 요청 크기가 너무 커집니다. 쓰기 일괄 처리 크기를 조정할 수 있습니다.

- **해결 방법**: 복사 활동 싱크에서 '일괄 처리 크기 쓰기' 값을 줄입니다(기본값은 10000).

### <a name="error-message-unique-index-constraint-violation"></a>오류 메시지: 고유 인덱스 제약 조건 위반

- **증상**: Cosmos DB에 데이터를 복사할 때 다음과 같은 오류가 발생했습니다.

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **원인**: 두 가지 가능한 원인이 있습니다:

    - **삽입을** 쓰기 비헤이비어로 사용하는 경우 이 오류는 원본 데이터에 동일한 ID를 가진 행/개체가 있는 것을 의미합니다.

    - **Upsert를** 쓰기 동작으로 사용하고 컨테이너에 다른 고유 키를 설정하는 경우 이 오류는 원본 데이터에 다른 아이디가 있지만 정의된 고유 키에 대해 동일한 값을 가진 행/개체가 사용된다는 것을 의미합니다.

- **해상도**: 

    - 원인1의 경우 **Upsert를** 쓰기 동작으로 설정합니다.
    - 원인 2의 경우 정의된 고유 키에 대해 각 문서의 값이 다른지 확인합니다.

### <a name="error-message-request-rate-is-large"></a>오류 메시지: 요청 속도가 클 수 있습니다.

- **증상**: Cosmos DB에 데이터를 복사할 때 다음과 같은 오류가 발생했습니다.

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **원인**: 사용된 요청 단위가 Cosmos DB에서 구성된 사용 가능한 RU보다 큽습니다. 코스모스 DB가 [여기에서](../cosmos-db/request-units.md#request-unit-considerations)RU를 계산하는 방법에 대해 알아보십시오.

- **해결 방법**: 다음은 두 가지 해결 방법입니다.

    1. 코스모스 DB에서 **컨테이너 RU를** 더 큰 값으로 늘려 카피 활동 성능을 향상시킵니다. 

    2. **writeBatchSize를** 작은 값(예: 1000)으로 줄이고 **병렬 복사를** 1과 같은 작은 값으로 설정하면 복사 실행 성능이 현재보다 성능이 저하되지만 Cosmos DB에서는 더 많은 비용이 발생하지 않습니다.

### <a name="column-missing-in-column-mapping"></a>열 매핑에서 누락된 열

- **증상**: 컬럼 매핑을 위해 Cosmos DB에 대한 스키마를 가져올 때 일부 열이 누락됩니다. 

- **원인**: ADF는 처음 10 코스모스 DB 문서에서 스키마를 추론합니다. 일부 열/속성에 해당 문서에 값이 없는 경우 ADF에서 검색되지 않으므로 표시되지 않습니다.

- **해결**방법 : 아래와 같이 쿼리를 조정하여 빈 값으로 결과 집합에 표시하도록 열을 적용할 수 있습니다(처음 10개 문서에서 "불가능" 열이 누락되었다고 가정). 또는 매핑을 위해 열을 수동으로 추가할 수 있습니다.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>오류 메시지: 판독기의 Guid표현은 CSharpLegacy입니다.

- **증상**: 코스모스 DB 몽고API/몽고DB에서 UUID 필드로 데이터를 복사할 때 다음과 같은 오류가 발생했습니다.

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **원인**: BSON에서 UUID를 나타내는 방법은 두 가지가 있습니다 - UuidStardard 및 UuidLegacy. 기본적으로 UuidLegacy는 데이터를 읽는 데 사용됩니다. MongoDB의 UUID 데이터가 UuidStandard인 경우 오류가 발생합니다.

- **해상도**: MongoDB 연결 문자열에서 옵션 "**uuidRepresentation= 표준**"를 추가합니다. 자세한 내용은 [MongoDB 연결 문자열을](connector-mongodb.md#linked-service-properties)참조하십시오.
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>오류 코드: AdlsGen2작업 실패

- **메시지**:`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **원인**: ADLS Gen2에서 작업이 실패했음을 나타내는 오류를 발생시면 됩니다.

- **권장 사항**: ADLS Gen2에서 throw된 자세한 오류 메시지를 확인합니다. 일시적인 오류로 인해 발생하는 경우 다시 시도하십시오. 추가 도움이 필요한 경우 Azure Storage 지원에 문의하여 오류 메시지에서 요청 ID를 제공하십시오.

- **원인**: 오류 메시지에 '금지됨'이 포함된 경우 사용하는 서비스 주체 또는 관리되는 ID가 ADLS Gen2에 액세스할 수 있는 충분한 권한이 없을 수 있습니다.

- **권장 사항**: 도움말 https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication문서를 참조하십시오.

- **원인**: 오류 메시지에 'InternalServerError'가 포함되어 있으면 ADLS Gen2에서 오류가 반환됩니다.

- **권장 사항**: 일시적인 오류로 인해 발생할 수 있으므로 다시 시도하십시오. 문제가 지속되면 Azure Storage 지원에 문의하여 오류 메시지에 요청 ID를 제공하십시오.


### <a name="error-code--adlsgen2invalidurl"></a>오류 코드: AdlsGen2유효Url

- **메시지**:`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>오류 코드: AdlsGen2유효폴더패스

- **메시지**:`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>오류 코드: AdlsGen2작업실패동시쓰기

- **메시지**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>오류 코드: AdlsGen2타임아웃오류

- **메시지**:`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>오류 메시지: 원격 서버가 오류를 반환했습니다: (403) 금지됨

- **증상**: 다음과 같은 오류로 복사 작업이 실패합니다. 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **원인**: 한 가지 가능한 원인은 사용하는 서비스 주체 또는 관리되는 ID가 특정 폴더/파일에 액세스할 수 있는 권한이 없기 때문에 발생할 수 있습니다.

- **해결 방법**: 복사해야 하는 모든 폴더 및 하위 폴더에 해당 권한을 부여합니다. 이 [문서를](connector-azure-data-lake-store.md#linked-service-properties)참조하십시오.

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>오류 메시지: 서비스 주체를 사용하여 액세스 토큰을 얻지 못했습니다. ADAL 오류: service_unavailable

- **증상**: 다음과 같은 오류로 복사 작업이 실패합니다.

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **원인**: Azure Active Directory가 소유한 서비스 토큰 서버(STS)를 사용할 수 없는 경우(예: 요청을 처리하기에 너무 많은 경우) HTTP 오류 503을 반환합니다. 

- **해결 방법**: 몇 분 후에 복사 활동을 다시 실행합니다.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL 데이터 웨어하우스/Azure SQL 데이터베이스/SQL 서버

### <a name="error-code--sqlfailedtoconnect"></a>오류 코드: SqlFailedToConnect

- **메시지**:`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **원인**: 오류 메시지에 "SqlException"이 포함되어 있으면 SQL Database에서 특정 작업이 실패했음을 나타내는 오류를 throw합니다.

- **권장 사항**: 자세한 내용은 이 참조 문서에서 https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorsSQL 오류 코드로 검색하십시오. 추가 도움이 필요한 경우 Azure SQL 지원에 문의하십시오.

- **원인**: 오류 메시지에 'IP 주소가 있는 클라이언트'가 포함된 경우 '...' 서버에 액세스할 수 없습니다", Azure SQL 데이터베이스에 연결 하려고 하는 경우 일반적으로 Azure SQL Database 방화벽 문제로 인해 발생 합니다.

- **권장 사항**: Azure SQL Server 방화벽 구성에서 "Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용" 옵션을 사용하도록 설정합니다. 참조 문서: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>오류 코드: SqlOperation실패

- **메시지**:`A database operation failed. Please search error to get more details.`

- **원인**: 오류 메시지에 "SqlException"이 포함되어 있으면 SQL Database에서 특정 작업이 실패했음을 나타내는 오류를 throw합니다.

- **권장 사항**: SQL 오류가 명확하지 않은 경우 데이터베이스를 최신 호환성 수준 '150'으로 변경하십시오. 최신 버전 SQL 오류를 throw할 수 있습니다. 세부 문서를 참조하십시오 https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat: .
        SQL 문제 해결을 위해 이 참조 문서에서 SQL 오류 코드를 https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors검색하여 자세한 내용을 확인하십시오. 추가 도움이 필요한 경우 Azure SQL 지원에 문의하십시오.

- **원인**: 오류 메시지에 "PdwManagedToNativeInteropException"이 포함되어 있는 경우 일반적으로 소스와 싱크 열 크기 간의 불일치로 인해 발생합니다.

- **권장 사항**: 소스 열과 싱크 열의 크기를 확인합니다. 추가 도움이 필요한 경우 Azure SQL 지원에 문의하십시오.

- **원인**: 오류 메시지에 "잘못된 OperationException"이 포함되어 있으면 일반적으로 잘못된 입력 데이터로 인해 발생합니다.

- **권장 사항**: 문제가 발생한 행을 식별하려면 복사 활동에 대한 내결함성 기능을 활성화하여 문제가 있는 행을 저장소로 리디렉션하여 추가 조사를 수행하십시오. 참조 문서: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>오류 코드: SqlUnauthorizedAccess

- **메시지**:`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **원인**: 자격 증명이 올바르지 않거나 로그인 계정이 SQL Database에 액세스할 수 없습니다.

- **권장 사항**: 로그인 계정에 SQL 데이터베이스에 액세스할 수 있는 충분한 권한이 있는지 확인합니다.


### <a name="error-code--sqlopenconnectiontimeout"></a>오류 코드: SqlOpenConnection타임아웃

- **메시지**:`Open connection to database timeout after '%timeoutValue;' seconds.`

- **원인**: SQL 데이터베이스 일시적인 오류일 수 있습니다.

- **권장 사항**: 연결된 서비스 연결 문자열을 더 큰 연결 시간 시간 값으로 업데이트하려면 다시 시도하십시오.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>오류 코드: Sql자동 만들기 테이블 형식맵 실패

- **메시지**:`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **원인**: 자동 생성 테이블은 소스 요구 사항을 충족할 수 없습니다.

- **권장 사항**: '매핑'에서 열 유형을 업데이트하거나 대상 서버에서 싱크 테이블을 수동으로 만듭니다.


### <a name="error-code--sqldatatypenotsupported"></a>오류 코드: SqlDataTypeNot지원되지 않음

- **메시지**:`A database operation failed. Check the SQL errors.`

- **원인**: SQL 원본에서 문제가 발생하고 오류가 SqlDateTime 오버플로와 관련된 경우 데이터 값은 논리 형식 범위(1/1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM)를 초과합니다.

- **권장 사항**: 소스 SQL 쿼리에서 문자열에 형식을 캐스팅하거나 복사 활동 열 매핑에서 열 형식을 'String'으로 변경합니다.

- **원인**: SQL 싱크에서 문제가 발생하고 오류가 SqlDateTime 오버플로와 관련된 경우 데이터 값은 싱크 테이블의 허용된 범위를 초과합니다.

- **권장 사항**: 해당 열 유형을 싱크 테이블의 'datetime2' 유형으로 업데이트합니다.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>오류 코드: SqlInvalidDb저장프로시저

- **메시지**:`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **원인**: 지정된 저장 프로시저가 잘못되었습니다. 저장 프로시저가 데이터를 반환하지 않기 때문에 발생할 수 있습니다.

- **권장 사항**: SQL 도구에서 저장 프로시저의 유효성을 검사합니다. 저장 프로시저가 데이터를 반환할 수 있는지 확인합니다.


### <a name="error-code--sqlinvaliddbquerystring"></a>오류 코드: SqlInvalidDb쿼리 문자열

- **메시지**:`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **원인**: 지정된 SQL 쿼리가 잘못되었습니다. 쿼리가 데이터를 반환하지 않기 때문에 발생할 수 있습니다.

- **권장 사항**: SQL 도구별로 SQL 쿼리의 유효성을 검사합니다. 쿼리가 데이터를 반환할 수 있는지 확인합니다.


### <a name="error-code--sqlinvalidcolumnname"></a>오류 코드: SqlInvalid열 이름

- **메시지**:`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **원인**: 열을 찾을 수 없습니다. 구성이 잘못되었습니다.

- **권장 사항**: 쿼리의 열, 데이터 집합의 '구조', 활동의 '매핑'을 확인합니다.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>오류 코드: SqlColumn이름 불일치대문자 감지

- **메시지**:`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>오류 코드: SqlBatchWrite타임아웃

- **메시지**:`Timeouts in SQL write operation.`

- **원인**: SQL 데이터베이스 일시적인 오류일 수 있습니다.

- **권장 사항**: 다시 시도하십시오. 문제 재현하는 경우 Azure SQL 지원에 문의하십시오.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>오류 코드: SqlBatchWrite트랜잭션실패

- **메시지**:`SQL transaction commits failed`

- **원인**: 예외 세부 정보가 트랜잭션 시간 초과를 지속적으로 알려주면 통합 런타임과 데이터베이스 간의 네트워크 대기 시간이 기본 임계값보다 30초 더 높습니다.

- **권장 사항**: Sql 연결 서비스 연결 문자열을 '연결 시간 초과' 값이 120 이상인 업데이트하고 활동을 다시 실행합니다.

- **원인**: 예외 세부 정보가 간헐적으로 sqlconnection이 끊어졌다고 말하는 경우 일시적인 네트워크 오류 또는 SQL Database 측 문제일 수 있습니다.

- **권장 사항**: 활동을 다시 시도하고 SQL Database 측면 메트릭을 검토하십시오.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>오류 코드: SqlBulkCopy유효열길이

- **메시지**:`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **원인**: SQL 대량 복사는 bcp 클라이언트에서 잘못된 열 길이를 수신하여 실패했습니다.

- **권장 사항**: 문제가 발생한 행을 식별하려면 복사 활동에 대한 내결함성 기능을 활성화하여 문제가 있는 행을 저장소로 리디렉션하여 추가 조사를 수행하십시오. 참조 문서: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>오류 코드: SqlConnectionIsClosed

- **메시지**:`The connection is closed by SQL Database.`

- **원인**: 높은 동시 실행 및 서버 연결을 종료 할 때 SQL 데이터베이스가 SQL 연결을 닫습니다.

- **권장 사항**: 원격 서버가 SQL 연결을 닫았습니다. 다시 시도하세요. 문제 재현하는 경우 Azure SQL 지원에 문의하십시오.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>오류 코드: SqlCreateTableFailed지원되지 않음 유형

- **메시지**:`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>오류 메시지: 문자 문자열에서 고유 식별자로 변환할 때 변환에 실패했습니다.

- **증상**: 테이블 형식의 데이터 원본(예: SQL Server)의 데이터를 단계별 복사 및 PolyBase를 사용하여 Azure SQL 데이터 웨어하우스로 복사하면 다음과 같은 오류가 발생합니다.

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **원인**: Azure SQL 데이터 웨어하우스 PolyBase는 빈 문자열을 GUID로 변환할 수 없습니다.

- **해결 방법**: Polybase 설정에서 복사 활동 싱크에서 **"사용 유형 기본값"** 옵션을 false로 설정합니다.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>오류 메시지: 예상 데이터 유형: DECIMAL(x, x), 잘못된 값

- **증상**: 테이블 형식의 데이터 원본(예: SQL Server)의 데이터를 단계별 복사 및 PolyBase를 사용하여 SQL DW로 복사하면 다음과 같은 오류가 발생합니다.

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **원인**: Azure SQL 데이터 웨어하우스 Polybase는 빈 문자열(null 값)을 소수자 열에 삽입할 수 없습니다.

- **해결 방법**: Polybase 설정에서 복사 활동 싱크에서 **"사용 유형 기본값"** 옵션을 false로 설정합니다.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>오류 메시지: 자바 예외 메시지:HdfsBridge::CreateRecordReader

- **증상**: PolyBase를 사용하여 데이터를 Azure SQL 데이터 웨어하우스로 복사하고 다음 오류가 발생했습니다.

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **원인**: 가능한 원인은 스키마 (총 열 너비)가 너무 커서 (1 MB보다 큽니다)입니다. 모든 열의 크기를 추가하여 대상 SQL DW 테이블의 스키마를 확인합니다.

    - Int -> 4바이트
    - 빅인트 -> 8바이트
    - Varchar(n), char(n), 바이너리(n), varbinary(n) -> n 바이트
    - Nvarchar(n), nchar(n) -> n*2 바이트
    - 날짜 -> 6바이트
    - 날짜 시간/(2), 작은날짜시간 -> 16바이트
    - 날짜 시간 오프셋 -> 20바이트
    - 소수점 -> 19바이트
    - 플로트 -> 8바이트
    - 돈 -> 8 바이트
    - 스몰머니 -> 4바이트
    - 실제 -> 4바이트
    - 스몰틴트 -> 2바이트
    - 시간 -> 12바이트
    - 타이니틴트 -> 1바이트

- **해상도**: 열 너비를 1MB 미만으로 줄입니다.

- 또는 Polybase를 사용하지 않도록 설정하여 대량 삽입 방식을 사용

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>오류 메시지: HTTP 조건부 헤더를 사용하여 지정된 조건이 충족되지 않습니다.

- **증상**: SQL 쿼리를 사용하여 Azure SQL 데이터 웨어하우스에서 데이터를 가져오고 다음과 같은 오류가 발생합니다.

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **원인**: Azure SQL 데이터 웨어하우스가 Azure Storage의 외부 테이블을 쿼리하는 문제를 발생했습니다.

- **해결 방법**: SSMS에서 동일한 쿼리를 실행하고 동일한 결과가 표시되는지 확인합니다. 동일한 결과가 표시된다면 Azure SQL Data Warehouse에 대한 지원 티켓을 열고 SQL DW 서버와 데이터베이스 이름을 제공하여 추가로 문제를 해결합니다.
            

## <a name="delimited-text-format"></a>구분 기호로 분리된 텍스트 형식

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>오류 코드: 디미지된텍스트열이름NotAllowNull

- **메시지**:`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **원인**: 활동에서 'firstRowAsHeader'를 설정하면 첫 번째 행이 열 이름으로 사용됩니다. 이 오류는 첫 번째 행에 빈 값이 포함되어 있음을 의미합니다. 예: 'ColumnA,,ColumnB'.

- **권장 사항**: 첫 번째 행을 확인하고 빈 값이 있는 경우 값을 수정합니다.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>오류 코드: 디미지된텍스트모어열정의

- **메시지**:`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **원인**: 문제가 있는 행의 열 수가 첫 번째 행의 열 개수보다 큽습니다. 데이터 문제 또는 잘못된 열 구분 기호/견적 char 설정으로 인해 발생할 수 있습니다.

- **권장 사항**: 오류 메시지에서 행 수를 얻고 행의 열을 확인하고 데이터를 수정하십시오.

- **원인**: 예상 열 수가 오류 메시지에서 "1"인 경우 잘못된 압축 또는 형식 설정을 지정하여 ADF가 파일을 잘못 구문 분석할 수 있습니다.

- **권장 사항**: 형식 설정을 확인하여 소스 파일과 일치하는지 확인합니다.

- **원인**: 소스가 폴더인 경우 지정된 폴더 아래에 있는 파일에 다른 스키마가 있을 수 있습니다.

- **권장 사항**: 지정된 폴더 아래에 있는 파일에 스키마가 동일한지 확인합니다.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>오류 코드: 구분된텍스트잘못된로디이미터

- **메시지**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>오류 코드: 디미지된텍스트TooLarge열카운트

- **메시지**:`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>오류 코드: 디미지된텍스트유효설정

- **메시지**:`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>역학 365/공통 데이터 서비스/역학 CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>오류 코드: 역학만들기서비스클라이언트오류

- **메시지**:`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **원인**: 이것은 역학 서버 측에서 일시적인 문제입니다.

- **권장 사항**: 파이프라인을 다시 실행합니다. 계속 실패하면 병렬 처리방법을 줄이십시오. 그래도 실패하면 역학 지원에 문의하십시오.



## <a name="json-format"></a>JSON 형식

### <a name="error-code--jsoninvalidarraypathdefinition"></a>오류 코드: JsonInvalidArrayPath정의정의

- **메시지**:`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>오류 코드: JsonEmptyJ개체데이터

- **메시지**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>오류 코드: JsonNullValueInPath정의

- **메시지**:`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>오류 코드: JsonUnsupportedHierical복합가치

- **메시지**:`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>오류 코드: JsonConflict분할디스커버리스키마

- **메시지**:`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>오류 코드: JsonInvalid데이터 형식

- **메시지**:`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>오류 코드: JsonInvalidDataMixedArrayAndObject

- **메시지**:`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Parquet 형식

### <a name="error-code--parquetjavainvocationexception"></a>오류 코드: 마루자바인호출예외

- **메시지**:`An error occurred when invoking java, message: %javaException;.`

- **원인**: 오류 메시지에 'java.lang.OutOfMemory', 'Java 힙 공간' 및 'doubleCapacity'이 포함되어 있는 경우 일반적으로 이전 버전의 통합 런타임에서 메모리 관리 문제입니다.

- **권장 사항**: 자체 호스팅 통합 런타임을 사용하고 있고 버전이 3.20.7159.1보다 이전인 경우 최신 버전으로 업그레이드하는 것이 좋습니다.

- **원인**: 오류 메시지에 'java.lang.OutOfMemory'가 포함되어 있으면 통합 런타임에 파일을 처리할 리소스가 부족합니다.

- **권장 사항**: 통합 런타임에서 동시 실행을 제한합니다. 자체 호스팅 통합 런타임의 경우 메모리가 8GB보다 크거나 큰 강력한 컴퓨터로 확장합니다.

- **원인**: 오류 메시지에 'NullPointerReference'가 포함되어 있으면 일시적인 오류일 수 있습니다.

- **권장 사항**: 다시 시도하십시오. 문제가 지속되면 지원팀에 문의하십시오.


### <a name="error-code--parquetinvalidfile"></a>오류 코드: 마루앵발리트파일

- **메시지**:`File is not a valid parquet file.`

- **원인**: 마루 파일 문제.

- **권장 사항**: 입력이 유효한 마루 파일인지 확인합니다.


### <a name="error-code--parquetnotsupportedtype"></a>오류 코드: 마루노지원타입

- **메시지**:`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **원인**: 쪽모이 세공 형식은 Azure 데이터 팩터리에서 지원되지 않습니다.

- **권장 사항**: 원본 데이터를 다시 확인합니다. 문서를 참조하십시오. https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>오류 코드: 마루, 실수, 정밀도

- **메시지**:`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **원인**: 숫자 정밀도와 배율을 구문 분석하려고 시도하지만 이러한 정보는 제공되지 않습니다.

- **권장 사항**: 'Source'가 올바른 정밀도와 배율을 반환하지 않습니다. 문제 열 정밀도와 배율을 확인합니다.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>오류 코드: 마루유효치정밀도저스케일

- **메시지**:`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **원인**: 스키마가 잘못되었습니다.

- **권장 사항**: 문제 열의 정밀도와 배율을 확인합니다.


### <a name="error-code--parquetcolumnnotfound"></a>오류 코드: 마루열NotFound

- **메시지**:`Column %column; does not exist in Parquet file.`

- **원인**: 소스 스키마가 싱크 스키마와 일치하지 않습니다.

- **권장 사항**: '활동'에서 '매핑'을 확인합니다. 원본 열을 오른쪽 싱크 열에 매핑할 수 있는지 확인합니다.


### <a name="error-code--parquetinvaliddataformat"></a>오류 코드: 마루입력유효데이터형식

- **메시지**:`Incorrect format of %srcValue; for converting to %dstType;.`

- **원인**: 데이터를 매핑에 지정된 유형으로 변환할 수 없습니다.source

- **권장 사항**: 원본 데이터를 다시 확인하거나 복사 활동 열 매핑에서 이 열에 대한 올바른 데이터 형식을 지정합니다. 문서를 참조하십시오. https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>오류 코드: 마루, 데이터입력NotMatch열카운트

- **메시지**:`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **원인**: 소스 컬럼 수 및 싱크 컬럼 수 불일치

- **권장 사항**: 원본 열 수를 다시 확인하여 '매핑'의 싱크 열 수와 같습니다.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>오류 코드: 마루데이터유형NotMatch열유형

- **메시지**: 데이터 유형 %srcType; 주어진 열 유형 %dstType과 일치하지 않습니다. 에서 '%열Index;'.

- **원인**: 소스의 데이터를 싱크에 정의된 입력된 데이터로 변환할 수 없습니다.

- **권장 사항**: mapping.sink에서 올바른 형식을 지정하십시오.


### <a name="error-code--parquetbridgeinvaliddata"></a>오류 코드: 마루브리지유효데이터

- **메시지**:`%message;`

- **원인**: 제한을 초과하는 데이터 값

- **권장 사항**: 다시 시도하십시오. 문제가 지속되면 저희에게 연락하십시오.


### <a name="error-code--parquetunsupportedinterpretation"></a>오류 코드: 마루무비 지원해석

- **메시지**:`The given interpretation '%interpretation;' of parquet format is not supported.`

- **원인**: 지원되지 않는 시나리오

- **권장 사항**: '마루통통용'은 'sparkSql'이 되어서는 안됩니다.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>오류 코드: 마루언서포트파일레벨압축옵션

- **메시지**:`File level compression is not supported for Parquet.`

- **원인**: 지원되지 않는 시나리오

- **권장 사항**: 페이로드에서 '압축 유형'을 제거합니다.



## <a name="general-copy-activity-error"></a>일반 복사 활동 오류

### <a name="error-code--jrenotfound"></a>오류 코드: JreNotFound

- **메시지**:`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **원인**: 자체 호스팅 통합 런타임에서 Java 런타임을 찾을 수 없습니다. Java 런타임은 특정 소스를 읽는 데 필요합니다.

- **권장 사항**: 통합 런타임 환경, 참조 문서 확인:https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>오류 코드: 와일드카드패스싱크지원되지 않음

- **메시지**:`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **원인**: 싱크 데이터 집합은 와일드카드를 지원하지 않습니다.

- **권장 사항**: 싱크 데이터 집합을 확인하고 와일드카드 값 없이 경로를 수정합니다.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>오류 코드: 매핑유효속성사용빈값

- **메시지**:`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>오류 코드: 매핑Invalid속성WithNamePathAnddinal

- **메시지**:`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>오류 코드: 매핑중복 Ordinal

- **메시지**:`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>오류 코드: 매핑유효OrdinalForSink열

- **메시지**:`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>다음 단계

문제 해결에 대한 자세한 도움말을 보려면 다음 리소스를 사용해 보십시오.

*  [데이터 팩토리 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [데이터 팩터리 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [데이터 팩터리용 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [데이터 팩토리에 대한 트위터 정보](https://twitter.com/hashtag/DataFactory)
            
