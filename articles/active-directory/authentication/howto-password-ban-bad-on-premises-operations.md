---
title: 온-프레미스 Azure AD 암호 보호 사용
description: 온-프레미스 Active Directory Domain Services 환경에서 Azure AD 암호 보호를 사용하도록 설정하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e11fa1bb9f1d3a3986a19da8ed44229829ec81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741782"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>온-프레미스 Azure Active Directory 암호 보호 사용

사용자가 학교, 스포츠 팀 또는 유명 인사와 같은 일반적인 로컬 단어를 사용하는 암호를 만드는 경우가 많습니다. 이러한 암호는 쉽게 추측할 수 있으며 사전 기반 공격에 취약합니다. 조직에서 강력한 암호를 적용하기 위해 Azure AD(Azure Active Directory) 암호 보호는 전체 및 사용자 지정 금지 암호 목록을 제공합니다. 해당 금지 암호 목록에 일치하는 항목이 있으면 암호 변경 요청이 실패합니다.

온-프레미스 AD DS(Active Directory Domain Services) 환경을 보호하기 위해 온-프레미스 DC와 함께 작동하도록 Azure AD 암호 보호를 설치하고 구성할 수 있습니다. 이 문서에서는 온-프레미스 환경에서 Azure AD 암호 보호를 사용하도록 설정하는 방법을 보여 줍니다.

온-프레미스 환경에서 Azure AD 암호 보호가 작동하는 방법에 대한 자세한 내용은 [Windows Server Active Directory에 Azure AD 암호 보호를 적용하는 방법](concept-password-ban-bad-on-premises.md)을 참고하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 온-프레미스 환경에서 Azure AD 암호 보호를 사용하도록 설정하는 방법을 보여 줍니다. 이 문서를 완료하기 전에 온-프레미스 AD DS 환경에 [Azure AD 암호 보호 프록시 서비스 및 DC 에이전트를 설치하고 등록](howto-password-ban-bad-on-premises-deploy.md)합니다.

## <a name="enable-on-premises-password-protection"></a>온-프레미스 암호 보호 사용

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **Azure Active Directory** >  **보안** > **인증 방법** > **암호 보호** 로 이동합니다.
1. **Windows Server Active Directory에서 암호 보호 사용** 옵션을 *예* 로 설정합니다.

    ‘아니요’로 설정하면 배포된 모든 Azure AD 암호 보호 DC 에이전트는 모든 암호가 있는 그대로 허용되는 정지 모드로 전환됩니다. 유효성 검사 활동이 수행되지 않으며 감사 이벤트가 생성되지 않습니다.

1. 처음에는 **모드** 를 ‘감사’로 설정하는 것이 좋습니다. 기능 및 조직의 사용자에게 미치는 영향에 익숙해진 후에는 **모드** 를 ‘강제 적용’으로 전환할 수 있습니다. 자세한 내용은 [작업 모드](#modes-of-operation)에 대한 다음 섹션을 참고하세요.
1. 준비되면 **저장** 을 선택합니다.

    [![Azure Portal 인증 방법에서 온-프레미스 암호 보호를 사용하도록 설정](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>작동 모드

온-프레미스 Azure AD 암호 보호를 사용하도록 설정하면 ‘감사’ 모드 또는 ‘강제 적용’ 모드를 사용할 수 있습니다.  초기 배포 및 테스트는 항상 감사 모드로 시작하는 것이 좋습니다. 그런 다음, 이벤트 로그의 항목을 모니터링하여 ‘강제 적용’ 모드가 활성화되면 기존 운영 프로세스가 방해를 받을지 예상해야 합니다.

### <a name="audit-mode"></a>감사 모드

‘감사’ 모드는 “what if” 모드에서 소프트웨어를 실행하기 위한 방법으로 고안되었습니다. 각 Azure AD 암호 보호 DC 에이전트 서비스는 현재 활성 정책에 따라 들어오는 암호를 평가합니다.

현재 정책이 감사 모드로 구성된 경우 “잘못된” 암호로 인해 이벤트 로그 메시지가 발생하지만 처리 및 업데이트됩니다. 이 동작이 감사 모드와 강제 적용 모드의 유일한 차이점입니다. 다른 모든 작업은 동일하게 실행됩니다.

### <a name="enforced-mode"></a>강제 적용 모드

‘강제 적용’ 모드는 최종 구성으로 사용됩니다. 감사 모드에서와 마찬가지로 각 Azure AD 암호 보호 DC 에이전트 서비스는 현재 활성 정책에 따라 들어오는 암호를 평가합니다. 그러나 강제 적용 모드를 사용하도록 설정하면 정책에 따라 안전하지 않은 것으로 간주되는 암호가 거부됩니다.

Azure AD 암호 보호 DC 에이전트가 강제 적용 모드에서 암호를 거부하면 기존 온-프레미스 암호 복잡성을 적용하여 암호가 거부되는 경우에 표시되는 것과 동일한 에러가 최종 사용자에게 표시됩니다. 예를 들어 사용자의 Windows 로그온 또는 암호 변경 화면에 다음과 같은 기존 오류 메시지가 표시될 수 있습니다.

“암호를 업데이트할 수 없습니다. 새 암호로 입력된 값이 도메인의 길이, 복잡성 또는 기록 요구 사항을 충족하지 않습니다.”

이 메시지는 여러 가지 가능한 결과 중 한 가지 예일 뿐입니다. 안전하지 않은 암호를 설정하려고 시도하는 실제 소프트웨어 또는 시나리오에 따라 오류 메시지가 달라질 수 있습니다.

영향을 받는 최종 사용자는 IT 직원의 도움을 받아 새 요구 사항을 이해하고 안전한 암호를 선택해야 합니다.

> [!NOTE]
> Azure AD 암호 보호는 취약한 암호를 거부하는 경우 클라이언트 머신에 표시되는 특정 오류 메시지를 제어하지 않습니다.

## <a name="next-steps"></a>다음 단계

조직에서 금지하는 암호 목록을 사용자 지정하려면 [Azure AD 암호 보호를 위한 사용자 지정 금지 암호 목록 구성](tutorial-configure-custom-password-protection.md)을 참고하세요.

온-프레미스 이벤트를 모니터링하려면 [온-프레미스 Azure AD 암호 보호 모니터링](howto-password-ban-bad-on-premises-monitor.md)을 참고하세요.
