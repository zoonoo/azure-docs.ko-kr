---
title: "Azure 검색을 사용하여 Azure 테이블 저장소 인덱싱"
description: "Azure 검색을 사용하여 Azure 테이블에 저장된 데이터를 인덱싱하는 방법을 알아봅니다"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/18/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 66e6fec16aab7764b05b616efc0fccbfb2d0595e
ms.lasthandoff: 03/30/2017

---

# <a name="indexing-azure-table-storage-with-azure-search"></a>Azure 검색을 사용하여 Azure 테이블 저장소 인덱싱
이 문서에서는 Azure 검색을 사용하여 Azure 테이블 저장소에 저장된 데이터를 인덱싱하는 방법을 보여 줍니다. 새로운 Azure 검색 테이블 인덱서로 이 과정을 신속하게 원활하게 수행할 수 있습니다.

## <a name="setting-up-azure-table-indexing"></a>Azure 테이블 인덱싱 설정
Azure 테이블 인덱서를 설정 및 구성하려면 [인덱서 작업](https://msdn.microsoft.com/library/azure/dn946891.aspx)에 설명된 대로 Azure Search REST API를 사용하여 **인덱서** 및 **데이터 원본**을 만들고 관리할 수 있습니다. .NET SDK의 [버전 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) 도 사용할 수 있습니다. 향후에는 테이블 인덱싱에 대한 지원이 Azure 포털에 추가될 예정입니다.

데이터 원본은 인덱싱할 데이터, 데이터에 액세스하는 데 필요한 자격 증명, Azure 검색에서 데이터 변경 내용(예: 수정되거나 삭제된 행)을 효율적으로 식별할 수 있도록 해주는 정책을 지정합니다.

인덱서는 데이터 원본에서 데이터를 읽어 대상 검색 인덱스에 로드합니다.

테이블 인덱싱을 설정하려면

1. 데이터 소스 만들기
   * `type` 매개 변수를 `azuretable`로 설정합니다.
   * 저장소 계정 연결 문자열을 `credentials.connectionString` 매개 변수로 전달합니다. 자세한 내용은 아래에서 [자격 증명을 지정하는 방법](#Credentials)을 참조하세요.
   * `container.name` 매개 변수를 사용하여 테이블 이름을 지정합니다.
   * 필요에 따라 `container.query` 매개 변수를 사용하여 쿼리를 지정합니다. 가능한 경우 최상의 성능을 위해 PartitionKey에서 필터를 사용합니다. 다른 쿼리를 사용하면 전체 테이블이 검색되므로 테이블이 큰 경우 성능이 저하될 수 있습니다.
2. 인덱싱하려는 테이블의 열에 해당하는 스키마로 검색 인덱스를 만듭니다.
3. 검색 인덱스에 데이터 원본을 연결하여 인덱서를 만듭니다.

### <a name="create-data-source"></a>데이터 원본 만들기
    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

데이터 원본 만들기 API에 대한 자세한 내용은 [데이터 원본 만들기](https://msdn.microsoft.com/library/azure/dn946876.aspx)를 참조하세요.

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>자격 증명을 지정하는 방법 ####

테이블에 대한 자격 증명을 제공하는 방법은 다음 중 하나입니다. 

- **전체 액세스 저장소 계정 연결 문자열**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Azure Portal에서 저장소 계정 블레이드 > 설정 > 키(클래식 저장소 계정) 또는 설정 > 액세스 키(Azure Resource Manager 저장소 계정)로 이동하여 연결 문자열을 가져올 수 있습니다.
- **저장소 계정 공유 액세스 서명**(SAS) 연결 문자열: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl`. SAS에 컨테이너(이 경우 테이블) 및 개체(테이블 행)에 대한 읽기 권한 및 목록이 있어야 합니다.
-  **테이블 공유 액세스 서명**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r`. SAS에 테이블에 대한 쿼리(읽기) 권한이 있어야 합니다.

저장소 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명 사용](../storage/storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

> [!NOTE]
> SAS 자격 증명을 사용하는 경우 자격 증명이 만료되는 것을 방지하기 위해 갱신된 서명을 사용하여 데이터 원본 자격 증명을 주기적으로 업데이트해야 합니다. SAS 자격 증명이 만료되면 `Credentials provided in the connection string are invalid or have expired.`와 유사한 오류 메시지가 표시되면서 인덱서가 실행되지 못합니다.  

### <a name="create-index"></a>인덱스 만들기
    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

인덱스 만들기 API에 대한 자세한 내용은 [Create Index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>인덱서 만들기
마지막으로 데이터 원본과 대상 인덱스를 참조하는 인덱서를 만듭니다. 예:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](https://msdn.microsoft.com/library/azure/dn946899.aspx)를 확인하세요.

이제 모든 인덱싱 작업을 마쳤습니다.

## <a name="dealing-with-different-field-names"></a>다른 필드 이름 처리
기존 인덱스의 필드 이름이 테이블의 속성 이름과 달라지는 경우가 종종 있습니다. **필드 매핑** 을 사용하여 테이블의 속성 이름을 검색 인덱스의 필드 이름에 매핑할 수 있습니다. 필드 매핑에 대해 자세히 알아보려면 [데이터 원본 및 검색 인덱스의 차이를 극복하는 Azure 검색 인덱서 필드 매핑](search-indexer-field-mappings.md)을 참조하세요.

## <a name="handling-document-keys"></a>문서 키 처리
Azure 검색에서는 문서 키가 문서를 고유하게 식별합니다. 모든 검색 인덱스는 `Edm.String`형식의 키 필드를 정확히 하나만 포함해야 합니다. 인덱스에 추가할 각 문서에는 키 필드(실제로 유일한 필수 필드)가 필요합니다.

테이블 행에 복합 키가 있으므로 Azure 검색에서 파티션 키와 행 키 값을 연결한 `Key` 라는 합성 필드를 생성합니다. 예를 들어 행의 PartitionKey가 `PK1`이고 RowKey가 `RK1`인 경우 `Key` 필드의 값은 `PK1RK1`이 됩니다.

> [!NOTE]
> `Key` 값은 문서 키의 유효하지 않은 문자(예: 대시)를 포함할 수 있습니다. `base64Encode` [필드 매핑 함수](search-indexer-field-mappings.md#base64EncodeFunction)를 사용하여 잘못된 문자를 처리할 수 있습니다. 또한 이 작업을 수행하는 경우 조회 같은 API 호출에서 문서 키 전달 시 URL 안전 Base64 인코딩을 사용해야 합니다.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>증분 인덱싱 및 삭제 감지
일정에 따라 실행되는 테이블 인덱서를 설정한 경우 이 인덱서는 행의 `Timestamp` 값에 따라 새 행 또는 업데이트된 행만 다시 인덱싱합니다. 변경 감지 정책을 지정하지 않아도 됩니다. 증분 인덱싱이 자동으로 사용됩니다.

인덱스에서 특정 문서를 제거해야 함을 나타내려면 소프트 삭제 전략을 사용합니다. 행을 삭제하는 대신, 삭제됨을 나타내는 속성을 추가하고 데이터 원본에 대한 소프트 삭제 감지 정책을 설정합니다. 예를 들어 아래에 표시된 정책은 `"true"` 값의 속성 `IsDeleted`가 있는 경우 행이 삭제됨을 고려합니다.

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Azure 검색 개선 지원
기능 요청 또는 개선에 대한 아이디어가 있는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search/)를 통해 연락해 주세요.

