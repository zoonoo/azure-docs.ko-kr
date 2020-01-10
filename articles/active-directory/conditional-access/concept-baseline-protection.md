---
title: 조건부 액세스 기준 정책-Azure Active Directory
description: 일반적인 공격 으로부터 조직을 보호 하기 위한 기준 조건부 액세스 정책
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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75767571"
---
# <a name="what-are-baseline-policies"></a>기준 정책 이란?

기준 정책은 여러 일반적인 공격 으로부터 조직을 보호 하는 데 도움이 되는 미리 정의 된 정책 집합입니다. 이러한 일반적인 공격은 암호 스프레이, 재생 및 피싱을 포함할 수 있습니다. 기본 정책은 모든 버전의 Azure AD에서 사용할 수 있습니다. 지난 몇 년 동안 ID 기반 공격이 증가해 왔기 때문에 Microsoft는 이러한 기준 보호 정책을 모든 사용자에게 제공하고 있습니다. 이러한 네 가지 정책의 목표는 모든 조직에서 추가 비용 없이 기본 보안 수준을 사용 하도록 설정 하는 것입니다.

사용자 지정 된 조건부 액세스 정책을 관리 하려면 Azure AD Premium 라이선스가 필요 합니다.

> [!IMPORTANT]
> 기준 정책은 더 이상 사용 되지 않습니다. 자세한 내용은 [Azure Active Directory의 새로운 기능](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults) 을 참조 하세요.

## <a name="baseline-policies"></a>기준 정책

![Azure Portal의 조건부 액세스 기준 정책](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

다음 네 가지 기준 정책이 있습니다.

* 관리자 용 MFA 필요 (미리 보기)
* 최종 사용자 보호 (미리 보기)
* 레거시 인증 차단 (미리 보기)
* 서비스 관리를 위해 MFA 필요 (미리 보기)

이러한 정책 중 4 개는 POP, IMAP 및 이전 Office 데스크톱 클라이언트와 같은 레거시 인증 흐름에 영향을 줍니다.

### <a name="exclusions"></a>제외

기준 정책이 초기 공개 미리 보기로 전환 되 면 정책에서 사용자를 제외 하는 옵션이 있습니다. 이 기능은 미리 보기를 통해 진화 되었으며 7 월 2019 일에 제거 되었습니다. 이미 제외를 만든 조직에서는 계속 해 서 새 사용자가 정책에 제외를 추가할 수 없었습니다.

### <a name="require-mfa-for-admins-preview"></a>관리자 용 MFA 필요 (미리 보기)

관리자 계정에는 강력 하 고 액세스 권한이 있으므로 특별히 주의 해 서 처리 해야 합니다. 권한 있는 계정에 대 한 보호를 개선 하는 일반적인 방법 중 하나는 로그인에 사용할 때 보다 강력한 계정 확인을 요구 하는 것입니다. Azure Active Directory 관리자가 Azure Multi-Factor Authentication를 등록 하 고 사용 하도록 요구 하 여 더 강력한 계정 확인을 얻을 수 있습니다.

관리자 용 MFA 필요 (미리 보기)는 다음 디렉터리 역할에 대해 MFA (multi-factor authentication)를 요구 하는 기준 정책으로, 가장 권한 있는 Azure AD 역할로 간주 됩니다.

* 전역 관리자
* SharePoint 관리자
* Exchange 관리자
* 조건부 액세스 관리자
* 보안 관리자
* 기술 지원팀 관리자/암호 관리자
* 대금 청구 관리자
* 사용자 관리자

조직에서 스크립트 또는 코드에 이러한 계정을 사용 중인 경우 이를 [관리 ID](../managed-identities-azure-resources/overview.md)로 바꾸는 것이 좋습니다.

### <a name="end-user-protection-preview"></a>최종 사용자 보호 (미리 보기)

권한이 높은 관리자는 공격 대상이 되는 유일한 관리자가 아닙니다. 잘못 된 행위자는 일반 사용자를 대상으로 하는 경향이 있습니다. 액세스 권한을 획득 한 후 이러한 잘못 된 행위자는 원래 계정 소유자를 대신 하 여 권한 있는 정보에 대 한 액세스를 요청 하거나 전체 디렉터리를 다운로드 하 고 전체 조직에서 피싱 공격을 수행할 수 있습니다. 모든 사용자에 대 한 보호를 개선 하는 일반적인 방법 중 하나는 위험한 로그인이 감지 될 때 더 강력한 형태의 계정 확인을 요구 하는 것입니다.

**최종 사용자 보호 (미리 보기)** 는 디렉터리의 모든 사용자를 보호 하는 기준 정책입니다. 이 정책을 사용 하도록 설정 하려면 모든 사용자가 14 일 이내에 Azure Multi-Factor Authentication에 등록 해야 합니다. 등록된 사용자에게는 위험한 로그인 시도 중에만 MFA를 묻는 메시지가 표시됩니다. 손상된 사용자 계정은 암호를 다시 설정하고 위험을 해제할 때까지 차단됩니다. 

> [!NOTE]
> 이전에 위험 플래그가 지정 된 사용자는 암호 재설정 및 정책 활성화 시 해제 위험에 도달할 때까지 차단 됩니다.

### <a name="block-legacy-authentication-preview"></a>레거시 인증 차단 (미리 보기)

레거시 인증 프로토콜 (예: IMAP, SMTP, POP3)은 이전 메일 클라이언트가 인증 하는 데 일반적으로 사용 되는 프로토콜입니다. 레거시 프로토콜은 multi-factor authentication을 지원 하지 않습니다. 디렉터리에 대해 multi-factor authentication을 요구 하는 정책이 있는 경우에도 잘못 된 행위자는 이러한 레거시 프로토콜 중 하나를 사용 하 여 인증 하 고 multi-factor authentication을 우회할 수 있습니다.

레거시 프로토콜에서 수행 하는 악의적인 인증 요청 으로부터 계정을 보호 하는 가장 좋은 방법은 차단 하는 것입니다.

**레거시 인증 (미리 보기) 차단** 기준 정책은 레거시 프로토콜을 사용 하 여 만든 인증 요청을 차단 합니다. 최신 인증을 사용 하 여 모든 사용자에 게 성공적으로 로그인 해야 합니다. 다른 기준 정책과 함께 사용 하는 경우 레거시 프로토콜에서 오는 요청이 차단 됩니다. 또한 모든 사용자는 필요할 때마다 MFA를 수행 해야 합니다. 이 정책은 Exchange ActiveSync를 차단 하지 않습니다.

### <a name="require-mfa-for-service-management-preview"></a>서비스 관리를 위해 MFA 필요 (미리 보기)

조직에서는 다양 한 Azure 서비스를 사용 하 고 다음과 같은 Azure Resource Manager 기반 도구에서 관리 합니다.

* Azure Portal
* Azure PowerShell
* Azure CLI

이러한 도구 중 하나를 사용 하 여 리소스 관리를 수행 하는 작업은 매우 특권 수준의 작업입니다. 이러한 도구는 서비스 설정 및 구독 청구와 같은 구독 전체의 구성을 변경할 수 있습니다.

권한 있는 작업을 보호 하려면 **서비스 관리 (미리 보기)에 대 한 MFA** 를 사용 하려면 Azure Portal, Azure PowerShell 또는 Azure CLI에 액세스 하는 모든 사용자에 대해 다단계 인증이 필요 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

* [보안 기본값 사용](../fundamentals/concept-fundamentals-security-defaults.md)
* [일반적인 조건부 액세스 정책](concept-conditional-access-policy-common.md)
* [ID 인프라를 보호하기 위한 5단계](../../security/fundamentals/steps-secure-identity.md)
