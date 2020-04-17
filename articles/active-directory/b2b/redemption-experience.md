---
title: B2B 공동 작업에서 초대 사용 - Azure AD
description: 개인 정보 보호 조건에 대한 규약을 비롯한 최종 사용자에 대한 Azure AD B2B 협업 초대 상환 환경을 설명합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0645807aa40557c163643f1393c310668518f9be
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535141"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 협업 초대 상환

이 문서에서는 게스트 사용자가 리소스에 액세스할 수 있는 방법과 사용자가 발생하는 동의 프로세스에 대해 설명합니다. 게스트에게 초대 이메일을 보내는 경우 초대에는 게스트가 앱 또는 포털에 액세스하기 위해 사용할 수 있는 링크가 포함됩니다. 초대 이메일은 게스트가 리소스에 액세스할 수 있는 방법 중 하나일 뿐입니다. 또는 디렉토리에 게스트를 추가하고 공유하려는 포털 또는 앱에 대한 직접 링크를 제공할 수 있습니다. 사용 방법에 관계없이 게스트는 첫 번째 동의 절차를 안내합니다. 이 프로세스는 숙박고객이 개인 정보 보호 약관에 동의하고 설정한 [모든 이용 약관에](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) 동의하도록 합니다.

디렉터리에 게스트 사용자를 추가하면 게스트 사용자 계정에 동의 상태(PowerShell에서 볼 수 있음)가 있으며, 이 상태는 처음에 **수락 보류**중으로 설정되어 있습니다. 이 설정은 게스트가 초대를 수락하고 개인 정보 보호 정책 및 이용 약관에 동의할 때까지 유지됩니다. 그 후 동의 상태가 **수락됨으로**변경되고 동의 페이지가 더 이상 게스트에게 표시되지 않습니다.

   > [!IMPORTANT]
   > **2021년 3월 31일부터**Microsoft는 B2B 공동 작업 시나리오에 대해 관리되지 않는 Azure AD 계정 및 테넌드를 만들어 초대 사용의 상환을 더 이상 지원하지 않습니다. 준비에, 우리는 고객이 [이메일 일회성 암호 인증을](one-time-passcode.md)선택하는 것이 좋습니다. 이 공개 미리 보기 기능에 대한 피드백을 환영하며 더 많은 공동 작업 방법을 만들 수 있게 되어 기쁩니다.

## <a name="redemption-through-the-invitation-email"></a>초대 이메일을 통해 상환

[Azure 포털을 사용하여](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)디렉터리에 게스트 사용자를 추가하면 초대 전자 메일이 해당 프로세스의 게스트에게 전송됩니다. [PowerShell을 사용하여](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) 디렉터리에 게스트 사용자를 추가할 때 초대 이메일을 보내도록 선택할 수도 있습니다. 다음은 이메일에 있는 링크를 사용할 때 게스트의 경험에 대한 설명입니다.

1. 게스트는 Microsoft 초대장에서 보낸 [초대 전자 메일을](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) **받습니다.**
2. 게스트가 이메일에서 **시작 받기를** 선택합니다.
3. 게스트에 Azure AD 계정, MSA(Microsoft 계정) 또는 페더레이션된 조직의 전자 메일 계정이 없는 경우 MSA를 만들라는 메시지가 [표시됩니다(일회성 암호](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) 기능이 활성화되어 있지 않으면 MSA가 필요하지 않습니다).
4. 투숙객은 아래에 설명된 [동의 를](#consent-experience-for-the-guest) 통해 안내됩니다.

## <a name="redemption-through-a-direct-link"></a>직접 링크를 통해 상환

초대 이메일 대신 게스트에게 앱 또는 포털에 대한 직접 링크를 제공할 수 있습니다. 먼저 [Azure 포털](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) 또는 [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)을 통해 디렉터리에 게스트 사용자를 추가해야 합니다. 그런 다음 직접 사인온 링크를 포함하여 [사용자에게 응용 프로그램을 배포하는 사용자 지정 가능한 방법을](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)사용할 수 있습니다. 게스트가 초대 이메일 대신 직접 링크를 사용하는 경우에도 첫 번째 동의 환경을 통해 안내됩니다.

> [!IMPORTANT]
> 직접 링크는 테넌트에 따라 다릅니다. 즉, 공유 앱이 있는 테넌트에서 게스트를 인증할 수 있도록 테넌트 ID 또는 인증된 도메인을 포함해야 합니다. 인증을 위해 https://myapps.microsoft.com 홈 테넌트로 리디렉션되므로 게스트와 같은 일반적인 URL은 작동하지 않습니다. 다음은 테넌트 컨텍스트가 있는 직접 링크의 몇 가지 예입니다.
 > - 앱 액세스 https://myapps.microsoft.com/?tenantid=&lt패널: ;테넌트 ID&gt; 
 > - 확인된 도메인에 대한 앱 https://myapps.microsoft.com/&lt액세스 패널: ;검증된 도메인&gt;
 > - Azure 포털: https://portal.azure.com/&lt;테넌트 ID&gt;
 > - 개별 앱: [직접 사인온 링크](../manage-apps/end-user-experiences.md#direct-sign-on-links) 사용 방법 보기

직접 링크를 통해 초대 이메일을 권장하는 경우가 있습니다. 이러한 특수한 경우는 조직에 중요한 경우 초대 전자 메일을 계속 보내는 방법을 사용하여 사용자를 초대하는 것이 좋습니다.
 - 사용자는 페더레이션된 조직에 Azure AD 계정, MSA 또는 전자 메일 계정이 없습니다. 일회용 암호 기능을 사용하지 않는 한 게스트는 초대 이메일을 사용하여 MSA를 만드는 단계를 안내해야 합니다.
 - 경우에 따라 연락처 개체(예: Outlook 연락처 개체)와 충돌이 발생하기 때문에 초대된 사용자 개체에는 이메일 주소가 없을 수 있습니다. 이 경우에 사용자는 초대 이메일에서 상환 URL을 클릭해야 합니다.
 - 사용자는 초대된 이메일 주소의 별칭으로 로그인할 수 있습니다. 별칭은 이메일 계정과 연결된 추가 이메일 주소입니다. 이 경우 사용자는 초대 이메일에서 교환 URL을 클릭해야 합니다.

## <a name="invitation-redemption-flow"></a>초대 교환 흐름

사용자가 [초대 이메일에서](invitation-email-elements.md) **초대 수락** 링크를 클릭하면 Azure AD는 아래와 같이 교환 흐름에 따라 초대를 자동으로 사용합니다.

![상환 흐름 다이어그램을 보여주는 스크린샷](media/redemption-experience/invitation-redemption-flow.png)

1. 상환 프로세스는 사용자에게 [MSA(기존 개인 Microsoft 계정)가](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)있는지 확인합니다.

2. 관리자가 직접 [페더레이션을](direct-federation.md)사용하도록 설정한 경우 Azure AD는 사용자의 도메인 접미사가 구성된 SAML/WS-Fed ID 공급자의 도메인과 일치하는지 확인하고 사용자를 미리 구성된 ID 공급자로 리디렉션합니다.

3. 관리자가 Google [페더레이션을](google-federation.md)사용하도록 설정한 경우 Azure AD는 사용자의 도메인 접미사가 gmail.com 또는 googlemail.com 있는지 확인하고 사용자를 Google로 리디렉션합니다.

4. Azure AD는 사용자 기반 검색을 수행하여 [사용자가 기존 Azure AD 테넌트에](what-is-b2b.md#easily-add-guest-users-in-the-azure-ad-portal)있는지 확인합니다.

5. 사용자의 **홈 디렉터리가** 식별되면 사용자가 로그인할 해당 ID 공급자로 전송됩니다.  

6. 1단계에서 4단계가 초대된 사용자에 대한 홈 디렉터리를 찾지 못하는 경우 Azure AD는 초대테넌트가 게스트를 위한 [전자 메일 일회용 암호(OTP)](one-time-passcode.md) 기능을 사용하도록 설정했는지 여부를 결정합니다.

7. [게스트를 위한 전자 메일 일회용 암호가 활성화된](one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)경우 초대된 이메일을 통해 사용자에게 암호가 전송됩니다. 사용자는 Azure AD 로그인 페이지에서 이 암호를 검색하고 입력합니다.

8. 게스트에 대한 전자 메일 일회성 암호가 비활성화된 경우 Azure AD는 Microsoft에서 유지 관리하는 소비자 도메인 목록에 대해 도메인 접미사를 확인합니다. 도메인이 소비자 도메인 목록의 도메인과 일치하는 경우 개인 Microsoft 계정을 만들라는 메시지가 표시됩니다. 그렇지 않으면 사용자에게 [Azure AD 셀프 서비스 계정(바이러스 성](../users-groups-roles/directory-self-service-signup.md) 계정)을 만들라는 메시지가 표시됩니다.

9. Azure AD는 전자 메일에 대한 액세스를 확인하여 Azure AD 자체 서비스 계정(바이러스 성 계정)을 만들려고 시도합니다. 계정 확인은 전자 메일에 코드를 보내고 사용자가 검색하여 Azure AD에 제출하도록 하여 수행됩니다. 그러나 초대된 사용자의 테넌트가 페더레이션된 경우 또는 초대된 사용자의 테넌트에서 AllowEmailVerifiedUsers 필드가 false로 설정된 경우 사용자는 상환을 완료할 수 없으며 흐름에 오류가 발생합니다. 자세한 내용은 Azure [Active Directory B2B 공동 작업 문제 해결을](troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption)참조하십시오.

10. 개인 마이크로 소프트 계정 (MSA)를 만들려면 메시지가 표시됩니다.

11. 올바른 ID 공급자에 인증한 후 사용자는 Azure AD로 리디렉션되어 [동의 환경을 완료합니다.](redemption-experience.md#consent-experience-for-the-guest)  

테넌트 응용 프로그램 링크를 통해 상환되는 JIT(적시) 상환의 경우 8단계에서 10단계까지사용할 수 없습니다. 사용자가 6단계에 도달하고 전자 메일 일회용 암호 기능을 사용할 수 없는 경우 사용자에게 오류 메시지가 전송되고 초대를 사용할 수 없습니다. 이를 방지하기 위해 관리자는 [전자 메일 일회성 암호를 사용하도록 설정하거나](one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) 사용자가 초대 링크를 클릭하도록 해야 합니다.

## <a name="consent-experience-for-the-guest"></a>게스트에 대한 동의 경험

게스트가 파트너 조직의 리소스에 처음으로 액세스하기 위해 로그인하면 다음 페이지를 안내합니다. 

1. 게스트는 초대 하는 조직의 개인 정보 취급 방침을 설명하는 검토 권한 페이지를 **검토합니다.** 사용자는 초대기관의 개인정보 보호정책에 따라 자신의 정보 사용에 **동의해야** 계속할 수 있습니다.

   ![권한 검토 페이지를 보여 주는 스크린샷](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 테넌트 관리자 권한을 가진 사용자가 조직의 개인정보처리방침에 연결할 수 있는 방법에 대한 정보는 [방법: Azure Active Directory에 조직의 개인 정보 추가](https://aka.ms/adprivacystatement)를 참조하세요.

2. 이용 약관이 구성된 경우 게스트는 이용 약관을 열고 검토한 다음 **수락을**선택합니다. 

   ![새 사용 약관을 보여 주는 스크린샷](media/redemption-experience/terms-of-use-accept.png) 

   조직 관계 **관리사용** > [약관](../governance/active-directory-tou.md)에서 **사용** > **약관**보기를 구성할 수 있습니다.

3. 달리 명시되지 않는 한 게스트는 게스트가 액세스할 수 있는 응용 프로그램을 나열하는 앱 액세스 패널로 리디렉션됩니다.

   ![앱 액세스 패널을 보여주는 스크린샷](media/redemption-experience/myapps.png) 

디렉터리에서 게스트의 **초대장이** 값 변경을 **예로**변경됨을 수락했습니다. MSA가 생성된 경우 게스트의 **소스에** **Microsoft 계정이**표시됩니다. 게스트 사용자 계정 속성에 대한 자세한 내용은 [Azure AD B2B 공동 작업 사용자의 속성을](user-properties.md)참조하십시오. 

## <a name="next-steps"></a>다음 단계

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [Azure Active Directory B2B 협업 사용자를 Azure Portal에 추가](add-users-administrator.md)
- [정보 근로자가 Azure Active Directory에 B2B 협업 사용자를 추가하는 방법](add-users-information-worker.md)
- [PowerShell을 사용하여 Azure Active Directory B2B 협업 사용자 추가](customize-invitation-api.md#powershell)
- [게스트 사용자로써 조직 나가기](leave-the-organization.md)
