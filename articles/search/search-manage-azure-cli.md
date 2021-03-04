---
title: Az search module을 사용 하 여 스크립트 Azure CLI
titleSuffix: Azure Cognitive Search
description: Azure CLI를 사용 하 여 Azure Cognitive Search 서비스를 만들고 구성 합니다. 서비스의 규모를 확장 또는 축소 하 고, 관리 및 쿼리 api 키를 관리 하 고, 시스템 정보를 쿼리할 수 있습니다.
manager: luisca
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 6287215233ae9baa220df37c6b820c1d1bec7720
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032520"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Cognitive Search 서비스 관리
> [!div class="op_single_selector"]
> * [포털](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Windows, macOS, Linux 또는 [Azure Cloud Shell](../cloud-shell/overview.md) 에서 Azure CLI 명령 및 스크립트를 실행 하 여 Azure Cognitive Search를 만들고 구성할 수 있습니다. [**Az search**](/cli/azure/search) 모듈은 [검색 관리 REST api](/rest/api/searchmanagement) 에 대 한 전체 패리티가 있는 [Azure CLI](/cli/) 를 확장 하 고 다음 작업을 수행 하는 기능을 제공 합니다.

> [!div class="checklist"]
> * [구독에서 search 서비스 나열](#list-search-services)
> * [서비스 정보 반환](#get-search-service-information)
> * [서비스 만들기 또는 삭제](#create-or-delete-a-service)
> * [개인 끝점을 사용 하 여 서비스 만들기](#create-a-service-with-a-private-endpoint)
> * [관리 API 다시 생성-키](#regenerate-admin-keys)
> * [쿼리 api 만들기 또는 삭제-키](#create-or-delete-query-keys)
> * [복제본 및 파티션을 사용 하 여 확장 또는 축소](#scale-replicas-and-partitions)
> * [공유 개인 링크 리소스 만들기](#create-a-shared-private-link-resource)

경우에 따라 위의 목록에 *없는* 작업에 대 한 질문을 받게 됩니다. 현재는 **az search** module 또는 management REST API를 사용 하 여 서버 이름, 지역 또는 계층을 변경할 수 없습니다. 서비스를 만들 때 전용 리소스가 할당 됩니다. 따라서 기본 하드웨어 (위치 또는 노드 형식)를 변경 하려면 새 서비스가 필요 합니다. 마찬가지로 한 서비스에서 다른 서비스로 콘텐츠를 전송 하기 위한 도구나 Api는 없습니다.

서비스 내에서 콘텐츠 작성 및 관리는 [Search Service REST API](/rest/api/searchservice/) 또는 [.net SDK](/dotnet/api/overview/azure/search.documents-readme)를 통해 진행 됩니다. 콘텐츠에 대 한 전용 PowerShell 명령은 없지만 REST 또는 .NET Api를 호출 하 여 인덱스를 만들고 로드 하는 스크립트를 작성할 수 있습니다.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-upgrade"></a>버전 확인 및 업그레이드

이 문서의 예는 대화형 이며 높은 권한이 필요 합니다. Azure CLI을 설치 해야 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

이제 `az` Windows 명령 프롬프트, PowerShell 또는 [Azure Cloud Shell](../cloud-shell/overview.md)에서 명령을 사용 하 여 Azure CLI를 실행할 수 있습니다. PowerShell은 Windows 명령 프롬프트에서 사용할 수 없는 일부 탭 완성 기능을 제공합니다. 

### <a name="check-the-azure-cli-version"></a>Azure CLI 버전 확인

Azure CLI 설치 되어 있는지 확실 하지 않은 경우 확인 단계로 다음 명령을 실행 합니다. 

```azurecli-interactive
az --version
```
이 명령이 작동 하지 않는 경우 설치 Azure CLI을 가져오려면 [Azure CLI 설치](/cli/azure/install-azure-cli) 를 참조 하세요.

버전 2.11.0 이상이 면 `az upgrade` 명령을 실행 하 여 CLI를 최신 버전으로 업데이트할 수 있습니다.

```azurecli-interactive
az upgrade
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>브라우저 로그인 토큰을 사용 하 여 Azure에 연결

포털 로그인 자격 증명을 사용 하 여 Azure CLI 구독에 연결할 수 있습니다. 또는 [서비스 주체를 사용 하 여 비 대화형으로 인증할](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)수 있습니다.

```azurecli-interactive
az login
```

여러 Azure 구독을 보유 하는 경우 Azure 구독을 설정 합니다. 현재 구독 목록을 보려면 다음 명령을 실행합니다.

```azurecli-interactive
az account list --output table
```

구독을 지정하려면 다음 명령을 실행합니다. 다음 예제에서 구독 이름은 `ContosoSubscription`입니다.

```azurecli-interactive
az account set --subscription "ContosoSubscription"
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>구독의 서비스 나열

다음 명령은 [**az resource**](/cli/azure/resource)에서 구독에 이미 프로 비전 된 기존 리소스 및 서비스에 대 한 정보를 반환 합니다. 이미 생성 된 검색 서비스 수를 모르는 경우 이러한 명령은 해당 정보를 반환 하 여 포털에 대 한 여행 정보를 저장 합니다.

첫 번째 명령은 모든 검색 서비스를 반환 합니다.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

서비스 목록에서 특정 리소스에 대 한 정보를 반환 합니다.

```azurecli-interactive
az resource list --name <service-name>
```

## <a name="list-all-az-search-commands"></a>모든 az search 명령 나열

CLI 내에서 [**az search**](/cli/azure/search) 에서 사용할 수 있는 하위 그룹 및 명령에 대 한 정보를 볼 수 있습니다. 또는 [설명서](/cli/azure/search)를 검토할 수 있습니다.

내에서 사용할 수 있는 하위 그룹을 보려면 `az search` 다음 명령을 실행 합니다.

```azurecli-interactive
az search --help
```

응답은 다음 출력과 유사 하 게 표시 됩니다.

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

각 하위 그룹 내에서 여러 명령을 사용할 수 있습니다. `service`다음 줄을 실행 하 여 하위 그룹에 사용할 수 있는 명령을 볼 수 있습니다.

```azurecli-interactive
az search service --help
```

특정 명령에 사용할 수 있는 인수도 확인할 수도 있습니다.

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>검색 서비스 정보 가져오기

검색 서비스를 포함 하는 리소스 그룹을 알고 있는 경우 [**az search service show**](/cli/azure/search/service#az_search_service_show) 를 실행 하 여 이름, 지역, 계층, 복제본 및 파티션 개수를 포함 한 서비스 정의를 반환 합니다.

```azurecli-interactive
az search service show --name <service-name> --resource-group <resource-group-name>
```

## <a name="create-or-delete-a-service"></a>서비스 만들기 또는 삭제

[새 검색 서비스를 만들려면](search-create-service-portal.md) [**az search service create**](/cli/azure/search/service#az_search_service_show) 명령을 사용 합니다.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

결과는 다음 출력과 유사 하 게 표시 됩니다.

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>IP 규칙을 사용 하 여 서비스 만들기

보안 요구 사항에 따라 [구성 된 IP 방화벽](service-configure-firewall.md)을 사용 하 여 검색 서비스를 만들 수 있습니다. 이렇게 하려면 아래와 같이 공용 IP (v4) 주소나 CIDR 범위를 인수에 전달 합니다 `ip-rules` . 규칙은 쉼표 ( `,` ) 또는 세미콜론 ()으로 구분 해야 합니다 `;` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>시스템 할당 관리 id를 사용 하 여 서비스 만들기

[관리 id를 사용 하 여 데이터 원본에 연결](search-howto-managed-identities-storage.md)하는 경우와 같은 일부 경우에는 [시스템 할당 관리 id](../active-directory/managed-identities-azure-resources/overview.md)를 설정 해야 합니다. 이 작업은 명령에를 추가 하 여 수행 됩니다 `--identity-type SystemAssigned` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>개인 끝점을 사용 하 여 서비스 만들기

Azure Cognitive Search에 대 한 [개인 끝점](../private-link/private-endpoint-overview.md) 은 가상 네트워크의 클라이언트가 [개인 링크](../private-link/private-link-overview.md)를 통해 검색 인덱스의 데이터에 안전 하 게 액세스할 수 있도록 합니다. 개인 끝점은 검색 서비스에 대 한 [가상 네트워크 주소 공간의](../virtual-network/private-ip-addresses.md) IP 주소를 사용 합니다. 클라이언트와 검색 서비스 간의 네트워크 트래픽은 가상 네트워크 및 Microsoft 백본 네트워크의 개인 링크를 순회 하 여 공용 인터넷에서 노출 되는 것을 제거 합니다. 자세한 내용은 [Azure에 대 한 개인 끝점을 만드는](service-create-private-endpoint.md) 방법에 대 한 설명서를 참조 하세요 Cognitive Search

다음 예제에서는 개인 끝점을 사용 하 여 검색 서비스를 만드는 방법을 보여 줍니다.

먼저가로 설정 된 검색 서비스를 배포 `PublicNetworkAccess` `Disabled` 합니다.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

다음으로 가상 네트워크와 개인 끝점을 만듭니다.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --query [id]' \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

마지막으로, 사설 DNS 영역을 만듭니다. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

PowerShell에서 개인 끝점을 만드는 방법에 대 한 자세한 내용은이 [개인 링크 빠른](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli) 시작을 참조 하세요.

### <a name="manage-private-endpoint-connections"></a>프라이빗 엔드포인트 연결 관리

개인 끝점 연결을 만드는 것 외에도, 및 연결을 사용할 수 있습니다 `show` `update` `delete` .

개인 끝점 연결을 검색 하 고 해당 상태를 확인 하려면 [**az search private-endpoint-connection show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show)를 사용 합니다.

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

연결을 업데이트 하려면 [**az search 개인 끝점-연결 업데이트**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update)를 사용 합니다. 다음 예제에서는 개인 끝점 연결이 거부 됨으로 설정 합니다.

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

개인 끝점 연결을 삭제 하려면 [**az search private-endpoint-connection delete**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete)를 사용 합니다.

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>관리자 키 다시 생성

관리 [API 키](search-security-api-keys.md)를 롤오버 하려면 [**az search admin-키 갱신**](/cli/azure/search/admin-key#az_search_admin_key_renew)을 사용 합니다. 각 서비스에서 인증 된 액세스에 대해 두 개의 관리 키가 생성 됩니다. 모든 요청에 키가 필요 합니다. 두 관리 키는 기능적으로 동일 하며, 모든 정보를 검색 하거나 개체를 만들고 삭제할 수 있는 기능을 사용 하 여 검색 서비스에 대 한 모든 쓰기 권한을 부여 합니다. 다른 키를 대체할 때 사용할 수 있도록 두 개의 키가 있습니다. 

또는 키로 지정 된 한 번에 하나씩만 다시 생성할 수 `primary` 있습니다 `secondary` . 중단 없는 서비스의 경우 기본 키를 롤링 하는 동안 보조 키를 사용 하도록 모든 클라이언트 코드를 업데이트 해야 합니다. 작업이 진행 되는 동안에는 키를 변경 하지 마세요.

짐작할 수 있듯이 클라이언트 코드를 업데이트 하지 않고 키를 다시 생성 하면 이전 키를 사용 하는 요청이 실패 합니다. 모든 새 키를 다시 생성 해도 서비스에서 영구적으로 잠기지 않으며 포털을 통해 서비스에 계속 액세스할 수 있습니다. 기본 및 보조 키를 다시 생성 한 후 새 키를 사용 하도록 클라이언트 코드를 업데이트할 수 있으며 그에 따라 작업이 다시 시작 됩니다.

API 키에 대 한 값은 서비스에 의해 생성 됩니다. 사용할 Azure Cognitive Search에 대 한 사용자 지정 키를 제공할 수 없습니다. 마찬가지로, 관리 API 키에 대 한 사용자 정의 이름이 없습니다. 키에 대 한 참조는 고정 문자열 `primary` 또는 `secondary` 입니다. 

```azurecli-interactive
az search admin-key renew \
    --resource-group <resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

결과는 다음 출력과 비슷합니다. 두 키는 한 번에 하나만 변경 하더라도 반환 됩니다.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>쿼리 키 만들기 또는 삭제

클라이언트 앱에서 Azure Cognitive Search 인덱스에 읽기 전용으로 액세스 하기 위한 쿼리 [API 키](search-security-api-keys.md) 를 만들려면 [**az Search query-key create**](/cli/azure/search/query-key#az_search_query_key_create)를 사용 합니다. 쿼리 키는 검색 결과를 검색 하기 위해 특정 인덱스에 인증 하는 데 사용 됩니다. 쿼리 키는 인덱스, 데이터 원본 또는 인덱서와 같은 서비스의 다른 항목에 대 한 읽기 전용 액세스 권한을 부여 하지 않습니다.

사용할 Azure Cognitive Search에 대 한 키를 제공할 수 없습니다. API 키는 서비스에 의해 생성 됩니다.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>복제본 및 파티션 크기 조정

[복제본 및 파티션을 늘리거나 줄이려면](search-capacity-planning.md) [**az search service update**](/cli/azure/search/service#az_search_service_update)를 사용 합니다. 복제본 또는 파티션을 증가 시키면 청구에 추가 되 고,이에 따라 고정 및 가변 요금이 청구 됩니다. 추가 처리 성능이 일시적으로 필요한 경우에는 워크 로드를 처리 하기 위해 복제본과 파티션을 늘릴 수 있습니다. 개요 포털 페이지의 모니터링 영역에는 쿼리 대기 시간, 초당 쿼리 수 및 제한 (현재 용량이 충분 한지 여부를 나타냄)에 대 한 타일이 있습니다.

높아지면를 추가 하거나 제거 하는 데 시간이 걸릴 수 있습니다. 용량에 대 한 조정 작업은 백그라운드에서 발생 하므로 기존 작업을 계속할 수 있습니다. 추가 구성은 필요 없이 들어오는 요청에 대해 준비 되는 즉시 추가 용량이 사용 됩니다. 

용량 제거는 방해가 될 수 있습니다. 삭제 된 요청을 방지 하기 위해 용량을 줄이기 전에 모든 인덱싱 및 인덱서 작업을 중지 하는 것이 좋습니다. 가능 하지 않은 경우 새 대상 수준에 도달할 때까지 한 번에 하나의 복제본과 파티션을 증분 방식으로 줄이는 것이 좋습니다.

명령을 제출한 후에는 중간에 종료할 수 있는 방법이 없습니다. 개수를 수정 하기 전에 명령이 완료 될 때까지 기다려야 합니다.

```azurecli-interactive
az search service update \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

복제본 및 파티션 수를 업데이트 하는 것 외에도, 및를 업데이트할 수 있습니다 `ip-rules` `public-access` `identity-type` .

## <a name="create-a-shared-private-link-resource"></a>공유 개인 링크 리소스 만들기

Azure Cognitive Search Api를 통해 생성 된 보안 리소스의 개인 끝점을 *공유 개인 링크 리소스* 라고 합니다. 이는 [Azure 개인 링크 서비스](https://azure.microsoft.com/services/private-link/)와 통합 된 저장소 계정과 같은 리소스에 대 한 액세스를 "공유" 하는 것입니다.

인덱서를 사용 하 여 Azure Cognitive Search에서 데이터를 인덱싱하는 경우 데이터 원본이 개인 네트워크에 있는 경우 아웃 바운드 [개인 끝점 연결](../private-link/private-endpoint-overview.md) 을 만들어 데이터에 연결할 수 있습니다.

Azure Cognitive Search에서 아웃 바운드 개인 끝점을 만들 수 있는 Azure 리소스의 전체 목록은 [여기](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) 에서 관련 된 **그룹 ID** 값과 함께 찾을 수 있습니다.

공유 개인 링크 리소스를 만들려면 [**az search shared-link-resource create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list)를 사용 합니다. 이 명령을 실행 하기 전에 일부 구성이 데이터 원본에 필요할 수 있습니다.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


공유 개인 링크 리소스를 검색 하 고 해당 상태를 확인 하려면 [**az search 공유-전용-링크-리소스 목록**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list)을 사용 합니다.

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

다음 명령을 사용 하 여 연결을 승인 해야 사용할 수 있습니다. 자식 리소스에서 개인 끝점 연결의 ID를 검색 해야 합니다. 이 경우 az storage에서 연결 ID를 가져옵니다.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

공유 개인 링크 리소스를 삭제 하려면 [**az search shared-link-resource delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete)를 사용 합니다.

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

공유 개인 링크 리소스를 설정 하는 방법에 대 한 자세한 내용은 [개인 끝점을 통한 인덱서 연결 만들기](search-indexer-howto-access-private.md)에 대 한 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[인덱스](search-what-is-an-index.md)를 작성 하 고 포털, REST api 또는 .net SDK를 사용 하 여 [인덱스를 쿼리](search-query-overview.md) 합니다.

* [Azure Portal에서 Azure Cognitive Search 인덱스 만들기](search-get-started-portal.md)
* [다른 서비스에서 데이터를 로드 하는 인덱서 설정](search-indexer-overview.md)
* [Azure Portal에서 검색 탐색기를 사용 하 여 Azure Cognitive Search 인덱스 쿼리](search-explorer.md)
* [.NET에서 Azure Cognitive Search를 사용 하는 방법](search-howto-dotnet-sdk.md)
