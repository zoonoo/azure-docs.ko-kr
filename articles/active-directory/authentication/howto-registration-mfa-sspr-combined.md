---
title: Azure AD SSPR 및 Multi-factor Authentication (미리 보기)-Azure Active Directory에 대 한 결합 된 등록 시작
description: Azure AD 다단계 인증을 결합 하는 사용 하도록 설정 하 고 셀프 서비스 암호 재설정 등록 (미리 보기)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc4ff596cdafd348288187b0cd9b32f7b4c2d275
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823383"
---
# <a name="enable-combined-security-information-registration-preview"></a>보안 정보 등록 결합 사용 (미리 보기)

새 환경을 설정 하기 전에 문서를 검토 [보안 정보 등록 (미리 보기)를 결합](concept-registration-mfa-sspr-combined.md) 기능과이 기능의 효과 이해 하도록 합니다.

![향상 된 환경 결합 된 보안 정보 등록](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Azure Multi-factor Authentication 및 Azure Active Directory (Azure AD) 셀프 서비스 암호 재설정에 대 한 통합된 보안 정보 등록에는 Azure AD의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

## <a name="enable-combined-registration"></a>결합 등록 사용

결합 된 등록을 사용 하도록 설정 하려면 다음이 단계를 완료 합니다.

1. 사용자 관리자 또는 전역 관리자로 Azure portal에 로그인 합니다.
2. 로 이동 **Azure Active Directory** > **사용자 설정** > **액세스 패널 미리 보기 기능에 대 한 설정을 관리**합니다.
3. 아래 **사용자가 사용할 수 미리 보기 기능 등록 하 고 보안 정보 관리에 대 한-새로 고침**에 대해 사용 하도록 설정할를 **선택한** 사용자 또는 그룹 **모든** 사용자입니다.

   ![모든 사용자에 대해 결합 된 보안 정보 미리 보기 환경을 사용 하도록 설정](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> 2019 년 3 월부터, 전화 통화 옵션을 Multi-factor Authentication 및 Azure AD 체험/평가판 테 넌 트에 사용자가 SSPR 사용할 수 없습니다. SMS 메시지는이 변경의 영향을 받지 않습니다. 전화 통화 옵션을 사용할 수 있습니다 사용자에 게 유료 Azure AD 테 넌 트입니다.

> [!NOTE]
> 결합 된 등록, 등록 또는 자신의 전화 번호를 확인 하는 사용자를 사용 하거나 새 환경을 통해 모바일 앱 수 사용 하 여 해당 Multi-factor Authentication 및 SSPR에 대 한 이러한 메서드는 Multi-factor Authentication 및 SSPR에 설정 된 경우 정책입니다. 이전 SSPR 등록으로 이동 하는 사용자 페이지에서 다음에이 환경을 비활성화 하면 `https:/aka.ms/ssprsetup` 페이지에 액세스 하기 전에 multi-factor authentication을 수행 해야 합니다.

Internet Explorer에서 사이트 할당 목록 영역을 구성한 경우 다음 사이트를 동일한 영역에 수 해야 합니다.

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>결합 된 등록에 대 한 조건부 액세스 정책

시기와 방식을 사용자가 Azure Multi-factor Authentication 및 셀프 서비스 암호 재설정 등록 조건부 액세스 정책에서 사용자 작업을 사용 하 여 수 있게 되었습니다. 보안이 유지 됩니다. 이 미리 보기 기능을 사용 하도록 설정 하는 조직에 사용할 수는 [미리 보기 등록 결합](../authentication/concept-registration-mfa-sspr-combined.md)합니다. HR 온 보 딩 하는 동안 Azure Multi-factor Authentication 및 신뢰할 수 있는 네트워크 위치와 같은 중앙 위치에서 SSPR에 등록 하려면 사용자가 원하는 곳의 조직에서이 기능을 사용할 수 있습니다. 조건부 액세스의 신뢰할 수 있는 위치를 만드는 방법에 대 한 자세한 내용은 문서를 참조 하세요. [Azure Active Directory 조건부 액세스의 위치 조건 이란?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>신뢰할 수 있는 위치에서 등록을 요구 하도록 정책 만들기

다음 정책을 신뢰할 수 있는 네트워크로 표시 된 위치에서 연결 하는 경우가 아니면 결합 된 등록 환경 및 요소 액세스를 사용 하 여 등록을 시도 하면 모든 선택한 사용자에 게 적용 됩니다.

![보안 정보 등록을 제어 하는 CA 정책 만들기](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. 에 **Azure portal**, 이동할 **Azure Active Directory** > **조건부 액세스**
1. **새 정책**을 선택합니다.
1. 이름에이 정책의 이름을 입력 합니다. 예를 들어 **신뢰할 수 있는 네트워크에 보안 정보 등록 결합**
1. 아래 **할당**, 클릭 **사용자 및 그룹**, 사용자 및이 정책을 적용할 그룹 선택

   > [!WARNING]
   > 사용자에 대해 사용 하도록 설정 해야 합니다 [등록 미리 보기를 결합](../authentication/howto-registration-mfa-sspr-combined.md)합니다.

1. 아래 **클라우드 앱 또는 작업**를 선택 **사용자 동작**, 확인 **보안 정보 (미리 보기)를 등록**
1. 아래 **조건을** > **위치**
   1. 구성 **예**
   1. 포함 **모든 위치**
   1. 제외 **신뢰할 수 있는 모든 위치**
   1. 클릭 **수행** 위치 블레이드에서
   1. 클릭 **수행** 조건 블레이드에서
1. 아래 **액세스 제어** > **권한 부여**
   1. 클릭 **액세스 차단**
   1. 클릭 **선택**
1. 설정할 **정책을 사용 하도록 설정** 에 **에서**
1. 클릭 **만들기**

## <a name="next-steps"></a>다음 단계

[Multi-factor Authentication 및 SSPR에 대 한 사용 가능한 메서드](concept-authentication-methods.md)

[셀프 서비스 암호 재설정 구성](howto-sspr-deployment.md)

[Azure Multi-factor Authentication 구성](howto-mfa-getstarted.md)

[결합 된 보안 정보 등록 문제 해결](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Azure Active Directory 조건부 액세스의 위치 조건 이란?](../conditional-access/location-condition.md)