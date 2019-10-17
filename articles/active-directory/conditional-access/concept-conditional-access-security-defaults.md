---
title: 보안 기본값 Azure Active Directory
description: 일반적인 공격 으로부터 조직을 보호 하기 위해 설계 된 보안 기본 정책
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4921f0605f16a601f6e2ee9a15b71b50d253201
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453044"
---
# <a name="what-are-security-defaults"></a>보안 기본값 이란?

암호 스프레이, 재생 및 피싱 같은 일반적인 id 관련 공격이 점점 더 인기 있는 경우 보안 관리가 어려울 수 있습니다. 이러한 일반적인 공격 으로부터 조직을 더욱 안전 하 게 보호 하는 간단한 방법을 만드는 것은 AD (Azure Active Directory)에서 보안 기본값의 목표입니다. 보안 기본값은 안전 하 게 보호 하 고 일반적인 공격 으로부터 조직을 보호 하는 데 도움이 됩니다. 보안 기본값에는 이러한 일반적인 공격에 대해 미리 구성 된 보안 설정이 포함 됩니다. Microsoft는 모든 사용자에 게 보안 기본값을 사용할 수 있도록 합니다. 목표는 모든 조직에 추가 비용 없이 기본 수준의 보안이 설정 되도록 하는 것입니다.

![새 보안 기본값 UX의 스크린샷](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
다음 보안 구성은 테 넌 트에서 켜 집니다. 

## <a name="unified-mfa-registration"></a>통합 MFA 등록

테 넌 트의 모든 사용자는 MFA (Azure Multi-Factor Authentication)에 대 한 등록이 필요 합니다. 사용자는 Microsoft Authenticator 앱을 사용 하 여 Azure MFA에 등록 하는 데 14 일이 있습니다. 14 일이 지나면 사용자는 MFA 등록이 완료 될 때까지 로그인 할 수 없습니다.

일부 사용자는 office가 아닐 수도 있고, 보안 기본값을 사용 하도록 설정한 후 14 일 동안 로그인 하지 않을 수도 있다는 것을 이해 하 고 있습니다. 모든 사용자에 게 MFA를 등록 하는 데 충분 한 시간이 부여 되도록 하려면 각 사용자에 대해 14 일의 기간이 고유 합니다. 보안 기본값을 사용 하는 경우 처음으로 성공한 대화형 로그인 이후에 사용자의 14 일 기간이 시작 됩니다.

## <a name="mfa-enforcement"></a>MFA 적용

### <a name="protecting-administrators"></a>관리자 보호

권한 있는 계정에 대 한 액세스 권한이 있는 사용자는 사용자 환경에 대 한 액세스 권한을 늘립니다. 이러한 계정은 권한이 크기 때문에 특별히 주의해서 처리해야 합니다. 권한 있는 계정의 보호를 향상하는 한 가지 일반적인 방법은 로그인에 사용할 때 보다 강력한 형태의 계정 확인을 요구하는 것입니다. Azure Active Directory에서 multi-factor authentication을 요구 하 여 더 강력한 계정 확인을 얻을 수 있습니다.

MFA 등록을 완료 한 후에는 다음 9 개의 Azure AD 관리자 역할이 로그인 할 때마다 MFA를 수행 해야 합니다.

- 전역 관리자
- SharePoint 관리자
- Exchange 관리자
- 조건부 액세스 관리자
- 보안 관리자
- 기술 지원팀 관리자/암호 관리자
- 대금 청구 관리자
- 사용자 관리자
- 인증 관리자

### <a name="protecting-all-users"></a>모든 사용자 보호

관리자 계정도 MFA (multi-factor authentication)를 사용 하 여 보호 해야 하는 유일한 계정 이라고 생각 하는 경향이 있습니다. 관리자는 중요 한 정보에 대 한 광범위 한 액세스 권한을 가지 며 구독 전체 설정을 변경할 수 있습니다. 그러나 잘못 된 행위자는 최종 사용자를 대상으로 하는 경향이 있습니다. 액세스 권한을 획득 한 후 이러한 잘못 된 행위자는 원래 계정 소유자를 대신 하 여 권한 있는 정보에 대 한 액세스를 요청 하거나 전체 디렉터리를 다운로드 하 여 전체 조직에서 피싱 공격을 수행할 수 있습니다. 모든 사용자에 대 한 보호를 개선 하는 일반적인 방법 중 하나는 모든 사용자를 위한 MFA (multi-factor authentication)와 같은 보다 강력한 형태의 계정 확인을 요구 하는 것입니다. MFA 등록이 완료 되 면 필요할 때마다 사용자에 게 MFA를 요청 하는 메시지가 표시 됩니다.

### <a name="blocking-legacy-authentication"></a>레거시 인증 차단

사용자가 클라우드 앱에 쉽게 액세스할 수 있도록 Azure AD(Active Directory)에서 레거시 인증을 포함한 다양한 인증 프로토콜을 지원합니다. 레거시 인증은 다음의 인증 요청을 참조 하는 용어입니다.

- 최신 인증을 사용 하지 않는 이전 Office 클라이언트 (예: Office 2010 클라이언트)
- IMAP/SMTP/POP3와 같은 레거시 메일 프로토콜을 사용 하는 모든 클라이언트

오늘날 대부분의 손상 된 로그인 시도는 대부분 레거시 인증에서 제공 됩니다. 레거시 인증은 MFA (multi-factor authentication)를 지원 하지 않습니다. 디렉터리에서 MFA 정책을 사용 하도록 설정한 경우에도 잘못 된 행위자는 레거시 프로토콜을 사용 하 여 인증 하 고 MFA를 우회할 수 있습니다. 테 넌 트에서 보안 기본값을 사용 하도록 설정 하면 레거시 프로토콜을 통해 수행 된 모든 인증 요청이 차단 됩니다. 보안 기본값은 Exchange ActiveSync를 차단 하지 않습니다.

### <a name="protecting-privileged-actions"></a>권한 있는 작업 보호

조직에서는 다음을 포함 하 여 Azure Resource Manager API를 통해 관리 되는 다양 한 Azure 서비스를 사용 합니다.

- Azure Portal 
- Azure PowerShell 
- Azure CLI

Azure Resource Manager를 사용 하 여 서비스를 관리 하는 작업은 매우 특권 수준의 작업입니다. Azure Resource Manager은 서비스 설정 및 구독 청구와 같은 테 넌 트 전체 구성을 변경할 수 있습니다. 단일 요소 인증은 피싱 및 암호 스프레이와 같은 다양 한 공격에 취약 합니다. 따라서 액세스를 허용 하기 전에 multi-factor authentication을 요구 하 여 Azure Resource Manager 및 업데이트 구성에 액세스 하려는 사용자의 id를 확인 하는 것이 중요 합니다.

테 넌 트에서 보안 기본값을 사용 하도록 설정 하면 Azure Portal, Azure PowerShell 또는 Azure CLI에 액세스 하는 모든 사용자가 multi-factor authentication을 완료 해야 합니다. 이 정책은 관리자 또는 사용자 인지에 관계 없이 Azure Resource Manager에 액세스 하는 모든 사용자에 게 적용 됩니다. 사용자가 MFA에 등록 되지 않은 경우 사용자는 계속 하려면 Microsoft Authenticator 앱을 사용 하 여 등록 해야 합니다. 14 일 MFA 등록 기간이 제공 되지 않습니다.

## <a name="deployment-considerations"></a>배포 고려 사항

다음은 테 넌 트의 보안 기본값 배포와 관련 된 몇 가지 추가 고려 사항입니다.

### <a name="legacy-protocols"></a>레거시 프로토콜

메일 클라이언트가 인증 요청을 수행 하는 데 사용 되는 레거시 인증 프로토콜 (IMAP, SMTP, POP3 등)입니다. 이러한 프로토콜은 MFA를 지원 하지 않습니다. Microsoft에서 발생 하는 대부분의 계정 손상은 대부분 MFA를 바이패스 하는 레거시 프로토콜에 대 한 공격을 수행 하는 잘못 된 행위자에 의해 발생 합니다. 관리 계정에 로그인 할 때 MFA가 필요 하 고 잘못 된 행위자가 MFA를 무시할 수 없도록 하기 위해 보안 기본값은 레거시 프로토콜의 관리자 계정에 대 한 모든 인증 요청을 차단 합니다.

> [!WARNING]
> 이 설정을 사용 하도록 설정 하기 전에 관리자가 레거시 인증 프로토콜을 사용 하 고 있지 않은지 확인 합니다. 자세한 내용은 [레거시 인증에서 밖으로 이동 하는 방법](concept-conditional-access-block-legacy-authentication.md)문서를 참조 하세요.

### <a name="conditional-access"></a>조건부 액세스

조건부 액세스를 사용 하 여 보안 기본값에서 사용 하도록 설정 된 것과 동일한 동작을 제공 하는 정책을 구성할 수 있습니다. 조건부 액세스를 사용 하 고 사용자 환경에서 조건부 액세스 정책을 사용 하도록 설정한 경우에는 보안 기본값을 사용할 수 없습니다. 조건부 액세스를 제공 하는 라이선스가 있지만 사용자 환경에서 조건부 액세스 정책을 사용 하도록 설정 하지 않은 경우 CA 정책을 사용 하도록 설정할 때까지 보안 기본값 사용을 시작 합니다.

![보안 기본값 또는 조건부 액세스 모두](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

조건부 액세스를 사용 하 여 동등한 정책을 구성 하는 방법에 대 한 단계별 가이드는 다음과 같습니다.

- [관리자 용 MFA 필요](howto-conditional-access-policy-admin-mfa.md)
- [Azure 관리를 위한 MFA 필요](howto-conditional-access-policy-azure-management.md)
- [레거시 인증 차단](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>보안 기본값 사용

디렉터리에서 보안 기본값을 사용 하도록 설정 하려면:

1. 보안 관리자, 조건부 액세스 관리자 또는 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
1.  **Azure Active Directory**  @ no__t-2 **속성** 으로 이동 합니다.
1. **보안 기본값 관리** 를 선택 합니다.
1. **보안 기본값 사용** 설정/해제를 **예**로 설정 합니다.
1. 저장을 클릭 합니다.

## <a name="next-steps"></a>다음 단계

[일반적인 조건부 액세스 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 란?](overview.md)
