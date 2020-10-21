---
title: 개인 끝점을 통한 인덱서 연결
titleSuffix: Azure Cognitive Search
description: 개인 끝점을 통해 보호 되는 다른 Azure 리소스의 콘텐츠에 액세스 하는 인덱서 연결을 구성 합니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: ff8aa6688d8a838fa2e06d2eef546025cdd9213f
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340056"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>개인 끝점을 통해 인덱서 연결 만들기

Azure storage 계정과 같은 많은 Azure 리소스는 가상 네트워크 목록에서 연결을 허용 하도록 구성 하 고 공용 네트워크에서 시작 되는 외부 연결을 거부할 수 있습니다. 인덱서를 사용 하 여 Azure Cognitive Search에서 데이터를 인덱싱하는 경우 데이터 원본이 개인 네트워크에 있는 경우 아웃 바운드 [개인 끝점 연결](../private-link/private-endpoint-overview.md) 을 만들어 데이터에 연결할 수 있습니다.

이 인덱서 연결 방법에는 다음 두 가지 요구 사항이 적용 됩니다.

+ 콘텐츠 또는 코드를 제공 하는 Azure 리소스는 이전에 [Azure 개인 링크 서비스](https://azure.microsoft.com/services/private-link/)에 등록 되어 있어야 합니다.

+ Azure Cognitive Search 서비스는 기본 계층 이상에 있어야 합니다. 무료 계층에서는이 기능을 사용할 수 없습니다. 또한 인덱서에 기술이 있는 경우 계층은 표준 2 (S2) 이상 이어야 합니다. 자세한 내용은 [서비스 제한](search-limits-quotas-capacity.md#shared-private-link-resource-limits)을 참조 하세요.

## <a name="shared-private-link-resources-management-apis"></a>공유 개인 링크 리소스 관리 Api

Azure Cognitive Search Api를 통해 생성 된 보안 리소스의 개인 끝점을 *공유 개인 링크 리소스*라고 합니다. 이는 [Azure 개인 링크 서비스](https://azure.microsoft.com/services/private-link/)와 통합 된 저장소 계정과 같은 리소스에 대 한 액세스를 "공유" 하는 것입니다.

Azure Cognitive Search는 관리 REST API를 통해 Azure Cognitive Search 인덱서에서 액세스를 구성 하는 데 사용할 수 있는 [Createorupdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) 작업을 제공 합니다.

다음 표에서 *미리* 보기가 지정 된 검색 관리 API (버전 *2020-08-01-preview* 이상)의 미리 보기 버전을 사용 하 여 일부 리소스에 대 한 개인 끝점 연결을 만들 수 있습니다. *미리 보기가* 지정 되지 않은 리소스는 미리 보기 또는 일반적으로 사용 가능한 API 버전 (*2020-08-01* 이상)을 사용 하 여 만들 수 있습니다.

다음 표에서는 Azure Cognitive Search에서 아웃 바운드 개인 끝점을 만들 수 있는 Azure 리소스를 나열 합니다. 공유 개인 링크 리소스를 만들려면 API에 작성 된 대로 정확 하 게 **그룹 ID** 값을 입력 합니다. 이 값은 대/소문자를 구분합니다.

| Azure 리소스 | 그룹 ID |
| --- | --- |
| Azure Storage-Blob (또는) ADLS Gen 2 | `blob`|
| Azure Storage-테이블 | `table`|
| Azure Cosmos DB-SQL API | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (미리 보기) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (미리 보기) | `sites` |

지원 되는 [api 목록을](/rest/api/searchmanagement/privatelinkresources/listsupported)사용 하 여 아웃 바운드 개인 끝점 연결이 지원 되는 Azure 리소스를 쿼리할 수도 있습니다.

이 문서의 나머지 부분에서는 [ARMClient](https://github.com/projectkudu/ARMClient) 및 [postman](https://www.postman.com/) api를 함께 사용 하 여 REST API 호출을 보여 줍니다.

> [!NOTE]
> 이 문서의 예는 다음과 같은 가정을 기반으로 합니다.
> * 검색 서비스의 이름은 _contoso-search_이며 구독 ID가 _00000000-0000-0000-0000-000000000000_인 구독의 _contoso_ 리소스 그룹에 있습니다. 
> * 이 검색 서비스의 리소스 ID는 _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search_입니다.

예제의 나머지 부분에서는 해당 인덱서가 보안 저장소 계정 _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage_의 데이터에 액세스할 수 있도록 _contoso search_ 서비스를 구성 하는 방법을 보여 줍니다.

## <a name="secure-your-storage-account"></a>저장소 계정 보호

[특정 서브넷 에서만 액세스할 수 있도록](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)저장소 계정을 구성 합니다. Azure Portal에서이 옵션을 선택 하 고 설정을 빈 상태로 두면 가상 네트워크에서 트래픽이 허용 되지 않습니다.

   ![선택한 네트워크에 대 한 액세스를 허용 하는 옵션을 보여 주는 "방화벽 및 가상 네트워크" 창의 스크린샷 ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> 신뢰할 수 있는 [Microsoft 서비스 접근 방식을](../storage/common/storage-network-security.md#trusted-microsoft-services) 사용 하 여 저장소 계정에 대 한 가상 네트워크 또는 IP 제한을 우회할 수 있습니다. 또한 검색 서비스를 사용 하 여 저장소 계정의 데이터에 액세스할 수 있습니다. 이렇게 하려면 [신뢰할 수 있는 서비스 예외를 사용 하 여 Azure Storage에 대 한 인덱서 액세스](search-indexer-howto-access-trusted-service-exception.md)를 참조 하세요. 
>
> 그러나이 방법을 사용 하는 경우 Azure Cognitive Search와 저장소 계정 간의 통신은 보안 Microsoft 백본 네트워크를 통해 저장소 계정의 공용 IP 주소를 통해 수행 됩니다.

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>1 단계: 저장소 계정에 대 한 공유 개인 링크 리소스 만들기

저장소 계정에 대 한 아웃 바운드 개인 끝점 연결을 만들도록 Azure Cognitive Search를 요청 하려면 다음 API 호출을 수행 합니다. 

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

API에 대 한 요청 본문을 나타내는 *create-pe.js* 파일의 내용은 다음과 같습니다.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

`202 Accepted`성공 시 응답이 반환 됩니다. 아웃 바운드 개인 끝점을 만드는 프로세스는 장기 실행 (비동기) 작업입니다. 여기에는 다음 리소스 배포가 포함 됩니다.

* 상태의 개인 IP 주소를 사용 하 여 할당 된 개인 끝점 `"Pending"` 입니다. 개인 IP 주소는 검색 서비스별 전용 인덱서에 대 한 실행 환경의 가상 네트워크에 할당 된 주소 공간에서 가져옵니다. 개인 끝점의 승인 시, Azure Cognitive Search에서 저장소 계정으로의 통신은 개인 IP 주소와 보안 개인 링크 채널에서 시작 됩니다.

* 을 기반으로 하는 리소스 형식에 대 한 개인 DNS 영역 `groupId` 입니다. 이 리소스를 배포 하 여 전용 리소스에 대 한 DNS 조회가 개인 끝점과 연결 된 IP 주소를 활용 하는지 확인 합니다.

`groupId`개인 끝점을 만들 리소스의 형식에 대해 올바른를 지정 해야 합니다. 모든 불일치는 응답 하지 않는 응답 메시지를 반환 합니다.

모든 비동기 Azure 작업에서와 같이 `PUT` 호출은 `Azure-AsyncOperation` 다음과 같은 헤더 값을 반환 합니다.

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

이 URI를 주기적으로 폴링하여 작업 상태를 가져올 수 있습니다. 계속 하기 전에 공유 개인 링크 리소스 작업의 상태가 터미널 상태에 도달할 때까지 기다리는 것이 좋습니다. 즉, 작업 상태가 *succeeded*로 표시 됩니다.

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>2a 단계: 저장소 계정에 대 한 개인 끝점 연결 승인

> [!NOTE]
> 이 섹션에서는 Azure Portal를 사용 하 여 개인 끝점의 승인 흐름을 저장소로 안내 합니다. 또는 저장소 리소스 공급자를 통해 사용할 수 있는 [REST API](/rest/api/storagerp/privateendpointconnections) 을 사용할 수 있습니다.
>
> Azure Cosmos DB 또는 Azure SQL Server와 같은 다른 공급자는 개인 끝점 연결을 관리 하기 위한 유사한 저장소 리소스 공급자 Api를 제공 합니다.

1. Azure Portal에서 저장소 계정의 **개인 끝점 연결** 탭을 선택 합니다. 비동기 작업이 성공한 후에는 이전 API 호출의 요청 메시지를 사용 하 여 개인 끝점 연결에 대 한 요청이 있어야 합니다.

   !["개인 끝점 연결" 창이 표시 된 Azure Portal의 스크린샷](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Azure Cognitive Search 만든 개인 끝점을 선택 합니다. **개인 끝점** 열에서, 이전 API에 지정 된 이름으로 개인 끝점 연결을 식별 하 고, **승인**을 선택한 후 적절 한 메시지를 입력 합니다. 메시지 내용이 중요 하지 않습니다. 

   다음 스크린샷에 표시 된 것 처럼 개인 끝점 연결이 표시 되는지 확인 합니다. 포털에서 상태를 업데이트 하는 데 1 ~ 2 분 정도 걸릴 수 있습니다.

   !["개인 끝점 연결" 창에 "승인 됨" 상태를 표시 하는 Azure Portal의 스크린샷](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

개인 끝점 연결 요청이 승인 되 면 트래픽이 개인 끝점을 통해 전달 될 *수* 있습니다. 개인 끝점이 승인 되 면 Azure Cognitive Search는 생성 된 DNS 영역에 필요한 DNS 영역 매핑을 만듭니다.

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>2b 단계: 공유 개인 링크 리소스의 상태 쿼리

승인 후 공유 개인 링크 리소스가 업데이트 되었는지 확인 하려면 [GET API](/rest/api/searchmanagement/sharedprivatelinkresources/get)를 사용 하 여 해당 리소스의 상태를 가져옵니다.

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

`properties.provisioningState`리소스의가이 `Succeeded` 고가 이면 `properties.status` `Approved` 공유 개인 링크 리소스가 작동 하 고 인덱서를 개인 끝점을 통해 통신 하도록 구성할 수 있습니다.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>3 단계: 개인 환경에서 실행 되도록 인덱서 구성

> [!NOTE]
> 개인 끝점 연결이 승인 되기 전에이 단계를 수행할 수 있습니다. 개인 끝점 연결이 승인 될 때 까지는 보안 리소스 (예: 저장소 계정)와 통신 하려고 하는 모든 인덱서가 일시적 오류 상태가 됩니다. 새 인덱서가 생성 되지 않습니다. 개인 끝점 연결이 승인 되는 즉시 인덱서는 개인 저장소 계정에 액세스할 수 있습니다.

1. 보안 저장소 계정 및 저장소 계정 내의 적절 한 컨테이너를 가리키는 [데이터 원본을 만듭니다](/rest/api/searchservice/create-data-source) . 다음 스크린샷은 Postman에서이 요청을 보여 줍니다.

   ![Postman 사용자 인터페이스에 대 한 데이터 원본 생성을 보여 주는 스크린샷](media\search-indexer-howto-secure-access\create-ds.png )

1. 마찬가지로 [인덱스를 만들고](/rest/api/searchservice/create-index) 필요에 따라 REST API를 사용 하 여 [기술를 만듭니다](/rest/api/searchservice/create-skillset) .

1. 이전 단계에서 만든 데이터 원본, 인덱스 및 기술을 가리키는 [인덱서를 만듭니다](/rest/api/searchservice/create-indexer) . 또한 인덱서 구성 속성을로 설정 하 여 개인 실행 환경에서 인덱서를 강제로 실행 합니다 `executionEnvironment` `private` .

   ![Postman 사용자 인터페이스에 대 한 인덱서 생성을 보여 주는 스크린샷](media\search-indexer-howto-secure-access\create-idr.png)

   인덱서를 성공적으로 만든 후에는 개인 끝점 연결을 통해 저장소 계정에서 콘텐츠 인덱싱을 시작 해야 합니다. [인덱서 상태 API](/rest/api/searchservice/get-indexer-status)를 사용 하 여 인덱서의 상태를 모니터링할 수 있습니다.

> [!NOTE]
> 기존 인덱서가 이미 있는 경우를로 설정 하 여 [PUT API](/rest/api/searchservice/create-indexer) 를 통해 업데이트할 수 있습니다 `executionEnvironment` `private` .

## <a name="troubleshooting"></a>문제 해결

- "데이터 원본 자격 증명이 잘못 되었습니다."와 같은 오류 메시지와 함께 인덱서를 만들지 못한 경우 개인 끝점 연결의 상태가 아직 *승인* 되지 않았거나 연결이 작동 하지 않는 것을 의미 합니다. 문제를 해결 하려면 다음을 수행 합니다. 
  * [GET API](/rest/api/searchmanagement/sharedprivatelinkresources/get)를 사용 하 여 공유 개인 링크 리소스의 상태를 가져옵니다. 상태가 *승인*됨 인 경우 `properties.provisioningState` 리소스의를 확인 합니다. 여기에 상태가 인 경우 `Incomplete` 리소스에 대 한 기본 종속성 중 일부를 설정 하지 못했음을 의미 합니다. `PUT`공유 개인 링크 리소스를 다시 만드는 요청을 다시 발급 하면 문제를 해결 해야 합니다. 다시 승인이 필요할 수 있습니다. 리소스 상태를 다시 확인 하 여 문제가 해결 되었는지 확인 합니다.

- 속성을 설정 하지 않고 인덱서를 만드는 경우 `executionEnvironment` 에는 성공적으로 만들 수 있지만 실행 기록은 인덱서 실행이 실패 했음을 보여 줍니다. 문제를 해결 하려면 다음을 수행 합니다.
   * [인덱서를 업데이트](/rest/api/searchservice/update-indexer) 하 여 실행 환경을 지정 합니다.

- 속성을 설정 하지 않고 인덱서를 만든 경우 `executionEnvironment` 성공적으로 실행 되 면 Azure Cognitive Search는 해당 실행 환경이 검색 서비스별 *개인* 환경 이라고 결정 했음을 의미 합니다. 이는 인덱서를 사용 하는 리소스, 검색 서비스에 대 한 로드 및 기타 요인에 따라 변경 될 수 있으며 나중에 실패할 수 있습니다. 문제를 해결 하려면 다음을 수행 합니다.
  * `executionEnvironment`나중에 실패 하지 않도록 속성을로 설정 하는 것이 좋습니다 `private` .

[할당량 및 한도](search-limits-quotas-capacity.md) 는 만들 수 있는 공유 개인 링크 리소스의 수와 검색 서비스의 SKU에 따라 달라 지는 방식을 결정 합니다.

## <a name="next-steps"></a>다음 단계

개인 끝점에 대 한 자세한 정보:

- [개인 끝점 이란?](../private-link/private-endpoint-overview.md)
- [전용 끝점에 필요한 DNS 구성](../private-link/private-endpoint-dns.md)
