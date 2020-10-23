---
title: Microsoft Azure Stack Edge Blob 저장소 요구 사항 | Microsoft Docs
description: Azure Stack Edge Blob storage 용 Api, Sdk 및 클라이언트 라이브러리에 대해 지원 되는 버전에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: f99a3110880626b3a809e6bab5edc02398094547
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426219"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Azure Stack Edge Blob 저장소 요구 사항

이 문서에서는 Azure Api, Azure 클라이언트 라이브러리 및 Azure Stack Edge Blob 저장소에서 지원 되는 도구 버전을 나열 합니다. Edge Blob storage Azure Stack는 Azure 일치 의미 체계를 사용 하 여 blob 관리 기능을 제공 합니다. 또한이 문서에는 Azure Storage 서비스와 알려진 Azure Stack Edge Blob 저장소 차이가 요약 되어 있습니다.

Azure Stack Edge Blob storage에 연결 하기 전에 정보를 신중 하 게 검토 하 고 필요에 따라 다시 참조 하는 것이 좋습니다.

## <a name="storage-differences"></a>스토리지 차이점

|     기능                                             |     Azure Storage                                     |     Azure Stack Edge Blob storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    클라우드 기반 SMB 파일 공유 지원됨              |    지원되지 않음      |
|    Storage 계정 유형                                 |    범용 및 Azure Blob Storage 계정    |    범용 v1만|
|    Blob 이름                                            |    1,024자(2,048바이트)                     |    880자(1,760바이트)|
|    블록 Blob 최대 크기                              |    4.75TB(100MB X 50,000개 블록)                   |    Azure Stack Edge 용 4.75 TB (100 MB x 5만 블록)|
|    페이지 Blob 최대 크기                               |    8 TB                                               |    1TB                   |
|    페이지 Blob 페이지 크기                                  |    512바이트                                          |    4KB                   |

## <a name="supported-api-versions"></a>지원되는 API 버전

다음 버전의 Azure Storage 서비스 Api는 Azure Stack Edge Blob 저장소에서 지원 됩니다.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 이상

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>지원되는 Azure 클라이언트 라이브러리

Edge Blob storage Azure Stack에는 특정 클라이언트 라이브러리 및 특정 끝점 접미사 요구 사항이 있습니다. Azure Stack Edge Blob storage 끝점은 최신 버전의 Azure Blob Storage REST API를 사용 하 여 전체 패리티를 갖지 않습니다. [Azure Stack Edge에 대해 지원 되는 API 버전](#supported-api-versions)을 참조 하세요. 스토리지 클라이언트 라이브러리의 경우 REST API와 호환되는 버전을 알아야 합니다.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 이상

다음 Azure 클라이언트 라이브러리 버전은 Azure Stack Edge Blob storage에 대해 지원 됩니다.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>작성기를 통해 PHP 클라이언트 설치-현재

작성기를 통해 PHP 클라이언트를 설치 하려면:

1. 다음 코드를 사용 하 여 프로젝트의 루트에 composer.js라는 파일을 만듭니다 (예: Azure Storage Blob 서비스 사용).

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. `composer.phar`을 프로젝트 루트에 다운로드합니다.

3. 실행: php composer.phar install.


## <a name="endpoint-declaration"></a>엔드포인트 선언

Azure Stack Edge Blob storage SDK에서 끝점 접미사-는 Azure Stack에 `<device serial number>.microsoftdatabox.com` 지 도메인을 식별 합니다. Blob service 끝점에 대 한 자세한 내용은 [Edge PRO GPU를 사용 하 Azure Stack 여 저장소 계정을 통해 데이터 전송](azure-stack-edge-j-series-deploy-add-storage-accounts.md)으로 이동 합니다.


## <a name="examples"></a>예

### <a name="net"></a>.NET

Azure Stack Edge Blob storage의 경우 끝점 접미사가 파일에 지정 됩니다 `app.config` .

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Edge Blob storage의 경우 연결 문자열 설치 시 끝점 접미사가 지정 됩니다. Azure Stack

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Edge Blob storage Azure Stack의 경우, 끝점 접미사는 선언 인스턴스에 지정 됩니다.

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Edge Blob storage의 경우 연결 문자열 설치 시 끝점 접미사가 지정 됩니다. Azure Stack

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Edge Blob storage의 경우 연결 문자열 설치 시 끝점 접미사가 지정 됩니다. Azure Stack

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Edge Blob storage Azure Stack의 경우, 끝점 접미사는 선언 인스턴스에 지정 됩니다.

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Edge Blob storage의 경우 연결 문자열 설치 시 끝점 접미사가 지정 됩니다. Azure Stack

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>다음 단계

* [GPU를 사용 하 여 Azure Stack Edge Pro 배포 준비](azure-stack-edge-gpu-deploy-prep.md)
