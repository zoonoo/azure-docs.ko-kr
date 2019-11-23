---
title: Microsoft identity platform developer glossary | Azure
description: A list of terms for commonly used Microsoft identity platform developer concepts and features.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221a491abad6c11ee12c75b1d69f1263f4abddc4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322609"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Microsoft identity platform developer glossary

This article contains definitions for some of the core developer concepts and terminology, which are helpful when learning about application development using Microsoft identity platform.

## <a name="access-token"></a>액세스 토큰

[보호된 리소스 서버](#resource-server)에 액세스하기 위해 [권한 부여 서버](#authorization-server)에서 발급하고, [클라이언트 애플리케이션](#client-application)에서 사용하는 [보안 토큰](#security-token)의 한 종류. Typically in the form of a [JSON Web Token (JWT)][JWT], the token embodies the authorization granted to the client by the [resource owner](#resource-owner), for a requested level of access. 토큰은 주체에 관한 모든 적용 가능한 [클레임](#claim)을 포함하여 클라이언트 애플리케이션이 지정된 리소스에 액세스할 때 자격 증명의 형식으로 사용할 수 있습니다. 또는 이는 리소스 소유자의 필요를 제거하여 클라이언트에 자격 증명을 노출합니다.

액세스 토큰은 자격 증명이 표시되는 방식에 따라 때로 "사용자 + 앱" 또는 "앱 전용"이라고 합니다. 예를 들어 클라이언트 애플리케이션이 다음을 사용할 경우.

* ["인증 코드" 권한 부여](#authorization-grant) - 최종 사용자는 리소스 소유자로서 우선 권한 부여를 하여 클라이언트가 리소스에 액세스할 수 있도록 권한 부여를 위임합니다. 클라이언트는 액세스 토큰을 가져올 때 나중에 인증합니다. 토큰은 클라이언트 애플리케이션에 권한 부여하는 사용자와 애플리케이션을 나타냄으로 때로 더 구체적으로 "사용자 + 앱"이라고 합니다.
* ["클라이언트 자격 증명" 권한 부여](#authorization-grant) - 클라이언트에서 리소스 소유자의 인증/권한 부여 없이 작동하는 유일한 인증을 제공하므로 때로는 토큰을 “앱 전용” 토큰이라고도 할 수 있습니다.

See [Microsoft identity platform Token Reference][AAD-Tokens-Claims] for more details.

## <a name="application-id-client-id"></a>application ID (client ID)

Azure AD가 애플리케이션 등록 시 부여하는 고유 식별자로, 해당 애플리케이션과 그 구성을 식별하는 데 사용됩니다. This application ID ([client ID](https://tools.ietf.org/html/rfc6749#page-15)) is used when performing authentication requests and is provided to the authentication libraries in development time. The application ID (client ID) is not a secret.

## <a name="application-manifest"></a>애플리케이션 매니페스트

A feature provided by the [Azure portal][AZURE-portal], which produces a JSON representation of the application's identity configuration, used as a mechanism for updating its associated [Application][AAD-Graph-App-Entity] and [ServicePrincipal][AAD-Graph-Sp-Entity] entities. See [Understanding the Azure Active Directory application manifest][AAD-App-Manifest] for more details.

## <a name="application-object"></a>애플리케이션 개체

When you register/update an application in the [Azure portal][AZURE-portal], the portal creates/updates both an application object and a corresponding [service principal object](#service-principal-object) for that tenant. 애플리케이션 개체는 애플리케이션의 ID 구성을 전역적으로(액세스하는 모든 테넌트에 걸쳐) *정의*하며, 해당 서비스 주체 개체가 런타임 시 로컬로(특정 테넌트에서) 사용되도록 *파생*되는 템플릿을 제공합니다.

For more information, see [Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="application-registration"></a>애플리케이션 등록

애플리케이션을 Azure AD와 통합하고 ID 및 액세스 관리 기능을 Azure AD에 위임할 수 있도록 허용하기 위해 Azure AD [테넌트](#tenant)를 사용하여 등록해야 합니다. Azure Ad를 사용하여 애플리케이션을 등록할 때 애플리케이션에 대한 ID 구성을 제공하여 Azure AD와 통합하고 다음과 같은 기능을 사용할 수 있도록 허용합니다.

* Robust management of Single Sign-On using Azure AD Identity Management and [OpenID Connect][OpenIDConnect] protocol implementation
* Brokered access to [protected resources](#resource-server) by [client applications](#client-application), via OAuth 2.0 [authorization server](#authorization-server)
* [동의 프레임워크](#consent) - 리소스 소유자 권한 부여에 따라 클라이언트의 보호된 리소스에 대한 액세스 관리

See [Integrating applications with Azure Active Directory][AAD-Integrating-Apps] for more details.

## <a name="authentication"></a>authentication

ID 및 액세스 제어에 사용할 보안 주체를 만들기 위한 기반을 제공하도록 당사자에게 합법적인 자격 증명을 하게 챌린지하는 작업. 예를 들어 [OAuth2 권한 부여](#authorization-grant) 시 당사자 인증은 사용된 권한 부여에 따라 [리소스 소유자](#resource-owner) 또는 [클라이언트 애플리케이션](#client-application)의 역할을 수행합니다.

## <a name="authorization"></a>authorization

인증된 보안 주체에게 작업을 수행할 수 있게 사용 권한을 부여하는 작업. Azure AD 프로그래밍 모델의 기본 사용 사례가 두 가지 있습니다.

* [OAuth2 권한 부여](#authorization-grant) 흐름 동안: [리소스 소유자](#resource-owner)가 [클라이언트 애플리케이션](#client-application)에 권한을 부여하여 클라이언트에서 리소스 소유자의 리소스에 액세스할 수 있도록 허용합니다.
* 클라이언트에서 리소스에 액세스하는 동안: [리소스 서버](#resource-server)에서 구현하는 대로 [액세스 토큰](#access-token)에 있는 [클레임](#claim) 값을 기반으로 하여 액세스 제어를 결정합니다.

## <a name="authorization-code"></a>인증 코드

네 가지 OAuth2 [권한 부여](#authorization-grant) 중 하나인 "인증 코드(Authorization Code)" 흐름의 일부로 [권한 부여 엔드포인트](#authorization-endpoint)에서 [클라이언트 애플리케이션](#client-application)에 제공하는 수명이 짧은 "토큰". 코드가 [리소스 소유자](#resource-owner)의 인증에 대한 응답으로 클라이언트 애플리케이션에 반환되며 이는 리소스 소유자가 요청된 리소스에 액세스하는 권한을 위임하였음을 나타냅니다. 흐름의 일부로, 코드는 나중에 [액세스 토큰](#access-token)으로 교환됩니다.

## <a name="authorization-endpoint"></a>권한 부여 엔드포인트

[권한 부여 서버](#authorization-server)에서 구현하는 엔드포인트 중 하나로 OAuth2 권한 부여 흐름 중에 [권한 부여](#authorization-grant)를 제공하기 위해 [리소스 소유자](#resource-owner)와 상호 작용하는 데 사용됩니다. 사용된 권한 부여 흐름에 따라 [인증 코드](#authorization-code) 또는 [보안 토큰](#security-token)을 포함하여 제공되는 실제 권한 부여는 다를 수 있습니다.

See the OAuth2 specification's [authorization grant types][OAuth2-AuthZ-Grant-Types] and [authorization endpoint][OAuth2-AuthZ-Endpoint] sections, and the [OpenIDConnect specification][OpenIDConnect-AuthZ-Endpoint] for more details.

## <a name="authorization-grant"></a>권한 부여

보호된 리소스에 액세스할 수 있도록 [클라이언트 애플리케이션](#client-application)에 부여된 [리소스 소유자의](#resource-owner)[권한 부여](#authorization)를 나타내는 자격 증명. A client application can use one of the [four grant types defined by the OAuth2 Authorization Framework][OAuth2-AuthZ-Grant-Types] to obtain a grant, depending on client type/requirements: "authorization code grant", "client credentials grant", "implicit grant", and "resource owner password credentials grant". 클라이언트에 반환되는 자격 증명은 사용되는 권한 부여 유형에 따라 [액세스 토큰](#access-token) 또는 [인증 코드](#authorization-code)(나중에 액세스 토큰과 교환됨) 중 하나입니다.

## <a name="authorization-server"></a>권한 부여 서버

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], the server responsible for issuing access tokens to the [client](#client-application) after successfully authenticating the [resource owner](#resource-owner) and obtaining its authorization. [클라이언트 애플리케이션](#client-application)에서는 런타임 시 [권한 부여](#authorization-grant)에서 정의한 OAuth2에 따라 [권한 부여](#authorization-endpoint) 및 [토큰](#token-endpoint) 엔드포인트를 통해 권한 부여 서버와 상호 작용합니다.

In the case of Microsoft identity platform application integration, Microsoft identity platform implements the authorization server role for Azure AD applications and Microsoft service APIs, for example [Microsoft Graph APIs][Microsoft-Graph].

## <a name="claim"></a>클레임

[보안 토큰](#security-token)에는 하나의 엔터티(예: [클라이언트 애플리케이션](#client-application) 또는 [리소스 소유자](#resource-owner))에 대한 어설션을 다른 엔터티(예: [리소스 서버](#resource-server))에 제공하는 클레임이 포함됩니다. 클레임은 토큰 주체(예를 들어 [권한 부여 서버](#authorization-server)에 의해 인증된 보안 주체)에 관한 팩트를 릴레이하는 이름/값 쌍입니다. 지정된 토큰에 있는 클레임은 토큰 형식, 주체 인증에 사용되는 자격 증명 유형, 애플리케이션 구성 등을 포함한 여러 변수에 따라 다릅니다.

See [Microsoft identity platform token reference][AAD-Tokens-Claims] for more details.

## <a name="client-application"></a>클라이언트 애플리케이션

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], an application that makes protected resource requests on behalf of the [resource owner](#resource-owner). &quot;클라이언트&quot;라는 용어는 특정 하드웨어 구현 특성(예: 애플리케이션이 서버, 데스크톱, 또는 다른 디바이스에서 실행할지 여부)을 의미하지 않습니다.

클라이언트 애플리케이션은 [OAuth2 권한 부여](#authorization-grant) 흐름에 참여하기 위해 리소스 소유자에게 [권한 부여](#authorization)를 요청하고 리소스 소유자를 대신하여 API/데이터에 액세스할 수 있습니다. The OAuth2 Authorization Framework [defines two types of clients][OAuth2-Client-Types], "confidential" and "public", based on the client's ability to maintain the confidentiality of its credentials. 애플리케이션은 웹 서버에서 실행되는 [웹 클라이언트(기밀)](#web-client), 디바이스에 설치된 [네이티브 클라이언트(공용)](#native-client) 또는 디바이스의 브라우저에서 실행되는 [사용자 에이전트 기반 클라이언트(공용)](#user-agent-based-client)를 구현할 수 있습니다.

## <a name="consent"></a>동의

[클라이언트 애플리케이션](#client-application)에 리소스 소유자를 대신하여 보호된 리소스에 액세스할 수 있는 특정 [권한](#permissions)을 부여하는 [리소스 소유자](#resource-owner)의 프로세스. 클라이언트가 요청한 사용 권한에 따라 관리자 또는 사용자에게 각기 자신의 조직/개인 데이터에 대한 액세스를 허용하는 데 동의하는지 묻는 메시지가 표시됩니다. [다중 테넌트](#multi-tenant-application) 시나리오에서도 동의하는 사용자의 테넌트에 애플리케이션의 [서비스 주체](#service-principal-object)를 기록합니다.

자세한 정보는 [동의 프레임워크](consent-framework.md)를 참조하세요.

## <a name="id-token"></a>ID 토큰

An [OpenID Connect][OpenIDConnect-ID-Token] [security token](#security-token) provided by an [authorization server's](#authorization-server) [authorization endpoint](#authorization-endpoint), which contains [claims](#claim) pertaining to the authentication of an end user [resource owner](#resource-owner). Like an access token, ID tokens are also represented as a digitally signed [JSON Web Token (JWT)][JWT]. 그렇지만 액세스 토큰과는 달리 ID 토큰의 클레임은 리소스 액세스 및 특히 액세스 제어와 관련된 용도로 사용되지 않습니다.

See [Microsoft identity platform token reference][AAD-Tokens-Claims] for more details.

## <a name="microsoft-identity-platform"></a>Microsoft ID 플랫폼

Microsoft ID 플랫폼은 Azure AD(Azure Active Directory) ID 서비스와 개발자 플랫폼의 발전된 형태입니다. 이 플랫폼을 사용하면 개발자가 모든 Microsoft ID에 로그인하고, Microsoft Graph, 기타 Microsoft API 또는 개발자가 빌드한 API를 호출하기 위한 토큰을 가져오는 애플리케이션을 빌드할 수 있습니다. 인증 서비스, 라이브러리, 애플리케이션 등록 및 구성, 완전한 개발자 설명서, 코드 샘플 및 기타 개발자 콘텐츠로 구성된 모든 기능을 갖춘 플랫폼입니다. Microsoft ID 플랫폼은 OAuth 2.0 및 OpenID Connect 등의 산업 표준 프로토콜을 지원합니다. 자세한 내용은 [Microsoft ID 플랫폼 정보](about-microsoft-identity-platform.md)를 참조하세요.

## <a name="multi-tenant-application"></a>다중 테넌트 애플리케이션

클라이언트를 등록한 테넌트 이외의 테넌트를 포함하여 Azure AD [테넌트](#tenant)에 프로비전된 사용자가 로그인하고 [동의](#consent)할 수 있도록 하는 애플리케이션의 한 종류입니다. [네이티브 클라이언트](#native-client) 애플리케이션은 기본적으로 다중 테넌트인 반면 [웹 클라이언트](#web-client) 및 [웹 리소스/API](#resource-server) 애플리케이션은 단일 테넌트와 다중 테넌트 중에서 선택하는 기능이 있습니다. 반면 단일 테넌트로 등록된 웹 애플리케이션은 애플리케이션이 등록된 동일한 테넌트에서 프로비전된 사용자 계정에서만 로그인하는 것을 허용합니다.

See [How to sign in any Azure AD user using the multi-tenant application pattern][AAD-Multi-Tenant-Overview] for more details.

## <a name="native-client"></a>네이티브 클라이언트

디바이스에 고유하게 설치된 [클라이언트 애플리케이션](#client-application) 의 유형. 모든 코드가 디바이스에서 실행되기 때문에 자격 증명을 비공개로/기밀로 저장할 수 없으므로 "공용" 클라이언트로 간주합니다. See [OAuth2 client types and profiles][OAuth2-Client-Types] for more details.

## <a name="permissions"></a>권한

[클라이언트 애플리케이션](#client-application)은 권한 요청을 선언하여 [리소스 서버](#resource-server)에 대한 액세스 권한을 얻습니다. 두 가지 유형이 있습니다.

* 하나는 "위임된" 권한으로서 로그인된 [리소스 소유자](#resource-owner)로부터 위임된 권한 부여를 사용하여 [범위 기반](#scopes) 액세스를 지정하며, 런타임 시 클라이언트의 [액세스 토큰](#access-token)에서 ["scp" 클레임](#claim)으로 리소스에 제공됩니다.
* 또 하나는 "애플리케이션" 권한으로서 클라이언트 애플리케이션의 자격 증명/ID를 사용하여 [역할 기반](#roles) 액세스를 지정하며, 런타임 시 클라이언트의 액세스 토큰에서 ["역할" 클레임](#claim)으로 리소스에 제공됩니다.

또한 [동의](#consent) 프로세스 동안 표면화되어 관리자 또는 리소스 소유자에게 자신의 테넌트에 있는 리소스에 대한 클라이언트 액세스를 허용/거부할 기회를 제공합니다.

Permission requests are configured on the **API permissions** page for an application in the [Azure portal][AZURE-portal], by selecting the desired "Delegated Permissions" and "Application Permissions" (the latter requires membership in the Global Admin role). [공용 클라이언트](#client-application)는 자격 증명을 안전하게 유지할 수 없으므로 위임된 권한만 요청할 수 있는 반면에 [기밀 클라이언트](#client-application)는 위임된 권한 및 애플리케이션 권한을 모두 요청할 수 있습니다. The client's [application object](#application-object) stores the declared permissions in its [requiredResourceAccess property][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>리소스 소유자

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], an entity capable of granting access to a protected resource. 리소스 소유자가 개인인 경우 최종 사용자라고 합니다. For example, when a [client application](#client-application) wants to access a user's mailbox through the [Microsoft Graph API][Microsoft-Graph], it requires permission from the resource owner of the mailbox.

## <a name="resource-server"></a>리소스 서버

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], a server that hosts protected resources, capable of accepting and responding to protected resource requests by [client applications](#client-application) that present an [access token](#access-token). 보호된 리소스 서버 또는 리소스 애플리케이션이라고도 합니다.

리소스 서버는 API를 공개하고 OAuth 2.0 권한 부여 프레임워크를 사용하여 [범위](#scopes) 및 [역할](#roles)을 통해 보호된 리소스에 대한 액세스를 수행합니다. Azure AD 테넌트 데이터에 대한 액세스를 제공하는 Azure AD Graph API 및 메일과 일정 등 데이터에 대한 액세스를 제공하는 Office 365 API를 예로 들 수 있습니다. Both of these are also accessible via the [Microsoft Graph API][Microsoft-Graph].

클라이언트 애플리케이션과 마찬가지로 리소스 애플리케이션 ID 구성은 Azure AD 테넌트에서 [등록](#application-registration)을 통해 설정되며 애플리케이션과 서비스 주체 개체를 모두 제공합니다. Azure AD Graph API와 같은 일부 Microsoft 제공 API에는 프로비전 중에 모든 테넌트에서 사용할 수 있도록 설정된 사전 등록된 서비스 주체가 있습니다.

## <a name="roles"></a>roles

[범위](#scopes)와 마찬가지로 역할은 [리소스 서버](#resource-server)에서 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 여기에는 두 가지 유형이 있습니다. "사용자" 역할은 리소스에 대한 액세스 권한이 필요한 사용자/그룹의 역할 기반 액세스 제어를 구현하는 반면, "애플리케이션" 역할은 액세스 권한이 필요한 [클라이언트 애플리케이션](#client-application)에 대해 동일한 제어를 구현합니다.

Roles are resource-defined strings (for example "Expense approver", "Read-only", "Directory.ReadWrite.All"), managed in the [Azure portal][AZURE-portal] via the resource's [application manifest](#application-manifest), and stored in the resource's [appRoles property][AAD-Graph-Sp-Entity]. Azure Portal은 "user" 역할에 사용자를 할당하고 클라이언트 [애플리케이션 사용 권한](#permissions)을 구성하는 데 사용되어 "application" 역할에 액세스합니다.

For a detailed discussion of the application roles exposed by Azure AD's Graph API, see [Graph API Permission Scopes][AAD-Graph-Perm-Scopes]. For a step-by-step implementation example, see [Manage access using RBAC and the Azure portal][AAD-RBAC].

## <a name="scopes"></a>범위

[역할](#roles)과 마찬가지로 범위는 [리소스 서버](#resource-server)에서 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. Scopes are used to implement [scope-based][OAuth2-Access-Token-Scopes] access control, for a [client application](#client-application) that has been given delegated access to the resource by its owner.

Scopes are resource-defined strings (for example "Mail.Read", "Directory.ReadWrite.All"), managed in the [Azure portal][AZURE-portal] via the resource's [application manifest](#application-manifest), and stored in the resource's [oauth2Permissions property][AAD-Graph-Sp-Entity]. 또한 Azure Portal은 클라이언트 애플리케이션 [위임된 사용 권한](#permissions)에서 범위에 액세스하도록 구성하는 데도 사용됩니다.

모범 사례 명명 규칙은 "resource.operation.constraint" 형식을 사용 하는 것입니다. For a detailed discussion of the scopes exposed by Azure AD's Graph API, see [Graph API Permission Scopes][AAD-Graph-Perm-Scopes]. For scopes exposed by Office 365 services, see [Office 365 API permissions reference][O365-Perm-Ref].

## <a name="security-token"></a>보안 토큰

OAuth2 토큰 또는 SAML 2.0 어설션과 같은 클레임을 포함한 서명된 문서입니다. For an OAuth2 [authorization grant](#authorization-grant), an [access token](#access-token) (OAuth2) and an [ID Token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) are types of security tokens, both of which are implemented as a [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>서비스 주체 개체

When you register/update an application in the [Azure portal][AZURE-portal], the portal creates/updates both an [application object](#application-object) and a corresponding service principal object for that tenant. 애플리케이션 개체는 애플리케이션의 ID 구성을 전역적으로(액세스 권한이 관련된 애플리케이션에 부여된 모든 테넌트에 걸쳐) *정의*하며, 해당 서비스 주체 개체가 런타임 시 로컬로(특정 테넌트에서) 사용되도록 *파생*되는 템플릿입니다.

For more information, see [Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="sign-in"></a>로그인

[보안 토큰](#security-token)을 획득하고 애플리케이션 세션의 범위를 해당 상태로 지정할 목적으로 최종 사용자 인증을 시작하고 관련 상태를 캡처하는 [클라이언트 애플리케이션](#client-application)의 프로세스. 상태는 사용자 프로필 정보, 토큰 클레임에서 파생된 정보 등과 같은 아티팩트를 포함할 수 있습니다.

애플리케이션의 로그인 기능은 일반적으로 Single Sign-On(SSO)을 구현하는 데 사용됩니다. (처음 로그인에 대해)애플리케이션에 액세스 권한을 가진 최종 사용자에 대한 진입점으로 "등록" 함수로 시작될 수도 있습니다. 등록 기능은 사용자에게 특유한 추가 상태를 수집하고 유지하기 위해 사용되며, [사용자 동의](#consent)를 필요로 할 수 있습니다.

## <a name="sign-out"></a>로그 아웃

[로그인](#sign-in) 중 [클라이언트 애플리케이션](#client-application) 세션과 연결된 사용자 상태를 분리하여 최종 사용자의 인증을 무효화하는 프로세스

## <a name="tenant"></a>tenant

Azure AD 디렉터리의 인스턴스는 Azure AD 테넌트라고 합니다. 다음과 같은 여러 기능을 제공합니다.

* 통합 애플리케이션에 대한 레지스트리 서비스
* 사용자 계정 및 등록된 애플리케이션의 인증
* OAuth2 및 SAML을 포함한 다양한 프로토콜을 지원하는 데 필요한 REST 엔드포인트([권한 부여 엔드포인트](#authorization-endpoint), [토큰 엔드포인트](#token-endpoint) 및 [다중 테넌트 애플리케이션](#multi-tenant-application)에서 사용하는 "일반" 엔드포인트 포함).

Azure AD 테넌트는 등록 시 Azure 및 Office 365 구독으로 생성/연결되어 구독에 대한 ID 및 액세스 관리 기능을 제공합니다. Azure 구독 관리자는 Azure Portal을 통해 Azure AD 테넌트를 추가로 만들 수도 있습니다. See [How to get an Azure Active Directory tenant][AAD-How-To-Tenant] for details on the various ways you can get access to a tenant. See [How Azure subscriptions are associated with Azure Active Directory][AAD-How-Subscriptions-Assoc] for details on the relationship between subscriptions and an Azure AD tenant.

## <a name="token-endpoint"></a>토큰 엔드포인트

OAuth2 [권한 부여](#authorization-grant)를 지원하기 위해 [권한 부여 서버](#authorization-server)에서 구현하는 엔드포인트 중 하나. Depending on the grant, it can be used to acquire an [access token](#access-token) (and related "refresh" token) to a [client](#client-application), or [ID token](#id-token) when used with the [OpenID Connect][OpenIDConnect] protocol.

## <a name="user-agent-based-client"></a>사용자 에이전트 기반 클라이언트

웹 서버에서 코드를 다운로드하고 SPA(단일 페이지 애플리케이션)와 같은 사용자 에이전트(예: 웹 브라우저) 내에서 실행하는 [클라이언트 애플리케이션](#client-application)의 유형입니다. 모든 코드가 디바이스에서 실행되기 때문에 자격 증명을 비공개로/기밀로 저장할 수 없으므로 "공용" 클라이언트로 간주합니다. For more information, see [OAuth2 client types and profiles][OAuth2-Client-Types].

## <a name="user-principal"></a>사용자 주체

서비스 주체 개체가 애플리케이션 인스턴스를 나타내는 데 사용되는 방식과 유사하게 사용자 계정 개체는 사용자를 나타내는 다른 유형의 보안 주체입니다. The Azure AD Graph [User entity][AAD-Graph-User-Entity] defines the schema for a user object, including user-related properties such as first and last name, user principal name, directory role membership, etc. This provides the user identity configuration for Azure AD to establish a user principal at run-time. 사용자 주체는 Single Sign-On, [동의](#consent) 위임 기록, 액세스 제어 결정 등을 위해 인증된 사용자를 나타내는 데 사용됩니다.

## <a name="web-client"></a>웹 클라이언트

웹 서버에 대한 모든 코드를 실행하고 서버에서 해당 자격 증명을 안전하게 저장하여 "기밀" 클라이언트로 작동하도록 하는 [클라이언트 애플리케이션](#client-application) 유형입니다. For more information, see [OAuth2 client types and profiles][OAuth2-Client-Types].

## <a name="next-steps"></a>다음 단계

The [Microsoft identity platform Developer's Guide][AAD-Dev-Guide] is the landing page to use for all Microsoft identity platform development-related topics, including an overview of [application integration][AAD-How-To-Integrate] and the basics of [Microsoft identity platform authentication and supported authentication scenarios][AAD-Auth-Scenarios]. [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=)에서도 코드 샘플과 빠른 시작 자습서를 확인할 수 있습니다.

다음 설명 섹션을 사용하여 피드백을 제공하고 새 정의 및 기존 정의 업데이트 요청을 포함하여 이 콘텐츠를 구체화하고 형성하는 데 도움을 줍니다.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: /graph/permissions-reference
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
