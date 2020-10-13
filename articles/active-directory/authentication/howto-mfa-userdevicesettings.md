---
title: Azure Multi-Factor Authentication에 대 한 사용자 설정 관리-Azure Active Directory
description: Azure에 대 한 사용자 설정 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다 Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7705792e58a50371a11d0b733b36ffa325681045
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968636"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에 대 한 사용자 설정 관리

Azure Multi-Factor Authentication 사용자를 관리 하려면 사용자가 암호를 재설정 하 고 MFA에 다시 등록 하거나 기존 MFA 세션을 해지 하도록 요구할 수 있습니다. 앱 암호를 정의한 사용자의 경우 이러한 암호를 삭제 하도록 선택 하 여 해당 응용 프로그램에서 레거시 인증이 실패 하도록 할 수도 있습니다. 이러한 작업은 사용자에 게 지원을 제공 해야 하거나 보안 상태를 다시 설정 하려는 경우에 필요할 수 있습니다.

## <a name="manage-user-authentication-options"></a>사용자 인증 옵션 관리

*인증 관리자* 역할이 할당 된 경우 사용자에 게 암호를 재설정 하 고 MFA를 다시 등록 하거나 사용자 개체에서 기존 MFA 세션을 취소 하도록 요구할 수 있습니다. 사용자 설정을 관리 하려면 다음 단계를 완료 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.
1. 작업을 수행 하려는 사용자를 선택 하 고 **인증 방법**을 선택 합니다. 창의 맨 위에서 사용자에 대 한 다음 옵션 중 하나를 선택 합니다.
   - **암호 다시 설정** 사용자의 암호를 다시 설정 하 고 다음 로그인 시 변경 해야 할 임시 암호를 할당 합니다.
   - **Mfa를 다시 등록 해야** 합니다. 그러면 사용자가 다음 번에 로그인 할 때 새 mfa 인증 방법을 설정 하도록 요청 됩니다.
   
      > [!NOTE]
      > 관리자가 MFA를 다시 등록 해야 하는 경우 사용자의 현재 등록 된 인증 방법이 삭제 되지 않습니다. 사용자가 MFA를 다시 등록 한 후에는 보안 정보를 검토 하 고 더 이상 사용할 수 없는 이전에 등록 된 인증 방법을 삭제 하는 것이 좋습니다.
   
   - **Mfa 세션 철회** 사용자의 기억 된 mfa 세션을 지우고 장치에서 정책에 따라 다음에 필요할 때 mfa를 수행 하도록 요구 합니다.

   ![Azure Portal에서 인증 방법 관리](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>사용자 기존 앱 암호 삭제

필요한 경우 사용자가 만든 모든 앱 암호를 삭제할 수 있습니다. 이러한 앱 암호와 연결된 브라우저가 아닌 앱은 새 앱 암호가 생성될 때까지 작동 중지됩니다. 이 작업을 수행 하려면 *전역 관리자* 권한이 필요 합니다.

사용자의 앱 암호를 삭제 하려면 다음 단계를 완료 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **Azure Active Directory**  >  **사용자**  >  **모든 사용자**를 선택 합니다.
1. **Multi-Factor Authentication**을 선택합니다. 이 메뉴 옵션을 보려면 오른쪽으로 스크롤해야 할 수 있습니다. 다음 스크린샷 예를 선택 하 여 전체 Azure Portal 창 및 메뉴 위치를 확인 합니다. [ ![ Azure AD의 사용자 창에서 Multi-Factor Authentication를 선택 합니다.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 관리하려는 사용자 한 명 또는 여러 명 옆의 상자를 선택합니다. 오른쪽에 빠른 단계 옵션 목록이 표시 됩니다.
1. **사용자 설정 관리**를 선택 하 고 다음 예제와 같이 모든 기존 앱 암호 삭제의 확인란을 선택 하 여 **선택한 사용자가 생성 한 모든 기존 앱 암호를 삭제**합니다. 확인란을 선택 합니다. ![](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. **저장**, **닫기**를 차례로 선택 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 개별 사용자 설정을 구성 하는 방법을 살펴보았습니다. 전체 Azure Multi-Factor Authentication 서비스 설정을 구성 하려면 [azure Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md)을 참조 하세요.

사용자에 게 도움이 필요한 경우 [Azure Multi-Factor Authentication에 대 한 사용자 가이드](../user-help/multi-factor-authentication-end-user-first-time.md)를 참조 하세요.
