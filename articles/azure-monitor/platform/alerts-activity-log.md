---
title: 만들기, 보기 및 Azure Monitor의 활동 로그 경고를 관리 합니다.
description: Azure portal는 Azure Resource Manager 템플릿 및 Azure PowerShell을 사용 하 여 활동 로그 경고를 만듭니다.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: 71e61228fcdbd52abedbc1f1205baa60b1aea923
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705286"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>만들기, 보기 및 Azure Monitor를 사용 하 여 활동 로그 경고 관리  

## <a name="overview"></a>개요
활동 로그 경고는 경고에 지정된 조건과 일치하는 새 활동 로그 이벤트가 발생할 때 활성화되는 경고입니다.

이러한 경고 Azure 리소스 이며 Azure Resource Manager 템플릿을 사용 하 여 만들 수 있습니다. 또한 Azure Portal에서 생성, 업데이트 또는 삭제할 수 있습니다. 일반적으로 활동 로그 경고를 Azure 구독의 리소스에 특정 변경이 발생할 때 알림을 받을를 만듭니다. 경고는 특정 리소스 그룹 또는 리소스에 범위를 지정 하기도 합니다. 때 알림을 하려는 하는 예를 들어 샘플 리소스 그룹에서 virtual machine **myProductionResourceGroup** 삭제 됩니다. 또는 새 역할의 사용자 구독에 할당 된 경우 알림을 받도록 할 수 있습니다.

> [!IMPORTANT]
> 활동 로그 경고 만들기에 대 한 인터페이스를 통해 서비스 상태 알림 경고를 만들 수 없습니다. 만들기 및 서비스 상태 알림 사용 방법에 대 한 자세한 내용은 참조 하세요 [서비스 상태 알림에 대 한 활동 로그 경고 수신](alerts-activity-log-service-notifications.md)합니다.

경고 규칙을 만들 때 다음을 확인 합니다.

- 구독 범위에서 경고가 만들어진 구독에서 다른 아닙니다.
- 조건 수준, 상태, 호출자, 리소스 그룹, 리소스 ID 또는 경고가 구성 된 리소스 형식 이벤트 범주 여야 합니다.
- "AnyOf" 조건 또는 경고 구성 JSON에서에서 중첩 된 조건이 필요 하지 않습니다. 기본적으로 하나의 "allOf" 조건 추가 "allOf" 또는 "anyOf" 조건 없이 허용 됩니다.
- 범주가 "관리" 경우 경고에서 앞에 나온 조건 중 하나 이상 지정 해야 합니다. 활동 로그에서 이벤트가 생성될 때마다 활성화되는 경고를 만들 필요는 없습니다.


## <a name="azure-portal"></a>Azure Portal

만들기 및 활동 로그 경고 규칙을 수정 하려면 Azure portal을 사용할 수 있습니다. 환경은 원하는 특정 이벤트에 대 한 경고 원활 하 게 생성 되도록 Azure 활동 로그와 통합 됩니다.

### <a name="create-with-the-azure-portal"></a>Azure portal을 사용 하 여 만들기

다음 절차를 따르십시오.

1. Azure portal에서 선택 **모니터** > **경고**합니다.
2. 선택 **새 경고 규칙** 의 왼쪽 위 모서리에는 **경고** 창입니다.

     ![새 경고 규칙](media/alerts-activity-log/AlertsPreviewOption.png)

     **규칙 만들기** 창이 나타납니다.

      ![새 경고 규칙 옵션](media/alerts-activity-log/create-new-alert-rule-options.png)

3. 아래 **경고 조건 정의**, 다음 정보를 제공 하 고 선택 **수행**:

   - **경고 대상:** 사용 하 여 확인 하 고 새 경고에 대 한 대상을 선택 **구독 별로 필터링** / **리소스 종류별로 필터링**합니다. 표시 된 목록에서 리소스 또는 리소스 그룹을 선택 합니다.

     > [!NOTE]
     > 
     > 리소스, 리소스 그룹 또는 활동 로그 신호를 위한 전체 구독을 선택할 수 있습니다.

     **경고 대상 샘플 보기**

     ![대상 선택](media/alerts-activity-log/select-target.png)

   - 아래 **대상 조건**를 선택 **조건 추가**합니다. 다양 한 범주에서 해당 항목을 포함 하는 대상에 대 한 모든 사용 가능한 신호의 표시 됩니다 **활동 로그**합니다. 범주 이름에 추가 되는 **모니터 서비스** 이름입니다.

   - **활동 로그** 유형에 대해 가능한 다양한 작업에 대해 표시된 목록에서 신호를 선택합니다.

     이 대상 신호에 대한 로그 기록 타임라인 및 해당 경고 논리를 선택할 수 있습니다.

     **조건 추가 화면**

     ![조건 추가](media/alerts-activity-log/add-criteria.png)

     - **기록 시간**: 마지막 6, 12, 또는 24 시간 또는 지난 주 동안 선택한 작업에 사용할 수 있는 이벤트를 그릴 수 있습니다.

     - **경고 논리**:

       - **이벤트 수준**: 이벤트의 심각도 수준: 자세한 정보, _정보_, _경고_, _오류_ 또는 _위험_.
       - **상태**: 상태 이벤트입니다. 시작, _실패_ 또는 _성공_.
       - **이벤트를 시작한 사람**: 호출자가 라고도 합니다. 작업을 수행한 사용자의 이메일 주소 또는 Active Directory 식별자입니다.

       이 샘플 신호 그래프에 적용 하 여 경고 논리에 있습니다.

       ![조건 선택](media/alerts-activity-log/criteria-selected.png)

4. 아래 **경고 세부 정보 정의**, 다음 세부 정보를 제공 합니다.

    - **경고 규칙 이름**: 새 경고 규칙의 이름입니다.
    - **설명**: 새 경고 규칙에 대 한 설명입니다.
    - **리소스 그룹에 경고 저장**: 이 새 규칙을 저장 하려는 리소스 그룹을 선택 합니다.

5. **작업 그룹** 아래의 드롭다운 메뉴에서 이 새 경고 규칙에 할당할 작업 그룹을 지정합니다. 또는 [새 작업 그룹을 만들](../../azure-monitor/platform/action-groups.md) 새 규칙에 할당 합니다. 새 그룹을 만들려면 **+ 새 그룹**합니다.

6. 파일을 만든 후 규칙을 사용 하려면 **예** 에 대 한 합니다 **활성화 규칙을 만들면** 옵션입니다.
7. 선택 **경고 규칙 만들기**합니다.

    활동 로그에 대 한 새 경고 규칙 만들어지고 창의 오른쪽 위 모서리에서 확인 메시지가 표시 됩니다.

    규칙을 사용/사용하지 않도록 설정, 편집 또는 삭제할 수 있습니다. 활동 로그 규칙을 관리 하는 방법을 자세히 알아봅니다.


에 대 한 활동 로그 경고 규칙을 만들 수는 이해 조건에 대 한 간단한 비유를 탐색 하거나 통해 이벤트를 필터링 하는 것은 [Azure portal에서 활동 로그](activity-log-view.md#azure-portal)합니다. 에 **Azure Monitor-활동 로그** 화면, 필터링 또는 필요한 이벤트를 찾아보세요를 사용 하 여 경고를 만든 합니다 **활동 로그 경고 추가** 단추입니다. 그런 다음 표시 되는 4 ~ 7 이전 처럼 단계를 따릅니다.
    
 ![활동 로그에서 경고 추가](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>보기 및 Azure portal에서 관리

1. Azure portal에서 선택 **모니터** > **경고**합니다. 선택 **경고 규칙 관리** 창의 왼쪽 위 모퉁이에서.

    ![경고 규칙 관리](media/alerts-activity-log/manage-alert-rules.png)

    사용 가능한 규칙 목록이 나타납니다.

2. 수정할 활동 로그 규칙을 검색 합니다.

    ![활동 로그 경고 규칙 검색](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    사용 가능한 필터를 사용할 수 있습니다 _구독_를 _리소스 그룹_를 _리소스_를 _신호 형식_, 또는 _상태_ 를 편집할 활동 규칙을 찾습니다.

   > [!NOTE]
   > 
   > 만 편집할 수 있습니다 **설명을**를 **대상 조건**, 및 **작업 그룹**합니다.

3. 규칙을 선택 하 고 규칙 옵션을 편집 하려면 두 번 클릭 합니다. 필요한 변경 작업을 선택한 후 **저장할**합니다.

   ![경고 규칙 관리](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. 설정 지정, 해제 또는 규칙을 삭제 합니다. 2 단계에 설명 된 대로 규칙을 선택한 후 창의 맨 위에 있는 적절 한 옵션을 선택 합니다.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿
Azure Resource Manager 템플릿을 사용 하 여 활동 로그 경고를 만들려는 형식의 리소스를 만들 `microsoft.insights/activityLogAlerts`합니다. 그런 다음 모든 관련된 속성을 입력합니다. 활동 로그 경고를 만드는 템플릿을 다음과 같습니다.

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
이전 샘플 JSON이이 연습을 위해 sampleActivityLogAlert.json 예를 들어, 다른 이름으로 저장할 수 있습니다 사용 하 여 배포할 수 있습니다 [Azure portal에서 Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy-portal.md)합니다.

> [!NOTE]
> 활성화 하려면 새 활동 로그 경고 규칙에 대 한 최대 5 분까지 걸릴 수 있습니다.

## <a name="rest-api"></a>REST API 
합니다 [Azure Monitor 활동 로그 경고 API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) 는 REST API입니다. Azure Resource Manager REST API를 사용 하 여 완벽 하 게 호환 됩니다. Resource Manager cmdlet 또는 Azure CLI를 사용 하 여 PowerShell을 통해 사용할 수 있습니다.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>PowerShell 사용 하 여 Resource Manager 템플릿 배포
PowerShell 사용 하 여 이전에 표시 된 샘플 Resource Manager 템플릿을 배포 하려면 [Azure Resource Manager 템플릿을](#azure-resource-manager-template) 섹션에서 다음 명령을 사용 합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

여기서는 sampleActivityLogAlert.parameters.json 경고 규칙을 만드는 데 필요한 매개 변수에 대해 제공 된 값을 포함 합니다.

### <a name="use-activity-log-powershell-cmdlets"></a>활동 로그 PowerShell cmdlet 사용

활동 로그 경고에서는 다음과 같은 전용 PowerShell cmdlet을 사용할 수 있습니다.

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): 새 활동 로그 경고를 만들거나 기존 활동 로그 경고를 업데이트 합니다.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): 하나 이상의 활동 로그 경고 리소스를 가져옵니다.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): 기존 활동 로그 경고를 사용 하도록 설정 하 고 해당 태그를 설정 합니다.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): 기존 활동 로그 경고를 사용 하지 않도록 설정 하 고 해당 태그를 설정 합니다.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): 활동 로그 경고를 제거합니다.

## <a name="azure-cli"></a>Azure CLI

설정된 [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert)의 전용 Azure CLI 명령을 활동 로그 경고 규칙을 관리하는 데 사용할 수 있습니다.

새 활동 로그 경고 규칙을 만들려면 다음 명령을이 순서 대로 사용 합니다.

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): 새 활동 로그 경고 규칙 리소스를 만듭니다.
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): 생성된 된 활동 로그 경고 규칙에 대 한 범위를 추가 합니다.
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): 활동 로그 경고 규칙에 작업 그룹을 추가 합니다.

활동 로그 경고 규칙 리소스를 검색 하려면 Azure CLI 명령을 사용 하 여 [az monitor 활동 로그 경고 표시](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)합니다. 리소스 그룹의 모든 활동 로그 경고 규칙 리소스를 보려면 사용 하 여 [az monitor 활동 로그 경고 목록](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)합니다.
Azure CLI 명령을 사용 하 여 활동 로그 경고 규칙 리소스를 제거할 수 있습니다 [az monitor 활동 로그 경고 삭제](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)합니다.

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 [활동 로그에 대 한 웹 후크 스키마](../../azure-monitor/platform/activity-log-alerts-webhook.md)합니다.
- 읽기를 [활동 로그 개요](../../azure-monitor/platform/activity-log-alerts.md)합니다.
- [작업 그룹](../../azure-monitor/platform/action-groups.md)에 대해 자세히 알아보세요.  
- [서비스 상태 알림](../../azure-monitor/platform/service-notifications.md)에 대해 자세히 알아보세요.
