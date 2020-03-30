---
title: 조건부 액세스 기준 정책 - Azure Active Directory
description: 일반적인 공격으로부터 조직을 보호하기 위한 기본 조건부 액세스 정책
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de5a5c604273225a85e49ca682980f83a951d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767571"
---
# <a name="what-are-baseline-policies"></a>기준 정책이란 무엇입니까?

기준 정책은 많은 일반적인 공격으로부터 조직을 보호하는 데 도움이 되는 미리 정의된 정책 집합입니다. 이러한 일반적인 공격은 암호 스프레이, 재생 및 피싱을 포함할 수 있습니다. 기준 정책은 Azure AD의 모든 버전에서 사용할 수 있습니다. 지난 몇 년 동안 ID 기반 공격이 증가해 왔기 때문에 Microsoft는 이러한 기준 보호 정책을 모든 사용자에게 제공하고 있습니다. 이 네 가지 정책의 목표는 모든 조직이 추가 비용 없이 기본 보안 수준을 사용하도록 설정하는 것입니다.

사용자 지정된 조건부 액세스 정책을 관리하려면 Azure AD Premium 라이선스가 필요합니다.

> [!IMPORTANT]
> 기준 정책이 더 이상 사용되지 않습니다. 자세한 내용은 [Azure Active Directory의 새로운 내용을](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults) 참조하세요.

## <a name="baseline-policies"></a>기준 정책

![Azure 포털의 조건부 액세스 기준 정책](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

네 가지 기준 정책이 있습니다.

* 관리자를 위한 MFA 필요(미리 보기)
* 최종 사용자 보호(미리 보기)
* 레거시 인증 차단(미리 보기)
* 서비스 관리를 위한 MFA 필요(미리 보기)

이러한 네 가지 정책은 모두 POP, IMAP 및 이전 Office 데스크톱 클라이언트와 같은 레거시 인증 흐름에 영향을 미칩니다.

### <a name="exclusions"></a>제외

기준 정책이 초기 공개 미리 보기에 들어갔을 때 정책에서 사용자를 제외하는 옵션이 있었습니다. 이 기능은 미리 보기를 통해 발전했으며 2019년 7월에 제거되었습니다. 이미 제외를 만든 조직은 새 사용자가 정책에 제외를 추가할 수 없도록 계속 유지할 수 있었습니다.

### <a name="require-mfa-for-admins-preview"></a>관리자를 위한 MFA 필요(미리 보기)

관리자 계정에 있는 전원 및 액세스 권한으로 인해 특별한 주의를 기울여 처리해야 합니다. 권한 있는 계정의 보호를 개선하는 한 가지 일반적인 방법은 로그인하는 데 사용할 때 보다 강력한 형태의 계정 확인을 요구하는 것입니다. Azure Active Directory에서 관리자가 Azure 다단계 인증을 등록하고 사용하도록 요구하여 보다 강력한 계정 확인을 받을 수 있습니다.

관리자에 대 한 MFA 필요 (미리 보기) 가장 권한이 있는 Azure AD 역할으로 간주 되는 다음 디렉터리 역할에 대 한 다단계 인증 (MFA)를 필요로 하는 기준 정책입니다.

* 전역 관리자
* SharePoint 관리자
* Exchange 관리자
* 조건부 액세스 관리자
* 보안 관리자
* 기술 지원팀 관리자/암호 관리자
* 대금 청구 관리자
* 사용자 관리자

조직에서 스크립트 또는 코드에 이러한 계정을 사용 중인 경우 이를 [관리 ID](../managed-identities-azure-resources/overview.md)로 바꾸는 것이 좋습니다.

### <a name="end-user-protection-preview"></a>최종 사용자 보호(미리 보기)

높은 권한이 있는 관리자만이 공격의 대상이 되는 것은 아닙니다. 악의적인 행위자는 일반 사용자를 대상으로 하는 경향이 있습니다. 액세스 권한을 얻은 후 이러한 악의적인 행위자는 원래 계정 소유자를 대신하여 권한 있는 정보에 대한 액세스를 요청하거나 전체 디렉터리를 다운로드하여 전체 조직에 대한 피싱 공격을 수행할 수 있습니다. 모든 사용자에 대한 보호를 개선하는 한 가지 일반적인 방법은 위험한 로그인이 감지되면 더 강력한 형태의 계정 확인을 요구하는 것입니다.

**최종 사용자 보호(미리 보기)는** 디렉터리에서 모든 사용자를 보호하는 기준 정책입니다. 이 정책을 사용하려면 모든 사용자가 14일 이내에 Azure 다단계 인증에 등록해야 합니다. 등록된 사용자에게는 위험한 로그인 시도 중에만 MFA를 묻는 메시지가 표시됩니다. 손상된 사용자 계정은 암호를 다시 설정하고 위험을 해제할 때까지 차단됩니다. 

> [!NOTE]
> 이전에 위험에 대해 플래그가 지정된 모든 사용자는 암호가 재설정될 때까지 차단되고 정책 활성화 시 해고될 위험이 있습니다.

### <a name="block-legacy-authentication-preview"></a>레거시 인증 차단(미리 보기)

레거시 인증 프로토콜(예: IMAP, SMTP, POP3)은 일반적으로 이전 메일 클라이언트에서 인증하는 데 사용되는 프로토콜입니다. 레거시 프로토콜은 다단계 인증을 지원하지 않습니다. 디렉터리에 다단계 인증을 요구하는 정책이 있더라도 악의적인 행위자는 이러한 레거시 프로토콜 중 하나를 사용하여 인증하고 다단계 인증을 우회할 수 있습니다.

레거시 프로토콜에 의한 악의적인 인증 요청으로부터 계정을 보호하는 가장 좋은 방법은 계정을 차단하는 것입니다.

**레거시 인증 차단(미리 보기)** 기준 정책은 레거시 프로토콜을 사용하여 만들어진 인증 요청을 차단합니다. 모든 사용자에 대해 성공적으로 로그인하려면 최신 인증을 사용해야 합니다. 다른 기준 정책과 함께 사용하면 레거시 프로토콜에서 오는 요청이 차단됩니다. 또한 모든 사용자는 필요할 때마다 MFA에 필요합니다. 이 정책은 Exchange ActiveSync를 차단하지 않습니다.

### <a name="require-mfa-for-service-management-preview"></a>서비스 관리를 위한 MFA 필요(미리 보기)

조직은 다음과 같은 다양한 Azure 서비스를 사용하고 Azure Resource Manager 기반 도구에서 관리합니다.

* Azure portal
* Azure PowerShell
* Azure CLI

이러한 도구를 사용하여 리소스 관리를 수행하는 것은 매우 권한 있는 작업입니다. 이러한 도구는 서비스 설정 및 구독 청구와 같은 구독 전체 구성을 변경할 수 있습니다.

권한 있는 작업을 보호 하려면 이 **서비스 관리(미리 보기)** 정책에 대 한 MFA 필요 Azure 포털, Azure PowerShell 또는 Azure CLI에 액세스 하는 모든 사용자에 대 한 다단계 인증이 필요 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

* [보안 기본설정](../fundamentals/concept-fundamentals-security-defaults.md)
* [일반 조건부 액세스 정책](concept-conditional-access-policy-common.md)
* [ID 인프라를 보호하기 위한 5단계](../../security/fundamentals/steps-secure-identity.md)
