---
title: 일반적인 검색 인덱서 문제 해결
titleSuffix: Azure Cognitive Search
description: 데이터 원본 연결, 방화벽 및 누락된 문서를 포함하여 Azure Cognitive Search의 인덱서에 대한 오류 및 일반적인 문제를 해결합니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190924"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Azure 인지 검색에서 일반적인 인덱서 문제 해결

인덱서에는 Azure Cognitive Search로 데이터를 인덱싱할 때 여러 가지 문제가 있을 수 있습니다. 오류의 주요 범주는 다음과 같습니다.

* [데이터 원본 또는 기타 리소스에 연결](#connection-errors)
* [문서 처리](#document-processing-errors)
* [인덱스에 대한 문서 수집](#index-errors)

## <a name="connection-errors"></a>연결 오류

> [!NOTE]
> 인덱서에는 Azure 네트워크 보안 메커니즘에 의해 보호되는 데이터 원본 및 기타 리소스에 대한 액세스지원이 제한되어 있습니다. 현재 인덱서에는 해당 IP 주소 범위 제한 메커니즘 또는 NSG 규칙을 통해서만 데이터 원본에 액세스할 수 있습니다. 지원되는 각 데이터 원본에 액세스하기 위한 자세한 내용은 아래에서 확인할 수 있습니다.
>
> 정규화된 도메인 이름(예: `<your-search-service-name>.search.windows.net`)을 ping하여 검색 서비스의 IP 주소를 찾을 수 있습니다.
>
> [다운로드 가능한 JSON 파일을](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) 사용하거나 서비스 태그 검색 [API를](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)통해 [서비스 태그의](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) `AzureCognitiveSearch` IP 주소 범위를 확인할 수 있습니다. IP 주소 범위는 매주 업데이트됩니다.

### <a name="configure-firewall-rules"></a>방화벽 규칙 구성

Azure 저장소, CosmosDB 및 Azure SQL은 구성 가능한 방화벽을 제공합니다. 방화벽을 사용하는 경우 특정 오류 메시지가 발생하지 않습니다. 일반적으로 방화벽 오류는 일반적이며 `The remote server returned an error: (403) Forbidden` `Credentials provided in the connection string are invalid or have expired`다음과 같습니다.

이러한 경우 인덱서가 이러한 리소스에 액세스할 수 있도록 허용하는 2가지 옵션이 있습니다.

* **모든 네트워크에서** 액세스를 허용하여 방화벽을 사용하지 않도록 설정합니다(가능한 경우).
* 또는 리소스의 방화벽 규칙(IP 주소 범위 제한)에서 검색 `AzureCognitiveSearch` 서비스의 IP 주소및 [서비스 태그의](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) IP 주소 범위에 대한 액세스를 허용할 수 있습니다.

각 데이터 원본 유형에 대한 IP 주소 범위 제한 구성에 대한 자세한 내용은 다음 링크에서 확인할 수 있습니다.

* [Azure 저장소](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**제한**: Azure Storage에 대한 위의 설명서에 설명된 대로 IP 주소 범위 제한은 검색 서비스와 저장소 계정이 다른 지역에 있는 경우에만 작동합니다.

Azure 함수(사용자 지정 웹 [Api 기술로](cognitive-search-custom-skill-web-api.md)사용될 수 있음)도 IP 주소 [제한을](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions)지원합니다. 구성할 IP 주소 목록은 검색 서비스의 IP 주소와 서비스 태그의 `AzureCognitiveSearch` IP 주소 범위입니다.

Azure VM의 SQL 서버에서 데이터에 액세스하기 위한 자세한 내용은 [여기에](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) 설명되어 있습니다.

### <a name="configure-network-security-group-nsg-rules"></a>NSG(네트워크 보안 그룹) 규칙 구성

SQL 관리 인스턴스에서 데이터에 액세스하거나 Azure VM을 [사용자 지정 웹 Api 기술의](cognitive-search-custom-skill-web-api.md)웹 서비스 URI로 사용하는 경우 고객은 특정 IP 주소에 대해 걱정할 필요가 없습니다.

이러한 경우 Azure VM 또는 SQL 관리 인스턴스를 가상 네트워크 내에 상주하도록 구성할 수 있습니다. 그런 다음 가상 네트워크 서브넷 및 네트워크 인터페이스에서 유입및 유출될 수 있는 네트워크 트래픽 유형을 필터링하도록 네트워크 보안 그룹을 구성할 수 있습니다.

서비스 `AzureCognitiveSearch` 태그는 IP 주소 범위를 조회할 필요 없이 인바운드 [NSG 규칙에서](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) 직접 사용할 수 있습니다.

SQL 관리 인스턴스의 데이터에 액세스하기 위한 자세한 내용은 [여기에](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md) 설명되어 있습니다.

### <a name="cosmosdb-indexing-isnt-enabled"></a>코스모스DB "인덱싱"이 활성화되지 않았습니다.

Azure 인지 검색코스모스 DB 인덱싱에 대한 암시적 종속성이 있습니다. Cosmos DB에서 자동 인덱싱을 끄면 Azure Cognitive Search는 성공한 상태를 반환하지만 컨테이너 내용을 인덱싱하지 못합니다. 설정을 확인하고 인덱싱을 설정하는 방법에 대한 지침은 [Azure Cosmos DB에서 인덱싱 관리](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal)를 참조하세요.

## <a name="document-processing-errors"></a>문서 처리 오류

### <a name="unprocessable-or-unsupported-documents"></a>처리할 수 없거나 지원되지 않는 문서

Blob 인덱서 [문서는 문서 형식을 명시적으로 지원합니다.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) 경우에 따라 Blob Storage 컨테이너에는 지원되지 않는 문서가 포함되어 있습니다. 문제가 있는 다른 문서가 있을 수 있습니다. [구성 옵션을 변경](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)하여 이러한 문서에서 인덱서를 중지하지 않을 수 있습니다.

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>누락된 문서 콘텐츠

Blob 인덱서는 [컨테이너의 blob에서 텍스트를 찾아서 추출합니다](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). 텍스트 추출에 대한 몇 가지 문제는 다음과 같습니다.

* 문서에는 스캔된 이미지만 포함됩니다. 스캔된 이미지(JPG)와 같은 텍스트가 아닌 콘텐츠를 포함한 PDF Blob은 표준 Blob 인덱싱 파이프라인에서 결과를 생성하지 않습니다. 텍스트 요소가 있는 이미지 콘텐츠가 있는 경우 [인지 검색을](cognitive-search-concept-image-scenarios.md) 사용하여 텍스트를 찾고 추출할 수 있습니다.
* Blob 인덱서는 메타데이터를 인덱스하도록 구성됩니다. 콘텐츠를 추출하려면 [콘텐츠 및 메타데이터를 모두 추출](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)하도록 Blob 인덱서를 구성해야 합니다.

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>인덱스 오류

### <a name="missing-documents"></a>누락된 문서

인덱서는 [데이터 원본](https://docs.microsoft.com/rest/api/searchservice/create-data-source)에서 문서를 찾습니다. 인덱싱되어야 하는 데이터 원본의 문서가 인덱스에서 누락된 것으로 나타나는 경우도 있습니다. 다음과 같은 오류가 발생할 수 있는 일반적인 몇 가지 이유가 있습니다.

* 문서는 인덱스되지 않았습니다. 성공적인 인덱서 실행을 위해 포털을 확인합니다.
* 문서는 인덱서 실행 후에 업데이트되었습니다. 인덱서가 [일정](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)대로 진행되는 경우 결국 문서를 다시 실행하고 선택합니다.
* 데이터 원본에 지정된 [쿼리](/rest/api/searchservice/create-data-source)는 문서를 제외합니다. 인덱서는 데이터 원본에 포함되지 않는 문서를 인덱싱할 수 없습니다.
* [필드 매핑](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) 또는 [AI 보강으로](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) 문서가 변경되었으며 예상과 다르게 보입니다.
* [문서 조회 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document)를 사용하여 문서를 찾습니다.
