---
title: Azure 사이트 복구에서 이동성 서비스의 자동 업데이트
description: Azure 사이트 복구를 사용하여 Azure VM을 복제할 때 이동성 서비스의 자동 업데이트 에 대한 개요입니다.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: 67298ecf0c17feee2d36bb8774cae37b1ca81381
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618969"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Azure-Azure 복제에서 모빌리티 서비스의 자동 업데이트

Azure 사이트 복구는 월별 릴리스 케이던스를 사용하여 문제를 해결하고 기존 기능을 향상시키거나 새 기능을 추가합니다. 서비스를 최신 상태로 유지하려면 매월 패치 배포를 계획해야 합니다. 각 업그레이드와 관련된 오버헤드를 방지하려면 사이트 복구에서 구성 요소 업데이트를 관리하도록 허용할 수 있습니다.

[Azure-Azure 재해 복구 아키텍처에서](azure-to-azure-architecture.md)설명한 것처럼 모빌리티 서비스는 한 Azure 지역에서 다른 Azure 지역으로 복제가 활성화된 모든 Azure 가상 시스템(VM)에 설치됩니다. 자동 업데이트를 사용하면 각 새 릴리스가 모빌리티 서비스 확장을 업데이트합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>자동 업데이트 작동 방식

사이트 복구를 사용하여 업데이트를 관리하는 경우 볼트와 동일한 구독에서 만든 자동화 계정을 통해 Azure 서비스에서 사용하는 전역 Runbook을 배포합니다. 각 볼트는 하나의 자동화 계정을 사용합니다. 볼트의 각 VM에 대해 Runbook은 활성 자동 업데이트를 확인합니다. 최신 버전의 Mobility 서비스 확장을 사용할 수 있는 경우 업데이트가 설치됩니다.

기본 Runbook 일정은 복제된 VM 지리의 표준 시간대에서 매일 오전 12:00에 발생합니다. 자동화 계정을 통해 Runbook 일정을 변경할 수도 있습니다.

> [!NOTE]
> [롤업 업데이트 35부터](site-recovery-whats-new.md#updates-march-2019)업데이트에 사용할 기존 자동화 계정을 선택할 수 있습니다. 롤업 35 업데이트 이전에 사이트 복구는 기본적으로 자동화 계정을 만들었습니다. VM에 대한 복제를 사용하도록 설정할 때만 이 옵션을 선택할 수 있습니다. 복제가 이미 활성화된 VM에는 사용할 수 없습니다. 선택한 설정은 동일한 자격 증명 모음에서 보호되는 모든 Azure VM에 적용됩니다.

자동 업데이트를 켜면 Azure VM을 다시 시작하거나 진행 중인 복제에 영향을 주지 않아도 됩니다.

자동화 계정의 작업 청구는 한 달에 사용된 작업 런타임 분 수를 기반으로 합니다. 작업 실행은 매일 몇 초에서 약 1분 정도 걸리며 무료 단위로 다룹니다. 기본적으로 다음 표와 같이 500분은 자동화 계정의 무료 단위로 포함됩니다.

| 무료 유닛 포함(매월) | Price |
|---|---|
| 작업 런타임 500분 | ₹0.14/분

## <a name="enable-automatic-updates"></a>자동 업데이트 사용

사이트 복구가 확장 업데이트를 관리할 수 있는 방법에는 여러 가지가 있습니다.

- [복제 활성화 단계의 일부로 관리](#manage-as-part-of-the-enable-replication-step)
- [자격 증명 모음 내에서 확장 업데이트 설정 전환](#toggle-the-extension-update-settings-inside-the-vault)
- [수동으로 업데이트 관리](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>복제 활성화 단계의 일부로 관리

VM 보기 또는 [복구 서비스 자격 증명 모음에서](azure-to-azure-how-to-enable-replication.md)VM에 대한 복제를 사용하도록 설정하면 사이트 복구를 허용하여 사이트 복구 확장에 대한 업데이트를 관리하거나 수동으로 관리할 수 있습니다. [from the VM view](azure-to-azure-quickstart.md)

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="확장 설정":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>자격 증명 모음 내에서 확장 업데이트 설정 전환

1. 복구 서비스 자격 증명 모음에서**사이트 복구 인프라** **관리로** > 이동합니다.
1. **Azure 가상 컴퓨터** > **확장 업데이트 설정에서** > 사이트 **On****복구를 관리할 수 있습니다**.

   확장을 수동으로 관리하려면 **을 선택합니다.**

1. **저장**을 선택합니다.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="확장 업데이트 설정":::

> [!IMPORTANT]
> **사이트 복구 관리를 선택선택하면**볼트의 모든 VM에 설정이 적용됩니다.

> [!NOTE]
> 두 옵션 중 하나는 업데이트 관리에 사용되는 자동화 계정을 알려주는 것입니다. 이 기능을 볼트에서 처음 사용하는 경우 기본적으로 새 자동화 계정이 만들어집니다. 또는 설정을 사용자 지정하고 기존 자동화 계정을 선택할 수 있습니다. 동일한 볼트에서 복제를 사용하도록 설정하는 모든 후속 taks는 이전에 만든 자동화 계정을 사용합니다. 현재 드롭다운 메뉴에는 볼트와 동일한 리소스 그룹에 있는 자동화 계정만 나열됩니다.

> [!IMPORTANT]
> 다음 스크립트는 자동화 계정의 컨텍스트에서 실행되어야 합니다.
사용자 지정 자동화 계정의 경우 다음 스크립트를 사용합니다.

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
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
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

            # Rate controlling the get calls to maximum 120 calls each minute.
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

### <a name="manage-updates-manually"></a>수동으로 업데이트 관리

1. VM에 설치된 Mobility 서비스에 대한 새 업데이트가 있는 경우 새 **사이트 복구 복제 에이전트 업데이트를 사용할 수 있다는 알림이 표시됩니다. 설치하려면 클릭합니다.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="복제된 항목 창":::

1. 알림을 선택하여 VM 선택 페이지를 엽니다.
1. 업그레이드할 VM을 선택한 다음 **확인을**선택합니다. 선택한 각 VM에 대해 업데이트 모빌리티 서비스가 시작됩니다.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="복제된 항목 VM 목록":::

## <a name="common-issues-and-troubleshooting"></a>일반적인 문제 및 문제 해결

자동 업데이트에 문제가 있는 경우 볼트 대시보드의 **구성 문제** 아래에 오류 알림이 표시됩니다.

자동 업데이트를 활성화할 수 없는 경우 다음과 같은 일반적인 오류 및 권장 작업을 참조하세요.

- **오류**: Azure 실행 계정(서비스 사용자)을 만들고 서비스 사용자에게 기여자 역할을 부여할 권한이 없습니다.

  **권장 작업**: 로그인한 계정이 기고자로 할당되어 있는지 확인하고 다시 시도하십시오. 사용 권한 할당에 대한 자세한 내용은 [포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체를 만드는 방법의](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)필수 사용 권한 섹션을 참조하십시오.

  자동 업데이트를 사용하도록 설정한 후 대부분의 문제를 해결하려면 **복구를**선택합니다. 복구 단추를 사용할 수 없는 경우 확장 업데이트 설정 창에 표시되는 오류 메시지를 참조하세요.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="확장 업데이트 설정의 사이트 복구 서비스 복구 단추":::

- **오류**: 복구 서비스 리소스에 액세스할 수 있는 권한이 실행 계정에 없습니다.

  **권장 작업**: 삭제한 다음 계정으로 실행 을 [다시 만듭니다.](/azure/automation/automation-create-runas-account) 또는 계정의 Azure Active Directory 응용 프로그램이 복구 서비스 리소스에 액세스할 수 있는지 확인합니다.

- **오류**: 실행 계정을 찾을 수 없습니다. Azure Active Directory 애플리케이션, 서비스 사용자, 역할, Automation 인증서 자산, Automation 연결 자산 중 하나가 삭제되었거나 생성되지 않았습니다. 또는 인증서와 연결 사이에서 지문이 일치하지 않습니다.

  **권장 작업**: 삭제한 다음 계정으로 실행 을 [다시 만듭니다.](/azure/automation/automation-create-runas-account)

- **오류**: 자동화 계정에서 사용하는 인증서로 Azure 실행이 곧 만료됩니다.

  Run As 계정에 대해 생성된 자체 서명된 인증서는 생성일로부터 1년 만료됩니다. 만료되기 전에 언제든지 갱신할 수 있습니다. 이메일 알림에 가입한 경우, 옆에서 조치가 필요할 때 이메일도 받게 됩니다. 이 오류는 만료 날짜 2개월 전에 표시되며 인증서가 만료되면 중요한 오류로 변경됩니다. 인증서가 만료되면 동일한 인증서를 갱신할 때까지 자동 업데이트가 작동하지 않습니다.

  **권장 작업**: 이 문제를 해결하려면 **복구를** 선택한 다음 **인증서를 갱신합니다.**

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="갱신 인증서":::

  > [!NOTE]
  > 인증서를 갱신한 후 페이지를 새로 고쳐 현재 상태를 표시합니다.
