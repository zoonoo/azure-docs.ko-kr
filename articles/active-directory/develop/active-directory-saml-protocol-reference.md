---
title: Microsoft id 플랫폼에서 SAML 프로토콜을 사용 하는 방법
description: 이 문서에서는 Azure Active Directory에서 Single Sign-on 및 Single Sign-Out SAML 프로필에 대한 개요를 제공합니다.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 06f80f94be25e42c9e8f0270e6cb15aca086ae18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87552801"
---
# <a name="how-microsoft-identity-platform-uses-the-saml-protocol"></a>Microsoft id 플랫폼에서 SAML 프로토콜을 사용 하는 방법

Microsoft id 플랫폼은 SAML 2.0 프로토콜을 사용 하 여 응용 프로그램에서 사용자에 게 Single Sign-On 환경을 제공할 수 있도록 합니다. Azure AD의 [Single Sign-On](single-sign-on-saml-protocol.md) 및 [Single Sign-Out](single-sign-out-saml-protocol.md) SAML 프로필은 ID 공급자 서비스에서 SAML 어설션, 프로토콜 및 바인딩이 사용되는 방법을 설명합니다.

SAML 프로토콜에는 id 공급자 (Microsoft identity platform) 및 서비스 공급자 (응용 프로그램)가 자신에 대 한 정보를 교환 해야 합니다.

애플리케이션이 Azure AD에 등록된 경우 앱 개발자는 페더레이션 관련 정보를 Azure AD에 등록합니다. 이 정보에는 애플리케이션의 **리디렉션 URI** 및 **메타데이터 URI**가 포함됩니다.

Microsoft id 플랫폼은 클라우드 서비스의 **메타 데이터 uri** 를 사용 하 여 서명 키와 로그 아웃 uri를 검색 합니다. 고객은 **Azure AD -> 앱 등록**에서 앱을 열고 **설정 -> 속성**에서 로그아웃 URL을 업데이트할 수 있습니다. 이러한 방식으로 Microsoft id 플랫폼은 올바른 URL에 응답을 보낼 수 있습니다. 

Azure Active Directory는 테넌트별 및 공통(테넌트 독립적) single sign-on 및 single sign-out 엔드포인트를 노출합니다. 이러한 URL은 단순히 식별자가 아니라 주소 지정이 가능한 위치를 나타내므로 엔드포인트로 이동하여 메타데이터를 읽을 수 있습니다.

* 테넌트별 엔드포인트는 `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`에 있습니다. *\<TenantDomainName>* 자리 표시자는 AZURE AD 테 넌 트의 등록 된 도메인 이름 또는 TENANTID GUID를 나타냅니다. 예를 들어 contoso.com 테넌트의 페더레이션 메타데이터는 https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml에 있습니다.

* 테넌트 독립적 엔드포인트는 `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`에 있습니다. 이 엔드포인트 주소에는 테넌트 도메인 이름 또는 ID 대신 **common**이 나타납니다.

Azure AD에서 게시 하는 페더레이션 메타 데이터 문서에 대 한 자세한 내용은 [페더레이션 메타 데이터](../azuread-dev/azure-ad-federation-metadata.md)를 참조 하세요.
