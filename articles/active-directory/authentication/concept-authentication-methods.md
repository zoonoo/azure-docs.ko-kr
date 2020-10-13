---
title: 인증 방법 및 기능 - Azure Active Directory
description: 로그인 이벤트 개선 및 보안을 지원하는 데 사용할 수 있는 Azure Active Directory의 다양한 인증 방법 및 기능에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: c5ae0e0d312aa9a959b114d576f887bfa5072f49
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965508"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory에서 사용할 수 있는 인증 및 확인 방법은 무엇인가요?

Azure AD(Azure Active Directory)의 계정에 대한 로그인 환경에는 사용자가 스스로 인증할 수 있는 다양한 방법이 있습니다. 사용자 이름 및 암호는 사용자가 자격 증명을 기록으로 제공하는 가장 일반적인 방법입니다. Azure AD의 최신 인증 및 보안 기능을 사용 하면 기본 암호를 보완 하거나 보다 안전한 인증 방법으로 바꾸어야 합니다.

![Azure AD의 장점 및 기본 인증 방법 표](media/concept-authentication-methods/authentication-methods.png)

Windows Hello, FIDO2 보안 키, Microsoft Authenticator 앱 등의 passwordless 인증 방법은 가장 안전한 로그인 이벤트를 제공 합니다.

Azure Multi-Factor Authentication는 사용자가 로그인 할 때 암호를 사용 하는 경우에만 추가 보안을 추가 합니다. 사용자에 게 푸시 알림에 응답 하거나, 소프트웨어 또는 하드웨어 토큰에서 코드를 입력 하거나, SMS 또는 전화 통화에 응답 하는 등의 추가 인증 양식을 입력 하 라는 메시지가 표시 될 수 있습니다.

사용자가 온 보 딩 환경을 간소화 하 고 MFA 및 SSPR 모두에 등록 하려면 [결합 된 보안 정보 등록을 사용](howto-registration-mfa-sspr-combined.md)하는 것이 좋습니다. 복원 력을 위해 사용자에 게 여러 인증 방법을 등록 하도록 요구 하는 것이 좋습니다. 로그인 또는 SSPR 중에 한 가지 방법을 사용자가 사용할 수 없는 경우 다른 방법으로 인증 하도록 선택할 수 있습니다. 자세한 내용은 [AZURE AD에서 복원 력 있는 액세스 제어 관리 전략 만들기](concept-resilient-controls.md)를 참조 하세요.

## <a name="authentication-method-strength-and-security"></a>인증 방법 강도 및 보안

조직에서 Azure Multi-Factor Authentication와 같은 기능을 배포 하는 경우 사용 가능한 인증 방법을 검토 합니다. 보안, 유용성 및 가용성 측면에서 요구 사항을 충족 하거나 초과 하는 방법을 선택 합니다. 가능 하면 가장 높은 수준의 보안을 사용 하는 인증 방법을 사용 합니다.

다음 표에서는 사용 가능한 인증 방법에 대 한 보안 고려 사항을 간략하게 설명 합니다. 가용성은 사용자가 Azure AD의 서비스 가용성이 아니라 인증 방법을 사용할 수 있음을 나타냅니다.

| 인증 방법          | 보안 | 사용 편의성 | 가용성 |
|--------------------------------|:--------:|:---------:|:------------:|
| 비즈니스용 Windows Hello     | 높음     | 높음      | 높음         |
| Microsoft Authenticator 앱    | 높음     | 높음      | 높음         |
| FIDO2 보안 키 (미리 보기)   | 높음     | 높음      | 높음         |
| OATH 하드웨어 토큰(미리 보기) | 중간   | 중간    | 높음         |
| OATH 소프트웨어 토큰           | 중간   | 중간    | 높음         |
| SMS                            | 중간   | 높음      | 중간       |
| 음성                          | 중간   | 중간    | 중간       |
| 암호                       | 낮음      | 높음      | 높음         |

보안에 대 한 자세한 내용은 [인증 취약성 및 공격 벡터](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)를 참조 하세요.

> [!TIP]
> 유연성과 유용성을 위해 Microsoft Authenticator 앱을 사용 하는 것이 좋습니다. 이 인증 방법은 passwordless, MFA 푸시 알림 및 OATH 코드와 같은 최상의 사용자 환경 및 여러 모드를 제공 합니다.

## <a name="how-each-authentication-method-works"></a>각 인증 방법의 작동 방식

일부 인증 방법은 FIDO2 보안 키 또는 암호를 사용 하는 등 응용 프로그램 또는 장치에 로그인 할 때 기본 요소로 사용할 수 있습니다. 다른 인증 방법은 Azure Multi-Factor Authentication 또는 SSPR를 사용 하는 경우에만 보조 요소로 사용할 수 있습니다.

다음 표에서는 로그인 이벤트 중에 인증 방법을 사용할 수 있는 경우에 대해 간략하게 설명 합니다.

| 방법                         | Primary authentication(기본 인증) | 보조 인증  |
|--------------------------------|:----------------------:|:-------------------------:|
| 비즈니스용 Windows Hello     | 예                    | MFA                       |
| Microsoft Authenticator 앱    | 예(미리 보기)          | MFA 및 SSPR              |
| FIDO2 보안 키 (미리 보기)   | 예                    | MFA                       |
| OATH 하드웨어 토큰(미리 보기) | 예                     | MFA                       |
| OATH 소프트웨어 토큰           | 예                     | MFA                       |
| SMS                            | 예(미리 보기)          | MFA 및 SSPR              |
| 음성 통화                     | 예                     | MFA 및 SSPR              |
| 암호                       | 예                    |                           |

이러한 모든 인증 방법은 Azure Portal에서 구성할 수 있으며 [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)를 사용 하 여 점점 더 사용할 수 있습니다.

각 인증 방법의 작동 방식에 대 한 자세한 내용은 다음의 개별 개념 문서를 참조 하세요.

* [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Microsoft Authenticator 앱](concept-authentication-authenticator-app.md)
* [FIDO2 보안 키 (미리 보기)](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH 하드웨어 토큰(미리 보기)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [OATH 소프트웨어 토큰](concept-authentication-oath-tokens.md#oath-software-tokens)
* SMS [로그인 (미리 보기)](howto-authentication-sms-signin.md) 및 [확인](concept-authentication-phone-options.md#mobile-phone-verification)
* [음성 통화 확인](concept-authentication-phone-options.md)
* 암호

> [!NOTE]
> Azure AD에서 암호는 주로 기본 인증 방법 중 하나입니다. 암호 인증 방법을 사용하지 않도록 설정할 수는 없습니다. 암호를 기본 인증 요소로 사용 하는 경우 Azure Multi-Factor Authentication를 사용 하 여 로그인 이벤트의 보안을 강화 합니다.

특정 시나리오에서 다음과 같은 추가 인증 방법을 사용할 수 있습니다.

* [앱 암호](howto-mfa-app-passwords.md) -최신 인증을 지원 하지 않으며 사용자별 Azure Multi-Factor Authentication에 대해 구성할 수 있는 이전 응용 프로그램에 사용 됩니다.
* [보안 질문](concept-authentication-security-questions.md) -SSPR에만 사용 됩니다.
* [전자 메일 주소](concept-sspr-howitworks.md#authentication-methods) -SSPR에만 사용 됩니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [SSPR(셀프 서비스 암호 재설정)에 대한 자습서][tutorial-sspr] 및 [Azure Multi-Factor Authentication][tutorial-azure-mfa]을 참조하세요.

SSPR 개념에 대한 자세한 내용은 [Azure AD 셀프 서비스 암호 재설정 작동 방법][concept-sspr]을 참조하세요.

MFA 옵션에 대해 자세히 알아보려면 [Azure Multi-Factor Authentication 작동 방법][concept-mfa]을 참조하세요.

[Microsoft Graph REST API 베타](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)를 사용 하 여 인증 방법 구성에 대해 자세히 알아보세요.

사용 중인 인증 방법을 검토 하려면 PowerShell을 사용 하 [여 Azure Multi-Factor Authentication 인증 방법 분석](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)을 참조 하세요.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
