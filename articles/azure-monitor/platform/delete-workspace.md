---
title: Azure 로그 분석 작업 영역 삭제 및 복구 | 마이크로 소프트 문서
description: 개인 구독에서 작업 영역을 만들었거나 작업 영역 모델을 재구성한 경우 Log Analytics 작업 영역을 삭제하는 방법을 알아봅니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: 1dceb3db4572ecdaf504745dba1099a5eccead43
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80395778"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Azure 로그 분석 작업 영역 삭제 및 복구

이 문서에서는 Azure Log Analytics 작업 영역 소프트 삭제의 개념과 삭제된 작업 영역을 복구하는 방법에 대해 설명합니다. 

## <a name="considerations-when-deleting-a-workspace"></a>작업 영역을 삭제할 때 고려해야 할 사항

Log Analytics 작업 영역을 삭제하면 삭제가 우발적이든 의도적이든 14일 이내에 데이터 및 연결된 에이전트를 포함한 작업 영역을 복구할 수 있도록 소프트 삭제 작업이 수행됩니다. 소프트 삭제 기간 이후에는 작업 영역 리소스와 해당 데이터를 복구할 수 없습니다. 작업 영역 이름은 '해제'이며 새 작업 영역을 만드는 데 사용할 수 있습니다.

> [!NOTE]
> 소프트 삭제 동작을 재정의하고 작업 영역을 영구적으로 삭제하려면 [영구 작업 영역 삭제](#permanent-workspace-delete)단계 수행을 수행합니다.

서비스 작업에 부정적인 영향을 미칠 수 있는 중요한 데이터 및 구성이 있을 수 있으므로 작업 영역을 삭제할 때 주의해야 합니다. 로그 애널리틱스에 데이터를 저장하는 에이전트, 솔루션 및 기타 Azure 서비스 및 소스를 검토합니다.

* 관리 솔루션
* Azure Automation
* Windows 및 Linux 가상 머신에서 실행되는 에이전트
* 사용자 환경의 Windows 및 Linux 컴퓨터에서 실행되는 에이전트
* System Center Operations Manager

소프트 삭제 작업은 작업 영역 리소스를 삭제하고 관련 사용자의 권한이 손상되었습니다. 사용자가 다른 작업 영역과 연결되어 있는 경우 다른 작업 영역과 함께 Log Analytics를 계속 사용할 수 있습니다.

## <a name="soft-delete-behavior"></a>일시 삭제 동작

작업 영역 삭제 작업은 작업 영역 리소스 관리자 리소스를 제거하지만 해당 구성 및 데이터는 작업 영역이 삭제된 모양을 유지하면서 14일 동안 유지됩니다. 작업 영역에 보고하도록 구성된 모든 에이전트 및 시스템 센터 운영 관리자 관리 그룹은 소프트 삭제 기간 동안 분리된 상태로 유지됩니다. 이 서비스는 데이터 및 연결된 리소스를 포함하여 삭제된 작업 영역을 복구하고 기본적으로 삭제를 취소하는 메커니즘을 제공합니다.

> [!NOTE] 
> Azure Automation 계정과 같은 설치된 솔루션 및 연결된 서비스는 삭제 시간에 작업 영역에서 영구적으로 제거되며 복구할 수 없습니다. 작업 영역을 이전에 구성된 상태로 가져오려면 복구 작업 후에 이러한 작업을 다시 구성해야 합니다.

[PowerShell,](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0) [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)또는 [Azure 포털에서](https://portal.azure.com)작업 영역을 삭제할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. 로그인하려면 [Azure 포털로](https://portal.azure.com)이동하십시오. 
2. Azure 포털에서 **모든 서비스를**선택합니다. 리소스 목록에서 **로그 분석을**입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics 작업 영역**을 선택합니다.
3. 로그 분석 작업 영역 목록에서 작업 영역을 선택한 다음 가운데 창 상단에서 **삭제를** 클릭합니다.
   ![작업 영역 속성 창에서 옵션 삭제](media/delete-workspace/log-analytics-delete-workspace.png)
4. 작업 영역 삭제를 확인할지 묻는 확인 메시지 창이 나타나면 **예**를 클릭합니다.
   ![작업 영역 삭제 확인](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

### <a name="troubleshooting"></a>문제 해결

로그 분석 작업 영역을 삭제하려면 '로그 분석 기여자' 권한이 있어야 합니다.<br>
작업 영역을 만들 때 *'이 작업 영역 이름이 이미 사용 중이라는*오류 메시지가 나타납니다.
* 작업 영역 이름을 사용할 수 없으며 조직의 다른 사용자 또는 다른 고객이 사용하고 있습니다.
* 작업 영역은 지난 14일 동안 삭제되었으며 해당 이름은 소프트 삭제 기간 동안 예약된 것으로 유지됩니다. 소프트 삭제를 재정의하고 작업 영역을 즉시 삭제하고 동일한 이름으로 새 작업 영역을 만들려면 다음 단계를 수행하여 먼저 작업 영역을 복구하고 영구 삭제를 수행합니다.<br>
   1. 작업 영역을 [복구합니다.](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace)
   2. 작업 영역을 [영구적으로 삭제합니다.](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete)
   3. 동일한 작업 영역 이름을 사용하여 새 작업 영역을 만듭니다.


## <a name="permanent-workspace-delete"></a>영구 작업 영역 삭제
소프트 삭제 방법은 동일한 설정 및 작업 영역 이름으로 배포를 반복해야 하는 개발 및 테스트와 같은 일부 시나리오에 적합하지 않을 수 있습니다. 이러한 경우 작업 영역을 영구적으로 삭제하고 소프트 삭제 기간을 "재정의"할 수 있습니다. 영구 작업 영역 삭제 작업은 작업 영역 이름을 해제하고 동일한 이름을 사용하여 새 작업 영역을 만들 수 있습니다.


> [!IMPORTANT]
> 영구 작업 영역 삭제 작업을 사용 하 여 되돌릴 수 없기 때문에 주의 하 고 작업 영역 및 해당 데이터를 복구할 수 없습니다.

영구 작업 영역 삭제는 현재 REST API를 통해 수행할 수 있습니다.

> [!NOTE]
> 모든 API 요청은 요청 헤더에 Bearer 권한 부여 토큰을 포함해야 합니다.
>
> 다음을 사용하여 토큰을 획득할 수 있습니다.
> - [앱 등록](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - 브라우저에서 개발자 콘솔(F12)을 사용하여 Azure 포털로 이동합니다. **요청 헤더**에서 인증 문자열에 대 한 **일괄 처리** 중 하나를 찾습니다. 이것은 패턴 권한 *부여에 있을 <token>것입니다: bearer *. 예제와 같이 API 호출에 복사하여 추가합니다.
> - Azure REST 설명서 사이트로 이동합니다. 누릅니다 모든 API에서 **그것을 시도,** 베어러 토큰을 복사, 당신의 API 호출에 추가.
작업 영역을 영구적으로 삭제하려면 작업 영역 - 강제 태그가 있는 REST API 호출 [삭제를]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) 사용합니다.
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
어디 'eyJ0eXAiOiJKV1Qi...' 은 전체 권한 부여 토큰을 나타냅니다.

## <a name="recover-workspace"></a>작업 영역 복구

소프트 삭제 작업 전에 작업 영역이 연결된 구독 및 리소스 그룹에 대한 기여자 권한이 있는 경우 데이터, 구성 및 연결된 에이전트를 포함하여 소프트 삭제 기간 동안 복구할 수 있습니다. 소프트 삭제 기간 이후에는 작업 영역을 복구할 수 없으며 영구 삭제에 할당됩니다. 삭제된 작업 영역의 이름은 일시 삭제 기간 동안 보존되며 새 작업 영역을 만들려고 할 때 사용할 수 없습니다.  

다음 작업 영역 만들기 메서드: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) 또는 [REST API를]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) 사용 하 여 다음 속성이 삭제 된 작업 영역 세부 정보로 채워집니다 를 사용 하 여 작업 영역을 다시 만들 수 있습니다.

* 구독 ID
* 리소스 그룹 이름
* 작업 영역 이름
* 지역

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

작업 영역과 모든 데이터는 복구 작업 후에 다시 가져온다. 솔루션 및 연결된 서비스는 삭제될 때 작업 영역에서 영구적으로 제거되었으며 작업 영역을 이전에 구성된 상태로 가져오도록 다시 구성해야 합니다. 연결된 솔루션이 다시 설치되고 해당 스키마가 작업 영역에 추가될 때까지 작업 영역 복구 후 일부 데이터를 쿼리에 사용하지 못할 수 있습니다.

> [!NOTE]
> * 작업 영역 복구는 Azure [포털에서](https://portal.azure.com)지원되지 않습니다. 
> * 소프트 삭제 기간 동안 작업 영역을 다시 만들면 이 작업 영역 이름이 이미 사용 중임을 알 수 있습니다. 
> 
