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
ms.date: 02/21/2019
ms.author: magoedte
ms.openlocfilehash: c59e0e7cd846f77082b7131aaec15e4945a18191
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110157"
---
# <a name="manage-log-analytics-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용한 Log Analytics 관리

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Azure Resource Manager 템플릿](../../azure-resource-manager/resource-group-authoring-templates.md)을 사용하여 Log Analytics 작업 영역 만들고 구성할 수 있습니다. 템플릿을 사용하여 수행할 수 있는 작업의 예:

* 설정 가격 책정 계층을 포함하는 작업 영역 만들기 
* 솔루션 추가
* 저장된 검색 만들기
* 컴퓨터 그룹 만들기
* Windows 에이전트가 설치된 컴퓨터에서 IIS 로그 수집 활성화
* Linux 및 Windows 컴퓨터에서 성능 카운터 수집
* Linux 컴퓨터의 syslog에서 이벤트 수집 
* Windows 이벤트 로그에서 이벤트 수집
* Azure 가상 머신에 Log Analytics 에이전트 추가
* Azure 진단을 사용하여 수집된 데이터를 인덱싱하도록 Log Analytics 구성

이 문서에서는 템플릿에서 수행할 수 있는 몇 가지 구성을 보여 주는 템플릿 샘플을 제공합니다.

## <a name="api-versions"></a>API 버전
다음 표에는 이 예제에서 사용된 리소스의 API 버전이 제공됩니다.

| 리소스 | 리소스 형식 | API 버전 |
|:---|:---|:---|
| 작업 영역   | 작업 영역    | 2017-03-15-preview |
| 검색      | savedSearches | 2015-03-20 |
| 데이터 원본 | datasources   | 2015-11-01-preview |
| 솔루션    | 솔루션     | 2015-11-01-preview |

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기
다음 예제에서는 로컬 컴퓨터에서 템플릿을 사용하여 작업 영역을 만듭니다. JSON 템플릿은 작업 영역의 이름만 사용자에게 입력을 요청하도록 구성되며, 환경에서 표준 구성으로 사용될수 있는 다른 매개 변수에 대해서는 기본값을 지정합니다.  

다음 매개 변수는 기본값을 설정합니다.

* 위치 - 기본값은 미국 동부
* SKU - 2018년 4월 가격 책정 모델에서 배포된 새로운 GB당 가격 책정 계층이 기본값

> [!NOTE]
>새 2018년 4월 가격 책정 모델을 선택한 구독에서 Log Analytics 작업 영역을 만들거나 구성할 때 유효한 유일한 Log Analytics 가격 책정 계층은 **PerGB2018**입니다.  
>일부 구독이 [2018년 4월 이전 가격 책정 모델](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs#new-pricing-model)인 경우 **독립 실행형** 가격 책정 계층을 지정할 수 있으며, 이 방법은 2018년 4월 이전 가격 책정 모델의 구독과 새 가격 책정의 구독 둘 다에서 성공합니다. 새 가격 책정 모델을 도입한 구독의 작업 영역에서는 가격 책정 계층이 **PerGB2018**로 설정됩니다. 

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
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
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
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```
2. 요구 사항을 충족하도록 템플릿을 편집합니다.  지원되는 속성 및 값은 [Microsoft.OperationalInsights/workspaces 템플릿](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) 참조를 검토하세요. 
3. 이 파일을 로컬 폴더에 **deploylaworkspacetemplate.json**으로 저장합니다.
4. 이제 이 템플릿을 배포할 수 있습니다. PowerShell 또는 명령줄을 사용하여 작업 영역을 만듭니다.

   * PowerShell의 경우 템플릿이 포함된 폴더에서 다음 명령을 사용합니다.
   
        ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
        ```

   * 명령줄의 경우 템플릿이 포함된 폴더에서 다음 명령을 사용합니다.

        ```cmd
        azure config mode arm
        azure group deployment create <my-resource-group> <my-deployment-name> --TemplateFile deploylaworkspacetemplate.json
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
9. Azure 진단을 통해 저장소 계정에 기록한 Windows 이벤트 로그 및 IIS 로그 수집

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "workspaceName"
      }
    },
    "serviceTier": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Standalone",
        "PerNode",
        "PerGB2018"
      ],
      "metadata": {
        "description": "Service Tier: Free, Standalone, PerNode, or PerGB2018"
    }
      },
    "dataRetention": {
      "type": "int",
      "defaultValue": 30,
      "minValue": 7,
      "maxValue": 730,
      "metadata": {
        "description": "Number of days of retention. Free plans can only have 7 days, Standalone and Log Analytics plans include 30 days for free"
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "East US",
        "West Europe",
        "Southeast Asia",
        "Australia Southeast"
      ]
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
          "Name": "[parameters('serviceTier')]"
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

#### <a name="command-line"></a>명령줄
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

