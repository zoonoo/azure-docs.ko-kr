---
title: Azure Service Fabric-Azure Monitor 로그를 사용 하 여 모니터링 설정 | Microsoft Docs
description: Azure Service Fabric 클러스터를 모니터링 하기를 시각화 및 분석 하 고 이벤트에 대 한 Azure Monitor 로그를 설정 하는 방법에 알아봅니다.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: c8f7198b59a0fe7ed6775736f8b97f5b5a262640
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306855"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>클러스터에 대 한 Azure Monitor 로그 설정

Azure Monitor 로그는 클러스터 수준 이벤트를 모니터링하는 데 추천됩니다. Azure Resource Manager, PowerShell 또는 Azure Marketplace를 통해 Log Analytics 작업 영역을 설정할 수 있습니다. 나중에 사용 하기 위해 배포의 업데이트 된 Resource Manager 템플릿을 유지 관리 하면 동일한 템플릿을 사용 하 여 Azure Monitor 로그 환경을 설정 합니다. 진단을 사용하도록 설정하여 클러스터를 이미 배포한 경우 Marketplace를 통한 배포가 더 용이합니다. 배포하는 계정에 구독 수준 액세스 권한이 없는 경우 PowerShell 또는 Resource Manager 템플릿을 사용하여 배포합니다.

> [!NOTE]
> Azure Monitor 로그 클러스터 모니터링을 설정 하려면 진단을 클러스터 수준 또는 플랫폼 수준 이벤트를 보기 위해 사용 하도록 설정 해야 합니다. 자세한 내용은 [Windows 클러스터에서 진단을 설정하는 방법](service-fabric-diagnostics-event-aggregation-wad.md) 및 [Linux 클러스터에서 진단을 설정하는 방법](service-fabric-diagnostics-oms-syslog.md)을 참조하세요.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Azure Marketplace를 사용하여 Log Analytics 작업 영역 배포

클러스터를 배포한 후에 Log Analytics 작업 영역을 추가하려면 포털에서 Azure Marketplace로 이동하고 **Service Fabric 분석**을 찾습니다. 이것은 Service Fabric 관련 데이터가 있는 Service Fabric 배포를 위한 사용자 지정 솔루션입니다. 이 프로세스에서 솔루션(정보를 보기 위한 대시보드)과 작업 영역(기본 클러스터 데이터의 집계)을 모두 만듭니다.

1. 왼쪽 탐색 메뉴에서 **새로 만들기**를 선택합니다. 

2. **Service Fabric 분석**을 검색합니다. 표시되는 리소스를 선택합니다.

3. **만들기**를 선택합니다.

    ![Marketplace에서 Service Fabric 분석](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Service Fabric 분석 만들기 창에서 **OMS 작업 영역** 필드에 대해 **작업 영역 선택**을 선택한 다음, **새 작업 영역 만들기**를 선택합니다. 필수 정보를 입력합니다. 단, Service Fabric 클러스터와 작업 영역에 대한 구독이 동일해야 합니다. 입력의 유효성이 검사되면 작업 영역이 배포되기 시작합니다. 배포에 몇 분밖에 걸리지 않습니다.

5. 완료되면 Service Fabric 분석 만들기 창의 맨 아래에서 **만들기**를 다시 선택합니다. **OMS 작업 영역** 아래에 새 작업 영역이 표시되는지 확인합니다. 이 작업은 방금 만든 작업 영역에 솔루션을 추가합니다.

Windows를 사용 하 여 클러스터 이벤트가 저장 된 저장소 계정에 Azure Monitor 로그를 연결 하려면 다음 단계를 사용 하 여 계속 합니다. 

>[!NOTE]
>Windows 클러스터에 대 한 Service Fabric 분석 솔루션만 지원 됩니다. Linux 클러스터에 대 한 문서를 참조 하세요 온 [Linux 클러스터에 대 한 Azure Monitor 로그를 설정 하는 방법을](service-fabric-diagnostics-oms-syslog.md)합니다.  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Log Analytics 작업 영역을 클러스터에 연결 

1. 클러스터에서 가져오는 진단 데이터에 작업 영역을 연결해야 합니다. Service Fabric 분석 솔루션을 만든 리소스 그룹으로 이동합니다. **ServiceFabric\<nameOfWorkspace\>** 를 선택하고 개요 페이지로 이동합니다. 여기서 솔루션 설정과 작업 영역 설정을 변경하고 Log Analytics 작업 영역에 액세스할 수 있습니다.

2. 왼쪽 탐색 메뉴의 **작업 영역 데이터 원본**에서 **저장소 계정 로그**를 선택합니다.

3. **저장소 계정 로그** 페이지에서 맨 위의 **추가**를 선택하여 작업 영역에 클러스터 로그를 추가합니다.

4. **저장소 계정**을 선택하여 클러스터에서 만든 적절한 계정을 추가합니다. 기본 이름을 사용한 경우 저장소 계정은 **sfdg\<resourceGroupName\>** 입니다. **applicationDiagnosticsStorageAccountName**에 사용된 값을 확인하여 클러스터를 배포하는 데 사용된 Azure Resource Manager 템플릿으로 이 이름을 확인할 수도 있습니다. 이름이 표시되지 않는 경우 아래로 스크롤하여 **추가 로드**를 선택합니다. 저장소 계정 이름을 선택합니다.

5. 데이터 형식을 지정합니다. **Service Fabric 이벤트**로 설정합니다.

6. 원본이 **WADServiceFabric\*EventTable**로 자동으로 설정되었는지 확인합니다.

7. **확인**을 선택하여 작업 영역을 클러스터 로그에 연결합니다.

    ![Azure Monitor 로그 저장소 계정 로그 추가](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

이제 계정이 작업 영역의 데이터 원본에서 스토리지 계정 로그의 일부로 표시됩니다.

Log Analytics 작업 영역에 추가한 Service Fabric 분석 솔루션이 이제 클러스터의 플랫폼 및 애플리케이션 로그 표에 제대로 연결되었습니다. 같은 방식으로 작업 영역에 추가적인 원본을 추가할 수 있습니다.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Azure Monitor 로그 Azure Resource Manager 배포

Resource Manager 템플릿을 사용하여 클러스터를 배포하는 경우 템플릿은 새 Log Analytics 작업 영역을 만들고, 작업 영역에 Service Fabric 솔루션을 추가하고, 적절한 스토리지 테이블에서 데이터를 읽도록 구성합니다.

[이 샘플 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure)을 사용하고 요구 사항에 맞게 수정할 수 있습니다. 이 템플릿은 다음을 수행합니다.

* 5 노드 Service Fabric 클러스터 만들기
* Log Analytics 작업 영역 및 Service Fabric 솔루션 만들기
* 작업 영역에 2개 샘플 성능 카운터를 수집 및 보내도록 Log Analytics 에이전트 구성
* Service Fabric을 수집하도록 WAD 구성 및 Azure Storage 테이블로 전송(WADServiceFabric*EventTable)
* 이러한 테이블에서 이벤트를 읽도록 Log Analytics 작업 영역 구성


사용 하 여 클러스터에 템플릿을 Resource Manager 업그레이드로 배포할 수는 `New-AzResourceGroupDeployment` Azure PowerShell 모듈에는 API입니다. 예제 명령은 다음과 같습니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager는 이 명령이 기존 리소스에 대한 업데이트임을 감지합니다. 기존 배포를 구동하는 템플릿과 제공된 새 템플릿 간의 변경 내용만 처리합니다.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Azure PowerShell을 사용 하 여 Azure Monitor 로그를 배포 합니다.

사용 하 여 PowerShell 통해 log analytics 리소스를 배포할 수도 있습니다는 `New-AzOperationalInsightsWorkspace` 명령입니다. 이 방법을 사용하려면 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)을 설치했는지 확인합니다. 이 스크립트를 사용하여 새 Log Analytics 작업 영역을 만들고 여기에 Service Fabric 솔루션을 추가합니다. 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

완료 되 면 Azure Monitor 로그 적절 한 저장소 계정에 연결 하려면 이전 섹션의 단계를 수행 합니다.

PowerShell을 사용하여 Log Analytics 작업 영역에 다른 솔루션을 추가하거나 다른 수정 작업을 할 수도 있습니다. 자세한 내용은 참조 하세요 [Azure Monitor 관리는 PowerShell을 사용 하 여 로그](../azure-monitor/platform/powershell-workspace-configuration.md)합니다.

## <a name="next-steps"></a>다음 단계
* 노드에 [Log Analytics 에이전트를 배포](service-fabric-diagnostics-oms-agent.md)하여 성능 카운터를 수집하고 컨테이너에 대한 docker 통계 및 로그를 수집합니다.
* 알아보기 합니다 [로그 검색 및 쿼리](../log-analytics/log-analytics-log-searches.md) Azure Monitor 로그의 일부로 제공 하는 기능
* [뷰 디자이너를 사용 하 여 Azure Monitor 로그에서 사용자 지정 뷰 만들기](../azure-monitor/platform/view-designer.md)
