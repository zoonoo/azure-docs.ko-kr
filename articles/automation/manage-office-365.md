---
title: Azure 자동화를 사용하여 Office 365 서비스 관리
description: Azure 자동화를 사용하여 Office 365 구독 서비스를 관리하는 방법을 알려줍니다.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550423"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Azure 자동화를 사용하여 Office 365 서비스 관리

Microsoft Word 및 Microsoft Outlook과 같은 제품에 대해 Office 365 구독 서비스 관리에 Azure 자동화를 사용할 수 있습니다. Office 365와의 상호 작용은 [Azure Active Directory(Azure AD)에서](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)사용할 수 있습니다. [Azure 자동화에서 Azure AD 사용을 사용하여 Azure 에 인증합니다.](automation-use-azure-ad.md)

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 자동화에서 Office 365 구독 서비스를 관리하려면 다음이 필요합니다.

* Azure 구독 [구독 결정 가이드를](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)참조하십시오.
* 사용자 계정 자격 증명 및 Runbook을 보유하는 Azure의 자동화 개체입니다. [Azure 자동화 에 대한 소개를](https://docs.microsoft.com/azure/automation/automation-intro)참조하십시오.
* 설정하세요. [Azure 자동화에서 Azure AD 사용을 사용하여 Azure 에 인증합니다.](automation-use-azure-ad.md)
* 계정이 있는 Office 365 테넌트입니다. [Office 365 테넌트 설정을](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant)참조하십시오.

## <a name="installing-the-msonline-and-msonlineext-modules"></a>MS온라인 및 MSOnlineExt 모듈 설치

Azure 자동화 내에서 Office 365를 사용하려면 Windows PowerShell(모듈)에`MSOnline` 대한 Microsoft Azure Active Directory가 필요합니다. 또한 단일 및 다중 테넌트 환경에서 Azure AD 관리를 간소화하는 모듈이 [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)필요합니다. Azure 에 [인증하려면 Azure 자동화에서 Azure AD 사용에](automation-use-azure-ad.md)설명된 대로 모듈을 설치합니다.

>[!NOTE]
>MSOnline PowerShell을 사용하려면 Azure AD의 멤버여야 합니다. 게스트 사용자는 모듈을 사용할 수 없습니다.

## <a name="creating-an-azure-automation-account"></a>Azure 자동화 계정 만들기

이 문서의 단계를 완료하려면 Azure 자동화의 계정이 필요합니다. [Azure 자동화 계정 만들기를](automation-quickstart-create-account.md)참조하십시오.
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>MS온라인 및 MSOnlineExt를 자산으로 추가

이제 설치된 MSOnline 및 MSOnlineExt 모듈을 추가하여 Office 365 기능을 활성화합니다. Azure [자동화에서 모듈 관리를](shared-resources/modules.md)참조하십시오.

1. Azure 포털에서 **자동화 계정**.
2. 자동화 계정을 선택합니다.
3. **공유 리소스**아래에 있는 **모듈 갤러리를 선택합니다.**
4. MSOnline을 검색합니다.
5. PowerShell `MSOnline` 모듈을 선택하고 **가져오기를** 클릭하여 모듈을 자산으로 가져옵니다.
6. 4단계와 5단계를 반복하여 `MSOnlineExt` 모듈을 찾아 가져옵니다. 

## <a name="creating-a-credential-asset-optional"></a>자격 증명 자산 만들기(선택 사항)

스크립트를 실행할 권한이 있는 Office 365 관리 사용자에 대 한 자격 증명 자산을 만드는 것은 선택 사항입니다. 하지만 PowerShell 스크립트 내부에 사용자 이름과 암호를 노출하지 않도록 하는 데 도움이 될 수 있습니다. 지침은 자격 [증명 자산 만들기를](automation-use-azure-ad.md#creating-a-credential-asset)참조하십시오.

## <a name="creating-an-office-365-service-account"></a>Office 365 서비스 계정 만들기

Office 365 구독 서비스를 실행하려면 원하는 작업을 수행할 수 있는 권한이 있는 Office 365 서비스 계정이 필요합니다. 하나의 글로벌 관리자 계정, 서비스당 하나의 계정을 사용하거나 실행할 함수 또는 스크립트가 하나 있습니다. 어쨌든 서비스 계정에는 복잡하고 안전한 암호가 필요합니다. [비즈니스용 Office 365 설정을](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide)참조하십시오. 

## <a name="connecting-to-the-azure-ad-online-service"></a>Azure AD 온라인 서비스에 연결

>[!NOTE]
>MSOnline 모듈 cmdlet을 사용하려면 Windows PowerShell에서 실행해야 합니다. PowerShell 코어는 이러한 cmdlet을 지원하지 않습니다.

MSOnline 모듈을 사용하여 Office 365 구독에서 Azure AD에 연결할 수 있습니다. 연결은 Office 365 사용자 이름과 암호를 사용하거나 MFA(다단계 인증)를 사용합니다. Azure 포털 또는 Windows PowerShell 명령 프롬프트를 사용하여 연결할 수 있습니다(상승할 필요는 없습니다).

PowerShell 예제는 다음과 같습니다. [자격 증명](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) 받기 cmdlet은 자격 증명을 묻는 `Msolcred` 메시지를 표시하고 변수에 저장합니다. 그런 다음 [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) cmdlet은 자격 증명을 사용하여 Azure 디렉터리 온라인 서비스에 연결합니다. 특정 Azure 환경에 연결하려면 매개 변수를 `AzureEnvironment` 사용합니다.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

오류가 없는 경우 성공적으로 연결한 것입니다. 빠른 테스트는 예를 들어 `Get-MsolUser`Office 365cmdlet을 실행하고 결과를 확인하는 것입니다. 오류가 발생하면 일반적인 문제가 잘못된 암호라는 점에 유의하십시오.

>[!NOTE]
>또한 AzureRM 모듈 또는 Az 모듈을 사용하여 Office 365 구독에서 Azure AD에 연결할 수도 있습니다. 주요 연결 cmdlet은 [연결-AzureAD입니다.](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0) 이 cmdlet은 `AzureEnvironmentName` 특정 Office 365 환경에 대한 매개 변수를 지원합니다.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>기존 스크립트에서 PowerShell 실행북 만들기

PowerShell 스크립트에서 Office 365 기능에 액세스합니다. 다음은 의 사용자 이름으로 명명된 `Office-Credentials` 자격 증명에 `admin@TenantOne.com`대한 스크립트의 예입니다. Office `Get-AutomationPSCredential` 365 자격 증명을 가져오는 데 사용 됩니다.

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>Runbook에서 스크립트 실행

Azure 자동화 실행책에서 스크립트를 사용할 수 있습니다. 예를 들어 PowerShell 런북 유형을 사용합니다.

1. 새 PowerShell 실행책을 만듭니다. Azure [자동화 실행책 만들기](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook)를 참조하십시오.
2. 자동화 계정에서 프로세스 자동화 에서 **Runbook을** **선택합니다.**
3. 새 Runbook을 선택하고 **편집을**클릭합니다.
4. 스크립트를 복사하여 Runbook의 텍스트 편집기에 붙여넣습니다.
5. **ASSETS를**선택한 다음 자격 증명을 확장하고 Office 365 자격 증명이 있는지 **확인합니다.**
6. **저장**을 클릭합니다.
7. **테스트 창을**선택한 다음 **실행을** 클릭하여 실행북 테스트를 시작합니다. [Azure 자동화에서 실행 문서 관리를](https://docs.microsoft.com/azure/automation/manage-runbooks)참조하십시오.
8. 테스트가 완료되면 테스트 창에서 종료합니다.

## <a name="publishing-and-scheduling-the-runbook"></a>Runbook 게시 및 예약

Runbook을 게시한 다음 예약하려면 [Azure Automation에서 Runbook 관리를](https://docs.microsoft.com/azure/automation/manage-runbooks)참조하십시오.

## <a name="next-steps"></a>다음 단계

* Azure 자동화 에서 [자격 증명 자산에](shared-resources/credentials.md)대한 자동화 자격 증명 자산에 대한 정보를 찾을 수 있습니다.
* [Azure 자동화에서 모듈 관리를](shared-resources/modules.md) 참조하여 자동화 모듈을 사용하는 방법을 알아봅니다.
* Runbook 관리에 대한 개요는 [Azure 자동화의 Runbook 관리를](https://docs.microsoft.com/azure/automation/manage-runbooks)참조하십시오.
* Azure 자동화에서 Runbook을 시작하는 데 사용할 수 있는 방법에 대해 자세히 알아보려면 [Azure Automation에서 Runbook 시작을](automation-starting-a-runbook.md)참조하십시오.
* 언어 참조 및 학습 모듈을 포함한 [PowerShell에](https://docs.microsoft.com/powershell/scripting/overview)대한 자세한 내용은 PowerShell 문서를 참조하십시오.