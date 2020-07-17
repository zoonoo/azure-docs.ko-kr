---
title: Azure Resource Manager 템플릿을 사용하여 Log Analytics 작업 영역에 Azure 활동 로그 보내기
description: ARM 템플릿을 사용하여 Log Analytics 작업 영역 및 Azure Monitor 로그에 활동 로그를 보내는 진단 설정을 만듭니다.
ms.subservice: logs
ms.topic: quickstart
ms.custom: subject-armqs
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 26e8c40c35b130510f1bf8ae1456cb15907b345c
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851915"
---
# <a name="quickstart-send-azure-activity-log-to-log-analytics-workspace-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Log Analytics 작업 영역에 Azure 활동 로그 보내기

활동 로그는 구독 수준 이벤트에 대한 인사이트를 제공하는 Azure의 플랫폼 로그입니다. 여기에는 리소스가 수정되거나 가상 머신이 시작되는 등의 이벤트 정보가 포함됩니다. Azure Portal에서 활동 로그를 보거나 PowerShell 및 CLI를 사용하여 항목을 검색할 수 있습니다. 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Log Analytics 작업 영역 및 Azure Monitor 로그에 활동 로그를 보내는 진단 설정을 만드는 방법을 보여줍니다. Azure Monitor 로그에서는 [로그 쿼리](../log-query/log-query-overview.md)를 사용하여 활동 로그를 분석하고 [로그 경고](../platform/alerts-log-query.md) 및 [통합 문서](../platform/workbooks-overview.md) 같은 다른 기능을 사용할 수 있습니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 로컬 컴퓨터에서 명령을 실행하려면 Azure CLI 또는 Azure PowerShell 모듈을 설치합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli) 및 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

### <a name="review-the-template"></a>템플릿 검토

다음 템플릿은 빈 Log Analytics 작업 영역을 만듭니다. 이 템플릿을 *CreateWorkspace.json*으로 저장합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
        "metadata": {
          "description": "Name of the workspace."
        }
    },
    "sku": {
      "type": "string",
      "allowedValues": [
        "pergb2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "defaultValue": "pergb2018",
      "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral",
        "australiaeast",
        "australiasoutheast",
        "brazilsouth",
        "canadacentral",
        "centralindia",
        "centralus",
        "eastasia",
        "eastus",
        "eastus2",
        "francecentral",
        "japaneast",
        "koreacentral",
        "northcentralus",
        "northeurope",
        "southafricanorth",
        "southcentralus",
        "southeastasia",
        "switzerlandnorth",
        "switzerlandwest",
        "uksouth",
        "ukwest",
        "westcentralus",
        "westeurope",
        "westus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for the workspace."
      }
    },
    "retentionInDays": {
      "type": "int",
      "defaultValue": 120,
      "metadata": {
        "description": "Number of days to retain data."
      }
    },
    "resourcePermissions": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "true to use resource or workspace permissions. false to require workspace permissions."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-03-01-preview",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "name": "[parameters('sku')]"
        },
        "retentionInDays": "[parameters('retentionInDays')]",
        "features": {
          "searchVersion": 1,
          "legacy": 0,
          "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
        }
      }
    }
  ]
}
```

이 템플릿은 다음 리소스를 정의합니다.

- [Microsoft.OperationalInsights/workspaces](/azure/templates/microsoft.operationalinsights/workspaces)

### <a name="deploy-the-template"></a>템플릿 배포

CLI 및 PowerShell을 사용하는 다음 예제처럼 [ARM 템플릿 배포](../../azure-resource-manager/templates/deploy-portal.md)를 위한 표준 방법을 사용하여 템플릿을 배포합니다. **리소스 그룹**, **workspaceName** 및 **위치**의 샘플 값을 각자 환경에 맞는 값으로 바꿉니다. 작업 영역 이름은 모든 Azure 구독에서 고유해야 합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create \
    --name CreateWorkspace \
    --resource-group my-resource-group \
    --template-file CreateWorkspace.json \
    --parameters workspaceName='my-workspace-01' location='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile CreateWorkspace.json -workspaceName my-workspace-01 -location eastus
```

---

### <a name="validate-the-deployment"></a>배포 유효성 검사

다음 명령 중 하나를 사용하여 작업 영역이 생성되었는지 확인합니다. **리소스 그룹** 및 **workspaceName**의 샘플 값을 위에서 사용한 값으로 바꿉니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor log-analytics workspace show --resource-group my-workspace-01 --workspace-name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzOperationalInsightsWorkspace -Name my-workspace-01 -ResourceGroupName my-resource-group
```

---

## <a name="create-diagnostic-setting"></a>진단 설정 만들기

### <a name="review-the-template"></a>템플릿 검토

다음 템플릿은 활동 로그를 Log Analytics 작업 영역으로 보내는 진단 설정을 만듭니다. 이 템플릿을 *CreateDiagnosticSetting.json*으로 저장합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "settingName": {
        "type": "String"
    },
    "workspaceId": {
        "type": "String"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[parameters('settingName')]",
      "dependsOn": [],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "logs": [
          {
          "category": "Administrative",
          "enabled": true
          },
          {
          "category": "Alert",
          "enabled": true
          },
          {
          "category": "Autoscale",
          "enabled": true
          },
          {
          "category": "Policy",
          "enabled": true
          },
          {
          "category": "Recommendation",
          "enabled": true
          },
          {
          "category": "ResourceHealth",
          "enabled": true
          },
          {
          "category": "Security",
          "enabled": true
          },
          {
          "category": "ServiceHealth",
          "enabled": true
          }
        ]
      }
    }
  ]
}
```

이 템플릿은 다음 리소스를 정의합니다.

- [Microsoft.Insights/diagnosticSettings](/azure/templates/microsoft.insights/diagnosticsettings)

### <a name="deploy-the-template"></a>템플릿 배포

CLI 및 PowerShell을 사용하는 다음 예제처럼 [ARM 템플릿 배포](../../azure-resource-manager/templates/deploy-portal.md)를 위한 표준 방법을 사용하여 템플릿을 배포합니다. **리소스 그룹**, **workspaceName** 및 **위치**의 샘플 값을 각자 환경에 맞는 값으로 바꿉니다. 작업 영역 이름은 모든 Azure 구독에서 고유해야 합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment sub create --name CreateDiagnosticSetting --location eastus --template-file CreateDiagnosticSetting.json --parameters settingName='Send Activity log to workspace' workspaceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzSubscriptionDeployment -Name CreateDiagnosticSetting -location eastus -TemplateFile CreateDiagnosticSetting.json -settingName="Send Activity log to workspace" -workspaceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01"
```
---

### <a name="validate-the-deployment"></a>배포 유효성 검사

다음 명령 중 하나를 사용하여 진단 설정이 생성되었는지 확인합니다. 구독 및 설정 이름의 샘플 값을 위에서 사용한 값으로 바꿉니다.

> [!NOTE]
> 현재는 PowerShell을 사용하여 구독 수준 진단 설정을 검색할 수 없습니다.

```azurecli
az monitor diagnostic-settings show --resource '/subscriptions/00000000-0000-0000-0000-000000000000' --name 'Send Activity log to workspace'
```

## <a name="generate-log-data"></a>로그 데이터 생성

새 활동 로그 항목만 Log Analytics 작업 영역으로 전송되므로, 구독에서 가상 머신 시작/중지 또는 다른 리소스 만들기/수정처럼 로깅될 작업을 수행합니다. 진단 설정이 만들어지고 작업 영역에 데이터가 처음 기록될 때까지 몇 분 정도 걸릴 수 있습니다. 이 시간이 지나면 작업 로그에 기록된 모든 이벤트가 몇 초 내에 작업 영역으로 전송됩니다.

## <a name="retrieve-data-with-a-log-query"></a>로그 쿼리를 사용하여 데이터 검색

Azure Portal에서 Log Analytics를 사용하여 작업 영역의 데이터를 검색합니다. Azure Portal에서 **모니터**를 검색하여 선택합니다.

![Azure portal](media/quick-collect-activity-log/azure-portal-monitor.png)

**Azure Monitor** 메뉴에서 **로그**를 선택합니다. **예제 쿼리** 페이지를 닫습니다. 범위를 앞에서 만든 작업 영역으로 설정하지 않은 경우 **범위 선택**을 클릭하여 앞에서 만든 작업 영역을 찾습니다.

![Log Analytics 범위](media/quick-collect-activity-log/log-analytics-scope.png)

쿼리 창에서 `AzureActivity`를 입력하고 **실행**을 클릭합니다. 활동 로그에서 보낸 모든 레코드를 포함하는 *AzureActivity* 테이블의 모든 레코드를 반환하는 단순 쿼리입니다.

![단순 쿼리](media/quick-collect-activity-log/query-01.png)

레코드 중 하나를 확장하면 자세한 속성이 표시됩니다.

![속성 확장](media/quick-collect-activity-log/expand-properties.png)

범주별로 요약된 이벤트 수를 제공하는 `AzureActivity | summarize count() by CategoryValue`처럼 좀 더 복잡한 쿼리를 시도해 봅시다

![복합한 쿼리](media/quick-collect-activity-log/query-02.png)

## <a name="clean-up-resources"></a>리소스 정리

후속 빠른 시작 및 자습서를 계속 사용하려는 경우 이러한 리소스를 그대로 유지할 수 있습니다. 더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 그러면 경고 규칙과 관련 리소스가 삭제됩니다. Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 삭제하려면 다음을 수행합니다.

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

이 빠른 시작에서는 Log Analytics 작업 영역으로 전송되도록 활동 로그를 구성했습니다. 이제 작업 영역으로 수집할 다른 데이터를 구성할 수 있습니다. 그런 다음, Azure Monitor의 [로그 쿼리](../log-query/log-query-overview.md)를 사용하여 함께 분석하고 [로그 경고](../platform/alerts-log-query.md) 및 [통합 문서](../platform/workbooks-overview.md) 등의 기능을 활용할 수 있습니다. 다음으로, 활동 로그의 데이터를 보충하여 각 리소스 내에서 수행된 작업에 대한 인사이트를 제공하는 [리소스 로그](../platform/resource-logs.md)를 Azure 리소스에서 수집해야 합니다.

> [!div class="nextstepaction"]
> [Azure Monitor를 사용하여 리소스 로그 수집 및 분석](tutorial-resource-logs.md)