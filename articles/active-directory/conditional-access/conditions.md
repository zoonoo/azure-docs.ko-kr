---
title: Azure Active Directory 조건부 액세스의 조건이란? | Microsoft Docs
description: Azure Active Directory 조건부 액세스에서 조건을 사용하여 정책을 트리거하는 방법을 알아봅니다.
services: active-directory
keywords: 앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2018
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0245fa060f22b2f59a037af5323397409a59da69
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165937"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory 조건부 액세스의 조건이란? 

[Azure AD(Azure Active Directory) 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)를 사용하여 사용자가 클라우드 앱에 액세스하는 방법을 제어할 수 있습니다. 조건부 액세스 정책에서 정책을 트리거하는 이유("When this happens")에 대한 응답("Then do this")을 정의합니다. 

![이유 및 응답](./media/conditions/10.png)


조건부 액세스의 컨텍스트에서 **When this happens(이 경우)** 를 **조건**이라 합니다. **When this happens(이 경우)** 를 **액세스 제어**라 합니다. 조건과 액세스 제어의 조합이 조건부 액세스 정책을 나타냅니다.

![조건부 액세스 정책](./media/conditions/61.png)


조건부 액세스 정책에 구성하지 않은 조건은 적용되지 않습니다. 일부 조건은 환경에 조건부 액세스 정책을 적용하는 데 [필수적](best-practices.md)입니다. 

이 문서에서는 조건의 개요 및 조건부 액세스 정책에서 조건을 사용하는 방법을 설명합니다. 

## <a name="users-and-groups"></a>개요

사용자 및 그룹 조건은 조건부 액세스 정책에서 필수입니다. 정책에서 **모든 사용자**를 선택할 수도 있고 특정 사용자 및 그룹을 선택할 수도 있습니다.

![개요](./media/conditions/111.png)

**모든 사용자**를 선택하면 게스트 사용자를 포함하여 디렉터리에 있는 모든 사용자에게 정책이 적용됩니다.

**사용자 및 그룹**을 선택하면 다음 옵션을 설정할 수 있습니다.

* **모든 게스트 사용자**는 정책의 대상을 B2B 게스트 사용자로 합니다. 이 조건은 **userType** 특성이 **guest**로 설정된 모든 사용자 계정과 일치합니다. Azure AD의 초대 흐름에 계정이 생성되는 즉시 정책을 적용해야 하는 경우 이 설정을 사용합니다.

* **디렉터리 역할**은 사용자의 역할 할당을 기반으로 정책을 대상으로 합니다. 이 조건은 **전역 관리자** 또는 **암호 관리자**와 같은 디렉터리 역할을 지원합니다.

* **사용자 및 그룹**은 특정 사용자 집합을 대상으로 합니다. 예를 들어 클라우드 앱으로 선택된 HR 앱의 경우 HR 부서의 모든 구성원을 포함하는 그룹을 선택할 수 있습니다. 그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다.

정책에서 특정 사용자 또는 그룹을 제외할 수도 있습니다. 정책에서 다단계 인증(MFA)을 적용하는 경우의 일반적인 사용 사례 중 하나는 서비스 계정입니다. 

새 정책을 배포할 때에는 특정 사용자 집합을 대상으로 지정하는 것이 유리합니다. 새 정책에서 정책 동작의 유효성을 검사하기 위해 초기 사용자 집합만 대상으로 지정해야 합니다. 



## <a name="cloud-apps"></a>클라우드 앱 

클라우드 앱은 웹 사이트 또는 서비스입니다. Azure AD 애플리케이션 프록시로 보호되는 웹 사이트는 클라우드 앱이기도 합니다. 지원되는 클라우드 앱에 대한 자세한 설명은 [클라우드 앱 할당](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments)을 참조하세요. 

**클라우드 앱** 조건은 조건부 액세스 정책에서 필수입니다. 정책에서 **모든 클라우드 앱**을 선택할 수도 있고 특정 앱을 선택할 수도 있습니다.

![클라우드 앱 포함](./media/conditions/03.png)

선택:

- 조직 전체에 기준 정책을 적용하려면 **모든 클라우드 앱**을 선택합니다. 클라우드 앱에 대한 로그인 위험이 감지되면 다단계 인증을 요구하는 정책에 이 선택을 사용합니다. **모든 클라우드 앱**에 적용된 정책은 모든 웹 사이트 및 서비스에 대한 액세스에 적용됩니다. 이 설정은 **앱 선택** 목록에 표시되는 클라우드 앱으로 제한되지 않습니다. 

- 정책에 의해 특정 서비스를 대상으로 지정하려면 **앱 선택**을 선택합니다. 예를 들어 사용자가 SharePoint Online에 액세스하려면 [규격 디바이스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online)를 사용하도록 요구할 수 있습니다. 이 정책은 사용자가 SharePoint 콘텐츠 팀에 액세스할 때 다른 서비스에도 적용됩니다. Microsoft Teams를 예로 들 수 있습니다. 

특정 앱을 정책에서 제외할 수 있습니다. 그러나 사용자가 액세스하는 서비스에 적용되는 정책이 계속 적용됩니다. 



## <a name="sign-in-risk"></a>로그인 위험

로그인 위험은 로그인을 사용자 계정의 합법적인 소유자가 수행하지 않았을 가능성의 지표(높음, 중간 또는 낮음)입니다. Azure AD는 사용자 로그인 중에 로그인 위험 수준을 계산합니다. 계산된 로그인 위험 수준을 조건부 액세스 정책의 조건으로 사용할 수 있습니다.

![로그인 위험 수준](./media/conditions/22.png)

이 조건을 사용하려면 [Azure Active Directory Id 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable)를 사용하도록 설정해야 합니다.
 
이 조건의 일반적인 사용 사례는 다음 보호가 적용된 정책입니다. 

- 로그인 위험이 높은 사용자를 차단합니다. 이 보호는 비합적일 수 있는 사용자가 클라우드 앱에 액세스하지 못하게 차단합니다. 
- 로그인 위험 수준이 보통인 사용자에게 다단계 인증을 요구합니다. 다단계 인증을 사용하면 로그인이 합법적 계정 소유자에 의해 수행된다는 추가 신뢰를 제공할 수 있습니다.

자세한 내용은 [세션 위험이 감지되면 액세스 차단](app-sign-in-risk.md)을 참조하세요.  

## <a name="device-platforms"></a>디바이스 플랫폼

디바이스 플랫폼은 다음 디바이스에서 실행되는 운영 체제를 특징으로 합니다. Azure AD는 디바이스에서 제공하는 정보(예: 사용자 에이전트)를 사용하여 플랫폼을 식별합니다. 이 정보는 확인되지 않습니다. 모든 플랫폼에 정책을 적용하는 것이 좋습니다. 정책은 액세스를 차단하거나, Microsoft Intune 정책 준수를 요구하거나, 디바이스 도메인 가입을 요구해야 합니다. 기본값은 모든 디바이스 플랫폼에 정책을 적용하는 것입니다. 


![디바이스 옵션 구성](./media/conditions/24.png)

지원되는 디바이스 플랫폼 목록은 [디바이스 플랫폼 조건](technical-reference.md#device-platform-condition)을 참조하세요.


이 조건의 일반적인 사용 사례는 클라우드 앱에 대한 액세스를 [관리 디바이스](require-managed-devices.md)로 제한하는 정책입니다. 디바이스 플랫폼 조건을 포함하여 더 많은 시나리오를 보려면 [Azure Active Directory 앱 기반 조건부 액세스](app-based-conditional-access.md)를 참조하세요.



## <a name="device-state"></a>디바이스 상태

디바이스 상태 조건은 하이브리드 Azure AD 가입 디바이스와, 조건부 액세스 정책에서 준수로 표시된 디바이스를 제외합니다. 


![디바이스 상태 구성](./media/conditions/112.png)

이 조건은 추가적인 세션 보안을 제공하기 위해 관리되지 않는 디바이스에만 정책을 적용하는 경우에 유용합니다. 예를 들어 디바이스가 관리되지 않는 경우 Microsoft Cloud App Security 세션 제어만 적용합니다. 

## <a name="locations"></a>위치

위치를 사용하여 연결이 시도된 위치를 기준으로 조건을 정의할 수 있습니다. 

![위치 구성](./media/conditions/25.png)

이 조건의 일반적인 사용 사례는 다음 보호가 적용된 정책입니다.

- 사용자가 회사 네트워크 외부에서 서비스에 액세스할 때 다단계 인증을 요구합니다.  

- 특정 국가 또는 지역에서 서비스에 액세스하는 사용자를 차단합니다. 

자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건이란?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)을 참조하세요.


## <a name="client-apps"></a>클라이언트 앱

기본적으로 조건부 액세스 정책은 다음 앱에 적용됩니다.

- **[브라우저 앱](technical-reference.md#supported-browsers)** - 브라우저 앱에는 SAML, WS-Federation 또는 OpenID Connect 웹 SSO 프로토콜을 사용하는 웹 사이트가 포함됩니다. 이는 OAuth 기밀 클라이언트로 등록된 모든 웹 사이트 또는 웹 서비스에도 적용됩니다. 예를 들어 Office 365 SharePoint 웹 사이트가 있습니다. 

- **[최신 인증을 사용하는 모바일 및 데스크톱 앱](technical-reference.md#supported-mobile-applications-and-desktop-clients)** - 이러한 앱에는 Office 데스크톱 앱과 휴대폰 앱이 포함됩니다. 


또한 예를 들어 최신 인증을 사용하지 않는 특정 클라이언트 앱에 대해 정책을 대상으로 지정할 수 있습니다.

- **[Exchange ActiveSync 클라이언트](conditions.md#exchange-activesync-clients)** - 정책에 따라 Exchange ActiveSync 사용이 차단되면 영향을 받는 사용자는 차단된 이유에 대한 정보가 포함된 격리 이메일을 하나 받습니다. 필요한 경우 이메일에는 디바이스를 Intune에 등록하는 방법에 대한 지침이 포함됩니다.

- **[다른 클라이언트](block-legacy-authentication.md)** - 이러한 앱에는 IMAP, MAPI, POP, SMTP 및 최신 인증을 사용하지 않는 이전 Office 앱과 같은 메일 프로토콜과 함께 기본 인증을 사용하는 클라이언트가 포함됩니다. 자세한 내용은 [Office 2013 및 Office 2016 클라이언트 앱에 대한 최신 인증 작동 방식](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016)을 참조하세요.

![클라이언트 앱](./media/conditions/41.png)

이 조건의 일반적인 사용 사례는 다음 요구 사항이 적용된 정책입니다.

- 디바이스에 데이터를 다운로드하는 모바일 및 데스크톱 애플리케이션에 대해 **[관리 디바이스를 요구](require-managed-devices.md)** 합니다. 동시에 모든 디바이스에서 브라우저 액세스를 허용합니다. 이 시나리오는 관리되지 않는 디바이스에 문서를 저장하고 동기화하지 못하도록 합니다. 이 방법으로 디바이스를 분실하거나 도난당한 경우 데이터 손실 가능성을 줄일 수 있습니다.

- ActiveSync를 사용하여 Exchange Online에 액세스하는 앱에 대해 **[관리 디바이스를 요구](require-managed-devices.md)** 합니다.

- Azure AD(기타 클라이언트)에 대해 **[레거시 인증을 차단](block-legacy-authentication.md)** 합니다.

- 웹 애플리케이션 액세스를 차단하지만, 모바일 및 데스크톱 애플리케이션 액세스는 허용합니다.



### <a name="exchange-activesync-clients"></a>Exchange ActiveSync 클라이언트

다음과 같은 경우 **Exchange ActiveSync 클라이언트만** 선택할 수 있습니다.


- Microsoft Office 365 Exchange Online은 선택한 유일한 클라우드 앱입니다.

    ![클라우드 앱](./media/conditions/32.png)

- 정책에 다른 조건이 구성되어 있지 않습니다. 그러나 [지원되는 플랫폼](technical-reference.md#device-platform-condition)에만 적용하도록 이 조건의 범위를 좁힐 수 있습니다.
 
    ![지원되는 플랫폼에만 정책 적용](./media/conditions/33.png)


[관리 디바이스](require-managed-devices.md)가 필요해서 액세스가 차단된 경우 영향을 받는 사용자는 Intune을 사용하라고 안내해 주는 메일을 하나 받습니다. 

승인된 앱이 필요한 경우 영향을 받는 Outlook 모바일 클라이언트를 설치해서 사용하라는 지침을 받습니다.

다른 경우, 예를 들어 MFA가 필요한 경우 기본 인증을 사용하는 클라이언트가 MFA를 지원하지 않으므로 영향을 받는 사용자가 차단됩니다.

사용자 및 그룹에 대해서만 이 설정을 대상으로 지정할 수 있습니다. 게스트나 역할을 지원하지 않습니다. 게스트 또는 역할 조건이 구성된 경우 조건부 액세스에서 정책을 사용자에게 적용해야 하는지 여부를 확인할 수 없기 때문에 모든 사용자가 차단됩니다.


 자세한 내용은 다음을 참조하세요.

- [Azure Active Directory 조건부 액세스를 위한 SharePoint Online 및 Exchange Online 설정](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)
 
- [Azure Active Directory 앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) 



## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법에 대해 알아보려면 [빠른 시작: Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구](app-based-mfa.md)를 참조하세요.

- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 모범 사례](best-practices.md)를 참조하세요. 

