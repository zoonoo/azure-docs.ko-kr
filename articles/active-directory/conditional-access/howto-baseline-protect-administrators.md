---
title: 기본 정책 관리자-Azure Active Directory에 대 한 MFA 필요
description: 관리자 용 multi-factor authentication을 요구 하도록 조건부 액세스 정책
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
ms.openlocfilehash: 4474283b9a233e39497cd05f0f04ea0984f02401
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560946"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>기본 정책: 관리자 (미리 보기)에 대해 MFA 요구

권한 있는 계정에 대한 액세스 권한이 있는 사용자는 사용자 환경을 무제한 액세스할 수 있습니다. 이러한 계정은 권한이 크기 때문에 특별히 주의해서 처리해야 합니다. 권한 있는 계정의 보호를 향상하는 한 가지 일반적인 방법은 로그인에 사용할 때 보다 강력한 형태의 계정 확인을 요구하는 것입니다. Azure Active Directory에서 MFA(다단계 인증)를 요구하면 보다 강력한 계정 확인이 가능합니다.

**관리자 (미리 보기)에 대 한 MFA를 요구할** 되는 [기본 정책](concept-baseline-protection.md) 다음 권한 있는 관리자 역할 중 하나에 로그인 할 때마다 MFA를 요구 하는:

* 전역 관리자
* SharePoint 관리자
* Exchange 관리자
* 조건부 액세스 관리자
* 보안 관리자
* 기술 지원팀 관리자 / 암호 관리자
* 대금 청구 관리자
* 사용자 관리자

관리자 정책에 대 한 필요한 MFA를 사용 하도록 설정 하면, 위의 9 관리자 역할에 Authenticator 앱을 사용 하 여 MFA에 등록 해야 합니다. MFA 등록 완료 되 면 관리자에 로그인 할 때마다 MFA를 수행 해야 합니다.

## <a name="deployment-considerations"></a>배포 고려 사항

때문에 합니다 **관리자 (미리 보기)에 대 한 MFA를 요구** 중요 한 모든 관리자에 게 정책 적용, 원활한 배포에 대 한 몇 가지 고려 사항이 필요 합니다. 이러한 고려 사항은 사용자 및 응용 프로그램 및 최신 인증을 지원 하지 않는 조직에서 사용 하는 클라이언트와 함께 MFA를 수행 하지 않아야 하거나 없는 Azure AD에서 서비스 주체를 식별을 포함 합니다.

### <a name="legacy-protocols"></a>레거시 프로토콜

레거시 인증 프로토콜 (예: IMAP, SMTP, POP3, 등)는 인증 요청을 메일 클라이언트에서 사용 됩니다. 이러한 프로토콜에서 MFA를 지원 하지 않습니다. 대부분의 Microsoft에서 표시 되는 계정 손상은 MFA를 바이패스 하는 동안 레거시 프로토콜에 대 한 공격을 수행 하는 잘못 된 행위자가 발생 합니다. 관리자 계정에 로그인 할 때 MFA가 필요한 및 믿지 못할 자 MFA를 바이패스 하 수 없는 되도록이 정책에서 레거시 프로토콜 관리자 계정에 대 한 모든 인증 요청을 차단 합니다.

> [!WARNING]
> 이 정책은 사용 하기 전에 관리자에 게 레거시 인증 프로토콜을 사용 하지 않는 있는지 확인 합니다. 문서를 참조 [방법: Azure ad 조건부 액세스를 사용 하 여 레거시 인증 블록](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) 자세한 내용은 합니다.

## <a name="enable-the-baseline-policy"></a>기준 정책을 사용 하도록 설정

정책 **기준 정책: 관리자 (미리 보기)에 대 한 MFA를 요구할** 미리 구성 되어 및 Azure portal에서 조건부 액세스 블레이드로 이동 하면 위쪽에 표시 됩니다.

이 정책을 사용 하도록 설정 및 관리자에 게 보호 합니다.

1. 에 로그인 합니다 **Azure portal** 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자입니다.
1. 이동할 **Azure Active Directory** > **조건부 액세스**합니다.
1. 정책 목록에서 선택 **기준 정책: 관리자 (미리 보기)에 대 한 MFA를 요구할**합니다.
1. 설정할 **정책을 사용 하도록 설정** 하 **즉시 정책을 사용 하 여**입니다.
1. 클릭 **저장할**합니다.

> [!WARNING]
> 옵션이 **자동으로 정책을 사용 하도록 나중에 설정** 경우이 정책은 미리 보기로 제공 됩니다. 급격 한 사용자에 게 영향을 최소화 하려면이 옵션을 제거 했습니다. 사용할 때이 옵션을 선택한 경우 **정책을 사용 하지 않는** 자동으로 선택 됩니다. 이 기준 정책을 사용 하는 경우 사용 하도록 설정 하려면 위의 단계를 참조 하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

* [조건부 액세스 기준 보호 정책](concept-baseline-protection.md)
* [ID 인프라를 보호하기 위한 5단계](../../security/azure-ad-secure-steps.md)
* [Azure Active Directory의 조건부 액세스는 무엇입니까?](overview.md)
