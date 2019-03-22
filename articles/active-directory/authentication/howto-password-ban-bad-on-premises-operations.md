---
title: Azure AD 암호 보호 작업 및 보고-Azure Active Directory
description: Azure AD 암호 보호 배포 후 작업 및 보고
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca85007bb016cc98d1be61ce08865945e699ad4a
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312430"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Azure AD 암호 보호 운영 절차

[Azure AD 암호 보호가 온-프레미스에 설치](howto-password-ban-bad-on-premises-deploy.md)되면 Azure Portal에서 구성해야 하는 몇 가지 항목이 있습니다.

## <a name="configure-the-custom-banned-password-list"></a>사용자 지정 금지 암호 목록 구성

조직의 금지된 암호 목록을 사용자 지정하는 단계에 대한 [사용자 지정 금지 암호 목록 구성](howto-password-ban-bad-configure.md) 문서의 지침을 따릅니다.

## <a name="enable-password-protection"></a>암호 보호 사용

1. 에 로그인 하는 [Azure portal](https://portal.azure.com) 로 이동한 **Azure Active Directory**, **인증 방법**, 한 다음 **암호 보호**합니다.
1. **Windows Server Active Directory에 대한 암호 보호를 사용하도록 설정**을 **예**로 설정합니다.
1. [배포 가이드](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy)에 설명된 대로, 처음에는 **모드**를 **감사**로 설정하는 것이 좋습니다.
   * 기능에 익숙해진 후 **모드**를 **강제 적용**으로 전환하면 됩니다.
1. 페이지 맨 아래에 있는 **저장**

![Azure Portal에서 Azure AD 암호 보호 구성 요소를 사용하도록 설정](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>감사 모드

감사 모드는 "what if" 모드에서 소프트웨어를 실행하기 위한 방법으로 고안되었습니다. 각 DC 에이전트 서비스는 현재 활성 정책에 따라 들어오는 암호를 평가합니다. 현재 정책이 감사 모드에 포함되도록 구성된 경우 "잘못된" 암호는 이벤트 로그 메시지로 이어지지만 허용됩니다. 이것이 감사 모드와 강제 적용 모드의 유일한 차이점이며, 나머지 작업은 동일하게 실행됩니다.

> [!NOTE]
> 초기 배포 및 테스트는 항상 감사 모드로 시작하는 것이 좋습니다. 그 후 이벤트 로그의 이벤트를 모니터링하면서 강제 적용 모드를 사용하면 기존 운영 프로세스에 방해가 되는지 예상해 볼 수 있습니다.

## <a name="enforce-mode"></a>강제 적용 모드

강제 적용 모드는 최종 구성으로 고안되었습니다. 위에서 설명한 감사 모드와 마찬가지로, 각 DC 에이전트 서비스는 현재 활성 정책에 따라 들어오는 암호를 평가합니다. 강제 적용 모드를 사용하더라도 암호 정책에 따라 안전하지 않은 것으로 판단되는 암호는 거부됩니다.

Azure AD 암호 보호 DC 에이전트가 적용 모드에서 암호를 거부하면 기존 온-프레미스 암호 복잡성을 적용하여 암호가 거부되는 경우에 표시되는 것과 동일한 시각적 영향을 최종 사용자에게 보여 줍니다. 예를 들어 사용자의 Windows 로그온/암호 변경 화면에 다음과 같은 기존 오류 메시지가 표시될 수 있습니다.

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

이 메시지는 여러 가지 가능한 결과 중 한 가지 예일 뿐입니다. 안전하지 않은 암호를 설정하려고 시도하는 실제 소프트웨어 또는 시나리오에 따라 오류 메시지가 달라질 수 있습니다.

영향을 받는 최종 사용자는 IT 직원의 도움을 받아 새 요구 사항을 이해하면 안전한 암호를 선택해야 합니다.

## <a name="enable-mode"></a>모드 사용

일반적으로 이 설정은 기본 설정된(예) 상태로 두어야 합니다. 이 설정을 사용 안 함(아니요)으로 구성하면 배포된 모든 Azure AD 암호 보호 DC 에이전트가 모든 암호를 그대로 허용하는 정지 모드로 전환되어 모든 유효성 검사 작업이 실행하지 않습니다(예: 감사 이벤트조차도 내보내지 않음).

## <a name="next-steps"></a>다음 단계

[Azure AD 암호 보호 모니터링](howto-password-ban-bad-on-premises-monitor.md)
