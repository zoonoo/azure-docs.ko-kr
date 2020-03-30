---
title: Az.Search 모듈을 사용하는 PowerShell 스크립트
titleSuffix: Azure Cognitive Search
description: PowerShell을 사용하여 Azure 인지 검색 서비스를 만들고 구성합니다. 서비스를 확장 또는 축소하고, 관리자 및 쿼리 API키를 관리하고, 시스템 정보를 쿼리할 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209299"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>PowerShell을 사용하여 Azure 인지 검색 서비스 관리
> [!div class="op_single_selector"]
> * [포털](search-manage.md)
> * [Powershell](search-manage-powershell.md)
> * [나머지 API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [파이썬](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Windows, Linux 또는 [Azure 클라우드 셸에서](https://docs.microsoft.com/azure/cloud-shell/overview) PowerShell cmdlet 및 스크립트를 실행하여 Azure 인지 검색을 만들고 구성할 수 있습니다. **Az.Search** 모듈은 [검색 관리 REST API와](https://docs.microsoft.com/rest/api/searchmanagement) 다음 작업을 수행할 수 있는 기능을 완전히 패리티로 [Azure PowerShell을](https://docs.microsoft.com/powershell/) 확장합니다.

> [!div class="checklist"]
> * [구독에서 검색 서비스 나열](#list-search-services)
> * [반품 서비스 정보](#get-search-service-information)
> * [서비스 만들기 또는 삭제](#create-or-delete-a-service)
> * [관리자 API 키 재생성](#regenerate-admin-keys)
> * [쿼리 API 키 만들기 또는 삭제](#create-or-delete-query-keys)
> * [복제본 및 파티션으로 확장 또는 축소](#scale-replicas-and-partitions)

경우에 따라 위의 목록에 *없는* 작업에 대한 질문이 표시됩니다. 현재 **는 Az.Search** 모듈 또는 관리 REST API를 사용하여 서버 이름, 지역 또는 계층을 변경할 수 없습니다. 전용 리소스는 서비스를 만들 때 할당됩니다. 따라서 기본 하드웨어(위치 또는 노드 유형)를 변경하려면 새 서비스가 필요합니다. 마찬가지로 인덱스와 같은 콘텐츠를 한 서비스에서 다른 서비스로 전송하기 위한 도구나 API가 없습니다.

서비스 내에서 콘텐츠 생성 및 관리는 [검색 서비스 REST API](https://docs.microsoft.com/rest/api/searchservice/) 또는 [.NET SDK를](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search)통해 관리됩니다. 콘텐츠전용 PowerShell 명령은 없지만 REST 또는 .NET API를 호출하여 인덱스를 만들고 로드하는 PowerShell 스크립트를 작성할 수 있습니다.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>버전 및 로드 모듈 확인

이 문서의 예제는 대화형이며 높은 사용 권한이 필요합니다. Azure **PowerShell(Az** 모듈)을 설치해야 합니다. 자세한 내용은 [Azure PowerShell 설치를](/powershell/azure/overview)참조하십시오.

### <a name="powershell-version-check-51-or-later"></a>PowerShell 버전 확인(5.1 이상)

로컬 PowerShell은 지원되는 모든 운영 체제에서 5.1 이상이어야 합니다.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Azure 파워셸 로드

**Az가** 설치되어 있는지 확실하지 않은 경우 다음 명령을 확인 단계로 실행합니다. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

일부 시스템은 모듈을 자동 로드하지 않습니다. 이전 명령에 오류가 발생하면 모듈을 로드해 보고 실패하면 설치 지침으로 돌아가 단계를 놓쳤는지 확인합니다.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>브라우저 로그인 토큰으로 Azure에 연결

포털 로그인 자격 증명을 사용하여 PowerShell의 구독에 연결할 수 있습니다. 또는 서비스 [주체와 비대화형으로 인증할](../active-directory/develop/howto-authenticate-service-principal-powershell.md)수 있습니다.

```azurepowershell-interactive
Connect-AzAccount
```

여러 Azure 구독을 보유하는 경우 Azure 구독을 설정합니다. 현재 구독 목록을 보려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

구독을 지정하려면 다음 명령을 실행합니다. 다음 예제에서 구독 이름은 `ContosoSubscription`입니다.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>구독의 서비스 목록

다음 명령은 [**Az.Resources에서**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources)구독에 이미 프로비전된 기존 리소스 및 서비스에 대한 정보를 반환합니다. 이미 생성된 검색 서비스 수를 모르는 경우 이러한 명령은 해당 정보를 반환하여 포털로의 이동을 저장합니다.

첫 번째 명령은 모든 검색 서비스를 반환합니다.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

서비스 목록에서 특정 리소스에 대한 정보를 반환합니다.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

결과는 다음 출력과 비슷합니다.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>가져오기 Az.검색

모듈을 로드할 때까지 [**Az.Search의**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) 명령을 사용할 수 없습니다.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>모든 Az.Search 명령 나열

확인 단계로 모듈에 제공된 명령 목록을 반환합니다.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

결과는 다음 출력과 비슷합니다.

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

## <a name="get-search-service-information"></a>검색 서비스 정보 받기

**Az.Search를** 가져오고 검색 서비스가 포함된 리소스 그룹을 알고 나면 [Get-AzSearchService를](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) 실행하여 이름, 지역, 계층 및 복제본 및 파티션 수를 포함한 서비스 정의를 반환합니다.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

결과는 다음 출력과 비슷합니다.

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

## <a name="create-or-delete-a-service"></a>서비스 만들기 또는 삭제

[**New-AzSearchService는**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) [새 검색 서비스를 만드는](search-create-service-portal.md)데 사용됩니다.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
결과는 다음 출력과 비슷합니다.

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

[**New-AzSearchAdminKey는**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) 관리자 API [키를](search-security-api-keys.md)롤오버하는 데 사용됩니다. 인증된 액세스를 위해 각 서비스에 두 개의 관리 키가 만들어집니다. 모든 요청시 열쇠가 필요합니다. 두 관리자 키는 기능적으로 동일하므로 정보를 검색하거나 개체를 만들고 삭제할 수 있는 검색 서비스에 대한 전체 쓰기 액세스 권한을 부여합니다. 다른 키를 교체하는 동안 하나를 사용할 수 있도록 두 개의 키가 있습니다. 

`primary` 또는 `secondary` 키로 지정된 한 번에 하나씩만 다시 생성할 수 있습니다. 중단 없는 서비스의 경우 기본 키를 롤오버하는 동안 보조 키를 사용하도록 모든 클라이언트 코드를 업데이트해야 합니다. 작업이 비행 하는 동안 키를 변경 하지 마십시오.

예상대로 클라이언트 코드를 업데이트하지 않고 키를 다시 생성하면 이전 키를 사용하는 요청이 실패합니다. 모든 새 키를 다시 생성해도 서비스가 영구적으로 중단되지는 않으며 포털을 통해 서비스에 계속 액세스할 수 있습니다. 기본 키와 보조 키를 다시 생성한 후 클라이언트 코드를 업데이트하여 새 키를 사용할 수 있으며 그에 따라 작업이 재개됩니다.

API 키에 대한 값은 서비스에서 생성됩니다. 사용할 Azure 인지 검색에 대 한 사용자 지정 키를 제공할 수 없습니다. 마찬가지로 관리자 API 키에 대한 사용자 정의 이름이 없습니다. 키에 대한 참조는 고정 `primary` 된 `secondary`문자열 또는 . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

결과는 다음 출력과 비슷합니다. 한 번에 하나씩만 변경해도 두 키 모두 반환됩니다.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>쿼리 키 만들기 또는 삭제

[**New-AzSearchQueryKey는**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) 클라이언트 앱에서 Azure 인지 검색 인덱스에 대한 읽기 전용 액세스를 위한 쿼리 [API 키를](search-security-api-keys.md) 만드는 데 사용됩니다. 쿼리 키는 검색 결과를 검색하기 위해 특정 인덱스를 인증하는 데 사용됩니다. 쿼리 키는 인덱스, 데이터 원본 또는 인덱서와 같은 서비스의 다른 항목에 대한 읽기 전용 액세스 권한을 부여하지 않습니다.

Azure 인지 검색에서 사용할 키를 제공할 수 없습니다. API 키는 서비스에 의해 생성됩니다.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>복제본 및 파티션 확장

[**Set-AzSearchService는**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) 서비스 내에서 청구 가능한 리소스를 다시 조정하기 위해 [복제본 및 파티션을 늘리거나 줄이는](search-capacity-planning.md) 데 사용됩니다. 복제본 또는 파티션을 늘리면 청구서에 고정 요금과 가변 요금이 모두 추가됩니다. 추가 처리 능력이 일시적으로 필요한 경우 복제본과 파티션을 늘려 워크로드를 처리할 수 있습니다. 개요 포털 페이지의 모니터링 영역에는 쿼리 대기 시간, 초당 쿼리 및 제한에 대한 타일이 있으며 현재 용량이 적절한지 여부를 나타냅니다.

리소싱을 추가하거나 제거하는 데 시간이 걸릴 수 있습니다. 용량에 대한 조정은 백그라운드에서 수행되므로 기존 워크로드를 계속할 수 있습니다. 추가 용량은 추가 구성없이 준비되는 즉시 들어오는 요청에 사용됩니다. 

용량을 제거하는 것은 중단될 수 있습니다. 용량 감소 전에 모든 인덱싱 및 인덱서 작업을 중지하면 요청이 중단되지 않도록 하는 것이 좋습니다. 이 경우 새 대상 수준에 도달할 때까지 한 번에 하나의 복제본 및 파티션으로 용량을 점진적으로 줄이는 것이 좋습니다.

명령을 제출하면 중간에 명령을 종료할 수 없습니다. 카운트를 수정하기 전에 명령이 완료될 때까지 기다려야 합니다.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

결과는 다음 출력과 비슷합니다.

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

[인덱스를](search-what-is-an-index.md)빌드하고 포털, REST API 또는 .NET SDK를 사용하여 [인덱스를 쿼리합니다.](search-query-overview.md)

* [Azure Portal에서 Azure Cognitive Search 인덱스 만들기](search-create-index-portal.md)
* [다른 서비스에서 데이터를 로드하도록 인덱서 설정](search-indexer-overview.md)
* [Azure 포털에서 검색 탐색기를 사용하여 Azure 인지 검색 인덱스 쿼리](search-explorer.md)
* [.NET에서 Azure 인지 검색을 사용하는 방법](search-howto-dotnet-sdk.md)