---
title: 셀프 서비스 암호 재설정에 대한 연락처 정보 미리 채우기 - Azure Active Directory
description: 사용자가 등록 프로세스를 완료하지 않고도 기능을 사용할 수 있도록 Azure Active Directory SSPR(셀프 서비스 암호 재설정) 사용자에 대한 연락처 정보를 미리 채우는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed93944c2a94370250fb75a679c1ac37a2a8418c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861156"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>Azure Active Directory SSPR(셀프 서비스 암호 재설정)에 대한 사용자 인증 연락처 정보 미리 채우기

Azure AD(Azure Active Directory) SSPR(셀프 서비스 암호 재설정)을 사용하려면 사용자에 대한 인증 연락처 정보가 있어야 합니다. 일부 조직에서는 사용자들이 직접 자신의 인증 데이터를 등록하도록 합니다. 다른 조직에서는 AD DS(Active Directory Domain Services)에 이미 있는 인증 데이터에서 동기화하는 것을 선호합니다. 이 동기화된 데이터는 사용자 개입 없이 Azure AD 및 SSPR에 제공됩니다. 사용자가 암호를 변경하거나 다시 설정해야 할 때 이전에 등록된 연락처 정보가 없는 경우에도 이 작업을 수행할 수 있습니다.

다음 요구 사항을 충족하는 경우 인증 연락처 정보를 미리 채울 수 있습니다.

* 온-프레미스 디렉터리에 데이터가 올바른 형식으로 저장되어 있습니다.
* Azure AD 테넌트에 [Azure AD Connect](../hybrid/how-to-connect-install-express.md)를 구성했습니다.

전화 번호는 *+1 4251234567* 과 같은 *+국가 번호 전화 번호* 형식이어야 합니다.

> [!NOTE]
> 국가 번호와 전화 번호 사이에 공백이 필요합니다.
>
> 암호 재설정은 내선 번호를 지원하지 않습니다. *+1 4251234567X12345* 형식에서도 전화를 걸기 전에 내선 번호가 제거됩니다.

## <a name="fields-populated"></a>채워진 필드

Azure AD Connect에서 기본 설정을 사용할 경우, SSPR에 대한 인증 연락처 정보를 채우기 위해 다음 매핑이 만들어집니다.

| 온-프레미스 Active Directory | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | 사무실 전화 |
| mobile                       | 휴대폰 |

사용자가 자신의 휴대폰 번호를 확인하면 Azure AD에서 **인증 연락처 정보** 아래의 *전화* 필드도 해당 번호로 채워집니다.

## <a name="authentication-contact-info"></a>인증 연락처 정보

Azure Portal에서 Azure AD 사용자에 대한 **인증 방법** 페이지에서 전역 관리자는 인증 연락처 정보를 수동으로 설정할 수 있습니다. 다음 예제 스크린샷에 표시된 대로 *사용할 수 있는 인증 방법* 섹션 또는 **+인증 방법 추가** 에서 기존 방법을 검토할 수 있습니다.

:::image type="content" source="media/howto-sspr-authenticationdata/user-authentication-contact-info.png" alt-text="Azure Portal에서 인증 방법 관리":::

이 인증 연락처 정보에는 다음 고려 사항이 적용됩니다.

* *전화* 필드가 채워지고 *휴대폰* 이 SSPR 정책에서 활성화되는 경우 사용자는 암호 재설정 등록 페이지와 암호 재설정 워크플로 중 해당 번호를 볼 수 있습니다.
* *이메일* 필드가 채워지고 *이메일* 이 SSPR 정책에서 활성화되는 경우 사용자는 암호 재설정 등록 페이지와 암호 재설정 워크플로 중 해당 이메일을 볼 수 있습니다.

## <a name="security-questions-and-answers"></a>보안 질문 및 답변

본인 확인 질문과 답변은 Azure AD 테넌트에 안전하게 저장되며 사용자가 [SSPR 등록 포털](https://aka.ms/ssprsetup)을 통해서만 액세스할 수 있습니다. 관리자는 다른 사용자의 질문과 답변 내용을 보거나, 설정하거나, 수정할 수 없습니다.

## <a name="what-happens-when-a-user-registers"></a>사용자가 등록하면 어떻게 되나요?

사용자가 등록하면 등록 페이지에서 다음 필드를 설정합니다.

* **인증 전화**
* **인증 전자 메일**
* **본인 확인 질문 및 답변**

관리자가 *휴대폰* 또는 *대체 메일* 에 값을 입력하면 서비스에 등록하지 않은 사용자도 해당 값을 사용하여 자신의 암호를 즉시 재설정할 수 있습니다.

또한, 사용자는 처음으로 등록할 때 해당 값을 보고, 원하는 경우 값을 변경할 수도 있습니다. 등록을 완료한 후에는 *인증 전화* 및 *인증 이메일* 필드에 해당하는 값이 유지됩니다.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>PowerShell을 사용하여 인증 데이터 설정 및 읽기

PowerShell을 사용하여 다음 필드를 설정할 수 있습니다.

* *대체 메일*
* *휴대폰*
* *사무실 전화*
    * 온-프레미스 디렉터리와 동기화하지 않는 경우에만 설정할 수 있습니다.

> [!IMPORTANT]
> PowerShell v1과 PowerShell v2 간의 명령 기능에는 알려진 패리티 부족이 있습니다. [인증 방법에 대한 Microsoft Graph REST API(베타)](/graph/api/resources/authenticationmethods-overview)는 최신 상호 작용을 제공하기 위한 현재 엔지니어링 포커스입니다.

### <a name="use-powershell-version-1"></a>PowerShell 버전 1 사용

시작하려면 [Azure AD PowerShell 모듈을 다운로드하고 설치합니다](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule). 설치한 후 다음 단계를 사용하여 각 필드를 구성합니다.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>PowerShell 버전 1을 사용하여 인증 데이터 설정

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>PowerShell 버전 1을 사용하여 인증 데이터 읽기

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>인증 전화 및 인증 메일 옵션 읽기

PowerShell 버전 1에서 아래 명령을 사용하여 **인증 전화** 와 **인증 메일** 을 읽습니다.

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>PowerShell 버전 2 사용

시작하려면 [Azure AD 버전 2 PowerShell 모듈을 다운로드하여 설치합니다](/powershell/module/azuread/).

`Install-Module`을 지원하는 최신 버전의 PowerShell에서 빠르게 설치하려면 다음 명령을 실행합니다. 첫 줄에서는 모듈이 이미 설치되어 있는지 확인합니다.

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

모듈을 설치한 후 다음 단계를 사용하여 각 필드를 구성합니다.

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>PowerShell 버전 2를 사용하여 인증 데이터 설정

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>PowerShell 버전 2를 사용하여 인증 데이터 읽기

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>다음 단계

사용자에 대한 인증 연락처 정보가 미리 채워져 있으면 다음 자습서를 완료하여 셀프 서비스 암호 재설정을 사용하도록 설정합니다.

> [!div class="nextstepaction"]
> [Azure AD 셀프 서비스 암호 재설정 사용](tutorial-enable-sspr.md)
