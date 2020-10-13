---
title: Azure Log Analytics 작업 영역 삭제 및 복구 | Microsoft Docs
description: 개인 구독에서 작업 영역을 만들었거나 작업 영역 모델을 재구성한 경우 Log Analytics 작업 영역을 삭제하는 방법을 알아봅니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 292e446d5b713a43f77ee5e579d7e6dd5905ff69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448529"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Azure Log Analytics 작업 영역 삭제 및 복구

이 문서에서는 Azure Log Analytics 작업 영역 일시 삭제의 개념과 삭제된 작업 영역을 복구하는 방법을 설명합니다.

## <a name="considerations-when-deleting-a-workspace"></a>작업 영역 삭제 시 고려 사항

Log Analytics 작업 영역을 삭제하면 삭제가 우연인지 의도적인지 여부에 관계 없이 해당 데이터 및 연결된 에이전트를 포함하여 14일 이내에 작업 영역을 복구할 수 있도록 일시 삭제 작업이 수행됩니다. 일시 삭제 기간이 후 작업 영역 리소스와 해당 데이터는 복구할 수 없습니다. 해당 데이터가 영구적으로 삭제되고 30일 내에 완전히 제거될 때까지 대기됩니다. 작업 영역 이름은 '릴리스'이며 새 작업 영역을 만드는 데 사용할 수 있습니다.

> [!NOTE]
> 일시 삭제 동작을 재정의 하 고 작업 영역을 영구적으로 삭제 하려면 [영구 작업 영역 삭제](#permanent-workspace-delete)의 단계를 수행 합니다.

서비스 작업에 부정적인 영향을 미칠 수 있는 중요한 데이터 및 구성이 있을 수 있으므로 작업 영역을 삭제할 때는 주의해야 합니다. 다음과 같이 Log Analytics에 데이터를 저장 하는 에이전트, 솔루션 및 기타 Azure 서비스를 검토 합니다.

* 관리 솔루션
* Azure Automation
* Windows 및 Linux 가상 머신에서 실행되는 에이전트
* 사용자 환경의 Windows 및 Linux 컴퓨터에서 실행되는 에이전트
* System Center Operations Manager

일시 삭제 작업은 작업 영역 리소스를 삭제하고 연결된 사용자의 사용 권한은 중단됩니다. 사용자가 다른 작업 영역과 연결되어 있으면 다른 작업 영역에서 Log Analytics를 계속 사용할 수 있습니다.

## <a name="soft-delete-behavior"></a>일시 삭제 동작

작업 영역 삭제 작업은 작업 영역 Resource Manager 리소스를 제거하지만 해당 구성과 데이터는 14일 동안 유지하는 동시에 작업 영역이 삭제된 것처럼 보이게 합니다. 작업 영역에 보고하도록 구성된 모든 에이전트 및 System Center Operations Manager 관리 그룹은 일시 삭제 기간 동안 분리된 상태로 유지됩니다. 또한 서비스는 해당 데이터와 연결된 리소스를 포함하여 삭제된 작업 영역을 복구하고 기본적으로 삭제를 취소하는 메커니즘을 제공합니다.

> [!NOTE] 
> 설치된 솔루션과 Azure Automation 계정과 같은 연결된 서비스는 삭제 시 작업 영역에서 영구적으로 제거되어 복구할 수 없습니다. 이전에 구성된 상태로 작업 영역을 가져오기 위해 복구 작업 후 다시 구성해야 합니다.

[PowerShell](/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [REST API](/rest/api/loganalytics/workspaces/delete) 또는 [Azure Portal](https://portal.azure.com)을 사용하여 작업 영역을 삭제할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics 작업 영역**을 선택합니다.
3. Log Analytics 작업 영역 목록에서 작업 영역을 선택한 다음, 가운데 창 위쪽에서 **삭제**를 클릭합니다.
4. 지난 주 동안 작업 영역에 대한 데이터 수집을 보여 주는 확인 페이지가 나타납니다. 작업 영역의 이름을 입력하여 확인한 다음, **삭제**를 클릭합니다.

   ![작업 영역 삭제 확인](media/delete-workspace/workspace-delete.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>영구 작업 영역 삭제
일시 삭제 방법은 개발 및 테스트와 같은 일부 시나리오에 맞지 않을 수 있습니다. 이 경우 동일한 설정 및 작업 영역 이름으로 배포를 반복해야 합니다. 이 경우 작업 영역을 영구적으로 삭제하고 일시 삭제 기간을 "재정의"할 수 있습니다. 영구 작업 영역 삭제 작업은 작업 영역 이름을 해제하고 동일한 이름을 사용하여 새 작업 영역을 만들 수 있습니다.


> [!IMPORTANT]
> 영구 작업 영역 삭제 작업은 취소할 수 없고 작업 영역 및 해당 데이터를 복구할 수 없으므로 주의해서 사용해야 합니다.

작업 영역을 영구적으로 삭제 하려면 '-ForceDelete ' 태그를 추가 합니다. '-ForceDelete ' 옵션은 현재 Az. OperationalInsights 2.3.0 이상에서 사용할 수 있습니다. 

```powershell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name" -ForceDelete
```

## <a name="recover-workspace"></a>작업 영역 복구
Log Analytics 작업 영역을 실수로 또는 의도적으로 삭제하면 서비스에서 작업 영역에 액세스할 수 없도록 하는 일시 삭제 상태로 작업 영역을 배치합니다. 삭제된 작업 영역의 이름은 일시 삭제 기간 동안 보존되며 새 작업 영역을 만드는 데 사용할 수 없습니다. 일시 삭제 기간이 지나면 작업 영역은 복구할 수 없는 상태가 되고, 영구적으로 삭제되고 이름이 해제되며 새 작업 영역을 만드는 데 사용할 수 있습니다.

해당 데이터, 구성 및 연결된 에이전트를 포함하여 일시 삭제 기간 동안 작업 영역을 복구할 수 있습니다. 일시 삭제 작업 전에 작업 영역이 있는 리소스 그룹 및 구독에 대한 기여자 권한이 필요합니다. 작업 영역 복구는 다음을 포함하여 삭제된 작업 영역의 세부 정보를 사용하여 Log Analytics 작업 영역을 만드는 방식으로 수행됩니다.

- 구독 ID
- 리소스 그룹 이름
- 작업 영역 이름
- 지역

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics 작업 영역**을 선택합니다. 선택한 범위에 있는 작업 영역 목록이 표시됩니다.
3. 왼쪽 위 메뉴에서 **복구**를 클릭하여 복구할 수 있는 일시 삭제 상태의 작업 영역을 포함하는 페이지를 엽니다.

   ![메뉴 모음에서 복구가 강조 표시 된 Azure Portal Log Analytics 작업 영역 화면의 스크린샷](media/delete-workspace/recover-menu.png)

4. 작업 영역을 선택하고 **복구**를 클릭하여 해당 작업 영역을 복구합니다.

   ![작업 영역이 강조 표시 되 고 복구 단추가 선택 된 Azure Portal의 삭제 된 Log Analytics 작업 영역 복구 대화 상자 스크린샷](media/delete-workspace/recover-workspace.png)


### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

작업 영역 및 모든 해당 데이터는 복구 작업 후에 다시 가져옵니다. 솔루션 및 연결된 서비스는 삭제 시 작업 영역에서 영구적으로 제거되며 작업 영역을 이전에 구성된 상태로 가져오도록 다시 구성해야 합니다. 연결된 솔루션을 다시 설치하고 해당 스키마를 작업 영역에 추가할 때까지 작업 영역 복구 후에 일부 데이터를 쿼리에 사용할 수 없습니다.

> [!NOTE]
> * 일시 삭제 기간 동안 작업 영역을 다시 만들면 이 작업 영역 이름이 이미 사용 중임을 나타냅니다. 
 
## <a name="troubleshooting"></a>문제 해결

작업 영역을 삭제하려면 적어도 *Log Analytics 기여자* 권한이 있어야 합니다.

* 삭제 된 작업 영역이 일시 삭제 상태 이며 복구할 수 있는지 확실 하지 않은 경우 *Log Analytics 작업 영역* 에서 [복구](#recover-workspace) 를 클릭 하 여 구독 당 일시 삭제 된 작업 영역 목록을 표시 합니다. 영구적으로 삭제 된 작업 영역은 목록에 포함 되지 않습니다.
* 작업 영역을 만들 때 *이 작업 영역 이름은 이미 사용 중입니다* 또는 *충돌* 오류 메시지가 표시되는 원인은 다음과 같습니다.
  * 작업 영역 이름을 사용할 수 없으며 조직의 누군가 또는 다른 고객에 의해 사용되고 있습니다.
  * 최근 14일 이내에 작업 영역이 삭제되었으며 일시 삭제 기간 동안 해당 이름이 예약되어 있습니다. 일시 삭제를 재정의하고 작업 영역을 영구적으로 삭제하여 같은 이름으로 새 작업 영역을 만들려면 다음 단계를 수행하여 작업 영역을 먼저 복구한 후 영구 삭제를 수행합니다.<br>
     1. [작업 영역](#recover-workspace)을 복구합니다.
     2. 작업 영역을 [영구 삭제](#permanent-workspace-delete)합니다.
     3. 동일한 작업 영역 이름을 사용하여 새 작업 영역을 만듭니다.
