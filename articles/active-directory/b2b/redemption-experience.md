---
title: B2B 협업의 초대 사용 - Azure AD
description: 개인 정보 보호 조건에 대한 규약을 비롯한 최종 사용자에 대한 Azure AD B2B 협업 초대 상환 환경을 설명합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6bf5d40262c5991504d3dc62490fb50f6a20592
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826097"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 협업 초대 상환

이 문서에서는 게스트 사용자가 리소스에 액세스하는 방법 및 게스트 사용자가 거치게 될 동의 프로세스에 대해 설명합니다. 게스트에게 초대 이메일을 보낼 때 초대장에는 게스트가 앱 또는 포털에 대한 액세스 권한을 얻기 위해 사용할 수 있는 링크가 포함됩니다. 초대 이메일은 게스트가 리소스에 액세스할 수 있는 방법 중 하나입니다. 디렉터리에 게스트를 추가하고, 공유하려는 포털 또는 앱의 직접 링크를 제공하는 방법도 있습니다. 게스트는 어떤 방법을 사용하든 최초 동의 프로세스를 거쳐야 합니다. 이 프로세스를 통해 게스트는 개인정보처리방침 및 [사용 약관](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)에 동의하게 됩니다.

게스트 사용자를 디렉터리에 추가하면 게스트 사용자 계정의 동의 상태가 처음에는 **PendingAcceptance**로 설정됩니다(PowerShell에서 볼 수 있음). 이 설정은 게스트가 초대를 수락하고 개인정보처리방침 및 사용 약관에 동의할 때까지 유지됩니다. 그 후에는 동의 상태가 **동의함**으로 바뀌고, 동의 페이지가 더 이상 게스트에게 표시되지 않습니다.

   > [!IMPORTANT]
   > **2021년 3월 31일부터** Microsoft는 B2B 협업 시나리오에 대해 관리되지 않는 Azure AD 계정과 테넌트를 만들어 더 이상 초대 상환을 지원하지 않습니다. 준비가 되면 고객이 [이메일 일회성 암호 인증](one-time-passcode.md)을 옵트인하는 것이 좋습니다. 이 공개 미리 보기 기능에 대한 사용자 의견을 환영하며 협업을 위해 훨씬 더 많은 방법을 만들어 냈습니다.

## <a name="redemption-through-the-invitation-email"></a>초대 이메일을 통해 상환

[Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)을 사용하여 디렉터리에 게스트 사용자를 추가하는 과정에서 게스트에게 초대 이메일이 전송됩니다. [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)을 사용하여 디렉터리에 게스트 사용자를 추가할 때 초대 이메일을 보내도록 선택할 수도 있습니다. 다음은 게스트가 이메일의 링크를 사용할 때 거치게 되는 경험에 대한 설명입니다.

1. 게스트가 **Microsoft Invitations**에서 보낸 [초대 이메일](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)을 받습니다.
2. 게스트가 이메일에서 **초대 수락**을 선택합니다.
3. 게스트가 자신의 자격 증명을 사용하여 여러분의 디렉터리에 로그인합니다. 게스트가 디렉터리에 페더레이션할 수 있는 계정을 갖고 있지 않으며 [이메일 OTP(일회용 암호)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) 기능이 설정되어 있지 않으면 [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) 또는 [Azure AD 셀프 서비스 계정](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)을 만들라는 메시지가 게스트에게 표시됩니다. 자세한 내용은 [초대 사용 흐름](#invitation-redemption-flow)을 참조하세요.
4. 게스트는 아래에 설명된 [동의 환경](#consent-experience-for-the-guest)을 거칩니다.

## <a name="redemption-through-a-direct-link"></a>직접 링크를 통해 상환

초대 이메일 대신 게스트에게 앱 또는 포털의 직접 링크를 제공할 수도 있습니다. 그러려면 먼저 [Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) 또는 [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)을 통해 게스트 사용자를 디렉터리에 추가해야 합니다. 그런 다음, 직접 로그온 링크를 비롯한 [사용자 지정 가능한 방법을 사용하여 사용자에게 애플리케이션을 배포](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)할 수 있습니다. 게스트가 초대 이메일 대신 직접 링크를 사용하더라도 최초 동의 과정을 거치게 됩니다.

> [!IMPORTANT]
> 직접 링크는 테넌트와 관련되어야 합니다. 즉, 공유 앱이 있는 테넌트에서 게스트를 인증할 수 있도록 테넌트 ID 또는 확인된 도메인이 포함되어야 합니다. https://myapps.microsoft.com 형식의 일반적인 URL은 인증을 위해 해당 홈 테넌트로 리디렉션하기 때문에 게스트에게는 작동하지 않습니다. 다음은 테넌트 컨텍스트가 포함된 직접 링크의 예입니다.
 > - 앱 액세스 패널: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - 확인된 도메인의 앱 액세스 패널: `https://myapps.microsoft.com/<;verified domain>`
 > - Azure Portal: `https://portal.azure.com/<tenant id>`
 > - 개별 앱: [직접 로그온 링크](../manage-apps/end-user-experiences.md#direct-sign-on-links) 사용 방법을 참조하세요.

다음과 같이 직접 링크를 통해 초대 이메일을 보내는 것이 좋은 경우도 있습니다. 이러한 특수한 사례가 조직에 중요한 경우 초대 이메일을 전송하는 방법을 사용하여 사용자를 초대하는 것이 좋습니다.
 - 사용자에게 페더레이션 조직의 Azure AD 계정, MSA 또는 이메일 계정이 없습니다. 일회용 암호 기능을 사용하지 않는 한, 게스트는 초대 이메일을 사용하여 MSA를 만드는 단계를 거쳐야 합니다.
 - 경우에 따라 연락처 개체(예: Outlook 연락처 개체)와 충돌이 발생하기 때문에 초대된 사용자 개체에는 이메일 주소가 없을 수 있습니다. 이 경우에 사용자는 초대 이메일에서 상환 URL을 클릭해야 합니다.
 - 사용자는 초대된 이메일 주소의 별칭으로 로그인할 수 있습니다. (별칭은 이메일 계정과 연결된 추가 이메일 주소입니다.) 이 경우에 사용자는 초대 이메일에서 상환 URL을 클릭해야 합니다.

## <a name="invitation-redemption-flow"></a>초대 사용 흐름

사용자가 [초대 이메일](invitation-email-elements.md)에서 **초대 수락** 링크를 클릭하면 Azure AD는 아래의 사용 흐름에 따라 자동으로 초대를 사용합니다.

![사용 흐름 다이어그램을 보여주는 스크린샷](media/redemption-experience/invitation-redemption-flow.png)

**사용자의 UPN(사용자 계정 이름)이 기존 Azure AD 및 개인 MSA 계정과 일치하면 사용자에게 사용할 계정을 선택하라는 메시지가 표시됩니다.*

1. Azure AD가 사용자 기반 검색을 수행하여 [기존 Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b#easily-add-guest-users-in-the-azure-ad-portal)에 사용자가 있는지 확인합니다.

2. 관리자가 [직접 페더레이션](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)을 사용하도록 설정했으면 Azure AD는 사용자의 도메인 접미사가 구성된 SAML/WS-Fed ID 공급자의 도메인과 일치하는지 확인한 후 사용자를 미리 구성된 ID 공급자로 리디렉션합니다.

3. 관리자가 [Google 페더레이션](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)을 사용하도록 설정했으면 Azure AD는 사용자의 도메인 접미사가 gmail.com인지 아니면 googlemail.com인지 확인한 후 사용자를 Google로 리디렉션합니다.

4. 사용 프로세스에서는 사용자에게 기존 개인 MSA([Microsoft 계정)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)가 있는지 확인합니다.

5. 사용자의 **홈 디렉터리**가 확인되면 로그인을 위해 사용자가 해당 ID 공급자로 전송됩니다.  

6. 1~4단계에서 초대받은 사용자의 홈 디렉터리를 찾지 못하면 Azure AD는 초대하는 테넌트에서 게스트에게 [이메일 OTP(일회용 암호)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) 기능을 사용하도록 설정했는지 여부를 확인합니다.

7. 게스트에게 [이메일 일회용 암호를 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode)되었으면 초대받은 이메일을 통해 사용자에게 암호가 전송됩니다. 사용자가 Azure AD 로그인 페이지에서 이 암호를 검색하여 입력합니다.

8. 게스트에게 이메일 일회용 암호를 사용하도록 설정되지 않았으면 Azure AD는 도메인 접미사를 검사하여 소비자 계정에 속하는지 확인합니다. 속한다면 개인 [Microsoft 계정](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)을 만들라는 메시지가 사용자에게 표시됩니다. 속하지 않으면 [Azure AD 셀프 서비스 계정](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)을 만들라는 메시지가 사용자에게 표시됩니다.

9. Azure AD는 이메일 액세스를 확인하여 [Azure AD 셀프 서비스 계정](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup) 만들기를 시도합니다. 계정 확인은 이메일로 코드를 전송하고 사용자에게 코드를 검색하여 Azure AD에 제출하도록 요청하는 방식으로 진행됩니다. 그러나 초대받은 사용자의 테넌트가 페더레이션되었거나 초대받은 사용자의 테넌트에서 AllowEmailVerifiedUsers 필드가 false로 설정된 경우에는 사용자가 초대 사용을 완료할 수 없고 흐름에서 오류가 발생합니다. 자세한 내용은 [Azure Active Directory B2B 협업 문제 해결](https://docs.microsoft.com/azure/active-directory/b2b/troubleshoot#the-user-that-i-invited-is-receiving-an-error-during-redemption)을 참조하세요.

10. 개인 [MSA(Microsoft 계정)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)를 만들라는 메시지가 사용자에게 표시됩니다.

11. 올바른 ID 공급자에 인증하면 사용자는 [동의 환경](https://docs.microsoft.com/azure/active-directory/b2b/redemption-experience#consent-experience-for-the-guest)을 완료할 수 있는 Azure AD로 리디렉션됩니다.  

테넌트 애플리케이션 링크를 통해 사용되는 JIT(Just-In-Time) 사용의 경우 8~10단계를 사용할 수 없습니다. 사용자가 6단계에 도달하고 이메일 일회용 암호 기능이 설정되지 않으면 사용자는 오류 메시지를 받게 되고 초대를 사용할 수 없습니다. 이 오류를 방지하려면 관리자는 [이메일 일회용 암호를 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode)하거나 사용자가 초대 링크를 클릭하게 만들어야 합니다.

## <a name="consent-experience-for-the-guest"></a>게스트의 동의 환경

게스트 사용자가 파트너 조직의 리소스에 액세스하기 위해 처음으로 로그인하면 다음과 같은 페이지가 표시됩니다. 

1. 게스트는 초대장을 보낸 조직의 개인정보처리방침을 설명하는 **권한 검토** 페이지를 검토합니다. 사용자는 계속 진행하려면 초대장을 보낸 조직의 개인 정보 정책에 따라 정보 사용에 **동의**해야 합니다.

   ![권한 검토 페이지를 보여 주는 스크린샷](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 테넌트 관리자 권한을 가진 사용자가 조직의 개인정보처리방침에 연결할 수 있는 방법에 대한 정보는 [방법: Azure Active Directory에 조직의 개인 정보 추가](https://aka.ms/adprivacystatement)를 참조하세요.

2. 사용 약관이 구성되면 게스트가 사용 약관을 열고 검토한 다음, **동의**를 선택합니다. 

   ![새 사용 약관을 보여 주는 스크린샷](media/redemption-experience/terms-of-use-accept.png) 

   [사용 약관](../governance/active-directory-tou.md)은 **외부 ID** > **사용 약관**에서 구성할 수 있습니다.

3. 달리 지정하지 않는 한, 게스트는 액세스 가능한 애플리케이션이 나열되는 앱 액세스 패널로 리디렉션됩니다.

   ![앱 액세스 패널을 보여주는 스크린샷](media/redemption-experience/myapps.png) 

디렉터리에서 게스트의 **초대가 수락됨** 값이 **예**로 변경됩니다. MSA를 만든 경우 게스트의 **원본**이 **Microsoft 계정**으로 표시됩니다. 게스트 사용자 계정 속성에 대한 자세한 내용은 [Azure AD B2B 협업 사용자 속성](user-properties.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [Azure Portal에서 Azure Active Directory B2B Collaboration 사용자 추가](add-users-administrator.md)
- [정보 근로자가 Azure Active Directory에 B2B 협업 사용자를 추가하는 방법](add-users-information-worker.md)
- [PowerShell을 사용하여 Azure Active Directory B2B 협업 사용자 추가](customize-invitation-api.md#powershell)
- [게스트 사용자로써 조직 나가기](leave-the-organization.md)
