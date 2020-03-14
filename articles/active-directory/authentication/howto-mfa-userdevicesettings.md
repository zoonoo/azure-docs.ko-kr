---
title: 사용자 및 장치 관리 Azure MFA-Azure Active Directory
description: 사용자가 증명 프로세스를 다시 수행 하도록 강제 하는 등의 방법으로 관리자가 사용자 설정을 변경할 수 있습니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07845bb5b742b1bcfbb22d260457e9a8e16edab6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263713"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 설정 관리

관리자는 다음 사용자 및 디바이스 설정을 관리할 수 있습니다.

* 사용자가 연락 방법을 다시 제공하도록 요청
* 앱 암호 삭제
* 모든 신뢰할 수 있는 디바이스에서 MFA 요청

## <a name="manage-authentication-methods"></a>인증 방법 관리

관리자가 인증 관리자 역할을 할당 한 경우 사용자가 암호를 재설정 하 고 MFA를 다시 등록 하거나 사용자 개체에서 기존 MFA 세션을 취소 하도록 요구할 수 있습니다.

![Azure Portal에서 인증 방법 관리](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.
1. 작업을 수행 하려는 사용자를 선택 하 고 **인증 방법**을 선택 합니다.
   - **암호 재설정** 은 사용자의 암호를 다시 설정 하 고 다음에 로그인 할 때 변경 해야 하는 임시 암호를 할당 합니다.
   - **Mfa를 다시 등록 해야** 합니다. 그러면 사용자가 다음 번에 로그인 할 때 새 mfa 인증 방법을 설정 하도록 요청 됩니다.
   - **Mfa 세션 철회** 사용자의 기억 된 mfa 세션을 지우고 장치에서 정책에 따라 다음에 필요할 때 mfa를 수행 하도록 요구 합니다.

## <a name="delete-users-existing-app-passwords"></a>사용자 기존 앱 암호 삭제

이 설정은 사용자가 만든 모든 앱 암호를 삭제합니다. 이러한 앱 암호와 연결된 브라우저가 아닌 앱은 새 앱 암호가 생성될 때까지 작동 중지됩니다. 이 작업을 수행 하려면 전역 관리자 권한이 필요 합니다.

### <a name="how-to-delete-users-existing-app-passwords"></a>사용자 기존 앱 암호를 삭제하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.
3. 오른쪽의 도구 모음에서 **Multi-Factor Authentication**을 선택합니다. 다단계 인증 페이지가 열립니다.
4. 관리하려는 사용자 한 명 또는 여러 명 옆의 상자를 선택합니다. 오른쪽에 빠른 단계 옵션 목록이 표시 됩니다.
5. **Manage user settings**(사용자 설정 관리)를 선택합니다.
6. **선택한 사용자에 의해 생성된 모든 기존 앱 암호 삭제** 상자를 선택합니다.
   기존 앱 암호를 모두 삭제 ![](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. **저장**을 클릭합니다.
8. **닫기**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md) 방법에 대해 자세히 알아보기
- 사용자에게 지원이 필요한 경우 [2단계 인증에 대한 사용자 가이드](../user-help/multi-factor-authentication-end-user.md) 안내
