---
title: Azure Log Analytics 작업 영역 삭제 및 복구 | Microsoft Docs
description: 개인 구독에서 작업 영역을 만들었거나 작업 영역 모델을 재구성한 경우 Log Analytics 작업 영역을 삭제하는 방법을 알아봅니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: 739f97e912a33402aa7482e59dd78f5aeb005772
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75944437"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Azure Log Analytics 작업 영역 삭제 및 복원

이 문서에서는 Azure Log Analytics 작업 영역 일시 삭제의 개념과 삭제 된 작업 영역을 복구 하는 방법을 설명 합니다. 

## <a name="considerations-when-deleting-a-workspace"></a>작업 영역을 삭제할 때의 고려 사항

Log Analytics 작업 영역을 삭제 하면 해당 데이터 및 연결 된 에이전트를 포함 하는 작업 영역을 14 일 내에 복구 하도록 허용 하기 위해 일시 삭제 작업이 수행 됩니다. 일시 삭제 기간이 지나면 작업 영역 리소스와 해당 데이터는 복구할 수 없습니다. 해당 데이터가 영구적으로 삭제 되 고 30 일 이내에 완전히 제거 될 때까지 대기 됩니다. 작업 영역 이름은 ' 릴리스 ' 이며 새 작업 영역을 만드는 데 사용할 수 있습니다.

> [!NOTE]
> 일시 삭제 동작은 해제할 수 없습니다. 삭제 작업에서 ' force ' 태그를 사용할 때 일시 삭제를 재정의 하는 옵션을 곧 추가할 예정입니다.

작업 영역을 삭제할 때 서비스 작업에 부정적인 영향을 줄 수 있는 중요 한 데이터 및 구성이 있을 수 있으므로 주의를 기울여야 합니다. 다음과 같이 Log Analytics에 데이터를 저장 하는 에이전트, 솔루션 및 기타 Azure 서비스와 소스를 검토 합니다.

* 관리 솔루션
* Azure Automation
* Windows 및 Linux 가상 머신에서 실행되는 에이전트
* 사용자 환경의 Windows 및 Linux 컴퓨터에서 실행되는 에이전트
* System Center Operations Manager

일시 삭제 작업은 작업 영역 리소스를 삭제 하 고 연결 된 사용자의 사용 권한은 중단 됩니다. 사용자가 다른 작업 영역과 연결 되어 있으면 다른 작업 영역과 함께 Log Analytics를 계속 사용할 수 있습니다.

## <a name="soft-delete-behavior"></a>일시 삭제 동작

작업 영역 삭제 작업은 작업 영역 리소스 관리자 리소스를 제거 하지만 해당 구성과 데이터는 14 일 동안 유지 되지만 작업 영역을 삭제 하는 모양을 제공 합니다. 작업 영역에 보고 하도록 구성 된 모든 에이전트와 System Center Operations Manager 관리 그룹은 일시 삭제 기간 동안 분리 된 상태로 유지 됩니다. 이 서비스는 데이터 및 연결 된 리소스를 포함 하 여 삭제 된 작업 영역을 복구 하는 메커니즘을 제공 하며, 기본적으로 삭제를 취소 합니다.

> [!NOTE] 
> 설치 된 솔루션 및 Azure Automation 계정과 같은 연결 된 서비스는 삭제 시 작업 영역에서 영구적으로 제거 되며 복구할 수 없습니다. 이전에 구성 된 상태로 작업 영역을 가져오기 위해 복구 작업 후 다시 구성 해야 합니다.

[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)또는 [Azure Portal](https://portal.azure.com)를 사용 하 여 작업 영역을 삭제할 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

1. 로그인 하려면 [Azure Portal](https://portal.azure.com)으로 이동 합니다. 
2. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics 작업 영역**을 선택합니다.
3. Log Analytics 작업 영역 목록에서 작업 영역을 선택한 다음 가운데 창의 위쪽에서 **삭제** 를 클릭 합니다.
   ![작업 영역 속성 창에서 옵션 삭제](media/delete-workspace/log-analytics-delete-workspace.png)
4. 작업 영역 삭제를 확인할지 묻는 확인 메시지 창이 나타나면 **예**를 클릭합니다.
   ![작업 영역 삭제 확인](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="recover-workspace"></a>작업 영역 복구

일시 삭제 작업 이전에 작업 영역이 연결 된 구독 및 리소스 그룹에 대 한 참가자 권한이 있는 경우 해당 데이터, 구성 및 연결 된 에이전트를 포함 하 여 일시 삭제 기간 동안 복구할 수 있습니다. 일시 삭제 기간이 지나면 작업 영역은 복구 불가능 하 고 영구 삭제를 위해 할당 됩니다. 삭제 된 작업 영역의 이름은 일시 삭제 기간 동안 보존 되며 새 작업 영역을 만들려고 할 때 사용할 수 없습니다.  

다음 속성이 삭제 된 작업 영역 정보로 채워지는 경우 [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) 또는 [REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) 작업 영역을 사용 하 여 작업 영역을 다시 만들면 작업 영역을 복구할 수 있습니다.

* 구독 ID
* 리소스 그룹 이름
* 작업 영역 이름
* 지역

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

작업 영역 및 모든 해당 데이터는 복구 작업 후에 다시 가져옵니다. 솔루션 및 연결 된 서비스는 삭제 시 작업 영역에서 영구적으로 제거 되며 작업 영역을 이전에 구성 된 상태로 전환 하도록 다시 구성 해야 합니다. 연결 된 솔루션을 다시 설치 하 고 해당 스키마를 작업 영역에 추가할 때까지 작업 영역 복구 후에 일부 데이터를 쿼리에 사용할 수 없습니다.

> [!NOTE]
> * 작업 영역 복구는 [Azure Portal](https://portal.azure.com)에서 지원 되지 않습니다. 
> * 일시 삭제 기간 동안 작업 영역을 다시 만들면이 작업 영역 이름이 이미 사용 중임을 나타냅니다. 
> 
