<properties
   pageTitle="Azure 진단 및 Azure Operational Insights에서 로그 수집 방법 | Microsoft Azure"
   description="이 문서는 Azure에서 실행 중인 서비스 패브릭에서 로그를 수집하도록 Azure 진단 및 Operational Insights를 설정하는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="toddabel"/>


# Azure 진단 및 Operational Insights를 사용하여 서비스 패브릭 클러스터에서 로그 수집

Azure 서비스 패브릭 클러스터를 실행할 때 모든 노드의 로그를 중앙 위치에 수집하는 것이 좋습니다. 중앙 위치에 로그를 두면 클러스터나 해당 클러스터에서 실행 중인 응용 프로그램 및 서비스의 문제를 간편하게 분석하고 해결할 수 있습니다. 로그를 업로드 및 수집하는 방법 중 하나는 로그를 Azure 저장소에 업로드하는 Azure 진단 확장을 사용하는 것입니다.

Azure [Operational Insights](https://azure.microsoft.com/services/operational-insights/)(Microsoft Operations Management Suite의 일부)는 간편하게 로그를 분석하고 검색할 수 있는 SaaS 솔루션입니다. 아래는 클러스터의 VM에 Azure 진단 확장을 설정하여 로그를 중앙 저장소에 업로드한 뒤 Operational Insights 포털에서 로그를 볼 수 있게 Operational Insights가 로그를 가져오도록 구성하는 단계입니다.

Operational Insights는 서비스 패브릭 클러스터에서 업로드되는 다양한 유형의 로그를 로그가 저장된 저장소 테이블의 이름으로 식별합니다. 따라서 Operational Insights가 검색하는 이름과 일치하는 이름으로 저장소 테이블에 로그를 업로드하도록 Azure 진단 확장을 구성해야 합니다. 이 문서의 구성 설정 예에서는 필요한 저장소 테이블 이름을 보여줍니다.


## 필수 조건
이러한 도구는 이 문서의 일부 작업을 수행하는 데 사용됩니다.

* [Azure PowerShell](../powershell-install-configure.md)
* [Operational Insights](https://azure.microsoft.com/services/operational-insights/)


## 클러스터 로그를 보고 검색하기 위한 Operational Insights 설정
[Azure 진단으로 로그를 수집하는 방법](service-fabric-diagnostics-how-to-setup-wad.md)을 자세히 보여주는 문서에는 Azure 저장소 계정으로 전송되는 로그를 사용하도록 설정하는 단계별 가이드가 있습니다. 클러스터에 진단이 설정되어 저장소 계정에 로그를 업로드하기 시작하면, 이제 다음으로 Operational Insights 포털을 통해 모든 클러스터 로그를 확인, 검색 및 쿼리할 수 있도록 Operational Insights를 설정합니다.

### Operational Insights 작업 영역 만들기
Operational Insights 작업 영역을 만드는 단계를 확인하려면 아래 문서로 이동하세요. 작업 영역을 만드는 두 가지 방법이 설명되어 있습니다. Azure 포털 및 구독 기반 접근 방식을 선택합니다. 이 문서의 뒷부분에서는 Operational Insights 작업 영역의 이름이 필요합니다. 저장소 계정을 포함하여 모든 클러스터 리소스를 만드는 데 사용한 것과 동일한 구독을 사용하여 Operational Insights 작업 공간을 만듭니다.

[Operational Insights 탑재](https://technet.microsoft.com/library/mt484118.aspx)

### 클러스터 로그를 표시하도록 Operational Insights 작업 영역 구성
앞에서 설명한 것처럼 Operational Insights 작업 영역을 만든 후에는 진단 확장을 통해 클러스터에서 로그가 업로드되는 Azure 저장소 테이블로부터 로그를 가져오도록 작업 영역을 구성합니다. 현재 이 구성은 Operational Insights 포털에서 수행할 수 없고 PowerShell 명령을 통해서만 수행할 수 있습니다. 다음 PowerShell 스크립트를 실행합니다.

```powershell

    <#
    This script will configure an Operations Management Suite workspace (aka Operational Insights workspace) to read Diagnostics from an Azure Storage account.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and Resource Manager storage accounts.

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

$workspace = Select-Workspace
$storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try
{
    $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop
}
catch [Hyak.Common.CloudException]
{
    # HTTP Not Found is returned if the storage insight doesn't exist
}

if ($existingConfig) {
    Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else {
    if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") {
        Switch-AzureMode -Name AzureServiceManagement
        $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary
        Switch-AzureMode -Name AzureResourceManager
    } else {
        $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1
    }
    New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers
}
```

저장소 계정의 Azure 테이블에서 읽을 Operational Insights 작업 영역을 구성하면 포털에 로그인하고 Operational Insights 리소스에 대한 **저장소** 탭으로 이동해야 합니다. 다음과 유사하게 표시됩니다. ![Azure 포털에서 Operational Insights 저장소 구성](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Operational Insights에서 로그 검색 및 보기
Operational Insights 작업 영역이 특정 저장소 계정에서 로그를 읽도록 구성한 후 로그가 Operational Insights UI에 표시되는 데 최대 10분 정도 소요될 수 있습니다. 새 로그가 생성될 수 있게 서비스 패브릭 응용 프로그램을 클러스터에 배포해야 합니다. 그래야 서비스 플랫폼으로부터 운영 이벤트가 생성됩니다.

1. 로그를 보려면 Operational Insights 포털의 기본 페이지에서 **로그 검색**을 선택합니다. ![Operational Insights 로그 검색 옵션](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. 로그 검색 페이지에서 **Type=ServiceFabricOperationalEvent** 쿼리를 사용합니다. 클러스터에서 아래와 같이 운영 로그가 표시될 것입니다. 모든 행위자 프로그래밍 모델 로그를 보려면 **Type=ServiceFabricReliableActorEvent**에 대해 쿼리합니다. Reliable Services 프로그래밍 모델의 모든 로그를 보려면 **Type=ServiceFabricReliableServiceEvent**를 입력합니다. ![Operational Insights 로그 쿼리 및 보기](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### 문제 해결에 도움이 되는 샘플 쿼리
몇 가지 시나리오의 예와 문제 해결에 사용 가능한 쿼리는 다음과 같습니다.

1. **서비스 패브릭에서 특정 서비스에 대해 RunAsync가 호출되었는지 확인하려는 경우:** 서비스를 시작하는 중에 충돌 여부를 배제하려는 경우 이 작업이 필요할 수 있습니다. 이 작업을 수행하려면 아래의 쿼리를 배포한 서비스 및 응용 프로그램 이름에 맞게 대체하여 검색하고 결과가 반환되는지 확인합니다.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. **상태 저장 서비스를 실행 중이며 서비스 패브릭에서 오류로 표시한 예외가 발생했는지 확인하려는 경우:** 다음과 유사한 쿼리로 해당 이벤트를 확인합니다.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. **배포된 모든 응용 프로그램과 서비스에서 행위자 메서드가 생성한 모든 예외에 해당하는 이벤트를 찾으려는 경우:** 다음과 유사한 쿼리를 사용할 수 있습니다.

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## 새 EventSource 채널에서 로그를 수집 및 업로드하도록 진단 업데이트
배포하려는 새 응용 프로그램을 나타내는 새 EventSource 채널에서 로그를 수집하도록 진단을 업데이트하려면 기존 클러스터에 대한 진단 설정을 설명하는 [위 섹션](#deploywadarm)에서와 동일한 단계를 수행하면 됩니다.

리소스 관리자 명령을 통해 구성 업데이트를 적용하려면 먼저 새 EventSources에 대해 항목을 추가하도록 WadConfigUpdate.json의 EtwEventSourceProviderConfiguration 섹션을 업데이트해야 합니다. Operational Insights가 응용 프로그램 ETW 이벤트를 읽어오도록 구성된 테이블이므로 업로드를 위한 테이블은 동일합니다(ETWEventTable).


## 다음 단계
문제를 해결하는 동안 조사해야 하는 이벤트에 대한 자세한 내용을 확인하려면 [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) 및 [Reliable Services](service-fabric-reliable-services-diagnostics.md)가 내보낸 진단 이벤트를 확인합니다.

<!---HONumber=AcomDC_0330_2016-->