<properties
   pageTitle="WAD 및 Operational Insights에서의 로그 수집 방법 | Microsoft Azure"
   description="이 문서는 Azure를 실행 중인 서비스 패브릭에서 로그를 수집하도록 Windows Azure Diagnostics 및 Operational Insights를 설정하는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2015"
   ms.author="kunalds"/>


# WAD(Windows Azure Diagnostics) 및 Operational Insights를 사용하여 Azure의 서비스 패브릭 클러스터에서 로그 수집

Azure에서 서비스 패브릭 클러스터를 실행할 때 모든 노드의 로그를 중앙 위치에 수집하려 합니다. 중앙 위치에 로그를 두면 클러스터나, 해당 클러스터에서 실행 중인 응용 프로그램 및 서비스에서 문제를 간편하게 분석하고 해결할 수 있습니다. 로그를 업로드 및 수집하는 방법 중 하나는 로그를 Azure 테이블 저장소에 업로드하는 WAD(Windows Azure Diagnostics) 확장을 사용하는 것입니다. Operational Insights(Microsoft Operations Management Suite의 일원)는 간편한 로그 분석과 검색을 제공하는 SaaS 솔루션입니다. 아래 단계에서는 클러스터의 VM에서 WAD를 설정하여 로그를 중앙 저장소에 업로드한 뒤 Operational Insights 포털에서 볼 수 있게 Operational Insights가 로그를 가져오도록 구성하는 방법을 설정합니다. Operational Insights는 서비스 패브릭 클러스터로부터 업로드된 다양한 형식의 로그를 저장된 Azure 저장소 테이블의 이름으로 식별하므로, Operational Insights가 검색하는 이름과 일치하는 이름으로 Azure 저장소 테이블에 로그를 업로드하도록 WA를 구성해야 합니다. 이 문서의 구성 설정 예에서는 필요한 저장소 테이블 이름을 보여줍니다.

## 참고 자료
* [Windows Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)(클라우드 서비스와 관련이 있지만 여러 좋은 정보와 예 제공)
* [Operational Insights](https://azure.microsoft.com/services/operational-insights/)
* [Azure 리소스 관리자](https://azure.microsoft.com/documentation/articles/resource-group-overview/)

## 필수 조건
이 문서의 일부 작업을 수행하는 데 [Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) * [ARM 클라이언트](https://github.com/projectkudu/ARMClient) 등의 도구가 사용됩니다.

## 수집하려는 다양한 로그 원본
1. 서비스 패브릭 로그: 플랫폼에서 표준 ETW와 EventSource 채널로 내보냅니다. 다음과 같은 형식 중 하나가 될 수 있습니다.
  - 작업 이벤트: 서비스 패브릭 플랫폼이 수행한 작업에 대한 로그입니다. 응용 프로그램 및 서비스 만들기, 노드 상태 변경, 업그레이드 정보 등을 예로 들 수 있습니다.
  - [행위자 프로그래밍 모델 이벤트](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-actors-diagnostics/)
  - [Reliable Services 프로그래밍 모델 이벤트](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-services-diagnostics/)
2. 응용 프로그램 이벤트: 서비스 코드에서 발생하며 Visual Studio에서 제공하는 EventSource 도우미 클래스를 사용하여 작성된 이벤트입니다. 응용 프로그램에서의 로그 작성 방법에 대한 자세한 내용은 이 [문서](https://azure.microsoft.com/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/)를 참조하세요.


## WAD(Windows Azure Diagnostics)를 서비스 패브릭 클러스터에 배포하여 로그 수집 및 업로드
로그를 수집하는 첫 단계는 서비스 패브릭 클러스터의 각 VM에 WAD 확장을 배포하는 것입니다. WAD는 각 VM에서 로그를 수집하여 사용자가 지정한 저장소 계정에 업로드합니다. 포털 또는 ARM의 사용 여부와, 배포를 클러스터 만들기의 일환으로 수행 중인지 또는 이미 존재하는 클러스터에 대한 것인지에 따라 단계에 약간 차이가 있습니다. 각 시나리오에 대한 단계를 살펴보겠습니다.

### 포털을 통해 클러스터 만들기의 일환으로 WAD 배포
클러스터 만들기의 일환으로 VM에 WAD를 배포하려면 아래 이미지에 표시된 진단 설정을 사용합니다. 이 값은 기본적으로 ON입니다. ![클러스터 만들기를 위한 포털의 WAD 설정](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### ARM을 통해 클러스터 만들기의 일환으로 WAD 배포
ARM을 사용하여 클러스터를 만들려면 클러스터를 만들기 전에 WAD 구성 JSON을 전체 클러스터 ARM에 추가해야 합니다. ARM 템플릿 샘플의 일부로 WAD 구성이 추가된 샘플 5 VM 클러스터 ARM 템플릿을 제공합니다. Azure 샘플 갤러리 [WAD ARM 템플릿 샘플이 있는 5개 노드 클러스터](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad)에서 확인할 수 있습니다.

ARM 템플릿에서 WAD 설정을 확인할면 "WadCfg"를 검색합니다. 이 템플릿이 있는 클러스터를 만들려면 위의 링크에서 제공하는 “Azure에 배포” 버튼을 누르면 됩니다. 또는 ARM 샘플을 다운로드하고, Azure Powershell 창에서 `New-AzureResourceGroupDeployment` 명령을 사용하여 변경하고 수정된 템플릿으로 클러스터를 만들 수 있습니다. 명령에 전달해야 하는 매개 변수는 아래를 참조하세요. 추가적으로 이 배포 명령을 호출하기 전에 Azure 계정 추가(`Add-AzureAccount`), 구독 선택(`Select-AzureSubscription`), ARM 모드로 전환(`Switch-AzureMode AzureResourceManager`) 및 아직 만들지 않은 경우 리소스 그룹 만들기(`New-AzureResourceGroup`) 등을 포함한 몇 가지 설정 작업이 필요할 수 있습니다.

```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>기존 클러스터에 WAD 배포
배포된 WAD가 없는 기존 클러스터인 경우 다음 단계를 통해 WAD를 추가할 수 있습니다. 아래 JSON으로 WadConfigUpdate.json 및 WadConfigUpdateParams.json 등의 두 파일을 만듭니다.

##### WadConfigUpdate.json
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",

  "parameters": {
        "vmNamePrefix": {
      "type": "string"
    },
        "applicationDiagnosticsStorageAccountName": {
      "type": "string"
    },
        "vmCount": {
            "type": "int"
    }
  },

  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(parameters('vmNamePrefix'),copyIndex(0),'/Microsoft.Insights.VMDiagnosticsSettings')]",
            "location": "[resourceGroup().location]",
      "properties": {
        "type": "IaaSDiagnostics",
                "typeHandlerVersion": "1.5",
        "publisher": "Microsoft.Azure.Diagnostics",
                "autoUpgradeMinorVersion": true,
        "settings": {
                    "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                "EtwProviders": {
                    "EtwEventSourceProviderConfiguration": [
                        {
                            "provider": "Microsoft-ServiceFabric-Actors",
                            "scheduledTransferKeywordFilter": "1",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableActorEventTable" }
                        },
                        {
                            "provider": "Microsoft-ServiceFabric-Services",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableServiceEventTable" },
                                        "scheduledTransferPeriod": "PT5M"
                        }
                    ],
                    "EtwManifestProviderConfiguration": [
                        {
                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                        "scheduledTransferLogLevelFilter": "Information",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricSystemEventTable" }
                        }
                    ]
                }
            }
    },
                    "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountNamee')]"
                },
                "protectedSettings": {
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountNamee')]",
                    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                    "storageAccountEndPoint": "https://core.windows.net/"
                }
            },
            "copy": {
                "name": "vmExtensionLoop",
                "count": "[parameters('vmCount')]"
            }
        }
    ],

    "outputs": {
    }
}
```

##### WadConfigUpdateParams.json
클러스터를 만드는 동안 VM에 선택한 접두사로 vmNamePrefix를 대체하고, vmStorageAccountName이 VM으로부터 로그를 업로드하려는 저장소 계정이 되게 편집합니다. ```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmNamePrefix": {
            "value": "VM"
        },
        "applicationDiagnosticsStorageAccountName": {
            "value": "testdiagacc"
        },
        "vmCount": {
            "value": 5
        }
    }
}
``` 앞에서처럼 Json 파일을 만들고 사용자 환경에 특정하게 변경한 후 이 명령을 호출하여 서비스 패브릭 클러스터에 대한 리소스 그룹의 이름을 전달합니다. 이 명령이 성공적으로 실행되면 WAD가 모든 VM에 배포되며 클러스터로부터 지정된 Azure 저장소 계정의 테이블로 로그를 업로드하기 시작합니다. 추가적으로 이 배포 명령을 호출하기 전에 Azure 계정 추가(`Add-AzureAccount`), 올바른 구독 선택(`Select-AzureSubscription`) 및ARM 모드로 전환(`Switch-AzureMode AzureResourceManager`) 등을 포함한 몇 가지 설정 작업이 필요할 수 있습니다.```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## 클러스터 로그를 보고 검색하기 위한 Operational Insights 설정
WAD가 클러스터에서 설정되고 로그를 저장소 계정에 업로드하게 되면 이제 다음으로 Operational Insights의 포털 UI를 통해 모든 클러스터를 확인, 검색 및 쿼리할 수 있게 Operational Insights를 설정합니다.

### Operational Insights 작업 영역 만들기
Operational Insights 작업 영역을 만드는 단계를 확인하려면 아래 문서를 참조하세요. 작업 공간을 만드는 두 가지 다른 방법을 설명하므로 접근 방법에 따라 Azure 포털 및 구독을 선택합니다. 이 문서의 뒷부분에서는 Operational Insights 작업 영역의 이름이 필요합니다. 저장소 계정을 포함하여 모든 클러스터 리소르를 만드는 데 사용한 것과 동일한 구독을 사용하여 Operational Insights 작업 공간을 만듭니다.

[Operational Insights 탑재](https://technet.microsoft.com/library/mt484118.aspx)

### 클러스터 로그를 표시하도록 Operational Insights 작업 영역 구성
앞에서 설명한 것처럼 Operational Insights 작업 영역을 만든 후에는 WAD를 통해 클러스터로부터 업로드된 Azure 테이블에서 로그를 가져오도록 작업 영역을 구성합니다. 현재 이 구성은 Operational Insights 포털에서 제공되지 않으며 Powershell 명령을 통해서만 가능합니다. 이 PS 스크립트를 실행 합니다. ```powershell <# 이 스크립트는 Operations Management Suite 작업 영역(즉 Operational Insights 작업 영역)이 Azure 저장소 계정에서 Windows Azure Diagnostics을 읽도록 구성합니다.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and ARM storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
#>
Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager

$validTables = "WADServiceFabric*EventTable", "WADETWEventTable"

function Select-Workspace {

    $workspace = ""

    $allWorkspaces = Get-AzureOperationalInsightsWorkspace  

    switch ($allWorkspaces.Count) {
        0 {Write-Error "No Operations Management Suite workspaces found"}
        1 {return $allWorkspaces}
        default {
            $uiPrompt = "Enter the number corresponding to the workspace you want to configure.`n"

            $count = 1
            foreach ($workspace in $allWorkspaces) {
                $uiPrompt += "$count. " + $workspace.Name + " (" + $workspace.CustomerId + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt) - 1
            $workspace = $allWorkspaces[$answer]
        }  
    }
    return $workspace
}

function Select-StorageAccount {

    $storage = ""

    $allStorageAccounts = (get-azureresource -ResourceType Microsoft.ClassicStorage/storageAccounts) + (get-azureresource -ResourceType Microsoft.Storage/storageAccounts)

    switch ($allStorageAccounts.Count) {
        0 {Write-Error "No storage accounts found"}
        1 {$storage = $allStorageAccounts}
        default {

            $uiPrompt = "Enter the number corresponding to the storage account with diagnostics.`n"

            $count = 1
            foreach ($storageAccount in $allStorageAccounts) {
                $uiPrompt += "$count. " + $storageAccount.Name + " (" + $storageAccount.Location + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt)

            $storage = $allStorageAccounts[$answer - 1]
        }
    }
    return $storage
}

$workspace = Select-Workspace $storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try { $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop } catch [Hyak.Common.CloudException] { # HTTP Not Found is returned if the storage insight doesn't exist }

if ($existingConfig) { Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else { if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") { Switch-AzureMode -Name AzureServiceManagement $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary Switch-AzureMode -Name AzureResourceManager } else { $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1 } New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers } ``` Operational Insights 작업 영역이 스토리지 계정의 Azure 테이블로부터 읽도록 구성한 후에는 Azure 포털에 로그인하여 저장소 탭에서 Operational Insights 리소스를 찾아야 합니다. 다음과 유사하게 표시됩니다.![Azure 포털에서 Operational Insights 저장소 구성](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Operational Insights에서 로그 검색 및 보기
Operational Insights 작업 영역이 특정 저장소 계정에서 로그를 읽도록 구성한 후 로그가 Operational Insights UI에 표시되는 데 최대 10분 정도 소요될 수 있습니다. 새 로그가 생성될 수 있게 서비스 패브릭 응용 프로그램을 클러스터에 배포해야 합니다. 그래야 서비스 플랫폼으로부터 운영 이벤트가 생성됩니다.

1. 로그를 보려면 Operational Insights 포털의 기본 페이지에서 LogSearch를 선택합니다. ![Operational Insights 로그 검색 옵션](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. 로그 검색 페이지에서 "Type=ServiceFabricOperationalEvent" 쿼리를 사용합니다. 클러스터에서 아래와 같이 운영 로그가 표시될 것입니다. 모든 행위자 프로그래밍 모델 로그를 보려면 "Type=ServiceFabricReliableActorEvent”에 대해 쿼리하고, Reliable Services프로그래밍 모델의 모든 로그를 보려면 "Type=ServiceFabricReliableServiceEvent"를 입력합니다.![Operational Insights 로그 쿼리 및 보기](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### 문제 해결에 도움이 되는 샘플 쿼리
몇 가지 시나리오의 예와 문제 해결에 사용 가능한 쿼리는 다음과 같습니다.

1. 서비스 패브릭에서 특정 서비스에 대해 RunAsync가 호출되었는지 확인합니다. 서비스를 시작하는 중에 충돌 여부를 배제하려는 경우 이 작업이 필요할 수 있습니다. 이를 위해, 아래의 쿼리를 배포한 서비스 및 응용 프로그램 이름에 맞게 대체하여 검색하고 결과가 반환되는지 확인합니다.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. 상태 저장 서비스를 실행 중이며 서비스 패브릭에서 오류로 표시한 예외가 발생했는지 파악하고자 할 경우 이와 유사한 쿼리로 해당 이벤트를 확인합니다.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. 모든 배포된 응용 프로그램과 서비스에서 행위자 메서드가 생성한 모든 예외에 해당하는 이벤트를 찾으려면 이와 유사한 쿼리를 사용할 수 있습니다.

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## 새 EventSource 채널에서 로그를 수집 및 업로드하도록 WAD 업데이트
배포하려는 새 응용 프로그램을 나타내는 새 EventSource 채널에서 로그를 수집하도록 WAD를 업데이트하려면 기존 클러스터에 대한 WAD 설정을 설명하는 [위 섹션](#deploywadarm)에서와 동일한 단계를 수행하면 됩니다. ARM 명령을 통해 구성 업데이트를 적용하려면 먼저 새 EventSources에 대해 항목을 추가하도록 WadConfigUpdate.json의 EtwEventSourceProviderConfiguration 섹션을 업데이트해야 합니다. Operational Insights가 응용 프로그램 ETW 이벤트를 읽어오도록 구성된 테이블이므로 업로드를 위한 테이블은 동일합니다(ETWEventTable).


## 다음 단계
문제를 해결하는 동안 조사해야 하는 이벤트에 대한 자세한 내용을 확인하려면 [신뢰할 수 있는 행위자](service-fabric-reliable-actors-diagnostics.md) 및 [Reliable Services](service-fabric-reliable-services-diagnostics.md)가 내보낸 진단 이벤트를 확인합니다.

<!---HONumber=Nov15_HO4-->