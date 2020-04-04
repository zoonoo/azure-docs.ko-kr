---
title: 사용자 및 장치 관리 Azure MFA - Azure Active 디렉터리
description: 관리자는 사용자가 교정 프로세스를 다시 수행하도록 강제하는 것과 같은 사용자 설정을 변경하는 방법을 사용할 수 있습니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d94fa761980151c420984eb7e8c3254a3509ef
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653491"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 설정 관리

관리자는 다음 사용자 및 디바이스 설정을 관리할 수 있습니다.

* 사용자가 연락 방법을 다시 제공하도록 요청
* 앱 암호 삭제
* 모든 신뢰할 수 있는 디바이스에서 MFA 요청

## <a name="manage-authentication-methods"></a>인증 방법 관리

관리자가 인증 관리자 역할을 할당한 경우 사용자가 암호를 재설정하거나, MFA에 다시 등록하거나, 사용자 개체에서 기존 MFA 세션을 해지하도록 요구할 수 있습니다.

![Azure 포털에서 인증 방법 관리](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 Azure **Active Directory** > **모든** > **사용자를**선택합니다.
1. 에서 작업을 수행할 사용자를 선택하고 인증 **방법을**선택합니다.
   - **암호 재설정은** 사용자의 암호를 재설정하고 다음 로그인시 변경해야 하는 임시 암호를 할당합니다.
   - **다시 등록해야 MFA는** 사용자가 다음에 서명할 때 새 MFA 인증 방법을 설정하도록 요청됩니다.
   - **취소 MFA 세션은** 사용자의 기억된 MFA 세션을 지우고 다음에 장치의 정책에 필요할 때 MFA를 수행하도록 요구합니다.

## <a name="delete-users-existing-app-passwords"></a>사용자 기존 앱 암호 삭제

이 설정은 사용자가 만든 모든 앱 암호를 삭제합니다. 이러한 앱 암호와 연결된 브라우저가 아닌 앱은 새 앱 암호가 생성될 때까지 작동 중지됩니다. 이 작업을 수행하려면 전역 관리자 권한이 필요합니다.

### <a name="how-to-delete-users-existing-app-passwords"></a>사용자 기존 앱 암호를 삭제하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 Azure **Active Directory** > **모든** > **사용자를**선택합니다.
3. 오른쪽의 도구 모음에서 **Multi-Factor Authentication**을 선택합니다. 다단계 인증 페이지가 열립니다.
4. 관리하려는 사용자 한 명 또는 여러 명 옆의 상자를 선택합니다. 빠른 단계 옵션 목록이 오른쪽에 나타납니다.
5. **Manage user settings**(사용자 설정 관리)를 선택합니다.
6. **선택한 사용자에 의해 생성된 모든 기존 앱 암호 삭제** 상자를 선택합니다.
   ![기존 앱 암호 모두 삭제](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. **저장**을 클릭합니다.
8. 닫기 를 **클릭합니다.**

## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md) 방법에 대해 자세히 알아보기
- 사용자에게 지원이 필요한 경우 [2단계 인증에 대한 사용자 가이드](../user-help/multi-factor-authentication-end-user.md) 안내
