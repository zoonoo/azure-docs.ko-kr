---
title: Azure Data Factory 커넥터 문제 해결
description: Azure Data Factory에서 커넥터 문제를 해결 하는 방법을 알아봅니다.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 8cabc1031f9d0be772ba087109ae1dfc881ce163
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680075"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory 커넥터 문제 해결

이 문서에서는 Azure Data Factory의 커넥터에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="azure-data-lake-storage"></a>Azure Data Lake 스토리지

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>오류 메시지: 원격 서버에서 오류를 반환 했습니다. (403) 사용할 수 없음

- **증상**: 복사 작업이 실패 하 고 다음 오류가 발생 합니다. 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **원인**: 사용 하는 서비스 주체 또는 관리 id에 특정 폴더/파일에 대 한 액세스 권한이 없기 때문일 수 있습니다.

- **해결**방법: 복사 해야 하는 모든 폴더와 하위 폴더에 해당 하는 사용 권한을 부여 합니다. [이 문서](connector-azure-data-lake-store.md#linked-service-properties)를 참조 하세요.

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>오류 메시지: 서비스 주체를 사용 하 여 액세스 토큰을 가져오지 못했습니다. ADAL 오류: service_unavailable

- **증상**: 복사 작업이 실패 하 고 다음 오류가 발생 합니다.

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **원인**: Azure Active Directory에서 소유 하 고 있는 STS (서비스 토큰 서버)를 사용할 수 없는 경우 (예: 요청을 처리 하기에 너무 많음) HTTP 오류 503을 반환 합니다. 

- **해결**방법: 몇 분 후에 복사 작업을 다시 실행 합니다.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>오류 메시지: 문자열에서 uniqueidentifier로 변환 하지 못했습니다.

- **증상**: 준비 된 복사 및 PolyBase를 사용 하 여 테이블 형식 데이터 원본 (예: SQL Server)에서 Azure SQL Data Warehouse로 데이터를 복사 하는 경우 다음 오류가 발생 합니다.

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **원인**: PolyBase Azure SQL Data Warehouse 빈 문자열을 GUID로 변환할 수 없습니다.

- **해결**방법: 복사 작업 싱크에서 Polybase 설정에서 "**형식 기본값 사용**" 옵션을 false로 설정 합니다.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>오류 메시지: 예상 데이터 형식: DECIMAL (x, x), 잘못 되는 값

- **증상**: 준비 된 복사 및 PolyBase를 사용 하 여 테이블 형식 데이터 원본 (예: SQL Server)에서 SQL DW로 데이터를 복사 하는 경우 다음 오류가 발생 합니다.

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **원인**: Polybase Azure SQL Data Warehouse 빈 문자열 (null 값)을 decimal 열에 삽입할 수 없습니다.

- **해결**방법: 복사 작업 싱크에서 Polybase 설정에서 "**형식 기본값 사용**" 옵션을 false로 설정 합니다.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>오류 메시지: Java 예외 메시지: HdfsBridge:: CreateRecordReader

- **증상**: PolyBase를 사용 하 여 Azure SQL Data Warehouse에 데이터를 복사 하 고 다음 오류를 발생 합니다.

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **원인**: 스키마 (총 열 너비)가 너무 커서 (1mb 보다 큼) 발생할 수 있습니다. 모든 열의 크기를 추가 하 여 대상 SQL DW 테이블의 스키마를 확인 합니다.

    - Int-> 4 바이트
    - Bigint-> 8 바이트
    - Varchar (n), char (n), binary (n), varbinary (n)-> n 바이트
    - Nvarchar (n), nchar (n)-> n * 2 바이트
    - 날짜-> 6 바이트
    - Datetime/(2), smalldatetime-> 16 바이트
    - Datetimeoffset-> 20 바이트
    - 10 진수 > 19 바이트
    - Float-> 8 바이트
    - Money-> 8 바이트
    - Smallmoney > 4 바이트
    - 실제 > 4 바이트
    - Smallint-> 2 바이트
    - 시간 > 12 바이트
    - Tinyint-> 1 바이트

- **해결**방법: 열 너비를 1mb 미만으로 줄입니다.

- 또는 Polybase를 사용 하지 않도록 설정 하 여 대량 삽입 방법을 사용 합니다.

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>오류 메시지: HTTP 조건부 헤더를 사용 하 여 지정한 조건이 충족 되지 않았습니다.

- **증상**: SQL 쿼리를 사용 하 여 Azure SQL Data Warehouse에서 데이터를 끌어오고 다음 오류가 발생 합니다.

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **원인**: Azure Storage에서 외부 테이블을 쿼리 하는 데 Azure SQL Data Warehouse 적중 문제가 있습니다.

- **해결**방법: SSMS에서 동일한 쿼리를 실행 하 고 동일한 결과가 표시 되는지 확인 합니다. 그렇다면 지원 티켓을 열고 Azure SQL Data Warehouse 하 고 SQL DW 서버와 데이터베이스 이름을 제공 하 여 추가로 문제를 해결 합니다.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>오류 메시지: 요청 크기가 너무 깁니다.

- **증상**: 기본 쓰기 일괄 처리 크기를 사용 하 여 Azure Cosmos DB에 데이터를 복사 하 고 *"**요청 크기가 너무 큼**"* 오류가 발생 합니다.

- **원인**: Cosmos DB 하나의 요청 크기를 2mb로 제한 합니다. 수식은 요청 크기 = 단일 문서 크기 * 쓰기 일괄 처리 크기입니다. 문서 크기가 크면 기본 동작으로 인해 요청 크기가 너무 커집니다. 쓰기 일괄 처리 크기를 튜닝할 수 있습니다.

- **해결**방법: 복사 작업 싱크에서 ' 일괄 처리 크기 ' 값을 줄입니다 (기본값은 1만).

### <a name="error-message-unique-index-constraint-violation"></a>오류 메시지: 고유 인덱스 제약 조건 위반

- **증상**: Cosmos DB에 데이터를 복사 하는 경우 다음 오류가 발생 합니다.

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **원인**: 다음과 같은 두 가지 원인이 있을 수 있습니다.

    - **Insert** as write 동작을 사용 하는 경우이 오류는 원본 데이터에 동일한 ID를 가진 행/개체가 있음을 의미 합니다.

    - **Upsert** 을 쓰기 동작으로 사용 하 고 컨테이너에 다른 고유 키를 설정 하는 경우이 오류는 원본 데이터에 다른 id를 가진 행/개체가 있지만 정의 된 고유 키에 대해 동일한 값을 의미 합니다.

- **해결 방법**: 

    - Cause1의 경우 **Upsert** 을 쓰기 동작으로 설정 합니다.
    - 원인 2의 경우 각 문서에 정의 된 고유 키에 대해 서로 다른 값이 있는지 확인 합니다.

### <a name="error-message-request-rate-is-large"></a>오류 메시지: 요청 비율이 깁니다.

- **증상**: Cosmos DB에 데이터를 복사 하는 경우 다음 오류가 발생 합니다.

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **원인**: 사용 된 요청 단위가 Cosmos DB에서 구성 된 사용 가능한 r u 보다 큽니다. [여기](../cosmos-db/request-units.md#request-unit-considerations)에서 Cosmos DB을 계산 하는 방법을 알아보세요.

- **해결**방법: 다음 두 가지 해결 방법이 있습니다.

    1. **컨테이너** 를 보다 큰 값으로 늘리면 복사 작업 성능이 향상 되는 반면, Cosmos DB에 더 많은 비용이 발생 하는 Cosmos DB. 

    2. **Writebatchsize** 를 더 작은 값 (예: 1000)으로 줄이고 **parallelCopies** 을 1과 같은 더 작은 값으로 설정 합니다 .이 값을 사용 하면 현재 보다는 복사 실행 성능은 저하 되지만 Cosmos DB 비용은 더 이상 발생 하지 않습니다.

### <a name="column-missing-in-column-mapping"></a>열 매핑의 열이 누락 되었습니다.

- **증상**: 열 매핑에 대 한 Cosmos DB 스키마를 가져올 때 일부 열이 누락 되었습니다. 

- **원인**: ADF는 처음 10 개의 Cosmos DB 문서에서 스키마를 유추 합니다. 일부 열/속성에 해당 문서에 값이 없는 경우 ADF에 의해 검색 되지 않으므로 표시 되지 않습니다.

- **해결**방법: 아래와 같이 쿼리를 조정 하 여 열이 빈 값을 가진 결과 집합에 표시 되도록 할 수 있습니다. 예를 들어 첫 10 개 문서에 "불가능" 열이 없습니다. 또는 매핑을 위한 열을 수동으로 추가할 수 있습니다.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>오류 메시지: 판독기의 GuidRepresentation은 CSharpLegacy입니다.

- **증상**: UUID 필드를 사용 하 여 Cosmos DB MongoAPI/MongoDB에서 데이터를 복사 하는 경우 다음 오류가 발생 합니다.

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **원인**: bson-UuidStardard 및 UUIDLEGACY에 UUID를 표시 하는 두 가지 방법이 있습니다. 기본적으로 UuidLegacy는 데이터를 읽는 데 사용 됩니다. MongoDB의 UUID 데이터가 UuidStandard 인 경우 오류가 발생 합니다.

- **해결**방법: MongoDB 연결 문자열에서 "**uuidRepresentation = standard**" 옵션을 추가 합니다. 자세한 내용은 [MongoDB connection string](connector-mongodb.md#linked-service-properties)항목을 참조 하세요.

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>오류 메시지: ' SshPublicKey ' 인증 유형에 대해 제공 된 SFTP 자격 증명이 잘못 되었습니다.

- **증상**: `SshPublicKey` 인증을 사용 하 여 다음 오류를 적중 합니다.

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **원인**: 다음과 같은 세 가지 원인이 있을 수 있습니다.

    1. ADF 제작 UI를 사용 하 여 SFTP 연결 된 서비스를 작성 하는 경우이 오류는 사용 하도록 선택한 개인 키의 형식이 잘못 되었음을 의미 합니다. SSH 개인 키의 PKCS # 8 형식을 사용할 수 있지만 ADF는 기존 SSH 키 형식만 지원 합니다. 구체적으로 말하자면 PKCS # 8 형식과 기존 키 형식 간의 차이는 PKCS # 8 키 콘텐츠는 "*암호화 된 개인 키-----시작-----* "으로 시작 하는 반면 기존 키 형식은 " *-----시작 RSA 개인 키-----* "로 시작 합니다.
    2. Azure Key Vault를 사용 하 여 개인 키 콘텐츠를 저장 하거나 programmatical를 사용 하 여 SFTP 연결 된 서비스를 작성 하는 경우이 오류는 개인 키 콘텐츠가 잘못 된 것을 의미 하며,이는 b a s e 64로 인코딩되지 않았을 수 있습니다.
    3. 자격 증명 또는 개인 키 콘텐츠가 잘못 되었습니다.

- **해결 방법**: 

    - 원인 #1의 경우 다음 명령을 실행 하 여 키를 일반 키 형식으로 변환한 다음 ADF 제작 UI에서 사용 합니다.

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - 이러한 문자열을 생성 하기 위해 #2 하는 이유는 다음과 같은 2 가지 방법을 사용할 수 있습니다.
    - 타사 base64 변환 도구 사용: [Base64 인코딩 및 디코드](https://www.base64encode.org/)와 같은 도구에 전체 개인 키 콘텐츠를 붙여넣고, base64 형식 문자열로 인코딩한 다음,이 문자열을 key vault에 붙여넣거나, SFTP 연결 된 서비스를 프로그래밍 방식으로 작성 하는 데이 값을 사용 합니다.
    - 코드 C# 사용:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - #3 경우에는 다른 도구를 사용 하 여 키 파일이 나 암호가 올바른지 확인 하 고이를 사용 하 여 SFTP 서버에 올바르게 액세스할 수 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory에 대 한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대 한 Twitter 정보](https://twitter.com/hashtag/DataFactory)



