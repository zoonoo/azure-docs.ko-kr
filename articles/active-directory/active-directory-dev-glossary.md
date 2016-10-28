<properties
   pageTitle="Azure Active Directory 개발자 용어집 | Microsoft Azure"
   description="일반적으로 사용되는 Azure Active Directory 개발자 개념 및 기능에 대한 용어 목록입니다."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="bryanla"/>

# Azure Active Directory 개발자 용어집
이 문서에는 Azure AD에 대한 응용 프로그램 개발에 관해 알아보고자 할 때 유용한 핵심 Azure Active Directory(AD) 개발자 개념 함 합니다.

## 액세스 토큰 
[보호된 리소스 서버](#resource-server)에 액세스하기 위해 [권한 부여 서버](#authorization-server)가 발급하고, [클라이언트 응용 프로그램](#client-application)이 사용하는 [보안 토큰](#security-token)의 종류. 일반적으로 [JSON Web Token(JWT)][JWT]의 형태로, 토큰은 요청된 액세스 수준에 대해 [리소스 소유자](#resource-owner)가 클라이언트에 부여한 권한 부여를 구체화합니다. 토큰은 주체에 관한 모든 적용 가능한 [클레임](#claim)을 포함하여 클라이언트 응용 프로그램이 지정된 리소스에 액세스할 때 자격 증명의 형식으로 사용할 수 있습니다. 또는 이는 리소스 소유자의 필요를 제거하여 클라이언트에 자격 증명을 노출합니다.

액세스 토큰은 자격 증명이 표시되는 방식에 따라 때로 "사용자 + 앱" 또는 "앱 전용"이라고 합니다. 예를 들어 클라이언트 응용 프로그램이 다음을 사용할 경우.

- ["권한 부여 코드" 권한 부여](#authorization-grant), 최종 사용자는 리소스 소유자로서 우선 권한 부여를 하여 클라이언트가 리소스에 액세스할 수 있도록 권한 부여를 위임합니다. 클라이언트는 액세스 토큰을 가져올 때 나중에 인증합니다. 토큰은 클라이언트 응용 프로그램에 권한 부여하는 사용자와 응용 프로그램을 나타냄으로 때로 더 구체적으로 "사용자 + 앱"이라고 합니다.
- ["클라이언트 자격 증명" 권한 부여](#authorization-grant), 클라이언트는 유일한 인증을 제공하여 리소스 소유자의 인증/권한 부여 없이 작동하도록 합니다. 따라서 토큰을 때로 “앱 전용” 토큰이라고도 할 수 있습니다.

자세한 내용은 [Azure AD 토큰 참조][AAD-Tokens-Claims]를 참조하세요.

## 응용 프로그램 매니페스트  
[Azure 클래식 포털][AZURE-classic-portal]이 제공하는 기능, 응용 프로그램 ID 구성의 JSON 표현을 생성하며 연결된 [응용 프로그램][AAD-Graph-App-Entity] 및 [ServicePrincipal][AAD-Graph-Sp-Entity] 엔터티를 업데이트하기 위한 메커니즘으로 사용됩니다. 자세한 내용은 [Azure Active Directory 응용 프로그램 매니페스트 이해][AAD-App-Manifest]를 참조하세요.

## 응용 프로그램 개체  
[Azure 클래식 포털][AZURE-classic-portal]의 응용 프로그램을 등록/업데이트할 때, 포털은 응용 프로그램 개체와 그 테넌트에 대한 해당 [서비스 주체 개체](#service-principal-object)를 만들기/업데이트합니다. 응용 프로그램 개체는 응용 프로그램의 ID 구성을(액세스하는 모든 테넌트에 걸쳐) 전역으로 *정의*하며, 해당 서비스 주체 개체가 런타임 시(특정 테넌트에서) 로컬로 사용되도록 *파생*되는 템플릿을 제공합니다.

자세한 내용은 [응용 프로그램 및 서비스 주체 개체][AAD-App-SP-Objects]를 참조하세요.

## 응용 프로그램 등록  
응용 프로그램을 Azure AD와 통합하고 ID 및 액세스 관리 기능을 Azure AD에 위임할 수 있도록 허용하기 위해 Azure AD [테넌트](#tenant)를 사용하여 등록해야 합니다. Azure Ad를 사용하여 응용 프로그램을 등록할 때 응용 프로그램에 대한 ID 구성을 제공하여 Azure AD와 통합하고 다음과 같은 기능을 사용할 수 있도록 허용합니다.

- Azure AD ID 관리 및 [OpenID Connect][OpenIDConnect] 프로토콜 구현을 사용한 Single Sign-On의 강력한 관리.
- [클라이언트 응용 프로그램](#client-application)이 Azure Ad의 OAuth 2.0 [권한 부여 서버](#authorization-server) 구현을 통해 [보호된 리소스](#resource-server)에 조정된 액세스.
- 리소스 소유자 권한 부여에 따라 클라이언트가 보호된 리소스에 액세스하는 것을 관리하는 [동의 프레임 워크](#consent).

자세한 내용은 [Azure Active Directory와 응용 프로그램 통합][AAD-Integrating-Apps]을 참조하세요.

## authentication
ID 및 액세스 제어에 사용할 보안 주체를 만들기 위한 기반을 제공하도록 당사자에게 합법적인 자격 증명을 하게 챌린지하는 작업. 예를 들어 [OAuth2 권한 부여](#authorization-grant) 시에 당사자 인증은 사용된 권한 부여에 따라 [리소스 소유자](#resource-owner) 또는 [클라이언트 응용 프로그램](#client-application)의 역할을 충족합니다.

## 권한 부여
인증된 보안 주체에게 작업을 수행할 수 있게 사용 권한을 부여하는 작업. Azure AD 프로그래밍 모델의 기본 사용 사례가 두 가지 있습니다.

- [OAuth2 권한 부여](#authorization-grant) 흐름 동안: [리소스 소유자](#resource-owner)가 [클라이언트 응용 프로그램](#client-application)에 권한 부여를 할 때 클라이언트가 리소스 소유자의 리소스에 액세스할 수 있도록 허용합니다.
- 클라이언트가 리소스 액세스하는 동안: [리소스 서버](#resource-server)에 의해 구현되는 대로 [액세스 토큰](#access-token)에 있는 [클레임](#claim) 값을 사용하여 그것들에 기초한 액세스 제어 결정을 합니다.

## 권한 부여 코드
네 가지 OAuth2 [권한 부여](#authorization-grant) 중 하나인 "권한 부여 코드" 흐름의 일부로 [권한 부여 끝점](#authorization-endpoint)에 의해 [클라이언트 응용 프로그램](#client-application)에 제공되는 수명이 짧은 "토큰". 코드가 [리소스 소유자](#resource-owner)의 인증에 대한 응답으로 클라이언트 응용 프로그램에 반환되며 이는 리소스 소유자가 요청된 리소스에 액세스하는 권한을 위임하였음을 나타냅니다. 흐름의 일부로, 코드는 나중에 [액세스 토큰](#access-token)으로 교환됩니다.

## 권한 부여 끝점
[권한 부여 서버](#authorization-server)에 의해 구현되는 끝점 중 하나로 OAuth2 권한 부여 흐름 동안 [권한 부여](#authorization-grant)를 제공하기 위해 [리소스 소유자](#resource-owner)와 상호 작용하는 데 사용됩니다. 사용된 권한 부여 흐름에 따라 실제로 제공된 권한 부여는 [권한 부여 코드](#authorization-code) 또는 [보안 토큰](#security-token)을 포함하여 다를 수 있습니다.

자세한 내용은 OAuth2 사양의 [권한 부여 형식][OAuth2-AuthZ-Grant-Types] 및 [권한 부여 끝점][OAuth2-AuthZ-Endpoint] 섹션과 [OpenIDConnect 사양][OpenIDConnect-AuthZ-Endpoint]을 참조하세요.

## 권한 부여
[클라이언트 응용 프로그램](#client-application)에 부여된, 보호된 리소스에 액세스할 수 있는 [리소스 소유자의](#resource-owner) [권한 부여](#authorization)를 나타내는 자격 증명. 클라이언트 응용 프로그램은 클라이언트 유형/요구 사항에 따라 권한 부여를 얻기 위해 [OAuth2에 의해 정의된 네 가지 권한 부여][OAuth2-AuthZ-Grant-Types] 중 하나를 사용할 수 있습니다. 그 유형은 "권한 부여 코드 부여", "클라이언트 자격 증명 부여", "암시적 허용" 및 "리소스 소유자 암호 자격 증명 부여"입니다. 클라이언트에 반환되는 자격 증명은 사용된 권한 부여 유형에 따라 [액세스 토큰](#access-token), 또는 [권한 부여 코드](#authorization-code)(나중에 액세스 토큰과 교환됨)입니다.

## 권한 부여 서버
[OAuth2 권한 부여 프레임워크][OAuth2-Role-Def]에서 정의된 대로 [리소스 소유자](#resource-owner)를 성공적으로 인증하고 권한 부여를 얻은 후에 [클라이언트](#client-application)에 액세스 토큰 발급을 담당하는 서버. [클라이언트 응용 프로그램](#client-application)은 런타임시 [권한 부여](#authorization-grant)에서 정의된 OAuth2에 따라 [권한 부여](#authorization-endpoint) 및 [토큰](#token-endpoint) 끝점을 통해 권한 부여 서버와 상호 작용합니다.

Azure AD 응용 프로그램 통합의 경우 Azure AD는 예를 들어 [Microsoft Graph API][Microsoft-Graph]와 같은 Azure AD 응용 프로그램 및 Microsoft 서비스 API에 대한 권한 부여 서버 역할을 구현합니다.

## 클레임
[보안 토큰](#security-token)은 클레임을 포함하며 하나의 엔터티([클라이언트 응용 프로그램](#client-application) 또는 [리소스 소유자](#resource-owner) 등)에 관한 어설션을([리소스 서버](#resource-server)와 같은) 또 다른 엔터티에 제공합니다. 클레임은 토큰 주체(예를 들어 [권한 부여 서버](#authorization-server)에 의해 인증된 보안 주체)에 관한 팩트를 릴레이하는 이름/값 쌍입니다. 지정된 토큰에 있는 클레임은 토큰 형식, 주체 인증에 사용되는 자격 증명 유형, 응용 프로그램 구성 등을 포함한 여러 변수에 따라 다릅니다.

자세한 내용은 [Azure AD 토큰 참조][AAD-Tokens-Claims]를 참조하세요.

## 클라이언트 응용 프로그램  
[OAuth2 권한 부여 프레임워크][OAuth2-Role-Def]에 정의된 대로, [리소스 소유자](#resource-owner) 대신 보호된 리소스 요청을 하는 응용 프로그램. "클라이언트"라는 용어는 특정 하드웨어 구현 특성(예: 응용 프로그램이 서버, 데스크톱, 또는 다른 장치에서 실행할지 여부)을 의미하지 않습니다.

클라이언트 응용 프로그램은 [OAuth2 권한 부여](#authorization-grant) 흐름에 참여하기 위해 리소스 소유자에게 [권한 부여](#authorization)를 요청하고, 리소스 소유자 대신 API/데이터에 액세스할 수 있습니다. OAuth2 권한 부여 프레임워크는 해당 자격 증명의 기밀을 유지할 수 있는 클라이언트의 능력에 기반하여 "기밀"과 "공용"이란 [두 가지 유형의 클라이언트를 정의합니다][OAuth2-Client-Types]. 응용 프로그램은 웹 서버에서 실행되는 [웹 클라이언트(기밀)](#web-client), 장치에 설치된 [네이티브 클라이언트(공용)](#native-client) 또는 장치의 브라우저에서 실행되는 [사용자 에이전트 기반 클라이언트(공용)](#user-agent-based-client)를 구현할 수 있습니다.

## 동의
[클라이언트 응용 프로그램](#client-application)에 리소스 소유자 대신 보호된 리소스에 액세스할 수 있는 특정한 [사용 권한](#permissions)인 권한 부여를 부여하는 [리소스 소유자](#resource-owner)의 프로세스. 클라이언트가 요청한 사용 권한에 따라 관리자 또는 사용자에게 각기 자신의 조직/개인 데이터에 대한 액세스를 허용하는 데 동의하는지 묻는 메시지가 표시됩니다. [다중 테넌트](#multi-tenant-application) 시나리오에서 응용 프로그램의 [서비스 주체](#service-principal-object)는 또한 동의하는 사용자의 테넌트에도 기록됩니다.

## ID 토큰
[권한 부여 서버의](#authorization-server) [권한 부여 끝점](#authorization-endpoint)에 의해 제공된 [OpenID Connect][OpenIDConnect-ID-Token] [보안 토큰](#security-token)으로 최종 사용자 [리소스 소유자](#resource-owner)의 인증에 관련된 [클레임](#claim)을 포함합니다. 액세스 토큰과 마찬가지로 ID 토큰도 또한 디지털로 서명된 [JSON Web Token (JWT)][JWT]으로 표시됩니다. 그렇지만 액세스 토큰과는 달리 ID 토큰의 클레임은 리소스 액세스 및 특히 액세스 제어와 관련된 용도로 사용되지 않습니다.

자세한 내용은 [Azure AD 토큰 참조][AAD-Tokens-Claims]를 참조하세요.

## 다중 테넌트 응용 프로그램
클라이언트를 등록한 테넌트가 아닌 테넌트를 포함하여 Azure AD [테넌트](#tenant)에 프로비전된 사용자가 로그인하고 [동의](#consent)할 수 있도록 하는 [클라이언트 응용 프로그램](#client-application)의 클래스입니다. 반면 단일 테넌트로 등록된 응용 프로그램은 응용 프로그램이 등록된 동일한 테넌트에서 프로비전된 사용자 계정에서만 로그인하는 것을 허용합니다. [네이티브 클라이언트](#native-client) 응용 프로그램은 기본적으로 다중 테넌트인 반면 [웹 클라이언트](#web-client) 응용 프로그램은 단일 테넌트와 다중 테넌트 중에서 선택하는 기능이 있습니다.

자세한 내용은 [다중 테넌트 응용 프로그램 패턴을 사용하여 모든 Azure AD 사용자를 로그인하는 방법][AAD-Multi-Tenant-Overview]을 참조하세요.

## 네이티브 클라이언트
장치에 고유하게 설치된 [클라이언트 응용 프로그램](#client-application)의 유형. 모든 코드가 장치에서 실행되기 때문에 자격 증명을 비공개로/기밀로 저장할 수 없으므로 "공용" 클라이언트로 간주합니다. 더 자세한 내용은 [OAuth2 클라이언트 형식 및 프로필][OAuth2-Client-Types]을 참조하세요.

## 권한
[클라이언트 응용 프로그램](#client-application)은 사용 권한 요청을 선언하여 [리소스 서버](#resource-server)에 대한 액세스 권한을 얻습니다. 두 가지 유형이 있습니다.

- 하나는 "위임된" 사용 권한으로, 로그인된 [리소스 소유자](#resource-owner)로부터 위임된 권한 부여 하에 [범위 기반](#scopes) 액세스를 요청하며 런타임 시 리소스에 대해 클라이언트의 [액세스 토큰](#access-token)에서 ["scp" 클레임](#claim)으로 나타납니다.
- 또 하나는 "응용 프로그램" 사용 권한으로, 클라이언트 응용 프로그램의 자격 증명/ID 하에서 [역할 기반](#roles) 액세스를 요청하고, 런타임 시 리소스에 대해 클라이언트의 액세스 토큰에서 ["roles" 클레임](#claim)으로 나타납니다.

또한 [동의](#consent) 프로세스 동안 표면화되어 관리자 또는 리소스 소유자에게 자신의 테넌트에 있는 리소스에 대한 클라이언트 액세스를 허용/거부할 기회를 제공합니다.

사용 권한 요청은 [Azure 클래식 포털][AZURE-classic-portal]의 “응용 프로그램” / "구성" 탭에서 원하는 "위임된 권한" 및 "응용 프로그램 사용 권한"(후자는 전역 관리자 역할 필요)을 선택하여 “다른 응용 프로그램에 대한 권한” 아래에 구성됩니다. [공용 클라이언트](#client-application)는 자격 증명을 유지할 수 없으므로 위임된 사용 권한만을 요청할 수 있는 반면에 [기밀 클라이언트](#client-application)는 위임된 사용 권한 및 응용 프로그램 사용 권한을 모두 요청할 수 있습니다. 클라이언트의 [응용 프로그램 개체](#application-object)는 선언된 사용 권한을 [requiredResourceAccess 속성][AAD-Graph-App-Entity]에 저장할 수 있습니다.

## 리소스 소유자
[OAuth2 권한 부여 프레임워크][OAuth2-Role-Def]에 정의된 대로, 보호된 리소스에 대한 액세스 부여할 수 있는 엔터티. 리소스 소유자가 개인인 경우 최종 사용자라고 합니다. 예를 들어, [클라이언트 응용 프로그램](#client-application)이 [Microsoft Graph API][Microsoft-Graph]를 통해 사용자 사서함에 액세스하려 한다면 사서함의 리소스 소유자로부터 사용 권한 허가가 필요합니다.

## 리소스 서버
[OAuth2 권한 부여 프레임워크][OAuth2-Role-Def]에 정의된 대로, [액세스 토큰](#access-token)에 있는 [클라이언트 응용 프로그램](#client-application)에 의한 보호된 리소스 요청을 수락하고 응답할 수 있는, 보호된 리소스를 호스트하는 서버. 보호된 리소스 서버 또는 리소스 응용 프로그램이라고도 합니다.

리소스 서버는 API를 노출하고 OAuth 2.0 권한 부여 프레임워크를 사용하여 [범위](#scopes) 및 [역할](#roles)을 통해 보호된 리소스에 액세스를 강화합니다. Azure AD 테넌트 데이터에 대한 액세스를 제공하는 Azure AD Graph API 및 메일과 일정 등 데이터에 대한 액세스를 제공하는 Office 365 API를 예로 들 수 있습니다. 둘 다 [Microsoft Graph API][Microsoft-Graph]를 통해 액세스할 수 있습니다.

클라이언트 응용 프로그램과 마찬가지로 리소스 응용 프로그램 ID 구성은 Azure AD 테넌트에서 [등록](#application-registration)을 통해 설정되며 응용 프로그램과 서비스 주체 개체를 모두 제공 합니다. Azure AD Graph API와 같은 일부 Microsoft 제공 API에는 프로비전 중에 모든 테넌트에서 사용할 수 있도록 설정된 사전 등록된 서비스 주체가 있습니다.

## roles
[범위](#scopes)와 같이, 역할은 [리소스 서버](#resource-server)가 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 두 가지 유형이 있습니다. "사용자" 역할은 리소스에 대한 액세스를 필요로 하는 사용자/그룹에 대한 역할 기반 액세스 제어를 구현하는 반면에 "응용 프로그램" 역할은 액세스를 필요로 하는 [클라이언트 응용 프로그램](#client-application)에 대해 동일한 것을 구현합니다.

역할은 리소스 정의 문자열(예: "비용 승인자", "읽기 전용", "Directory.ReadWrite.All")로, 리소스의 [응용 프로그램 매니페스트](#application-manifest)를 통해 [Azure 클래식 포털][AZURE-classic-portal]에서 관리되며, 리소스의 [appRoles 속성][AAD-Graph-Sp-Entity]에 저장됩니다. Azure 클래식 포털은 "user" 역할에 사용자를 할당하고 클라이언트 [응용 프로그램 사용 권한](#permissions)을 구성하는 데 사용되어 "application" 역할에 액세스합니다.

Azure AD Graph API에 의해 노출된 응용 프로그램 역할에 대한 자세한 내용은 [Graph API 사용 권한 범위][AAD-Graph-Perm-Scopes]를 참조하세요. 단계별 구현 예제는 [Azure AD를 사용하여 클라우드 응용 프로그램에서 역할 기반 액세스 제어][Duyshant-Role-Blog]를 참조하세요.

## 범위
[역할](#roles)와 같이, 범위는 [리소스 서버](#resource-server)에 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 범위는 해당 소유자가 리소스에 대해 위임한 액세스를 부여한 [클라이언트 응용 프로그램](#client-application)에 대해 [범위 기반][OAuth2-Access-Token-Scopes] 액세스 제어를 구현하는 데 사용됩니다.

범위는 리소스 정의 문자열(예: "Mail.Read", "Directory.ReadWrite.All")로, 리소스의 [응용 프로그램 매니페스트](#application-manifest)를 통해 [Azure 클래식 포털][AZURE-classic-portal]에서 관리되며, 리소스의 [oauth2Permissions 속성][AAD-Graph-Sp-Entity]에 저장됩니다. 또한 Azure 클래식 포털은 클라이언트 응용 프로그램 [위임된 사용 권한](#permissions)을 구성하는 데 사용되어 범위에 액세스합니다.

모범 사례 명명 규칙은 "resource.operation.constraint" 형식을 사용 하는 것입니다. Azure AD Graph API에 의해 노출된 범위에 대한 자세한 내용은 [Graph API 사용 권한 범위][AAD-Graph-Perm-Scopes]를 참조하세요. Office 365 서비스에 의해 노출되는 범위는 [Office 365 API 사용 권한 참조][O365-Perm-Ref]를 참조하세요.

## 보안 토큰
OAuth2 토큰 또는 SAML 2.0 어설션과 같은 클레임을 포함한 서명된 문서입니다. OAuth2 [권한 부여](#authorization-grant)의 경우, [액세스 토큰](#access-token)(OAuth2)과 [ID 토큰](OpenID Connect)은 보안 토큰의 유형으로 둘 다 [JSON Web Token(JWT)][JWT]으로 구현됩니다.

## 서비스 주체 개체
[Azure 클래식 포털][AZURE-classic-portal]에서 응용 프로그램을 등록/업데이트할 때, 포털은 [응용 프로그램 개체](#application-object)와 해당 테넌트에 대한 각각의 서비스 주체 개체를 만들고 업데이트합니다. 응용 프로그램 개체는 응용 프로그램의 ID 구성을(관련된 응용 프로그램에게 액세스 권한이 부여된 모든 테넌트에 걸쳐) 전역으로 *정의*하며, 해당 서비스 주체 개체가 런타임에(특정 테넌트에서) 로컬로 사용되도록 *파생*되는 템플릿입니다.

자세한 내용은 [응용 프로그램 및 서비스 주체 개체][AAD-App-SP-Objects]를 참조하세요.

## 로그인
[보안 토큰](#security-token)을 획득하고 응용 프로그램 세션을 해당 상태로 범위 지정할 목적으로 최종 사용자 인증을 시작하고 관련 상태를 캡처하는 [클라이언트 응용 프로그램](#client-application)의 프로세스입니다. 상태는 사용자 프로필 정보, 토큰 클레임에서 파생된 정보 등과 같은 아티팩트를 포함할 수 있습니다.

응용 프로그램의 로그인 기능은 일반적으로 Single Sign-On(SSO)을 구현하는 데 사용됩니다. (처음 로그인에 대해)응용 프로그램에 액세스 권한을 가진 최종 사용자에 대한 진입점으로 "등록" 함수로 시작될 수도 있습니다. 등록 기능은 사용자에게 특유한 추가 상태를 수집하고 유지하기 위해 사용되며, [사용자 동의](#consent)를 필요로 할 수 있습니다.

## 로그 아웃
이 프로세스는 [로그인](#sign-in) 동안 [클라이언트 응용 프로그램](#client-application) 세션과 연결된 사용자 상태를 분리하여, 최종 사용자의 인증을 무효화합니다.

## tenant
Azure AD 디렉터리의 인스턴스는 Azure AD 테넌트라고 합니다. 다음을 비롯한 다양한 기능을 제공합니다.

- 통합 응용 프로그램에 대한 레지스트리 서비스
- 사용자 계정 및 등록된 응용 프로그램의 인증
- OAuth2 및 SAML을 비롯한 다양한 프로토콜을 지원하는 데 필요한 REST 끝점은 [권한 부여 끝점](#authorization-endpoint), [토큰 끝점](#token-endpoint) 및 [다중 테넌트 응용 프로그램](#multi-tenant-application)에서 사용하는 "일반" 끝점을 포함합니다.

테넌트는 구독을 프로비전하는 동안 Azure AD 또는 Office 365와 연결되어 구독에 대한 ID 및 액세스 관리 기능을 제공합니다. 테넌트에 대한 액세스 권한을 가질 수 있는 다양한 방법에 대한 자세한 내용은 [Azure Active Directory 테넌트를 얻는 방법][AAD-How-To-Tenant]을 참조하세요. 구독과 Azure AD 테넌트 간의 관계에 대한 자세한 내용은 [Azure Active Directory와 Azure 구독을 연결하는 방법][AAD-How-Subscriptions-Assoc]을 참조하세요.

## 토큰 끝점
OAuth2 [권한 부여](#authorization-grant)를 지원하기 위해 [권한 부여 서버](#authorization-server)에서 구현한 끝점 중 하나입니다. 권한 부여에 따라 [OpenID Connect][OpenIDConnect] 프로토콜과 함께 사용할 경우 [클라이언트](#client-application) 또는 [ID 토큰](#ID-token)에 대한 [액세스 토큰](#access-token)(및 관련된 "새로 고침" 토큰)을 얻기 위해 사용될 수 있습니다.

## 사용자 에이전트 기반 클라이언트
웹 서버에서 코드를 다운로드하고 단일 페이지 응용 프로그램(SPA)와 같은 사용자 에이전트(예: 웹 브라우저) 내에서 실행하는 [클라이언트 응용 프로그램](#client-application)의 유형입니다. 모든 코드가 장치에서 실행되기 때문에 자격 증명을 비공개로/기밀로 저장할 수 없으므로 "공용" 클라이언트로 간주합니다. 더 자세한 내용은 [OAuth2 클라이언트 형식 및 프로필][OAuth2-Client-Types]을 참조하세요.

## 사용자 주체
서비스 주체 개체가 응용 프로그램 인스턴스를 나타내는 데 사용되는 방식과 유사하게 사용자 계정 개체는 사용자를 나타내는 다른 유형의 보안 주체입니다. Azure AD Graph [사용자 엔터티][AAD-Graph-User-Entity]는 이름과 성, 사용자 주체 이름, 디렉터리 역할 멤버 자격 등 사용자 관련 속성을 포함하는 사용자 개체에 대한 스키마를 정의합니다. Azure AD에 대한 사용자 ID 구성을 제공하여 런타임 시 사용자 주체를 설정합니다. 사용자 주체는 Single Sign-On, [동의](#consent) 위임 기록, 액세스 제어 결정에 인증된 사용자를 나타내는 데 사용됩니다.

## 웹 클라이언트
웹 서버에 대한 모든 코드를 실행하고 서버에서 해당 자격 증명을 안전하게 저장하여 "기밀" 클라이언트로 작동하도록 하는 [클라이언트 응용 프로그램](#client-application) 유형입니다. 더 자세한 내용은 [OAuth2 클라이언트 형식 및 프로필][OAuth2-Client-Types]을 참조하세요.

## 다음 단계
[Azure AD 개발자 가이드][AAD-Dev-Guide]는 [응용 프로그램 통합][AAD-How-To-Integrate] 개요 및 [Azure AD 인증 및 지원되는 인증 시나리오][AAD-Auth-Scenarios]의 기본을 포함하는 Azure AD 개발 관련 모든 항목에 대해 사용하는 포털입니다.

다음 DISQUS 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ./active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/ko-KR/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken

<!---HONumber=AcomDC_0831_2016-->