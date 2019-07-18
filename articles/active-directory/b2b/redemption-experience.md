---
title: B2B 협업의 초대 상환 - Azure Active Directory | Microsoft Docs
description: 개인 정보 보호 조건에 대한 규약을 비롯한 최종 사용자에 대한 Azure AD B2B 협업 초대 상환 환경을 설명합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: a80eaa134130195fce00ee6a4d68851e478c4532
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052505"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 협업 초대 상환

이 문서에서는 게스트 사용자는 리소스 및 접할 이러한 동의 프로세스에 액세스할 수 방법을 설명 합니다. 게스트 초대 전자 메일을 보내는 경우 초대 게스트 교환할 수 링크를 포함 하는 앱 또는 포털 액세스를 가져오려고 합니다. 초대 전자 메일에는 게스트에는 리소스에 액세스 권한을 얻을 수 방법 중 하나일 뿐입니다. 대 안으로, 디렉터리에 게스트를 추가 하 포털 또는 공유 하려는 앱에 대 한 직접 링크를 제공 합니다. 사용 하는 방법에 관계 없이 게스트 처음으로 동의 프로세스를 안내 합니다. 이 프로세스를 통해 게스트 개인정보 처리 방침에 동의 하 고 받아들일 [사용 약관](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) 설정 했습니다.

게스트 사용자 계정에 처음으로 설정 된 승인 상태 (PowerShell에서 볼 수 있는) 디렉터리에 게스트 사용자를 추가 하면 **PendingAcceptance**합니다. 이 설정은 게스트 초대 허용 개인 정보 취급 방침 및 사용 약관에 동의할 때까지 유지 됩니다. 그 후 승인 상태를 변경 **수락**, 동의 페이지는 게스트에 더 이상 표시 됩니다.

## <a name="redemption-through-the-invitation-email"></a>초대 이메일을 통해 상환

디렉터리에 게스트 사용자를 추가 하면 [Azure portal을 사용 하 여](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), 프로세스에서 게스트에 게 초대 전자 메일이 전송 됩니다. 경우 초대 전자 메일을 보낼 수도 있습니다 [PowerShell을 사용 하 여](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) 디렉터리에 게스트 사용자를 추가 합니다. 전자 메일의 링크를 사용 하는 경우는 게스트의 환경 설명은 다음과 같습니다.

1. 게스트 수신를 [초대 전자 메일](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) 에서 전송 되는 **Microsoft 초대**합니다.
2. 게스트 select **시작** 전자 메일에 있습니다.
3. 게스트에 없는 경우 Azure AD 계정을 Microsoft 계정 (MSA) 또는 전자 메일 계정이 페더레이션된 조직에서 MSA를 만드는 메시지가 나타나지만 (경우가 아니면 합니다 [일회용 암호](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) MSA 필요 하지 않은 기능을 활성화 ).
4. 게스트 안내 됩니다는 [환경을 동의](#consent-experience-for-the-guest) 아래에서 설명 합니다.

## <a name="redemption-through-a-direct-link"></a>직접 링크를 통해 상환

초대 전자 메일을 대신 지정할 수 있습니다 게스트 직접 링크를 앱 또는 포털. 통해 디렉터리에 게스트 사용자를 추가 해야 합니다 [Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) 또는 [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)합니다. 중 하나를 사용할 수 있습니다는 [사용자에 게 응용 프로그램을 배포 하는 사용자 지정 가능한 방법을](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), 직접 로그온 링크를 포함 합니다. 초대 전자 메일 대신 직접 링크를 사용 하는 게스트를 하는 경우는 여전히 안내 됩니다 처음으로 동의 경험 합니다.

> [!IMPORTANT]
> 직접 링크를 테 넌 트 별 이어야 합니다. 즉, 테 넌 트 ID를 포함 해야 합니다 또는 공유 앱 인 테 넌 트에서 게스트를 인증할 수 있도록 도메인을 확인 합니다. 공용 URL 같은 https://myapps.microsoft.com 인증에 대 한 해당 홈 테 넌 트로 리디렉션됩니다 없으므로 게스트에 대 한 작동 하지 않습니다. 테 넌 트 컨텍스트와 직접 링크의 몇 가지 예는 다음과 같습니다.
 > - 앱 액세스 패널: https://myapps.microsoft.com/?tenantid=&lt; 테 넌 트 id&gt; 
 > - 확인된 된 도메인에 대 한 앱 액세스 패널: https://myapps.microsoft.com/&lt, 확인 된 도메인&gt;
 > - Azure portal: https://portal.azure.com/&lt; 테 넌 트 id&gt;
 > - 개별 앱: 사용 하는 방법은 [직접 로그온 링크](../manage-apps/end-user-experiences.md#direct-sign-on-links)

직접 링크를 통해 초대 전자 메일 좋습니다 있는 경우도 있습니다. 이러한 특별 한 경우 조직에 중요 한 경우에 계속 초대 전자 메일을 전송 하는 메서드를 사용 하 여 사용자를 초대 하는 것이 좋습니다.
 - 페더레이션된 조직에서 Azure AD 계정, MSA를 또는 전자 메일 계정 사용자가 없습니다. (일회성 암호) 기능을 사용 하는 경우가 아니면 게스트는 MSA를 만드는 과정을 안내 하는 초대 전자 메일을 사용 해야 합니다.
 - 경우에 따라 연락처 개체(예: Outlook 연락처 개체)와 충돌이 발생하기 때문에 초대된 사용자 개체에는 이메일 주소가 없을 수 있습니다. 이 경우에 사용자는 초대 이메일에서 상환 URL을 클릭해야 합니다.
 - 사용자는 초대된 이메일 주소의 별칭으로 로그인할 수 있습니다. (별칭은 이메일 계정과 연결된 추가 이메일 주소입니다.) 이 경우에 사용자는 초대 이메일에서 상환 URL을 클릭해야 합니다.

## <a name="consent-experience-for-the-guest"></a>게스트에 대 한 동의 경험

게스트는 처음에는 파트너 조직의 리소스에에서 액세스 하려면 로그인 하면 다음 페이지를 안내 하는 합니다. 

1. 게스트 검토 합니다 **사용 권한을 검토** 초대 조직의 개인정보취급방침 설명 하는 페이지입니다. 사용자가 해야 합니다 **Accept** 계속 초대 조직의 개인 정보 보호 정책에 따라에서 해당 정보의 사용 합니다.

   ![권한 검토 페이지를 보여 주는 스크린샷](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 테넌트 관리자 권한을 가진 사용자가 조직의 개인정보처리방침에 연결할 수 있는 방법에 대한 정보는 [방법: Azure Active Directory에 조직의 개인 정보 추가](https://aka.ms/adprivacystatement)를 참조하세요.

2. 사용 약관을 구성 하는 경우 게스트 및 사용 약관 검토를 열고 다음 선택 **Accept**합니다. 

   ![새 사용 약관을 보여 주는 스크린샷](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > 참조를 구성할 수 있습니다 [사용 약관](../governance/active-directory-tou.md) 에 **관리** > **조직 관계** > **약관**.

3. 달리 지정 하지 않으면 게스트 게스트 액세스할 수 있는 응용 프로그램을 나열 하는 앱 액세스 패널로 리디렉션됩니다.

   ![앱 액세스 패널을 보여 주는 스크린샷](media/redemption-experience/myapps.png) 

디렉터리에서 게스트의 **초대가 수락 됨** 값으로 변경 **예**합니다. MSA를 만든 경우 게스트 **소스** 표시 **Microsoft 계정**합니다. 게스트 사용자 계정 속성에 대 한 자세한 내용은 참조 하세요. [Azure AD B2B 공동 작업 사용자 속성](user-properties.md)합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
- [Azure Portal에서 Azure Active Directory B2B 공동 작업 사용자 추가](add-users-administrator.md)
- [정보 근로자가 Azure Active Directory에 B2B 공동 작업 사용자를 추가하는 방법](add-users-information-worker.md)
- [PowerShell을 사용하여 Azure Active Directory B2B 공동 작업 사용자 추가](customize-invitation-api.md#powershell)
- [게스트 사용자로써 조직 나가기](leave-the-organization.md)
