---
title: Azure 트래픽 분석 질문과 대답 | Microsoft Docs
description: 트래픽 분석에 대해 자주 묻는 질문에 대한 대답을 알아봅니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 69d2d80e40400cc7fa40aeb5a163dce5036905ab
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49402763"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>트래픽 분석 질문과 대답

이 문서는 Azure Network Watcher의 트래픽 분석에 대한 많은 질문과 대답을 한 곳에서 수집합니다.

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>트래픽 분석을 사용하기 위한 필수 구성 요소는 무엇인가요?

트래픽 분석을 사용하려면 다음 필수 구성 요소가 필요합니다.

- Network Watcher 지원 구독.
- 모니터링할 NSG(네트워크 보안 그룹)에 대해 사용 가능한 NSG 흐름 로그.
- 원시 흐름 로그를 저장할 Azure Storage 계정.
- 읽기 및 쓰기 권한이 있는 Azure Log Analytics 작업 영역.

트래픽 분석을 사용하려면 계정이 다음 중 하나를 충족해야 합니다.

- 구독 수준에서 계정 관리자, 서비스 관리자 또는 공동 관리자 역할 중 하나에 계정이 할당되어야 합니다.
- 구독 범위에서 RBAC(역할 기반 액세스 제어) 역할인 소유자, 참가자, 독자 또는 네트워크 참가자 중 하나가 계정에 있어야 합니다.
- 계정이 이전에 나열된 역할 중 하나에 할당되지 않은 경우, 구독 수준에서 다음 작업이 할당된 사용자 지정 역할에 할당되어야 합니다.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
구독에 대해 사용자에게 할당된 역할을 확인하려면:

1. **Login-AzureRmAccount**를 사용하여 Azure에 로그인합니다. 

2. **Select-AzureRmSubscription**을 사용하여 필요한 구독을 선택합니다. 

3. 지정된 사용자에게 할당된 모든 역할을 나열하려면 **Get-AzureRmRoleAssignment -SignInName [사용자 메일] -IncludeClassicAdministrators**를 사용합니다. 

출력이 표시되지 않으면 각 구독 관리자에게 문의하여 명령을 실행할 권한을 얻으세요. 자세한 내용은 [Azure PowerShell을 사용하여 역할 기반 액세스 제어 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)를 참조하세요.


## <a name="in-which-azure-regions-are-traffic-analytics-available"></a>트래픽 분석을 사용할 수 있는 Azure 지역은 어디인가요?

미국 중서부, 미국 동부, 미국 동부 2, 미국 중북부, 미국 중남부, 미국 중부, 미국 서부, 미국 서부 2, 유럽 서부, 북유럽, 영국 서부, 영국 남부, 오스트레일리아 동부, 오스트레일리아 남동부 및 동남 아시아에서 NSG용 트래픽 분석을 사용할 수 있습니다. Log Analytics 작업 영역이 미국 중서부, 미국 동부, 유럽 서부, 영국 남부, 오스트레일리아 남동부 또는 동남 아시아 지역에 있어야 합니다.

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>흐름 로그를 설정하려는 NSG가 작업 영역과 다른 지역에 있어도 되나요?

예. 이러한 NSG는 Log Analytics 작업 영역과 다른 지역에 있을 수 있습니다.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>단일 작업 영역 내에서 여러 NSG를 구성할 수 있나요?

예.

## <a name="can-i-use-an-existing-workspace"></a>기존 작업 영역을 사용해도 되나요?

예. 기존 작업 영역을 선택하는 경우, 새 쿼리 언어로 마이그레이션되었는지 확인해야 합니다. 작업 영역을 업그레이드하지 않으려면 새 작업 영역을 만들어야 합니다. 새 쿼리 언어에 대한 자세한 내용은 [새 로그 검색으로 Azure Log Analytics 업그레이드](../log-analytics/log-analytics-log-search-upgrade.md)를 참조하세요.

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Azure Storage 계정과 Log Analytics 작업 영역이 서로 다른 구독에 있어도 되나요?

네, Azure Storage 계정이 한 구독에 있고, Log Analytics 작업 영역이 다른 구독에 있어도 됩니다.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>원시 로그를 다른 구독에 저장할 수 있나요?

아니요. 흐름 로그에 NSG를 사용할 수 있는 저장소 계정에 원시 로그를 저장할 수 있습니다. 그러나 저장소 계정과 원시 로그는 둘 다 동일한 구독 및 지역에 있어야 합니다.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>“찾을 수 없음” 오류로 인해 트래픽 분석에 대해 NSG를 구성할 수 없으면 어떻게 하나요?

지원되는 지역을 선택합니다. 지원되지 않는 영역을 선택할 경우 "찾을 수 없음" 오류가 표시됩니다. 지원되는 지역은 이 문서의 앞 부분에 나와 있습니다.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>NSG 흐름 로그 페이지에 상태가 “로드 실패”로 표시되면 어떻게 하나요?

흐름 로깅이 성공적으로 작동하려면 Microsoft.Insights 공급자를 등록해야 합니다. Microsoft.Insights 공급자가 구독에 대해 등록된 것인지 확실하지 않은 경우, 다음 명령에서 *xxxxx-xxxxx-xxxxxx-xxxx*를 바꾼 후 PowerShell에서 다음 명령을 실행합니다.

```powershell-interactive
**Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>솔루션을 구성했습니다. 그런데 왜 대시보드에 아무 것도 표시되지 않나요?

대시보드에 처음으로 항목이 표시될 때까지 최대 30분이 걸릴 수 있습니다. 솔루션에서 의미 있는 인사이트를 얻는 데 필요한 데이터를 충분히 수집한 후에야 보고서가 생성됩니다. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>다음 메시지가 표시되면 어떻게 하나요? “이 작업 영역에서 선택한 시간 간격의 데이터를 찾을 수 없습니다. 시간 간격을 변경하거나 다른 작업 영역을 선택해 보세요.”

다음 옵션을 시도해 보세요.
- 위쪽 메뉴에서 시간 간격을 변경합니다.
- 위쪽 메뉴에서 다른 Log Analytics 작업 영역을 선택합니다.
- 트래픽 분석이 최근에 설정된 경우, 30분 후에 액세스해 봅니다.
    
그래도 문제가 계속되면 [사용자 의견 포럼](https://feedback.azure.com/forums/217313-networking?category_id=195844)에 문제를 올려주세요.

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>다음 메시지가 표시되면 어떻게 하나요? “NSG 흐름 로그를 처음으로 분석하는 중입니다. 이 프로세스를 완료하는 데 20-30분 정도 걸릴 수 있습니다. 잠시 후 다시 확인하십시오. 2) 위의 단계가 작동하지 않거나 작업 영역이 무료 SKU에 소속된 경우, 여기서 작업 영역 사용량을 확인하여 할당량과 비교해 보십시오. 추가 정보가 필요하면 FAQ를 참조하십시오.”

다음 이유로 메시지가 표시될 수 있습니다.
- 트래픽 분석이 최근에 설정되었으며 의미 있는 인사이트를 얻는 데 필요한 데이터를 아직 충분히 수집하지 못했을 수 있습니다.
- Log Analytics 작업 영역의 체험판 버전을 사용하고 있고 할당량 한도를 초과했습니다. 용량이 큰 작업 영역을 사용해야 할 수도 있습니다.
    
그래도 문제가 계속되면 [사용자 의견 포럼](https://feedback.azure.com/forums/217313-networking?category_id=195844)에 문제를 올려주세요.
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>다음 메시지가 표시되면 어떻게 하나요? “리소스 데이터(토폴로지)는 있는데 흐름 정보가 표시되지 않습니다. 리소스 데이터를 보려면 여기를 클릭하고 추가 정보가 필요하면 FAQ를 참조하십시오.”

대시보드에서 리소스 정보를 보고 있지만 흐름 관련 통계가 없습니다. 리소스 간 통신이 없어 데이터가 없는 것일 수 있습니다. 60분 후에 상태를 다시 확인하세요. 문제가 지속되고 리소스 간에 통신 흐름이 있다고 확신하는 경우, [사용자 의견 포럼](https://feedback.azure.com/forums/217313-networking?category_id=195844)에 문제를 올려주세요.

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>PowerShell 또는 Azure Resource Manager 템플릿 또는 클라이언트를 사용하여 트래픽 분석을 구성할 수 있나요?

버전 6.2.1부터 Windows PowerShell을 사용하여 트래픽 분석을 구성할 수 있습니다. Set cmdlet을 사용하여 특정 NSG에 대한 흐름 로깅 및 트래픽 분석을 구성하려면 [Set-AzureRmNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog?view=azurermps-6.3.0)를 참조하세요. 특정 NSG에 대한 흐름 로깅 및 트래픽 분석 상태를 가져오려면 [Get-AzureRmNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkwatcherflowlogstatus?view=azurermps-6.3.0)를 참조하세요.

현재 트래픽 분석을 구성하는 데는 Azure Resource Manager 템플릿을 사용할 수 없습니다.

Azure Resource Manager 클라이언트를 사용하여 트래픽 분석을 구성하려면 다음 예제를 참조하세요.

**Set cmdlet 예제:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Get cmdlet 예제:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```



## <a name="how-is-traffic-analytics-priced"></a>트래픽 분석의 비용은 얼마인가요?

트래픽 분석은 요금이 측정됩니다. 요금은 서비스에서 생성된 흐름 로그 데이터 처리 및 Log Analytics 작업 영역에서 생성된 향상된 로그 저장을 기준으로 측정됩니다. 

예를 들어 [가격 책정 계획](https://azure.microsoft.com/pricing/details/network-watcher/)과 관련해서 미국 중서부 지역을 고려해보겠습니다. 트래픽 분석에 의해 처리되는 저장소 계정에 저장된 흐름 로그 데이터가 10GB이고 Log Analytics 작업 영역에서 수집된 보강된 로그가 1GB인 경우 해당 요금은 10 x 2.3$ + 1 x 2.76$ = 25.76$가 됩니다.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>지역 지도 보기에서 키보드를 사용하여 탐색할 수 있나요?

지역 지도 페이지에는 두 개의 기본 섹션이 있습니다.
    
- **배너**: 지역 지도 위쪽에 있는 배너는 트래픽 분포 필터(예: 배포, 국가의 트래픽 및 악성)를 선택하는 단추를 제공합니다. 단추를 선택하면 각 필터가 지도에 적용됩니다. 예를 들어, [활성] 단추를 선택하면 지도에서 배포의 활성 데이터 센터가 강조 표시됩니다.
- **지도**: 배너 아래의 지도 섹션에는 Azure 데이터 센터와 국가 간의 트래픽 분포가 표시됩니다.
    
### <a name="keyboard-navigation-on-the-banner"></a>배너에서 키보드 탐색
    
- 기본적으로 배너에 대한 지역 지도 페이지에서 선택할 수 있는 항목은 “Azure DC” 필터입니다.
- 다른 필터로 이동하려면 `Tab` 또는 `Right arrow` 키를 사용합니다. 뒤로 이동하려면 `Shift+Tab` 또는 `Left arrow` 키를 사용합니다. 앞으로 탐색은 왼쪽에서 오른쪽, 위쪽에서 아래쪽 순입니다.
- `Enter` 또는 `Down` 화살표 키를 누르면 선택한 필터가 적용됩니다. 선택하는 필터와 배포에 따라 지도 섹션 아래에 있는 하나 이상의 노드가 강조 표시됩니다.
- 배너 및 지도 사이에서 전환하려면 `Ctrl+F6` 키를 누릅니다.
        
### <a name="keyboard-navigation-on-the-map"></a>지도에서 키보드 탐색
    
- 배너에서 필터를 선택하고 `Ctrl+F6`을 누르면 지도 보기에서 강조 표시된 노드 중 하나(**Azure 데이터 센터** 또는 **국가/지역**)로 포커스가 이동됩니다.
- 지도에서 강조 표시된 다른 노드로 이동하려면 `Tab` 또는 `Right arrow` 키를 사용하여 앞으로 이동합니다. 뒤로 이동하려면 `Shift+Tab` 또는 `Left arrow` 키를 사용합니다.
- 지도에서 강조 표시된 노드를 선택하려면 `Enter` 또는 `Down arrow` 키를 사용합니다.
- 노드를 선택하면 해당 노드의 **정보 도구 상자**로 포커스가 이동됩니다. 기본적으로 포커스는 **정보 도구 상자**의 닫힌 단추로 이동합니다. **상자** 보기 내부에서 추가로 이동하려면 `Right arrow` 및 `Left arrow` 키를 사용하여 각각 앞으로 또는 뒤로 이동할 수 있습니다. `Enter` 키를 누르면 **정보 도구 상자**에서 포커스가 있는 단추를 선택한 것과 동일한 효과가 적용됩니다.
- **정보 도구 상자**에 포커스가 있는 동안 `Tab` 키를 누르면 선택된 노드와 동일한 대륙의 끝점으로 포커스가 이동합니다. `Right arrow` 및 `Left arrow` 키를 사용하여 이러한 엔드포인트 간을 이동합니다.
- 다른 흐름 엔드포인트 또는 대륙 클러스터로 이동하려면 `Tab` 키를 사용하여 앞으로 이동하고 `Shift+Tab` 키를 사용하여 뒤로 이동합니다.
- **대륙 클러스터**에 포커스가 있을 때 `Enter` 또는 `Down` 화살표 키를 사용하여 대륙 클러스터 내부의 엔드포인트를 강조 표시할 수 있습니다. 대륙 클러스터의 정보 상자에서 엔드포인트와 [닫기] 단추 간을 이동하려면 `Right arrow` 또는 `Left arrow` 키를 사용하여 각각 앞으로 또는 뒤로 이동할 수 있습니다. 아무 엔드포인트에서 `Shift+L` 키를 사용하여 선택한 노드와 엔드포인트를 잇는 연결선으로 전환할 수 있습니다. `Shift+L`을 다시 누르면 선택한 엔드포인트로 이동할 수 있습니다.
        
### <a name="keyboard-navigation-at-any-stage"></a>모든 단계에서 키보드 탐색
    
- `Esc` 키는 확장된 선택 영역을 축소합니다.
- `Up arrow` 키는 `Esc` 키와 동일한 작업을 수행합니다. `Down arrow` 키는 `Enter` 키와 동일한 작업을 수행합니다.
- `Shift+Plus` 키로 확대, `Shift+Minus` 키로 축소할 수 있습니다.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>가상 네트워크 토폴로지 보기에서 키보드를 사용하여 탐색할 수 있나요?

가상 네트워크 토폴로지 페이지에는 두 개의 기본 섹션이 있습니다.
    
- **배너**: 가상 네트워크 토폴로지 위쪽에 있는 배너는 트래픽 분포 필터(예: 연결된 가상 네트워크, 연결이 끊어진 가상 네트워크 및 공용 IP)를 선택하는 단추를 제공합니다. 단추를 선택하면 각 필터가 토폴로지에 적용됩니다. 예를 들어, [활성] 단추를 선택하면 토폴로지에서 배포의 활성 가상 네트워크가 강조 표시됩니다.
- **토폴로지**: 배너 아래의 토폴로지 섹션에는 가상 네트워크 간의 트래픽 분포가 표시됩니다.
    
### <a name="keyboard-navigation-on-the-banner"></a>배너에서 키보드 탐색
    
- 기본적으로 배너에 대한 가상 네트워크 토폴로지 페이지에서 선택할 수 있는 항목은 “연결된 VNet” 필터입니다.
- 다른 필터로 이동하려면 `Tab` 키를 사용하여 앞으로 이동합니다. 뒤로 이동하려면 `Shift+Tab` 키를 사용합니다. 앞으로 탐색은 왼쪽에서 오른쪽, 위쪽에서 아래쪽 순입니다.
- `Enter` 키를 누르면 선택한 필터가 적용됩니다. 선택하는 필터와 배포에 따라 토폴로지 섹션 아래에 있는 하나 이상의 노드(가상 네트워크)가 강조 표시됩니다.
- 배너와 토폴로지 사이를 전환하려면 `Ctrl+F6`을 누릅니다.
        
### <a name="keyboard-navigation-on-the-topology"></a>토폴로지에서 키보드 탐색
    
- 배너에서 필터를 선택하고 `Ctrl+F6`을 누르면 토폴로지 보기에서 강조 표시된 노드 중 하나(**VNet**)로 포커스가 이동됩니다.
- 토폴로지 보기에서 강조 표시된 다른 노드로 이동하려면 `Shift+Right arrow` 키를 사용하여 앞으로 이동합니다. 
- 강조 표시된 노드에서는 노드의 **정보 도구 상자**로 포커스가 이동됩니다. 기본적으로 포커스는 **정보 도구 상자**의 **자세한 정보** 단추로 이동합니다. **상자** 보기 내부에서 추가로 이동하려면 `Right arrow` 및 `Left arrow` 키를 사용하여 각각 앞으로 또는 뒤로 이동할 수 있습니다. `Enter` 키를 누르면 **정보 도구 상자**에서 포커스가 있는 단추를 선택한 것과 동일한 효과가 적용됩니다.
- 이러한 노드를 선택할 때 `Shift+Left arrow` 키를 눌러 해당 연결을 모두 하나씩 방문할 수 있습니다. 포커스가 해당 연결의 **정보 도구 상자**로 이동합니다. 언제든지 `Shift+Right arrow`를 다시 눌러 포커스를 다시 노드로 이동할 수 있습니다.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>서브넷 토폴로지 보기에서 키보드를 사용하여 탐색할 수 있나요?

서브네트워크 토폴로지 페이지에는 두 개의 기본 섹션이 있습니다.
    
- **배너**: 가상 서브네트워크 토폴로지 위쪽에 있는 배너는 트래픽 분포 필터(예: 활성, 중간 및 게이트웨이 서브넷)를 선택하는 단추를 제공합니다. 단추를 선택하면 각 필터가 토폴로지에 적용됩니다. 예를 들어, [활성] 단추를 선택하면 토폴로지에서 배포의 활성 가상 서브네트워크가 강조 표시됩니다.
- **토폴로지**: 배너 아래의 토폴로지 섹션에는 가상 서브네트워크 간의 트래픽 분포가 표시됩니다.
    
### <a name="keyboard-navigation-on-the-banner"></a>배너에서 키보드 탐색
    
- 기본적으로 배너에 대한 가상 서브네트워크 토폴로지 페이지에서 선택할 수 있는 항목은 “서브넷” 필터입니다.
- 다른 필터로 이동하려면 `Tab` 키를 사용하여 앞으로 이동합니다. 뒤로 이동하려면 `Shift+Tab` 키를 사용합니다. 앞으로 탐색은 왼쪽에서 오른쪽, 위쪽에서 아래쪽 순입니다.
- `Enter` 키를 누르면 선택한 필터가 적용됩니다. 선택하는 필터와 배포에 따라 토폴로지 섹션 아래에 있는 하나 이상의 노드(서브넷)가 강조 표시됩니다.
- 배너와 토폴로지 사이를 전환하려면 `Ctrl+F6`을 누릅니다.
        
### <a name="keyboard-navigation-on-the-topology"></a>토폴로지에서 키보드 탐색
    
- 배너에서 필터를 선택하고 `Ctrl+F6`을 누르면 토폴로지 보기에서 강조 표시된 노드 중 하나(**서브넷**)로 포커스가 이동됩니다.
- 토폴로지 보기에서 강조 표시된 다른 노드로 이동하려면 `Shift+Right arrow` 키를 사용하여 앞으로 이동합니다. 
- 강조 표시된 노드에서는 노드의 **정보 도구 상자**로 포커스가 이동됩니다. 기본적으로 포커스는 **정보 도구 상자**의 **자세한 정보** 단추로 이동합니다. **상자** 보기 내부에서 추가로 이동하려면 `Right arrow` 및 `Left arrow` 키를 사용하여 각각 앞으로 또는 뒤로 이동할 수 있습니다. `Enter` 키를 누르면 **정보 도구 상자**에서 포커스가 있는 단추를 선택한 것과 동일한 효과가 적용됩니다.
- 이러한 노드를 선택할 때 `Shift+Left arrow` 키를 눌러 해당 연결을 모두 하나씩 방문할 수 있습니다. 포커스가 해당 연결의 **정보 도구 상자**로 이동합니다. 언제든지 `Shift+Right arrow`를 다시 눌러 포커스를 다시 노드로 이동할 수 있습니다.    

