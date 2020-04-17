---
title: 통합 등록 시작 - Azure Active Directory
description: 결합된 Azure AD 다단계 인증 및 셀프 서비스 암호 재설정 등록 사용
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d9544b1f4dd5ecbf66493f26c373c5502dce68a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451087"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Azure Active Directory에서 통합된 보안 정보 등록 을 활성화합니다.

결합된 등록 전에 사용자는 Azure 다단계 인증 및 셀프 서비스 암호 재설정(SSPR)에 대한 인증 방법을 별도로 등록했습니다. 사람들은 다단계 인증 및 SSPR에 유사한 방법이 사용되었지만 두 기능에 대해 등록해야 한다는 사실에 혼란스러워했습니다. 이제 결합 된 등록으로 사용자는 한 번 등록하고 다단계 인증 및 SSPR의 이점을 얻을 수 있습니다.

새 환경을 활성화하기 전에 이 기능의 기능과 효과를 이해하려면 통합 [보안 정보 등록](concept-registration-mfa-sspr-combined.md) 문서를 검토하십시오.

![통합 된 보안 정보 등록 향상 된 경험](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>결합 등록 사용

다음 단계를 완료하여 결합된 등록을 활성화합니다.

1. Azure 포털에 사용자 관리자 또는 전역 관리자로 로그인합니다.
2. Azure **Active Directory** > **사용자 설정사용자** > **기능 미리 보기 설정 관리로**이동합니다.
3. 사용자 에서 **보안 정보를 등록 하고 관리 하기 위한 미리 보기 기능을 사용할 수**있습니다., **선택한** 사용자 그룹 또는 **모든** 사용자에 대 한 사용 하도록 선택 합니다.

   ![모든 사용자에 대한 통합 보안 정보 미리 보기 환경 사용](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> 결합 등록을 활성화한 후 새 환경을 통해 전화 번호 또는 모바일 앱을 등록하거나 확인하는 사용자는 다단계 인증 및 SSPR 정책에서 이러한 메서드를 사용하도록 설정된 경우 다단계 인증 및 SSPR에 사용할 수 있습니다. 그런 다음 이 환경을 사용하지 않도록 설정하면 페이지에 액세스하기 `https://aka.ms/ssprsetup` 전에 이전 SSPR 등록 페이지로 이동하는 사용자가 다단계 인증을 수행해야 합니다.

Internet Explorer에서 사이트 대 영역 할당 목록을 구성한 경우 다음 사이트가 동일한 영역에 있어야 합니다.

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>결합 된 등록에 대 한 조건부 액세스 정책

이제 조건부 액세스 정책의 사용자 작업을 통해 사용자가 Azure 다단계 인증 및 셀프 서비스 암호 재설정에 등록하는 시기와 방법을 확보할 수 있습니다. 이 기능은 [통합 등록 기능을](../authentication/concept-registration-mfa-sspr-combined.md)사용하도록 설정한 조직에서 사용할 수 있습니다. 이 기능은 사용자가 HR 온보딩 중에 신뢰할 수 있는 네트워크 위치와 같은 중앙 위치에서 Azure 다단계 인증 및 SSPR에 등록하도록 하려는 조직에서 활성화될 수 있습니다. 조건부 액세스에서 신뢰할 수 있는 위치를 만드는 것에 대한 자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건은 무엇입니까?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>신뢰할 수 있는 위치에서 등록을 요구하는 정책 만들기

다음 정책은 결합된 등록 환경을 사용하여 등록을 시도하는 모든 선택된 사용자에게 적용되며 신뢰할 수 있는 네트워크로 표시된 위치에서 연결하지 않는 한 액세스를 차단합니다.

![보안 정보 등록을 제어하는 CA 정책 만들기](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Azure **포털에서**Azure **Active 디렉터리** > **보안** > **조건부 액세스로** 검색
1. **새 정책** 선택
1. 이름으로 이 정책의 이름을 입력합니다. 예를 들어 **신뢰할 수 있는 네트워크에서 통합된 보안 정보 등록**
1. **할당에서**사용자 **및 그룹을**클릭하고 이 정책을 적용할 사용자 및 그룹을 선택합니다.

   > [!WARNING]
   > [사용자는 결합된 등록을](../authentication/howto-registration-mfa-sspr-combined.md)위해 활성화되어야 합니다.

1. **클라우드 앱 또는 작업**에서 사용자 작업을 선택하고 보안 정보 **등록(미리 보기)을** **선택합니다.**
1. **조건** > **위치** 하에서
   1. **예** 구성
   1. **모든 위치** 포함
   1. **신뢰할 수 있는 모든 위치** 제외
   1. 위치 블레이드에서 **완료를** 클릭합니다.
   1. 조건 블레이드에서 **완료를** 클릭합니다.
1. **액세스 제어** > **권한 부여**
   1. **액세스 차단을 클릭합니다.**
   1. 그런 다음 **선택을 클릭합니다.**
1. **정책 사용** 설정 **켜짐**
1. 그런 다음 **만들기를 클릭합니다.**

## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [결합된 보안 정보 등록 문제를 해결하는](howto-registration-mfa-sspr-combined-troubleshoot.md) 방법을 참조하거나 Azure Active [Directory 조건부 액세스의 위치 조건은 무엇입니까?](../conditional-access/location-condition.md)

Azure AD 테넌트의 기능을 활성화하려면 셀프 [서비스 암호 재설정을 사용하도록 설정하고](tutorial-enable-sspr.md) Azure 다단계 인증을 사용하도록 설정하는 자습서를 [참조하세요.](tutorial-enable-azure-mfa.md)

[테넌트에서 결합된 등록을 사용하도록 설정하거나](howto-registration-mfa-sspr-combined.md) [사용자가 인증 방법을 다시 등록하도록 하는](howto-mfa-userdevicesettings.md#manage-user-authentication-options)방법을 알아봅니다.

[Azure 다단계 인증 및 SSPR에 사용할 수](concept-authentication-methods.md)있는 메서드를 검토할 수도 있습니다.
