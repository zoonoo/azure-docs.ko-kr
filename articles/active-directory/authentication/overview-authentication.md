---
title: Azure Active Directory 인증 개요
description: Azure Active Directory를 사용하여 사용자 로그인에 대한 다양한 인증 방법 및 보안 기능에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 07/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e65149b2524d2785039be78caad4ce84924e7e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87035114"
---
# <a name="what-is-azure-active-directory-authentication"></a>Azure Active Directory 인증이란?

ID 플랫폼의 주요 기능 중 하나는 사용자가 디바이스, 애플리케이션 또는 서비스에 로그인할 때 자격 증명을 확인하거나 *인증*하는 것입니다. Azure AD(Azure Active Directory)에서 인증은 사용자 이름과 암호를 확인하는 것 외에도 더 많은 작업을 수행합니다. 보안을 강화하고 지원 센터 지원의 필요성을 줄이기 위해 Azure AD 인증에 포함되는 구성 요소는 다음과 같습니다.

* 셀프 서비스 암호 재설정
* Azure Multi-Factor Authentication
* 온-프레미스 환경에 암호 변경 쓰기를 저장하기 위한 하이브리드 통합
* 온-프레미스 환경에 암호 보호 정책을 적용하기 위한 하이브리드 통합
* 암호 없는 인증

## <a name="improve-the-end-user-experience"></a>최종 사용자 환경 개선

Azure AD는 사용자의 ID를 보호하고 로그인 환경을 간소화하는 데 도움이 됩니다. 셀프 서비스 암호 재설정과 같은 기능을 통해 사용자는 모든 디바이스에서 웹 브라우저를 사용하여 암호를 업데이트하거나 변경할 수 있습니다. 이 기능은 사용자가 암호를 잊어버렸거나 계정이 잠겨 있는 경우에 특히 유용합니다. 지원 센터 또는 관리자가 지원을 제공할 때까지 기다리지 않고 사용자가 자신을 차단 해제하여 작업을 계속 수행할 수 있습니다.

Azure Multi-Factor Authentication을 사용하면 사용자가 로그인하는 동안 전화 통화 또는 모바일 앱 알림과 같은 추가 형식의 인증을 선택할 수 있습니다. 이 기능을 통해 하드웨어 토큰과 같이 고정된 단일 형식의 보조 인증에 대한 요구 사항을 줄일 수 있습니다. 사용자에게 현재 한 가지 형식의 추가 인증이 없는 경우 다른 방법을 선택하여 작업을 계속 수행할 수 있습니다.

![로그인 화면에서 사용하는 인증 방법](media/concept-authentication-methods/overview-login.png)

암호 없는 인증을 사용하면 사용자가 보안 암호를 만들고 저장할 필요가 전혀 없습니다. 비즈니스용 Windows Hello 또는 FIDO2 보안 키와 같은 기능을 사용하면 사용자가 암호 없이 디바이스 또는 애플리케이션에 로그인할 수 있습니다. 이 기능을 통해 다양한 환경에서 암호 관리의 복잡성을 줄일 수 있습니다.

## <a name="self-service-password-reset"></a>셀프 서비스 암호 재설정

셀프 서비스 암호 재설정을 사용하면 관리자 또는 지원 센터에서 개입하지 않고도 사용자가 암호를 변경하거나 다시 설정할 수 있습니다. 사용자 계정이 잠겨 있거나 암호를 잊어버린 경우 프롬프트에 따라 자신을 차단 해제하여 작업을 다시 수행할 수 있습니다. 사용자가 디바이스 또는 애플리케이션에 로그인할 수 없는 경우 이 기능을 통해 지원 센터 호출 및 생산성 저하를 줄일 수 있습니다.

셀프 서비스 암호 재설정이 작동하는 시나리오는 다음과 같습니다.

* **암호 변경 –** 사용자가 암호를 알고 있지만 새 암호로 변경하려는 경우
* **암호 재설정 -** 사용자가 암호를 잊어버린 경우와 같이 로그인할 수 없고 암호를 다시 설정하려는 경우
* **계정 잠금 해제 -** 계정이 잠겨 있어 로그인할 수 없을 때 계정의 잠금을 해제하려는 경우

사용자가 셀프 서비스 암호 재설정을 사용하여 암호를 업데이트하거나 다시 설정하는 경우 해당 암호 쓰기를 온-프레미스 Active Directory 환경에 저장할 수도 있습니다. 비밀번호 쓰기 저장을 통해 사용자가 업데이트된 자격 증명을 온-프레미스 디바이스 및 애플리케이션에 즉시 사용할 수 있습니다.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

다단계 인증은 로그인 프로세스 중에 코드를 휴대폰에 입력하거나 지문 검사를 제공하는 것과 같은 추가 형식의 식별을 요구하는 프로세스입니다.

암호만 사용하여 사용자를 인증하면 공격에 안전하지 않은 벡터가 유지됩니다. 암호가 약하거나 다른 곳에 노출된 경우 실제로 사용자 이름과 암호를 사용하는 사용자 로그인일까요, 아니면 공격자일까요? 두 번째 인증 형식이 필요한 경우 이 추가 요소는 공격자가 쉽게 얻거나 복제할 수 있는 것이 아니므로 보안이 향상됩니다.

![다양한 형식의 다단계 인증에 대한 개념적 이미지](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication은 다음 인증 방법 중 둘 이상을 요구하여 작동합니다.

* 사용자가 알고 있는 정보(일반적으로 암호)
* 사용자의 소유물 정보(예: 휴대폰 또는 하드웨어 키와 같이 쉽게 복제되지 않는 신뢰할 수 있는 디바이스)
* 사용자의 생체 인식 정보(예: 지문 또는 얼굴 스캔)

사용자는 셀프 서비스 암호 재설정 및 Azure Multi-Factor Authentication을 모두 한 단계로 등록하여 온보딩 환경을 간소화할 수 있습니다. 관리자는 사용할 수 있는 보조 인증 형식을 정의할 수 있습니다. 사용자가 셀프 서비스 암호 재설정을 수행하여 프로세스를 더 안전하게 보호하는 경우에도 Azure Multi-Factor Authentication이 필요할 수 있습니다.

## <a name="password-protection"></a>암호 보호

Azure AD는 기본적으로 *Password1*와 같은 취약한 암호를 차단합니다. 알려진 취약한 암호가 포함되는 글로벌로 금지된 암호 목록이 자동으로 업데이트되고 적용됩니다. Azure AD 사용자가 암호를 이러한 취약한 암호 중 하나로 설정하려고 하면 더 안전한 암호를 선택하라는 알림이 받습니다.

보안을 강화하기 위해 사용자 지정 암호 보호 정책을 정의할 수 있습니다. 예를 들어 이러한 정책은 필터를 사용하여 이름(예: *Contoso*) 또는 위치(예: *London*)가 포함되는 암호의 변형을 차단할 수 있습니다.

하이브리드 보안을 위해 Azure AD 암호 보호를 온-프레미스 Active Directory 환경에 통합할 수 있습니다. 온-프레미스 환경에 설치된 구성 요소는 Azure AD로부터 글로벌로 금지된 암호 목록 및 사용자 지정 암호 보호 정책을 받고, 도메인 컨트롤러는 이를 사용하여 암호 변경 이벤트를 처리합니다. 이러한 하이브리드 방법을 사용하면 사용자가 자격 증명을 변경하는 방법 또는 위치에 관계없이 강력한 암호를 사용하도록 적용할 수 있습니다.

## <a name="passwordless-authentication"></a>암호 없는 인증

대부분의 환경에서 최종 목표는 로그인 이벤트의 일부로 암호 사용을 제거하는 것입니다. Azure 암호 보호 또는 Azure Multi-Factor Authentication과 같은 기능은 보안을 향상시키는 데 도움이 되지만, 사용자 이름과 암호는 노출되거나 무차별 암호 대입 공격을 받을 수 있는 취약한 형식의 인증으로 유지됩니다.

![암호 없는 인증 프로세스를 통한 보안 및 편의성](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

암호 없는 인증 방법으로 로그인하는 경우 비즈니스용 Windows Hello 또는 FIDO2 보안 키를 사용하는 생체 인식과 같은 방법을 사용하여 자격 증명이 제공됩니다. 이러한 인증 방법은 공격자가 쉽게 복제할 수 없습니다.

Azure AD는 기본적으로 암호 없는 방법을 통해 인증하여 사용자의 로그인 환경을 간소화하고 공격 위험을 줄일 수 있습니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [SSPR(셀프 서비스 암호 재설정)에 대한 자습서][tutorial-sspr] 및 [Azure Multi-Factor Authentication][tutorial-azure-mfa]을 참조하세요.

셀프 서비스 암호 재설정 개념에 대한 자세한 내용은 [Azure AD 셀프 서비스 암호 재설정 작동 방법][concept-sspr]을 참조하세요.

다단계 인증 개념에 대한 자세한 내용은 [Azure Multi-Factor Authentication 작동 방법][concept-mfa]을 참조하세요.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
