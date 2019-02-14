---
title: Azure에서 Azure 재해 복구로 Mobility Service 자동 업데이트 | Microsoft Docs
description: Azure Site Recovery를 사용하여 Azure VM을 복제할 때 Mobility Service의 자동 업데이트에 대한 개요를 제공합니다.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 3f0f28ca22321b537ab7e8911c5cbb513a1ade81
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818927"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Azure에서 Azure 복제로 Mobility Service 자동 업데이트

Azure Site Recovery는 매달 릴리스를 출시하여 기존 기능을 개선하고, 새 기능을 추가하고, 알려진 문제를 해결합니다. 즉, 서비스를 최신 상태로 유지하려면 매달 이러한 패치를 배포하도록 계획을 세워야 합니다. 업그레이드와 관련된 오버헤드를 방지하기 위해 사용자는 Site Recovery가 알아서 구성 요소 업데이트를 관리하도록 선택할 수 있습니다. Azure에서 Azure 재해 복구에 대한 [아키텍처 참조](azure-to-azure-architecture.md)에 자세히 설명되어 있듯이, 한 Azure 지역에서 다른 지역으로 가상 머신을 복제할 때 복제를 사용하도록 설정된 모든 Azure 가상 머신에 Mobility Service가 설치됩니다. 자동 업데이트를 사용하도록 설정한 경우 Mobility Service 확장은 모든 새 릴리스로 업데이트됩니다. 이 문서에서는 다음 내용을 자세히 다룹니다.

- 자동 업데이트의 작동 원리
- 자동 업데이트 사용
- 일반적인 문제 및 문제 해결
 
## <a name="how-does-automatic-update-work"></a>자동 업데이트의 작동 원리

Site Recovery에서 업데이트를 관리하도록 허용하면 자격 증명 모음과 동일한 구독에 생성된 자동화 계정을 통해 글로벌 Runbook(Azure 서비스에서 사용하는)이 배포됩니다. 한 자동화 계정은 특정 자격 증명 모음에 사용됩니다. Runbook은 자격 증명 모음에서 자동 업데이트가 설정된 각 VM을 확인하여 새 버전이 있으면 Mobility Service 확장의 업그레이드를 시작합니다. Runbook의 기본 일정은 복제된 가상 머신이 있는 지역의 표준 시간대에 따라 매일 오전 12시에 반복됩니다. 필요한 경우 사용자가 자동화 계정을 통해 Runbook 일정을 수정할 수 있습니다. 

> [!NOTE]
> 자동 업데이트를 설정해도 Azure VM을 다시 부팅할 필요가 없으며 진행 중인 복제가 영향을 받지도 않습니다.

> [!NOTE]
> 자동화 계정에서 사용하는 작업에 대한 청구는 그 달에 사용된 작업 실행 시간(분)을 기반으로 하며, 기본적으로 500분은 자동화 계정의 무료 단위로 포함됩니다. 일별 작업 실행은 **몇 초에서 1분** 사이이며, **크레딧으로 충분**합니다.

포함되는 무료 단위(매월)**   작업 실행 시간 요금 500분 ₹0.14/분

## <a name="enable-automatic-updates"></a>자동 업데이트 사용

다음과 같은 방법으로 Site Recovery가 업데이트를 관리하도록 선택할 수 있습니다.

- [복제 설정 단계의 일부](#as-part-of-the-enable-replication-step)
- [자격 증명 모음 내에서 확장 업데이트 설정 전환](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>복제 설정 단계의 일부:

[가상 머신 보기에서](azure-to-azure-quickstart.md) 또는 [복구 서비스 자격 증명 모음에서](azure-to-azure-how-to-enable-replication.md) 가상 머신에 복제를 사용하도록 설정하면 Site Recovery 확장 업데이트를 Site Recovery에게 맡길 것인지 아니면 수동으로 관리할 것인지 선택하는 옵션이 제공됩니다.

![복제 자동 업데이트 사용](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>자격 증명 모음 내에서 확장 업데이트 설정 전환

1. 자격 증명 모음 내에서 **관리**-> **Site Recovery 인프라**로 이동
2. **Azure 가상 머신**-> **확장 업데이트 설정** 아래에서 토글을 클릭하여 *ASR에서 업데이트를 관리하도록 허용*하거나 *수동으로 관리* 중에 선택합니다. **저장**을 클릭합니다.

![자격 증명 모음 설정/해제-자동 업데이트](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> *ASR에서 업데이트를 관리하도록 허용*을 선택하면 해당 자격 증명 모음에 있는 모든 가상 머신에 설정이 적용됩니다.


> [!Note] 
> 두 옵션 모두 업데이트 관리에 사용되는 자동화 계정에 대한 알림을 제공합니다. 자격 증명 모음에서 이 기능을 처음으로 사용하는 경우 새 자동화 계정이 만들어집니다. 동일한 자격 증명 모음에서 이루어지는 모든 후속 복제에는 이전에 생성된 계정이 사용됩니다.

**사용자 지정 자동화 계정을 사용하려는 경우 아래 스크립트를 사용하세요:-**

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
    Param
    (
        [Parameter(Mandatory=$true)]      
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
        [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

        [Switch]
        $DisplayMessageToUser
    )

    Write-Output $Message

}

function Write-InformationTracing
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
        $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
        
        return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers    
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try 
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try 
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls per minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage 
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)   
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-manually"></a>수동으로 관리

1. Azure VM에 설치된 모바일 서비스에 사용 가능한 새 업데이트가 있는 경우 "새 사이트 복구 복제 에이전트 업데이트를 사용할 수 있습니다. 설치하려면 클릭하세요.”라는 알림이 표시됩니다.

     ![복제된 항목 창](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
3. 이 알림을 선택하여 가상 머신 선택 페이지를 엽니다.
4. Mobile Service를 업그레이드하려는 가상 머신을 선택하고 **확인**을 선택합니다.

     ![복제된 항목 VM 목록](./media/vmware-azure-install-mobility-service/update-okpng.png)

선택한 가상 머신 각각에 대한 Mobility Service 업데이트 작업이 시작됩니다.


## <a name="common-issues--troubleshooting"></a>일반적인 문제 및 문제 해결

자동 업데이트에는 문제가 있는 경우 자격 증명 모음 대시보드의 '구성 문제'에 동일한 알림이 제공됩니다. 

자동 업데이트를 사용하려고 했으나 실패한 경우 아래를 참조하여 문제를 해결할 수 있습니다.

**오류**: Azure 실행 계정(서비스 사용자)을 만들고 서비스 사용자에 기여자 역할을 부여할 수 있는 권한이 없습니다. 
- 권장 작업: 로그인한 계정에 ‘기여자’ 역할이 할당되었는지 확인한 후 작업을 다시 시도합니다. 올바른 권한을 할당하는 방법에 대한 자세한 내용은 [이](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) 문서를 참조하세요.
 
자동 업데이트가 켜져 있으면 Site Recovery 서비스가 대부분의 문제를 해결하며 사용자는 '**복구**' 단추를 클릭해야 합니다.

![복구 단추](./media/azure-to-azure-autoupdate/repair.png)

복구 단추를 사용할 수 없는 경우 확장 설정 창에 표시되는 오류 메시지를 참조하세요.

 - **오류**: 복구 서비스 리소스에 액세스할 수 있는 권한이 실행 계정에 없습니다.

    **권장 작업**: 실행 계정을 삭제한 다음, [다시 만들거나](https://docs.microsoft.com/azure/automation/automation-create-runas-account) Automation 실행 계정의 Azure Active Directory 애플리케이션이 복구 서비스 리소스에 액세스할 수 있는지 확인합니다.

- **오류**: 실행 계정을 찾을 수 없습니다. Azure Active Directory 애플리케이션, 서비스 사용자, 역할, Automation 인증서 자산, Automation 연결 자산 중 하나가 삭제되었거나 생성되지 않았습니다. 또는 인증서와 연결 사이에서 지문이 일치하지 않습니다. 

    **권장 작업**: 실행 계정을 삭제한 다음, [다시 만듭니다](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
