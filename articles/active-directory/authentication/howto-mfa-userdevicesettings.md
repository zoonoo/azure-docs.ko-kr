---
title: Azure 다단계 인증 - Azure Active Directory에 대한 사용자 설정 관리
description: Azure 다단계 인증에 대한 Azure Active Directory 사용자 설정을 구성하는 방법에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309767"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Azure 다단계 인증에 대한 사용자 설정 관리

Azure 다단계 인증의 사용자를 관리하려면 사용자가 암호를 재설정하거나 MFA에 다시 등록하거나 기존 MFA 세션을 해지하도록 요구할 수 있습니다. 앱 암호를 정의한 사용자의 경우 이러한 암호를 삭제하도록 선택할 수도 있으므로 해당 응용 프로그램에서 레거시 인증이 실패할 수 있습니다. 사용자에게 지원을 제공해야 하거나 보안 상태를 재설정하려는 경우 이러한 작업이 필요할 수 있습니다.

## <a name="manage-user-authentication-options"></a>사용자 인증 옵션 관리

*인증 관리자* 역할이 할당된 경우 사용자가 암호를 재설정하거나, MFA에 다시 등록하거나, 사용자 개체에서 기존 MFA 세션을 해지하도록 요구할 수 있습니다. 사용자 설정을 관리하려면 다음 단계를 완료합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 Azure **Active Directory** > **모든** > **사용자를**선택합니다.
1. 에서 작업을 수행할 사용자를 선택하고 인증 **방법을**선택합니다. 창 맨 위에서 사용자에 대한 다음 옵션 중 하나를 선택합니다.
   - **암호 재설정은** 사용자의 암호를 재설정하고 다음 로그인시 변경해야 하는 임시 암호를 할당합니다.
   - **MFA 를 다시 등록해야** 하므로 사용자가 다음에 서명할 때 새 MFA 인증 방법을 설정하도록 요청합니다.
   - **취소 MFA 세션은** 사용자의 기억된 MFA 세션을 지우고 다음에 장치의 정책에 필요할 때 MFA를 수행하도록 요구합니다.

   ![Azure 포털에서 인증 방법 관리](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>사용자 기존 앱 암호 삭제

필요한 경우 사용자가 만든 모든 앱 암호를 삭제할 수 있습니다. 이러한 앱 암호와 연결된 브라우저가 아닌 앱은 새 앱 암호가 생성될 때까지 작동 중지됩니다. 이 작업을 수행하려면 *전역 관리자* 권한이 필요합니다.

사용자의 앱 암호를 삭제하려면 다음 단계를 완료합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **Azure Active Directory** > **모든** > **사용자를**선택합니다.
1. **다단계 인증을**선택합니다. 이 메뉴 옵션을 보려면 오른쪽으로 스크롤해야 할 수 있습니다. 아래 예제 스크린샷을 선택하여 전체 Azure 포털 창 및 메뉴 위치를 확인합니다.[![](media/howto-mfa-userstates/selectmfa-cropped.png "Azure AD의 사용자 창에서 다단계 인증 선택")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 관리하려는 사용자 한 명 또는 여러 명 옆의 상자를 선택합니다. 빠른 단계 옵션 목록이 오른쪽에 나타납니다.
1. **사용자 설정 관리를**선택한 다음 선택한 다음 예제와 같이 선택한 사용자가 **생성한 모든 기존 앱 암호 삭제**확인란을 선택합니다. ![](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. **저장을**선택한 다음 **닫습니다.**

## <a name="next-steps"></a>다음 단계

이 문서에서는 개별 사용자 설정을 구성하는 데 도움이 되었습니다. Azure 다단계 인증 서비스 설정을 구성하려면 [Azure 다단계 인증 설정 구성](howto-mfa-mfasettings.md) 을 참조하세요.

사용자에게 도움이 필요한 경우 [Azure 다단계 인증에 대한 사용자 가이드를](../user-help/multi-factor-authentication-end-user.md)참조하십시오.
