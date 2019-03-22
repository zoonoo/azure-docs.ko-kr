---
title: Microsoft Azure Data Box Blob Storage 요구 사항 | Microsoft Docs
description: Azure Data Box Blob Storage의 API, SDK 및 클라이언트 라이브러리에 지원되는 버전을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 5770cd9a4955013100b7e58698eed77da10c0583
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012265"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box Blob Storage 요구 사항

이 문서에는 Data Box Blob Storage에서 지원되는 Azure API, SDK 및 도구의 버전이 나와 있습니다. Data Box Blob Storage는 Blob 관리 기능에 Azure 일관성이 있는 의미 체계를 제공합니다. 이 문서에서는 Azure Storage 서비스와 Azure Data Box Blob Storage의 알려진 차이점도 요약합니다.

Data Box Blob Storage에 연결하기 전에 정보를 주의 깊게 검토한 후 필요에 따라 다시 참조하는 것이 좋습니다.


## <a name="storage-differences"></a>스토리지 차이점

|     기능                                             |     Azure Storage                                     |     Data Box Blob Storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    클라우드 기반 SMB 파일 공유 지원됨              |    지원되지 않음      |
|    미사용 데이터에 대한 서비스 암호화                  |    256비트 AES 암호화                             |    256비트 AES 암호화 |
|    Storage 계정 유형                                 |    범용 및 Azure Blob Storage 계정    |    범용 v1만|
|    Blob 이름                                            |    1,024자(2,048바이트)                     |    880자(1,760바이트)|
|    블록 Blob 최대 크기                              |    4.75TB(100MB X 50,000개 블록)                   |    Azure Data Box v 1.8 이상의 경우 4.75TB(100MB x 50,000개 블록)|
|    페이지 Blob 최대 크기                               |    8 TB                                               |    1TB                   |
|    페이지 Blob 페이지 크기                                  |    512바이트                                          |    4KB                   |

## <a name="supported-api-versions"></a>지원되는 API 버전

Data Box Blob Storage에서 지원되는 Azure Storage 서비스 API 버전은 다음과 같습니다.

공개 미리 보기 릴리스(Azure Data Box 1.8 이상)

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)

## <a name="supported-sdk-versions"></a>지원되는 SDK 버전

|     클라이언트 라이브러리     |     Data Box Blob Storage 지원되는 버전     |     링크             |     엔드포인트 사양         |
|------------------------|-------------------------------------------------|---------------------------------------------|------------------------------------|
|    .NET                |    6.2.0~8.7.0.                         |    NuGet 패키지: https://www.nuget.org/packages/WindowsAzure.Storage/ <br>GitHub 릴리스: https://github.com/Azure/azure-storage-net/releases                                                                      |    app.config 파일                 |
|    자바                |    4.1.0~6.1.0                          |    Maven 패키지: https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage   <br>GitHub 릴리스: https://github.com/Azure/azure-storage-java/releases                                                      |    연결 문자열 설정         |
|    Node.js             |    1.1.0~2.7.0                          |    NPM 링크:   https://www.npmjs.com/package/azure-storage(예: “npm install azure-storage@2.7.0” 실행)   <br>GitHub 릴리스: https://github.com/Azure/azure-storage-node/releases                            |    서비스 인스턴스 선언    |
|    C++                 |    2.4.0~3.1.0                          |    NuGet 패키지: https://www.nuget.org/packages/wastorage.v140/   <br>GitHub 릴리스: https://github.com/Azure/azure-storage-cpp/releases                                                                            |    연결 문자열 설정         |
|    PHP                 |    0.15.0~1.0.0                         |    GitHub 릴리스: https://github.com/Azure/azure-storage-php/releases   <br>작성기를 통해 설치(아래 세부 정보 참조)                                                                                                   |    연결 문자열 설정         |
|    Python              |    0.30.0~1.0.0                         |    GitHub 릴리스: https://github.com/Azure/azure-storage-python/releases                                                                                                                                              |    서비스 인스턴스 선언    |
|    Ruby                |    0.12.1~1.0.1                         |    RubyGems 패키지:<br>일반: https://rubygems.org/gems/azure-storage-common/   <br>Blob: https://rubygems.org/gems/azure-storage-blob/      <br>GitHub 릴리스: https://github.com/Azure/azure-storage-ruby/releases    |                                   |

## <a name="supported-azure-client-libraries"></a>지원되는 Azure 클라이언트 라이브러리

Data Box Blob Storage에는 특정 클라이언트 라이브러리 및 특정 엔드포인트 접미사 요구 사항이 있습니다. Data Box Blob 스토리지 엔드포인트는 최신 버전의 Azure Blob Storage REST API와 완전히 동일하지는 않습니다. [Azure Data Box 1.8 이상에 지원되는 버전](#supported-api-versions)을 참조하세요. 스토리지 클라이언트 라이브러리의 경우 REST API와 호환되는 버전을 알아야 합니다.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8 이상

| 클라이언트 라이브러리     |Data Box Blob Storage 지원되는 버전     | 링크   |     엔드포인트 사양      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    8.7.0                                           |    NuGet 패키지: https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0    <br>GitHub 릴리스: https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0                                                                                                                                                                                               |    app.config 파일                 |
|    자바                |    6.1.0                                           |    Maven 패키지: https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub 릴리스: https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0                                                                                                                                                                              |    연결 문자열 설정         |
|    Node.js             |    2.7.0                                           |    NPM 링크:   https://www.npmjs.com/package/azure-storage(실행: npm install azure-storage@2.7.0)   <br>GitHub 릴리스: https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0                                                                                                                                                                        |    서비스 인스턴스 선언    |
|    C++                 |    3.1.0                                           |    NuGet 패키지: https://www.nuget.org/packages/wastorage.v140/3.1.0   <br>GitHub 릴리스: https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0                                                                                                                                                                                                     |    연결 문자열 설정         |
|    PHP                 |    1.0.0                                           |    GitHub 릴리스:<br>일반: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common   <br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob      <br>작성기를 통해 설치( 자세한 내용은 아래 세부 정보 참조)                                                                                                             |    연결 문자열 설정         |
|    Python              |    1.0.0                                           |    GitHub 릴리스:<br>일반: https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob: https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob                                                                                                                                                                          |    서비스 인스턴스 선언    |
|    Ruby                |    1.0.1                                           |    RubyGems 패키지:<br>일반: https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub 릴리스:<br>일반: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    연결 문자열 설정         |



### <a name="install-php-client-via-composer---current"></a>작성기를 통해 PHP 클라이언트 설치 - 현재

작성기를 통해 설치하려면: (Blob을 예로 사용).
다음 코드를 사용하여 프로젝트 루트에 composer.json이라는 파일을 만듭니다.

```
 {
   "require": {
   "Microsoft/azure-storage-blob":"1.0.0"
   }
```

`composer.phar`을 프로젝트 루트에 다운로드합니다.

실행: php composer.phar install.

### <a name="endpoint-declaration"></a>엔드포인트 선언

Azure Data Box Blob Storage 엔드포인트에는 지역 이름과 Data Box 도메인의 두 부분이 포함됩니다. 데이터 상자 Blob storage SDK에 기본 끝점은 \<일련 번호입니다. 장치 >. microsoftdatabox.com 합니다.  Blob 서비스 엔드포인트에 대한 자세한 내용은 [Data Box Blob Storage를 통해 연결](data-box-deploy-copy-data-via-rest.md)을 참조하세요.
 
## <a name="examples"></a>예

### <a name="net"></a>.NET

Data Box Blob Storage의 엔드포인트 접미사는 `app.config` 파일에 지정됩니다.

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>자바

Data Box Blob Storage의 엔드포인트 접미사는 연결 문자열 설정에 지정됩니다.

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Data Box Blob Storage의 엔드포인트 접미사는 선언 인스턴스에 지정됩니다.

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Data Box Blob Storage의 엔드포인트 접미사는 연결 문자열 설정에 지정됩니다.

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Data Box Blob Storage의 엔드포인트 접미사는 연결 문자열 설정에 지정됩니다.

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Data Box Blob Storage의 엔드포인트 접미사는 선언 인스턴스에 지정됩니다.

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Data Box Blob Storage의 엔드포인트 접미사는 연결 문자열 설정에 지정됩니다.

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>다음 단계

* [Azure Data Box 배포](data-box-deploy-ordered.md)
