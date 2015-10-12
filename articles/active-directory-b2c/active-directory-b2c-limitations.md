<properties
	pageTitle="Azure Active Directory B2C 미리 보기: 제한 사항 | Microsoft Azure"
	description="Azure Active Directory B2C에서 발생하는 제한 사항 목록"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 미리 보기: 제한 사항

미리 보기하는 동안 아직 지원되지 않는 Azure Active Directory (AD) B2C의 여러 특징 및 기능이 있습니다. 이러한 많은 제한 사항은 Azure AD B2C가 일반 공급되기 전에 제거되지만 미리 보기하는 동안 Azure AD B2C를 사용하여 소비자 지향 응용 프로그램을 빌드하는 경우 알고 있어야 합니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Azure AD B2C 디렉터리를 만드는 동안 문제점

[Azure AD B2C 테넌트를 만드는](active-directory-b2c-get-started) 동안 발생할 수 있다고 알려진 문제가 있습니다. 지침은 이 [문서](active-directory-b2c-support-create-directory.md)를 확인합니다.

## 확인 메일 및 셀프 서비스 암호 재설정 페이지의 브랜딩 문제

기본 확인 메일 및 셀프 서비스 암호 재설정 페이지에는 "Microsoft" 및 "Azure" 브랜드 요소가 포함되어 있습니다. 이러한 요소는 앞으로 제거될 예정입니다. 이러한 브랜딩 요소가 표시되지 않도록 [회사 브랜딩 기능](./active-directory/active-directory-add-company-branding.md)을 사용하여 해당 페이지의 브랜딩을 변경할 수 있습니다.

## 프로덕션 응용 프로그램에 대한 지원

Azure AD B2C와 통합된 응용 프로그램은 프로덕션 수준 응용 프로그램으로 일반 사용자에게 출시하면 안 됩니다. Azure AD B2C은 현재 미리 보기 상태이며, 언제든지 주요 변경 내용이 도입될 수 있고 서비스에서 보장하는 SLA가 없습니다. 발생할 수 있는 사건에 대한 지원이 제공되지 않습니다. 아직 개발 중인 서비스를 사용하는 위험을 감수하려는 경우 @AzureAD에서 트윗하여 응용 프로그램 또는 서비스의 범위를 논의해야 합니다.

## 응용 프로그램에 대한 제한 사항

다음과 같은 응용 프로그램의 형식은 현재 Azure AD B2C 미리 보기에서 지원되지 않습니다. 지원되는 응용 프로그램 종류에 대한 설명은 [이 문서](active-directory-b2c-apps)를 참조하세요.

### 단일 페이지 응용 프로그램(Javascript)

대부분의 최신 응용 프로그램에는 주로 Javascript로 작성되고 AngularJS, Ember.js, Durandal 등과 같은 SPA 프레임워크로도 작성되는 단일 페이지 응용 프로그램(SPA) 프런트 엔드가 있습니다. 이 흐름을 Azure AD B2C 미리 보기에서 사용할 수 없습니다.

### 디먼/서버 측면 응용 프로그램

장기 실행 프로세스를 포함하거나 사용자 없이 작동하는 응용 프로그램은 Web API아 같은 보안 리소스에 액세스하는 방법도 필요합니다. 이러한 응용 프로그램은 [OAuth 2.0 클라이언트 자격 증명 흐름](active-directory-b2c-protocols.md#oauth2-client-credentials-grant-flow)을 사용하여 소비자의 위임된 ID 대신 응용 프로그램의 ID로 인증하고 토큰을 가져올 수 있습니다. 이 흐름을 Azure AD B2C 미리 보기에서 아직 사용할 수 없으므로 대화형 소비자 로그인 흐름이 발생한 후에 응용 프로그램은 토큰을 가져올 수 있습니다.

### 독립 실행형 Web API

Azure AD B2C 미리 보기에서는 [OAuth 2.0 토큰을 사용하여 보안된 Web API를 빌드](active-directory-b2c-apps.md#web-apis)할 수 있습니다. 그러나 해당 Web API는 동일한 응용 프로그램 ID를 공유하는 클라이언트에서만 토큰을 받을 수 있습니다. 여러 다른 클라이언트에서 액세스되는 Web API 빌드는 지원되지 않습니다.

### Web API 체인(On-Behalf-Of)

많은 아키텍처에는 다른 다운스트림 Web API를 호출해야 하는 Web API가 포함되어 있으며 둘 다 Azure AD B2C로 보안됩니다. 이 시나리오는 Web API 백 엔드를 포함하는 네이티브 클라이언트에서 일반적이며, Web API 백 엔드가 다시 Azure AD Graph API와 같은 Microsoft 온라인 서비스를 호출합니다.

On-Behalf-Of 흐름이라고도 하는 OAuth 2.0 Jwt 전달자 자격 증명 권한 부여를 사용하여 이 연결된 Web API 시나리오를 지원할 수 있습니다. 그러나 On-Behalf-Of 흐름은 현재 Azure AD B2C 미리 보기에 구현되어 있지 않습니다.

## 라이브러리 및 SDK에 대한 제한 사항

일부 언어 및 플랫폼에는 Azure AD B2C 미리 보기를 지원하는 라이브러리가 없습니다. 인증 라이브러리 집합은 현재 .NET, iOS, Android 및 NodeJS로 제한됩니다. 각 항목에 해당하는 빠른 시작 자습서는 [시작](active-directory-b2c-overview.md#getting-started) 섹션에서 확인할 수 있습니다.

다른 언어 또는 플랫폼을 사용하는 Azure AD B2C 미리 보기와 응용 프로그램을 통합하려는 경우 Azure AD B2C 서비스와 통신하는 데 필요한 HTTP 메시지를 생성하는 방법을 안내하는 [OAuth 2.0 및 OpenID Connect 프로토콜 참조](active-directory-b2c-protocols.md)를 참조하세요.

## 프로토콜에 대한 제한 사항

Azure AD B2C 미리 보기는 OpenID Connect 및 OAuth 2.0을 지원합니다. 그러나 각 프로토콜의 일부 특징과 기능은 구현되지 않습니다. Azure AD B2C 미리 보기에서 지원되는 프로토콜 기능의 범위를 더 잘 이해하려면 [OpenID Connect 및 OAuth 2.0 프로토콜 참조](active-directory-b2c-protocols.md)를 자세히 읽어보세요.

## 토큰에 대한 제한 사항

Azure AD B2C 미리 보기에서 발급된 토큰은 대부분 JSON 웹 토큰, 즉 JWT로 구현됩니다. 그러나 JWT에 포함된 일부 정보("클레임"이라고 함)는 부족하거나 누락되었습니다. 일부 예제는 "sub" 및 "preferred\_username" 클레임을 포함합니다. 미리 보기하는 동안 여기서 상당히 변경될 것을 예상해야 합니다. Azure AD B2C 서비스에서 현재 내보내는 토큰을 더 잘 이해하려면 [토큰 참조](active-directory-b2c-tokens.md)를 자세히 읽어보세요.

## Azure 포털에서 사용자 관리에 발생하는 문제

B2C 기능은 Azure Preview 포털에 액세스할 수 있습니다. 그러나 Azure 포털을 사용하여 사용자 관리를 포함하여 다른 테넌트 기능에 액세스할 수 있습니다. 현재 Azure 포털의 사용자 관리(**사용자** 탭)와 관련해서 알려진 몇 가지 문제가 있습니다.

- 로컬 계정 사용자(즉, 메일 주소 및 암호 또는 사용자 이름 및 암호로 등록한 소비자)의 경우 **사용자 이름** 필드가 등록할 때 사용한 로그인 식별자(메일 주소 또는 사용자 이름)와 일치하지 않습니다. 즉, Azure 포털에 표시되는 필드가 실제로 사용자 계정 이름(UPN)이기 때문에 B2C 시나리오에서 사용하지 않습니다. 로컬 계정의 로그인 식별자를 보려면 [Graph Explorer](https://graphexplorer.cloudapp.net/)에서 사용자 개체를 찾습니다. 소셜 계정 사용자와 동일한 문제를 찾을 수 있지만(즉, Facebook, Google + 등으로 등록된 소비자) 이 경우 이야기할 로그인 식별자가 없습니다.

    ![로컬 계정 - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- 로컬 계정 사용자의 경우 **프로필** 탭에서 필드를 편집하고 변경 내용을 저장할 수 없습니다. 이 문제는 곧 해결됩니다.

## Azure 포털의 관리자가 시작한 암호 재설정 관련 문제

Azure 포털에서 로컬 계정 기반 소비자에 대한 암호를 재설정하는 경우(**사용자** 탭의 **암호 재설정** 명령) 해당 소비자는 다음 로그인 시 자신의 암호를 변경할 수 없고 응용 프로그램에서 잠깁니다. 당사에서 이 문제를 해결하기 위한 작업을 하고 있습니다. 해결 방법으로 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)를 사용하여 소비자의 암호를 재설정하세요.

## Azure AD B2C 디렉터리의 삭제에 대한 제한 사항

Azure 포털에서 Azure AD B2C 테넌트를 삭제할 수 없습니다.

<!---HONumber=Oct15_HO1-->