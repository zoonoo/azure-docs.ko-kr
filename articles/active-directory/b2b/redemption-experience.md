---
title: B2B 공동 작업의 초대 상환-Azure AD
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
ms.openlocfilehash: 2bbfd0027f9de93ee939815853f5141044404c53
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199502"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 협업 초대 상환

이 문서에서는 게스트 사용자가 리소스에 액세스 하는 방법 및 사용자가 발견할 수 있는 동의 프로세스를 설명 합니다. 게스트에 게 초대 전자 메일을 보내면 방문자가 앱 또는 포털에 대 한 액세스 권한을 얻기 위해 사용할 수 있는 링크가 초대에 포함 됩니다. 초대 전자 메일은 게스트가 리소스에 액세스할 수 있는 방법 중 하나일 뿐입니다. 또는 디렉터리에 게스트를 추가 하 고 공유 하려는 포털 또는 앱에 대 한 직접 링크를 제공할 수 있습니다. 게스트는 사용 하는 방법에 관계 없이 첫 번째 시간 승인 프로세스를 안내 합니다. 이 프로세스를 통해 게스트가 개인 정보 취급 방침에 동의 하 고 설정한 [사용 약관](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) 에 동의 하 게 됩니다.

게스트 사용자를 디렉터리에 추가 하면 게스트 사용자 계정에 초기에 **Pendingacceptance**으로 설정 된 동의 상태 (PowerShell에서 볼 수 있음)가 있습니다. 이 설정은 게스트가 초대를 수락 하 고 개인 정보 취급 방침 및 사용 약관에 동의할 때까지 유지 됩니다. 그러면 동의 상태가 **수락 됨**으로 변경 되 고 동의 페이지가 더 이상 게스트에 표시 되지 않습니다.

   > [!IMPORTANT]
   > **2021 년 3 월 31**일부 터 MICROSOFT는 B2B 공동 작업 시나리오에 대해 관리 되지 않는 Azure AD 계정 및 테 넌 트를 만들어 초대 상환을 더 이상 지원 하지 않습니다. 준비가 되 면 고객이 [일회용 암호 인증을 전자 메일로](one-time-passcode.md)옵트인 하는 것이 좋습니다. 이 공개 미리 보기 기능에 대 한 사용자 의견을 환영 하며 공동 작업을 위해 훨씬 더 많은 방법을 만들어 보세요.

## <a name="redemption-through-the-invitation-email"></a>초대 이메일을 통해 상환

[Azure Portal를 사용 하](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)여 디렉터리에 게스트 사용자를 추가 하는 경우 프로세스의 게스트로 초대 이메일이 전송 됩니다. 또한 [PowerShell을 사용](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) 하 여 게스트 사용자를 디렉터리에 추가 하는 경우 초대 전자 메일을 보내도록 선택할 수 있습니다. 전자 메일의 링크를 사용할 때 게스트의 경험에 대 한 설명은 다음과 같습니다.

1. 게스트가 **Microsoft 초대**에서 보낸 [초대 전자 메일](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) 을 받습니다.
2. 게스트는 전자 메일의 **초대 수락** 을 선택 합니다.
3. 게스트는 자신의 자격 증명을 사용 하 여 디렉터리에 로그인 합니다. 게스트에 디렉터리에 페더레이션 할 수 있는 계정이 없고 [전자 메일 OTP (일회용 암호)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) 기능을 사용할 수 없는 경우 게스트에 개인 [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) 또는 [Azure AD 셀프 서비스 계정을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)만들라는 메시지가 표시 됩니다. 자세한 내용은 [초대 상환 흐름](#invitation-redemption-flow) 을 참조 하세요.
4. 게스트는 아래에 설명 된 [동의 환경을](#consent-experience-for-the-guest) 안내 합니다.

## <a name="redemption-through-a-direct-link"></a>직접 링크를 통해 상환

초대 전자 메일의 대 안으로 게스트에 앱 또는 포털에 대 한 직접 링크를 제공할 수 있습니다. 먼저 [Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) 또는 [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)을 통해 게스트 사용자를 디렉터리에 추가 해야 합니다. 그런 다음 [사용자 지정 가능한 방법](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)중 하나를 사용 하 여 직접 로그온 링크를 비롯 한 응용 프로그램을 사용자에 게 배포할 수 있습니다. 게스트가 초대 전자 메일 대신 직접 링크를 사용 하는 경우 처음에는 첫 번째 시간 승인 환경에 따라 진행 됩니다.

> [!IMPORTANT]
> 직접 링크는 테 넌 트와 관련 되어야 합니다. 즉, 테 넌 트 ID 또는 확인 된 도메인을 포함 해야 합니다. 그러면 공유 앱이 있는 테 넌 트에서 게스트를 인증할 수 있습니다. 와 같은 공통 URL은 https://myapps.microsoft.com 인증을 위해 해당 홈 테 넌 트로 리디렉션되도록 게스트에서 작동 하지 않습니다. 다음은 테 넌 트 컨텍스트가 포함 된 직접 링크의 몇 가지 예입니다.
 > - 앱 액세스 패널: https://myapps.microsoft.com/?tenantid=&lt , 테 넌 트 id&gt; 
 > - 확인 된 도메인의 앱 액세스 패널: https://myapps.microsoft.com/&lt ; 확인 된 도메인&gt;
 > - Azure Portal: https://portal.azure.com/&lt , 테 넌 트 id&gt;
 > - 개별 앱: [직접 로그온 링크](../manage-apps/end-user-experiences.md#direct-sign-on-links) 를 사용 하는 방법을 참조 하세요.

직접 링크를 통해 초대 전자 메일을 권장 하는 경우도 있습니다. 이러한 특수 사례가 조직에 중요 한 경우 초대 전자 메일을 계속 보내는 방법을 사용 하 여 사용자를 초대 하는 것이 좋습니다.
 - 사용자에 게 페더레이션 조직의 Azure AD 계정, MSA 또는 전자 메일 계정이 없습니다. 일회용 암호 기능을 사용 하지 않는 한 게스트는 MSA를 만드는 단계를 안내 하는 초대 전자 메일을 사용 해야 합니다.
 - 경우에 따라 연락처 개체(예: Outlook 연락처 개체)와 충돌이 발생하기 때문에 초대된 사용자 개체에는 이메일 주소가 없을 수 있습니다. 이 경우에 사용자는 초대 이메일에서 상환 URL을 클릭해야 합니다.
 - 사용자는 초대된 이메일 주소의 별칭으로 로그인할 수 있습니다. 별칭은 전자 메일 계정과 연결 된 추가 전자 메일 주소입니다. 이 경우 사용자는 초대 전자 메일에서 상환 URL을 클릭 해야 합니다.

## <a name="invitation-redemption-flow"></a>초대 상환 흐름

사용자가 [초대 전자 메일](invitation-email-elements.md)의 **초대 수락** 링크를 클릭 하면 Azure AD는 아래와 같이 상환 흐름에 따라 초대를 자동으로 교환 합니다.

![상환 흐름 다이어그램을 보여 주는 스크린샷](media/redemption-experience/invitation-redemption-flow.png)

**사용자의 UPN (사용자 계정 이름)이 기존 Azure AD 및 개인 MSA 계정과 일치 하는 경우 사용자에 게 사용할 계정을 선택 하 라는 메시지가 표시 됩니다.*

1. Azure AD는 사용자 기반 검색을 수행 하 여 [기존 AZURE ad 테 넌 트](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b#easily-add-guest-users-in-the-azure-ad-portal)에 사용자가 존재 하는지 확인 합니다.

2. 관리자가 [직접 페더레이션을](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)사용 하도록 설정 하는 경우 Azure AD는 사용자의 도메인 접미사가 구성 된 SAML/WS-급지됨 id 공급자의 도메인과 일치 하는지 확인 하 고 사용자를 미리 구성 된 id 공급자로 리디렉션합니다.

3. 관리자가 [Google 페더레이션을](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)사용 하도록 설정 하는 경우 Azure AD는 사용자의 도메인 접미사가 gmail.com 또는 googlemail.com 인지 확인 하 고 사용자를 google으로 리디렉션합니다.

4. 상환 프로세스는 사용자에 게 기존 [MSA (개인 Microsoft 계정)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)가 있는지 여부를 확인 합니다.

5. 사용자의 **홈 디렉터리** 를 확인 한 후에는 해당 id 공급자로 사용자를 전송 하 여 로그인 합니다.  

6. 1 ~ 4 단계에서 초대 된 사용자의 홈 디렉터리를 찾지 못하면 Azure AD는 초대 하는 테 넌 트가 게스트에 대해 [전자 메일 OTP (일회용 암호)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) 기능을 사용 하도록 설정 했는지 여부를 확인 합니다.

7. [게스트에 대 한 전자 메일 일회용 암호를 사용 하는](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode)경우 초대 된 전자 메일을 통해 사용자에 게 암호를 보냅니다. 사용자가 Azure AD 로그인 페이지에서이 암호를 검색 하 고 입력 합니다.

8. 게스트에 대 한 전자 메일 일회용 암호를 사용 하지 않도록 설정 하는 경우 Azure AD는 도메인 접미사를 확인 하 여 소비자 계정에 속하는지 확인 합니다. 이 경우 사용자에 게 개인 [Microsoft 계정](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)를 만들라는 메시지가 표시 됩니다. 그렇지 않으면 사용자에 게 [AZURE AD 셀프 서비스 계정을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)만들라는 메시지가 표시 됩니다.

9. Azure AD는 전자 메일에 대 한 액세스를 확인 하 여 [AZURE ad 셀프 서비스 계정을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup) 만들려고 시도 합니다. 전자 메일에 코드를 전송 하 고 사용자가 Azure AD에 검색 하 여 전송 하도록 하 여 계정을 확인 합니다. 그러나 초대한 사용자의 테 넌 트가 페더레이션 되었거나 초대 된 사용자의 테 넌 트에서 AllowEmailVerifiedUsers 필드가 false로 설정 된 경우 사용자는 상환을 완료할 수 없으며 흐름에서 오류가 발생 합니다. 자세한 내용은 [B2B 공동 작업 Azure Active Directory 문제 해결](https://docs.microsoft.com/azure/active-directory/b2b/troubleshoot#the-user-that-i-invited-is-receiving-an-error-during-redemption)을 참조 하세요.

10. 사용자에 게 [MSA (개인 Microsoft 계정)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)를 만들라는 메시지가 표시 됩니다.

11. 올바른 id 공급자를 인증 한 후 사용자가 Azure AD로 리디렉션되어 [승인 환경을](https://docs.microsoft.com/azure/active-directory/b2b/redemption-experience#consent-experience-for-the-guest)완료 합니다.  

테 넌 트 응용 프로그램 링크를 통해 상환 하는 JIT (just-in-time) 횟수가의 경우 8 ~ 10 단계를 사용할 수 없습니다. 사용자가 6 단계에 도달 하 고 전자 메일 일회용 암호 기능을 사용 하도록 설정 하지 않은 경우 사용자는 오류 메시지를 받고 초대를 전환할 수 없습니다. 이 오류를 방지 하려면 관리자가 [전자 메일 일회용 암호를 사용 하도록 설정](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode) 하거나 사용자가 초대 링크를 클릭 해야 합니다.

## <a name="consent-experience-for-the-guest"></a>게스트에 대 한 동의 환경

게스트가 파트너 조직의 리소스에 처음으로 액세스 하기 위해 로그인 하는 경우 다음 페이지를 안내 합니다. 

1. 게스트는 초대 조직의 개인 정보 취급 방침을 설명 하는 **권한 검토** 페이지를 검토 합니다. 사용자는 초대 하는 조직의 개인 정보 취급 방침에 따라 사용자의 정보 사용을 **수락** 해야 계속할 수 있습니다.

   ![권한 검토 페이지를 보여 주는 스크린샷](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 테넌트 관리자 권한을 가진 사용자가 조직의 개인정보처리방침에 연결할 수 있는 방법에 대한 정보는 [방법: Azure Active Directory에 조직의 개인 정보 추가](https://aka.ms/adprivacystatement)를 참조하세요.

2. 사용 약관을 구성 하면 게스트가 열리고 사용 약관을 검토 한 다음 **동의**를 선택 합니다. 

   ![새 사용 약관을 보여 주는 스크린샷](media/redemption-experience/terms-of-use-accept.png) 

   **조직 관계** (또는 **외부 id**) > **사용 약관**에 [사용 약관](../governance/active-directory-tou.md) 을 구성할 수 있습니다.

3. 달리 지정 하지 않는 한 게스트는 게스트가 액세스할 수 있는 응용 프로그램을 나열 하는 앱 액세스 패널로 리디렉션됩니다.

   ![앱 액세스 패널을 보여 주는 스크린샷](media/redemption-experience/myapps.png) 

디렉터리에서 방문자의 **초대 허용** 값이 **예**로 변경 됩니다. MSA를 만든 경우 게스트의 **원본** 에 **Microsoft 계정이**표시 됩니다. 게스트 사용자 계정 속성에 대 한 자세한 내용은 [AZURE AD B2B 공동 작업 사용자의 속성](user-properties.md)을 참조 하세요. 

## <a name="next-steps"></a>다음 단계

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [Azure Active Directory B2B 협업 사용자를 Azure Portal에 추가](add-users-administrator.md)
- [정보 근로자가 Azure Active Directory에 B2B 협업 사용자를 추가하는 방법](add-users-information-worker.md)
- [PowerShell을 사용하여 Azure Active Directory B2B 협업 사용자 추가](customize-invitation-api.md#powershell)
- [게스트 사용자로써 조직 나가기](leave-the-organization.md)
