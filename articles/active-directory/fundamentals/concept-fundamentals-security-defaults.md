---
title: Azure Active Directory 보안 기본값
description: Azure AD의 일반 공격으로부터 조직을 보호하는 데 유용한 보안 기본 정책
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 385107f3bb5dd30a118d3a5f7a5f14213f0ad1c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85554528"
---
# <a name="what-are-security-defaults"></a>보안 기본값이란?

암호 스프레이, 재생 및 피싱 같은 일반적인 ID 관련 공격이 점점 더 많이 확산되게 되면 보안 관리가 어려울 수 있습니다. 보안 기본값을 사용하면 미리 구성된 보안 설정을 사용하여 조직을 이러한 공격에서 쉽게 보호할 수 있습니다.

- 모든 사용자에게 Azure Multi-Factor Authentication에 등록하도록 요구
- 관리자에게 다단계 인증을 수행하도록 요구
- 레거시 인증 프로토콜 차단
- 필요한 경우 사용자에게 다단계 인증을 수행하도록 요구
- Azure Portal에 대한 액세스와 같은 권한 있는 작업 보호

![보안 기본값을 사용하도록 설정/해제하는 Azure Portal의 스크린샷](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
보안 기본값을 사용 가능하게 하는 이유에 대한 자세한 내용은 Alex Weinert의 블로그 게시물 [보안 기본값 소개](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)에서 확인할 수 있습니다.

## <a name="availability"></a>가용성

Microsoft는 모든 사용자가 보안 기본값을 사용할 수 있도록 합니다. 목표는 모든 조직에서 추가 비용 없이 기본 수준의 보안을 설정할 수 있도록 하는 것입니다. Azure Portal에서 보안 기본값을 설정합니다. 테넌트가 2019년 10월 22일 이후에 만들어진 경우에는 테넌트에서 보안 기본값을 이미 사용할 수 있습니다. 모든 사용자를 보호하기 위해 생성된 모든 새 테넌트로 보안 기본값이 롤아웃됩니다.

### <a name="whos-it-for"></a>누구를 위한 기능인가요?

- 보안 상태를 높이려고 하지만 어떻게 어디서부터 시작해야 할지 잘 모르는 조직은 보안 기본값을 사용하면 좋습니다.
- Azure Active Directory 라이선스의 무료 계층을 활용하고 있는 조직은 보안 기본값을 사용하면 좋습니다.

### <a name="who-should-use-conditional-access"></a>조건부 액세스는 어떤 사용자가 사용해야 하나요?

- 현재, 조건부 액세스 정책을 사용하여 신호를 하고, 의사 결정을 내리고, 조직 정책을 적용하는 조직은 보안 기본값을 사용하는 것이 적절하지 않습니다. 
- Azure Active Directory Premium 라이선스가 있는 조직은 보안 기본값이 적절하지 않을 수 있습니다.
- 조직에 복잡한 보안 요구 사항이 있는 경우 조건부 액세스를 고려해야 합니다.

## <a name="policies-enforced"></a>정책 적용

### <a name="unified-multi-factor-authentication-registration"></a>통합 다단계 인증 등록

테넌트의 모든 사용자는 Azure Multi-Factor Authentication의 형태로 MFA(다단계 인증)에 등록해야 합니다. 사용자는 Microsoft Authenticator 앱을 사용하여 Azure Multi-Factor Authentication에 등록하는 데 14일이 제공됩니다. 14일이 지나면 사용자는 등록이 완료될 때까지 로그인할 수 없습니다. 사용자의 14일 기간은 보안 기본값을 사용하도록 설정한 후 처음 성공한 대화형 로그인 이후에 시작됩니다.

### <a name="protecting-administrators"></a>관리자 보호

권한 있는 액세스 권한이 있는 사용자는 사용자 환경에 대해 강화된 액세스 권한을 갖습니다. 이러한 계정은 권한이 크기 때문에 특별히 주의해서 처리해야 합니다. 권한 있는 계정의 보호를 향상하는 한 가지 일반적인 방법은 로그인 시 보다 강력한 형태의 계정 확인을 요구하는 것입니다. Azure AD에서 다단계 인증을 요구하면 보다 강력한 계정 확인이 가능합니다.

Azure Multi-Factor Authentication 등록을 완료한 후에는 로그인할 때마다 추가 인증 위해 다음 9개의 Azure AD 관리자 역할이 필요합니다.

- 전역 관리자
- SharePoint 관리자
- Exchange 관리자
- 조건부 액세스 관리자
- 보안 관리자
- 기술 지원팀 관리자
- 대금 청구 관리자
- 사용자 관리자
- 인증 관리자

### <a name="protecting-all-users"></a>모든 사용자 보호

관리자 계정도 추가 인증 계층이 필요한 유일한 계정이라고 생각하는 경향이 있습니다. 관리자는 중요한 정보에 대해 광범위한 액세스 권한을 가지며 구독 전체 설정을 변경할 수 있습니다. 그러나 공격자는 최종 사용자를 대상으로 하는 경우가 많습니다. 

이러한 공격자는 액세스 권한을 얻은 후에 원래 계정 소유자를 대신하여 권한 있는 정보에 대한 액세스를 요청할 수 있습니다. 전체 디렉터리를 다운로드하여 전체 조직에서 피싱 공격을 수행할 수도 있습니다. 

모든 사용자에 대해 보호를 개선하는 일반적인 방법 중 하나는 모든 사용자에 대해 다단계 인증과 같은 보다 강력한 형식의 계정 확인을 요구하는 것입니다. 사용자가 다단계 인증 등록을 완료한 후에는 필요할 때마다 추가 인증을 요구하는 메시지가 표시됩니다. 이 기능은 SaaS 애플리케이션을 포함하여 Azure AD에 등록된 모든 애플리케이션을 보호합니다.

### <a name="blocking-legacy-authentication"></a>레거시 인증 차단

사용자가 클라우드 앱에 쉽게 액세스할 수 있도록 Azure AD에서 레거시 인증을 포함한 다양한 인증 프로토콜을 지원합니다. *레거시 인증*은 다음을 통해 수행된 인증 요청을 나타내는 용어입니다.

- 최신 인증을 사용하지 않는 클라이언트(예: Office 2010 클라이언트)
- IMAP, SMTP, POP3 등의 이전 메일 프로토콜을 사용하는 모든 클라이언트

현재 대부분의 손상된 로그인 시도는 레거시 인증에서 제공됩니다. 레거시 인증은 다단계 인증을 지원하지 않습니다. 디렉터리에서 다단계 인증 정책을 사용하도록 설정한 경우에도 공격자는 이전 프로토콜을 사용하여 인증하고 다단계 인증을 무시할 수 있습니다. 

테넌트에서 보안 기본값을 사용하도록 설정한 후에는 이전 프로토콜을 통해 수행된 모든 인증 요청이 차단됩니다. 보안 기본값은 Exchange Active Sync 기본 인증을 차단합니다.

> [!WARNING]
> 보안 기본값을 사용하도록 설정하기 전에 관리자가 이전 인증 프로토콜을 사용하지 않는지 확인합니다. 자세한 내용은 [레거시 인증에서 전환하는 방법](concept-fundamentals-block-legacy-authentication.md)을 참조하세요.

- [Office 365 및 Microsoft 365를 사용하여 이메일을 보내도록 다기능 디바이스 또는 애플리케이션을 설정하는 방법](https://docs.microsoft.com/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>권한 있는 작업 보호

조직에서는 다음을 포함하여 Azure Resource Manager API를 통해 관리되는 다양한 Azure 서비스를 사용합니다.

- Azure portal 
- Azure PowerShell 
- Azure CLI

Azure Resource Manager를 사용하여 서비스를 관리하는 작업은 높은 권한 수준의 작업입니다. Azure Resource Manager는 서비스 설정 및 구독 청구와 같은 테넌트 전체 구성을 변경할 수 있습니다. 단일 단계 인증은 피싱 및 암호 스프레이와 같은 다양한 공격에 취약합니다. 

Azure Resource Manager에 액세스하고 구성을 업데이트하려는 사용자의 ID를 확인하는 것이 중요합니다. 액세스를 허용하기 전에 추가 인증을 요구하여 ID를 확인합니다.

테넌트에서 보안 기본값을 사용하도록 설정한 후 Azure Portal, Azure PowerShell 또는 Azure CLI에 액세스하는 모든 사용자는 추가 인증을 완료해야 합니다. 이 정책은 Azure Resource Manager에 액세스하는 관리자 또는 사용자 모두에게 적용됩니다. 

> [!NOTE]
> 2017년 이전 Exchange Online 테넌트는 최신 인증을 기본적으로 사용하지 않도록 설정합니다. 이러한 테넌트를 통해 인증하는 동안 로그인 루프가 발생하지 않도록 하려면 [최신 인증을 사용하도록 설정](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)해야 합니다.

> [!NOTE]
> Azure AD Connect 동기화 계정은 보안 기본값에서 제외되며 다단계 인증에 등록하거나 이러한 인증을 수행하라는 메시지가 표시되지 않습니다. 조직에서는 다른 용도로 이 계정을 사용하지 않아야 합니다.

## <a name="deployment-considerations"></a>배포 고려 사항

보안 기본값의 배포와 관련하여 다음과 같은 사항을 추가로 고려해야 합니다.

### <a name="authentication-methods"></a>인증 방법

이러한 무료 보안 기본값을 사용하면 **알림을 통해 Microsoft Authenticator 앱만 사용하여** Azure Multi-Factor Authentication에 등록하고 사용할 수 있습니다. 조건부 액세스를 사용하면 관리자가 사용하도록 선택하는 모든 인증 방법을 사용할 수 있습니다.

| 메서드 | 보안 기본값 | 조건부 액세스 |
| --- | --- | --- |
| 모바일 앱을 통한 알림 | X | X |
| 모바일 앱 또는 하드웨어 토큰의 확인 코드 | X** | X |
| 휴대폰에 문자 메시지 전송 |   | X |
| 휴대폰에 전화 걸기 |   | X |
| 앱 암호 |   | X*** |

- ** 사용자는 Microsoft Authenticator 앱의 확인 코드를 사용할 수 있지만 알림 옵션만 사용하여 등록할 수 있습니다.
- *** 앱 암호는 관리자가 사용하도록 설정한 경우에만 레거시 인증 시나리오를 사용하여 사용자별 MFA에서 사용할 수 있습니다.

### <a name="disabled-mfa-status"></a>사용하지 않도록 설정된 MFA 상태

조직이 사용자 기준 Azure Multi-Factor Authentication의 이전 사용자인 경우 다단계 인증 상태 페이지를 볼 때 **사용** 또는 **적용** 상태에 사용자가 표시되지 않더라도 놀라지 마세요. **사용 안 함**은 보안 기본값 또는 조건부 액세스 기반 Azure Multi-Factor Authentication을 사용하는 사용자에게 적절한 상태입니다.

### <a name="conditional-access"></a>조건부 액세스

조건부 액세스를 사용하여 보안 기본값과 유사하지만 보안 기본값에서는 사용할 수 없는 사용자 제외를 비롯한 보다 강력한 세분성으로 정책을 구성할 수 있습니다. 조건부 액세스를 사용하고 사용자 환경에서 조건부 액세스 정책을 사용하도록 설정하는 경우 보안 기본값을 사용할 수 없습니다. 조건부 액세스를 제공하는 라이선스가 있지만 사용자 환경에서 조건부 액세스 정책을 사용하도록 설정하지 않은 경우 조건부 액세스 정책을 사용하도록 설정할 때까지 보안 기본값 사용할 수 있습니다. Azure AD 라이선스에 대한 자세한 내용은 [Azure AD 가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory/)에서 찾을 수 있습니다.

![보안 기본값 또는 조건부 액세스 중 하나만 사용할 수 있다는 경고 메시지](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

조건부 액세스를 사용하여 보안 기본값을 통해 사용하도록 설정된 정책과 동급의 정책을 구성하는 방법에 대한 단계별 가이드는 다음과 같습니다.

- [관리자용 MFA 필요](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Azure 관리용 MFA 필요](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [레거시 인증 차단](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [모든 사용자용 MFA 필요](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Azure MFA 등록 필요](../identity-protection/howto-identity-protection-configure-mfa-policy.md) - Azure AD Premium P2의 Azure AD ID 보호 부분이 필요합니다.

## <a name="enabling-security-defaults"></a>보안 기본값 사용

디렉터리에서 보안 기본값을 사용하도록 설정하려면

1.  [Azure Portal](https://portal.azure.com) 에 보안 관리자, 조건부 액세스 관리자 또는 전역 관리자로 로그인합니다.
1.  **Azure Active Directory** > **속성**으로 이동합니다.
1. **보안 기본값 관리**를 선택합니다.
1. **보안 기본값 사용** 토글을 **예**로 설정합니다.
1. **저장**을 선택합니다.

## <a name="disabling-security-defaults"></a>보안 기본값 사용 안 함

보안 기본값을 대체하는 조건부 액세스 정책을 구현하도록 선택하는 조직은 보안 기본값을 사용하지 않도록 설정해야 합니다. 

![경고 메시지 보안 기본값을 사용하지 않도록 설정하여 조건부 액세스를 사용하도록 설정](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

디렉터리에서 보안 기본값을 사용하지 않도록 설정하려면

1.  [Azure Portal](https://portal.azure.com) 에 보안 관리자, 조건부 액세스 관리자 또는 전역 관리자로 로그인합니다.
1.  **Azure Active Directory** > **속성**으로 이동합니다.
1. **보안 기본값 관리**를 선택합니다.
1. **보안 기본값 사용** 토글을 **아니요**로 설정합니다.
1. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

[일반 조건부 액세스 정책](../conditional-access/concept-conditional-access-policy-common.md)
