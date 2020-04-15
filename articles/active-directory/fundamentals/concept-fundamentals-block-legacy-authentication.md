---
title: Azure AD에서 레거시 인증 프로토콜 차단
description: 조직에서 레거시 인증 프로토콜을 차단하는 방법과 이유 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55ce0233fdefb8360376e94c0baafabe4c62ced7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309204"
---
# <a name="blocking-legacy-authentication"></a>레거시 인증 차단
 
사용자가 클라우드 앱에 쉽게 액세스할 수 있도록 Azure AD(Active Directory)에서 레거시 인증을 포함한 다양한 인증 프로토콜을 지원합니다. 레거시 인증은 다음이 만든 인증 요청을 참조하는 용어입니다.

- 최신 인증을 사용하지 않는 이전 Office 클라이언트(예: Office 2010 클라이언트)
- IMAP/SMTP/POP3와 같은 레거시 메일 프로토콜을 사용하는 모든 클라이언트

오늘날 대부분의 손상 방지 로그인 시도는 레거시 인증에서 비롯됩니다. 레거시 인증은 MFA(다단계 인증)를 지원하지 않습니다. 디렉터리에서 MFA 정책을 사용하도록 설정한 경우에도 악의적인 행위자도 레거시 프로토콜을 사용하여 인증하고 MFA를 우회할 수 있습니다. 레거시 프로토콜에 의한 악의적인 인증 요청으로부터 계정을 보호하는 가장 좋은 방법은 이러한 시도를 완전히 차단하는 것입니다.

## <a name="identify-legacy-authentication-use"></a>레거시 인증 사용 식별

디렉터리에서 레거시 인증을 차단하려면 먼저 사용자에게 레거시 인증을 사용하는 앱이 있는지, 그리고 레거시 인증을 사용하는 앱이 전체 디렉터리에 미치는 영향을 이해해야 합니다. Azure AD 로그인 로그를 사용하여 레거시 인증을 사용하는지 확인할 수 있습니다.

1.  **Azure 포털** > **Azure Active 디렉터리** > **로그인으로 이동합니다.**
1. 열 **클라이언트 응용** 프로그램을 클릭하여 표시되지 않는 경우 클라이언트 **앱 열을** **추가합니다.** >
1. 클라이언트 **앱별로**   필터링> 제시된 모든 **레거시 인증 클라이언트** 옵션을 확인합니다.
1. **상태** > **별 성공**으로 필터링 합니다. 
1. 날짜 필터를 사용하여 필요한 경우 **날짜** 범위를 확장합니다.

필터링은 선택한 레거시 인증 프로토콜에 의해 이루어진 성공적인 로그인 시도만 표시합니다. 각 개별 로그인 시도를 클릭하면 추가 세부 정보가 표시됩니다. 개별 데이터 행을 선택한 후 기본 정보 탭 아래의 클라이언트 앱 열 또는 클라이언트 앱 필드는 사용된 레거시 인증 프로토콜을 나타냅니다. 이러한 로그는 레거시 인증에 따라 남아 있는 사용자와 인증 요청을 하기 위해 레거시 프로토콜을 사용하는 응용 프로그램을 나타냅니다. 이러한 로그에 나타나지 않고 레거시 인증을 사용하지 않는 것으로 확인된 사용자의 경우 조건부 액세스 정책을 구현하거나 기준 정책(이러한 사용자에 대해서만 레거시 인증을 차단)을 사용하도록 설정합니다.

## <a name="moving-away-from-legacy-authentication"></a>레거시 인증에서 벗어나기 

디렉터리에서 레거시 인증을 사용하는 사용자와 해당 응용 프로그램에 종속되는 응용 프로그램을 더 잘 파악한 다음 다음 단계는 사용자가 최신 인증을 사용하도록 업그레이드하는 것입니다. 최신 인증은 보다 안전한 사용자 인증 및 권한 부여를 제공하는 ID 관리 방법입니다. 디렉터리에 MFA 정책이 있는 경우 최신 인증을 통해 필요한 경우 사용자에게 MFA 메시지가 표시됩니다. 레거시 인증 프로토콜에 대한 보다 안전한 대안입니다.

이 섹션에서는 환경을 최신 인증으로 업데이트하는 방법에 대한 단계별 개요를 제공합니다. 조직에서 레거시 인증 차단 정책을 활성화하기 전에 아래 단계를 읽어보십시오.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>1단계: 디렉터리에서 최신 인증 사용

최신 인증을 사용하도록 설정하는 첫 번째 단계는 디렉터리에서 최신 인증을 지원하는지 확인하는 것입니다. 2017년 8월 1일 또는 그 이후에 만든 디렉터리에 대해 최신 인증은 기본적으로 활성화됩니다. 이 날짜 이전에 디렉터리를 만든 경우 다음 단계를 사용하여 디렉터리에 대한 최신 인증을 수동으로 사용하도록 설정해야 합니다.

1. 비즈니스용 [Skype 온라인 PowerShell 모듈에서](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell) `Get-CsOAuthConfiguration` 실행하여 디렉터리에서 이미 최신 인증을 지원하는지 확인합니다.
1. 명령이 빈 `OAuthServers` 속성을 반환하는 경우 최신 인증이 비활성화됩니다. 을 사용하여 `Set-CsOAuthConfiguration`최신 인증을 사용하도록 설정을 업데이트합니다.  `OAuthServers`시설에 항목이 포함되어 있으면 이동이 좋습니다.

앞으로 나아가기 전에 이 단계를 완료해야 합니다. 디렉터리 구성은 모든 Office 클라이언트에서 사용할 프로토콜을 지정하기 때문에 먼저 변경되는 것이 중요합니다. 최신 인증을 지원하는 Office 클라이언트를 사용하는 경우에도 디렉터리에서 최신 인증을 사용하지 않는 경우 레거시 프로토콜을 기본적으로 사용합니다.

### <a name="step-2-office-applications"></a>2단계: 사무실 응용 프로그램

디렉터리에서 최신 인증을 사용하도록 설정한 후에는 Office 클라이언트에 대한 최신 인증을 사용하도록 설정하여 응용 프로그램 업데이트를 시작할 수 있습니다. Office 2016 이상 클라이언트는 기본적으로 최신 인증을 지원합니다. 추가 단계가 필요하지 않습니다.

Office 2013 Windows 클라이언트 를 사용하는 경우 Office 2016 이상으로 업그레이드하는 것이 좋습니다. 디렉터리에서 최신 인증을 사용하도록 설정하는 이전 단계를 완료한 후에도 이전 Office 응용 프로그램은 레거시 인증 프로토콜을 계속 사용합니다. Office 2013 클라이언트를 사용 하 고 Office 2016 이후 Office로 즉시 업그레이드할 수 없는 경우 다음 문서의 단계를 수행하여 [Windows 장치에서 Office 2013에 대한 최신 인증 을 사용하도록 설정합니다.](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication) 레거시 인증을 사용하는 동안 계정을 보호하려면 디렉터리 전체에서 강력한 암호를 사용하는 것이 좋습니다. Azure [AD 암호 보호를](../authentication/concept-password-ban-bad.md) 확인하여 디렉터리 전체에서 약한 암호를 금지합니다.

Office 2010은 최신 인증을 지원하지 않습니다. Office 2010을 사용하여 모든 사용자를 최신 버전의 Office로 업그레이드해야 합니다. 기본적으로 레거시 인증을 차단하므로 Office 2016 이상으로 업그레이드하는 것이 좋습니다.

macOS를 사용하는 경우 Mac 2016 이상Office로 업그레이드하는 것이 좋습니다. 네이티브 메일 클라이언트를 사용하는 경우 모든 장치에서 macOS 버전 10.14 이상이 있어야 합니다.

### <a name="step-3-exchange-and-sharepoint"></a>3단계: 교환 및 공유점

Windows 기반 Outlook 클라이언트가 최신 인증을 사용하려면 Exchange Online도 최신 인증을 사용하도록 설정해야 합니다. Exchange Online에 대해 최신 인증을 사용하지 않는 경우 최신 인증을 지원하는 Windows 기반 Outlook 클라이언트(Outlook 2013 이상)는 기본 인증을 사용하여 Exchange Online 사서함에 연결합니다.

SharePoint Online은 최신 인증 기본값에 사용할 수 있습니다. 2017년 8월 1일 이후에 만든 디렉터리에 대해 Exchange Online에서 기본적으로 최신 인증이 활성화됩니다. 그러나 이전에 최신 인증을 사용하지 않도록 설정했거나 이 날짜 이전에 만든 디렉터리를 사용하는 경우 다음 문서의 단계를 수행하여 [Exchange Online에서 최신 인증 을 사용하도록 설정합니다.](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)

### <a name="step-4-skype-for-business"></a>4 단계: 비즈니스를 위한 Skype

비즈니스용 Skype에서 수행한 레거시 인증 요청을 방지하려면 비즈니스용 Skype 온라인용 최신 인증을 사용하도록 설정해야 합니다. 2017년 8월 1일 이후에 만든 디렉터리에 대해 비즈니스용 Skype에 대한 최신 인증은 기본적으로 활성화되어 있습니다.

기본적으로 최신 인증을 지원하는 Microsoft Teams로 전환하는 것이 좋습니다. 그러나 현재 마이그레이션할 수 없는 경우 비즈니스용 Skype 클라이언트가 최신 인증을 사용하기 시작하도록 비즈니스용 Skype 온라인에 대한 최신 인증을 사용하도록 설정해야 합니다. 이 문서의 단계를 [따르십시오 비즈니스용 Skype 최신 인증으로 지원되는 비즈니스 용 Skype 토폴로지,](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)비즈니스용 Skype에 대한 최신 인증을 활성화합니다.

비즈니스용 Skype 온라인에 대한 최신 인증을 사용하도록 설정하는 것 외에도 비즈니스용 Skype에 대한 최신 인증을 사용하도록 설정하면 Exchange Online에 대한 최신 인증을 사용하도록 설정하는 것이 좋습니다. 이 프로세스는 Exchange Online 및 비즈니스용 Skype에서 최신 인증 상태를 동기화하는 데 도움이 되며 비즈니스용 Skype 클라이언트에 대한 여러 로그인 프롬프트를 방지합니다.

### <a name="step-5-using-mobile-devices"></a>5단계: 모바일 장치 사용

모바일 장치의 응용 프로그램도 레거시 인증을 차단해야 합니다. 모바일용 Outlook을 사용하는 것이 좋습니다. 모바일용 Outlook은 기본적으로 최신 인증을 지원하며 다른 MFA 기준 보호 정책을 충족합니다.

기본 iOS 메일 클라이언트를 사용하려면 메일 클라이언트가 레거시 인증을 차단하도록 업데이트되었는지 확인하기 위해 iOS 버전 11.0 이상을 실행해야 합니다.

### <a name="step-6-on-premises-clients"></a>6단계: 온-프레미스 클라이언트

Exchange Server 온-프레미스 및 비즈니스용 Skype온-프레미스를 사용하는 하이브리드 고객인 경우 최신 인증을 활성화하려면 두 서비스를 모두 업데이트해야 합니다. 하이브리드 환경에서 최신 인증을 사용하는 경우에도 온-프레미스에서 사용자를 인증하는 것입니다. 리소스(파일 또는 전자 메일)에 대한 액세스 권한을 부여한 스토리가 변경됩니다.

온-프레미스에서 최신 인증을 사용하도록 설정하기 전에 필수 구성 조건을 충족했는지 확인하십시오. 이제 온-프레미스에서 최신 인증을 사용할 준비가 되었습니다.

최신 인증을 활성화하는 단계는 다음 문서에서 찾을 수 있습니다.

* [하이브리드 최신 인증을 사용하도록 Exchange 서버 온-프레미스를 구성하는 방법](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [비즈니스용 Skype를 사용하여 최신 인증(ADAL)을 사용하는 방법](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>다음 단계

- [하이브리드 최신 인증을 사용하도록 Exchange 서버 온-프레미스를 구성하는 방법](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [비즈니스용 Skype를 사용하여 최신 인증(ADAL)을 사용하는 방법](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [레거시 인증 차단](../conditional-access/block-legacy-authentication.md)
