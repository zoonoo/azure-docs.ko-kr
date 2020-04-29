---
title: Azure Automation에서 Azure AD를 사용하여 Azure에 인증
description: Azure에 대 한 인증을 위한 공급자로 Azure Automation 내에서 Azure AD를 사용 하는 방법을 알아봅니다.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548331"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Azure Automation에서 Azure AD를 사용하여 Azure에 인증

[AD (Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) ) 서비스를 사용 하면 사용자 관리, 도메인 관리 및 Single Sign-On 구성과 같은 다양 한 관리 작업을 수행할 수 있습니다. 이 문서에서는 azure에 대 한 인증을 위한 공급자로 Azure Automation 내에서 Azure AD를 사용 하는 방법을 설명 합니다. 

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="installing-azure-ad-modules"></a>Azure AD 모듈 설치

다음 PowerShell 모듈을 통해 Azure AD를 사용 하도록 설정할 수 있습니다.

* Graph 용 PowerShell Azure Active Directory (AzureRM 및 Az modules). Azure Automation는 AzureRM 모듈 및 Az module의 최근 업그레이드와 함께 제공 됩니다. 기능에는 azure AD 사용자 (OrgId) 자격 증명 기반 인증을 사용 하 여 Azure에 대 한 비 대화형 인증이 포함 됩니다. [AZURE AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76)를 참조 하세요.

* Windows PowerShell에 대 한 Microsoft Azure Active Directory (MSOnline 모듈). 이 모듈은 Office 365을 포함 하 여 Microsoft Online과의 상호 작용을 가능 하 게 합니다.

>[!NOTE]
>PowerShell Core는 MSOnline 모듈을 지원 하지 않습니다. 모듈 cmdlet을 사용 하려면 Windows PowerShell에서 실행 해야 합니다. MSOnline 모듈 대신 최신 Azure Active Directory PowerShell for Graph 모듈을 사용 하는 것이 좋습니다. 

### <a name="preinstallation"></a>전

컴퓨터에 Azure AD 모듈을 설치 하기 전에 다음을 수행 합니다.

* 이전 버전의 AzureRM/Az module 및 MSOnline 모듈을 제거 합니다. 

* Microsoft Online Services 로그인 도우미를 제거 하 여 새 PowerShell 모듈의 올바른 작동을 확인 합니다.  

### <a name="install-the-azurerm-and-az-modules"></a>AzureRM 및 Az 모듈 설치

>[!NOTE]
>이러한 모듈로 작업 하려면 64 비트 버전의 Windows에서 PowerShell 버전 5.1 이상을 사용 해야 합니다. 

1. WMF (Windows Management Framework) 5.1을 설치 합니다. [WMF 5.1 설치 및 구성을](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7)참조 하세요.

2. [PowerShellGet을 사용 하 여 Windows에 Azure PowerShell 설치](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0)의 지침을 사용 하 여 AzureRM 및/또는 Az를 설치 합니다.

### <a name="install-the-msonline-module"></a>MSOnline 모듈 설치

>[!NOTE]
>MSOnline 모듈을 설치 하려면 Office 365 관리자 역할의 구성원 이어야 합니다. [관리 역할 정보](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)를 참조 하세요.

1. 컴퓨터에서 Microsoft .NET Framework 3.5. x 기능이 사용 되도록 설정 되어 있는지 확인 합니다. 컴퓨터에 최신 버전이 설치 되어 있을 수 있지만 이전 버전의 .NET Framework와 이전 버전과의 호환성을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 

2. 64 비트 버전의 [Microsoft Online Services 로그인 도우미](https://www.microsoft.com/download/details.aspx?id=41950)를 설치 합니다.

3. 관리자 권한으로 Windows PowerShell을 실행 하 여 관리자 권한으로 Windows PowerShell 명령 프롬프트를 만듭니다.

4. [Msonline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0)에서 Azure Active Directory를 배포 합니다.

5. NuGet 공급자를 설치할지 묻는 메시지가 표시 되 면 Y를 입력 하 고 ENTER 키를 누릅니다.

6. [PSGallery](https://www.powershellgallery.com/)에서 모듈을 설치 하 라는 메시지가 표시 되 면 Y를 입력 하 고 enter 키를 누릅니다.

### <a name="install-support-for-pscredential"></a>PSCredential에 대 한 지원 설치

Azure Automation는 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) 클래스를 사용 하 여 자격 증명 자산을 나타냅니다. 스크립트는 cmdlet `PSCredential` 을 `Get-AutomationPSCredential` 사용 하 여 개체를 검색 합니다. 자세한 내용은 [Azure Automation 자격 증명 자산](shared-resources/credentials.md)을 참조 하세요.

## <a name="assigning-a-subscription-administrator"></a>구독 관리자 할당

Azure 구독에 대 한 관리자를 할당 해야 합니다. 이 사용자에 게는 구독 범위에 대 한 소유자 역할이 있습니다. [Azure Automation의 역할 기반 액세스 제어를](automation-role-based-access-control.md)참조 하세요. 

## <a name="changing-the-azure-ad-users-password"></a>Azure AD 사용자의 암호 변경

Azure AD 사용자의 암호를 변경 하려면:

1. Azure에서 로그 아웃 합니다.

2. 관리자가 전체 사용자 이름 (도메인 포함) 및 임시 암호를 사용 하 여 만든 Azure AD 사용자로 Azure에 로그인 하도록 합니다. 

3. 메시지가 표시 되 면 관리자에 게 암호를 변경 하도록 요청 합니다.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Azure AD 사용자를 사용 하 여 Azure 구독을 관리 하도록 Azure Automation 구성

Azure AD와 통신 하는 Azure Automation azure AD에 대 한 Azure 연결과 관련 된 자격 증명을 검색 해야 합니다. 이러한 자격 증명의 예로는 테 넌 트 ID, 구독 ID 및 등이 있습니다. Azure AD와 Azure AD 간의 연결에 대 한 자세한 내용은 [Azure Active Directory에 조직 연결](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops)을 참조 하세요.

## <a name="creating-a-credential-asset"></a>자격 증명 자산 만들기

사용할 수 있는 azure AD 용 Azure 자격 증명을 사용 하 여 runbook 및 DSC (필요한 상태 구성) 스크립트에서 액세스할 수 있도록 Azure AD 자격 증명을 안전 하 게 저장 하는 Azure Automation 자격 증명 자산을 만들 수 있습니다. Azure Portal 또는 PowerShell cmdlet 중 하나를 사용 하 여이 작업을 수행할 수 있습니다.

### <a name="create-the-credential-asset-in-azure-portal"></a>Azure Portal에서 자격 증명 자산 만들기

Azure Portal를 사용 하 여 자격 증명 자산을 만들 수 있습니다. **공유 리소스**의 **자격 증명** 을 사용 하 여 Automation 계정에서이 작업을 수행 합니다. [Azure Automation에서 자격 증명 자산을](shared-resources/credentials.md)참조 하세요.

### <a name="create-the-credential-asset-with-windows-powershell"></a>Windows PowerShell을 사용 하 여 자격 증명 자산 만들기

Windows PowerShell에서 새 자격 증명 자산을 준비 하기 위해 스크립트는 먼저 할당 `PSCredential` 된 사용자 이름 및 암호를 사용 하 여 개체를 만듭니다. 그런 다음 스크립트는이 개체를 사용 하 여 [새-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) cmdlet에 대 한 호출을 통해 자산을 만듭니다. 또는 스크립트는 [Get Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) cmdlet을 호출 하 여 사용자에 게 이름과 암호를 입력 하 라는 메시지를 표시할 수 있습니다. [Azure Automation에서 자격 증명 자산을](shared-resources/credentials.md)참조 하세요. 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Azure Automation runbook에서 Azure 리소스 관리

자격 증명 자산을 사용 하 여 Azure Automation runbook에서 Azure 리소스를 관리할 수 있습니다. 다음은 Azure 구독에서 가상 컴퓨터를 중지 하 고 시작 하는 데 사용 하는 자격 증명 자산을 수집 하는 예제 PowerShell runbook입니다. 이 runbook은 먼저 `Get-AutomationPSCredential` 을 사용 하 여 Azure에 인증 하는 데 사용할 자격 증명을 검색 합니다. 그런 다음 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) cmdlet을 호출 하 여 자격 증명을 사용 하 여 Azure에 연결 합니다. 이 스크립트는 [get-azuresubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) cmdlet을 사용 하 여 사용할 구독을 선택 합니다. 

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

* [Azure Automation의 자격 증명 자산](shared-resources/credentials.md)에서 Automation 자격 증명 자산에 대 한 정보를 찾을 수 있습니다.
* Automation 모듈로 작업 하는 방법에 대 한 자세한 내용은 [Azure Automation 모듈 관리](shared-resources/modules.md) 를 참조 하세요.
* Azure Automation에서 runbook을 시작 하는 데 사용할 수 있는 방법에 대해 자세히 알아보려면 [Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md)을 참조 하세요.
* 언어 참조 및 학습 모듈을 비롯 한 PowerShell에 대 한 자세한 내용은 [Powershell 문서](https://docs.microsoft.com/powershell/scripting/overview)를 참조 하세요.