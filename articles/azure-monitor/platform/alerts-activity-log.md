---
title: Azure Monitor에서 활동 로그 경고 만들기, 보기 및 관리
description: Azure Portal, Azure Resource Manager 템플릿 및 Azure PowerShell을 사용하여 활동 로그 경고를 만듭니다.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: 45345d06e64194224df48a33fab1e74433a1eaac
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744257"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리하기  

## <a name="overview"></a>개요

활동 로그 경고는 경고에 지정된 조건과 일치하는 새 활동 로그 이벤트가 발생할 때 활성화되는 경고입니다.

이 경고는 Azure 리소스에 대한 것이며, Azure Resource Manager 템플릿을 사용하여 만들 수 있습니다. 또한 Azure Portal에서 생성, 업데이트 또는 삭제할 수 있습니다. 일반적으로 Azure 구독의 리소스에서 특정 변경이 발생할 때 알림을 받기 위해 활동 로그 경고를 만듭니다. 특정 리소스 그룹 또는 리소스로 범위를 지정하는 경우가 많습니다. 예를 들어 샘플 리소스 그룹 **myProductionResourceGroup**에서 가상 머신이 삭제되는 경우 알림을 받도록 할 수 있습니다. 또는 새 규칙이 사용자 구독의 사용자에 할당되는 경우 알림을 받도록 할 수도 있습니다.

> [!IMPORTANT]
> 서비스 상태 알림에 대한 경고는 활동 로그 경고 작성을 위한 인터페이스를 통해 만들 수 없습니다. 서비스 상태 알림 생성 및 사용에 대해 자세히 알아보려면 [서비스 상태 알림에서 활동 로그 경고 수신](alerts-activity-log-service-notifications.md)을 참조하세요.

경고 규칙을 만들 때 다음을 확인합니다.

- 범위의 구독이 경고가 생성된 구독과 다르지 않습니다.
- 조건은 경고가 구성된 수준/상태/호출자/리소스 그룹/리소스 ID/리소스 종류/이벤트 범주여야 합니다.
- 경고 구성 JSON에는 "anyOf" 조건 또는 중첩 조건이 없습니다. 기본적으로 "allOf" 또는 "anyOf" 조건이 추가로 포함되지 않은 하나의 "allOf" 조건만 허용됩니다.
- 범주가 "관리"인 경우, 앞에 나온 조건 중 하나 이상을 경고에 지정해야 합니다. 활동 로그에서 이벤트가 생성될 때마다 활성화되는 경고를 만들 필요는 없습니다.

## <a name="azure-portal"></a>Azure portal

Azure Portal를 사용하여 활동 로그 경고 규칙을 만들고 수정할 수 있습니다. 이 환경은 관심이 있는 특정 이벤트에 대한 원활한 경고 생성을 보장하기 위해 Azure 활동 로그와 통합됩니다.

### <a name="create-with-the-azure-portal"></a>Azure Portal을 사용하여 만들기

다음 절차를 사용합니다.

1. Azure Portal에서 **모니터** > **경고**를 선택합니다.
2. 왼쪽 상단 **경고** 창에서 **새 경고 규칙**을 선택합니다.

     ![새 경고 규칙](media/alerts-activity-log/AlertsPreviewOption.png)

     **규칙 만들기** 창이 나타납니다.

      ![새 경고 규칙 옵션](media/alerts-activity-log/create-new-alert-rule-options.png)

3. **경고 조건 정의**에서 다음 정보를 입력하고 **완료**를 선택합니다.

   - **경고 대상:** 새 경고에 대한 대상을 확인하고 선택하려면 **구독별로 필터링** / **리소스 종류별로 필터링**을 사용합니다. 표시된 목록에서 리소스 또는 리소스 그룹을 선택합니다.

     > [!NOTE]
     > 
     > 활동 로그 신호에 대해 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 추적 리소스, 리소스 그룹 또는 전체 구독만을 선택할 수 있습니다. 

     **경고 대상 샘플 보기**

     ![대상 선택](media/alerts-activity-log/select-target.png)

   - **대상 조건**에서 **조건 추가**를 선택합니다. 대상에 대해 사용 가능한 모든 신호가 표시되고, 여기에는 **활동 로그**의 다양한 범주의 신호가 포함됩니다. 범주 이름은 **서비스 모니터링** 이름에 추가됩니다.

   - **활동 로그** 유형에 대해 가능한 다양한 작업에 대해 표시된 목록에서 신호를 선택합니다.

     이 대상 신호에 대한 로그 기록 타임라인 및 해당 경고 논리를 선택할 수 있습니다.

     **조건 추가 화면**

     ![조건 추가](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  고품질의 효과적인 규칙을 사용하려면 "모든 관리" 신호를 사용하여 규칙에 하나 이상의 조건을 추가해야 합니다. 
     > 경고 정의의 일환으로 다음 드롭다운 중 하나를 선택해야 합니다. "이벤트 수준", "상태" 또는 "초기자"를 선택하면 규칙이 더욱 구체적이게 됩니다.

     - **기록 시간**: 선택한 작업에 사용할 수 있는 이벤트는 지난 6, 12, 24시간 동안 또는 지난 주 동안 표시될 수 있습니다.

     - **경고 논리**:

       - **이벤트 수준**: 이벤트의 심각도 수준: _자세한 정보_, _정보_, _경고_, _오류_ 또는 _위험_.
       - **상태**: 이벤트의 상태: _시작_, _실패_ 또는 _성공_.
       - **이벤트를 시작한 사람**: 호출자라고도 합니다. 작업을 수행한 사용자의 이메일 주소 또는 Active Directory 식별자입니다.

       경고 논리가 적용된 샘플 신호 그래프:

       ![선택한 기준](media/alerts-activity-log/criteria-selected.png)

4. **경고 세부 정보 정의**에서 다음 세부 정보를 입력합니다.

    - **경고 규칙 이름**: 새 경고 규칙의 이름입니다.
    - **설명**: 새 경고 규칙에 대한 설명입니다.
    - **리소스 그룹에 경고 저장**: 이 새 규칙을 저장하고자 하는 리소스 그룹을 선택합니다.

5. **작업 그룹** 아래의 드롭다운 메뉴에서 이 새 경고 규칙에 할당할 작업 그룹을 지정합니다. 또는 [새 작업 그룹을 만들어](../../azure-monitor/platform/action-groups.md) 새 규칙에 할당합니다. 새 그룹을 만들려면 **+ 새 그룹**을 선택합니다.

6. 규칙을 만든 후 사용하도록 설정하려면 **규칙을 만들면 바로 사용**에 대해 **예**를 선택합니다.
7. **경고 규칙 만들기**를 선택합니다.

    활동 로그에 대한 새 경고 규칙이 생성되고 창의 오른쪽 상단에 확인 메시지가 나타납니다.

    규칙을 사용/사용하지 않도록 설정, 편집 또는 삭제할 수 있습니다. 활동 로그 규칙을 관리하는 방법을 알아봅니다.


활동 로그에 대한 경고 규칙을 만들 수 있는 조건을 이해하는 간단한 방법은 [Azure Portal의 활동 로그](activity-log-view.md#azure-portal)를 통해 이벤트를 탐색하고 필터링하는 것입니다. **Azure Monitor - 활동 로그** 화면에서 필요한 이벤트를 필터링하거나 찾고 **활동 로그 경고 추가** 단추를 사용하여 경고를 만들 수 있습니다. 이후 앞에 있는 4~7단계를 수행합니다.
    
 ![활동 로그에서 경고 추가](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Azure Portal에서 보기 및 관리

1. Azure Portal에서 **모니터** > **경고**를 선택합니다. 창의 왼쪽 상단에서 **경고 규칙 관리**를 선택합니다.

    ![경고 규칙 관리](media/alerts-activity-log/manage-alert-rules.png)

    사용 가능한 규칙 목록이 나타납니다.

2. 수정할 활동 로그 규칙을 검색합니다.

    ![활동 로그 경고 규칙 검색](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    사용 가능한 필터(구독, 리소스 그룹, 리소스, 신호 유형 또는 상태)를 사용하여 편집할 활동 규칙을 찾을 수 있습니다.

   > [!NOTE]
   > 
   > **설명** , **대상 조건** 및 **작업 그룹**만 편집할 수 있습니다.

3. 규칙을 선택하고 두 번 클릭하여 규칙 옵션을 편집합니다. 필요한 변경 작업을 수행하고 **저장**을 선택합니다.

   ![경고 규칙 관리](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. 규칙을 사용/사용하지 않도록 설정하거나 삭제할 수 있습니다. 2단계에서 설명한 대로 규칙을 선택한 후 창의 맨 위에서 적절한 옵션을 선택합니다.


## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿
Azure Resource Manager 템플릿을 사용하여 활동 로그 경고 규칙을 만들려면 `microsoft.insights/activityLogAlerts` 종류의 리소스를 만듭니다. 그런 다음 모든 관련된 속성을 입력합니다. 다음은 활동 로그 경고 규칙을 만드는 템플릿입니다.

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
이전 샘플 JSON은 이 연습의 목적을 위해 예를 들어 sampleActivityLogAlert.json으로 저장될 수 있으며 [Azure Portal에서 Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md)를 사용하여 배포될 수 있습니다.

다음 필드는 조건 필드의 Azure Resource Manager 템플릿에서 사용할 수 있는 옵션입니다. “Resource Health”, “Advisor” 및 “Service Health”에는 특수 필드에 대한 추가 속성 필드가 있습니다. 
1. resourceId:  경고가 생성되어야 하는 활동 로그 이벤트에서 영향을 받는 리소스의 리소스 ID입니다.
2. category: 활동 로그 이벤트의 범주입니다. 다음은 그 예입니다.  Administrative, ServiceHealth, ResourceHealth, Autoscale, Security, Recommendation, Policy.
3. caller: 활동 로그 이벤트의 작업을 수행한 사용자의 메일 주소 또는 Azure Active Directory 식별자입니다.
4. level: 경고가 생성되어야 하는 활동 로그 이벤트의 활동 수준입니다. 다음은 그 예입니다.  Critical, Error, Warning, Informational, Verbose.
5. operationName: 활동 로그 이벤트의 작업 이름입니다. 다음은 그 예입니다.  Microsoft.Resources/deployments/write
6. resourceGroup: 활동 로그 이벤트에서 영향을 받는 리소스의 리소스 그룹 이름입니다.
7. resourceProvider: [Azure 리소스 공급자 및 종류 설명](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). 리소스 공급자를 Azure 서비스로 매핑하는 목록은 [Azure 서비스용 리소스 공급자](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)를 참조하세요.
8. status: 활동 이벤트의 작업 상태를 설명하는 문자열입니다. 다음은 그 예입니다.  Started, In Progress, Succeeded, Failed, Active, Resolved
9. subStatus: 하지만 일반적으로 해당 REST 호출의 HTTP 상태 코드는 하위 상태를 설명하는 다른 문자열을 포함할 수도 있습니다.   다음은 그 예입니다.  확인(HTTP 상태 코드: 200), 만들어짐(HTTP 상태 코드: 201), 수락됨(HTTP 상태 코드: 202), 콘텐츠 없음(HTTP 상태 코드: 204), 잘못된 요청(HTTP 상태 코드: 400), 찾을 수 없음(HTTP 상태 코드: 404), 충돌(HTTP 상태 코드: 409), 내부 서버 오류(HTTP 상태 코드: 500), 서비스를 사용할 수 없음(HTTP 상태 코드: 503), 게이트웨이 시간 초과(HTTP 상태 코드: 504) 등이 있습니다.
10. resourceType: 이벤트의 영향을 받은 리소스 종류입니다. 다음은 그 예입니다.  Microsoft.Resources/deployments

다음은 그 예입니다.

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
[여기](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)에서 활동 로그 필드에 대한 자세한 내용을 확인할 수 있습니다.



> [!NOTE]
> 새로운 활동 로그 경고 규칙이 활성화되는 데에는 최대 5분이 걸릴 수 있습니다.

## <a name="rest-api"></a>REST API 
[Azure Monitor 활동 로그 경고 API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts)는 REST API입니다. Azure Resource Manager REST API와 완전히 호환됩니다. Resource Manager cmdlet 또는 Azure CLI를 사용하여 PowerShell을 통해 사용할 수 있습니다.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>PowerShell을 사용하여 Resource Manager 템플릿 배포
PowerShell을 사용하여 이전 [Azure Resource Manager 템플릿](#azure-resource-manager-template) 섹션에 표시된 샘플 Resource Manager 템플릿을 배포하려면 다음 명령을 사용합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

여기에서 sampleActivityLogAlert.parameters.json에는 경고 규칙 생성에 필요한 매개 변수에 대해 제공된 값이 있습니다.

### <a name="use-activity-log-powershell-cmdlets"></a>활동 로그 PowerShell cmdlet 사용

활동 로그 경고에서는 다음과 같은 전용 PowerShell cmdlet을 사용할 수 있습니다.

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): 새 활동 로그 경고를 만들거나 기존 활동 로그 경고를 업데이트합니다.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): 하나 이상의 활동 로그 경고 리소스를 가져옵니다.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): 기존 활동 로그 경고를 사용 설정하고 태그를 설정합니다.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): 기존 활동 로그 경고를 사용하지 않도록 설정하고 태그를 설정합니다.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): 활동 로그 경고를 제거합니다.

## <a name="azure-cli"></a>Azure CLI

설정된 [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert)의 전용 Azure CLI 명령을 활동 로그 경고 규칙을 관리하는 데 사용할 수 있습니다.

새로운 활동 로그 경고 규칙을 만들려면 다음 명령을 순서대로 사용합니다.

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): 새로운 활동 로그 경고 규칙 리소스를 만듭니다.
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): 생성된 활동 로그 경고 규칙에 대한 범위를 추가합니다.
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): 활동 로그 경고 규칙에 작업 그룹을 추가합니다.

하나의 활동 로그 경고 규칙 리소스를 검색하려면 Azure CLI 명령 [az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)를 사용합니다. 리소스 그룹의 모든 활동 로그 경고 규칙 리소스를 보려면 [az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)를 사용합니다.
Azure CLI 명령 [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)를 사용하여 활동 로그 경고 규칙 리소스를 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [활동 로그에 대한 웹후크 스키마](../../azure-monitor/platform/activity-log-alerts-webhook.md)를 알아봅니다.
- [활동 로그 개요](../../azure-monitor/platform/activity-log-alerts.md)를 읽습니다.
- [작업 그룹](../../azure-monitor/platform/action-groups.md)에 대해 자세히 알아보세요.  
- [서비스 상태 알림](../../azure-monitor/platform/service-notifications.md)에 대해 자세히 알아보세요.
