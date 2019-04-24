---
title: Az.Search 모듈-Azure Search를 사용 하 여 PowerShell 스크립트
description: 만들고 PowerShell을 사용 하 여 Azure Search 서비스를 구성 합니다. 서비스 확장 또는 축소, 쿼리 시스템 정보 관리 및 쿼리 api-key를 관리할 수 있습니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: heidist
ms.openlocfilehash: 8f07468ccff4431e1afdf66aedc72599ddc0c25b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194277"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>PowerShell을 사용한 Azure Search 서비스 관리
> [!div class="op_single_selector"]
> * [포털](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Windows, Linux에서 또는 PowerShell cmdlet 및 스크립트를 실행할 수 있습니다 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 만들기 및 Azure Search를 구성 합니다. **Az.Search** Azure PowerShell 모듈을 확장 하며]에 완벽 한 패리티를 사용 하 여 합니다 [Azure Search 관리 REST Api](https://docs.microsoft.com/rest/api/searchmanagement)합니다. Azure PowerShell을 사용 하 고 **Az.Search**, 다음 작업을 수행할 수 있습니다.

> [!div class="checklist"]
> * [모든 구독에서 search 서비스 나열](#list-search-services)
> * [특정 검색 서비스에 대 한 정보 가져오기](#get-search-service-information)
> * [만들거나 서비스를 삭제 합니다.](#create-or-delete-a-service)
> * [관리 API 키를 다시 생성](#regenerate-admin-keys)
> * [만들거나 쿼리 api-key를 삭제 합니다.](#create-or-delete-query-keys)
> * [복제본 및 파티션의 늘리거나 줄여 서비스를 크기 조정](#scale-replicas-and-partitions)

이름, 지역 또는 서비스의 계층을 변경 하려면 PowerShell은 사용할 수 없습니다. 전용된 리소스는 서비스가 만들어질 때 할당 됩니다. 기본 하드웨어 (위치 또는 노드 형식)를 변경 하려면 새 서비스 합니다. 다른 하나의 서비스에서 콘텐츠를 전송 하기 위한 없습니다 도구 또는 Api 됩니다. 모든 콘텐츠 관리를 통해 [REST](https://docs.microsoft.com/rest/api/searchservice/) 하거나 [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) Api을 다시 만든 다음 새 서비스에 다시 로드 해야 인덱스를 이동 하려는 경우. 

콘텐츠 관리에 대 한 전용된 PowerShell 명령을 있기는 만들고 인덱스를 로드 하는 REST 또는.NET 호출 하는 PowerShell 스크립트를 작성할 수 있습니다. 합니다 **Az.Search** 자체적으로 모듈은 이러한 작업을 제공 하지 않습니다.

PowerShell 또는 다른 API (포털만 해당)를 통해 지원 되지 않습니다 다른 작업에는 다음이 포함 됩니다.
+ [Cognitive services 리소스를 연결](cognitive-search-attach-cognitive-services.md) 에 대 한 [인덱싱 AI 보강](cognitive-search-concept-intro.md)합니다. Cognitive 서비스는 기술 집합을 구독 아니거나 서비스에 연결 됩니다.
+ [추가 기능 모니터링 솔루션](search-monitor-usage.md#add-on-monitoring-solutions) 또는 [검색 트래픽 분석](search-traffic-analytics.md) Azure Search를 모니터링에 사용 됩니다.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>버전을 확인 하 고 모듈 로드

이 문서의 예제에서는 대화형이 고 상승 된 권한이 필요 합니다. Azure PowerShell (합니다 **Az** 모듈)를 설치 해야 합니다. 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/overview)를 참조하세요.

### <a name="powershell-version-check-51-or-later"></a>PowerShell 버전 확인 (5.1 이상)

로컬 PowerShell 5.1 이상에서 지원 되는 운영 체제 여야 합니다.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Azure PowerShell을 로드 합니다.

확실 하지 않은 경우 여부 **Az** 설치를 확인 단계로 다음 명령을 실행 합니다. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

일부 시스템 모듈이 자동 로드를 수행합니다. 오류가 발생 하는 경우 이전 명령 모듈을 로드 하 고, 실패할 경우 단계를 놓친 경우 확인 하려면 설치 지침을 돌아갑니다.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>브라우저에서 로그인 토큰을 사용 하 여 Azure에 연결

PowerShell에서 구독에 연결 하려면 포털에 로그인 자격 증명을 사용할 수 있습니다. 또는 [서비스 주체를 사용 하 여 비 대화형 인증](../active-directory/develop/howto-authenticate-service-principal-powershell.md)합니다.

```azurepowershell-interactive
Connect-AzAccount
```

여러 Azure 구독을 보유 하는 경우 Azure 구독을 설정 합니다. 현재 구독 목록을 보려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

구독을 지정하려면 다음 명령을 실행합니다. 다음 예제에서 구독 이름은 `ContosoSubscription`입니다.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>구독에서 모든 Azure Search 서비스 나열

다음 명령에서 됩니다 [ **Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), 기존 리소스 및 구독에서 이미 프로 비전 서비스에 대 한 정보를 반환 합니다. 검색 서비스 이미 만들어진 알 수 없는 경우 이러한 명령은 포털로 여정을 저장 하는 해당 정보를 반환 합니다.

첫 번째 명령은 모든 search 서비스를 반환합니다.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

서비스 목록에서 특정 리소스에 대 한 정보를 반환 합니다.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

결과 다음 출력과 유사 합니다.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Import Az.Search

명령을 [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) 는 모듈을 로드할 때까지 사용할 수 없습니다.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>모든 Az.Search 명령 목록

확인 단계에서는 모듈에서 제공 하는 명령의 목록을 반환 합니다.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

결과 다음 출력과 유사 합니다.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>검색 서비스 정보 가져오기

후 **Az.Search** 가져온 실행 검색 서비스를 포함 하는 리소스 그룹을 알고 [Get AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) 이름, 지역, 계층 및 복제본을 포함 하 여 서비스 정의 반환 하 고 파티션 수를 계산 합니다.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

결과 다음 출력과 유사 합니다.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>만들거나 서비스를 삭제 합니다.

[**새 AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) 하는 데 사용 됩니다 [새 search 서비스 만들기](search-create-service-portal.md)합니다.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
결과 다음 출력과 유사 합니다.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>관리자 키 다시 생성

[**새 AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) 관리자를 롤오버 하는 데 사용 됩니다 [API 키](search-security-api-keys.md)합니다. 두 개의 관리자 키는 인증 된 액세스를 위해 각 서비스를 사용 하 여 생성 됩니다. 모든 요청에 키가 필요 합니다. 두 관리자 키가 기능적 모든 정보를 검색 하거나 만들고 모든 개체를 삭제 하는 기능을 사용 하 여 전체 검색 서비스에 대 한 쓰기 권한을 부여 합니다. 두 키가 없는 다른 대체 하는 동안 하나를 사용할 수 있도록 합니다. 

수만 다시 생성 한 번에 하나씩으로 지정 된 `primary` 또는 `secondary` 키입니다. 중단 없이 서비스에 대 한 기본 키를 롤오버 하는 동안 보조 키를 사용 하려면 모든 클라이언트 코드를 업데이트 해야 합니다. 작업이 진행에서 되는 동안 키를 변경 하지 마십시오.

예상할 수 있듯이, 클라이언트 코드를 업데이트 하지 않고 키를 다시 생성 하면, 이전 키를 사용 하 여 요청 실패 합니다. 모든 새 키를 다시 생성지 않습니다 하지 영구적으로 잠글 서비스 및 포털을 통해 서비스에 액세스할 수 있습니다. 기본 및 보조 키를 다시 생성 하면 새 키를 사용 하도록 클라이언트 코드를 업데이트할 수 있습니다 후 작업이 적절 하 게 다시 시작 됩니다.

API 키에 대 한 값은 서비스에 의해 생성 됩니다. 사용 하도록 Azure Search에 대 한 사용자 지정 키를 제공할 수 없습니다. 마찬가지로, admin api-key에 대 한 사용자 정의 이름이 없는 경우 키에 대 한 참조는 고정 문자열, 하거나 `primary` 또는 `secondary`합니다. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

결과 다음 출력과 유사 합니다. 한 번에 하나씩 변경할 수 있지만 키를 모두 반환 됩니다.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>만들기 또는 쿼리 키 삭제

[**새 AzSearchQueryKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) 쿼리를 만드는 데 사용 됩니다 [API 키](search-security-api-keys.md) 에 대 한 읽기 전용 액세스 클라이언트 앱에서 Azure Search 인덱스입니다. 쿼리 키는 검색 결과 검색 하기 위해 특정 인덱스에 대 한 인증에 사용 됩니다. 쿼리 키, 인덱스, 데이터 원본 또는 인덱서 같은 서비스의 다른 항목에 대 한 읽기 전용 액세스를 부여 하지 마세요.

사용 하도록 Azure Search에 대 한 키를 제공할 수 없습니다. API 키는 서비스에 의해 생성 됩니다.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>확장 복제본 및 파티션

[**집합 AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) 하는 데 사용 됩니다 [복제본 및 파티션의 늘리거나](search-capacity-planning.md) 를 서비스 내에서 청구 가능한 리소스를 다시 조정할 합니다. 둘 다 있는 청구서에 추가 하는 복제본 또는 파티션을 늘려 고정 및 변수 요금입니다. 임시 추가 처리 능력이 필요한 경우 복제본 및 파티션의 처리 워크 로드를 늘릴 수 있습니다. 포털 개요 페이지의 모니터링 영역에 쿼리 대기 시간, 두 번째 및 제한, 현재 용량이 충분 한 인지를 나타내는 쿼리 타일에 있습니다.

추가 또는 리소스 관리를 제거 하는 데 걸릴 수 있습니다. 용량을 조정 하려면 기존 워크 로드를 허용 합니다. 백그라운드에서 발생 합니다. 추가 용량 추가 구성이 필요 없는 준비가 완료 된 것으로 들어오는 요청에 사용 됩니다. 

용량을 제거 하는 것은 중단 될 수 있습니다. 삭제 요청을 피하 여 용량은 감소 하기 전에 모든 인덱싱 및 인덱서 작업을 중지 하는 것이 좋습니다. 경우는 불가능 좋습니다 용량을 증분 감소 한 복제본 및 파티션에 새 대상 수준에 도달할 때까지 한 번에 합니다.

명령을 제출 하면 방법이 있으면 종료를 통해 중간에 있는 합니다. 명령 개수를 수정 하기 전에 완료 될 때까지 대기 해야 합니다.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

결과 다음 출력과 유사 합니다.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```


## <a name="next-steps"></a>다음 단계

빌드를 [인덱스](search-what-is-an-index.md)를 [인덱스 쿼리](search-query-overview.md) 포털, REST Api 또는.NET SDK를 사용 하 여 합니다.

* [Azure Portal에서 Azure Search 인덱스 만들기](search-create-index-portal.md)
* [다른 서비스에서 데이터를 로드 하려면 인덱서를 설정](search-indexer-overview.md)
* [Azure Portal에서 검색 탐색기를 사용하여 Azure Search 인덱스 쿼리](search-explorer.md)
* [.NET에서 Azure Search를 사용하는 방법](search-howto-dotnet-sdk.md)