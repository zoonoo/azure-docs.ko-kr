---
title: 전용 끝점을 통해 보안 리소스에 액세스 하는 인덱서
titleSuffix: Azure Cognitive Search
description: 보안 리소스와 통신 하는 인덱서의 개인 끝점 설정에 대해 설명 하는 방법 가이드
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 9ffd7d2513e87f818001d7ccf96212a4dbef7ac2
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950145"
---
# <a name="accessing-secure-resources-via-private-endpoints"></a>개인 끝점을 통해 보안 리소스 액세스

Azure 리소스 (예: 데이터 원본으로 사용 되는 저장소 계정)는 특정 가상 네트워크 목록 에서만 액세스할 수 있도록 구성할 수 있습니다. "공용 네트워크" 액세스를 허용 하지 않도록 구성할 수도 있습니다.
고객은 인덱서를 통해 이러한 데이터 소스의 데이터에 안전 하 게 액세스 하기 위해 (아웃 바운드) [개인 끝점 연결](../private-link/private-endpoint-overview.md) 을 만들도록 Azure Cognitive Search를 요청할 수 있습니다.

## <a name="shared-private-link-resources-management-apis"></a>공유 개인 링크 리소스 관리 Api

고객 요청에 따라 Azure Cognitive Search에서 만든 개인 끝점을 "보안" 리소스에 액세스 하는 것을 *공유 개인 링크 리소스*라고 합니다. 고객은 [Azure 개인 링크 서비스](https://azure.microsoft.com/services/private-link/)에 대 한 온-등록을 포함 하는 리소스 (예: 저장소 계정)에 대 한 액세스를 "공유" 합니다.

Azure Cognitive Search는 [공유 개인 링크 리소스를 만들거나 업데이트](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate)하는 기능을 제공 하는 검색 관리 API를 통해 제공 됩니다. 다른 *공유 개인 링크 리소스* 관리 api와 함께이 api를 사용 하 여 Azure Cognitive Search 인덱서에 보안 리소스에 대 한 액세스를 구성 합니다.

일부 리소스에 대 한 개인 끝점 연결은 `2020-08-01-Preview` 아래 표에 있는 "preview" 태그로 표시 된 검색 관리 API ()의 미리 보기 버전을 통해서만 만들 수 있습니다. "Preview" 태그가 없는 리소스는 미리 보기 API 뿐만 아니라 GA API ()를 통해 만들 수 있습니다. `2020-08-01`

다음은 Azure Cognitive Search에서 아웃 바운드 개인 끝점을 만들 수 있는 Azure 리소스 목록입니다. `groupId` 아래 표에 나열 된는 공유 개인 링크 리소스를 만들기 위해 API에서 정확히 (대/소문자 구분) 사용 해야 합니다.

| Azure 리소스 | 그룹 ID |
| --- | --- |
| Azure Storage-Blob (또는) ADLS Gen 2 | `blob`|
| Azure Storage-테이블 | `table`|
| Azure Cosmos DB-SQL API | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (미리 보기) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (미리 보기) | `sites` |

아웃 바운드 개인 끝점 연결을 지 원하는 Azure 리소스의 목록은 지원 되는 [LIST API](/rest/api/searchmanagement/privatelinkresources/listsupported)를 통해 쿼리할 수도 있습니다.

이 가이드의 목적을 위해 [ARMClient](https://github.com/projectkudu/ARMClient) 및 [postman](https://www.postman.com/) 을 함께 사용 하 여 REST API 호출을 보여 줍니다.

> [!NOTE]
> 이 가이드에서는 구독 ID가 __00000000-0000-0000-0000-000000000000__인 구독의 리소스 그룹 __contoso__ 에 존재 하는 검색 서비스 이름이 __contoso-search__ 인 것으로 가정 합니다. 이 검색 서비스의 리소스 ID는 다음과 같을 예정입니다. `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

이 가이드의 나머지 부분에서는 해당 인덱서가 보안 저장소 계정의 데이터에 액세스할 수 있도록 __contoso search__ 서비스를 구성 하는 방법을 보여 줍니다. `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>저장소 계정 보안 설정

[특정 서브넷 에서만 액세스할 수 있도록](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)저장소 계정을 구성 합니다. Azure Portal를 통해이 옵션을 선택 하 고 해당 설정을 빈 상태로 두면 가상 네트워크의 트래픽이 허용 되지 않습니다.

   ![Virtual Network 액세스](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "Virtual Network 액세스")

> [!NOTE]
> 신뢰할 수 있는 [Microsoft 서비스 접근 방식을](../storage/common/storage-network-security.md#trusted-microsoft-services) 사용 하 여 이러한 저장소 계정에 대 한 가상 네트워크 또는 IP 제한을 우회할 수 있으며, [방법 가이드](search-indexer-howto-access-trusted-service-exception.md)에 설명 된 대로 search 서비스에서 저장소 계정의 데이터에 액세스할 수 있도록 할 수 있습니다. 그러나이 방법을 사용 하는 경우 Azure Cognitive Search와 저장소 계정 간의 통신은 보안 Microsoft 백본 네트워크를 통해 저장소 계정의 공용 IP 주소를 통해 수행 됩니다.

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>1 단계: 저장소 계정에 대 한 공유 개인 링크 리소스 만들기

저장소 계정에 대 한 아웃 바운드 개인 끝점 연결을 만들도록 Azure Cognitive Search를 요청 하는 다음 API 호출을 수행 합니다.

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

`create-pe.json`API에 대 한 요청 본문을 나타내는 파일의 내용은 다음과 같습니다.

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

1. 상태의 개인 IP 주소를 사용 하 여 할당 된 개인 끝점 `"Pending"` 입니다. 개인 IP 주소는 검색 서비스별 개인 인덱서 실행 환경의 가상 네트워크에 할당 된 주소 공간에서 가져옵니다. 개인 끝점의 승인 시, Azure Cognitive Search에서 저장소 계정으로의 통신은 개인 IP 주소와 보안 개인 링크 채널에서 시작 됩니다.
2. 을 기반으로 하는 리소스 형식에 대 한 개인 DNS 영역 `groupId` 입니다. 이렇게 하면 전용 리소스에 대 한 DNS 조회가 개인 끝점과 연결 된 IP 주소를 활용 합니다.

`groupId`개인 끝점을 만들 리소스의 형식에 대해 올바른를 지정 해야 합니다. 모든 불일치는 응답 하지 않는 응답 메시지를 반환 합니다.

모든 비동기 Azure 작업과 마찬가지로 호출은 다음과 같이 표시 `PUT` `Azure-AsyncOperation` 되는 헤더 값을 반환 합니다.

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

이 URI를 정기적으로 폴링하여 작업 상태를 가져올 수 있습니다. 계속 하기 전에 공유 개인 링크 리소스 작업 상태가 터미널 상태 ()에 도달할 때까지 대기 하는 것이 좋습니다 `succeeded` .

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>2a 단계: 저장소 계정에 대 한 개인 끝점 연결 승인

> [!NOTE]
> 이 섹션에서는 Azure Portal를 사용 하 여 개인 끝점에서 저장소로의 승인 흐름을 안내 합니다. RP (저장소 리소스 공급자)를 통해 사용할 수 있는 [REST API](/rest/api/storagerp/privateendpointconnections) 을 대신 사용할 수도 있습니다.
>
> CosmosDB, Azure SQL server 등의 다른 공급자도 개인 끝점 연결을 관리 하는 유사한 RP Api를 제공 합니다.

Azure Portal에서 저장소 계정의 "**개인 끝점 연결**" 탭으로 이동 합니다. 비동기 작업이 __성공__하면 이전 API 호출의 요청 메시지와 함께 개인 끝점 연결에 대 한 요청이 있어야 합니다.

   ![개인 끝점 승인](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "개인 끝점 승인")

Azure Cognitive Search에서 만든 개인 끝점을 선택 하 고 ("개인 끝점" 열을 사용 하 여 이전 API에 지정 된 이름으로 개인 끝점 연결을 식별), 적절 한 메시지와 함께 "승인"을 선택 합니다 (메시지가 중요 하지 않음). 개인 끝점 연결이 다음과 같이 표시 되는지 확인 합니다. 포털에서 상태를 업데이트 하는 데 1-2 분이 걸릴 수 있습니다.

![개인 끝점 승인 됨](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "개인 끝점 승인 됨")

개인 끝점 연결 요청이 승인 된 후에는 트래픽이 개인 끝점을 통해 전달 될 *수* 있음을 의미 합니다. 개인 끝점이 승인 되 면 Azure Cognitive Search는 생성 된 DNS 영역에서 필요한 DNS 영역 매핑을 만듭니다.

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>2b 단계: 공유 개인 링크 리소스의 상태 쿼리

 승인 후 공유 개인 링크 리소스가 업데이트 되었는지 확인 하려면 [GET API](/rest/api/searchmanagement/sharedprivatelinkresources/get)를 통해 해당 상태를 가져옵니다.

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

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>3 단계: 개인 환경에서 실행 되도록 인덱서 구성

> [!NOTE]
> 개인 끝점 연결이 승인 되기 전에도이 단계를 수행할 수 있습니다. 개인 끝점 연결이 승인 될 때 까지는 보안 리소스 (예: 저장소 계정)와 통신 하려고 하는 모든 인덱서가 일시적 오류 상태가 됩니다. 새 인덱서가 생성 되지 않습니다. 개인 끝점 연결이 승인 되는 즉시 인덱서는 개인 저장소 계정에 액세스할 수 있습니다.

1. 보안 저장소 계정 및 저장소 계정 내의 적절 한 컨테이너를 가리키는 [데이터 원본을 만듭니다](/rest/api/searchservice/create-data-source) . 다음은 Postman을 통해 수행 되는이 요청을 보여 줍니다.
![데이터 원본 만들기](media\search-indexer-howto-secure-access\create-ds.png "데이터 원본 만들기")

2. 마찬가지로 REST API 사용 하 여 [인덱스를 만들고](/rest/api/searchservice/create-index) 필요에 따라 [기술를 만듭니다](/rest/api/searchservice/create-skillset) .

3. 위에서 만든 데이터 원본, 인덱스 및 기술을 가리키는 [인덱서를 만듭니다](/rest/api/searchservice/create-indexer) . 또한 인덱서 구성 속성을로 설정 하 여 인덱서를 개인 실행 환경에서 강제로 실행 합니다 `executionEnvironment` `"Private"` .
![인덱서 만들기](media\search-indexer-howto-secure-access\create-idr.png "인덱서 만들기")

인덱서를 성공적으로 만들어야 하며, 개인 끝점 연결을 통해 저장소 계정에서 콘텐츠를 인덱싱 해야 합니다. 인덱서 상태는 [인덱서 상태 API](/rest/api/searchservice/get-indexer-status)를 통해 모니터링할 수 있습니다.

> [!NOTE]
> 기존 인덱서가 이미 있는 경우 [PUT API](/rest/api/searchservice/create-indexer) 를 통해이를 업데이트 하 여를로 설정할 수 있습니다 `executionEnvironment` `"Private"` .

## <a name="troubleshooting-issues"></a>문제 해결

- 인덱서를 만들 때 "데이터 원본 자격 증명이 잘못 되었습니다."와 유사한 오류 메시지와 함께 생성이 실패 하면 개인 끝점 연결이 *승인* 되지 않았거나 작동 하지 않음을 의미 합니다.
[GET API](/rest/api/searchmanagement/sharedprivatelinkresources/get)를 사용 하 여 공유 개인 링크 리소스의 상태를 가져옵니다. *승인* 된 경우 리소스의를 확인 합니다 `properties.provisioningState` . `Incomplete`이 경우 리소스에 대 한 기본 종속성 중 일부를 프로 비전 하지 못했습니다 .이는 문제를 `PUT` 해결 해야 하는 공유 개인 링크 리소스를 "다시 만들기" 위해 요청을 다시 실행 하는 것입니다. 다시 승인이 필요할 수도 있습니다. 다시 확인 하려면 리소스의 상태를 다시 확인 하세요.
- 를 설정 하지 않고 인덱서를 만드는 경우 `executionEnvironment` 인덱서 생성이 성공할 수 있지만 실행 기록은 인덱서 실행에 실패 했음을 표시 합니다. 실행 환경을 지정 하려면 [인덱서를 업데이트](/rest/api/searchservice/update-indexer) 해야 합니다.
- 를 설정 하지 않고 인덱서를 만든 경우 `executionEnvironment` 성공적으로 실행 되 면 Azure Cognitive Search는 해당 실행 환경이 검색 서비스별 "개인" 환경 이라는 것을 결정 했음을 의미 합니다. 그러나이는 다양 한 요소 (인덱서에서 사용 하는 리소스, 검색 서비스에 대 한 로드 등)에 따라 변경 될 수 있으며 나중에 실패할 수 있습니다 `executionEnvironment` . 나중에 실패 하지 않도록를로 설정 하는 것이 좋습니다 `"Private"` .
- [할당량 및 한도](search-limits-quotas-capacity.md) 는 만들 수 있는 공유 개인 링크 리소스의 수와 검색 서비스의 SKU에 따라 달라 지는 방식을 결정 합니다.

## <a name="next-steps"></a>다음 단계

개인 끝점에 대 한 자세한 정보:

- [개인 끝점 이란?](../private-link/private-endpoint-overview.md)
- [전용 끝점에 필요한 DNS 구성](../private-link/private-endpoint-dns.md)