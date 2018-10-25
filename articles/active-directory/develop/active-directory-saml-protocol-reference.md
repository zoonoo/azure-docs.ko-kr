---
title: Azure AD에서 SAML 프로토콜을 사용하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Active Directory에서 Single Sign-on 및 Single Sign-Out SAML 프로필에 대한 개요를 제공합니다.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: b9ae7cf9d6fd383c7cdfa3957b5d9b94c7207d50
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900658"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Azure AD에서 SAML 프로토콜을 사용하는 방법

Azure AD(Azure Active Directory)는 SAML 2.0 프로토콜을 사용하여 응용 프로그램에서 사용자에게 Single Sign-On 환경을 제공할 수 있도록 합니다. Azure AD의 [Single Sign-On](single-sign-on-saml-protocol.md) 및 [Single Sign-Out](single-sign-out-saml-protocol.md) SAML 프로필은 ID 공급자 서비스에서 SAML 어설션, 프로토콜 및 바인딩이 사용되는 방법을 설명합니다.

SAML 프로토콜에는 자신에 대한 정보를 교환하기 위해 ID 공급자(Azure AD)와 서비스 공급자(응용 프로그램)가 필요합니다.

응용 프로그램이 Azure AD에 등록된 경우 앱 개발자는 페더레이션 관련 정보를 Azure AD에 등록합니다. 이 정보에는 응용 프로그램의 **리디렉션 URI** 및 **메타데이터 URI**가 포함됩니다.

Azure AD는 클라우드 서비스의 **메타데이터 URI**를 사용하여 서명 키와 로그아웃 URI를 검색합니다. 응용 프로그램에서 메타데이터 URI를 지원하지 않으면 개발자가 Microsoft 지원에 문의하여 로그아웃 URI 및 서명 키를 제공해야 합니다.

Azure Active Directory는 테넌트별 및 공통(테넌트 독립적) single sign-on 및 single sign-out 엔드포인트를 노출합니다. 이러한 URL은 단순히 식별자가 아니라 주소 지정이 가능한 위치를 나타내므로 엔드포인트로 이동하여 메타데이터를 읽을 수 있습니다.

* 테넌트별 엔드포인트는 `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`에 있습니다. *<TenantDomainName>* 자리 표시자는 Azure AD 테넌트의 등록된 도메인 이름 또는 TenantID GUID를 나타냅니다. 예를 들어 contoso.com 테넌트의 페더레이션 메타데이터는 https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml에 있습니다.

* 테넌트 독립적 엔드포인트는 `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`에 있습니다. 이 엔드포인트 주소에는 테넌트 도메인 이름 또는 ID 대신 **common**이 나타납니다.

Azure AD에서 게시하는 페더레이션 메타데이터 문서에 대한 자세한 내용은 [페더레이션 메타데이터](azure-ad-federation-metadata.md)를 참조하세요.
