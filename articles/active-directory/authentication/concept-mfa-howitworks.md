---
title: Azure 다단계 인증 개요
description: Azure 다단계 인증을 통해 간단한 로그인 프로세스에 대한 사용자 요구를 충족하면서 데이터 및 응용 프로그램에 대한 액세스를 보호하는 방법을 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c50232abd12c8c0390409bd7bf72833b4f153e02
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667354"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>작동 방법: Azure Multi-Factor Authentication

다단계 인증은 로그인 프로세스 중에 코드를 휴대폰에 입력하거나 지문 검사를 제공하는 것과 같은 추가 형식의 식별을 요구하는 프로세스입니다.

암호만 사용하여 사용자를 인증하면 공격에 안전하지 않은 벡터가 유지됩니다. 암호가 약하거나 다른 곳에 노출된 경우 실제로 사용자 이름과 암호를 사용하는 사용자 로그인일까요, 아니면 공격자일까요? 두 번째 인증 형식이 필요한 경우 이 추가 요소는 공격자가 쉽게 얻거나 복제할 수 있는 것이 아니므로 보안이 향상됩니다.

![다양한 형식의 다단계 인증에 대한 개념적 이미지](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication은 다음 인증 방법 중 둘 이상을 요구하여 작동합니다.

* 사용자가 알고 있는 정보(일반적으로 암호)
* 사용자의 소유물 정보(예: 휴대폰 또는 하드웨어 키와 같이 쉽게 복제되지 않는 신뢰할 수 있는 디바이스)
* 사용자의 생체 인식 정보(예: 지문 또는 얼굴 스캔)

사용자는 셀프 서비스 암호 재설정 및 Azure Multi-Factor Authentication을 모두 한 단계로 등록하여 온보딩 환경을 간소화할 수 있습니다. 관리자는 사용할 수 있는 보조 인증 형식을 정의할 수 있습니다. 사용자가 셀프 서비스 암호 재설정을 수행하여 프로세스를 더 안전하게 보호하는 경우에도 Azure Multi-Factor Authentication이 필요할 수 있습니다.

![로그인 화면에서 사용하는 인증 방법](media/concept-authentication-methods/overview-login.png)

Azure 다단계 인증을 사용하면 사용자의 단순성 유지를 유지하면서 데이터 및 응용 프로그램에 대한 액세스를 보호할 수 있습니다. 두 번째 형식의 인증을 요구하여 추가 보안을 제공하고 사용하기 쉬운 다양한 [인증 방법](concept-authentication-methods.md)을 통해 강력한 인증을 제공합니다. 관리자가 결정한 구성에 따라 사용자에게 MFA 챌린지가 표시될 수도 있고 그렇지 않을 수도 있습니다.

응용 프로그램 또는 서비스는 Azure 다단계 인증을 사용하기 위해 변경할 필요가 없습니다. 확인 프롬프트는 필요한 경우 MFA 챌린지를 자동으로 요청하고 처리하는 Azure AD 로그인 이벤트의 일부입니다.

## <a name="available-verification-methods"></a>사용 가능한 검증 방법

사용자가 응용 프로그램 또는 서비스에 로그인하고 MFA 프롬프트를 받으면 등록된 추가 확인 양식 중 하나를 선택할 수 있습니다. 관리자는 이러한 Azure 다단계 인증 인증 방법을 등록해야 하거나 사용자가 자신의 [내 프로필에](https://myprofile.microsoft.com) 액세스하여 확인 방법을 편집하거나 추가할 수 있습니다.

Azure 다단계 인증과 함께 다음과 같은 추가 확인 양식을 사용할 수 있습니다.

* Microsoft Authenticator 앱
* OATH 하드웨어 토큰
* sms
* 음성 통화

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Azure 다단계 인증을 활성화하고 사용하는 방법

Azure 다단계 인증에 대해 사용자 및 그룹을 활성화하여 로그인 이벤트 중에 추가 확인을 요청받을 수 있습니다. 모든 Azure AD 테넌트에서 모든 사용자에 대해 Microsoft 인증자 앱을 빠르게 사용할 수 있도록 [보안 기본값을](../fundamentals/concept-fundamentals-security-defaults.md) 사용할 수 있습니다.

보다 세부적인 컨트롤의 경우 [조건부 액세스](../conditional-access/overview.md) 정책을 사용하여 MFA가 필요한 이벤트 또는 응용 프로그램을 정의할 수 있습니다. 이러한 정책은 사용자가 회사 네트워크 또는 등록된 장치에 있을 때 정기적인 로그인 이벤트를 허용할 수 있지만 원격 또는 개인 장치에서 추가 확인 요소를 묻는 메시지가 표시됩니다.

![조건부 액세스를 적용하여 로그인 프로세스를 보호하는 방법에 대한 개략적인 다이어그램](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>다음 단계

라이선스에 대한 자세한 내용은 [Azure 다단계 인증에 대한 기능 및 라이선스를](concept-mfa-licensing.md)참조하십시오.

MFA가 실행 중이면 다음 자습서에서 테스트 사용자 집합에 대해 Azure 다단계 인증을 사용하도록 설정합니다.

> [!div class="nextstepaction"]
> [Azure Multi-Factor Authentication 사용](tutorial-mfa-applications.md)
