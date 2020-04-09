---
title: Microsoft ID 플랫폼의 인증 | Azure
description: Microsoft ID 플랫폼(v2.0)에서 인증의 기본 사항에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: e78f822a88b093992f065a509c2250e6a5c0dec2
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885568"
---
# <a name="authentication-basics"></a>인증 기본 사항

## <a name="what-is-authentication"></a>인증이란 무엇입니까?

이 문서에서는 보호된 웹 앱, 웹 API 또는 보호된 웹 API를 호출하는 앱을 만들기 위해 이해해야 하는 많은 인증 개념을 다룹니다. 익숙하지 않은 용어가 표시되면 기본 개념을 다루는 [용어집](developer-glossary.md) 이나 [Microsoft ID 플랫폼 비디오를](identity-videos.md) 사용해 보십시오.

**인증은** 당신이 당신이 말하는 것을 증명하는 과정입니다. Authentication(인증)은 종종 AuthN으로 축약됩니다.

**권한 부여는** 인증된 당사자에게 무언가를 할 수 있는 권한을 부여하는 행위입니다. 액세스할 수 있는 데이터와 해당 데이터로 수행할 수 있는 작업을 지정합니다. Authorization(권한 부여)는 종종 AuthZ로 축약됩니다.

앱은 여러 앱에서 사용자를 추가하거나 제거해야 할 때 관리 부담이 큰 자체 사용자 이름과 암호 정보를 각각 유지하는 앱을 만드는 대신 중앙 집중식 ID 공급자에 해당 책임을 위임할 수 있습니다.

Azure Active Directory(Azure AD)는 클라우드의 중앙 집중식 ID 공급자입니다. 인증 및 권한 부여를 위임하면 사용자가 특정 위치에 있어야 하는 조건부 액세스 정책, 다단계 인증 사용, 사용자가 한 번 로그인한 다음 동일한 중앙 집중식 디렉터리를 공유하는 모든 웹 앱에 자동으로 로그인할 수 있는 조건부 액세스 정책과 같은 시나리오가 가능합니다. 이 기능을 단일 사인온(SSO)이라고 합니다.

중앙 집중식 ID 공급자는 엔터프라이즈 네트워크에서 반드시 로그인할 필요는 없는 전 세계에 사용자가 있는 앱에 더욱 중요합니다. Azure AD는 사용자를 인증하고 액세스 토큰을 제공합니다. [액세스 토큰은](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) 권한 부여 서버에서 발급되는 보안 토큰입니다. 여기에는 토큰이 의도된 사용자 및 앱에 대한 정보가 포함됩니다. 웹 API 및 기타 보호된 리소스에 액세스하는 데 사용할 수 있습니다.

Microsoft ID 플랫폼은 [OAuth 2.0](https://oauth.net/2/) 및 [OpenID Connect와](https://openid.net/connect/)같은 업계 표준 프로토콜을 지원하고 다양한 플랫폼에 대한 오픈 소스 라이브러리를 지원하여 서비스로 ID를 제공하여 응용 프로그램 개발자에 대한 인증을 간소화합니다. 이를 통해 개발자는 모든 Microsoft ID에 로그인하는 응용 프로그램을 빌드하고, 토큰을 사용하여 [Microsoft Graph,](https://developer.microsoft.com/graph/)기타 Microsoft API 또는 개발자가 빌드한 API를 호출할 수 있습니다. 자세한 내용은 [Microsoft ID 플랫폼의 Evolution을](about-microsoft-identity-platform.md)참조하십시오.

### <a name="tenants"></a>테넌트

클라우드 ID 공급자는 많은 조직에 서비스를 제공합니다. 서로 다른 조직의 사용자를 별도로 유지하기 위해 Azure AD는 조직당 하나의 테넌트를 사용하여 테넌트로 분할됩니다.

테넌트는 사용자 및 관련 앱을 추적합니다. Microsoft ID 플랫폼은 개인 Microsoft 계정으로 로그인하는 사용자도 지원합니다.

또한 Azure AD는 조직이 Google 계정과 같은 소셜 ID를 사용하여 사용자(일반적으로 고객)에 로그인할 수 있도록 Azure Active Directory B2C를 제공합니다. 자세한 내용은 [Azure Active Directory B2C 설명서를](https://docs.microsoft.com/azure/active-directory-b2c) 참조하십시오.

### <a name="security-tokens"></a>보안 토큰

보안 토큰에는 사용자 및 앱에 대한 정보가 포함됩니다. Azure AD는 클레임을 포함하는 JWT(JWT)를 사용합니다.

클레임은 [클라이언트 응용 프로그램](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) 또는 리소스 [소유자와](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner)같은 한 엔터티에 대한 어설션을 [리소스 서버와](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server)같은 다른 엔터티에 제공합니다.

클레임은 토큰 주제에 대한 사실을 전달하는 이름/값 쌍입니다. 예를 들어 클레임에는 [권한 부여 서버에서](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server)인증한 보안 주체에 대한 사실이 포함될 수 있습니다. 지정된 토큰에 있는 클레임은 토큰 유형, 주체를 인증하는 데 사용되는 자격 증명 유형, 응용 프로그램 구성 등 여러 가지에 따라 달라집니다.

응용 프로그램은 다음과 같은 다양한 작업에 클레임을 사용할 수 있습니다.

* 토큰 유효성 검사
* 토큰 주체의 테넌트 식별
* 사용자 정보 표시
* 피사체의 권한 부여 결정

클레임은 다음과 같은 정보를 제공하는 키-값 쌍으로 구성됩니다.

* 토큰을 생성한 보안 토큰 서버
* 토큰이 생성된 날짜
* 제목(예: 데몬 제외)
* 토큰이 생성된 앱인 잠재고객
* 토큰을 요청한 앱(클라이언트)입니다. 웹 앱의 경우, 이는 잠재고객과 동일할 수 있습니다.

자세한 클레임 정보는 [액세스 토큰](access-tokens.md) 및 [ID 토큰을](id-tokens.md)참조하십시오.

토큰이 생성된 앱, 로그인한 사용자 또는 호출되는 웹 API가 토큰의 유효성을 검사하는 것은 사용자의 유효성을 검사하는 앱입니다. 토큰은 개인 키로 STS(보안 토큰 서버)에 의해 서명됩니다. STS는 해당 공개 키를 게시합니다. 토큰의 유효성을 검사하기 위해 앱은 STS 공개 키를 사용하여 개인 키를 사용하여 서명이 만들어졌는지 확인하여 서명을 확인합니다.

토큰은 제한된 시간 동안만 유효합니다. 일반적으로 STS는 응용 프로그램 또는 보호된 리소스에 액세스하는 액세스 토큰과 액세스 토큰이 만료될 때 액세스 토큰을 새로 고치는 데 사용되는 새로 고침 토큰과 같은 토큰 쌍을 제공합니다.

액세스 토큰은 헤더의 전달자 토큰으로 웹 `Authorization` API에 전달됩니다. 앱은 STS에 새로 고침 토큰을 제공할 수 있으며 앱에 대한 사용자 액세스가 취소되지 않은 경우 새 액세스 토큰과 새 새로 고침 토큰을 다시 가져옵니다. 이것이 기업을 떠나는 사람의 시나리오를 처리하는 방법입니다. STS가 새로 고침 토큰을 받으면 사용자가 더 이상 권한이 없는 경우 다른 유효한 액세스 토큰을 발급하지 않습니다.

### <a name="how-each-flow-emits-tokens-and-codes"></a>각 흐름이 토큰 및 코드를 방출하는 방법

클라이언트를 빌드하는 방법에 따라 Azure AD에서 지원하는 인증 흐름 중 하나(또는 여러 개)를 사용할 수 있습니다. 이러한 흐름은 권한 부여 코드뿐만 아니라 다양한 토큰(id_tokens, 새로 고침 토큰, 액세스 토큰)을 생성할 수 있으며 작동하도록 하기 위해 다른 토큰이 필요합니다. 이 차트는 개요를 제공합니다.

|흐름 | 필요 | id_token | 액세스 토큰 | 토큰 새로 고침 | 인증 코드 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[암시적 흐름](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[하이브리드 OIDC 흐름](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[토큰 사용 새로 고침](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 토큰 새로 고침 | x | x | x| |
|[흐름을 대신하여](v2-oauth2-on-behalf-of-flow.md) | 액세스 토큰| x| x| x| |
|[클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md) | | | x(앱 전용)| | |

암시적 모드를 통해 발급된 토큰은 URL(위치 `response_mode` `query` 또는 `fragment`있음)을 통해 브라우저로 다시 전달되기 때문에 길이 제한이 있습니다.  일부 브라우저는 브라우저 표시줄에 넣을 수 있는 URL의 크기에 제한이 있으며 너무 길면 실패합니다.  따라서 이러한 토큰에는 `groups` `wids` 클레임이 없거나 클레임이 없습니다. 

기본 사항에 대한 개요를 읽고 ID 앱 모델 및 API를 이해하고 Azure AD에서 프로비저닝이 작동하는 방식을 알아보고 Azure AD가 지원하는 일반적인 시나리오에 대한 자세한 정보에 대한 링크를 가져옵니다.

## <a name="application-model"></a>애플리케이션 모델

응용 프로그램은 사용자 스스로 로그인하거나 ID 공급자에 로그인을 위임할 수 있습니다. Azure AD에서 지원하는 로그인 시나리오에 대해 알아보려면 [인증 흐름 및 앱 시나리오를](authentication-flows-app-scenarios.md) 참조하세요.

ID 공급자가 사용자가 특정 앱에 액세스할 수 있음을 알려면 사용자와 응용 프로그램을 모두 ID 공급자에 등록해야 합니다. Azure AD에 응용 프로그램을 등록할 때 Azure AD와 통합할 수 있는 응용 프로그램에 대한 ID 구성을 제공합니다. 응용 프로그램을 등록하면 다음을 수행할 수 있습니다.

* 로그인 대화 상자에서 응용 프로그램의 브랜딩을 사용자 지정합니다. 이는 사용자가 앱에 처음 경험하는 환경이기 때문에 중요합니다.
* 사용자가 조직에 속한 경우에만 로그인하도록 허용할지 여부를 결정합니다. 단일 테넌트 응용 프로그램입니다. 또는 사용자가 직장 또는 학교 계정을 사용하여 로그인할 수 있도록 허용합니다. 다중 테넌트 응용 프로그램입니다. 또한 개인 마이크로 소프트 계정, 또는 링크드 인에서 소셜 계정을 허용 할 수 있습니다, 구글, 등등.
* 범위 사용 권한을 요청합니다. 예를 들어 로그인한 사용자의 프로필을 읽을 수 있는 권한을 부여하는 "user.read" 범위를 요청할 수 있습니다.
* 웹 API에 대한 액세스를 정의하는 범위를 정의합니다. 일반적으로 앱이 API에 액세스하려는 경우 정의한 범위에 대한 권한을 요청해야 합니다.
* 앱의 ID를 Azure AD에 증명하는 Azure AD와 비밀을 공유합니다.  이는 앱이 기밀 클라이언트 응용 프로그램인 경우에 해당됩니다. 기밀 클라이언트 응용 프로그램은 자격 증명을 안전하게 보유할 수 있는 응용 프로그램입니다. 자격 증명을 저장하려면 신뢰할 수 있는 백 엔드 서버가 필요합니다.

등록되면 응용 프로그램에 토큰을 요청할 때 앱이 Azure AD와 공유하는 고유 식별자가 제공됩니다. 앱이 기밀 [클라이언트 응용 프로그램인](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)경우 인증서 또는 암호가 사용되었는지 여부에 따라 비밀 또는 공개 키*도 공유됩니다.

Microsoft ID 플랫폼은 다음 두 가지 주요 기능을 수행하는 모델을 사용하는 응용 프로그램을 나타냅니다.

* 지원하는 인증 프로토콜로 앱 식별
* 인증에 필요한 모든 식별자, URL, 비밀 및 관련 정보 제공

Microsoft ID 플랫폼:

* 런타임시 인증을 지원하는 데 필요한 모든 데이터를 보유합니다.
* 앱에서 액세스해야 하는 리소스및 지정된 요청을 이행해야 하는 상황을 결정하기 위한 모든 데이터를 보유합니다.
* 앱 개발자의 테넌트 및 다른 Azure AD 테넌트에 앱 프로비전을 구현하기 위한 인프라 제공
* 토큰 요청 시간 동안 사용자 동의를 처리하고 테넌트와 의 하여 앱의 동적 프로비저닝을 용이하게 합니다.

동의는 리소스 소유자를 대신하여 특정 권한에 따라 보호된 리소스에 액세스할 수 있는 클라이언트 응용 프로그램에 대한 권한을 부여하는 리소스 소유자의 프로세스입니다. Microsoft ID 플랫폼:

* 앱이 대신 리소스에 액세스하는 데 관해 사용자 및 관리자가 동적으로 권한을 부여하거나 동의를 거부할 수 있도록 설정합니다.
* 관리자가 궁극적으로 앱이 수행할 수 있는 작업, 특정 앱을 사용할 수 있는 사용자 및 디렉터리 리소스에 액세스하는 방식을 결정하도록 설정합니다.

Microsoft ID 플랫폼에서 [응용 프로그램 개체는](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) 응용 프로그램을 설명합니다. 배포 시 Microsoft ID 플랫폼은 응용 프로그램 개체를 청사진으로 사용하여 디렉터리 또는 테넌트 내에서 응용 프로그램의 구체적인 인스턴스를 나타내는 [서비스 주체를](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object)만듭니다. 서비스 주체는 앱이 실제로 특정 대상 디렉터리에서 수행할 수 있는 일, 사용할 수 있는 사용자, 액세스할 수 있는 리소스 등을 정의합니다. Microsoft ID 플랫폼은 **동의를**통해 응용 프로그램 개체에서 서비스 주체를 만듭니다.

다음 다이어그램에는 동의를 통해 구동되는 간소화된 Microsoft ID 플랫폼 프로비저닝 흐름이 나와 있습니다. A와 B. 테넌트 A가 응용 프로그램을 소유하는 두 개의 테넌트를 보여 주십습니다. 테넌트 B는 서비스 주체를 통해 응용 프로그램을 인스턴스화합니다.  

![동의를 통해 구동되는 간소화된 프로비전 흐름](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

이 프로비전 흐름에서

1. 테넌트 B의 사용자가 앱에 로그인하려고 시도하면 권한 부여 엔드포인트에서 애플리케이션의 토큰을 요청합니다.
1. 사용자 자격 증명을 획득하고 인증을 위해 확인됩니다.
1. 앱이 테넌트 B에 액세스하는 데 동의를 제공하라는 메시지가 표시됩니다.
1. Microsoft ID 플랫폼은 테넌트 A의 응용 프로그램 개체를 테넌트 B에서 서비스 주체를 만들기 위한 청사진으로 사용합니다.
1. 사용자는 요청된 토큰을 받습니다.

추가 테넌에 대해 이 프로세스를 반복할 수 있습니다. 테넌트 A에서 앱(애플리케이션 개체)에 대한 청사진을 유지합니다. 앱에 동의가 부여된 다른 모든 테넌트의 사용자와 관리자는 각 테넌트의 해당 서비스 주체 개체를 통해 응용 프로그램이 수행할 수 있는 작업을 제어합니다. 자세한 내용은 [Microsoft ID 플랫폼의 애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Azure AD를 사용하면 웹 앱 로그인 흐름

사용자가 브라우저에서 웹 앱으로 이동하면 다음과 같은 일이 발생합니다.

* 웹 앱은 사용자가 인증되었는지 여부를 결정합니다.
* 사용자가 인증되지 않은 경우 웹 앱은 Azure AD에 위임하여 사용자에 로그인합니다. 로그인은 조직의 정책을 준수하며, 이는 사용자에게 자격 증명을 입력하도록 요청하거나, 다단계 인증을 사용하거나, 암호를 전혀 사용하지 않음을 의미할 수 있습니다(예: Windows Hello 사용).
* 사용자는 클라이언트 앱에 필요한 액세스에 동의하라는 메시지가 표시됩니다. 따라서 Azure AD가 사용자가 동의한 액세스를 나타내는 토큰을 제공할 수 있도록 클라이언트 앱을 Azure AD에 등록해야 합니다.

사용자가 성공적으로 인증된 경우:

* Azure AD는 웹 앱에 토큰을 보냅니다.
* 브라우저의 쿠키 항아리에 사용자의 ID를 포함하는 Azure AD 도메인과 연결된 쿠키가 저장됩니다. 다음에 앱이 브라우저를 사용하여 Azure AD 권한 부여 끝점으로 이동할 때 브라우저는 사용자가 다시 로그인할 필요가 없도록 쿠키를 제공합니다. 이것은 또한 SSO가 달성되는 방법입니다. 쿠키는 Azure AD에서 생성되며 Azure AD에서만 이해할 수 있습니다.
* 그런 다음 웹 앱에서 토큰의 유효성을 검사합니다. 유효성 검사가 성공하면 웹 앱은 보호된 페이지를 표시하고 브라우저의 쿠키 항아리에 세션 쿠키를 저장합니다. 사용자가 다른 페이지로 이동하면 웹 앱은 사용자가 세션 쿠키를 기반으로 인증된다는 것을 알고 있습니다.

다음 시퀀스 다이어그램은 이 상호 작용을 요약합니다.

![웹 앱 인증 프로세스](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>웹 앱이 사용자가 인증되는지 여부를 결정하는 방법

웹 앱 개발자는 전체 또는 특정 페이지에 인증이 필요한지 여부를 나타낼 수 있습니다. 예를 들어 ASP.NET/ASP.NET 이 작업은 컨트롤러 작업에 `[Authorize]` 특성을 추가하여 수행됩니다. 

이 특성으로 인해 ASP.NET 사용자의 ID를 포함하는 세션 쿠키의 존재를 확인합니다. 쿠키가 없는 경우 ASP.NET 지정된 ID 공급자에게 인증을 리디렉션합니다. ID 공급자가 Azure AD인 경우 웹 앱은 `https://login.microsoftonline.com`인증을 로 리디렉션하여 로그인 대화 상자를 표시합니다.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>웹 앱이 Azure AD에 로그인을 위임하고 토큰을 가져오는 방법

사용자 인증은 브라우저를 통해 수행됩니다. OpenID 프로토콜은 표준 HTTP 프로토콜 메시지를 사용합니다.
* 웹 앱은 AZURE AD를 사용하기 위해 브라우저로 HTTP 302(리디렉션)를 보냅니다.
* 사용자가 인증되면 Azure AD는 브라우저를 통한 리디렉션을 사용하여 토큰을 웹 앱으로 보냅니다.
* 리디렉션은 리디렉션 URI의 형태로 웹 앱에서 제공됩니다. 이 리디렉션 URI는 Azure AD 응용 프로그램 개체에 등록 됩니다. 응용 프로그램이 여러 URL에 배포될 수 있으므로 여러 리디렉션 URI가 있을 수 있습니다. 따라서 웹 앱은 사용할 리디렉션 URI도 지정해야 합니다.
* Azure AD는 웹 앱에서 전송하는 리디렉션 URI가 앱에 등록된 리디렉션 URI 중 하나인지 확인합니다.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Azure AD를 사용하여 데스크톱 및 모바일 앱 로그인 흐름

위에서 설명한 흐름은 데스크톱 및 모바일 응용 프로그램에 약간의 차이가 있습니다.

데스크톱 및 모바일 응용 프로그램은 인증을 위해 포함된 웹 컨트롤 또는 시스템 브라우저를 사용할 수 있습니다. 다음 다이어그램에서는 데스크톱 또는 모바일 앱이 MSAL(Microsoft 인증 라이브러리)을 사용하여 액세스 토큰을 획득하고 웹 API를 호출하는 방법을 보여 주며 있습니다.

![데스크톱 앱이 어떻게 보이는지](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL은 브라우저를 사용하여 토큰을 얻습니다. 웹 앱과 마찬가지로 인증은 Azure AD에 위임됩니다.

Azure AD는 웹 앱과 동일한 ID 쿠키를 브라우저에 저장하므로 네이티브 또는 모바일 앱이 시스템 브라우저를 사용하는 경우 해당 웹 앱에서 즉시 SSO를 받게 됩니다.

기본적으로 MSAL은 시스템 브라우저를 사용합니다. 임베디드 컨트롤을 사용하여 보다 통합된 사용자 환경을 제공하는 .NET Framework 데스크톱 응용 프로그램은 예외입니다.

## <a name="next-steps"></a>다음 단계

* 일반적인 용어에 대해 알아보려면 [Microsoft ID 플랫폼 개발자 용어집을](developer-glossary.md) 참조하십시오.
* Microsoft ID 플랫폼에서 지원하는 사용자를 인증하기 위한 다른 시나리오에 대해 자세히 알아보려면 [인증 흐름 및 앱 시나리오를](authentication-flows-app-scenarios.md) 참조하세요.
* [MSAL 라이브러리를](msal-overview.md) 참조하여 간소화된 단일 프로그래밍 모델에서 Microsoft 계정, Azure AD 계정 및 Azure AD B2C 사용자와 함께 작동하는 응용 프로그램을 개발하는 데 도움이 되는 Microsoft 라이브러리에 대해 알아봅니다.
* 앱 서비스 앱에 대한 인증을 구성하는 방법을 알아보려면 [Microsoft ID 플랫폼과](/azure/app-service/configure-authentication-provider-aad) 앱 서비스 통합을 참조하세요.
