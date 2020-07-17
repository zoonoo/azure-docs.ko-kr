---
title: 리소스 관리자 템플릿을 사용 하 여 새 권장 사항에 대 한 Azure Advisor 경고 만들기
description: 새 권장 사항에 대 한 Azure Advisor 경고 만들기
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: ef15891cc01d0481c6253023de262f14dce0ec81
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921073"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용 하 여 새 권장 사항에 대 한 Azure Advisor 경고 만들기

이 문서에서는 Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 하 여 Azure Advisor에서 새로운 권장 사항에 대 한 경고를 설정 하는 방법을 보여 줍니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure Advisor에서 리소스 중 하나에 대 한 새로운 권장 사항을 발견할 때마다 이벤트는 [Azure 활동 로그](/azure/azure-monitor/platform/activity-logs-overview)에 저장 됩니다. 권장 사항 별 경고 생성 환경을 사용 하 여 Azure Advisor에서 이러한 이벤트에 대 한 경고를 설정할 수 있습니다. 구독을 선택 하 고 필요에 따라 리소스 그룹을 선택 하 여 경고를 수신 하려는 리소스를 지정할 수 있습니다.

이러한 속성을 사용 하 여 권장 구성 유형을 확인할 수도 있습니다.

- 범주
- 영향 수준
- 권장 사항 유형

또한에서 경고를 트리거할 때 수행 되는 작업을 구성할 수 있습니다.  

- 기존 작업 그룹을 선택 합니다.
- 새 작업 그룹 만들기

작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../azure-monitor/platform/action-groups.md)를 참조하세요.

> [!NOTE]
> Advisor 경고는 현재 고가용성, 성능 및 비용 권장 사항에 대해서만 사용할 수 있습니다. 보안 권장 사항은 지원 되지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 로컬 컴퓨터에서 명령을 실행 하려면 Azure CLI 또는 Azure PowerShell 모듈을 설치 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli) 및 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조 하세요.

## <a name="review-the-template"></a>템플릿 검토

다음 템플릿은 전자 메일 대상을 사용 하 여 작업 그룹을 만들고 대상 구독에 대 한 모든 서비스 상태 알림을 사용 하도록 설정 합니다. 이 템플릿을 *CreateAdvisorAlert.js*로 저장 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2019-06-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

템플릿은 다음 두 리소스를 정의 합니다.

- [Microsoft Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>템플릿 배포

CLI 및 PowerShell을 사용 하 여 다음 예제와 같은 [ARM 템플릿 배포](../azure-resource-manager/templates/deploy-portal.md) 를 위한 표준 방법을 사용 하 여 템플릿을 배포 합니다. **리소스 그룹**에 대 한 샘플 값과 **emailAddress** 을 사용자 환경에 적합 한 값으로 바꿉니다. 작업 영역 이름은 모든 Azure 구독에서 고유 해야 합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>배포 유효성 검사

다음 명령 중 하나를 사용 하 여 작업 영역을 만들었는지 확인 합니다. **리소스 그룹** 에 대 한 샘플 값을 위에서 사용한 값으로 바꿉니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>리소스 정리

후속 빠른 시작 및 자습서를 계속 사용하려는 경우 이러한 리소스를 그대로 유지할 수 있습니다. 더 이상 필요 하지 않은 경우 리소스 그룹을 삭제 합니다. 그러면 경고 규칙 및 관련 리소스가 삭제 됩니다. Azure CLI 또는 Azure PowerShell를 사용 하 여 리소스 그룹을 삭제 하려면

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>다음 단계

- [활동 로그 경고의 개요](../azure-monitor/platform/alerts-overview.md)를 확인하고 경고를 받는 방법에 대해 알아보세요.
- [작업 그룹](../azure-monitor/platform/action-groups.md)에 대해 자세히 알아보세요.
