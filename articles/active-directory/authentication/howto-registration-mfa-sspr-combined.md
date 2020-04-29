---
title: 결합 된 등록 시작 하기-Azure Active Directory
description: 결합 된 Azure AD Multi-Factor Authentication 및 셀프 서비스 암호 재설정 등록을 사용 하도록 설정
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466b063253ee49ab58c2685f359b4bb8a4079532
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639691"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Azure Active Directory에서 결합 된 보안 정보 등록 사용

결합 된 등록 전에 사용자는 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정 (SSPR)에 대 한 인증 방법을 별도로 등록 했습니다. 사용자는 비슷한 메서드가 Multi-Factor Authentication 및 SSPR에 사용 되었지만 두 기능 모두에 등록 해야 했습니다. 이제는 결합 된 등록을 통해 사용자가 한 번 등록 하 고 Multi-Factor Authentication 및 SSPR의 이점을 얻을 수 있습니다.

새 환경을 사용 하도록 설정 하기 전에이 기능의 기능 및 효과를 이해 하기 위해 [결합 된 보안 정보 등록](concept-registration-mfa-sspr-combined.md) 문서를 검토 하세요.

![결합 된 보안 정보 등록 고급 환경](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>결합 등록 사용

결합 된 등록을 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. 사용자 관리자 또는 전역 관리자 권한으로 Azure Portal에 로그인 합니다.
2. **Azure Active Directory** > **사용자**설정 > **사용자 기능 관리 미리 보기 설정**Azure Active Directory로 이동 합니다.
3. **사용자는 미리 보기 기능을 사용 하 여 보안 정보를 등록 하 고 관리할 수 있습니다**. **선택한** 사용자 그룹 또는 **모든** 사용자에 대해 사용을 선택 합니다.

   ![모든 사용자에 대해 결합 된 보안 정보 미리 보기 환경 사용](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> 결합 된 등록을 사용 하도록 설정한 후에는 새 환경을 통해 전화 번호 또는 모바일 앱을 등록 하거나 확인 하는 사용자가 Multi-Factor Authentication 및 SSPR 정책에서 해당 메서드를 사용할 수 있는 경우 Multi-Factor Authentication 및 SSPR에 사용할 수 있습니다. 이 환경을 사용 하지 않도록 설정 하는 경우의 이전 SSPR 등록 페이지로 `https://aka.ms/ssprsetup` 이동 하는 사용자는 multi-factor authentication을 수행 해야 페이지에 액세스할 수 있습니다.

Internet Explorer에서 사이트 간 할당 목록을 구성한 경우 다음 사이트가 동일한 영역에 있어야 합니다.

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>결합 된 등록에 대 한 조건부 액세스 정책

이제 조건부 액세스 정책의 사용자 작업을 통해 사용자가 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정에 등록 하는 시기 및 방법에 대 한 보안을 설정할 수 있습니다. 이 기능은 [결합 된 등록 기능](../authentication/concept-registration-mfa-sspr-combined.md)을 사용 하도록 설정한 조직에서 사용할 수 있습니다. 이 기능은 사용자가 HR 온 보 딩 중에 신뢰할 수 있는 네트워크 위치와 같은 중앙 위치에서 Azure Multi-Factor Authentication에 등록 하 고 SSPR 하는 조직에서 사용 하도록 설정할 수 있습니다.

조건부 액세스에서 신뢰할 수 있는 위치를 만드는 방법에 대 한 자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건 이란?](../conditional-access/location-condition.md#named-locations) 문서를 참조 하세요.

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>신뢰할 수 있는 위치에서 등록을 요구 하는 정책 만들기

다음 정책은 결합 된 등록 환경을 사용 하 여 등록 하려고 하는 모든 선택 된 사용자에 게 적용 되며, 신뢰할 수 있는 네트워크로 표시 된 위치에서 연결 하지 않는 한 액세스를 차단 합니다.

1. **Azure Portal**에서 **Azure Active Directory** > **보안** > **조건부 액세스** 로 이동 합니다.
1. **+ 새 정책** 선택
1. 이 정책의 이름을 입력 합니다 (예: *신뢰할 수 있는 네트워크에 전체 보안 정보 등록*).
1. **할당**에서 **사용자 및 그룹**을 선택합니다. 이 정책을 적용할 사용자 및 그룹을 선택한 다음 **완료**를 선택 합니다.

   > [!WARNING]
   > 사용자는 결합 된 등록을 사용 하도록 설정 해야 합니다.

1. **클라우드 앱 또는 작업**에서 **사용자 작업**을 선택 합니다. **보안 정보 등록**을 선택 하 고 **완료**를 선택 합니다.

    ![보안 정보 등록을 제어 하기 위한 조건부 액세스 정책 만들기](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. **조건** > **위치**에서 다음 옵션을 구성 합니다.
   1. **예** 구성
   1. **모든 위치** 포함
   1. **모든 신뢰할 수 있는 위치** 제외
1. *위치* 창에서 **완료** 를 선택 하 고 *조건* 창에서 **완료** 를 선택 합니다.
1. **액세스 제어** > **권한 부여**에서 **액세스 차단**을 선택 하 고 다음을 **선택** 합니다.
1. **정책 사용** 을 **켜기** 로 설정 합니다.
1. 정책을 마무리 하려면 **만들기** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [결합 된 보안 정보 등록 문제를 해결](howto-registration-mfa-sspr-combined-troubleshoot.md) 하는 방법 또는 [Azure Active Directory 조건부 액세스의 위치 조건 알아보기?](../conditional-access/location-condition.md) 를 참조 하세요.

Azure AD 테 넌 트에서 기능을 사용 하도록 설정 하려면 [셀프 서비스 암호 재설정을 사용](tutorial-enable-sspr.md) 하도록 설정 하 고 [azure Multi-Factor Authentication를 사용](tutorial-enable-azure-mfa.md)하도록 설정 하는 자습서를 참조 하세요.

[테 넌 트에서 결합 된 등록을 사용 하도록 설정](howto-registration-mfa-sspr-combined.md) 하거나 [사용자가 인증 방법을 다시 등록](howto-mfa-userdevicesettings.md#manage-user-authentication-options)하도록 하는 방법에 대해 알아봅니다.

[Azure Multi-Factor Authentication 및 SSPR에 대해 사용 가능한 방법을](concept-authentication-methods.md)검토할 수도 있습니다.
