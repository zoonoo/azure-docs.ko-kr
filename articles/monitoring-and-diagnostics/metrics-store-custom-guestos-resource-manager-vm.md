---
title: Windows Virtual Machine용 Resource Manager 템플릿을 사용하여 Azure Monitor 메트릭 저장소에 게스트 OS 메트릭 보내기
description: Windows Virtual Machine용 Resource Manager 템플릿을 사용하여 Azure Monitor 메트릭 저장소에 게스트 OS 메트릭 보내기
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 4ed911766a14dd35ea662326a5d50df11cf81698
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984077"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Windows Virtual Machine용 Resource Manager 템플릿을 사용하여 Azure Monitor 메트릭 저장소에 게스트 OS 메트릭 보내기

Azure Monitor [MAD(Microsoft Azure 진단) 확장](azure-diagnostics.md)을 사용하면 Virtual Machine, Cloud Service 또는 Service Fabric 클러스터의 일부로 실행되는 게스트 OS(게스트 운영 체제)에서 메트릭과 로그를 수집할 수 있습니다.  이 확장은 이전에 연결된 문서에 나열된 여러 다른 위치에 원격 분석을 보낼 수 있습니다.  

이 문서에서는 Windows Virtual Machine에 대한 게스트 OS 성능 메트릭을 Azure Monitor 데이터 저장소에 보내는 프로세스에 대해 설명합니다. MAD 버전 1.11부터 표준 플랫폼 메트릭이 이미 수집된 Azure Monitor 메트릭 저장소에 메트릭을 직접 기록할 수 있습니다. 이 위치에 저장하면 플랫폼 메트릭에 사용할 수 있는 동일한 작업에 액세스할 수 있습니다.  작업에는 실시간에 가까운 경고, 차트 작성, 라우팅, REST API에서 액세스 등이 포함됩니다.  과거에는 MAD 확장에서 Azure Monitor 데이터 저장소가 아니라 Azure 저장소에 기록했습니다.   

Resource Manager 템플릿을 처음 사용하는 경우 [템플릿 배포](../azure-resource-manager/resource-group-overview.md)와 해당 구조 및 구문에 대해 알아보세요.  

## <a name="pre-requisites"></a>필수 조건

- 구독은 [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1)에 등록해야 합니다. 

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1)이 설치되어 있어야 하거나 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview.md)을 사용할 수 있습니다. 

 
## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor를 데이터 싱크로 설정 
Azure 진단 확장은 "데이터 싱크"라는 기능을 사용하여 메트릭과 로그를 다른 위치로 라우팅합니다.  다음 단계에서는 Resource Manager 템플릿과 PowerShell을 사용하여 새 "Azure Monitor" 데이터 싱크를 통해 VM을 배포하는 방법을 보여 줍니다. 

## <a name="author-resource-manager-template"></a>Resource Manager 템플릿 작성 
이 예제에서는 공개적으로 사용할 수 있는 템플릿 샘플을 사용할 수 있습니다. 시작 템플릿은 https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows에 있습니다. 

- **Azuredeploy.json**은 Virtual Machine을 배포하도록 미리 구성된 Resource Manager 템플릿입니다. 

- **Azuredeploy.parameters.json**은 사용자의 VM에 대해 설정하려는 사용자 이름 및 암호와 같은 정보를 저장하는 매개 변수 파일입니다. Resource Manager 템플릿은 배포하는 동안 이 파일에 설정된 매개 변수를 사용합니다. 

두 파일은 모두 로컬로 다운로드하고 저장합니다. 

###  <a name="modify-azuredeployparametersjson"></a>azuredeploy.parameters.json 수정
*azuredeploy.parameters.json* 파일을 엽니다. 

1. VM에 대해 *adminUsername* 및 *adminPassword*에 대한 값을 입력합니다. 이러한 매개 변수는 VM에 대한 원격 액세스에 사용됩니다. VM이 하이재킹되지 않도록 방지하려면 이 템플릿의 매개 변수를 사용하지 마세요. 봇은 인터넷에서 공용 Github 리포지토리에 있는 사용자 이름과 암호를 검색하며, 이러한 기본값을 사용하여 VM을 테스트할 수도 있습니다.  

1. VM에 대한 고유한 dnsname을 만듭니다.  

### <a name="modify-azuredeployjson"></a>azuredeploy.json 수정

*azuredeploy.json* 파일을 엽니다. 

템플릿의 **variables** 섹션에 있는 **storageAccountName** 항목 뒤에 저장소 계정 ID를 추가합니다.  

```json
// Find these lines 
"variables": { 
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]", 

// Add this line directly below.  
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]", 
```

템플릿의 "resources" 섹션 위쪽에 MSI(관리 서비스 ID) 확장을 추가합니다.  이 확장을 통해 Azure Monitor에서 내보내는 메트릭을 허용합니다.  

```json
//Find this code 
"resources": [
// Add this code directly below
    { 
        "type": "Microsoft.Compute/virtualMachines/extensions", 
        "name": "WADExtensionSetup", 
        "apiVersion": "2015-05-01-preview", 
        "location": "[resourceGroup().location]", 
        "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ], 
        "properties": { 
            "publisher": "Microsoft.ManagedIdentity", 
            "type": "ManagedIdentityExtensionForWindows", 
            "typeHandlerVersion": "1.0", 
            "autoUpgradeMinorVersion": true, 
            "settings": { 
                "port": 50342 
            } 
        } 
    }, 
```

Azure에서 MSI 확장에 시스템 ID를 할당하도록 VM 리소스에 "identity" 구성을 추가합니다. 이 단계는 VM에서 자체에 대한 게스트 메트릭을 Azure Monitor로 내보낼 수 있도록 합니다. 

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{ 
    "apiVersion": "2017-03-30", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[variables('vmName')]", 
    "location": "[resourceGroup().location]", 
    // add these 3 lines below
    "identity": {  
    "type": "SystemAssigned" 
    }, 
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {   
    ...
```

다음 구성을 추가하여 Windows Virtual Machine에서 진단 확장을 사용하도록 설정합니다.  간단한 Resource Manager 기반 Virtual Machine의 경우 Virtual Machine에 대한 resources 배열에 확장 구성을 추가할 수 있습니다. "sinks": "AzMonSink" 줄 및 해당 섹션의 뒷부분에 있는 해당 "SinksConfig"를 사용하면 확장에서 Azure Monitor에 메트릭을 직접 내보낼 수 있습니다. 필요에 따라 성능 카운터를 자유롭게 추가/제거합니다.  


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": { 
    "bootDiagnostics": { 
    "enabled": true, 
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]" 
    } 
} 
}, 
//Start of section to add 
"resources": [        
{ 
            "type": "extensions", 
            "name": "Microsoft.Insights.VMDiagnosticsSettings", 
            "apiVersion": "2015-05-01-preview", 
            "location": "[resourceGroup().location]", 
            "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" 
            ], 
            "properties": { 
            "publisher": "Microsoft.Azure.Diagnostics", 
            "type": "IaaSDiagnostics", 
            "typeHandlerVersion": "1.4", 
            "autoUpgradeMinorVersion": true, 
            "settings": { 
                "WadCfg": { 
                "DiagnosticMonitorConfiguration": { 
    "overallQuotaInMB": 4096, 
    "DiagnosticInfrastructureLogs": { 
                    "scheduledTransferLogLevelFilter": "Error" 
        }, 
                    "Directories": { 
                    "scheduledTransferPeriod": "PT1M", 
    "IISLogs": { 
                        "containerName": "wad-iis-logfiles" 
                    }, 
                    "FailedRequestLogs": { 
                        "containerName": "wad-failedrequestlogs" 
                    } 
                    }, 
                    "PerformanceCounters": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "sinks": "AzMonSink", 
                    "PerformanceCounterConfiguration": [ 
                        { 
                        "counterSpecifier": "\\Memory\\Available Bytes", 
                        "sampleRate": "PT15S" 
                        }, 
                        { 
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                        "sampleRate": "PT15S" 
                        }, 
                        { 
                        "counterSpecifier": "\\Memory\\Committed Bytes", 
                        "sampleRate": "PT15S" 
                        } 
                    ] 
                    }, 
                    "WindowsEventLog": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "DataSource": [ 
                        { 
                        "name": "Application!*" 
                        } 
                    ] 
                    }, 
                    "Logs": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "scheduledTransferLogLevelFilter": "Error" 
                    } 
                }, 
                "SinksConfig": { 
                    "Sink": [ 
                    { 
                        "name" : "AzMonSink", 
                        "AzureMonitor" : {} 
                    } 
                    ] 
                } 
                }, 
                "StorageAccount": "[variables('storageAccountName')]" 
            }, 
            "protectedSettings": { 
                "storageAccountName": "[variables('storageAccountName')]", 
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]", 
                "storageAccountEndPoint": "https://core.windows.net/" 
            } 
            } 
        } 
        ] 
//End of section to add 
```


두 파일을 모두 저장하고 닫습니다. 
 

## <a name="deploy-the-resource-manager-template"></a>Resource Manager 템플릿 배포 

> [!NOTE]
> Azure 진단 확장 버전 1.5 이상을 실행하고 Resource Manager 템플릿에서 "autoUpgradeMinorVersion": 속성을 'true'로 설정해야 합니다.  그러면 VM이 시작될 때 Azure에서 적절한 확장을 로드합니다. 템플릿에 이러한 설정이 없는 경우 해당 설정을 변경하고 템플릿을 다시 배포합니다. 


Resource Manager 템플릿을 배포하기 위해 Azure PowerShell을 활용하겠습니다.  

1. PowerShell 시작 
1. `Login-AzureRmAccount`를 사용하여 Azure에 로그인합니다.
1. `Get-AzureRmSubscription`을 사용하여 구독 목록을 가져옵니다.
1. 가상 머신을 만들거나 업데이트할 구독을 설정합니다. 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. 배포할 VM에 대한 새 리소스 그룹을 만들고 아래 명령을 실행합니다. 

   ```PowerShell
    New-AzureRmResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>" 
   ```
   > [!NOTE] 
   > [사용자 지정 메트릭을 사용하도록 설정된 Azure 지역을 사용](metrics-custom-overview.md)해야 합니다. 
 
1. 다음 명령을 실행하여 다음 명령을 사용하여 VM을 배포합니다.  
   > [!NOTE] 
   > 기존 VM을 업데이트하려면 다음 명령의 끝에 *-Mode Incremental*을 추가하기만 하면 됩니다. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>" 
   ```
  
1. 배포가 완료되면 Azure Portal에서 해당 VM을 찾을 수 있어야 하며 메트릭을 Azure Monitor에 내보내야 합니다. 

   > [!NOTE] 
   > 선택한 vmSkuSize 관련 오류가 발생할 수 있습니다. 그렇다면 azuredeploy.json 파일로 돌아가서 vmSkuSize 매개 변수의 기본값을 업데이트하세요. 이 경우 "Standard_DS1_v2"를 사용하는 것이 좋습니다. 

## <a name="chart-your-metrics"></a>메트릭 차트 작성 

1. Azure Portal에 로그인합니다. 

1. 왼쪽 메뉴에서 **모니터**를 클릭합니다. 

1. [모니터] 페이지에서 **메트릭**을 클릭합니다. 

   ![메트릭 페이지](./media/metrics-store-custom-rest-api/metrics.png) 

1. 집계 기간을 **지난 30분**으로 변경합니다.  

1. 리소스 드롭다운에서 방금 만든 VM을 선택합니다. 템플릿의 이름을 변경하지 않은 경우 *SimpleWinVM2*여야 합니다.  

1. 네임스페이스 드롭다운에서 **azure.vm.windows.guest**를 선택합니다. 

1. 메트릭 드롭다운에서 **Memory\%Committed Bytes in Use**를 선택합니다.  
 

## <a name="next-steps"></a>다음 단계
- [사용자 지정 메트릭](metrics-custom-overview.md)에 대해 자세히 알아봅니다.