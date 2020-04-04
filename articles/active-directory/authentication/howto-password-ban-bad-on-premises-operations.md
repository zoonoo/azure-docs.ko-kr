---
title: 온-프레미스 Azure AD 암호 보호 사용
description: 온-프레미스 Active Directory 도메인 서비스 환경에 대한 Azure AD 암호 보호를 사용하도록 설정하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ee0f3d89d48b23db48e3bf4b78203b09fbcbdbd
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652629"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>온-프레미스 Azure Active 디렉터리 암호 보호 사용

사용자는 종종 학교, 스포츠 팀 또는 유명한 사람과 같은 일반적인 로컬 단어를 사용하는 암호를 만듭니다. 이러한 암호는 추측하기 쉽고 사전 기반 공격에 취약합니다. 조직에서 강력한 암호를 적용하기 위해 Azure Active Directory(Azure AD) 암호 보호는 전역 및 사용자 지정 금지 암호 목록을 제공합니다. 이러한 차단된 암호 목록에 일치하는 암호 변경 요청이 있으면 암호 변경 요청이 실패합니다.

온-프레미스 Active Directory 도메인 서비스(AD DS) 환경을 보호하려면 Azure AD 암호 보호를 설치하고 구성하여 온프레미스 DC에서 작업할 수 있습니다. 이 문서에서는 온-프레미스 환경에 대해 Azure AD 암호 보호를 사용하도록 설정하는 방법을 보여 줍니다.

온-프레미스 환경에서 Azure AD 암호 보호가 작동하는 방식에 대한 자세한 내용은 [Windows 서버 Active Directory에 대한 Azure AD 암호 보호를 적용하는 방법을](concept-password-ban-bad-on-premises.md)참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 온-프레미스 환경에 대해 Azure AD 암호 보호를 사용하도록 설정하는 방법을 보여 줍니다. 이 문서를 완료하기 전에 온-프레미스 AD DS 환경에서 [Azure AD 암호 보호 프록시 서비스 및 DC 에이전트를 설치하고 등록합니다.](howto-password-ban-bad-on-premises-deploy.md)

## <a name="enable-on-premises-password-protection"></a>온-프레미스 암호 보호 사용

1. [Azure 포털에](https://portal.azure.com) 로그인하고 **Azure Active Directory** > **보안** > **인증 방법** > **암호 보호를**찾아봅색입니다.
1. Windows 서버 **활성 디렉터리에서 암호 보호 를** *예로*설정하는 옵션.

    이 설정이 *아니요로*설정되면 배포된 모든 Azure AD 암호 보호 DC 에이전트는 모든 암호가 있는 것으로 허용되는 정지 모드로 전환됩니다. 유효성 검사 활동이 수행되지 않으며 감사 이벤트가 생성되지 않습니다.

1. 처음에 *감사를*위해 **모드를** 설정하는 것이 좋습니다. 이 기능과 조직의 사용자에게 미치는 영향에 익숙해지면 **모드를** *적용으로*전환할 수 있습니다. 자세한 내용은 [작동 모드에](#modes-of-operation)대한 다음 섹션을 참조하십시오.
1. 준비되면 **저장**을 선택합니다.

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>작동 모드

온-프레미스 Azure AD 암호 보호를 사용하도록 설정하면 *감사* 모드 또는 *적용* 모드를 사용할 수 있습니다. 초기 배포 및 테스트는 항상 감사 모드에서 시작하는 것이 좋습니다. 그런 다음 이벤트 로그의 항목을 모니터링하여 *적용* 모드가 활성화되면 기존 운영 프로세스가 방해받을지 여부를 예측해야 합니다.

### <a name="audit-mode"></a>감사 모드

*감사* 모드는 "what if" 모드에서 소프트웨어를 실행하는 방법을 위한 것입니다. 각 Azure AD 암호 보호 DC 에이전트 서비스는 현재 활성 정책에 따라 들어오는 암호를 평가합니다.

현재 정책이 감사 모드로 구성된 경우 "잘못된" 암호는 이벤트 로그 메시지를 생성하지만 처리되고 업데이트됩니다. 이 동작은 감사 모드와 적용 모드 간의 유일한 차이점입니다. 다른 모든 작업은 동일하게 실행됩니다.

### <a name="enforced-mode"></a>적용 모드

적용 모드는 최종 구성으로 *의도됩니다.* 감사 모드에서와 마찬가지로 각 Azure AD 암호 보호 DC 에이전트 서비스는 현재 활성 정책에 따라 들어오는 암호를 평가합니다. 하지만 적용 모드가 활성화되면 정책에 따라 안전하지 않은 것으로 간주되는 암호가 거부됩니다.

Azure AD 암호 보호 DC 에이전트가 적용 모드에서 암호를 거부하면 최종 사용자는 기존 온-프레미스 암호 복잡성 적용에 의해 암호가 거부되었는지 확인하는 것과 유사한 오류를 보게 됩니다. 예를 들어 사용자는 Windows 로그온 또는 변경 암호 화면에서 다음과 같은 기존 오류 메시지를 볼 수 있습니다.

*"암호를 업데이트할 수 없습니다. 새 암호에 제공된 값이 도메인의 길이, 복잡성 또는 기록 요구 사항을 충족하지 않습니다."*

이 메시지는 여러 가지 가능한 결과 중 한 가지 예일 뿐입니다. 특정 오류 메시지는 안전하지 않은 암호를 설정하려는 실제 소프트웨어 또는 시나리오에 따라 달라질 수 있습니다.

영향을 받는 최종 사용자는 IT 직원과 협력하여 새로운 요구 사항을 이해하고 보안 암호를 선택해야 할 수 있습니다.

> [!NOTE]
> Azure AD 암호 보호는 약한 암호가 거부될 때 클라이언트 컴퓨터에서 표시되는 특정 오류 메시지를 제어할 수 없습니다.

## <a name="next-steps"></a>다음 단계

조직에 대해 금지된 암호 목록을 사용자 지정하려면 [Azure AD 암호 보호 사용자 지정 금지 암호 목록 구성](tutorial-configure-custom-password-protection.md)을 참조하십시오.

온-프레미 이벤트를 모니터링하려면 [prem 온프레마 Azure AD 암호 보호 모니터링을](howto-password-ban-bad-on-premises-monitor.md)참조하십시오.
