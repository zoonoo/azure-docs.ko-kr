---
title: Azure 자동화에서 Azure AD를 사용하여 Azure에 인증
description: Azure 자동화 내에서 Azure AD를 Azure 인증 공급자로 사용하는 방법을 알아봅니다.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548331"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Azure 자동화에서 Azure AD를 사용하여 Azure에 인증

[Azure Active Directory(AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 서비스를 사용하면 사용자 관리, 도메인 관리 및 단일 사인온 구성과 같은 여러 관리 작업을 수행할 수 있습니다. 이 문서에서는 Azure 자동화 내에서 Azure AD를 Azure 인증을 위한 공급자로 사용하는 방법을 설명합니다. 

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="installing-azure-ad-modules"></a>Azure AD 모듈 설치

다음 PowerShell 모듈을 통해 Azure AD를 활성화할 수 있습니다.

* 그래프용 Azure Active Directory PowerShell(AzureRM 및 Az 모듈). Azure 자동화는 AzureRM 모듈과 최근 업그레이드인 Az 모듈과 함께 제공됩니다. 기능에는 Azure AD 사용자(OrgId) 자격 증명 기반 인증을 사용하여 Azure에 대한 비대화형 인증이 포함됩니다. [Azure AD 2.0.2.76을](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76)참조하십시오.

* 윈도우 파워 쉘 (MSOnline 모듈)에 대한 마이크로 소프트 Azure 활성 디렉토리. 이 모듈에서는 Office 365를 포함하여 Microsoft 온라인과의 상호 작용을 수행할 수 있습니다.

>[!NOTE]
>파워쉘 코어는 MSOnline 모듈을 지원하지 않습니다. 모듈 cmdlet을 사용하려면 Windows PowerShell에서 실행해야 합니다. MSOnline 모듈 대신 그래프 모듈에 최신 Azure Active Directory PowerShell을 사용하는 것이 좋습니다. 

### <a name="preinstallation"></a>사전 설치

컴퓨터에 Azure AD 모듈을 설치하기 전에 다음을 수행하십시오.

* AzureRM/Az 모듈 및 MSOnline 모듈의 이전 버전을 제거합니다. 

* 새 PowerShell 모듈이 올바르게 작동하도록 Microsoft 온라인 서비스 로그인 도우미를 제거합니다.  

### <a name="install-the-azurerm-and-az-modules"></a>AzureRM 및 Az 모듈 설치

>[!NOTE]
>이러한 모듈을 사용하려면 PowerShell 버전 5.1 이상에서 64비트 버전의 Windows를 사용해야 합니다. 

1. WMF(Windows 관리 프레임워크) 5.1을 설치합니다. [WMF 5.1 설치 및 구성을](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7)참조하십시오.

2. [PowerShellGet을 사용하여 Windows에서 Azure PowerShell 설치의](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0)지침을 사용하여 AzureRM 및/또는 Az를 설치합니다.

### <a name="install-the-msonline-module"></a>MS온라인 모듈 설치

>[!NOTE]
>MSOnline 모듈을 설치하려면 Office 365 관리자 역할의 구성원이어야 합니다. [관리자 역할 소개](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)를 참조하십시오.

1. 컴퓨터에서 Microsoft .NET 프레임워크 3.5.x 기능을 사용하도록 설정되어 있는지 확인합니다. 컴퓨터에 최신 버전이 설치되어 있을 수 있지만 이전 버전의 .NET Framework와의 이전 버전과의 이전 버전과의 호환성을 사용하거나 사용하지 않도록 설정할 수 있습니다. 

2. [Microsoft 온라인 서비스 로그인 도우미의](https://www.microsoft.com/download/details.aspx?id=41950)64비트 버전을 설치합니다.

3. 관리자로 Windows PowerShell을 실행하여 높은 Windows PowerShell 명령 프롬프트를 만듭니다.

4. [MSOnline 1.0에서](http://www.powershellgallery.com/packages/MSOnline/1.0)Azure 활성 디렉터리 배포 .

5. NuGet 공급자를 설치하라는 메시지가 표시되면 Y를 입력하고 ENTER를 누릅니다.

6. [PSGallery에서](https://www.powershellgallery.com/)모듈을 설치하라는 메시지가 표시되면 Y를 입력하고 ENTER를 누릅니다.

### <a name="install-support-for-pscredential"></a>PS자격증명에 대한 지원 설치

Azure 자동화는 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) 클래스를 사용하여 자격 증명 자산을 나타냅니다. 스크립트는 cmdlet을 `Get-AutomationPSCredential` 사용하여 개체를 검색합니다. `PSCredential` 자세한 내용은 [Azure 자동화의 자격 증명 자산을](shared-resources/credentials.md)참조하십시오.

## <a name="assigning-a-subscription-administrator"></a>구독 관리자 할당

Azure 구독에 대한 관리자를 할당해야 합니다. 이 사람은 구독 범위에 대한 소유자의 역할을 합니다. [Azure 자동화에서 역할 기반 액세스 제어를](automation-role-based-access-control.md)참조하십시오. 

## <a name="changing-the-azure-ad-users-password"></a>Azure AD 사용자의 암호 변경

Azure AD 사용자의 암호를 변경하려면 다음을 수행하십시오.

1. Azure에서 로그아웃합니다.

2. 전체 사용자 이름(도메인 포함)과 임시 암호를 사용하여 Azure AD 사용자가 방금 만든 대로 관리자가 Azure에 로그인하도록 합니다. 

3. 메시지가 표시되면 관리자에게 암호를 변경하도록 요청합니다.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Azure AD 사용자를 사용하여 Azure 구독을 관리하도록 Azure 자동화 구성

Azure 자동화가 Azure AD와 통신하려면 Azure AD에 대한 Azure 연결과 연결된 자격 증명을 검색해야 합니다. 이러한 자격 증명의 예로는 테넌트 ID, 구독 ID 등이 있습니다. Azure 와 Azure AD 간의 연결에 대한 자세한 내용은 [조직을 Azure Active Directory에 연결 참조하세요.](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops)

## <a name="creating-a-credential-asset"></a>자격 증명 자산 만들기

Azure AD에 대한 Azure 자격 증명을 사용할 수 있는 경우 런북 및 DSC(Desire 상태 구성) 스크립트가 해당 자격 증명에 액세스할 수 있도록 Azure AD 자격 증명을 안전하게 저장할 Azure 자동화 자격 증명 자산을 만들어야 합니다. Azure 포털 또는 PowerShell cmdlet을 사용하여 이 작업을 수행할 수 있습니다.

### <a name="create-the-credential-asset-in-azure-portal"></a>Azure 포털에서 자격 증명 자산 만들기

Azure 포털을 사용하여 자격 증명 자산을 만들 수 있습니다. **공유 리소스**에서 자격 증명을 사용하여 자동화 계정에서 이 작업을 **수행합니다.** [Azure 자동화에서 자격 증명 자산을](shared-resources/credentials.md)참조하십시오.

### <a name="create-the-credential-asset-with-windows-powershell"></a>Windows PowerShell을 사용하여 자격 증명 자산 만들기

Windows PowerShell에서 새 자격 증명 자산을 준비하려면 `PSCredential` 스크립트가 먼저 할당된 사용자 이름과 암호를 사용하여 개체를 만듭니다. 그런 다음 스크립트는 이 개체를 사용하여 [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) cmdlet에 대한 호출을 통해 자산을 만듭니다. 또는 스크립트는 [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) cmdlet을 호출하여 사용자에게 이름과 암호를 입력하라는 메시지를 표시할 수 있습니다. [Azure 자동화에서 자격 증명 자산을](shared-resources/credentials.md)참조하십시오. 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Azure 자동화 실행책에서 Azure 리소스 관리

자격 증명 자산을 사용하여 Azure 자동화 Runbook에서 Azure 리소스를 관리할 수 있습니다. 다음은 Azure 구독에서 가상 컴퓨터를 중지하고 시작하는 데 사용할 자격 증명 자산을 수집하는 PowerShell Runbook예제입니다. 이 Runbook은 `Get-AutomationPSCredential` 먼저 Azure를 인증하는 데 사용할 자격 증명을 검색하는 데 사용합니다. 그런 다음 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) cmdlet을 호출하여 자격 증명을 사용하여 Azure에 연결합니다. 스크립트는 [Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) cmdlet을 사용하여 작업할 구독을 선택합니다. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>다음 단계

* Azure 자동화 에서 [자격 증명 자산에](shared-resources/credentials.md)대한 자동화 자격 증명 자산에 대한 정보를 찾을 수 있습니다.
* [Azure 자동화에서 모듈 관리를](shared-resources/modules.md) 참조하여 자동화 모듈을 사용하는 방법을 알아봅니다.
* Azure 자동화에서 Runbook을 시작하는 데 사용할 수 있는 방법에 대해 자세히 알아보려면 [Azure Automation에서 Runbook 시작을](automation-starting-a-runbook.md)참조하십시오.
* 언어 참조 및 학습 모듈을 포함한 [PowerShell에](https://docs.microsoft.com/powershell/scripting/overview)대한 자세한 내용은 PowerShell 문서를 참조하십시오.