---
title: VM용 Azure Monitor 게스트 상태 (미리 보기) 사용
description: 구독에서 VM용 Azure Monitor 게스트 상태를 사용 하도록 설정 하는 방법 및 Vm을 등록 하는 방법에 대해 설명 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.openlocfilehash: 6033ca5b0eaf6845d14407832c776dd8e006226b
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686975"
---
# <a name="enable-azure-monitor-for-vms-guest-health-preview"></a>VM용 Azure Monitor 게스트 상태 (미리 보기) 사용
VM용 Azure Monitor 게스트 상태를 사용 하면 일정 한 간격으로 샘플링 되는 성능 측정 집합에 정의 된 대로 가상 컴퓨터의 상태를 볼 수 있습니다. 이 문서에서는 구독에서이 기능을 사용 하도록 설정 하는 방법과 각 가상 컴퓨터에 대해 게스트 모니터링을 사용 하도록 설정 하는 방법을 설명 합니다.

## <a name="current-limitations"></a>현재 제한 사항
VM용 Azure Monitor 게스트 상태는 공개 미리 보기에서 다음과 같은 제한 사항이 있습니다.

- 현재 Azure virtual machines만 지원 됩니다. 서버에 대 한 Azure Arc는 현재 지원 되지 않습니다.
- 가상 컴퓨터에서 다음 운영 체제 중 하나를 실행 해야 합니다. 
  - Ubuntu 16.04 LTS, Ubuntu 18.04 LTS
  - Windows Server 2012 이상
- 가상 머신은 다음 지역 중 하나에 있어야 합니다.
  - 오스트레일리아 남동부
  - 미국 중부
  - 미국 동부
  - 미국 동부 2
  - 미국 동부 2 EUAP
  - 북유럽
  - 동남아시아
  - 영국 남부
  - 서유럽
  - 미국 서부
  - 미국 서부 2
- Log Analytics 작업 영역은 다음 지역 중 하나에 있어야 합니다.
  - 미국 동부
  - 미국 동부 2 EUAP
  - 유럽 서부 지역

## <a name="prerequisites"></a>사전 요구 사항

- VM용 Azure Monitor 하려면 가상 컴퓨터를 등록 해야 합니다.
- 온 보 딩 단계를 실행 하는 사용자에 게는 가상 머신과 데이터 수집 규칙이 있는 구독에 대 한 최소 참가자 수준 액세스 권한이 있어야 합니다.
- 다음 섹션에 설명 된 대로 필요한 Azure 리소스 공급자를 등록 해야 합니다.


## <a name="register-required-azure-resource-providers"></a>필요한 Azure 리소스 공급자 등록
다음 Azure 리소스 공급자는 VM용 Azure Monitor 게스트 상태를 사용 하도록 설정 하기 위해 구독에 등록 해야 합니다. 

- Microsoft.WorkloadMonitor
- Microsoft.Insights

[Azure 리소스 공급자 및 형식](../../azure-resource-manager/management/resource-providers-and-types.md)에 설명 된 대로 사용 가능한 방법 중 하나를 사용 하 여 리소스 공급자를 등록할 수 있습니다. Armclient, postman 또는 다른 메서드를 사용 하 여 Azure Resource Manager에 대 한 인증 된 호출을 수행 하는 다음 샘플 명령을 사용할 수도 있습니다.

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Azure Portal를 사용 하 여 가상 컴퓨터 사용
Azure Portal에서 가상 컴퓨터에 대 한 게스트 상태를 사용 하도록 설정 하면 필요한 모든 구성이 수행 됩니다. 여기에는 데이터 수집 규칙을 만들고, 가상 컴퓨터에 게스트 상태 확장을 설치 하 고, 데이터 수집 규칙과의 연결을 만드는 작업이 포함 됩니다.

VM용 Azure Monitor의 **시작** 보기에서 가상 머신에 대 한 업그레이드 메시지 옆의 링크를 클릭 한 후 **업그레이드** 단추를 클릭 합니다. 여러 가상 컴퓨터를 선택 하 여 함께 업그레이드할 수도 있습니다.

![가상 머신에서 상태 기능 사용](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 가상 컴퓨터 사용
Azure Resource Manager를 사용 하 여 가상 컴퓨터를 사용 하도록 설정 하는 데 필요한 세 가지 단계가 있습니다.

- 데이터 수집 규칙을 만듭니다.
- 각 가상 컴퓨터에 게스트 상태 확장을 설치 합니다.
- 가상 머신과 데이터 수집 규칙 간에 연결을 만듭니다.

### <a name="create-data-collection-rule-dcr"></a>데이터 수집 규칙 만들기 (DCR)

> [!NOTE]
> Azure Portal를 사용 하 여 가상 컴퓨터를 사용 하도록 설정 하면 여기에 설명 된 데이터 수집 규칙이 생성 됩니다. 이 경우이 단계를 수행할 필요가 없습니다.

VM용 Azure Monitor 게스트 상태의 모니터 구성은 [데이터 수집 규칙 (DCR)](../platform/data-collection-rule-overview.md)에 저장 됩니다. 게스트 상태 확장이 있는 가상 컴퓨터에 대 한 모든 모니터를 사용 하도록 설정 하려면 아래 리소스 관리자 템플릿에 정의 된 데이터 수집 규칙을 설치 합니다. 게스트 상태 확장이 있는 각 가상 컴퓨터에는이 규칙과의 연결이 필요 합니다.

> [!NOTE]
> [VM용 Azure Monitor 게스트 상태 (미리 보기)의 모니터링 구성](vminsights-health-configure.md)에 설명 된 대로 추가 데이터 수집 규칙을 만들어 모니터의 기본 구성을 수정할 수 있습니다.

템플릿에는 다음 매개 변수에 대 한 값이 필요 합니다.

- **defaultHealthDataCollectionRuleName**: 템플릿에 정의 된 기본 이름을 유지 합니다.
- **destinationWorkspaceResourceId**: 가상 컴퓨터 데이터 수집에 사용 되는 Log Analytics 작업 영역의 리소스 ID입니다.
- **dataCollectionRuleLocation**: 데이터 수집 규칙의 지역입니다. 이는 Log Analytics 작업 영역의 영역과 일치 해야 합니다.


[리소스 관리자 템플릿에 대 한 배포 방법을](../../azure-resource-manager/templates/deploy-powershell.md)사용 하 여 템플릿을 배포 합니다. 다음 명령은 PowerShell 또는 Azure CLI를 사용 하 여 템플릿 및 매개 변수 파일을 배포 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="cli"></a>[CLI](#tab/cli)

```cli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---



```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data colleciton rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              }
            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>샘플 매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



## <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>게스트 상태 확장 설치 및 데이터 수집 규칙에 연결
게스트 상태에 대해 가상 컴퓨터를 사용 하도록 설정 하려면 다음 리소스 관리자 템플릿을 사용 합니다. 그러면 게스트 상태 확장이 설치 되 고 데이터 수집 규칙과의 연결이 생성 됩니다. [리소스 관리자 템플릿에 대 한 배포 방법을](../../azure-resource-manager/templates/deploy-powershell.md)사용 하 여이 템플릿을 배포할 수 있습니다.


예를 들어 다음 명령을 통해 PowerShell 또는 Azure CLI를 사용하여 리소스 그룹에 템플릿 및 매개 변수 파일을 배포합니다.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="cli"></a>[CLI](#tab/cli)

```cli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>템플릿 파일

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>샘플 매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      },
      "healthExtensionVersion": {
        "value": "private-preview"
      }
  }
}
```

## <a name="next-steps"></a>다음 단계

- [VM용 Azure Monitor에서 사용 하도록 설정 된 모니터 사용자 지정](vminsights-health-configure.md)