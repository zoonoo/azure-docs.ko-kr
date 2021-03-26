---
title: 조건부 액세스 - 결합된 보안 정보 - Azure Active Directory
description: 보안 정보 등록을 위한 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/24/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 711d4bdf2be2ad3158c12e4690a70fb83fe7a846
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559505"
---
# <a name="conditional-access-securing-security-info-registration"></a>조건부 액세스: 보안 정보 등록 보호

사용자가 조건부 액세스 정책의 사용자 작업을 통해 Azure AD Multi-Factor Authentication 및 셀프 서비스 암호 재설정에 등록 하는 시기 및 방법에 대 한 보안을 유지 합니다. 이 기능은 [결합 된 등록](../authentication/concept-registration-mfa-sspr-combined.md)을 사용 하도록 설정한 조직에서 사용할 수 있습니다. 이 기능을 사용 하면 조직에서 조건부 액세스 정책의 모든 응용 프로그램과 같이 등록 프로세스를 처리 하 고 전체 조건부 액세스 기능을 사용 하 여 환경을 보호할 수 있습니다. 

과거에는 등록 환경을 보호 하기 위한 수단으로 신뢰할 수 있는 네트워크 위치 또는 장치 준수를 사용 했을 수 있습니다. 관리자는 Azure AD에 [임시 액세스](../authentication/howto-authentication-temporary-access-pass.md) 를 추가 하 여 장치 또는 위치에서 등록할 수 있는 시간 제한 자격 증명을 사용자에 게 프로 비전 할 수 있습니다. 임시 액세스 전달 자격 증명은 multi-factor authentication에 대 한 조건부 액세스 요구 사항을 만족 합니다.

## <a name="create-a-policy-to-secure-registration"></a>등록을 보호 하는 정책 만들기

다음 정책은 선택한 사용자에 게 적용 되며, 결합 된 등록 환경을 사용 하 여 등록 하려고 시도 합니다. 이 정책을 사용 하려면 사용자가 multi-factor authentication을 수행 하거나 임시 액세스 전달 자격 증명을 사용 해야 합니다.

1. **Azure Portal** 에서 **Azure Active Directory** > **보안** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 이름에 이 정책의 이름을 입력합니다. 예를 들어 **탭을 사용 하 여 보안 정보 등록을 결합** 합니다.
1. **할당** 에서 **사용자 및 그룹** 을 선택하고 이 정책을 적용할 사용자 및 그룹을 선택합니다.
   1. **포함** 에서 **모든 사용자** 를 선택합니다.

      > [!WARNING]
      > 사용자에 대해 [결합된 등록](../authentication/howto-registration-mfa-sspr-combined.md)을 사용하도록 설정해야 합니다.

   1. **제외** 합니다.
      1. **모든 게스트 및 외부 사용자를** 선택 합니다.
      
         > [!NOTE]
         > 게스트 사용자에 대 한 임시 액세스 단계가 작동 하지 않습니다.

      1. **사용자 및 그룹** 을 선택 하 고 조직의 응급 액세스 또는 사용 중단 계정을 선택 합니다. 
1. **클라우드 앱 또는 작업** 에서 **사용자 작업** 을 선택하고 **보안 정보 등록** 을 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 다음을 수행합니다.
   1. **액세스 권한 부여** 를 선택합니다.
   1. **다단계 인증 필요** 를 선택합니다.
   1. **선택** 을 클릭합니다.
1. **정책 사용** 을 **켜기** 로 설정합니다.
1. 그런 다음 **만들기** 를 선택합니다.

이제 관리자는 새 사용자에 게 자격 증명을 전달 하 여 multi-factor authentication이 등록할 요구 사항을 충족할 수 있도록 해야 합니다. 이 작업을 수행 하는 단계는 [AZURE AD 포털에서 임시 액세스 단계 만들기](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass-in-the-azure-ad-portal)섹션에 나와 있습니다.

조직에서는 6b 단계에서 **multi-factor authentication을 요구** 하는 대신 다른 권한 부여 컨트롤을 요구 하도록 선택할 수 있습니다. 여러 컨트롤을 선택할 때 적절 한 라디오 단추를 선택 하 여이 변경 작업을 수행할 때 선택 된 컨트롤 중 **하나 또는 둘 중 하나** 를 수행 하도록 **설정 해야 합니다** .

### <a name="guest-user-registration"></a>게스트 사용자 등록

디렉터리에서 multi-factor authentication을 등록 해야 하는 [게스트 사용자](../external-identities/what-is-b2b.md) 의 경우 다음 가이드를 사용 하 여 [신뢰할 수 있는 네트워크 위치](concept-conditional-access-conditions.md#locations) 외부에서 등록을 차단 하도록 선택할 수 있습니다.

1. **Azure Portal** 에서 **Azure Active Directory** > **보안** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 이름에 이 정책의 이름을 입력합니다. 예를 들어 **신뢰할 수 있는 네트워크의 결합된 보안 정보 등록** 이라고 지정할 수 있습니다.
1. **할당** 에서 **사용자 및 그룹** 을 선택하고 이 정책을 적용할 사용자 및 그룹을 선택합니다.
   1. **포함** 아래에서 **모든 게스트 및 외부 사용자** 를 선택 합니다.
1. **클라우드 앱 또는 작업** 에서 **사용자 작업** 을 선택하고 **보안 정보 등록** 을 선택합니다.
1. **조건** > **위치** 에서 다음을 수행합니다.
   1. **예** 를 구성합니다.
   1. **임의의 위치** 를 포함합니다.
   1. **신뢰할 수 있는 모든 위치** 를 제외합니다.
   1. 위치 블레이드에서 **완료** 를 선택합니다.
   1. 조건 블레이드에서 **완료** 를 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 다음을 수행합니다.
   1. **액세스 차단** 을 선택합니다.
   1. 그런 다음 **선택** 을 클릭합니다.
1. **정책 사용** 을 **켜기** 로 설정합니다.
1. 그런 다음 **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고 전용 모드를 사용하여 영향 확인](howto-conditional-access-insights-reporting.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
