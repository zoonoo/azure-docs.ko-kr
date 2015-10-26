<properties
	pageTitle="앱 모델 v2.0 제한 사항 | Microsoft Azure"
	description="Azure AD v2.0 앱 모델의 제한 사항 목록입니다."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# 앱 모델 v2.0 미리 보기: 제한 사항

공개 미리 보기 기간에는 아직 지원되지 않는 v2.0 앱 모델의 여러 특징 및 기능이 있습니다. 이러한 각 제한 사항은 v2.0 앱 모델이 일반 공급되기 전에 제거되지만 공개 미리 보기 중 앱을 빌드하는 경우 알고 있어야 합니다.

> [AZURE.NOTE]이 정보는 v2.0 앱 모델 공개 미리 보기에 적용됩니다. 일반 공급 Azure AD 서비스와 통합하는 방법에 대한 지침은 [Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)를 참조하세요.

## 프로덕션 앱에 대한 지원
v2.0 앱 모델과 통합된 앱은 프로덕션 수준 앱으로 일반 사용자에게 출시하면 안 됩니다. v2.0 앱 모델은 현재 공개 미리 보기 상태이며, 언제든지 주요 변경 내용이 도입될 수 있고 서비스에서 보장하는 SLA가 없습니다. 발생할 수 있는 사건에 대한 지원이 제공되지 않습니다. 아직 개발 중인 서비스를 사용하는 위험을 감수하려는 경우 @AzureAD에서 문의하여 앱 또는 서비스의 범위를 논의해야 합니다.

## 앱에 대한 제한 사항
다음과 같은 앱 형식은 현재 v2.0 앱 모델 공개 미리 보기에서 지원되지 않습니다. 지원되는 앱 형식에 대한 설명은 [이 문서](active-directory-v2-flows.md)를 참조하세요.

##### 단일 페이지 앱(Javascript)
대부분의 최신 앱에는 주로 Javascript로 작성되고 AngularJS, Ember.js, Durandal 등과 같은 SPA 프레임워크로도 작성되는 단일 페이지 앱 프런트 엔드가 있습니다. 일반 공급 Azure AD 서비스는 [OAuth 2.0 암시적 흐름](active-directory-v2-protocols.md#oauth2-implicit-flow)을 사용하여 이러한 앱을 지원하지만 v2.0 앱 모델에서는 이 흐름을 아직 사용할 수 없습니다. 곧 제공될 예정입니다.

v2.0 앱 모델에서 SPA를 작동하려면 [웹앱 흐름](active-directory-v2-flows.md#web-apps)을 사용하여 인증을 구현할 수 있습니다. 그러나 권장 방법은 아니며 이 시나리오에 대한 설명서는 제한됩니다. SPA 시나리오를 사용하려면 [일반 공급 Azure AD SPA 코드 샘플](active-directory-devquickstarts-angular.md)을 확인할 수 있습니다.

##### 디먼/서버 쪽 앱
장기 실행 프로세스를 포함하거나 사용자 없이 작동하는 앱은 Web API아 같은 보안 리소스에 액세스하는 방법도 필요합니다. 이러한 앱은 [OAuth 2.0 클라이언트 자격 증명 흐름](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow)을 사용하여 사용자의 위임된 ID 대신 앱 ID로 인증하고 토큰을 가져올 수 있습니다.

이 흐름은 현재 v2.0 앱 모델에서 지원되지 않으므로 대화형 사용자 로그인 흐름이 발생한 후에만 앱이 토큰을 가져올 수 있습니다. 조만간 클라이언트 자격 증명 흐름이 추가될 예정입니다. 일반 공급 Azure AD 앱 모델에서 클라이언트 자격 증명 흐름을 확인하려면 [GitHub의 디먼 샘플](https://github.com/AzureADSamples/Daemon-DotNet)을 참조하세요.

##### 연결된 Web API(On-Behalf-Of)
많은 아키텍처에는 다른 다운스트림 Web API를 호출해야 하는 Web API가 포함되어 있으며 둘 다 v2.0 앱 모델로 보안됩니다. 이 시나리오는 Web API 백 엔드를 포함하는 네이티브 클라이언트에서 일반적이며, Web API 백 엔드가 다시 Office 365 또는 Graph API와 같은 Microsoft 온라인 서비스를 호출합니다.

[On-Behalf-Of 흐름](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow)이라고도 하는 OAuth 2.0 Jwt 전달자 자격 증명 권한 부여를 사용하여 이 연결된 Web API 시나리오를 지원할 수 있습니다. 그러나 On-Behalf-Of 흐름은 현재 v2.0 앱 모델 미리 보기에 구현되어 있지 않습니다. 일반 공급 Azure AD 서비스에서 이 흐름이 작동하는 방식을 확인하려면 [GitHub의 On-Behalf-Of 코드 샘플](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)을 참조하세요.

##### 독립 실행형 Web API
v2.0 앱 모델 미리 보기에서는 v2.0 끝점의 [OAuth 토큰을 사용하여 보안된 Web API를 빌드](active-directory-v2-flows.md#web-apis)할 수 있습니다. 그러나 해당 Web API는 동일한 응용 프로그램 ID를 공유하는 클라이언트에서만 토큰을 받을 수 있습니다. 여러 다른 클라이언트에서 액세스되는 타사 웹 서비스 빌드는 지원되지 않습니다.

동일한 앱 ID를 가진 잘 알려진 클라이언트의 토큰을 수락하는 Web API를 빌드하는 방법을 확인하려면 [시작](active-directory-appmodel-v2-overview.md#getting-started) 섹션에서 v2.0 앱 모델 Web API 샘플을 참조하세요.

## 사용자에 대한 제한 사항
현재 v2.0 앱 모델로 빌드된 각 앱은 Microsoft 계정 또는 Azure AD 계정을 가진 모든 사용자에게 공개적으로 노출됩니다. 두 형식의 계정을 가진 사용자는 모두 앱을 탐색하거나 설치하고, v2.0 앱 모델에서 자격 증명을 입력하고, 앱의 사용 권한에 동의할 수 있습니다. 특정 사용자 집합의 로그인을 거부하도록 앱 코드를 작성할 수 있지만, 그래도 앱에 동의할 수 없도록 차단되지는 않습니다.

실제로 앱은 해당 앱에 로그인할 수 있는 사용자 유형을 제한할 수 없습니다. LOB(기간 업무) 앱(한 조직의 사용자로 제한됨), Azure AD 계정을 가진 엔터프라이즈 사용자만 사용할 수 있는 앱 또는 Microsoft 계정을 가진 소비자만 사용할 수 있는 앱을 빌드할 수 없습니다.

## 앱 등록에 대한 제한 사항
이 시점에서 v2.0 앱 모델과 통합하려는 모든 앱은 [apps.dev.microsoft.com](https://apps.dev.microsoft.com)에서 새로운 앱 등록을 만들어야 합니다. 기존 Azure AD 또는 Microsoft 계정 앱은 v2.0 앱 모델과 호환되지 않으며 새로운 앱 등록 포털 외에는 어떤 포털에서도 앱이 등록되지 않습니다. 일반 공급 Azure AD 서비스에서 v2.0 앱 모델로의 앱 마이그레이션 경로는 없습니다.

마찬가지로, 새로운 앱 등록 포털에서 등록된 앱은 v2.0 앱 모델에서만 작동합니다. 앱 등록 포털을 사용하여 Azure AD 또는 Microsoft 계정 서비스와 통합되는 앱을 만들 수 없습니다.

새로운 응용 프로그램 등록 포털에서 등록된 앱은 현재 제한된 redirect\_uri 값 집합으로 한정됩니다. 웹앱 및 서비스에 대한 redirect\_uri는 체계 또는 `https`로 시작해야 하는 반면, 다른 모든 플랫폼에 대한 redirect\_uri는 `urn:ietf:oauth:2.0:oob`라는 하드 코드된 값을 사용해야 합니다.

새로운 응용 프로그램 등록 포털에서 앱을 등록하는 방법을 알아보려면 [이 문서](active-directory-v2-app-registration.md)를 참조하세요.

## 서비스 및 API에 대한 제한 사항
v2.0 앱 모델은 [지원되는 인증 흐름](active-directory-v2-flows.md) 목록에 해당하는 경우 현재 새로운 응용 프로그램 등록 포털에서 등록된 모든 앱에 대해 로그인을 지원합니다. 그러나 이러한 앱은 매우 제한된 리소스 집합에 대한 OAuth 2.0 액세스 토큰만 획득할 수 있습니다. v2.0 끝점은 다음에 대한 access\_token만 발급합니다.

- 토큰을 요청한 앱. 논리적 앱이 여러 구성 요소나 계층으로 이루어진 경우 앱이 자신에 대한 access\_token을 획득할 수 있습니다. 이 시나리오의 작동 방식을 확인하려면 [시작](active-directory-appmodel-v2-overview.md#getting-started) 자습서를 확인하세요.
- 모두 https://outlook.office.com에 있는 Outlook 메일, 일정 및 연락처 REST API. 이러한 API에 액세스하는 앱을 작성하는 방법을 알아보려면 [Office 시작](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) 자습서를 참조하세요.

조만간 사용자 고유의 Web API 및 서비스 지원뿐 아니라 더 많은 Microsoft 온라인 서비스가 추가될 예정입니다.

## 라이브러리 및 SDK에 대한 제한 사항
일부 언어 및 플랫폼에는 v2.0 앱 모델 미리 보기를 지원하는 라이브러리가 없습니다. 인증 라이브러리 집합은 현재 .NET, iOS, Android, NodeJS 및 Javascript로 제한됩니다. 각 항목에 해당하는 코드 샘플 및 자습서는 [시작](active-directory-appmodel-v2-overview.md#getting-started) 섹션에서 확인할 수 있습니다.

다른 언어 또는 플랫폼을 사용하는 v2.0 앱 모델과 앱을 통합하려는 경우 v2.0 끝점과 통신하는 데 필요한 HTTP 메시지를 생성하는 방법을 안내하는 [OAuth 2.0 및 OpenID Connect 프로토콜 참조](active-directory-v2-protocols.md)를 참조하세요.

## 프로토콜에 대한 제한 사항
v2.0 앱 모델은 Open ID Connect 및 OAuth 2.0을 지원합니다. 그러나 각 프로토콜의 일부 특징과 기능은 v2.0 앱 모델에 통합되어 있지 않습니다. 일부 사례:

- OpenID Connect `prompt` 매개 변수에 대한 전체 지원
- OpenID Connect `login_hint` 매개 변수
- OpenID Connect `domain_hint` 매개 변수
- OpenID Connect `end_sesssion_endpoint`

v2.0 앱 모델에서 지원되는 프로토콜 기능의 범위를 더 잘 이해하려면 [OpenID Connect 및 OAuth 2.0 프로토콜 참조](active-directory-v2-protocols.md)를 참조하세요.

<!---HONumber=Oct15_HO3-->