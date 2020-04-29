---
title: Azure Automation을 사용하여 Office 365 서비스 관리
description: Azure Automation를 사용 하 여 Office 365 구독 서비스를 관리 하는 방법을 설명 합니다.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550423"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Azure Automation을 사용하여 Office 365 서비스 관리

Microsoft Word, Microsoft Outlook 등의 제품에 대해 Azure Automation를 사용 하 여 Office 365 구독 서비스를 관리할 수 있습니다. Office 365와의 상호 작용은 [Azure Active Directory (AZURE AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)에서 사용 하도록 설정 됩니다. [Azure에 인증 하려면 Azure Automation에서 AZURE AD 사용을](automation-use-azure-ad.md)참조 하세요.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Automation에서 Office 365 구독 서비스를 관리 하려면 다음이 필요 합니다.

* Azure 구독 [구독 의사 결정 가이드](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)를 참조 하세요.
* 사용자 계정 자격 증명과 runbook을 보유 하는 Azure의 Automation 개체입니다. [Azure Automation 소개를](https://docs.microsoft.com/azure/automation/automation-intro)참조 하세요.
* 설정하세요. [Azure에 인증 하려면 Azure Automation에서 AZURE AD 사용을](automation-use-azure-ad.md)참조 하세요.
* 계정을 사용 하는 Office 365 테 넌 트. [Office 365 테 넌 트 설정](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant)을 참조 하세요.

## <a name="installing-the-msonline-and-msonlineext-modules"></a>MSOnline 및 MSOnlineExt 모듈 설치

Azure Automation 내에서 Office 365을 사용 하려면 Windows PowerShell (`MSOnline` 모듈) Microsoft Azure Active Directory 필요 합니다. 단일 및 다중 테 넌 트 [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)환경에서 Azure AD 관리를 간소화 하는 모듈도 필요 합니다. [Azure Automation에서 AZURE AD 사용](automation-use-azure-ad.md)에서 설명한 대로 모듈을 설치 하 여 azure에 인증 합니다.

>[!NOTE]
>MSOnline PowerShell을 사용 하려면 Azure AD의 구성원 이어야 합니다. 게스트 사용자는이 모듈을 사용할 수 없습니다.

## <a name="creating-an-azure-automation-account"></a>Azure Automation 계정 만들기

이 문서의 단계를 완료 하려면 Azure Automation 계정이 필요 합니다. [Azure Automation 계정 만들기](automation-quickstart-create-account.md)를 참조 하세요.
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>MSOnline 및 MSOnlineExt을 자산으로 추가

이제 설치 된 MSOnline 및 MSOnlineExt 모듈을 추가 하 여 Office 365 기능을 사용 하도록 설정 합니다. [Azure Automation에서 모듈 관리](shared-resources/modules.md)를 참조 하세요.

1. Azure Portal에서 **Automation 계정**을 선택 합니다.
2. Automation 계정을 선택 합니다.
3. **공유 리소스**아래에서 **모듈 갤러리** 를 선택 합니다.
4. MSOnline을 검색 합니다.
5. `MSOnline` PowerShell 모듈을 선택 하 고 **가져오기** 를 클릭 하 여 모듈을 자산으로 가져옵니다.
6. 4 단계와 5 단계를 반복 하 여 `MSOnlineExt` 모듈을 찾아서 가져옵니다. 

## <a name="creating-a-credential-asset-optional"></a>자격 증명 자산 만들기 (선택 사항)

스크립트를 실행할 수 있는 권한이 있는 Office 365 관리자에 대 한 자격 증명 자산을 만들 수도 있습니다. 그러나 PowerShell 스크립트 내에서 사용자 이름 및 암호를 제공 하는 것을 방지 하는 데 도움이 될 수 있습니다. 지침은 [자격 증명 자산 만들기](automation-use-azure-ad.md#creating-a-credential-asset)를 참조 하세요.

## <a name="creating-an-office-365-service-account"></a>Office 365 서비스 계정 만들기

Office 365 구독 서비스를 실행 하려면 원하는 작업을 수행할 수 있는 권한이 있는 Office 365 서비스 계정이 필요 합니다. 전역 관리자 계정 하나, 서비스 당 하나의 계정 또는 실행할 함수 또는 스크립트 하나를 사용할 수 있습니다. 어떤 경우 든 서비스 계정에는 복잡 하 고 안전한 암호가 필요 합니다. [비즈니스용 Office 365 설치](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide)를 참조 하세요. 

## <a name="connecting-to-the-azure-ad-online-service"></a>Azure AD online 서비스에 연결

>[!NOTE]
>MSOnline 모듈 cmdlet을 사용 하려면 Windows PowerShell에서 실행 해야 합니다. PowerShell Core는 이러한 cmdlet을 지원 하지 않습니다.

MSOnline 모듈을 사용 하 여 Office 365 구독에서 Azure AD에 연결할 수 있습니다. 연결에서 Office 365 사용자 이름 및 암호를 사용 하거나 MFA (multi-factor authentication)를 사용 합니다. Azure Portal 또는 Windows PowerShell 명령 프롬프트를 사용 하 여 연결할 수 있습니다 (승격 하지 않아도 됨).

PowerShell 예제는 다음과 같습니다. 자격 [증명을](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) 요청 하는 cmdlet은 자격 증명을 요청 하 `Msolcred` 고 변수에 저장 합니다. 그런 다음 [connect-msolservice](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) cmdlet은 자격 증명을 사용 하 여 Azure directory online 서비스에 연결 합니다. 특정 Azure 환경에 연결 하려면 `AzureEnvironment` 매개 변수를 사용 합니다.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

오류가 수신 되지 않으면 성공적으로 연결 된 것입니다. 빠른 테스트는 Office 365 cmdlet (예: `Get-MsolUser`)을 실행 하 고 결과를 확인 하는 것입니다. 오류가 발생 하는 경우 일반적인 문제는 잘못 된 암호입니다.

>[!NOTE]
>AzureRM 모듈 또는 Az module을 사용 하 여 Office 365 구독에서 Azure AD에 연결할 수도 있습니다. 주 연결 cmdlet은 [AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)입니다. 이 cmdlet은 특정 `AzureEnvironmentName` Office 365 환경에 대 한 매개 변수를 지원 합니다.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>기존 스크립트에서 PowerShell runbook 만들기

PowerShell 스크립트에서 Office 365 기능에 액세스 합니다. 다음은의 `Office-Credentials` `admin@TenantOne.com`사용자 이름으로 명명 된 자격 증명에 대 한 스크립트의 예입니다. 을 사용 `Get-AutomationPSCredential` 하 여 Office 365 자격 증명을 가져옵니다.

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

Azure Automation runbook에서 스크립트를 사용할 수 있습니다. 예를 들어 PowerShell runbook 형식을 사용 합니다.

1. 새 PowerShell runbook을 만듭니다. [Azure Automation Runbook 만들기](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook)를 참조 하세요.
2. Automation 계정에서 **프로세스 자동화**아래에 있는 **runbook** 을 선택 합니다.
3. 새 runbook을 선택 하 고 **편집**을 클릭 합니다.
4. 스크립트를 복사 하 여 runbook에 대 한 텍스트 편집기에 붙여 넣습니다.
5. **자산**을 선택 하 고 **자격 증명** 을 확장 한 다음 Office 365 자격 증명이 있는지 확인 합니다.
6. **저장**을 클릭합니다.
7. **테스트 창**을 선택 하 고 **시작** 을 클릭 하 여 runbook 테스트를 시작 합니다. [Azure Automation에서 Runbook 관리를](https://docs.microsoft.com/azure/automation/manage-runbooks)참조 하세요.
8. 테스트가 완료 되 면 테스트 창에서 종료 합니다.

## <a name="publishing-and-scheduling-the-runbook"></a>Runbook 게시 및 예약

Runbook을 게시 한 다음 예약 하려면 [Azure Automation에서 Runbook 관리](https://docs.microsoft.com/azure/automation/manage-runbooks)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Automation의 자격 증명 자산](shared-resources/credentials.md)에서 Automation 자격 증명 자산에 대 한 정보를 찾을 수 있습니다.
* Automation 모듈로 작업 하는 방법에 대 한 자세한 내용은 [Azure Automation 모듈 관리](shared-resources/modules.md) 를 참조 하세요.
* Runbook 관리에 대 한 개요는 [Azure Automation에서 Runbook 관리](https://docs.microsoft.com/azure/automation/manage-runbooks)를 참조 하세요.
* Azure Automation에서 runbook을 시작 하는 데 사용할 수 있는 방법에 대해 자세히 알아보려면 [Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md)을 참조 하세요.
* 언어 참조 및 학습 모듈을 비롯 한 PowerShell에 대 한 자세한 내용은 [Powershell 문서](https://docs.microsoft.com/powershell/scripting/overview)를 참조 하세요.