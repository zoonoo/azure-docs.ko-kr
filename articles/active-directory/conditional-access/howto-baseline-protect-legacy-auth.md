---
title: 기본 정책 블록 레거시 인증 (미리 보기)-Azure Active Directory
description: 블록 레거시 인증 프로토콜에 대 한 조건부 액세스 정책
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
ms.openlocfilehash: e7eebc68ae8a55d636f3bc85e179bd7d6813be8d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235547"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>기본 정책: 블록 레거시 인증 (미리 보기)

사용자가 클라우드 앱에 쉽게 액세스할 수 있도록 Azure AD(Active Directory)에서 레거시 인증을 포함한 다양한 인증 프로토콜을 지원합니다. 레거시 인증은 수행한 인증 요청을 나타내는 용어:

* 최신 인증 (예: Office 2010 클라이언트)을 사용 하지 않는 이전 Office 클라이언트
* IMAP/SMTP/POP3 등의 레거시 메일 프로토콜을 사용 하는 모든 클라이언트

오늘날 대부분 모든 그대로 유지 하면서 로그인 시도의 레거시 인증에서 제공 됩니다. 레거시 인증 (MFA) multi-factor authentication을 지원 하지 않습니다. 디렉터리에서 사용 하도록 설정 하는 MFA 정책에 있는 경우에 악의적 행위자가 레거시 프로토콜을 사용 하 여 인증 하 고 MFA를 무시할 수 있습니다.

계정을 보호 하기 위해 레거시 프로토콜에서 악의적인 인증 요청에서 모두 함께 이러한 시도 차단 하는 가장 좋은 방법은 합니다. 레거시 프로토콜에서 만든 모든 로그인 요청을 차단 하기 쉽게 이러한 작업을 수행 하는 기준 정책을 만들었습니다.

![조건부 액세스를 사용 하 여 레거시 인증 블록](./media/howto-baseline-protect-legacy-auth/baseline-policy-block-legacy-authentication.png)

**블록 레거시 인증** 됩니다 [기준 정책](concept-baseline-protection.md) 레거시 프로토콜의 모든 인증 요청을 차단 하는 합니다. 최신 인증을 성공적으로 모든 사용자에 대 한 로그인에 사용 되어야 합니다. 다른 기준 정책과 함께에서 사용 되는, 레거시 프로토콜에서 들어오는 모든 요청이 차단 되 고 모든 사용자가 필요할 때마다 MFA를 해야 합니다. 이 정책은 Exchange ActiveSync를 차단 하지 않습니다.

## <a name="identify-legacy-authentication-use"></a>레거시 인증 사용을 식별

디렉터리에서 레거시 인증을 차단할 수 있습니다, 전에 먼저 사용자가 앱 레거시 인증 및 전반적 디렉토리에 미치는 영향을 사용 하는 경우를 이해 해야 합니다. 레거시 인증을 사용 하는 경우를 이해 하려면 azure AD 로그인 로그를 사용할 수 있습니다.

1. 로 이동 합니다 **Azure portal** > **Azure Active Directory** > **로그인**합니다.
1. 클릭 하 여 표시 되지 않으면 클라이언트 앱 열을 추가 **열** > **클라이언트 앱**합니다.
1. 기준으로 필터링 **클라이언트 앱** > **기타 클라이언트** 누릅니다 **적용**합니다.

로그인 하면 표시 하는 시도 필터링을 레거시 인증 프로토콜에서 했습니다. 각 개별 로그인 시도에서 클릭 하면 추가 세부 정보 표시 됩니다. 합니다 **클라이언트 앱** 아래에 있는 필드를 **기본 정보** 탭 사용 된 레거시 인증 프로토콜을 표시 합니다.

이러한 로그는 응용 프로그램 인증 요청을 레거시 프로토콜을 사용 하 고 있는 사용자는 레거시 인증에 따라 계속 표시 됩니다. 이러한 로그에 표시 되지 않으며 레거시 인증 사용 하지 않는으로 확인 되는 사용자에 대 한 조건부 액세스 정책을 구현 또는 사용 하도록 설정 합니다 **기준 정책: 블록 레거시 인증** 만 이러한 사용자에 대 한 합니다.

## <a name="moving-away-from-legacy-authentication"></a>레거시 인증 밖으로 이동

디렉터리에서 레거시 인증을 사용 하는 사람 및 응용 프로그램에 종속 좋습니다를 만든 후 다음 단계를 사용자가 최신 인증을 사용 하려면 업그레이드 합니다. 최신 인증을 사용 하면 보다 안전한 사용자 인증 및 권한 부여를 제공 하는 id 관리의 메서드입니다. MFA 정책이 준비에서 디렉터리에 있는 경우는 메시지가 표시 됩니다 필요할 때 MFA에 대 한 최신 인증 확인 합니다. 것이 레거시 인증 프로토콜에 대 한 보다 안전한 대체 합니다.

이 섹션에서는 최신 인증 환경을 업데이트 하는 방법에 대 한 단계별 개요를 제공 합니다. 조직에서 정책을 차단 하는 레거시 인증을 사용 하도록 설정 하기 전에 다음 단계를 자세히 읽습니다.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>1단계: 디렉터리에서 최신 인증을 사용 하도록 설정

최신 인증을 사용 하도록 설정 하면 첫 번째 단계는 디렉터리에 최신 인증을 지원 하는지 확인할 것입니다. 2017 년 8 월 1 일 이후에 생성 된 디렉터리에 대해 기본적으로 최신 인증이 사용 됩니다. 디렉터리를 만든 경우이 날짜 전에 수동으로 다음 단계를 사용 하 여 디렉터리에 대 한 최신 인증을 사용 하도록 설정 해야 합니다.

1. 디렉터리를 실행 하 여 이미 최신 인증을 지원 하는 경우 참조 확인 `Get-CsOAuthConfiguration` 에서 합니다 [Business Online PowerShell 모듈에 대 한 Skype](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)합니다.
1. 명령에는 빈 반환 하는 경우 `OAuthServers` 속성을 최신 인증을 사용할 수 없습니다. 최신 인증을 사용 하도록 설정 하려면 설정을 업데이트 `Set-CsOAuthConfiguration`합니다. 경우에 `OAuthServers` 항목을 포함 하는 속성, 라면 계속 진행 하세요.

진행 하기 전에이 단계를 완료 해야 합니다. 모든 Office 클라이언트에서 사용할 프로토콜을 지정 하기 때문에 디렉터리 구성을 먼저 변경 되는 중요 한 것입니다. 최신 인증을 지 원하는 Office 클라이언트를 사용 하는 경우에 디렉터리에서 최신 인증을 비활성화 하는 경우 레거시 프로토콜을 사용 하 여 기본값이 있습니다.

### <a name="step-2-office-applications"></a>2단계: Office 응용 프로그램

디렉터리에서 최신 인증을 설정한 후 Office 클라이언트에 대 한 최신 인증을 사용 하 여 응용 프로그램 업데이트를 시작할 수 있습니다. Office 2016 또는 이후 클라이언트는 기본적으로 최신 인증을 지원 합니다. 추가 단계가 필요 하지 않습니다.

Office 2013 Windows 클라이언트를 사용 하는 경우 또는 이전 버전에서 Office 2016 이상 업그레이드 하는 것이 좋습니다. 디렉터리에서 최신 인증을 사용 하도록 설정 하는 이전 단계를 완료 한 후에 이전 Office 응용 프로그램은 레거시 인증 프로토콜을 사용 하 여 계속 합니다. Office 2013 클라이언트를 사용 하는 즉시 이상 Office 2016으로 업그레이드할 수 없는 경우 다음 문서를의 단계를 따릅니다 [Windows 장치에서 Office 2013 최신 인증을 사용 하도록 설정](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)합니다. 레거시 인증을 사용 하는 동안 계정을 보호 하기 위해, 디렉터리 간에 강력한 암호를 사용 하는 것이 좋습니다. 체크 아웃 [Azure AD 암호 보호](../authentication/concept-password-ban-bad.md) 을 디렉터리에서 취약 한 암호를 금지 합니다.

Office 2010에서 최신 인증을 지원 하지 않습니다. 모든 사용자가 Office 2010을 사용 하 여 Office의 최신 버전으로 업그레이드 해야 합니다. 기본적으로 레거시 인증 차단 하므로 Office 2016 이상으로 업그레이드 하는 것이 좋습니다.

MacOS를 사용 하는 경우에 Office for Mac 2016 이상으로 업그레이드 하는 것이 좋습니다. 네이티브 메일 클라이언트를 사용 하는 경우에 MacOS 버전 10.14 또는 나중에 모든 장치 보유 해야 합니다.

### <a name="step-3-exchange-and-sharepoint"></a>3단계: Exchange 및 SharePoint

최신 인증을 사용 하도록 Windows 기반 Outlook 클라이언트, Exchange Online 여야 최신 인증도 사용 합니다. 최신 인증에 대 한 비활성화 된 경우 Exchange Online, Windows 기반 Outlook 클라이언트 최신 인증 (Outlook 2013 이상) 기본 인증을 사용 하 여 Exchange Online 사서함에 연결 하는 지원 합니다.

SharePoint Online 최신 인증 기본값에 사용 됩니다. 2017 년 8 월 1 일 이후 생성 된 디렉터리에 대 한 최신 인증이 Exchange Online에서 기본적으로 사용 됩니다. 그러나 이전에 최신 인증을 비활성화 했습니다.이 날짜 이전에 만들어진 디렉터리를 사용 하 고, 단계를 수행 하려면 다음 문서의 [Exchange Online에서 최신 인증 사용](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)합니다.

### <a name="step-4-skype-for-business"></a>4단계: 비즈니스용 Skype

비즈니스용 Skype에서 레거시 인증 요청을 방지 하려면 비즈니스 온라인 용 Skype에 대 한 최신 인증을 사용 하도록 설정 하는 데 필요한 됩니다. 2017 년 8 월 1 일 이후 생성 된 디렉터리에 대 한 비즈니스용 Skype에 대 한 최신 인증이 기본적으로 사용 됩니다.

비즈니스용 Skype에 최신 인증을 사용 하려면 기본적으로 최신 인증을 지 원하는 Microsoft Teams 전환한을 것이 좋습니다. 그러나 없는 경우 tr 할 지금은, Skype 비즈니스 클라이언트에 대 한 최신 인증을 사용 하 여 시작 되도록 비즈니스 온라인 용 Skype에 대 한 최신 인증을 사용 하도록 설정 해야 합니다. 문서의 다음이 단계를 수행 [최신 인증을 사용 하 여 지원 되는 비즈니스 기술에 대 한 Skype](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported), 비즈니스용 Skype에 대 한 최신 인증을 사용 하도록 설정 하는 단계입니다.

비즈니스 온라인 용 Skype에 대 한 최신 인증을 사용 하도록 설정 하는 것 외에도 최신 권장 비즈니스용 Skype에 대 한 최신 인증을 사용 하도록 설정할 때 Exchange Online에 대 한 인증을 사용 합니다. 이 프로세스는 온라인 Exchange Online 및 비즈니스용 Skype에 최신 인증의 상태를 동기화 하는 데 도움이 됩니다 하 고 비즈니스 클라이언트에 대 한 Skype에 대 한 여러 로그인 프롬프트 방지 됩니다.

### <a name="step-5-using-mobile-devices"></a>5단계: 모바일 장치를 사용 하 여

모바일 장치에서 응용 프로그램은 레거시 인증도 차단 해야 합니다. Outlook mobile을 사용 하는 것이 좋습니다. Outlook Mobile 기본적으로 최신 인증을 지원 하 고 다른 MFA 기본 보호 정책은 충족 됩니다.

네이티브 iOS 메일 클라이언트를 사용 하려면 메일 클라이언트는 레거시 인증을 차단 하도록 업데이트 되었습니다 되도록 11.0 이상 iOS 버전을 실행 해야 합니다.

### <a name="step-6-on-premises-clients"></a>6단계: 온-프레미스 클라이언트

비즈니스 온-프레미스 Exchange Server 온-프레미스 및 Skype를 사용 하 여 하이브리드 고객 인 경우 두 서비스 모두 최신 인증을 사용 하도록 업데이트 해야 합니다. 하이브리드 환경에서 최신 인증을 사용 하면 여전히 사용자가 온-프레미스를 인증 하는 있습니다. 리소스 (파일 또는 전자 메일) 변경 내용에 대 한 액세스 권한을 부여 하는 사례입니다.

최신 인증 온-프레미스를 사용 하도록 설정 하려면, 먼저 수 theIf 충족 하는지 요건, 준비가 이제 온-프레미스 최신 인증을 사용 하도록 설정 합니다.

최신 인증을 사용 하도록 설정 하는 단계는 다음 문서에서 찾을 수 있습니다:

* [Exchange Server 온-프레미스 하이브리드 최신 인증을 사용 하도록 구성 하는 방법](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [비즈니스용 Skype를 사용 하 여 최신 인증 (ADAL)를 사용 하는 방법](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>기준 정책을 사용 하도록 설정

정책 **기준 정책: 블록 레거시 인증 (미리 보기)** 미리 구성 되어 및 Azure portal에서 조건부 액세스 블레이드로 이동 하면 위쪽에 표시 됩니다.

에이 정책을 사용 하도록 설정 하 고 조직을 보호 합니다.

1. 에 로그인 합니다 **Azure portal** 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자입니다.
1. 이동할 **Azure Active Directory** > **조건부 액세스**합니다.
1. 정책 목록에서 선택 **기준 정책: 블록 레거시 인증 (미리 보기)** 합니다.
1. 설정할 **정책을 사용 하도록 설정** 하 **즉시 정책을 사용 하 여**입니다.
1. 클릭 하 여 모든 사용자 제외를 추가할 **사용자** > **제외 된 사용자 선택** 제외할 수 해야 하는 사용자를 선택 합니다. 클릭 **선택** 한 다음 **수행**합니다.
1. 클릭 **저장할**합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

* [조건부 액세스 기준 보호 정책](concept-baseline-protection.md)
* [ID 인프라를 보호하기 위한 5단계](../../security/azure-ad-secure-steps.md)
* [Azure Active Directory의 조건부 액세스란?](overview.md)
