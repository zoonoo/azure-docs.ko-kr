---
title: Azure Portal에서 B2B 공동 작업 사용자 - Azure Active Directory | Microsoft Docs
description: 관리자가 Azure AD(Azure Active Directory) B2B 공동 작업을 사용하여 파트너 조직에서 해당 디렉터리에 게스트 사용자를 추가할 수 있는 방법을 보여줍니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 2/6/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: e43123e77a5c1cfb37f51c472420d3d7f2d440c9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173371"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Azure Active Directory B2B 공동 작업 사용자를 Azure Portal에 추가

전역 관리자 또는 제한된 관리자 디렉터리 역할 중 하나에 할당된 사용자로서 Azure Portal을 사용하여 B2B 공동 작업 사용자를 초대할 수 있습니다. 게스트 사용자를 디렉터리, 그룹 또는 애플리케이션에 초대할 수 있습니다. 다음 방법 중 하나를 통해 사용자를 초대한 후 초대된 사용자의 계정은 *게스트*의 사용자 유형으로 Azure AD(Azure Active Directory)에 추가됩니다. 게스트 사용자는 리소스에 액세스하려면 해당 초대를 사용해야 합니다.

게스트 사용자를 디렉터리에 추가한 후에 게스트 사용자에게 공유 앱에 대한 직접 링크를 보낼 수 있습니다. 또는 게스트 사용자는 초대 이메일에서 상환 URL을 클릭할 수 있습니다. 상환 프로세스에 대한 자세한 내용은 [B2B 공동 작업 초대 상환](redemption-experience.md)을 참조하세요.

> [!IMPORTANT]
> [방법: Azure Active Directory에서 조직의 개인 정보를 추가](https://aka.ms/adprivacystatement)의 단계를 수행하여 조직 개인정보처리방침의 URL을 추가해야 합니다. 첫 번째 초대 상환 프로세스의 일환으로 초대된 사용자는 계속하여 개인정보취급방침 사용 약관에 동의해야 합니다. 

## <a name="add-guest-users-to-the-directory"></a>디렉터리에 게스트 사용자 추가

B2B 공동 작업 사용자를 디렉터리에 추가하려면 다음 단계를 수행합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **관리**에서 **사용자**를 선택합니다.
4. **새 게스트 사용자**를 선택합니다.

   ![새 게스트 사용자의 UI 내 위치 표시](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
   > [!NOTE]
   > **조직 관계** 페이지에서 **새 게스트 사용자** 옵션을 사용할 수도 있습니다. **Azure Active Directory**의 **관리** 아래에서 **조직 관계**를 선택합니다.

5. **사용자 이름**에서 외부 사용자의 이메일 주소를 입력합니다. 필요에 따라 환영 메시지가 포함됩니다. 예를 들면 다음과 같습니다.

   ![새 게스트 사용자의 UI 내 위치 표시](./media/add-users-administrator/InviteGuest.png) 

    > [!NOTE]
    > 그룹 이메일 주소는 지원되지 않습니다. 개인용 이메일 주소를 입력하세요. 또한 일부 이메일 공급 기업에서는 수신함 필터링 등을 지원하기 위해 사용자가 이메일 주소에 더하기 기호(+) 및 추가 텍스트를 추가하는 것을 허용합니다. 그러나 Azure AD는 현재 이메일 주소에서 더하기 기호를 지원하지 않습니다. 배달 문제를 방지하려면 @ 기호 뒤에 오는 모든 문자 및 더하기 기호를 생략합니다.

6. **초대**를 선택하여 게스트 사용자에게 자동으로 초청을 발송합니다. 
 
초대를 발송한 후 사용자 계정이 디렉터리에 게스트로 자동 추가됩니다.


![게스트 사용자 유형의 B2B 사용자 표시](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>그룹에 게스트 사용자 추가
B2B 공동 작업 사용자를 Azure AD 관리자로 그룹에 수동으로 추가할 필요가 있는 경우 다음 단계를 수행합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **관리**에서 **그룹**을 선택합니다.
4. 그룹을 선택합니다(또는 **새 그룹**을 클릭하여 새 그룹을 만듭니다). B2B 게스트 사용자가 그룹에 포함된다는 것을 그룹 설명에 포함하는 것이 좋습니다.
5. **구성원**을 선택합니다. 
6. 다음 중 하나를 수행합니다.
   - 게스트 사용자가 이미 디렉터리에 있으면 B2B 사용자를 검색합니다. 사용자를 선택한 다음, **선택**을 클릭하여 사용자를 그룹에 추가합니다.
   - 게스트 사용자가 디렉터리에 존재하지 않는 경우 검색 상자에 해당 이메일 주소를 입력하고, 선택적 개인 메시지를 입력한 다음, **선택**을 클릭하여 그룹에 초대합니다. 초대가 초대된 사용자에게 자동으로 이동합니다.
     
     ![게스트 구성원을 추가하려면 초대 단추 추가](./media/add-users-administrator/GroupInvite.png)
   
또한 Azure AD B2B 공동 작업을 통해 동적 그룹을 사용할 수 있습니다. 자세한 내용은 [동적 그룹 및 Azure Active Directory B2B 공동 작업](use-dynamic-groups.md)을 참조합니다.

## <a name="add-guest-users-to-an-application"></a>애플리케이션에 게스트 사용자 추가

B2B 공동 작업 사용자를 Azure AD 관리자로 애플리케이션에 추가하려면 다음 단계를 수행합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **관리**에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 선택합니다.
4. 게스트 사용자를 추가하려는 애플리케이션을 선택 합니다.
5. 애플리케이션의 대시보드에서 **총 사용자 수**를 선택하여 **사용자 및 그룹** 창을 엽니다.

    ![사용자 및 그룹 열기를 추가하기 위해 총 사용자 수 단추](./media/add-users-administrator/AppUsersAndGroups.png)

6. **사용자 추가**를 선택합니다.
7. **할당 추가**에서 **사용자 및 그룹**을 선택합니다.
8. 다음 중 하나를 수행합니다.
   - 게스트 사용자가 이미 디렉터리에 있으면 B2B 사용자를 검색합니다. 사용자를 선택하고, **선택**을 클릭한 다음, **할당**을 클릭하여 사용자를 앱에 추가합니다.
   - 아직 디렉터리에 게스트 사용자가 없으면 **멤버 선택 또는 외부 사용자 초대**에서 사용자의 이메일 주소를 입력합니다. 메시지 상자에서 선택적 개인 메시지를 입력합니다. 메시지 상자에서 **초대**를 클릭합니다.
           
       ![게스트 구성원을 추가하려면 초대 단추 추가](./media/add-users-administrator/AppInviteUsers.png)
   
      **선택**을 클릭한 다음, **할당**을 클릭하여 사용자를 앱에 추가합니다. 초대가 초대된 사용자에게 자동으로 이동합니다.

9. 게스트 사용자가 **기본 액세스**의 할당된 역할을 포함하는 애플리케이션의 **사용자 및 그룹** 목록에 나타납니다. 역할을 변경하려는 경우 다음을 수행합니다.
   - 게스트 사용자를 선택한 다음, **편집**을 선택합니다. 
   - **할당 편집**에서 **역할 선택**을 클릭하고, 선택한 사용자에게 할당할 역할을 선택합니다.
   - **선택**을 클릭합니다.
   - **할당**을 클릭합니다.
 
## <a name="resend-invitations-to-guest-users"></a>게스트 사용자에게 초대 다시 보내기

게스트 사용자가 초대를 아직 사용하지 않은 경우 초대 이메일을 다시 보낼 수 있습니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory**를 선택합니다.
3. **관리**에서 **사용자**를 선택합니다.
5. 사용자 계정을 선택합니다.
6. **관리**에서 **프로필**을 선택합니다.
7. 사용자가 초대를 아직 수락하지 않은 경우 **초대 다시 보내기** 옵션을 사용할 수 있습니다. 다시 보내려면 이 단추를 선택합니다.

   ![사용자 프로필에서 초대 옵션 다시 보내기](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> 사용자를 특정 앱으로 인도하는 초대를 다시 보내는 경우 새 초대에 담긴 링크는 대신에 사용자를 최상위 액세스 패널로 이동시킨다는 것을 이해합니다.

## <a name="next-steps"></a>다음 단계

- 비 Azure AD 관리자가 B2B 게스트 사용자를 추가할 수 있는 방법을 알려면 [정보 작업자가 B2B 공동 작업 사용자를 추가하는 방법은?](add-users-information-worker.md)을 참조합니다.
- 초대 이메일에 대한 내용은 [B2B 공동 작업 이메일 요소](invitation-email-elements.md)를 참조합니다.

