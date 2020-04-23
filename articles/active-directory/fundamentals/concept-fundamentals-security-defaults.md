---
title: Azure Active Directory 보안 기본값
description: 일반적인 공격으로부터 조직을 보호하는 데 도움이 되는 보안 기본 정책
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f307553a97973d03b0699248373e53e4845aa39a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869909"
---
# <a name="what-are-security-defaults"></a>보안 기본값이란 무엇입니까?

일반적인 ID 관련 공격이 점점 더 인기를 끌고 있는 경우 보안을 관리하기가 어려울 수 있습니다. 이러한 공격에는 암호 스프레이, 재생 및 피싱이 포함됩니다.

Azure Active Directory(Azure AD)의 보안 기본값을 사용하면 보안을 강화하고 조직을 보호하는 데 도움이 됩니다. 보안 기본값에는 일반적인 공격에 대해 미리 구성된 보안 설정이 포함되어 있습니다. 

Microsoft는 모든 사용자가 보안 기본값을 사용할 수 있도록 하고 있습니다. 목표는 모든 조직이 추가 비용 없이 기본 수준의 보안을 사용하도록 설정하는 것입니다. Azure 포털에서 보안 기본값을 켭니다.

![보안 기본값을 사용하도록 설정하는 토글이 있는 Azure 포털의 스크린샷](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> 테넌트가 2019년 10월 22일 또는 그 이후에 생성된 경우 새 보안 기본 동작이 발생하고 테넌트에서 보안 기본값이 이미 활성화되어 있을 수 있습니다. 모든 사용자를 보호하기 위해 생성된 모든 새 테넌트에 보안 기본값이 롤아웃되고 있습니다.

보안 기본값을 사용할 수 있게 되는 이유에 대한 자세한 내용은 Alex Weinert의 블로그 게시물, [보안 기본값 소개에서](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)확인할 수 있습니다.

## <a name="unified-multi-factor-authentication-registration"></a>통합 다단계 인증 등록

테넌트의 모든 사용자는 Azure 다단계 인증 서비스의 형태로 MFA(다단계 인증)에 등록해야 합니다. 사용자는 Microsoft 인증자 앱을 사용하여 다단계 인증에 등록하는 데 14일이 있습니다. 14일이 지나면 다단계 인증 등록이 완료될 때까지 로그인할 수 없습니다.

일부 사용자는 보안 기본값을 사용하도록 설정한 직후 14일 동안 에는 퇴사하거나 로그인하지 않을 수 있습니다. 모든 사용자가 다단계 인증에 등록할 충분한 시간을 갖도록 하기 위해 14일 기간은 각 사용자에 대해 고유합니다. 사용자의 14일 기간은 보안 기본값을 사용하도록 설정한 후 첫 번째 대화형 로그인 이후에 시작됩니다.

## <a name="multi-factor-authentication-enforcement"></a>다단계 인증 적용

### <a name="protecting-administrators"></a>관리자 보호

권한 있는 계정에 액세스할 수 있는 사용자는 사용자 환경에 대한 액세스 권한을 증가시가지 않습니다. 이러한 계정은 권한이 크기 때문에 특별히 주의해서 처리해야 합니다. 권한 있는 계정의 보호를 개선하는 한 가지 일반적인 방법은 로그인을 위해 보다 강력한 형태의 계정 확인을 요구하는 것입니다. Azure AD에서는 다단계 인증을 요구하여 더 강력한 계정 확인을 받을 수 있습니다.

다단계 인증에 대한 등록이 완료되면 로그인할 때마다 추가 인증을 수행하기 위해 다음 9개의 Azure AD 관리자 역할이 필요합니다.

- 전역 관리자
- SharePoint 관리자
- Exchange 관리자
- 조건부 액세스 관리자
- 보안 관리자
- 헬프데스크 관리자 또는 암호 관리자
- 대금 청구 관리자
- 사용자 관리자
- 인증 관리자

### <a name="protecting-all-users"></a>모든 사용자 보호

관리자 계정이 추가 인증 계층이 필요한 유일한 계정이라고 생각하는 경향이 있습니다. 관리자는 중요한 정보에 광범위하게 액세스할 수 있으며 구독 전체 설정을 변경할 수 있습니다. 그러나 공격자는 최종 사용자를 대상으로 하는 경향이 있습니다. 

이러한 공격자가 액세스 권한을 얻은 후에는 원래 계정 소유자를 대신하여 권한 있는 정보에 대한 액세스를 요청할 수 있습니다. 전체 디렉터리를 다운로드하여 전체 조직에 대한 피싱 공격을 수행할 수도 있습니다. 

모든 사용자에 대한 보호를 개선하는 한 가지 일반적인 방법은 모든 사용자를 위해 다단계 인증과 같은 보다 강력한 형태의 계정 확인을 요구하는 것입니다. 사용자가 다단계 인증 등록을 완료하면 필요할 때마다 추가 인증을 요청합니다.

### <a name="blocking-legacy-authentication"></a>레거시 인증 차단

사용자에게 클라우드 앱에 쉽게 액세스할 수 있도록 Azure AD는 레거시 인증을 비롯한 다양한 인증 프로토콜을 지원합니다. *레거시 인증은* 다음이 만든 인증 요청을 참조하는 용어입니다.

- 최신 인증을 사용하지 않는 클라이언트(예: Office 2010 클라이언트).
- IMAP, SMTP 또는 POP3와 같은 이전 메일 프로토콜을 사용하는 모든 클라이언트입니다.

오늘날 대부분의 손상 사인 인 시도는 레거시 인증에서 비롯됩니다. 레거시 인증은 다단계 인증을 지원하지 않습니다. 디렉터리에서 다단계 인증 정책을 사용하도록 설정되어 있더라도 공격자는 이전 프로토콜을 사용하여 인증하고 다단계 인증을 우회할 수 있습니다. 

테넌트에서 보안 기본값을 사용하도록 설정하면 이전 프로토콜에 의한 모든 인증 요청이 차단됩니다. 보안 기본값은 Exchange 활성 동기화 기본 인증을 차단합니다.

> [!WARNING]
> 보안 기본값을 사용하도록 설정하기 전에 관리자가 이전 인증 프로토콜을 사용하고 있지 않은지 확인합니다. 자세한 내용은 [레거시 인증에서 벗어나는 방법을 참조하세요.](concept-fundamentals-block-legacy-authentication.md)

### <a name="protecting-privileged-actions"></a>권한 있는 작업 보호

조직은 다음과 같은 Azure 리소스 관리자 API를 통해 관리되는 다양한 Azure 서비스를 사용합니다.

- Azure portal 
- Azure PowerShell 
- Azure CLI

Azure 리소스 관리자를 사용하여 서비스를 관리하는 것은 권한 높은 작업입니다. Azure 리소스 관리자는 서비스 설정 및 구독 청구와 같은 테넌트 전체 구성을 변경할 수 있습니다. 단일 단계 인증은 피싱 및 암호 스프레이와 같은 다양한 공격에 취약합니다. 

Azure 리소스 관리자 및 업데이트 구성에 액세스하려는 사용자의 ID를 확인하는 것이 중요합니다. 액세스를 허용하기 전에 추가 인증을 요구하여 ID를 확인합니다.

테넌트에서 보안 기본값을 사용하도록 설정하면 Azure 포털, Azure PowerShell 또는 Azure CLI에 액세스하는 모든 사용자는 추가 인증을 완료해야 합니다. 이 정책은 관리자든 사용자이든 Azure 리소스 관리자에 액세스하는 모든 사용자에게 적용됩니다. 

> [!NOTE]
> 2017년 이전 Exchange Online 테넌은 기본적으로 최신 인증을 사용하지 않도록 설정했습니다. 이러한 테넌트를 통해 인증하는 동안 로그인 루프의 가능성을 방지하려면 [최신 인증을 사용하도록 설정해야](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)합니다.

> [!NOTE]
> Azure AD Connect 동기화 계정은 보안 기본값에서 제외되며 다단계 인증을 등록하거나 수행하라는 메시지가 표시되지 않습니다. 조직은 이 계정을 다른 목적으로 사용해서는 안 됩니다.

## <a name="deployment-considerations"></a>배포 고려 사항

다음 추가 고려 사항은 테넌트에 대한 보안 기본값 배포와 관련이 있습니다.

### <a name="authentication-methods"></a>인증 방법

보안 기본값은 **알림을 사용하여 Microsoft 인증자 앱만 사용하여**Azure 다단계 인증을 등록하고 사용할 수 있도록 합니다. 조건부 액세스를 사용하면 관리자가 사용하도록 선택한 모든 인증 방법을 사용할 수 있습니다.

|   | 보안 기본값 | 조건부 액세스 |
| --- | --- | --- |
| 모바일 앱을 통한 알림 | X | X |
| 모바일 앱 또는 하드웨어 토큰의 확인 코드 |   | X |
| 휴대폰에 문자 메시지 전송 |   | X |
| 휴대폰에 전화 걸기 |   | X |
| 앱 암호 |   | X** |

** 앱 암호는 관리자가 사용하도록 설정한 경우에만 레거시 인증 시나리오가 있는 사용자별 MFA에서만 사용할 수 있습니다.

### <a name="conditional-access"></a>조건부 액세스

조건부 Access를 사용하여 보안 기본값과 유사하지만 보안 기본값에서 사용할 수 없는 사용자 제외를 포함하여 보다 세분한 정책을 구성할 수 있습니다. 조건부 액세스를 사용하고 있고 사용자 환경에서 조건부 액세스 정책을 사용하도록 설정한 경우 보안 기본값을 사용할 수 없습니다. 조건부 액세스를 제공하지만 사용자 환경에서 조건부 액세스 정책을 사용하도록 설정한 라이선스가 없는 경우 조건부 액세스 정책을 사용하도록 설정할 때까지 보안 기본값을 사용할 수 있습니다. Azure AD 라이선스에 대한 자세한 내용은 [Azure AD 가격 책정 페이지에서](https://azure.microsoft.com/pricing/details/active-directory/)확인할 수 있습니다.

![보안 기본값 또는 조건부 액세스가 둘 다 없을 수 있다는 경고 메시지](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

다음은 조건부 액세스를 사용하여 동등한 정책을 구성하는 방법에 대한 단계별 가이드입니다.

- [관리자용 MFA 필요](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Azure 관리용 MFA 필요](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [레거시 인증 차단](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [모든 사용자용 MFA 필요](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Azure MFA 등록 필요](../identity-protection/howto-identity-protection-configure-mfa-policy.md) - Azure AD ID 보호 필요

## <a name="enabling-security-defaults"></a>보안 기본설정

디렉터리에서 보안 기본값을 사용하려면 다음을 수행하십시오.

1.  [Azure 포털에](https://portal.azure.com) 보안 관리자, 조건부 액세스 관리자 또는 전역 관리자로 로그인합니다.
1. Azure **Active 디렉터리** > **속성으로**검색합니다.
1. **보안 기본값 관리를 선택합니다.**
1. 보안 **기본값 설정** 설정 **(예)**
1. **저장**을 선택합니다.

## <a name="disabling-security-defaults"></a>보안 기본값 사용 안 설정

보안 기본값을 대체하는 조건부 액세스 정책을 구현하도록 선택한 조직은 보안 기본값을 사용하지 않도록 설정해야 합니다. 

![경고 메시지는 조건부 액세스를 사용하도록 설정하기 위해 보안 기본값을 사용하지 않도록 설정합니다.](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

디렉터리에서 보안 기본값을 사용하지 않도록 설정하려면 다음을 수행하십시오.

1.  [Azure 포털에](https://portal.azure.com) 보안 관리자, 조건부 액세스 관리자 또는 전역 관리자로 로그인합니다.
1. Azure **Active 디렉터리** > **속성으로**검색합니다.
1. **보안 기본값 관리를 선택합니다.**
1. 보안 기본값 설정 **없음으로** **설정합니다.**
1. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

[일반 조건부 액세스 정책](../conditional-access/concept-conditional-access-policy-common.md)
