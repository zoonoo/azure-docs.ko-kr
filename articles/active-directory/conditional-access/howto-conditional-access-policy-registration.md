---
title: 조건부 액세스 - 결합된 보안 정보 - Azure Active Directory
description: 보안 정보 등록을 위한 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8081bb8145a6654c168fb2d664e1666b32dc18
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457912"
---
# <a name="conditional-access-securing-security-info-registration"></a>조건부 액세스: 보안 정보 등록 보안

이제 조건부 액세스 정책의 사용자 작업을 통해 사용자가 Azure 다단계 인증 및 셀프 서비스 암호 재설정에 등록하는 시기와 방법을 확보할 수 있습니다. 이 미리 보기 기능은 [통합 등록 미리 보기를](../authentication/concept-registration-mfa-sspr-combined.md)사용하도록 설정한 조직에서 사용할 수 있습니다. 이 기능은 신뢰할 수 있는 네트워크 위치와 같은 조건을 사용하여 Azure 다단계 인증 및 셀프 서비스 암호 재설정(SSPR)에 대한 등록 액세스를 제한하려는 조직에서 활성화될 수 있습니다. 사용 가능한 조건에 대한 자세한 내용은 [조건부 액세스: 조건](concept-conditional-access-conditions.md)문서를 참조하십시오.

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>신뢰할 수 있는 위치에서 등록을 요구하는 정책 만들기

다음 정책은 결합된 등록 환경을 사용하여 등록을 시도하는 모든 선택된 사용자에게 적용되며 신뢰할 수 있는 네트워크로 표시된 위치에서 연결하지 않는 한 액세스를 차단합니다.

1. Azure **포털에서**Azure **Active 디렉터리** > **보안** > **조건부 액세스를**찾아봅
1. **새 정책**을 선택합니다.
1. 이름으로 이 정책의 이름을 입력합니다. 예를 들어 **신뢰할 수 있는 네트워크에서 보안 정보 등록을 결합했습니다.**
1. **할당에서**사용자 **및 그룹을**선택하고 이 정책을 적용할 사용자 및 그룹을 선택합니다.

   > [!WARNING]
   > [결합된 등록에](../authentication/howto-registration-mfa-sspr-combined.md)대해 사용자를 사용하도록 설정해야 합니다.

1. **클라우드 앱 또는 작업에서**사용자 **작업을**선택하고 보안 **정보 등록을**선택합니다.
1. **조건** > **위치.**
   1. **구성 예**.
   1. **모든 위치를**포함합니다.
   1. **신뢰할 수 있는 모든 위치를 제외합니다.**
   1. 위치 블레이드에서 **완료를** 선택합니다.
   1. 조건 블레이드에서 **완료를** 선택합니다.
1. **조건 클라이언트** > **앱(미리 보기)에서** **구성을** **예로**설정하고 **완료를**선택합니다.
1. **액세스 제어 권한** > **부여에서**.
   1. **액세스 차단**을 선택합니다.
   1. 그런 다음 **선택**을 클릭합니다.
1. **정책 사용**을 **켜기**로 설정합니다.
1. 그런 다음 **저장을**선택합니다.

이 정책의 6단계에서 조직은 선택할 수 있습니다. 위의 정책은 신뢰할 수 있는 네트워크 위치에서 등록해야 합니다. 조직은 **위치**대신 사용 가능한 조건을 활용할 수 있습니다. 이 정책은 블록 정책이므로 포함된 모든 것이 차단되고 포함과 일치하지 않는 모든 것이 허용됩니다. 

일부는 위의 6 단계에서 위치 대신 장치 상태를 사용하도록 선택할 수 있습니다.

6. **조건** > 에서**장치 상태(미리 보기)**.
   1. **구성 예**.
   1. **모든 장치 상태를**포함합니다.
   1. **장치 하이브리드 Azure AD 조인** 및/또는 장치 **비준수로 표시** 제외
   1. 위치 블레이드에서 **완료를** 선택합니다.
   1. 조건 블레이드에서 **완료를** 선택합니다.

> [!WARNING]
> 정책에서 장치 상태를 조건으로 사용하는 경우 디렉터리에서 게스트 사용자에게 영향을 미칠 수 있습니다. [보고서 전용 모드는](concept-conditional-access-report-only.md) 정책 결정의 영향을 결정하는 데 도움이 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-report-only.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
