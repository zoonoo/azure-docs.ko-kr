---
title: 메트릭에 대한 컨테이너에 대한 Azure 모니터를 업데이트하는 방법 | 마이크로 소프트 문서
description: 이 문서에서는 집계된 메트릭에 대한 탐색 및 경고를 지원하는 사용자 지정 메트릭 기능을 사용하도록 컨테이너용 Azure Monitor를 업데이트하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: a7f40cb0523c2366c47da228e49311c2f9579212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715912"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>메트릭을 사용할 수 있도록 컨테이너용 Azure Monitor를 업데이트하는 방법

컨테이너용 Azure Monitor는 AKS(Azure Kubernetes Services) 클러스터 노드 및 포드에서 메트릭을 수집하고 Azure Monitor 메트릭 저장소에 기록하는 지원을 도입합니다. 이 변경 사항은 성능 차트에서 집계 계산(평균, 개수, 최대, 최소, Sum)을 표시하고, Azure 포털 대시보드에서 성능 차트 고정을 지원하며, 메트릭 경고를 지원할 때 향상된 적시량을 제공하기 위한 것입니다.

>[!NOTE]
>이 기능은 현재 Azure Red Hat OpenShift 클러스터를 지원하지 않습니다.
>

다음 메트릭은 이 기능의 일부로 활성화됩니다.

| 메트릭 네임스페이스 | 메트릭 | 설명 |
|------------------|--------|-------------|
| 인사이트.컨테이너/노드 | cpuUsageMillicores, cpuusagePercentage, 메모리리터, 메모리어백분율, 메모리작업세트바이트, 메모리작업세트백, 노드카운트 | 이러한 지표는 *노드* 메트릭이며 *호스트를* 차원으로 포함하며<br> 노드의 이름을 *호스트* 차원의 값으로 지정합니다. |
| 인사이트.컨테이너/포드 | 포드 카운트 | 다음은 *포드* 메트릭이며 컨트롤러 이름, Kubernetes 네임스페이스, 이름, 위상 등 차원으로 다음을 포함합니다. |

이러한 새 기능을 지원 하도록 클러스터를 업데이트 Azure 포털, Azure PowerShell 또는 Azure CLI로 수행할 수 있습니다. Azure PowerShell 및 CLI를 사용하면 클러스터당 또는 구독의 모든 클러스터에 대해 이 기능을 활성화할 수 있습니다. AKS의 새 배포에는 이 구성 변경 및 기능이 자동으로 포함됩니다.

두 프로세스 중 하나에서 에이전트가 수집한 데이터를 클러스터 리소스에 게시할 수 있도록 **모니터링 메트릭 게시자** 역할을 클러스터의 서비스 주체에 할당합니다. 모니터링 메트릭 게시자는 메트릭을 리소스에 푸시할 수 있는 권한만 가지고 있으며, 상태를 변경하거나 리소스를 업데이트하거나 데이터를 읽을 수 없습니다. 역할에 대한 자세한 내용은 [모니터링 메트릭 게시자 역할을](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 확인합니다.

* 사용자 지정 메트릭은 Azure 지역의 하위 집합에서만 사용할 수 있습니다. 지원되는 지역 목록은 여기에 설명되어 [있습니다.](../platform/metrics-custom-overview.md#supported-regions)
* 노드 및 포드 사용자 지정 성능 메트릭의 컬렉션을 사용하도록 설정하려면 AKS 클러스터 리소스에서 **[소유자](../../role-based-access-control/built-in-roles.md#owner)** 역할의 구성원입니다. 

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.59 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Azure 포털에서 클러스터 업그레이드

컨테이너에 대해 Azure Monitor에서 모니터링하는 기존 AKS 클러스터의 경우 왼쪽 창에서 **Insights를** 선택하여 Azure Monitor의 다중 클러스터 보기에서 또는 클러스터에서 직접 상태를 볼 수 있도록 클러스터를 선택한 후 포털 상단에 배너가 표시됩니다.

![Azure 포털에서 AKS 클러스터 배너 업그레이드](./media/container-insights-update-metrics/portal-banner-enable-01.png)

**활성화를** 클릭하면 클러스터를 업그레이드하는 프로세스가 시작됩니다. 이 프로세스는 완료하는 데 수 초가 소요되며 메뉴의 알림에서 진행 상황을 추적할 수 있습니다.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Azure 명령 셸에서 Bash를 사용하여 모든 클러스터 업그레이드

다음 단계를 수행하여 Azure 명령 셸의 Bash를 사용하여 구독의 모든 클러스터를 업데이트합니다.

1. Azure CLI를 사용하여 다음 명령을 실행합니다.  AKS 클러스터에 대한 **AKS 개요** 페이지에서 값을 사용하여 **구독Id값을** 편집합니다.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    구성 변경을 완료하는 데 몇 초 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Azure CLI를 사용하여 클러스터당 업그레이드

Azure CLI를 사용하여 구독에서 특정 클러스터를 업데이트하려면 다음 단계를 수행합니다.

1. Azure CLI를 사용하여 다음 명령을 실행합니다. AKS 클러스터에 대한 **AKS 개요** 페이지의 값을 사용하여 **멤버쉽Id,** **리소스그룹이름**및 **클러스터이름에** 대한 값을 편집합니다.  **clientIdOfSPN의**값을 얻으려면 아래 예제와 같이 명령을 `az aks show` 실행할 때 반환됩니다.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ``` 

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Azure PowerShell을 사용하여 모든 클러스터 업그레이드

Azure PowerShell을 사용하여 구독의 모든 클러스터를 업데이트하려면 다음 단계를 수행합니다.

1. 다음 스크립트를 복사하여 파일에 붙여넣습니다.

    ```powershell
    <# 
    .DESCRIPTION 
        Adds the Monitoring Metrics Publisher role assignment to the all AKS clusters in specified subscription        
      
    
    .PARAMETER SubscriptionId
        Subscription Id that the AKS cluster is in

    #>

    param(
    [Parameter(mandatory = $true)]
    [string]$SubscriptionId 
    )


    # checks the required Powershell modules exist and if not exists, request the user permission to install
    $azAccountModule = Get-Module -ListAvailable -Name Az.Accounts
    $azAksModule = Get-Module -ListAvailable -Name Az.Aks 
    $azResourcesModule = Get-Module -ListAvailable -Name Az.Resources

    if (($null -eq $azAccountModule) -or ( $null -eq $azAksModule ) -or ($null -eq $azResourcesModule)) {

    $currentPrincipal = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())

    if ($currentPrincipal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {
        Write-Host("Running script as an admin...")
        Write-Host("")
    }
    else {
        Write-Host("Please run the script as an administrator") -ForegroundColor Red
        Stop-Transcript
        exit
    }


    $message = "This script will try to install the latest versions of the following Modules : `
                Az.Resources, Az.Accounts and Az.Aks using the command if not installed already`
                `'Install-Module {Insert Module Name} -Repository PSGallery -Force -AllowClobber -ErrorAction Stop -WarningAction Stop'
                `If you do not have the latest version of these Modules, this troubleshooting script may not run."
    $question = "Do you want to Install the modules and run the script or just run the script?"

    $choices = New-Object Collections.ObjectModel.Collection[Management.Automation.Host.ChoiceDescription]
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Yes, Install and run'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Continue without installing the Module'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Quit'))

    $decision = $Host.UI.PromptForChoice($message, $question, $choices, 0)

    switch ($decision) {
        0 { 

            if ($null -eq $azResourcesModule)   {
                try {
                    Write-Host("Installing Az.Resources...")
                    Install-Module Az.Resources -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAccountModule) {
                try {
                    Write-Host("Installing Az.Accounts...")
                    Install-Module Az.Accounts -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAksModule) {
                try {
                    Write-Host("Installing Az.Aks...")
                    Install-Module Az.Aks -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Aks in a new powershell window: eg. 'Install-Module Az.Aks -Repository PSGallery -Force'") -ForegroundColor Red 
                    exit
                }   
            }
           
        }
        1 {

            if ($null -eq $azResourcesModule)   {
                try {
                    Import-Module Az.Resources -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Resources...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Resources in a new powershell window: eg. 'Install-Module Az.Resources -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAccountModule) {
                try {
                    Import-Module Az.Accounts -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Accounts...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAksModule) {
                try {
                    Import-Module Az.Aks -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Aks... Please reinstall this Module") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }    
            }     
    
        }
        2 { 
            Write-Host("")
            Stop-Transcript
            exit
        }
      }
    }

    try {
    Write-Host("")
    Write-Host("Trying to get the current Az login context...")
    $account = Get-AzContext -ErrorAction Stop
    Write-Host("Successfully fetched current AzContext context...") -ForegroundColor Green
    Write-Host("")
    }
    catch {
    Write-Host("")
    Write-Host("Could not fetch AzContext..." ) -ForegroundColor Red
    Write-Host("")
    }

    if ($account.Account -eq $null) {
    try {
        Write-Host("Please login...")
        Connect-AzAccount -subscriptionid $SubscriptionId
    }
    catch {
        Write-Host("")
        Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
        Write-Host("")
        Stop-Transcript
        exit
     }
    }
    else {
    if ($account.Subscription.Id -eq $SubscriptionId) {
        Write-Host("Subscription: $SubscriptionId is already selected. Account details: ")
        $account
    }
    else {
        try {
            Write-Host("Current Subscription:")
            $account
            Write-Host("Changing to subscription: $SubscriptionId")
            Set-AzContext -SubscriptionId $SubscriptionId
        }
        catch {
            Write-Host("")
            Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
            Write-Host("")
            Stop-Transcript
            exit
        }
      }
    } 

    #
    #   get all the AKS clusters in specified subscription
    #
    Write-Host("getting all aks clusters in specified subscription ...")
    $allClusters = Get-AzAks -ErrorVariable notPresent -ErrorAction SilentlyContinue
    if ($notPresent) {
    Write-Host("")
    Write-Host("Failed to get Aks clusters in specified subscription. Please make sure that you have access to the existing clusters") -ForegroundColor Red
    Write-Host("")
    Stop-Transcript
    exit
    }
    Write-Host("Successfully got all aks clusters ...") -ForegroundColor Green

    $clustersCount = $allClusters.Id.Length

    Write-Host("Adding role assignment for the clusters ...")

    for ($index = 0 ; $index -lt $clustersCount ; $index++) {  

    #
    #  Add Monitoring Metrics Publisher role assignment to the AKS cluster resource
    #

    $servicePrincipalClientId = $allClusters.ServicePrincipalProfile[$index].ClientId
    $clusterResourceId = $allClusters.Id[$index]
    $clusterName = $allClusters.Name[$index]


    Write-Host("Adding role assignment for the cluster: $clusterResourceId, servicePrincipalClientId: $servicePrincipalClientId ...")

  
    New-AzRoleAssignment -ApplicationId $servicePrincipalClientId -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher"  -ErrorVariable assignmentError -ErrorAction SilentlyContinue

    if ($assignmentError) {

        $roleAssignment = Get-AzRoleAssignment -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher" -ErrorVariable getAssignmentError -ErrorAction SilentlyContinue     
        if ($assignmentError.Exception -match "role assignment already exists" -or ( $roleAssignment -and $roleAssignment.ObjectType -like "ServicePrincipal" )) {
            Write-Host("Monitoring Metrics Publisher role assignment already exists on the cluster resource : '" + $clusterName + "'") -ForegroundColor Green 
        }
        else { 
        
            Write-Host("Failed to add Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "' , error : $assignmentError") -ForegroundColor Red      
        }

    }
    else {

        Write-Host("Successfully added Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "'") -ForegroundColor Green 
   
    }   

    Write-Host("Completed adding role assignment for the cluster: $clusterName ...")
        
    }

    Write-Host("Completed adding role assignment for the aks clusters in subscriptionId :$SubscriptionId")   
    ```

2. 이 파일을 로컬 폴더에 **onboard_metrics_atscale.ps1로** 저장합니다.
3. Azure PowerShell을 사용하여 다음 명령을 실행합니다.  AKS 클러스터에 대한 **AKS 개요** 페이지에서 값을 사용하여 **구독Id값을** 편집합니다.

    ```powershell
    .\onboard_metrics_atscale.ps1 subscriptionId
    ```
    구성 변경을 완료하는 데 몇 초 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Azure PowerShell을 사용하여 클러스터당 업그레이드

Azure PowerShell을 사용하여 특정 클러스터를 업데이트하려면 다음 단계를 수행합니다.

1. 다음 스크립트를 복사하여 파일에 붙여넣습니다.

    ```powershell
    <# 
    .DESCRIPTION 
        Adds the Monitoring Metrics Publisher role assignment to the specified AKS cluster        
      
    
    .PARAMETER SubscriptionId
        Subscription Id that the AKS cluster is in

    .PARAMETER resourceGroupName
        Resource Group name that the AKS cluster is in
            
     .PARAMETER clusterName
        Name of the AKS cluster.
    #>

    param(
       [Parameter(mandatory = $true)]
       [string]$SubscriptionId,
       [Parameter(mandatory = $true)]
       [string]$resourceGroupName,
       [Parameter(mandatory = $true)]
       [string] $clusterName
    )

    # checks the required Powershell modules exist and if not exists, request the user permission to install
    $azAccountModule = Get-Module -ListAvailable -Name Az.Accounts
    $azAksModule = Get-Module -ListAvailable -Name Az.Aks 
    $azResourcesModule = Get-Module -ListAvailable -Name Az.Resources

    if (($null -eq $azAccountModule) -or ($null -eq $azAksModule) -or ($null -eq $azResourcesModule)) {


    $currentPrincipal = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())

    if ($currentPrincipal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {
        Write-Host("Running script as an admin...")
        Write-Host("")
    }
    else {
        Write-Host("Please run the script as an administrator") -ForegroundColor Red
        Stop-Transcript
        exit
    }


    $message = "This script will try to install the latest versions of the following Modules : `
                Az.Resources, Az.Accounts and Az.Aks using the command`
                `'Install-Module {Insert Module Name} -Repository PSGallery -Force -AllowClobber -ErrorAction Stop -WarningAction Stop'
                `If you do not have the latest version of these Modules, this troubleshooting script may not run."
    $question = "Do you want to Install the modules and run the script or just run the script?"

    $choices = New-Object Collections.ObjectModel.Collection[Management.Automation.Host.ChoiceDescription]
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Yes, Install and run'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Continue without installing the Module'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Quit'))

    $decision = $Host.UI.PromptForChoice($message, $question, $choices, 0)

    switch ($decision) {
        0 { 

            if ($null -eq $azResourcesModule) {
                try {
                    Write-Host("Installing Az.Resources...")
                    Install-Module Az.Resources -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAccountModule) {
                try {
                    Write-Host("Installing Az.Accounts...")
                    Install-Module Az.Accounts -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAksModule) {
                try {
                    Write-Host("Installing Az.Aks...")
                    Install-Module Az.Aks -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Aks in a new powershell window: eg. 'Install-Module Az.Aks -Repository PSGallery -Force'") -ForegroundColor Red 
                    exit
                }
            }
           
        }
        1 {

            if ($null -eq $azResourcesModule) {
                try {
                    Import-Module Az.Resources -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Resources...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Resources in a new powershell window: eg. 'Install-Module Az.Resources -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAccountModule) {
                try {
                    Import-Module Az.Accounts -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Accounts...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAksModule) {
                try {
                    Import-Module Az.Aks -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Aks... Please reinstall this Module") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }   
            }      
    
        }
        2 { 
            Write-Host("")
            Stop-Transcript
            exit
        }
     }
    }

    try {
       Write-Host("")
       Write-Host("Trying to get the current Az login context...")
       $account = Get-AzContext -ErrorAction Stop
       Write-Host("Successfully fetched current AzContext context...") -ForegroundColor Green
       Write-Host("")
    }
    catch {
      Write-Host("")
      Write-Host("Could not fetch AzContext..." ) -ForegroundColor Red
      Write-Host("")
    }


    if ($account.Account -eq $null) {
    try {
        Write-Host("Please login...")
        Connect-AzAccount -subscriptionid $SubscriptionId
    }
    catch {
        Write-Host("")
        Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
        Write-Host("")
        Stop-Transcript
        exit
     }
    }
    else {
    if ($account.Subscription.Id -eq $SubscriptionId) {
        Write-Host("Subscription: $SubscriptionId is already selected. Account details: ")
        $account
    }
    else {
        try {
            Write-Host("Current Subscription:")
            $account
            Write-Host("Changing to subscription: $SubscriptionId")
            Set-AzContext -SubscriptionId $SubscriptionId
        }
        catch {
            Write-Host("")
            Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
            Write-Host("")
            Stop-Transcript
            exit
        }
      }
    }

    #
    #   Check AKS cluster existence and access check
    #
    Write-Host("Checking aks cluster exists...")
    $cluster = Get-AzAks -ResourceGroupName $resourceGroupName -Name $clusterName  -ErrorVariable notPresent -ErrorAction SilentlyContinue
    if ($notPresent) {
       Write-Host("")
       Write-Host("Could not find Aks cluster. Please make sure that specified cluster exists: '" + $clusterName + "'is correct and you have access to the cluster") -ForegroundColor Red
       Write-Host("")
       Stop-Transcript
       exit
    }
    Write-Host("Successfully checked specified cluster exists details...") -ForegroundColor Green

    $servicePrincipalClientId = $cluster.ServicePrincipalProfile.clientId
    $clusterResourceId = $cluster.Id

    #
    #  Add Monitoring Metrics Publisher role assignment to the AKS cluster resource
    #

    New-AzRoleAssignment -ApplicationId $servicePrincipalClientId -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher"  -ErrorVariable assignmentError -ErrorAction SilentlyContinue

    if ($assignmentError) {

    $roleAssignment = Get-AzRoleAssignment -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher" -ErrorVariable getAssignmentError -ErrorAction SilentlyContinue     

    if ($assignmentError.Exception -match "role assignment already exists" -or ( $roleAssignment -and $roleAssignment.ObjectType -like "ServicePrincipal" )) {           
        Write-Host("Monitoring Metrics Publisher role assignment already exists on the cluster resource : '" + $clusterName + "'") -ForegroundColor Green 
    }
    else { 
        
        Write-Host("Failed to add Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "' , error : $assignmentError") -ForegroundColor Red      
    }

    }
    else {

    Write-Host("Successfully added Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "'") -ForegroundColor Green 
   
    }
    ```

2. 이 파일을 로컬 폴더에 **onboard_metrics.ps1로** 저장합니다.
3. Azure PowerShell을 사용하여 다음 명령을 실행합니다. AKS 클러스터에 대한 **AKS 개요** 페이지의 값을 사용하여 **멤버쉽Id,** **리소스그룹이름**및 **클러스터이름에** 대한 값을 편집합니다.

    ```powershell
    .\onboard_metrics.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    구성 변경을 완료하는 데 몇 초 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>업데이트 확인 

앞에서 설명한 방법 중 하나를 사용하여 업데이트를 시작하고 Azure Monitor 메트릭 탐색기를 사용하고 **메트릭 네임스페이스에서** **인사이트가** 나열되는지 확인할 수 있습니다. 이 경우 [메트릭 경고를](../platform/alerts-metric.md) 설정하거나 [차트를 대시보드에](../../azure-portal/azure-portal-dashboards.md)고정할 수 있음을 나타냅니다.  
