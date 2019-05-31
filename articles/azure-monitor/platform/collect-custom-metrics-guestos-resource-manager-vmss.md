---
title: Windows 가상 머신 확장 집합에 대해 Azure Resource Manager 템플릿을 사용하여 Azure Monitor 메트릭 저장소에 게스트 OS 메트릭 보내기
description: Windows 가상 머신 확장 집합에 대해 Resource Manager 템플릿을 사용하여 Azure Monitor 메트릭 저장소에 게스트 OS 메트릭 보내기
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 573c205cd2e208a1cb2b526d96fb08ca21331c80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129644"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Windows 가상 머신 확장 집합에 대해 Azure Resource Manager 템플릿을 사용하여 Azure Monitor 메트릭 저장소에 게스트 OS 메트릭 보내기

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor [WAD(Microsoft Azure Diagnostics) 확장](diagnostics-extension-overview.md)을 사용하여 가상 머신, 클라우드 서비스 또는 Azure Service Fabric 클러스터의 일부로 실행되는 게스트 OS(게스트 운영 체제)에서 메트릭과 로그를 수집할 수 있습니다. 이 확장은 이전에 연결된 문서에 나열된 여러 다른 위치에 원격 분석을 보낼 수 있습니다.  

이 문서에서는 Windows 가상 머신 확장 집합에 대한 게스트 OS 성능 메트릭을 Azure Monitor 데이터 저장소에 보내는 프로세스에 대해 설명합니다. Microsoft Azure Diagnostics 버전 1.11부터 표준 플랫폼 메트릭이 이미 수집된 Azure Monitor 메트릭 저장소에 메트릭을 직접 기록할 수 있습니다. 이 위치에 메트릭을 저장하면 플랫폼 메트릭에 사용할 수 있는 것과 동일한 작업에 액세스할 수 있습니다. 작업에는 실시간에 가까운 경고, 차트 작성, 라우팅, REST API에서 액세스 등이 포함됩니다. 과거에는 Microsoft Azure Diagnostics 확장이 Azure Monitor 데이터 저장소가 아니라 Azure Storage에 기록했습니다.  

Resource Manager 템플릿을 처음 사용하는 경우 [템플릿 배포](../../azure-resource-manager/resource-group-overview.md)와 해당 구조 및 구문에 대해 알아보세요.  

## <a name="prerequisites"></a>필수 조건

- 구독이 [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)에 등록되어야 합니다. 

- [Azure PowerShell](/powershell/azure)이 설치되어 있어야 하거나, [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)을 사용할 수 있습니다. 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor를 데이터 싱크로 설정 
Azure Diagnostics 확장은 **데이터 싱크**라는 기능을 사용하여 메트릭과 로그를 다른 위치로 라우팅합니다. 다음 단계에서는 Resource Manager 템플릿과 PowerShell을 사용하여 새 Azure Monitor 데이터 싱크를 통해 VM을 배포하는 방법을 보여 줍니다. 

## <a name="author-a-resource-manager-template"></a>Resource Manager 템플릿 작성 
이 예제에서는 공개적으로 사용 가능한 [샘플 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)을 사용할 수 있습니다.  

- **Azuredeploy.json**은 가상 머신 확장 집합을 배포하기 위해 미리 구성된 Resource Manager 템플릿입니다.

- **Azuredeploy.parameters.json**은 VM에 대해 설정하려는 사용자 이름 및 암호와 같은 정보를 저장하는 매개 변수 파일입니다. 배포하는 동안 Resource Manager 템플릿에서는 이 파일에 설정된 매개 변수를 사용합니다. 

두 파일을 다운로드하고 로컬로 저장합니다. 

###  <a name="modify-azuredeployparametersjson"></a>azuredeploy.parameters.json을 수정합니다.
**azuredeploy.parameters.json** 파일을 엽니다.  
 
- 배포할 **vmSKU**를 제공합니다. Standard_D2_v3을 사용하는 것이 좋습니다. 
- 가상 머신 확장 집합에 사용하려는 **windowsOSVersion**을 지정합니다. 2016-Datacenter를 사용하는 것이 좋습니다. 
- **vmssName** 속성을 사용하여 배포할 가상 머신 확장 집합 리소스의 이름을 지정합니다. 예를 들어, **VMSS-WAD-TEST**입니다.    
- **instanceCount** 속성을 사용하여 가상 머신 확장 집합에서 실행하려는 VM 수를 지정합니다.
- 가상 머신 확장 집합에 대한 **adminUsername** 및 **adminPassword**의 값을 입력합니다. 이러한 매개 변수는 확장 집합에서 VM에 원격으로 액세스하는 데 사용됩니다. VM이 하이재킹되지 않도록 방지하려면 이 템플릿에 있는 이름은 **사용하지 마세요**. 봇이 인터넷에서 공용 Github 리포지토리의 사용자 이름 및 암호를 검사합니다. 이러한 기본값을 사용하여 VM을 테스트할 가능성이 큽니다. 


###  <a name="modify-azuredeployjson"></a>Azuredeploy.json을 수정합니다.
**azuredeploy.json** 파일을 엽니다. 

Resource Manager 템플릿에서 저장소 계정 정보를 보유하는 변수를 추가합니다. 진단 구성 파일에 지정된 모든 로그 또는 성능 카운터는 Azure Monitor 메트릭 저장소와 여기서 지정한 저장소 계정 둘 다에 기록됩니다. 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
리소스 섹션에서 가상 머신 확장 집합 정의를 찾아 구성에 **identity** 섹션을 추가합니다. 이 섹션을 추가하면 Azure에서 시스템 ID를 할당합니다. 이 단계를 수행하면 확장 집합의 VM이 자체 게스트 메트릭을 Azure Monitor로 내보낼 수도 있습니다.  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

가상 머신 확장 집합 리소스에서 **virtualMachineProfile** 섹션을 찾습니다. **extensionsProfile**이라는 새 프로필을 추가하여 확장을 관리합니다.  


**VMSS WAD 확장 섹션**에 표시된 대로 **extensionProfile**에서 템플릿에 새 확장을 추가합니다.  이 섹션은 내보내는 메트릭을 Azure Monitor에서 수용했는지 확인하는 Azure 리소스 확장의 관리 ID입니다. **이름** 필드에는 어떤 이름이든지 포함될 수 있습니다. 

또한 MSI 확장의 다음 코드는 진단 확장 및 구성을 가상 머신 확장 집합 리소스에 확장 리소스로 추가합니다. 필요에 따라 성능 카운터를 자유롭게 추가 또는 제거합니다. 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


저장소 계정에 대한 **dependsOn**을 추가하여 올바른 순서로 생성되도록 합니다. 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

템플릿에서 저장소 계정을 아직 만들지 않은 경우 만듭니다. 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

두 파일을 모두 저장하고 닫습니다. 

## <a name="deploy-the-resource-manager-template"></a>Resource Manager 템플릿 배포 

> [!NOTE]  
> Azure Diagnostics 확장 버전 1.5 이상을 실행**하고** Resource Manager 템플릿에서 **autoUpgradeMinorVersion:** 속성을 **true**로 설정해야 합니다. 그러면 Azure에서 VM을 시작할 때 적절한 확장을 로드합니다. 템플릿에 이러한 설정이 없는 경우 해당 설정을 변경하고 템플릿을 다시 배포합니다. 


Resource Manager 템플릿을 배포하려면 Azure PowerShell을 사용합니다.  

1. PowerShell을 시작합니다. 
1. `Login-AzAccount`를 사용하여 Azure에 로그인합니다.
1. `Get-AzSubscription`을 사용하여 구독 목록을 가져옵니다.
1. 만들려는 구독을 설정하거나 가상 머신을 업데이트합니다. 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. 배포 중인 VM에 대한 새 리소스 그룹을 만듭니다. 다음 명령 실행: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > 사용자 지정 메트릭을 사용할 수 있는 Azure 지역을 사용해야 합니다. [사용자 지정 메트릭을 사용할 수 있는 Azure 지역](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions)을 사용해야 합니다.
 
1. 다음 명령을 실행하여 VM을 배포합니다.  

   > [!NOTE]  
   > 기존 확장 집합을 업데이트하려면 명령의 끝에 **-Mode Incremental**을 추가합니다. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. 배포에 성공하면 Azure Portal에서 가상 머신 확장 집합을 확인할 수 있습니다. 메트릭을 Azure Monitor로 내보내야 합니다. 

   > [!NOTE]  
   > 선택한 **vmSkuSize** 관련 오류가 발생할 수 있습니다. 이 경우 **azuredeploy.json** 파일로 돌아가서 **vmSkuSize** 매개 변수의 기본값을 업데이트합니다. **Standard_DS1_v2**를 사용해 보시기 바랍니다. 


## <a name="chart-your-metrics"></a>메트릭 차트 작성 

1. Azure 포털에 로그인합니다. 

1. 왼쪽 메뉴에서 **모니터**를 선택합니다. 

1. **모니터** 페이지에서 **메트릭**을 선택합니다. 

   ![모니터 - 메트릭 페이지](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. 집계 기간을 **지난 30분**으로 변경합니다.  

1. 리소스 드롭다운 메뉴에서 직접 만든 가상 머신 확장 집합을 선택합니다.  

1. 네임스페이스 드롭다운 메뉴에서 **azure.vm.windows.guest**를 선택합니다. 

1. 메트릭 드롭다운 메뉴에서 **메모리\%사용 중인 커밋된 바이트**를 선택합니다.  

그런 다음, 이 메트릭의 차원을 사용하여 특정 VM에 대한 차트를 작성하거나 확장 집합의 각 VM을 그림으로 나타낼 수도 있습니다. 



## <a name="next-steps"></a>다음 단계
- [사용자 지정 메트릭](metrics-custom-overview.md)에 대해 자세히 알아보세요.


