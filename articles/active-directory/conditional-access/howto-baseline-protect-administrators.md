---
title: 기본 정책에는 관리자 용 MFA 필요-Azure Active Directory
description: 관리자에 대해 multi-factor authentication을 요구 하는 조건부 액세스 정책
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 213540a5b6c77146155365133f2cca08eea25351
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608168"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>기준 정책: 관리자 용 MFA 필요 (미리 보기)

권한 있는 계정에 대한 액세스 권한이 있는 사용자는 사용자 환경을 무제한 액세스할 수 있습니다. 이러한 계정은 권한이 크기 때문에 특별히 주의해서 처리해야 합니다. 권한 있는 계정의 보호를 향상하는 한 가지 일반적인 방법은 로그인에 사용할 때 보다 강력한 형태의 계정 확인을 요구하는 것입니다. Azure Active Directory에서 MFA(다단계 인증)를 요구하면 보다 강력한 계정 확인이 가능합니다.

**관리자 (미리 보기)**  에 대 한 mfa 요구는 다음 권한 있는 관리자 역할 중 하나가 로그인 할 때마다 mfa를 요구 하는 [기준 정책](concept-baseline-protection.md) 입니다.

* 전역 관리자
* SharePoint administrator
* Exchange 관리자
* 조건부 액세스 관리자
* 보안 관리자
* 기술 지원팀 관리자/암호 관리자
* 대금 청구 관리자
* 사용자 관리자

관리자에 대해 MFA 필요 정책을 사용 하도록 설정 하면 위의 9 개 관리자 역할이 인증 앱을 사용 하 여 MFA에 등록 해야 합니다. MFA 등록이 완료 되 면 관리자는 로그인 할 때마다 MFA를 수행 해야 합니다.

## <a name="deployment-considerations"></a>배포 고려 사항

**관리자에 대해 MFA 필요 (미리 보기)** 정책은 모든 중요 한 관리자에 게 적용 되므로 원활한 배포를 위해 여러 가지 사항을 고려해 야 합니다. 이러한 고려 사항에는 MFA를 수행할 수 없는 Azure AD의 사용자 및 서비스 원칙과 최신 인증을 지원 하지 않는 조직에서 사용 하는 응용 프로그램 및 클라이언트를 식별 하는 작업이 포함 됩니다.

### <a name="legacy-protocols"></a>레거시 프로토콜

메일 클라이언트가 인증 요청을 수행 하는 데 사용 되는 레거시 인증 프로토콜 (IMAP, SMTP, POP3 등)입니다. 이러한 프로토콜은 MFA를 지원 하지 않습니다. Microsoft에서 발생 하는 대부분의 계정 손상은 대부분 MFA를 바이패스 하는 레거시 프로토콜에 대 한 공격을 수행 하는 잘못 된 행위자에 의해 발생 합니다. 관리 계정에 로그인 할 때 MFA가 필요 하 고 잘못 된 행위자가 MFA를 무시할 수 없도록 하려면이 정책은 레거시 프로토콜의 관리자 계정에 대 한 모든 인증 요청을 차단 합니다.

> [!WARNING]
> 이 정책을 사용 하도록 설정 하기 전에 관리자가 레거시 인증 프로토콜을 사용 하 고 있지 않은지 확인 합니다. [자세한 내용은 다음을 참조 하세요. 자세한 내용은 조건부 액세스](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) 를 사용 하 여 Azure AD에 대 한 레거시 인증을 차단 합니다.

## <a name="enable-the-baseline-policy"></a>기준 정책 사용

정책 **기준 정책: 관리자 (미리 보기)** 에 대 한 MFA 필요는 미리 구성 되어 있으며 Azure Portal의 조건부 액세스 블레이드로 이동 하면 위쪽에 표시 됩니다.

이 정책을 사용 하도록 설정 하 고 관리자를 보호 하려면 다음을 수행 합니다.

1. 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory** > **조건부 액세스**로 이동 합니다.
1. 정책 목록에서 기준 정책을 선택 **합니다. 관리자 용 MFA (미리 보기)** 가 필요 합니다.
1. 정책 **사용을 즉시 정책**사용으로 설정 합니다.
1.  **저장**을 클릭 합니다.

> [!WARNING]
> 이 정책이 미리 보기로 제공 되 **는 경우 나중에 정책을 자동으로 사용 하도록 설정 하** 는 옵션이 있습니다. 갑작스러운 사용자에 게 미치는 영향을 최소화 하기 위해이 옵션을 제거 했습니다. 이 옵션을 사용할 수 있는 경우이 옵션을 선택 하면 지금은 **정책 사용 안 함** 이 자동으로 선택 됩니다. 이 기준 정책을 사용 하려는 경우 위의 단계를 참조 하 여 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

참조 항목:

* [조건부 액세스 기준 보호 정책](concept-baseline-protection.md)
* [ID 인프라를 보호하기 위한 5단계](../../security/fundamentals/steps-secure-identity.md)
* [Azure Active Directory의 조건부 액세스란?](overview.md)
