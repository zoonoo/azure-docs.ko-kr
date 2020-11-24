---
title: VM용 Azure Monitor에 대해 Log Analytics 작업 영역 구성
description: VM용 Azure Monitor에서 사용 하는 Log Analytics 작업 영역을 만들고 구성 하는 방법을 설명 합니다.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: d3bedfae88544d0b0ebd07868438559305279fed
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95530117"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>VM용 Azure Monitor에 대해 Log Analytics 작업 영역 구성
VM용 Azure Monitor Azure Monitor의 Log Analytics 작업 영역 하나 이상에서 데이터를 수집 합니다. 에이전트를 온 보 딩 하기 전에 작업 영역을 만들고 구성 해야 합니다. 이 문서에서는 작업 영역에 대 한 요구 사항을 설명 하 고 VM용 Azure Monitor에 대해 구성 합니다.

## <a name="overview"></a>개요
단일 구독은 요구 사항에 따라 원하는 수의 작업 영역을 사용할 수 있습니다. 작업 영역의 유일한 요구 사항은 지원 되는 위치에 있고 *VMInsights* 솔루션을 사용 하 여 구성 되어야 한다는 것입니다.

작업 영역을 구성한 후에는 사용 가능한 옵션 중 하나를 사용 하 여 VM 및 VMSS에 필요한 에이전트를 설치 하 고 데이터를 보낼 작업 영역을 지정할 수 있습니다. VM용 Azure Monitor는 해당 구독의 구성 된 작업 영역에서 데이터를 수집 합니다.

> [!NOTE]
> Azure Portal를 사용 하 여 단일 VM 또는 VMSS에서 VM용 Azure Monitor를 사용 하도록 설정 하면 기존 작업 영역을 선택 하거나 새 작업 영역을 만들 수 있습니다. *VMInsights* 솔루션은이 작업 영역에 아직 설치 되지 않은 경우 설치 됩니다. 그런 다음 다른 에이전트에 대해이 작업 영역을 사용할 수 있습니다.


## <a name="create-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

>[!NOTE]
>이 섹션에서 설명 하는 정보는 [서비스 맵 솔루션](service-map.md)에도 적용 됩니다. 

**Log Analytics 작업 영역** 메뉴에서 Azure Portal Log Analytics 작업 영역에 액세스 합니다.

[![로그 Anlytics 작업 영역](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

다음 방법 중 하나를 사용 하 여 새 Log Analytics 작업 영역을 만들 수 있습니다. 사용자 환경에서 사용 해야 하는 작업 영역 수를 결정 하는 방법 및 해당 액세스 전략을 설계 하는 방법에 대 한 지침은 [Azure Monitor 로그 배포 디자인](../platform/design-logs-deployment.md) 을 참조 하세요.


* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

## <a name="supported-regions"></a>지원되는 지역

모든 지역에서 가상 컴퓨터를 모니터링할 수는 있지만 VM용 Azure Monitor은 다음 지역에서 Log Analytics 작업 영역을 지원 합니다. 가상 컴퓨터 자체는 Log Analytics 작업 영역에서 지원 되는 지역으로 제한 되지 않습니다.

- 미국 중서부
- 미국 서부
- 미국 서부 2
- 미국 중남부
- 미국 동부
- 미국 동부2
- 미국 중부
- 미국 중북부
- US Gov Az
- US Gov Va
- 캐나다 중부
- 영국 남부
- 북유럽
- 서유럽
- 동아시아
- 동남아시아
- 인도 중부
- 일본 동부
- 오스트레일리아 동부
- 오스트레일리아 남동부

## <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어
VM용 Azure Monitor의 기능을 사용 하도록 설정 하 고 액세스 하려면 작업 영역에 [Log Analytics 참가자 역할이](../platform/manage-access.md#manage-access-using-azure-permissions) 있어야 합니다. 성능, 상태 및 지도 데이터를 보려면 Azure VM에 대 한 [모니터링 독자 역할이](../platform/roles-permissions-security.md#built-in-monitoring-roles) 있어야 합니다. Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../platform/manage-access.md)를 참조하세요.

## <a name="add-vminsights-solution-to-workspace"></a>작업 영역에 VMInsights 솔루션 추가
VM용 Azure Monitor에서 Log Analytics 작업 영역을 사용 하려면 먼저 *VMInsights* 솔루션이 설치 되어 있어야 합니다. 작업 영역을 구성 하는 방법은 다음 섹션에 설명 되어 있습니다.

> [!NOTE]
> *VMInsights* 솔루션을 작업 영역에 추가 하면 작업 영역에 연결 된 모든 기존 가상 컴퓨터에서 데이터를 InsightsMetrics로 보내기 시작 합니다. 다른 데이터 형식에 대 한 데이터는 작업 영역에 연결 된 기존 가상 컴퓨터에 Dependency Agent를 추가할 때까지 수집 되지 않습니다.

### <a name="azure-portal"></a>Azure portal
Azure Portal를 사용 하 여 기존 작업 영역을 구성 하는 세 가지 옵션이 있습니다. 각각은 아래에 설명 되어 있습니다.

단일 작업 영역을 구성 하려면 **다른 온 보 딩 옵션** 을 선택한 다음 **작업 영역을 구성** 합니다. 구독 및 작업 영역을 선택한 다음 **구성** 을 클릭 합니다.

[![작업 영역 구성](media/vminsights-enable-at-scale-policy/configure-workspace.png)](media/vminsights-enable-at-scale-policy/configure-workspace.png#lightbox)

여러 작업 영역을 구성 하려면 Azure Portal **모니터** 메뉴의 **Virtual Machines** 메뉴에서 **작업 영역 구성** 탭을 선택 합니다. 필터 값을 설정 하 여 기존 작업 영역 목록을 표시 합니다. 사용할 각 작업 영역 옆의 상자를 선택 하 고 **선택 구성** 을 클릭 합니다.

[![작업 영역 구성](media/vminsights-enable-at-scale-policy/workspace-configuration.png)](media/vminsights-enable-at-scale-policy/workspace-configuration.png#lightbox)


Azure Portal를 사용 하 여 단일 VM 또는 VMSS에서 VM용 Azure Monitor를 사용 하도록 설정 하면 기존 작업 영역을 선택 하거나 새 작업 영역을 만들 수 있습니다. *VMInsights* 솔루션은이 작업 영역에 아직 설치 되지 않은 경우 설치 됩니다. 그런 다음 다른 에이전트에 대해이 작업 영역을 사용할 수 있습니다.

[![포털에서 단일 VM 사용](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager 템플릿
VM용 Azure Monitor에 대 한 Azure Resource Manager 템플릿은 [GitHub 리포지토리에서 다운로드할](https://aka.ms/VmInsightsARMTemplates)수 있는 보관 파일 (.zip)로 제공 됩니다. 여기에는 VM용 Azure Monitor에 대 한 Log Analytics 작업 영역을 구성 하는 **ConfigureWorkspace** 라는 템플릿이 포함 됩니다. 아래 샘플 PowerShell 및 CLI 명령을 비롯 한 표준 메서드 중 하나를 사용 하 여이 템플릿을 배포 합니다. 

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>다음 단계
- 에이전트를 VM용 Azure Monitor에 연결 [VM용 Azure Monitor에 대 한 온보드 에이전트](vminsights-enable-overview.md) 를 참조 하세요.
- 솔루션에서 작업 영역으로 전송 되는 데이터의 양을 제한 하려면 [Azure Monitor (미리 보기)의 모니터링 솔루션 대상 지정](solution-targeting.md) 을 참조 하세요.
