---
title: 만들고, Azure Monitor의 로그 경고 보기 및 관리 작업
description: Azure portal는 Azure Resource Manager 템플릿 및 Azure PowerShell을 사용 하 여 활동 로그 경고를 만드는 방법입니다.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.openlocfilehash: f25321fa5a13ed5a39a62a4115bb0bc10306d36f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244949"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리하기  

## <a name="overview"></a>개요
활동 로그 경고는 경고에 지정된 조건과 일치하는 새 활동 로그 이벤트가 발생할 때 활성화되는 경고입니다.

이 경고는 Azure 리소스에 대한 것이며, Azure Resource Manager 템플릿을 사용하여 만들 수 있습니다. 또한 Azure Portal에서 생성, 업데이트 또는 삭제할 수 있습니다. 일반적으로 Azure 구독의 리소스에서 특정 변경이 발생할 때 알림을 받기 위해 활동 로그 경고를 만들며, 특정 리소스 그룹 또는 리소스로 범위를 지정하는 경우가 많습니다. 예를 들어 (샘플 리소스 그룹) **myProductionResourceGroup**의 가상 머신이 삭제될 때 알림을 받거나, 구독의 사용자에게 새 역할이 할당될 경우 알림을 받고 싶을 수 있습니다.

> [!IMPORTANT]
> Service Health 알림에 대한 경고는 활동 로그 경고 작성을 위한 인터페이스를 통해 만들 수 없습니다. 서비스 상태 알림 생성 및 사용에 대해 자세히 알아보려면 [서비스 상태 알림에서 활동 로그 경고 수신](alerts-activity-log-service-notifications.md)을 참조하세요.

## <a name="azure-portal"></a>Azure portal

> [!NOTE]
> 
>  경고 규칙을 만드는 동안 다음을 확인합니다.
> 
> - 범위의 구독이 경고가 생성된 구독과 다르지 않습니다.
> - 조건은 경고가 구성된 수준/상태/호출자/리소스 그룹/리소스 ID/리소스 유형/이벤트 범주여야 합니다.
> - 경고 구성 JSON에 “anyOf” 조건 또는 중첩된 조건이 없습니다(기본적으로 추가 allOf/anyOf 없이 하나의 allOf만 허용됨).
> - 범주가 "관리"인 경우. 경고에 앞에 나온 조건 중 하나 이상을 지정해야 합니다. 활동 로그에서 이벤트가 생성될 때마다 활성화되는 경고를 만들 필요는 없습니다.

### <a name="create-with-azure-portal"></a>Azure Portal을 사용하여 만들기

이렇게 하려면 다음 절차를 수행합니다.

1. Azure Portal에서 **모니터** > **경고**를 선택합니다.
2. **경고** 창의 맨 위에서 **새 경고 규칙**을 클릭합니다.

     ![새 경고 규칙](media/alerts-activity-log/AlertsPreviewOption.png)

     **규칙 만들기** 창이 나타납니다.

      ![새 경고 규칙 옵션](media/alerts-activity-log/create-new-alert-rule-options.png)

3. **경고 조건 정의**에서 다음 정보를 제공하고 **완료**를 클릭합니다.

   - **경고 대상:** 새 경고의 대상을 보고 선택하려면 **구독별로 필터링** / **리소스 종류별로 필터링**을 사용하고 표시되는 목록에서 리소스 또는 리소스 그룹을 선택합니다.

     > [!NOTE]
     > 
     > 활동 로그 신호에 대한 리소스, 리소스 그룹 또는 전체 구독을 선택할 수 있습니다.

     **경고 대상 샘플 보기**
     ![대상 선택](media/alerts-activity-log/select-target.png)

   - 아래 **대상 조건**, 클릭 **조건 추가** 대상에 대 한 모든 사용 가능한 신호의의 다양 한 범주를 포함 하 여 표시 됩니다 **활동 로그**; 범주 이름 추가 **Monitor 서비스** 이름입니다.

   - 형식에 대 한 가능한 다양 한 작업의 표시 된 목록에서 신호를 선택 **활동 로그**합니다.

     이 대상 신호에 대한 로그 기록 타임라인 및 해당 경고 논리를 선택할 수 있습니다.

     **조건 추가 화면**

     ![조건 추가](media/alerts-activity-log/add-criteria.png)

     **기록 시간**: 선택한 작업에 사용할 수 있는 이벤트는 지난 6/12/24시간 동안 (또는) 지난 주 동안 표시될 수 있습니다.

     **경고 논리**:

     - **이벤트 수준**- 이벤트의 심각도 수준입니다. _자세한 정보_, _정보_, _경고_, _오류_ 또는 _위험_.
     - **상태**: 이벤트의 상태입니다. _시작_, _실패_ 또는 _성공_.
     - **이벤트를 시작한 사람**: 호출자라고도 합니다. 작업을 수행한 사용자의 메일 주소 또는 Azure Active Directory 식별자입니다.

       경고 논리가 적용된 샘플 신호 그래프:

       ![ 선택한 기준](media/alerts-activity-log/criteria-selected.png)

4. **경고 규칙 세부 정보 정의**에서 다음 세부 정보를 제공합니다.

    - **경고 규칙 이름** - 새 경고 규칙의 이름
    - **설명** - 새 경고 규칙에 대한 설명
    - **리소스 그룹에 경고 저장** - 이 새 규칙을 저장할 리소스 그룹을 선택합니다.

5. **작업 그룹** 아래의 드롭다운 메뉴에서 이 새 경고 규칙에 할당할 작업 그룹을 지정합니다. 또는 [새 작업 그룹을 만들어](../../azure-monitor/platform/action-groups.md) 새 규칙에 할당합니다. 새 그룹을 만들려면 **+ 새 그룹**을 클릭합니다.

6. 규칙을 만든 후 사용하도록 설정하려면 **규칙을 만들면 바로 사용**에 대해 **예**를 클릭합니다.
7. **경고 규칙 만들기**를 클릭합니다.

    활동 로그에 대한 새 경고 규칙이 생성되고 창의 오른쪽 위에 확인 메시지가 나타납니다.

    규칙을 사용/사용하지 않도록 설정, 편집 또는 삭제할 수 있습니다. 활동 로그 규칙 관리에 대해 자세히 알아보세요.


또는 활동 로그 경고 규칙을 만들 수는 이해 조건에 대 한 간단한 비유를 탐색 하거나 이벤트를 통해 필터링 [Azure portal에서 활동 로그](activity-log-view.md#azure-portal)합니다. Azure Monitor-활동 로그의에서 하나 수 필터링 필요한 이벤트 찾기 및 사용 하 여 경고를 만든 합니다 **활동 로그 경고 추가** 단추; 다음 4 단계부터 위의 자습서에 명시 된 대로 합니다.
    
 ![ 활동 로그에서 경고 추가](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>Azure Portal에서 보기 및 관리

1. Azure Portal에서 **모니터** > **경고**를 클릭하고 창의 왼쪽 위에서 **규칙 관리**를 클릭합니다.

    ![ 경고 규칙 관리](media/alerts-activity-log/manage-alert-rules.png)

    사용 가능한 규칙 목록이 나타납니다.

2. 수정할 활동 로그 규칙을 검색합니다.

    ![ 활동 로그 경고 규칙 검색](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    사용 가능한 필터(_구독_, _리소스 그룹_, _리소스_, _신호 유형_ 또는 _상태_)를 사용하여 편집할 활동 규칙을 찾을 수 있습니다.

   > [!NOTE]
   > 
   > **설명** , **대상 조건** 및 **작업 그룹**만 편집할 수 있습니다.

3. 규칙을 선택하고 두 번 클릭하여 규칙 옵션을 편집합니다. 필요에 따라 변경하고 **저장**을 클릭합니다.

   ![ 경고 규칙 관리](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. 규칙을 사용/사용하지 않도록 설정하거나 삭제할 수 있습니다. 2단계에서 설명한 대로 규칙을 선택한 후 창의 맨 위에서 적절한 옵션을 선택합니다.


## <a name="azure-resource-template"></a>Azure 리소스 템플릿
리소스 관리자 템플릿을 사용하여 활동 로그 경고를 만들려면 `microsoft.insights/activityLogAlerts` 유형의 리소스를 만듭니다. 그런 다음 모든 관련된 속성을 입력합니다. 다음은 활동 로그 경고를 만드는 템플릿입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
위의 샘플 json은 이 연습의 목적을 위해 sampleActivityLogAlert.json(예)으로 저장될 수 있으며 [Azure Portal에서 Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy-portal.md)를 사용하여 배포될 수 있습니다.

> [!NOTE]
> 새로운 활동 로그 경고 규칙이 활성화되는 데에는 최대 5분이 걸릴 수 있습니다.

## <a name="rest-api"></a>REST API 
[Azure Monitor-활동 로그 경고 API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) REST API 및 Azure Resource Manager REST API와 완전히 호환 됩니다. 따라서 Resource Manager cmdlet 뿐 아니라 Azure CLI를 사용하여 Powershell을 통해 사용할 수 있습니다.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-resource-manager-template-with-powershell"></a>PowerShell 사용 하 여 Resource Manager 템플릿 배포
리소스 템플릿 이전 [리소스 템플릿 섹션]에 표시 된 예제를 배포 하려면 PowerShell을 사용 하려면 (#resource manager-템플릿, 다음 명령을 사용 하 여:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

여기서는 sampleActivityLogAlert.parameters.json 경고 규칙을 만드는 데 필요한 매개 변수에 대해 제공 된 값을 포함 합니다.

### <a name="use-activity-log-powershell-cmdlets"></a>활동 로그 PowerShell cmdlet 사용

활동 로그 경고에서는 다음과 같은 전용 PowerShell cmdlet을 사용할 수 있습니다.

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert?view=azps-1.3.0) : 새 또는 기존 활동 로그 경고를 업데이트 합니다.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert?view=azps-1.3.0) : 하나 이상의 활동 로그 경고 리소스를 가져옵니다.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert?view=azps-1.3.0) : 기존 활동 로그 경고를 사용 하도록 설정 하 고 해당 태그를 설정 합니다.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert?view=azps-1.3.0) : 기존 활동 로그 경고를 사용 하지 않도록 설정 하 고 해당 태그를 설정 합니다.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert?view=azps-1.3.0)    : 활동 로그 경고를 제거합니다.

## <a name="cli"></a>CLI

설정된 [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert)의 전용 Azure CLI 명령을 활동 로그 경고 규칙을 관리하는 데 사용할 수 있습니다.

새로운 활동 로그 경고 규칙을 만들려면 다음 중 하나를 사용합니다.

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): 새로운 활동 로그 경고 규칙 리소스 만들기
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): 생성된 활동 로그 경고 규칙에 대한 범위 추가
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): 활동 로그 경고 규칙에 작업 그룹 추가

하나의 활동 로그 경고 규칙 리소스를 검색하려면 Azure CLI 명령 [az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)를 사용할 수 있습니다. 또한 리소스 그룹의 모든 활동 로그 경고 규칙 리소스를 보려면 [az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)를 사용합니다.
Azure CLI 명령 [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)를 사용하여 활동 로그 경고 규칙 리소스를 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [활동 로그에 대한 웹후크 스키마](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- [활동 로그 개요](../../azure-monitor/platform/activity-log-alerts.md) 
- [작업 그룹](../../azure-monitor/platform/action-groups.md)에 대해 자세히 알아보세요.  
- [서비스 상태 알림](../../azure-monitor/platform/service-notifications.md)에 대해 자세히 알아보세요.
