---
title: 인덱서 IP 범위에 대 한 액세스 허용
titleSuffix: Azure Cognitive Search
description: 방법 가이드에서는 인덱서가 액세스할 수 있도록 IP 방화벽 규칙을 설정 하는 방법을 설명 합니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: c80462707d3dccbb8fccff244017053c25ad46e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463783"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>인덱서 액세스를 사용 하도록 IP 방화벽 규칙 설정

저장소 계정, Cosmos DB 계정 및 Azure SQL server와 같은 Azure 리소스에 대 한 IP 방화벽 규칙은 특정 IP 범위에서 들어오는 트래픽만 데이터에 액세스 하도록 허용 합니다.

이 문서에서는 Azure Cognitive Search 인덱서가 데이터에 안전 하 게 액세스할 수 있도록 저장소 계정에 대해 Azure Portal를 통해 IP 규칙을 구성 하는 방법을 설명 합니다. 저장소와 관련 하 여이 가이드는 데이터에 대 한 액세스를 보호 하기 위한 IP 방화벽 규칙도 제공 하는 다른 Azure 리소스로 직접 변환할 수 있습니다.

> [!NOTE]
> 저장소 계정에 대 한 IP 방화벽 규칙은 저장소 계정과 검색 서비스가 서로 다른 지역에 있는 경우에만 유효 합니다. 설치 프로그램에서이를 허용 하지 않는 경우 [신뢰할 수 있는 서비스 예외 옵션](search-indexer-howto-access-trusted-service-exception.md)을 활용 하는 것이 좋습니다.

## <a name="get-the-ip-address-of-the-search-service"></a>검색 서비스의 IP 주소를 가져옵니다.

검색 서비스의 FQDN (정규화 된 도메인 이름)을 가져옵니다. 이는와 같습니다 `<search-service-name>.search.windows.net` . Azure Portal에서 검색 서비스를 조회 하 여 FQDN을 확인할 수 있습니다.

   ![서비스 FQDN 가져오기](media\search-indexer-howto-secure-access\search-service-portal.png "서비스 FQDN 가져오기")

`nslookup`FQDN의 (또는)를 수행 하 여 검색 서비스의 IP 주소를 가져올 수 있습니다 `ping` . 이는 방화벽 규칙에 추가할 IP 주소 중 하나입니다.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>"AzureCognitiveSearch" 서비스 태그의 IP 주소 범위를 가져옵니다.

서비스 태그의 IP 주소 범위는 `AzureCognitiveSearch` [검색 API (미리 보기)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) 또는 [다운로드 가능한 JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 통해 가져올 수 있습니다.

이 연습에서는 검색 서비스가 Azure 공용 클라우드 인 것으로 가정 하 여 [Azure 공용 JSON 파일](https://www.microsoft.com/download/details.aspx?id=56519) 을 다운로드 해야 합니다.

   ![JSON 파일 다운로드](media\search-indexer-howto-secure-access\service-tag.png "JSON 파일 다운로드")

JSON 파일에서 검색 서비스가 미국 서 부에 있다고 가정 하면 다중 테 넌 트 인덱서 실행 환경의 IP 주소 목록이 아래에 나열 됩니다.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

/32 IP 주소에 대해 "/32" (52.253.133.74/32 > 52.253.133.74)를 삭제 하 고 다른 일부는 축 자로 사용할 수 있습니다.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Ip 방화벽 규칙에 IP 주소 범위 추가

저장소 계정의 방화벽 규칙에 IP 주소 범위를 추가 하는 가장 쉬운 방법은 Azure Portal를 통하는 것입니다. 포털에서 저장소 계정을 찾고 "**방화벽 및 가상 네트워크**" 탭으로 이동 합니다.

   ![방화벽 및 가상 네트워크](media\search-indexer-howto-secure-access\storage-firewall.png "방화벽 및 가상 네트워크")

이전에 얻은 3 개의 IP 주소 (검색 서비스 IP의 경우 1, 서비스 태그의 경우 2 `AzureCognitiveSearch` )를 주소 범위에 추가 하 고 "**저장**"을 클릭 합니다.

   ![방화벽 IP 규칙](media\search-indexer-howto-secure-access\storage-firewall-ip.png "방화벽 IP 규칙")

방화벽 규칙을 업데이트 하는 데 5-10 분이 걸리며, 인덱서는 저장소 계정의 데이터에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

인덱스에 대 한 액세스를 허용 하기 위해 두 개의 IP 주소 집합을 가져오는 방법을 배웠으므로 다음 링크를 사용 하 여 몇 가지 일반적인 데이터 원본에 대 한 IP 방화벽 규칙을 업데이트 합니다.

- [Azure Storage 방화벽 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security)
- [CosmosDB에 대 한 IP 방화벽 구성](https://docs.microsoft.com/azure/cosmos-db/firewall-support)
- [Azure SQL server에 대 한 IP 방화벽 구성](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)