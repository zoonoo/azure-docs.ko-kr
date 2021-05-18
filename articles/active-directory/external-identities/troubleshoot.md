---
title: B2B 협업 문제 해결 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 협업과 관련된 일반 문제를 해결하는 방법
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 04/12/2021
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc9424af07125977bc74a62a6bca97b8c10b8da3
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108317400"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B 협업 문제 해결

Azure AD(Azure Active Directory) B2B 협업과 관련된 일반적인 문제에 대한 몇 가지 해결책은 다음과 같습니다.

   > [!IMPORTANT]
   > - **2021년 하반기부터** Google은 [웹 보기 로그인 지원을 중단](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)합니다. B2B 초대 또는 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md)에 Google 페더레이션을 사용하거나 Gmail에서 셀프 서비스 등록을 사용하는 경우, 앱에서 포함된 웹 보기를 사용하여 사용자를 인증하면 Google Gmail 사용자는 로그인할 수 없습니다. [자세한 정보를 알아보세요](google-federation.md#deprecation-of-web-view-sign-in-support).
   > - **2021년 10월부터** Microsoft는 B2B 협업 시나리오에 대해 관리되지 않는 Azure AD 계정과 테넌트를 만들어 더 이상 초대 상환을 지원하지 않습니다. 준비가 되면 고객이 [이메일 일회성 암호 인증](one-time-passcode.md)을 옵트인하는 것이 좋습니다. 이 공개 미리 보기 기능에 대한 사용자 의견을 환영하며 협업을 위해 훨씬 더 많은 방법을 만들어 냈습니다.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>외부 사용자를 추가했지만 [전체 주소 목록]이나 사용자 선택에서 볼 수 없습니다.

외부 사용자가 목록에 채워지지 않은 경우 개체를 복제하는 데 몇 분이 걸릴 수 있습니다.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B 게스트 사용자가 SharePoint Online/OneDrive 사용자 선택에 표시되지 않습니다.

SPO(SharePoint Online) 사용자 선택에서 기존 게스트 사용자를 검색하는 기능은 기존 동작과의 일치를 위해 기본적으로 꺼져 있습니다.

이 기능은 테넌트 및 사이트 모음 수준에서 설정 'ShowPeoplePickerSuggestionsForGuestUsers'를 통해 이 기능을 사용하도록 설정할 수 있습니다. 또한 멤버가 디렉터리에서 모든 기존 게스트 사용자를 검색할 수 있도록 허용하는 Set-SPOTenant 및 Set-SPOSite cmdlet을 사용하여 이 기능을 설정할 수 있습니다. 테넌트 범위에 대한 변경 내용은 이미 프로비전된 SPO 사이트에 영향을 주지 않습니다.

## <a name="invitations-have-been-disabled-for-directory"></a>디렉터리에 대한 초대가 사용하도록 설정되지 않았습니다.

사용자를 초대할 수 있는 권한이 없다는 알림이 표시되면 Azure Active Directory > 사용자 설정 > 외부 사용자 > 외부 협업 관리 설정에서 사용자 계정이 외부 사용자를 초대할 권한이 있는지 확인합니다.

![외부 사용자 설정을 보여 주는 스크린샷](media/troubleshoot/external-user-settings.png)

최근에 이러한 설정을 수정했거나 사용자에게 [게스트 초대자] 역할을 할당한 경우 변경 내용이 적용되는 데 15-60분 정도 걸릴 수 있습니다.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>상환 중에 내가 초대한 사용자에게 오류가 발생했습니다.

일반적인 오류는 다음과 같습니다.

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>초대 대상자의 관리자가 테넌트에 EmailVerified 사용자를 만들지 못하도록 했습니다.

Azure Active Directory를 사용하는 조직의 사용자를 초대하였으나 특정 사용자의 계정이 없는(예: Azure AD contoso.com에 존재하지 않는 사용자) 경우입니다. contoso.com의 관리자가 정책을 사용하여 사용자를 만들지 못하게 할 수 있습니다. 사용자는 외부 사용자가 허용된 경우인지 해당 관리자에게 확인해야 합니다. 외부 사용자의 관리자가 자체 도메인의 전자 메일 확인 사용자를 허용해야 할 수도 있습니다(전자 메일 확인 사용자 허용은 이 [문서](/powershell/module/msonline/set-msolcompanysettings)를 확인).

![테넌트가 이메일 확인 사용자를 허용하지 않음을 나타내는 오류](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>외부 사용자가 이미 페더레이션된 도메인에 존재하지 않습니다.

페더레이션 인증을 사용하려고 하며 사용자가 Azure Active Directory에 아직 없는 경우에는 사용자를 초대할 수 없습니다.

이 문제를 해결하려면 외부 사용자의 관리자가 사용자 계정을 Azure Active Directory와 동기화해야 합니다.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>일반적으로 잘못된 문자인 ‘\#’은 Azure AD와 어떻게 동기화됩니까?

초대된 계정 user@contoso.com becomes user_contoso.com#EXT#@fabrikam.onmicrosoft.com이므로 “\#”은 Azure AD B2B 협업 또는 외부 사용자에 대해 예약된 UPN 문자입니다. 따라서 온-프레미스에서 가져온 UPN의 \#은 Azure Portal에 로그인할 때 허용되지 않습니다. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>동기화된 그룹에 외부 사용자를 추가할 때 오류가 발생합니다.

외부 사용자는 "할당된" 그룹 또는 "보안" 그룹에만 추가할 수 있으며 온-프레미스에 마스터된 그룹에는 추가할 수 없습니다.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>외부 사용자가 상환할 전자 메일을 받지 못했습니다.

초대 대상자는 ISP 또는 스팸 필터를 확인하여 Invites@microsoft.com 주소가 허용되는지 확인해야 합니다.

> [!NOTE]
> 중국의 21Vianet에서 운영하는 Azure 서비스의 경우 발신자 주소는 Invites@oe.21vianet.com입니다.

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>사용자 지정 메시지가 초대 메시지에 포함되지 않는다는 것을 확인했습니다.

개인 정보 보호 법률을 준수하기 위해 다음 경우에 API에는 전자 메일 초대의 사용자 지정 메시지가 포함되지 않습니다.

- 초대자에게 초대하는 테넌트의 전자 메일 주소가 없는 경우
- AppService 보안 주체가 초대를 보내는 경우

이 시나리오가 중요한 경우 API 초대 전자 메일을 표시하지 않으면서 선택한 전자 메일 메커니즘을 통해 전송할 수 있습니다. 조직의 법률 자문에게 문의하여 이러한 방식으로 전송하는 전자 메일이 개인 정보 보호 법률을 준수하는지도 확인하세요.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Azure 리소스에 로그인을 시도할 때 "AADSTS65005" 오류가 표시됩니다.

게스트 계정이 있는 사용자는 로그인할 수 없으며 다음과 같은 오류 메시지가 수신됩니다.

```plaintext
    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.
```

사용자에게는 Azure 사용자 계정이 있으며 중단되었거나 관리되지 않는 바이럴 테넌트가 있습니다. 또한 테넌트에 전역 관리자가 없습니다.

이 문제를 해결하려면 중단된 테넌트를 수행해야 합니다. [Microsoft Azure Active Directory에서 관리자로서 관리되지 않는 디렉터리 인수](../enterprise-users/domains-admin-takeover.md)를 참조하세요. 또한 네임스페이스를 제어하고 있다는 직접적인 증거를 제공하기 위해 해당 도메인 접미사에 대한 인터넷 연결 DNS에 액세스해야 합니다. 테넌트가 관리되는 상태로 반환된 후 사용자를 두고 확인된 도메인 이름이 조직에 가장 적합한 옵션인지 여부를 고객과 논의하세요.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Just-in-Time 또는 "바이럴" 테넌트가 있는 게스트 사용자가 암호를 재설정할 수 없음

ID 테넌트가 JIT(Just-In-Time) 또는 "바이럴" 테넌트(별개의 관리되지 않는 Azure 테넌트를 의미)인 경우 게스트 사용자만 암호를 재설정할 수 있습니다. 직원이 회사 이메일 주소를 사용하여 서비스에 가입할 때 생성된 [바이럴 테넌트 관리를 인계](../enterprise-users/domains-admin-takeover.md)받는 조직도 있습니다. 조직이 바이럴 테넌트를 인계받고 나면 해당 조직의 관리자만이 사용자 암호를 재설정하거나 SSPR을 사용하도록 설정할 수 있습니다. 필요한 경우 초대 조직 관리자는 디렉터리에서 게스트 사용자 계정을 제거하고 초대를 다시 보낼 수 있습니다.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>게스트 사용자가 AzureAD PowerShell V1 모듈을 사용할 수 없습니다.

2019년 11월 18일까지 디렉터리의 게스트 사용자(**userType** 속성이 **Guest** 와 같은 사용자 계정으로 정의됨)는 AzureAD PowerShell V1 모듈을 사용하지 못하도록 차단됩니다. 앞으로 사용자는 멤버 사용자(여기서 **userType** 은 **Member** 와 같음)이거나 AzureAD PowerShell V2 모듈을 사용해야 합니다.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>Azure 미국 정부 테넌트에서 B2B 공동 작업 게스트 사용자를 초대할 수 없습니다.

Azure 미국 정부 클라우드 내에서 B2B 공동 작업은 현재 Azure 미국 정부 클라우드 내에 있고 B2B 공동 작업을 지원하는 테넌트 간에만 지원됩니다. Azure 미국 정부 클라우드의 일부가 아니거나 아직 B2B 공동 작업을 지원하지 않는 테넌트에 사용자를 초대하는 경우 오류가 발생합니다. 세부 정보 및 제한 사항은 [Azure Active Directory Premium P1 및 P2 변형](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2)을 참조하세요.

## <a name="i-receive-the-error-that-azure-ad-cannot-find-the-aad-extensions-app-in-my-tenant"></a>Azure AD가 내 테넌트에서 aad-extensions-app을 찾을 수 없다는 오류가 표시됩니다.

사용자 지정 사용자 특성 또는 사용자 흐름과 같은 셀프 서비스 등록 기능을 사용하는 경우 `aad-extensions-app. Do not modify. Used by AAD for storing user data.`이라는 앱이 자동으로 만들어집니다. 이는 수집된 특성을 등록하고 사용자 지정하는 사용자에 대한 정보를 저장하는 데 Azure AD 외부 ID로 사용됩니다.

실수로 `aad-extensions-app`을 삭제한 경우 30일 이내에 복구해야 합니다. Azure AD PowerShell 모듈을 사용하여 앱을 복원할 수 있습니다.

1. Azure AD PowerShell 모듈을 시작하고 `Connect-AzureAD`를 실행합니다.
1. 삭제된 앱을 복구하려는 Azure AD 테넌트에 대한 전역 관리자로 사이트에 로그인합니다.
1. PowerShell 명령 `Get-AzureADDeletedApplication`을 실행합니다.
1. 목록에서 표시 이름이 `aad-extensions-app`으로 시작하는 애플리케이션을 찾고 해당 `ObjectId` 속성 값을 복사합니다.
1. PowerShell 명령 `Restore-AzureADDeletedApplication -ObjectId {id}`를 실행합니다. 명령의 `{id}` 부분을 이전 단계의 `ObjectId`로 바꿉니다.

이제 Azure Portal에 복원된 앱이 표시됩니다.

## <a name="next-steps"></a>다음 단계

[B2B Collaboration에 대한 지원 받기](../fundamentals/active-directory-troubleshooting-support-howto.md)
