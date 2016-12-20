---
title: "Azure 실행 계정 구성 | Microsoft Docs"
description: "Azure 자동화에서 보안 주체 인증을 만들고 테스트하며 예제를 사용하는 과정을 안내하는 자습서입니다."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: "서비스 주체 이름, setspn, azure 인증"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 32afda1861c2c558f8bb9ffedf897cd8d1df0b5c


---
# <a name="authenticate-runbooks-with-azure-run-as-account"></a>Azure 실행 계정으로 Runbook 인증
이 항목에서는 Azure Resource Manager 또는 Azure Service Management에서 Runbook 관리 리소스를 인증하기 위해 실행 계정을 사용하여 Azure 포털의 자동화 계정을 구성하는 방법을 보여줍니다.

Azure 포털에서 새 자동화 계정을 만들 경우 다음을 자동으로 만듭니다.

* Azure Active Directory에서 새 서비스 주체, 인증서를 만들고 Runbook을 사용하여 Resource Manager 리소스를 관리하는 데 사용될 참여자 역할 기반 액세스 제어(RBAC)를 할당하는 실행 계정.   
* Runbook을 사용하여 Azure 서비스 관리 또는 클래식 리소스를 관리하는 데 사용될 관리 인증서를 업로드하는 클래식 실행 계정.  

이를 통해 처리를 간편하게 만들고 자동화 요구를 지원하는 Runbook의 구축 및 배포를 신속하게 시작할 수 있습니다.      

실행 계정과 클래식 실행 계정을 사용하여 다음 작업을 수행할 수 있습니다.

* Azure 포털의 Runbook로부터 Azure Resource Manager 또는 Azure 서비스 관리 리소스를 관리하는 경우 Azure로 인증하는 표준화된 방법을 제공합니다.  
* Azure 경고에 구성된 글로벌 Runbook의 사용을 자동화합니다.

> [!NOTE]
> 자동화 글로벌 Runbook을 포함한 Azure [경고 통합 기능](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)에는 실행 및 클래식 실행 계정이 구성된 자동화 계정이 필요합니다. 정의된 실행 계정 및 클래식 실행 계정이 있는 자동화 계정을 선택하거나 새로 하나를 만들도록 선택할 수 있습니다.
> 
> 

Azure 포털에서 자동화 계정을 만들고, PowerShell을 사용하여 자동화 계정을 업데이트하고, Runbook에서 인증하는 방법을 보여줍니다.

이를 수행하기 전에 먼저 이해하고 고려해야 하는 몇 가지 사항이 있습니다.

1. 이 작업은 클래식 또는 리소스 관리자 배포 모델에서 이미 만든 기존 자동화 계정에는 영향을 주지 않습니다.  
2. 이는 Azure 포털을 통해 만들어진 자동화 계정에만 적용됩니다.  클래식 포털에서 계정을 만들면 실행 계정 구성을 복제하지 않습니다.
3. 클래식 리소스를 관리하기 위해 이전에 만든 Runbook과 자산(즉, 일정, 변수 등)이 현재 있고 이 Runbook에서 새 클래식 실행 계정으로 인증하려는 경우, 새 자동화 계정으로 마이그레이션하거나 아래 PowerShell 스크립트를 사용하여 기존 계정을 업데이트해야 합니다.  
4. 새 실행 계정 및 클래식 실행 자동화 계정을 사용하여 인증하려면 아래 예제 코드를 사용하여 기존 Runbook을 수정해야 합니다.  **알아두십시오** .     

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Azure 포털에서 새 자동화 계정 만들기
이 섹션에서는 다음 단계를 수행하여 Azure 포털에서 새 Azure 자동화 계정을 만듭니다.  이 작업은 실행 및 클래식 실행 계정을 만드는 것입니다.  

> [!NOTE]
> 이 단계를 수행하는 사용자는 *반드시* 구독 관리자 역할의 멤버이자 사용자에 대한 구독에 액세스를 부여하는 구독의 공동 관리자여야 합니다.  사용자는 또한 해당 구독 기본 Active Directory에 사용자로서 추가되어야 합니다. 그러나 계정이 권한 있는 역할에 할당될 필요는 없습니다.
> 
> 

1. 구독 관리자 역할의 멤버이자 구독의 공동 관리자인 계정으로 Azure 포털에 로그인합니다.
2. **자동화 계정**을 선택합니다.
3. 자동화 계정 블레이드에서 **추가**를 클릭합니다.<br>![자동화 계정 추가](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)
   
   > [!NOTE]
   > **Automation 계정 추가** 블레이드에 다음 경고가 표시되는 경우, 계정이 구독 관리자 역할의 구성원이자 구독의 공동 관리자가 아니기 때문입니다.<br>![자동화 계정 경고 추가](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   > 
   > 
4. **자동화 계정 추가** 블레이드의 **이름** 상자에 새 자동화 계정에 대한 이름을 입력합니다.
5. 구독이 둘 이상인 경우 새 계정의 구독을 지정하고 새로운 또는 기존 **리소스 그룹** 및 Azure 데이터 센터 **위치**를 지정합니다.
6. **Azure 실행 계정 만들기** 옵션에 **예** 값을 선택했는지 확인하고 **만들기** 단추를 클릭합니다.  
   
   > [!NOTE]
   > **아니요** 옵션을 선택하여 실행 계정을 만들지 않는 경우 **자동화 계정 추가** 블레이드에 경고 메시지가 나타납니다.  Azure 포털에서 계정이 생성되는 동안, 클래식 또는 Resource Manager 구독 디렉터리 서비스 내에 해당하는 인증 ID가 없으므로 구독의 리소스에 대한 액세스 권한도 없습니다.  이렇게 하면 이 계정을 참조하는 Runbook이 그러한 배포 모델의 리소스에 대해 작업을 인증하고 수행하지 못하도록 방지합니다.
   > 
   > ![자동화 계정 경고 추가](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br>
   >  서비스 주체가 만들어지지 않은 경우 참여자 역할은 할당되지 않습니다.
   > 
   > 
7. Azure에서 자동화 계정을 만드는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

### <a name="resources-included"></a>포함된 리소스
자동화 계정이 성공적으로 만들어지면 몇 가지 리소스가 자동으로 만들어집니다.  다음 표에는 실행 계정에 대한 리소스가 요약되어 있습니다.<br>

| 리소스 | 설명 |
| --- | --- |
| AzureAutomationTutorial Runbook |실행 계정을 사용하여 인증하고 Resource Manager 리소스를 모두 가져오는 방법을 보여주는 예제 PowerShell Runbook입니다. |
| AzureAutomationTutorialScript Runbook |실행 계정을 사용하여 인증하고 Resource Manager 리소스를 모두 가져오는 방법을 보여주는 예제 PowerShell Runbook입니다. |
| AzureRunAsCertificate |자동화 계정을 만드는 동안 또는 기존 계정에 대해 아래의 PowerShell 스크립트를 사용한 경우 생성되는 인증서 자산입니다.  Runbook에서 Azure Resource Manager 리소스를 관리할 수 있도록 Azure로 인증할 수 있도록 해줍니다.  이 인증서는 수명이 1년입니다. |
| AzureRunAsConnection |자동화 계정을 만드는 동안 또는 기존 계정에 대해 아래의 PowerShell 스크립트를 사용한 경우 생성되는 연결 자산입니다. |

다음 표에는 클래식 실행 계정에 대한 리소스가 요약되어 있습니다.<br>

| 리소스 | 설명 |
| --- | --- |
| AzureClassicAutomationTutorial Runbook |클래식 실행 계정(인증서)를 사용하여 구독의 모든 클래식 VM을 가져온 다음 VM 이름 및 상태를 출력하는 예제 Runbook입니다. |
| AzureClassicAutomationTutorial Script Runbook |클래식 실행 계정(인증서)를 사용하여 구독의 모든 클래식 VM을 가져온 다음 VM 이름 및 상태를 출력하는 예제 Runbook입니다. |
| AzureClassicRunAsCertificate |Runbook의 Azure 클래식 리소스를 관리할 수 있도록 Azure를 통해 인증하는 데 사용되는 자동 생성 인증서 자산입니다.  이 인증서는 수명이 1년입니다. |
| AzureClassicRunAsConnection |Runbook의 Azure 클래식 리소스를 관리할 수 있도록 Azure를 통해 인증하는 데 사용되는 자동 생성 연결 자산입니다. |

## <a name="verify-run-as-authentication"></a>실행 인증 확인
다음으로 작은 테스트를 수행하여 새 실행 계정을 사용하여 성공적으로 인증될 수 있는지 확인합니다.     

1. Azure 포털에서 이전에 만든 자동화 계정을 엽니다.  
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.
3. **AzureAutomationTutorialScript** Runbook을 선택한 다음 **시작**을 클릭하여 Runbook을 시작합니다.  Runbook을 시작할지 확인하는 메시지가 나타납니다.
4. [Runbook 작업](automation-runbook-execution.md) 이 만들어지고, 작업 블레이드가 표시되며, **작업 요약** 타일에 작업 상태가 표시됩니다.  
5. 작업 상태는 클라우드의 Runbook 작업자가 사용 가능해질 때까지 기다리고 있음을 나타내는 *대기 중* 으로 시작합니다. 작업자가 작업을 요구한 경우, *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.  
6. Runbook 작업이 완료되면 **완료됨** 상태가 나타나야 합니다.<br> ![보안 주체 Runbook 테스트](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Runbook의 자세한 결과를 보려면 **출력** 타일을 클릭합니다.
8. **출력** 블레이드에서 리소스 그룹에서 사용할 수 있는 모든 리소스의 목록이 성공적으로 인증되고 반환되는 것을 볼 수 있습니다.
9. **출력** 블레이드를 닫으면 **작업 요약** 블레이드로 돌아갑니다.
10. **작업 요약**과 해당 **AzureAutomationTutorialScript** Runbook 블레이드를 닫습니다.

## <a name="verify-classic-run-as-authentication"></a>클래식 실행 인증 확인
다음으로 작은 테스트를 수행하여 새 클래식 실행 계정을 사용하여 성공적으로 인증될 수 있는지 확인합니다.     

1. Azure 포털에서 이전에 만든 자동화 계정을 엽니다.  
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.
3. **AzureClassicAutomationTutorialScript** Runbook을 선택한 다음 **시작**을 클릭하여 Runbook을 시작합니다.  Runbook을 시작할지 확인하는 메시지가 나타납니다.
4. [Runbook 작업](automation-runbook-execution.md) 이 만들어지고, 작업 블레이드가 표시되며, **작업 요약** 타일에 작업 상태가 표시됩니다.  
5. 작업 상태는 클라우드의 Runbook 작업자가 사용 가능해질 때까지 기다리고 있음을 나타내는 *대기 중* 으로 시작합니다. 작업자가 작업을 요구한 경우, *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.  
6. Runbook 작업이 완료되면 **완료됨** 상태가 나타나야 합니다.<br> ![보안 주체 Runbook 테스트](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Runbook의 자세한 결과를 보려면 **출력** 타일을 클릭합니다.
8. **출력** 블레이드에서 구독의 모든 클래식 VM 목록이 성공적으로 인증되고 반환되는 것을 볼 수 있어야 합니다.
9. **출력** 블레이드를 닫으면 **작업 요약** 블레이드로 돌아갑니다.
10. **작업 요약**과 해당 **AzureClassicAutomationTutorialScript** Runbook 블레이드를 닫습니다.

## <a name="update-an-automation-account-using-powershell"></a>PowerShell을 사용하여 자동화 계정 업데이트
여기에서는 다음과 같은 경우에 기존 자동화 계정을 업데이트하기 위해 PowerShell을 사용하기 위한 옵션을 제공합니다.

1. 자동화 계정을 만들었지만 실행 계정 만들기를 거부되었습니다.
2. Resource Manager 리소스를 관리하기 위한 자동화 계정이 이미 있다면 Runbook 인증을 위한 실행 계정을 포함하도록 업데이트해야 합니다.
3. 클래식 리소스를 관리하기 위한 자동화 계정이 이미 있다면 새 계정을 만들고 Runbook 및 자산을 마이그레이션하는 대신 클래식 계정을 사용하여 업데이트해야 합니다.   

계속하기 전에 다음을 확인하세요.

1. Windows 7을 실행하는 경우 [WMF(Windows 관리 프레임워크) 4.0](https://www.microsoft.com/download/details.aspx?id=40855) 을 다운로드하고 설치했습니다.   
    Windows Server 2012 R2, Windows Server 2012, Windows 2008 R2, Windows 8.1 및 Windows 7 SP1을 실행하는 경우 [Windows 관리 프레임워크 5.0](https://www.microsoft.com/download/details.aspx?id=50395) 을 설치에 사용할 수 있습니다.
2. Azure PowerShell 1.0 이 릴리스에 대한 정보 및 설치 방법은 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.
3. 자동화 계정을 만들었습니다.  이 계정은 아래 두 스크립트에서 –AutomationAccountName 및 -ApplicationDisplayName 매개 변수에 대한 값으로 참조됩니다.

스크립트에 대한 필수 매개 변수인 *SubscriptionID*, *ResourceGroup* 및 *AutomationAccountName*에 대한 값을 가져오려면, Azure 포털에서 **자동화 계정** 블레이드의 자동화 계정을 선택하고 **모든 설정**을 선택합니다.  **계정 설정** 아래에 있는 **모든 설정** 블레이드에서 **속성**을 선택합니다.  **속성** 블레이드에서 이들 값을 기록할 수 있습니다.<br> ![자동화 계정 속성](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-run-as-account-powershell-script"></a>실행 계정 PowerShell 스크립트 만들기
아래 PowerShell 스크립트는 다음을 구성합니다.

* 자체 서명된 인증서로 인증되는 Azure AD 응용 프로그램은, Azure AD에서 이 응용 프로그램에 대한 서비스 주체 계정을 만들며, 현재 구독 내에 이 계정에 대한 참가자 역할(소유자 또는 다른 어떤 역할로든 변경 가능)을 할당합니다.  자세한 내용은 [Azure 자동화에서 역할 기반 액세스 제어](automation-role-based-access-control.md) 문서를 검토합니다.
* **AzureRunAsCertificate**라는 지정된 자동화 계정의 자동화 인증서 자산은 서비스 주체가 사용하는 인증서를 보유합니다.
* **AzureRunAsConnection**이라는 지정된 자동화 계정의 자동화 연결 자산은 applicationId, tenantId, subscriptionId 및 인증서 지문을 보유합니다.    

아래 단계는 스크립트 실행 프로세스를 안내합니다.

1. 컴퓨터에 다음 스크립트를 저장합니다.  이 예제에서는 파일 이름을 **New-AzureServicePrincipal.ps1**으로 저장합니다.  
   
        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,
   
        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,
   
        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,
   
        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,
   
        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,
   
        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )
   
        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        Select-AzureRmSubscription -SubscriptionId $SubscriptionId
   
        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
   
        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"
   
        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
   
        $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
        $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())
   
        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= $EndDate
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.Type = "AsymmetricX509Cert"
        $KeyCredential.Usage = "Verify"
        $KeyCredential.Value = $KeyValue
   
        # Use Key credentials
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential
   
        New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
        Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose
   
        $NewRole = $null
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
           Sleep 5
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           Sleep 10
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
   
        # Get the tenant id for this subscription
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
   
        # Create the automation resources
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose
   
        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        $ConnectionAssetName = "AzureRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
2. 사용자 컴퓨터의 **시작** 화면에서 관리자 권한으로 **Windows PowerShell**을 시작합니다.
3. 관리자 권한 PowerShell 명령줄 셸에서 1단계에서 만든 스크립트가 포함된 폴더로 이동하고 *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* 및 *-CertPlainPassword* 매개 변수에 대한 값을 변경하는 스크립트를 실행합니다.<br>
   
   > [!NOTE]
   > 스크립트를 실행한 후에 Azure 인증을 묻는 메시지가 나타납니다. 구독 관리자 역할의 멤버이자 구독의 공동 관리자인 계정으로 로그인해야 합니다.
   > 
   > 
   
        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"  
   <br>

스크립트가 성공적으로 완료되면 아래의 [샘플 코드](#sample-code-to-authenticate-with-resource-manager-resources) 를 참조하여 Resource Manager 리소스를 사용하여 인증하고 자격 증명 구성의 유효성을 검사합니다.

### <a name="create-classic-run-as-account-powershell-script"></a>클래식 실행 계정 PowerShell 스크립트 만들기
아래 PowerShell 스크립트는 다음을 구성합니다.

* **AzureClassicRunAsCertificate**라는 지정된 자동화 계정의 자동화 인증서 자산은 Runbook을 인증하는 데 사용되는 인증서를 보유합니다.
* **AzureClassicRunAsConnection**이라는 지정된 자동화 계정의 자동화 연결 자산은 구독 이름, subscriptionId 및 인증서 자산 이름을 보유합니다.

스크립트는 자체 서명된 관리 인증서를 만들고 PowerShell 세션을 실행하는 데 사용되는 사용자 프로필 아래에 있는 컴퓨터의 임시 파일 폴더 *%USERPROFILE%\AppData\Local\Temp*에 저장합니다.  스크립트를 실행한 후, 자동화 계정이 만들어진 구독을 위한 관리 저장소에 Azure 관리 인증서를 업로드해야 합니다.  아래 단계는 스크립트 실행 및 인증서 업로드 프로세스를 안내합니다.  

1. 컴퓨터에 다음 스크립트를 저장합니다.  이 예에서는 이를 **New-AzureClassicRunAsAccount.ps1**라는 파일 이름으로 저장합니다.
   
        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,
   
        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,
   
        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,
   
        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,
   
        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,
   
        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )
   
        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        $SubscriptionName = $subscription.Subscription.SubscriptionName
   
        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
        $CertPathCer = Join-Path $env:TEMP ($ApplicationDisplayName + ".cer")
   
        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"
   
        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose
   
        # Create the automation resources
        $ClassicCertificateAssetName = "AzureClassicRunAsCertificate"
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name $ClassicCertificateAssetName  -Password $CertPassword -Exportable | write-verbose
   
        # Create a Automation connection asset named AzureClassicRunAsConnection in the Automation account. This connection uses the ClassicCertificateAssetName.
        $ConnectionAssetName = "AzureClassicRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicCertificateAssetName}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureClassicCertificate -ConnectionFieldValues $ConnectionFieldValues
   
        Write-Host -ForegroundColor red "Please upload the cert $CertPathCer to the Management store by following the steps below."
        Write-Host -ForegroundColor red "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates."
        Write-Host -ForegroundColor red "Then click Upload and upload the certificate $CertPathCer"
2. 사용자 컴퓨터의 **시작** 화면에서 관리자 권한으로 **Windows PowerShell**을 시작합니다.  
3. 관리자 권한 PowerShell 명령줄 셸에서 1단계에서 만든 스크립트가 포함된 폴더로 이동하고 *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* 및 *-CertPlainPassword* 매개 변수에 대한 값을 변경하는 스크립트를 실행합니다.<br>
   
   > [!NOTE]
   > 스크립트를 실행한 후에 Azure 인증을 묻는 메시지가 나타납니다. 구독 관리자 역할의 멤버이자 구독의 공동 관리자인 계정으로 로그인해야 합니다.
   > 
   > 
   
        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"

스크립트가 성공적으로 완료되면 사용자 프로필 **Temp** 폴더에서 만들어진 인증서를 복사해야 합니다.  Azure 클래식 포털에 [관리 API 인증서 업로드](../azure-api-management-certs.md)하는 단계를 수행한 다음 [샘플 코드](#sample-code-to-authenticate-with-service-management-resources)를 참조하여 Service Management 리소스를 통해 자격 증명 구성의 유효성을 검사합니다.

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Resource Manager 리소스를 사용하여 인증하는 샘플 코드
Runbook으로 Resource Manager 리소스를 관리하는 실행 계정을 사용하여 인증하기 위해 **AzureAutomationTutorialScript** 예제 Runbook에서 가져온 아래 업데이트된 샘플 코드를 사용할 수 있습니다.   

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Logging in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }


스크립트에는 여러 구독 간에 쉽게 작업할 수 있도록 구독 컨텍스트를 참조하기 위해 지원되는 두 개의 코드 줄이 추가로 포함됩니다. SubscriptionId라는 변수 자산은 구독 ID를 포함하고 Add-AzureRmAccount cmdlet 문 뒤에 있는 [Set-AzureRmContext cmdlet](https://msdn.microsoft.com/library/mt619263.aspx) 은 매개 변수 집합 *-SubscriptionId*으로 지정됩니다. 변수 이름이 너무 일반적인 경우 변수의 이름을 수정하여 용도에 맞게 식별하기 쉽도록 접두사 또는 다른 명명 규칙을 포함할 수 있습니다. 또한 해당하는 변수 자산이 있는 -SubscriptionId 대신 set -SubscriptionName 매개 변수를 사용할 수 있습니다.  

Runbook - **Add-AzureRmAccount**에서 인증에 사용 되는 cmdlet는 *ServicePrincipalCertificate* 매개 변수 집합을 사용합니다.  이것은 자격 증명이 아니라 서비스 주체 인증서를 사용하여 인증합니다.  

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Service Management 리소스를 사용하여 인증하는 샘플 코드
Runbook으로 클래식 리소스를 관리하는 클래식 실행 계정을 사용하여 인증하기 위해 **AzureClassicAutomationTutorialScript** 예제 Runbook에서 가져온 아래 업데이트된 샘플 코드를 사용할 수 있습니다.

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID


## <a name="next-steps"></a>다음 단계
* 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](../active-directory/active-directory-application-objects.md)를 참조합니다.
* Azure 자동화의 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 자동화에서 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.
* 인증서 및 Azure 서비스에 대한 자세한 내용은 [Azure 클라우드 서비스 인증서 개요](../cloud-services/cloud-services-certs-create.md)




<!--HONumber=Dec16_HO2-->


