---
title: Azure Automation 실행 계정 관리
description: 이 문서에서는 PowerShell 또는 포털에서 실행 계정을 관리하는 방법에 대해 설명합니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 8d7d0baacd5f702e8f435ab440eaf0338a60f4cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500762"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Azure Automation 실행 계정 관리

Azure 자동화에서 계정으로 실행 Azure cmdlets를 사용 하 여 Azure에서 리소스를 관리 하기 위한 인증을 제공 합니다. Run As 계정을 만들 때 Azure Active Directory(AD)에서 새 서비스 주체 사용자를 만들고 구독 수준에서 이 사용자에게 기여자 역할을 할당합니다. Azure 가상 머신에서 Hybrid Runbook Worker를 사용하는 Runbook의 경우 실행 계정 대신 [Azure 리소스에 대한 관리 ID](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)를 사용하여 Azure 리소스를 인증할 수 있습니다.

계정으로 실행에 대 한 서비스 주체는 기본적으로 Azure AD를 읽을 수 있는 권한이 없습니다. Azure AD를 읽거나 관리할 수 있는 권한을 추가하려면 **API 권한아래**서비스 주체에 대한 권한을 부여해야 합니다. 자세한 내용은 [웹 API에 액세스할 수 있는 권한 추가를](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)참조하십시오.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="types-of-run-as-accounts"></a>계정으로 실행 유형

Azure 자동화는 두 가지 유형의 Run As 계정을 사용합니다.

* Azure 계정으로 실행
* Azure 클래식 실행 계정으로 실행

>[!NOTE]
>CSP(Azure 클라우드 솔루션 공급자) 구독은 Azure 리소스 관리자 모델만 지원합니다. 비 Azure 리소스 관리자 서비스는 프로그램에서 사용할 수 없습니다. CSP 구독을 사용하는 경우 Azure 클래식 실행 로 계정이 만들어지지 않지만 Azure 실행 As 계정이 만들어집니다. CSP 구독에 대해 자세히 알아보려면 [CSP 구독에서 사용 가능한 서비스](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)를 참조하세요.

### <a name="run-as-account"></a>실행 계정

실행 As 계정은 [리소스 관리자 배포 모델](../azure-resource-manager/management/deployment-models.md) 리소스를 관리합니다. 다음 작업을 수행합니다.

* 자체 서명된 인증서로 Azure AD 애플리케이션을 만들고, Azure AD에 애플리케이션의 서비스 주체 계정을 만들며, 현재 구독에 있는 계정에 대해 기여자 역할을 할당합니다. 인증서 설정을 소유자 또는 다른 역할로 변경할 수 있습니다. 자세한 내용은 [Azure 자동화의 역할 기반 액세스 제어를](automation-role-based-access-control.md)참조하십시오.
  
* 지정된 자동화 계정에 `AzureRunAsCertificate` 명명된 자동화 인증서 자산을 만듭니다. 인증서 자산에는 Azure AD 응용 프로그램에서 사용하는 인증서 개인 키가 있습니다.
  
* 지정된 자동화 계정에 `AzureRunAsConnection` 명명된 자동화 연결 자산을 만듭니다. 연결 자산에는 응용 프로그램 ID, 테넌트 ID, 구독 ID 및 인증서 지문이 있습니다.

### <a name="azure-classic-run-as-account"></a>Azure 클래식 실행 계정

Azure 클래식 실행 As 계정은 [클래식 배포 모델](../azure-resource-manager/management/deployment-models.md) 리소스를 관리합니다. 이 유형의 계정을 만들거나 갱신하려면 구독의 공동 관리자여야 합니다.

Azure 클래식 실행 로 계정은 다음 작업을 수행합니다.

  * 구독에서 관리 인증서를 만듭니다.

  * 지정된 자동화 계정에 `AzureClassicRunAsCertificate` 명명된 자동화 인증서 자산을 만듭니다. 인증서 자산은 관리 인증서에서 사용되는 인증서 프라이빗 키를 보유합니다.

  * 지정된 자동화 계정에 `AzureClassicRunAsConnection` 명명된 자동화 연결 자산을 만듭니다. 연결 자산에는 구독 이름, 구독 ID 및 인증서 자산 이름이 있습니다.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>계정 권한으로 실행

이 섹션에서는 일반 실행 계정 및 클래식 실행 계정 모두에 대한 사용 권한을 정의합니다.

### <a name="permissions-to-configure-run-as-accounts"></a>실행 계정 구성 권한

실행 계정을 만들거나 업데이트하려면 특정 권한이 있어야 합니다. Azure Active Directory의 응용 프로그램 관리자와 구독의 소유자는 모든 작업을 완료할 수 있습니다. 업무가 분리된 경우 다음 표에는 필요한 작업, 이에 상응하는 cmdlet 및 사용 권한 목록이 표시됩니다.

|Task|Cmdlet  |최소 권한  |권한을 설정하는 위치|
|---|---------|---------|---|
|Azure AD 애플리케이션 만들기|[새-AzAD응용 프로그램](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | 응용 프로그램 개발자 역할<sup>1</sup>        |[Azure 광고](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>홈 > Azure AD > 앱 등록 |
|애플리케이션에 자격 증명을 추가합니다.|[새로운 아자드앱 자격 증명](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | 응용 프로그램 관리자 또는 글로벌 관리자<sup>1</sup>         |[Azure 광고](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>홈 > Azure AD > 앱 등록|
|Azure AD 서비스 주체 만들기 및 받기|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[Get-AzAD서비스교장](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | 응용 프로그램 관리자 또는 글로벌 관리자<sup>1</sup>        |[Azure 광고](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>홈 > Azure AD > 앱 등록|
|지정된 보안 주체의 RBAC 역할 할당 또는 가져오기|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | 사용자 액세스 관리자 또는 소유자 또는 다음 권한이 있습니다.</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [구독](../role-based-access-control/role-assignments-portal.md)</br>홈 > 구독 > \<구독 이름\> - 액세스 제어(IAM)|
|Automation 인증서 생성 또는 제거|[뉴 아즈자동화 인증서](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[제거-아즈자동화 인증서](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | 리소스 그룹에 대한 기여자         |자동화 계정 리소스 그룹|
|Automation 연결 생성 또는 제거|[새로운 아즈오토메이션커넥션](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[제거-아즈오토메이션커넥션](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|리소스 그룹에 대한 기여자 |자동화 계정 리소스 그룹|

<sup>1</sup> Azure AD 테넌트의 비관리자 사용자는 Azure AD 테넌트의 사용자가 사용자 설정 페이지에서 응용 프로그램 옵션을 등록할 수 있는 경우 [AD 응용](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) 프로그램을 **등록할 수** **있습니다.** 응용 프로그램 등록 설정이 **아니요인**경우 이 작업을 수행하는 사용자는 이 테이블에 정의된 사용자여야 합니다.

구독의 글로벌 관리자 역할에 추가하기 전에 구독의 Active Directory 인스턴스의 구성원이 아닌 경우 게스트로 추가됩니다. 이 경우에는 Automation 계정 추가 페이지에 `You do not have permissions to create…` 경고가 표시됩니다. 

글로벌 관리자 역할이 할당될 때 구독의 Active Directory 인스턴스의 구성원인 경우 `You do not have permissions to create…` 자동화 계정 추가 페이지에서 경고를 받을 수도 있습니다. 이 경우 구독의 Active Directory 인스턴스에서 제거를 요청한 다음 다시 추가하도록 요청하여 Active Directory에서 전체 사용자가 될 수 있습니다.

오류 메시지를 생성하는 상황이 해결되었는지 확인하려면 다음을 수행하십시오.

1. Azure 포털의 Azure Active Directory 창에서 **사용자 및 그룹을**선택합니다. 
2. **모든 사용자**를 선택합니다.
3. 이름을 선택한 다음 **프로필**을 선택합니다. 
4. 사용자 프로필 아래의 **사용자 유형** 특성 값이 **게스트로**설정되지 않았는지 확인합니다.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>클래식 실행을 계정으로 구성하는 권한

클래식 실행 As 계정을 구성하거나 갱신하려면 구독 수준에서 공동 관리자 역할이 있어야 합니다. 클래식 구독 권한에 대한 자세한 내용은 [Azure 클래식 구독 관리자를](../role-based-access-control/classic-administrators.md#add-a-co-administrator)참조하십시오.

## <a name="creating-a-run-as-account-in-azure-portal"></a>Azure 포털에서 계정으로 실행 만들기

다음 단계를 수행하여 Azure Portal에서 Azure 자동화 계정을 업데이트합니다. 계정으로 실행 및 클래식 실행을 개별적으로 만듭니다. 클래식 리소스를 관리할 필요가 없으면 Azure 실행 계정만 만들면 됩니다.

1. 구독 관리자 역할의 멤버이자 구독의 공동 관리자인 계정으로 Azure 포털에 로그인합니다.
2. **자동화 계정을**검색하고 선택합니다.
3. 자동화 계정 페이지에서 목록에서 자동화 계정을 선택합니다.
4. 왼쪽 창에서 계정 설정 섹션에서 **계정으로 실행을** 선택합니다.
5. 필요한 계정에 따라 **Azure 실행 계정** 또는 **Azure 클래식 실행 계정**을 선택합니다. 
6. 관심 계정에 따라 **Azure 실행 추가를** 사용하거나 **Azure 클래식 실행을 계정으로 추가합니다.** 개요 정보를 검토한 후 에서 **만들기를**클릭합니다.
6. Azure에서 Automation 계정을 만드는 동안 메뉴의 **알림** 아래에서 진행 상황을 추적할 수 있습니다. 계정이 생성되고 있음을 알리는 배너도 표시됩니다. 이 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="creating-a-run-as-account-using-powershell"></a>PowerShell을 사용하여 계정으로 실행 만들기

다음 목록은 PowerShell에서 실행 As 계정을 만드는 데 대한 요구 사항을 제공합니다. 이러한 요구 사항은 두 유형의 Run As 계정에 모두 적용됩니다.

* Azure Resource Manager 모듈 3.4.1 이상이 설치된 Windows 10 또는 Windows Server 2016. PowerShell 스크립트는 이전 버전의 Windows를 지원하지 않습니다.
* Azure PowerShell 1.0 이상 PowerShell 1.0 릴리스에 대한 정보는 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.
* `AutomationAccountName` 및 `ApplicationDisplayName` 매개 변수의 값으로 참조되는 자동화 계정입니다.
* [계정으로 실행을 구성하기 위해 필수 권한에](#permissions)나열된 권한과 동일한 권한입니다.

에 대한 `SubscriptionId` `ResourceGroupName`값을 얻으려면 PowerShell 스크립트에 필요한 매개 변수인 에 대한 다음 단계를 완료합니다.

1. Azure 포털에서 **자동화 계정**.
1. 자동화 계정 페이지에서 자동화 계정을 선택합니다.
1. 계정 설정 섹션에서 **속성**을 선택합니다.
1. 속성 페이지에서 **이름,** **구독 ID**및 **리소스 그룹의** 값을 기록합니다. 이러한 값은 각각 `AutomationAccountName`의 `SubscriptionId`, `ResourceGroupName` 및 PowerShell 스크립트 매개 변수의 값에 해당합니다.

   ![자동화 계정 속성 페이지](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>PowerShell 스크립트를 사용하여 계정으로 실행

이 섹션에서는 Run As 계정을 만드는 PowerShell 스크립트를 제공합니다. 스크립트에는 여러 구성에 대한 지원이 포함되어 있습니다.

* 자체 서명된 인증서를 사용하여 실행 계정을 만듭니다.
* 자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만듭니다.
* 엔터프라이즈 CA(인증 기관)에서 발급한 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만듭니다.
* Azure Government 클라우드에서 자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만듭니다.

이 스크립트는 여러 Azure Resource Manager cmdlet을 사용하여 리소스를 만듭니다. cmdlet 및 필요한 권한에 대 한 사용 권한 은 [계정으로 실행을 구성할 수 있는 권한을](#permissions-to-configure-run-as-accounts)참조 합니다.

파일 이름 **New-RunAsAccount.ps1을**사용하여 컴퓨터에 스크립트를 저장합니다.

```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory = $false)]
        [string] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",

        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )

    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }

    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
        Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRm.Profile
    Import-Module AzureRm.Resources

    $AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
    if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzAccount -Environment $EnvironmentName
    $Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"

    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }

    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"

        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
```

>[!NOTE]
>`Add-AzAccount``Add-AzureRMAccount` [및 Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). 이러한 cmdlet을 사용하거나 자동화 계정의 모듈을 최신 버전으로 [업데이트할](automation-update-azure-modules.md) 수 있습니다. 새 자동화 계정을 방금 만든 경우에도 모듈을 업데이트해야 할 수 있습니다.

### <a name="execute-the-powershell-script"></a>PowerShell 스크립트 실행

1. 사용자 컴퓨터의 **시작** 화면에서 관리자 권한으로 **Windows PowerShell**을 시작합니다.
1. 상승된 명령줄 셸에서 스크립트가 포함된 폴더로 이동합니다.
1. 필요한 구성에 대한 매개 변수 값을 사용하여 스크립트를 실행합니다.
1. 스크립트가 실행된 후 클래식 실행 As 계정을 만드는 경우 자동화 계정이 만들어진 구독의 관리 저장소에 공용**인증서(.cer** 파일 이름 확장자)를 업로드합니다.

스크립트가 실행되면 Azure를 사용하여 인증하라는 메시지가 표시됩니다. 구독 관리자 역할및 구독의 공동 관리자인 계정으로 로그인합니다.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>자체 서명된 인증서를 사용하여 실행 As 계정 만들기

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정 만들기

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>엔터프라이즈 인증서를 사용하여 실행 계정 및 클래식 실행 계정 만들기

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

엔터프라이즈 공용**인증서(.cer** 파일)가 있는 클래식 실행 As 계정을 만든 경우 이 인증서를 사용합니다. [Azure 포털에 관리 API 인증서 업로드](../azure-api-management-certs.md)를 참조하십시오.

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Azure Government 클라우드에서 자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정 만들기

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

자체 서명된 공용**인증서(.cer** 파일)가 있는 클래식 실행 As 계정을 만든 경우 스크립트는 이를 만들고 컴퓨터의 임시 파일 폴더에 저장합니다. PowerShell 세션을 실행하는 데 `%USERPROFILE%\AppData\Local\Temp`사용한 사용자 프로필에서 찾을 수 있습니다.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>계정으로 실행 또는 클래식 실행을 삭제

이 섹션에서는 실행 로 또는 클래식 실행 으로 계정을 삭제 하는 방법을 설명 합니다. 이 작업을 실행하는 경우 Automation 계정이 보존됩니다. 계정을 삭제한 후 Azure Portal에서 계정을 다시 만들 수 있습니다.

1. Azure Portal에서 Automation 계정을 엽니다.

2. 왼쪽 창에서 계정 설정 섹션에서 **계정으로 실행을** 선택합니다.

3. 실행 계정 속성 페이지에서 삭제하려는 실행 계정 또는 클래식 실행 계정을 선택합니다. 

4. 선택한 계정의 속성 창에서 **삭제를**클릭합니다.

   ![실행 계정 삭제](media/manage-runas-account/automation-account-delete-runas.png)

5. 계정이 삭제되는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

6. 계정이 삭제되면 실행 계정 속성 페이지에서 **Azure 실행 계정** 만들기 옵션을 선택하여 계정을 다시 만들 수 있습니다.

   ![Automation 실행 계정 다시 만들기](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>자체 서명된 인증서 갱신

Run As 계정에 대해 만든 자체 서명된 인증서는 생성일로부터 1년 만료됩니다. 실행 As 계정이 만료되기 전에 인증서를 갱신해야 합니다. 만료되기 전에 언제든지 갱신할 수 있습니다. 

자체 서명된 인증서를 갱신하면 현재 유효한 인증서가 유지되어 큐에 대기 중이거나 활발하게 실행중인 Runbook및 Run As 계정으로 인증되는 모든 런북이 부정적인 영향을 받지 않도록 합니다. 인증서는 만료 날짜까지 유효합니다.

>[!NOTE]
>실행 As 계정이 손상되었다고 생각되면 자체 서명된 인증서를 삭제하고 다시 만들 수 있습니다.

>[!NOTE]
>엔터프라이즈 인증서 기관에서 발급한 인증서를 사용하도록 Run As 계정을 구성하고 이 옵션을 사용하여 자체 서명된 인증서 옵션을 갱신하면 엔터프라이즈 인증서가 자체 서명된 인증서로 대체됩니다.

다음 단계를 사용하여 자체 서명된 인증서를 갱신합니다.

1. Azure Portal에서 Automation 계정을 엽니다.

1. 계정 설정 섹션에서 **거래처로 실행을** 선택합니다.

    ![Automation 계정 속성 창](media/manage-runas-account/automation-account-properties-pane.png)

1. 계정으로 실행 속성 페이지에서 인증서를 갱신할 계정으로 실행 또는 클래식 실행 계정 중 하나를 선택합니다.

1. 선택한 계정의 속성 창에서 **인증서 갱신을**클릭합니다.

    ![실행 계정용 인증서 갱신](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. 인증서가 갱신되는 동안 메뉴의 **알림**에서 진행률을 추적할 수 있습니다.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>자동화 Runbook을 통해 자동 인증서 갱신 설정

인증서를 자동으로 갱신하려면 자동화 Runbook을 사용할 수 있습니다. [GitHub의](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) 이 스크립트는 자동화 계정에서 이 기능을 사용할 수 있습니다.

>[!NOTE]
>스크립트를 실행하려면 Azure AD의 글로벌 관리자 또는 회사 관리자여야 합니다.

이 스크립트는 Run As 계정 인증서를 갱신하는 주간 일정을 만듭니다. 자동화 계정에 **업데이트-AutomationRunAsCredential** 실행책을 추가합니다. 스크립트 [업데이트-AutomationRunAsCredential.ps1에서](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1)GitHub에서 Runbook 코드를 볼 수 있습니다. 필요에 따라 파일의 PowerShell 코드를 사용하여 인증서를 수동으로 갱신할 수 있습니다.

다음 단계를 사용하여 갱신 프로세스를 즉시 테스트합니다.

1. **Update-AutomationRunAsCredential** 실행북을 편집하고 **종료(1)** 명령 앞에 122줄에 주석 문자(#)를 배치합니다.

   ```powershell
   #Exit(1)
   ```

2. Runbook을 게시합니다.
3. Runbook을 시작합니다.
4. 다음 코드로 성공적인 갱신을 확인합니다.

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    출력:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. 테스트가 끝나면 Runbook을 편집하고 1단계에서 추가한 주석 문자를 제거합니다.
6. Runbook을 게시합니다.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>실행 계정 권한 제한

Azure의 리소스에 대한 자동화 대상을 제어하려면 [업데이트-AutomationRunAsAccountRoleAssignments.ps1 스크립트를](https://aka.ms/AA5hug8) 실행할 수 있습니다. 이 스크립트는 사용자 지정 역할 정의를 만들고 사용하기 위해 기존 Run As 계정 서비스 주체를 변경합니다. 역할에 [키 자격 증명 모음을](https://docs.microsoft.com/azure/key-vault/)제외한 모든 리소스에 대한 사용 권한이 있습니다.

>[!IMPORTANT]
>**업데이트-AutomationRunAsAccountRoleAssignments.ps1** 스크립트를 실행한 후 Run As 계정을 사용하여 키 볼트에 액세스하는 런북은 더 이상 작동하지 않습니다. 스크립트를 실행하기 전에 계정의 Runbook에서 Azure Key Vault에 대한 호출을 검토해야 합니다. Azure 자동화 실행 책에서 키 볼트에 대한 액세스를 활성화하려면 [키 볼트의 권한에 계정으로 실행을 추가해야 합니다.](#add-permissions-to-key-vault)

서비스 주체실행 주체가 수행할 수 있는 작업을 추가로 제한해야 `NotActions` 하는 경우 사용자 지정 역할 정의의 요소에 다른 리소스 형식을 추가할 수 있습니다. 다음 예제는 에 `Microsoft.Compute/*`대한 액세스를 제한합니다. 역할 정의에 `NotActions` 대해 이 리소스 유형을 추가하면 역할은 Compute 리소스에 액세스할 수 없습니다. 역할 정의에 대해 자세히 알아보려면 [Azure 리소스에 대한 역할 정의 이해하기를](../role-based-access-control/role-definitions.md)참조하십시오.

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Run As 계정에서 사용하는 서비스 주체가 기여자 역할 정의 또는 사용자 지정 계정에 있는지 확인할 수 있습니다. 

1. 자동화 계정으로 이동하여 계정 설정 섹션에서 **계정으로 실행을** 선택합니다.
2. **계정으로 Azure 실행을 선택합니다.** 
3. **역할을** 선택하여 사용 중인 역할 정의를 찾습니다.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

여러 구독 또는 자동화 계정에 대해 Run As 계정에서 사용하는 역할 정의를 결정할 수도 있습니다. 파워쉘 갤러리에서 [확인 자동화RunAsAccountRole할당.ps1](https://aka.ms/AA5hug5) 스크립트를 사용하여 이 작업을 수행합니다.

### <a name="add-permissions-to-key-vault"></a>키 자격 증명 모음에 사용 권한 추가

Azure 자동화를 허용하여 키 자격 증명 모음 및 계정 서비스 주체가 사용자 지정 역할 정의를 사용하고 있는지 확인할 수 있습니다. 다음이 필요합니다.

* 키 볼트에 권한을 부여합니다.
* 액세스 정책을 설정합니다.

PowerShell 갤러리에서 [확장 자동화RunAsAccountRoleToAki.ps1](https://aka.ms/AA5hugb) 스크립트를 사용하여 키 자격 증명 모음에 대한 계정 사용 권한을 부여할 수 있습니다. 키 자격 증명 모음에 대한 사용 권한 설정에 대한 자세한 내용은 [키 자격 증명 모음에](../key-vault/key-vault-group-permissions-for-apps.md) 대한 권한 부여 액세스를 참조하십시오.

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>계정으로 실행의 잘못된 구성 문제 해결

실행 됨 또는 클래식 실행 로 계정에 필요한 일부 구성 항목이 초기 설정 중에 삭제되거나 잘못 생성되었을 수 있습니다. 잘못된 구성의 가능한 인스턴스는 다음과 같습니다.

* 인증서 자산
* 연결 자산
* 기고자 역할에서 계정이 제거됨으로 실행
* Azure AD의 서비스 주체 또는 애플리케이션

이러한 잘못된 구성 인스턴스의 경우 자동화 계정은 변경 내용을 `Incomplete` 감지하고 계정의 계정으로 실행 속성 창에 상태를 표시합니다.

![불완전 실행 계정 구성 상태](media/manage-runas-account/automation-account-runas-incomplete-config.png)

계정으로 실행을 선택하면 계정 속성 창에 다음과 같은 오류 메시지가 표시됩니다.

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

계정을 삭제하고 다시 만들어서 이러한 실행 계정 문제를 신속하게 해결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체를](../active-directory/develop/app-objects-and-service-principals.md)참조하십시오.
* 인증서 및 Azure 서비스에 대한 자세한 내용은 [Azure Cloud Services 인증서 개요](../cloud-services/cloud-services-certs-create.md)를 참조하세요.
