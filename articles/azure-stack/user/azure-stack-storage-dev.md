---
title: Azure Stack 저장소 개발 도구를 사용 하 여 시작 | Microsoft Docs
description: Azure Stack 저장소 개발 도구를 사용 하 여 시작 하기 위한 지침
services: azure-stack
author: mabriggs
ms.author: mabrigg
ms.date: 10/10/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.openlocfilehash: 3eefa13a7283febbebd2adc16047b28ffcae55fe
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078378"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Azure Stack 저장소 개발 도구를 사용 하 여 시작

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Microsoft Azure Stack에는 blob, 테이블 및 큐 저장소를 포함 하는 저장소 서비스의 집합을 제공 합니다.

Azure Stack 저장소 개발 도구를 사용 하 여 시작 하려면이 문서를 지침으로 사용 합니다. 해당 Azure storage 자습서에서 샘플 코드와 자세한 정보를 찾을 수 있습니다.

> [!NOTE]  
> Azure Stack 저장소와 각 플랫폼에 대 한 특정 요구 사항을 비롯 한 Azure storage의 차이점 알려져 있습니다. 예를 들어 특정 클라이언트 라이브러리 및 Azure Stack에 대 한 특정 끝점 접미사 요구 됩니다. 자세한 내용은 [Azure Stack 저장소: 차이점 및 고려 사항](azure-stack-acs-differences.md)합니다.

## <a name="azure-client-libraries"></a>Azure 클라이언트 라이브러리

Azure Stack 저장소에 대 한 지원 되는 REST API 버전 2017-04-17, 2016-05-31, 2015 년 12 월 11, 2015-07-08, 2015-04-05 1802 업데이트 또는 최신 버전 및 이전 버전 2015-04-05 됩니다. Azure Stack 끝점에 최신 버전의 Azure storage REST API를 사용 하 여 완벽 한 패리티를 사용할 필요가 없습니다. 저장소 클라이언트 라이브러리에 대 한 REST API와 호환 되는 버전에 주의 해야 합니다.

### <a name="1802-update-or-newer-versions"></a>1802 업데이트 또는 최신 버전

| 클라이언트 라이브러리 | Azure Stack 지원 되는 버전 | 링크 | 끝점 지정 |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Nuget 패키지:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>GitHub 릴리스:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | app.config 파일 |
| 자바 | 6.1.0 | Maven 패키지:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>GitHub 릴리스:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | 연결 문자열 설정 |
| Node.js | 2.7.0 | NPM 링크:<br>https://www.npmjs.com/package/azure-storage<br>(실행: `npm install azure-storage@2.7.0`)<br> <br>Github 릴리스:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | 서비스 인스턴스 선언 |
| C++ | 3.1.0 | Nuget 패키지:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>GitHub 릴리스:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | 연결 문자열 설정 |
| PHP | 1.0.0 | GitHub 릴리스:<br>일반적인: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>큐:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>테이블: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>작성기를 통해 설치 (자세한 내용은 [아래 세부 정보를 보려면](#install-php-client-via-composer---current).) | 연결 문자열 설정 |
| Python | 1.0.0 | GitHub 릴리스:<br>일반적인:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Blob:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>큐:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | 서비스 인스턴스 선언 |
| Ruby | 1.0.1 | RubyGems 패키지:<br>일반적인:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>큐: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>테이블: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>GitHub 릴리스:<br>일반적인: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>큐: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>테이블: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | 연결 문자열 설정 |

#### <a name="install-php-client-via-composer---current"></a>현재 작성기-를 통해 PHP 클라이언트를 설치 합니다.

작성기를 통해 설치 합니다. (예로 take blob).

1. 이라는 파일을 만듭니다 **composer.json** 다음 코드를 사용 하 여 프로젝트의 루트에:

  ```php
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```

2. 다운로드 [composer.phar](http://getcomposer.org/composer.phar) 프로젝트 루트에 있습니다.
3. `php composer.phar install`을 실행합니다.

### <a name="previous-versions"></a>이전 버전

|클라이언트 라이브러리|Azure Stack 지원 되는 버전|링크|끝점 지정|
|---------|---------|---------|---------|
|.NET     |6.2.0|Nuget 패키지:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>GitHub 릴리스:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|app.config 파일|
|자바|4.1.0|Maven 패키지:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>GitHub 릴리스:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|연결 문자열 설정|
|Node.js     |1.1.0|NPM 링크:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(실행: `npm install azure-storage@1.1.0)`<br><br>Github 릴리스:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|서비스 인스턴스 선언||C++|2.4.0|Nuget 패키지:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub 릴리스:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|연결 문자열 설정|
|C++|2.4.0|Nuget 패키지:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub 릴리스:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|연결 문자열 설정|
|PHP|0.15.0|GitHub 릴리스:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>작성기를 통해 설치 (아래 세부 정보 참조)|연결 문자열 설정|
|Python     |0.30.0|PIP 패키지:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(실행: `pip install -v azure-storage==0.30.0)`<br><br>GitHub 릴리스:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|서비스 인스턴스 선언|
|Ruby|0.12.1<br>미리 보기|RubyGems 패키지:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>GitHub 릴리스:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|연결 문자열 설정|

#### <a name="install-php-client-via-composer---previous"></a>이전-작성기를 통해 PHP 클라이언트를 설치 합니다.

작성기를 통해 설치:

1. 이라는 파일을 만듭니다 **composer.json** 다음 코드를 사용 하 여 프로젝트의 루트에:

  ```php
    {
          "require":{
          "Microsoft/azure-storage":"0.15.0"
          }
    }
  ```

2. 다운로드 [composer.phar](http://getcomposer.org/composer.phar) 프로젝트 루트에 있습니다.
3. `php composer.phar install`을 실행합니다.

## <a name="endpoint-declaration"></a>끝점 선언

두 부분을 포함 하는 Azure Stack 끝점: 지역 및 Azure Stack 도메인의 이름입니다.
기본 끝점의 Azure Stack 개발 키트 **local.azurestack.external**합니다.
확실 하지 않은 끝점에 대 한 경우 클라우드 관리자에 게 문의 합니다.

## <a name="examples"></a>예

### <a name="net"></a>.NET

Azure Stack에 대 한 끝점 접미사는 app.config 파일에 지정 됩니다.

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>자바

Azure Stack에 대 한 끝점 접미사는 연결 문자열의 설정에 지정 됩니다.

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Azure Stack에 대 한 끝점 접미사는 인스턴스를 선언에 지정 됩니다.

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Azure Stack에 대 한 끝점 접미사는 연결 문자열의 설정에 지정 됩니다.

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Azure Stack에 대 한 끝점 접미사는 연결 문자열의 설정에 지정 됩니다.

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Azure Stack에 대 한 끝점 접미사는 인스턴스를 선언에 지정 됩니다.

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Azure Stack에 대 한 끝점 접미사는 연결 문자열의 설정에 지정 됩니다.

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob 저장소

다음 Azure Blob storage 자습서는 Azure Stack에 적용 됩니다. 이전에 설명 된 Azure Stack에 대 한 특정 끝점 접미사 요구 사항을 유의 [예제](#examples) 섹션입니다.

* [.NET을 사용하여 Azure Blob 저장소 시작](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Java에서 Blob 저장소를 사용하는 방법](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Node.js에서 Blob 저장소를 사용하는 방법](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [C++에서 Blob 저장소를 사용하는 방법](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [PHP에서 Blob 저장소를 사용하는 방법](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Python에서 Azure Blob storage를 사용 하는 방법](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Ruby에서 Blob 저장소를 사용하는 방법](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>큐 저장소

다음 Azure Queue storage 자습서는 Azure Stack에 적용 됩니다. 이전에 설명 된 Azure Stack에 대 한 특정 끝점 접미사 요구 사항을 유의 [예제](#examples) 섹션입니다.

* [.NET을 사용하여 Azure 큐 저장소 시작](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Java에서 큐 저장소를 사용하는 방법](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Node.js에서 큐 저장소를 사용하는 방법](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [C++에서 큐 저장소를 사용하는 방법](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [PHP에서 큐 저장소를 사용하는 방법](../../storage/queues/storage-php-how-to-use-queues.md)
* [Python에서 큐 저장소를 사용하는 방법](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Ruby에서 큐 저장소를 사용하는 방법](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)

## <a name="table-storage"></a>테이블 저장소

다음 Azure Table storage 자습서는 Azure Stack에 적용 됩니다. 이전에 설명 된 Azure Stack에 대 한 특정 끝점 접미사 요구 사항을 유의 [예제](#examples) 섹션입니다.

* [.NET을 사용하여 Azure 테이블 저장소 시작](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Java에서 테이블 저장소를 사용하는 방법](../../cosmos-db/table-storage-how-to-use-java.md)
* [Node.js에서 Azure Table Storage를 사용하는 방법](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [C + +에서 Table storage를 사용 하는 방법](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [PHP에서 테이블 저장소를 사용하는 방법](../../cosmos-db/table-storage-how-to-use-php.md)
* [Python에서 Table storage를 사용 하는 방법](../../cosmos-db/table-storage-how-to-use-python.md)
* [Ruby에서 테이블 저장소를 사용하는 방법](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>다음 단계

* [Microsoft Azure storage 소개](../../storage/common/storage-introduction.md)