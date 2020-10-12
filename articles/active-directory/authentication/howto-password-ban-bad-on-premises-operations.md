---
title: 온-프레미스 Azure AD 암호 보호 사용
description: 온-프레미스 Active Directory Domain Services 환경에 대해 Azure AD 암호 보호를 사용 하도록 설정 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 4e4797c3e089e0ae9d15cc604e9f93605978b36c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87419770"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>온-프레미스 Azure Active Directory 암호 보호 사용

사용자가 학교, 스포츠 팀 또는 유명 인사와 같은 일반적인 로컬 단어를 사용하는 암호를 만드는 경우가 많습니다. 이러한 암호는 쉽게 추측할 수 있으며 사전 기반 공격에 취약합니다. 조직에서 강력한 암호를 적용 하기 위해 Azure Active Directory (Azure AD) 암호 보호는 전역 및 사용자 지정 금지 된 암호 목록을 제공 합니다. 이러한 금지 된 암호 목록에 일치 하는 항목이 있으면 암호 변경 요청이 실패 합니다.

온-프레미스 Active Directory Domain Services (AD DS) 환경을 보호 하기 위해 Azure AD 암호 보호를 설치 하 고 구성 하 여 온-프레미스 DC를 사용할 수 있습니다. 이 문서에서는 온-프레미스 환경에 대해 Azure AD 암호 보호를 사용 하도록 설정 하는 방법을 보여 줍니다.

온-프레미스 환경에서 Azure AD 암호 보호가 작동 하는 방식에 대 한 자세한 내용은 [Windows Server Active Directory에 AZURE Ad 암호 보호를 적용 하는 방법](concept-password-ban-bad-on-premises.md)을 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 온-프레미스 환경에 대해 Azure AD 암호 보호를 사용 하도록 설정 하는 방법을 보여 줍니다. 이 문서를 완료 하기 전에 온-프레미스 AD DS 환경에 [AZURE AD 암호 보호 프록시 서비스 및 DC 에이전트를 설치 하 고 등록](howto-password-ban-bad-on-premises-deploy.md) 합니다.

## <a name="enable-on-premises-password-protection"></a>온-프레미스 암호 보호 사용

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 **Azure Active Directory**  >  **보안**  >  **인증 방법**  >  **암호 보호**로 이동 합니다.
1. **Windows Server에서 암호 보호 사용 Active Directory** 의 옵션을 *예*로 설정 합니다.

    이 설정이 *아니요*로 설정 된 경우 배포 된 모든 Azure AD 암호 보호 DC 에이전트는 모든 암호가 그대로 적용 되는 정지 모드로 전환 됩니다. 유효성 검사 작업은 수행 되지 않으며 감사 이벤트는 생성 되지 않습니다.

1. 처음에는 **모드** 를 *Audit*로 설정 하는 것이 좋습니다. 기능 및 조직의 사용자에 대 한 영향에 익숙해지면 **모드** 를 *적용*으로 전환할 수 있습니다. 자세한 내용은 [작업 모드](#modes-of-operation)에 대 한 다음 섹션을 참조 하세요.
1. 준비되면 **저장**을 선택합니다.

    [![Azure Portal의 인증 방법에서 온-프레미스 암호 보호를 사용 하도록 설정](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>작동 모드

온-프레미스 Azure AD 암호 보호를 사용 하도록 설정 하면 *감사* 모드 또는 *강제* 모드를 사용할 수 있습니다. 초기 배포 및 테스트는 항상 감사 모드에서 시작 하는 것이 좋습니다. 그런 다음 *적용* 모드를 사용 하도록 설정 하면 이벤트 로그의 항목을 모니터링 하 여 기존 운영 프로세스를 방해 하는지 여부를 예상 합니다.

### <a name="audit-mode"></a>감사 모드

*감사* 모드는 "what if" 모드에서 소프트웨어를 실행 하는 방법으로 사용 됩니다. 각 Azure AD 암호 보호 DC 에이전트 서비스는 현재 활성 정책에 따라 들어오는 암호를 평가 합니다.

현재 정책이 감사 모드로 구성 되어 있으면 "불량" 암호는 이벤트 로그 메시지를 생성 하지만 처리 및 업데이트 됩니다. 이 동작은 감사 모드와 적용 모드의 유일한 차이점입니다. 다른 모든 작업은 동일 하 게 실행 됩니다.

### <a name="enforced-mode"></a>적용 모드

*적용* 모드는 최종 구성으로 제공 됩니다. 감사 모드에서와 같이 각 Azure AD 암호 보호 DC 에이전트 서비스는 현재 활성 정책에 따라 들어오는 암호를 평가 합니다. 그러나 적용 모드를 사용 하도록 설정 하면 정책에 따라 안전 하지 않은 것으로 간주 되는 암호가 거부 됩니다.

Azure AD 암호 보호 DC 에이전트에서 적용 모드로 암호를 거부 하면 기존 온-프레미스 암호 복잡성 적용에 의해 암호가 거부 되었는지와 유사한 오류가 최종 사용자에 게 표시 됩니다. 예를 들어 사용자가 Windows 로그온 또는 암호 변경 화면에서 다음과 같은 일반적인 오류 메시지를 볼 수 있습니다.

*"암호를 업데이트할 수 없습니다. 새 암호에 대해 제공 된 값이 도메인의 길이, 복잡성 또는 기록 요구 사항에 맞지 않습니다. "*

이 메시지는 여러 가지 가능한 결과 중 한 가지 예일 뿐입니다. 특정 오류 메시지는 보안 되지 않은 암호를 설정 하려는 실제 소프트웨어 또는 시나리오에 따라 달라질 수 있습니다.

영향을 받는 최종 사용자는 새 요구 사항을 이해 하 고 보안 암호를 선택 하기 위해 IT 담당자와 협력 해야 할 수 있습니다.

> [!NOTE]
> Azure AD 암호 보호는 취약 한 암호를 거부 하는 경우 클라이언트 컴퓨터에 표시 되는 특정 오류 메시지를 제어 하지 않습니다.

## <a name="next-steps"></a>다음 단계

조직의 금지 된 암호 목록을 사용자 지정 하려면 [AZURE AD 암호 보호 사용자 지정 금지 된 암호 목록 구성](tutorial-configure-custom-password-protection.md)을 참조 하세요.

온-프레미스 이벤트를 모니터링 하려면 [온-프레미스 AZURE AD 암호 보호 모니터링](howto-password-ban-bad-on-premises-monitor.md)을 참조 하세요.
