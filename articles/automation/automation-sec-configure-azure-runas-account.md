<properties
    pageTitle="Azure 실행 계정 구성 | Microsoft Azure"
    description="Azure 자동화에서 보안 주체 인증을 만들고 테스트하며 예제를 사용하는 과정을 안내하는 자습서입니다."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/20/2016"
    ms.author="magoedte"/>

# Azure 실행 계정으로 Runbook 인증
이 항목에서는 자동화 Runbook으로 구독에서 ARM(Azure Resource Manager) 리소스에 액세스하기 위해 새 실행 계정 기능(서비스 주체라고도 함)을 사용하여 Azure 포털에서 자동화 계정을 구성하는 방법을 보여줍니다. Azure 포털에서 새 자동화 계정을 만드는 경우 이 계정은 자동으로 새 서비스 주체를 만들고 기본적으로 구독에서 참가자 RBAC(역할 기반 액세스 제어) 역할에 할당됩니다. 이를 통해 처리를 간편하게 만들고 자동화 요구를 지원하는 Runbook의 구축 및 배포를 신속하게 시작할 수 있습니다.

서비스 주체를 사용하여 다음을 수행할 수 있습니다.

* Runbook을 사용하여 Azure ARM 리소스를 관리하는 경우 Azure로 인증하는 표준화된 방법을 제공합니다.
* Azure 경고에 구성된 글로벌 Runbook의 사용 자동화


>[AZURE.NOTE] 자동화 글로벌 Runbook을 포함한 Azure [경고 통합 기능](../azure-portal/insights-receive-alert-notifications.md)에는 서비스 주체로 구성된 자동화 계정이 필요합니다. 정의된 서비스 주체 사용자가 있는 자동화 계정을 선택하거나 새로 하나를 만들도록 선택할 수 있습니다.



Azure 포털에서 자동화 계정을 만들고 Azure PowerShell을 사용하여 실행 계정이 있는 계정을 업데이트하는 방법 및 Runbook에서 서비스 주체를 인증하는 방법을 보여줍니다.

## Azure 포털에서 새 자동화 계정 만들기
이 섹션에서는 다음 단계를 수행하여 Azure 포털에서 새 Azure 자동화 계정 및 서비스 주체를 만듭니다.

>[AZURE.NOTE] 이 단계를 수행하는 사용자는 구독 관리자 역할의 멤버*여야* 합니다.

1. 관리하려는 Azure 구독의 서비스 관리자 권한으로 Azure 포털에 로그인합니다.
2. **자동화 계정**을 선택합니다.
3. 자동화 계정 블레이드에서 **추가**를 클릭합니다.<br>![자동화 계정 추가](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. **자동화 계정 추가** 블레이드의 **이름** 상자에 새 자동화 계정에 대한 이름을 입력합니다.
5. 구독이 둘 이상인 경우 새 계정에 대해 하나를 지정하고 새로운 또는 기존 **리소스 그룹** 및 Azure 데이터 센터 **위치**를 지정합니다.
6. **Azure 실행 계정 만들기** 옵션에 **예** 값을 선택했는지 확인하고 **만들기** 단추를 클릭합니다.  

    ![자동화 계정 경고 추가](media/automation-sec-configure-azure-runas-account/add-account-decline-create-runas-msg.png)

    >[AZURE.NOTE] **아니요** 옵션을 선택하여 실행 계정을 만들지 않도록 선택하는 경우 **자동화 계정 추가** 블레이드에 경고 메시지가 나타납니다. 계정이 생성되고 구독의 **참가자** 역할에 할당되는 동안 구독 디렉터리 서비스 내에서 해당하는 인증 ID가 없고 따라서 구독의 리소스에 대한 액세스 권한도 없습니다. 이렇게 하면 이 계정을 참조하는 Runbook이 ARM 리소스에 대해 인증되고 작업을 수행하지 못하도록 방지합니다.

    ![자동화 계정 경고 추가](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

    >[AZURE.NOTE] **만들기** 단추를 클릭한 후에 사용 권한 거부 오류 메시지를 수신하는 경우 계정이 구독 관리자 역할의 멤버가 아니기 때문입니다.

7. Azure에서 자동화 계정을 만드는 동안 메뉴에서 **알림**의 진행률을 추적할 수 있습니다.

완료될 경우 자동화 계정은 1년 수명인 **AzureRunAsCertificate**라는 인증서 자산 및 **AzureRunAsConnection**이라는 연결 자산으로 만들어집니다.

## PowerShell을 사용하여 자동화 계정 업데이트
다음 절차는 기존의 자동화 계정을 업데이트하고 PowerShell을 사용하는 서비스 주체를 만듭니다. 계정을 만들었지만 실행 계정 만들기를 거부한 경우 이 절차가 필요합니다.

계속하기 전에 다음을 확인하세요.

1. [Windows PowerShell용 Azure Active Directory 모듈(64비트 버전)](http://go.microsoft.com/fwlink/p/?linkid=236297)을 다운로드하고 설치했습니다.
2. 자동화 계정을 만들었습니다. 이 계정은 아래 스크립트에서 –AutomationAccountName 및 -ApplicationDisplayName 매개 변수에 대한 값으로 참조됩니다.
3. [Azure 자동화 작성 도구 키트](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.2)를 설치했습니다.

```
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser
```

PowerShell 스크립트는 다음을 구성합니다.

* 자체 서명된 인증서로 인증되는 Azure AD 응용 프로그램은, Azure AD에서 이 응용 프로그램에 대한 서비스 주체 계정을 만들며, 현재 구독 내에 이 계정에 대한 참가자 역할(소유자 또는 다른 어떤 역할로든 변경 가능)을 할당합니다. 보다 자세한 정보는 [Azure 자동화에서 역할 기반 액세스 제어](../automation/automation-role-based-access-control.md) 문서를 검토합니다.  
* **AzureRunAsCertificate**라는 지정된 자동화 계정의 자동화 인증서 자산은 서비스 주체에서 사용되는 인증서를 보유합니다.
* **AzureRunAsConnection**이라는 지정된 자동화 계정의 자동화 연결 자산은 applicationId, tenantId, subscriptionId 및 인증서 지문을 보유합니다.  


### PowerShell 스크립트 실행

1. 컴퓨터에 다음 스크립트를 저장합니다. 이 예제에서는 파일 이름을 **New-AzureServicePrincipal.ps1**로 저장합니다.  

    ```
    #Requires - RunAsAdministrator
    Param (
    [Parameter(Mandatory=$true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory=$true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory=$true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory=$true)]
    [String] $CertPlainPassword,

    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )

    Login-AzureRmAccount
    Import-Module AzureRM.Resources

    $CurrentDate = Get-Date
    $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
    $KeyId = (New-Guid).Guid
    $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")

    $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

    $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

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
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    $SubscriptionId = $SubscriptionInfo | Select SubscriptionId -First 1
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
    ```
<br>
2. 사용자 컴퓨터에서 관리자 권한으로 **시작** 화면에서 **Windows PowerShell**을 시작합니다.
3. 관리자 권한 PowerShell 명령줄 셸에서 1단계에서 만든 스크립트를 포함하는 폴더로 이동하고 *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName* 및 *-CertPlainPassword* 매개 변수에 대한 값을 변경하는 스크립트를 실행합니다.<br>

    ```
    .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> `
     -AutomationAccountName <NameofAutomationAccount> `
     -ApplicationDisplayName <DisplayNameofAutomationAccount> `
     -CertPlainPassword "<StrongPassword>"
    ```   
<br>

    >[AZURE.NOTE] 스크립트를 실행한 후에 Azure 인증을 묻는 메시지가 나타납니다. 구독의 서비스 관리자 계정으로 로그인*해야* 합니다. <br>
4. 스크립트가 성공적으로 완료된 후에 다음 단계로 진행하여 새 자격 증명 구성을 테스트하고 확인합니다.

### 인증 확인
다음으로 작은 테스트를 수행하여 새 서비스 주체를 사용하여 성공적으로 인증될 수 있는지 확인합니다. 성공적으로 인증할 수 없는 경우 1단계로 돌아가서 다시 이전 단계 각각을 확인합니다.

1. Azure 포털에서 이전에 만든 자동화 계정을 엽니다.  
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.
3. **Runbook 추가** 단추를 클릭하여 새 Runbook을 만들고 **Runbook 추가** 블레이드에서 **새 Runbook 만들기**를 선택합니다.
4. Runbook을 *Test-SecPrin-Runbook*으로 명명하고 **Runbook 유형**에 PowerShell을 선택합니다. **만들기**를 클릭하여 Runbook을 만듭니다.
5. **PowerShell Runbook 편집** 블레이드에서 캔버스에 다음 코드를 붙여넣습니다.<br>

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection `
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```  
<br>
6. **저장**을 클릭하여 Runbook을 저장합니다.
7. **테스트** 블레이드를 열려면 **테스트 창**을 클릭합니다.
8. **시작**을 클릭하여 테스트를 시작합니다.
9. 이 창에서 [Runbook 작업](automation-runbook-execution.md)이 생성되고 해당 상태가 표시됩니다.  
10. 작업 상태는 클라우드의 Runbook 작업자가 사용 가능해질 때까지 기다리고 있음을 나타내는 *대기 중*으로 시작합니다. 작업자가 작업을 요구한 경우, *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.  
11. Runbook 작업이 완료되면 해당 출력이 표시됩니다. 이 경우에 **완료됨**이라는 상태를 확인해야 합니다.<br> ![보안 주체 Runbook 테스트](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br>
12. 캔버스로 돌아가려면 **테스트** 블레이드를 닫습니다.
13. **PowerShell Runbook 편집** 블레이드를 닫습니다.
14. **Test-SecPrin-Runbook** 블레이드를 닫습니다.

새 계정을 제대로 설치했는지를 확인하는 데 사용된 위의 코드는 Azure 자동화에서 인증하기 위해 PowerShell Runbook에서 사용되어 ARM 리소스를 관리합니다. 물론 현재 사용 중인 자동화 계정으로 인증을 계속할 수 있습니다.

## 다음 단계
- 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](../active-directory/active-directory-application-objects.md)를 참조합니다.
- Azure 자동화의 역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 자동화에서 역할 기반 액세스 제어](../automation/automation-role-based-access-control.md)를 참조하십시오.

<!---HONumber=AcomDC_0427_2016-->