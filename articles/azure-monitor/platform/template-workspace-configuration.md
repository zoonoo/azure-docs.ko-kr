---
title: Azure Resource Manager 템플릿을 사용하여 Log Analytics 작업 영역 만들기 및 구성 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 Log Analytics 작업 영역 만들고 구성할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d21ca1b0-847d-4716-bb30-2a8c02a606aa
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: magoedte
ms.openlocfilehash: a55a4b2f3045aac8dfe9e46a50074585ab3ef491
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827787"
---
# <a name="manage-log-analytics-workspace-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하 여 Log Analytics 작업 영역 관리

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

사용할 수 있습니다 [Azure Resource Manager 템플릿](../../azure-resource-manager/resource-group-authoring-templates.md) 만들기 및 Azure Monitor에서 Log Analytics 작업 영역을 구성 합니다. 템플릿을 사용하여 수행할 수 있는 작업의 예:

* 설정 가격 책정 계층을 포함하는 작업 영역 만들기 
* 솔루션 추가
* 저장된 검색 만들기
* 컴퓨터 그룹 만들기
* Windows 에이전트가 설치된 컴퓨터에서 IIS 로그 수집 활성화
* Linux 및 Windows 컴퓨터에서 성능 카운터 수집
* Linux 컴퓨터의 syslog에서 이벤트 수집 
* Windows 이벤트 로그에서 이벤트 수집
* Azure 가상 머신에 로그 분석 에이전트 추가
* Azure 진단을 사용하여 수집된 데이터를 인덱싱하도록 로그 분석 구성

이 문서에서는 템플릿에서 수행할 수 있는 몇 가지 구성을 보여 주는 템플릿 샘플을 제공합니다.

## <a name="api-versions"></a>API 버전

다음 표에는 이 예제에서 사용된 리소스의 API 버전이 제공됩니다.

| 리소스 | 리소스 종류 | API 버전 |
|:---|:---|:---|
| 작업 영역   | workspaces    | 2017-03-15-preview |
| 검색      | savedSearches | 2015-03-20 |
| 데이터 원본 | datasources   | 2015-11-01-preview |
| 솔루션    | solutions     | 2015-11-01-preview |

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

다음 예제에서는 로컬 컴퓨터에서 템플릿을 사용하여 작업 영역을 만듭니다. JSON 템플릿 이름 및 새 작업 영역에 (다른 작업 영역 등의 매개 변수 가격 책정 계층 및 보존에 대 한 기본 값 사용)의 위치에만 필요 하도록 구성 됩니다.  

### <a name="create-and-deploy-template"></a>템플릿 만들기 및 배포

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
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
              "uksouth", 
              "ukwest", 
              "westcentralus", 
              "westeurope", 
              "westus", 
              "westus2" 
            ],
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. 요구 사항을 충족하도록 템플릿을 편집합니다. 지원되는 속성 및 값은 [Microsoft.OperationalInsights/workspaces 템플릿](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) 참조를 검토하세요. 
3. 이 파일을 로컬 폴더에 **deploylaworkspacetemplate.json**으로 저장합니다.
4. 이제 이 템플릿을 배포할 수 있습니다. 명령의 일부로 작업 영역 이름 및 위치를 지정 하는 작업 영역을 만들려면 PowerShell 또는 명령줄을 사용 합니다.

   * PowerShell의 경우 템플릿이 포함된 폴더에서 다음 명령을 사용합니다.
   
        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json -workspaceName <workspace-name> -location <location>
        ```

   * 명령줄의 경우 템플릿이 포함된 폴더에서 다음 명령을 사용합니다.

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json --workspaceName <workspace-name> --location <location>
        ```

배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사하게 결과가 포함된 메시지가 표시됩니다.<br><br> ![배포가 완료되었을 때 결과 예](./media/template-workspace-configuration/template-output-01.png)

## <a name="configure-a-log-analytics-workspace"></a>Log Analytics 작업 영역 구성

다음 템플릿 샘플에서는 다음 작업의 방법을 보여 줍니다.

1. 작업 영역에 솔루션 추가
2. 저장된 검색 만들기
3. 컴퓨터 그룹 만들기
4. Windows 에이전트가 설치된 컴퓨터에서 IIS 로그 수집 활성화
5. Linux 컴퓨터에서 논리 디스크 성능 카운터 수집(사용된 Inode 비율, 사용 가능한 MB, 사용된 공간 비율, 초당 디스크 전송, 초당 디스크 읽기, 초당 디스크 쓰기)
6. Linux 컴퓨터에서 syslog 이벤트 수집
7. Windows 컴퓨터에서 애플리케이션 이벤트 로그의 오류 및 경고 이벤트 수집
8. Windows 컴퓨터에서 사용 가능한 메모리(MB) 성능 카운터 수집
9. Azure 진단을 통해 스토리지 계정에 기록한 Windows 이벤트 로그 및 IIS 로그 수집

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Workspace name"
      }
    },
    "pricingTier": {
      "type": "string",
      "allowedValues": [
        "PerGB2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "defaultValue": "PerGB2018",
      "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
      }
    },
    {
    "immediatePurgeDataOn30Days": {
      "type": "bool",
      "metadata": {
        "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
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
        "uksouth", 
        "ukwest", 
        "westcentralus", 
        "westeurope", 
        "westus", 
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location in which to create the workspace."
      }
    },
    "applicationDiagnosticsStorageAccountName": {
        "type": "string",
        "metadata": {
          "description": "Name of the storage account with Azure diagnostics output"
        }
    },
    "applicationDiagnosticsStorageAccountResourceGroup": {
        "type": "string",
        "metadata": {
          "description": "The resource group name containing the storage account with Azure diagnostics output"
        }
    }
  },
  "variables": {
    "Updates": {
      "Name": "[Concat('Updates', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "Updates"
    },
    "AntiMalware": {
      "Name": "[concat('AntiMalware', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "AntiMalware"
    },
    "SQLAssessment": {
      "Name": "[Concat('SQLAssessment', '(', parameters('workspaceName'), ')')]",
      "GalleryName": "SQLAssessment"
    },
    "diagnosticsStorageAccount": "[resourceId(parameters('applicationDiagnosticsStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-11-01-preview",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "name": "[parameters('pricingTier')]"
          "features": {
            "immediatePurgeDataOn30Days": "[parameters('immediatePurgeDataOn30Days')]"
          }
        },
    "retentionInDays": "[parameters('dataRetention')]"
      },
      "resources": [
        {
          "apiVersion": "2015-03-20",
          "name": "VMSS Queries2",
          "type": "savedSearches",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "Category": "VMSS",
            "ETag": "*",
            "DisplayName": "VMSS Instance Count",
            "Query": "Event | where Source == \"ServiceFabricNodeBootstrapAgent\" | summarize AggregatedValue = count() by Computer",
            "Version": 1
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsEvent1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleWindowsPerfCounter1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsPerformanceCounter",
          "properties": {
            "objectName": "Memory",
            "instanceName": "*",
            "intervalSeconds": 10,
            "counterName": "Available MBytes"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleIISLog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "IISLogs",
          "properties": {
            "state": "OnPremiseEnabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslog1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslog",
          "properties": {
            "syslogName": "kern",
            "syslogSeverities": [
              {
                "severity": "emerg"
              },
              {
                "severity": "alert"
              },
              {
                "severity": "crit"
              },
              {
                "severity": "err"
              },
              {
                "severity": "warning"
              }
            ]
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleSyslogCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxSyslogCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerf1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceObject",
          "properties": {
            "performanceCounters": [
              {
                "counterName": "% Used Inodes"
              },
              {
                "counterName": "Free Megabytes"
              },
              {
                "counterName": "% Used Space"
              },
              {
                "counterName": "Disk Transfers/sec"
              },
              {
                "counterName": "Disk Reads/sec"
              },
              {
                "counterName": "Disk Writes/sec"
              }
            ],
            "objectName": "Logical Disk",
            "instanceName": "*",
            "intervalSeconds": 10
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "type": "datasources",
          "name": "sampleLinuxPerfCollection1",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "LinuxPerformanceCollection",
          "properties": {
            "state": "Enabled"
          }
        },
        {
          "apiVersion": "2015-03-20",
          "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('workspaceName'))]",
          "type": "storageinsightconfigs",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "containers": [ 
              "wad-iis-logfiles" 
            ],
            "tables": [
              "WADWindowsEventLogsTable"
            ],
            "storageAccount": {
              "id": "[variables('diagnosticsStorageAccount')]",
              "key": "[listKeys(variables('diagnosticsStorageAccount'),'2015-06-15').key1]"
            }
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('Updates').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('Updates').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('Updates').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('AntiMalware').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('AntiMalware').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('AntiMalware').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('AntiMalware').GalleryName)]",
            "promotionCode": ""
          }
        },
        {
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "name": "[variables('SQLAssessment').Name]",
          "type": "Microsoft.OperationsManagement/solutions",
          "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('SQLAssessment').Name)]",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          },
          "plan": {
            "name": "[variables('SQLAssessment').Name]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('SQLAssessment').GalleryName)]",
            "promotionCode": ""
          }
        }
      ]
    }
  ],
  "outputs": {
    "workspaceName": {
      "type": "string",
      "value": "[parameters('workspaceName')]"
    },
    "provisioningState": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').provisioningState]"
    },
    "source": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').source]"
    },
    "customerId": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').customerId]"
    },
    "pricingTier": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').sku.name]"
    },
    "retentionInDays": {
      "type": "int",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').retentionInDays]"
    },
    "immediatePurgeDataOn30Days": {  
      "type": "bool",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').features.immediatePurgeDataOn30Days]"
    },
    "portalUrl": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-11-01-preview').portalUrl]"
    }
  }
}

```
### <a name="deploying-the-sample-template"></a>샘플 템플릿 배포

샘플 템플릿을 배포하려면

1. 연결된 샘플을 파일(예: `azuredeploy.json`)에 저장합니다. 
2. 원하는 구성으로 템플릿을 편집합니다.
3. PowerShell 또는 명령줄을 사용하여 템플릿을 배포합니다.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile azuredeploy.json
```

#### <a name="command-line"></a>명령 줄

```cmd
azure config mode arm
azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile azuredeploy.json
```

## <a name="example-resource-manager-templates"></a>Resource Manager 템플릿 예

Azure 빠른 시작 템플릿 갤러리에는 다음과 같이 Log Analytics를 위한 여러 템플릿이 포함되어 있습니다.

* [Log Analytics VM 확장을 사용하여 Windows를 실행하는 가상 컴퓨터 배포](https://azure.microsoft.com/documentation/templates/201-oms-extension-windows-vm/)
* [Log Analytics VM 확장을 사용하여 Linux를 실행하는 가상 머신 배포](https://azure.microsoft.com/documentation/templates/201-oms-extension-ubuntu-vm/)
* [기존 Log Analytics 작업 영역을 사용하여 Azure Site Recovery 모니터링](https://azure.microsoft.com/documentation/templates/asr-oms-monitoring/)
* [기존 Log Analytics 작업 영역을 사용하여 Azure Web Apps 모니터링](https://azure.microsoft.com/documentation/templates/101-webappazure-oms-monitoring/)
* [기존 저장소 계정을 Log Analytics에 추가](https://azure.microsoft.com/resources/templates/oms-existing-storage-account/)

## <a name="next-steps"></a>다음 단계

* [Resource Manager 템플릿을 사용하여 Azure VM에 Windows 에이전트 배포](../../virtual-machines/extensions/oms-windows.md).

* [Resource Manager 템플릿을 사용하여 Azure VM에 Linux 에이전트 배포](../../virtual-machines/extensions/oms-linux.md).