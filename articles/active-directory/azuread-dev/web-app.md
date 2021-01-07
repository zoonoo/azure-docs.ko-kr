---
title: Azure Active Directory의 웹앱
description: 웹앱을 소개하고 이 앱 유형에 대한 프로토콜 흐름, 등록 및 토큰 만료 기본 사항을 설명합니다.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80154409"
---
# <a name="web-apps"></a>웹 앱

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

웹앱은 웹 브라우저의 사용자를 웹 애플리케이션에 인증하는 애플리케이션입니다. 이 시나리오에서 웹 응용 프로그램은 사용자의 브라우저가 Azure AD에 로그인 하도록 지시 합니다. Azure AD는 보안 토큰의 사용자에 대 한 클레임을 포함 하는 사용자의 브라우저를 통해 로그인 응답을 반환 합니다. 이 시나리오에서는 OpenID Connect, SAML 2.0 및 WS-Federation 프로토콜을 사용한 로그온이 지원됩니다.

## <a name="diagram"></a>다이어그램

![브라우저-웹 애플리케이션의 인증 흐름](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>프로토콜 흐름

1. 사용자가 애플리케이션을 방문할 때 로그인해야 하는 경우 로그인 요청을 통해 Azure AD의 인증 엔드포인트로 리디렉션됩니다.
1. 사용자가 로그인 페이지에서 로그인합니다.
1. 인증에 성공 하면 Azure AD는 인증 토큰을 만들고 Azure Portal에 구성 된 응용 프로그램의 회신 URL에 대 한 로그인 응답을 반환 합니다. 프로덕션 애플리케이션의 경우 이 회신 URL은 HTTPS여야 합니다. 반환된 토큰에는 애플리케이션이 토큰의 유효성을 검사하는 데 필요한, 사용자 및 Azure AD에 대한 클레임이 포함됩니다.
1. 애플리케이션이 Azure AD에 대한 페더레이션 메타데이터 문서에서 제공되는 공개 서명 키 및 발급자 정보를 사용하여 토큰의 유효성을 검사합니다. 애플리케이션이 토큰의 유효성을 검사한 후에 사용자의 새 세션을 시작합니다. 이 세션에서 사용자는 애플리케이션이 만료될 때까지 애플리케이션에 액세스할 수 있습니다.

## <a name="code-samples"></a>코드 샘플

웹 브라우저-웹 애플리케이션 시나리오에 대한 코드 샘플을 참조하세요. 그리고 새로운 샘플이 자주 추가되므로 자주 확인해 보세요.

## <a name="app-registration"></a>앱 등록

웹 앱을 등록 하려면 [앱 등록](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조 하세요.

* 단일 테 넌 트-조직 전용 응용 프로그램을 빌드하는 경우 Azure Portal를 사용 하 여 회사의 디렉터리에 등록 해야 합니다.
* 다중 테 넌 트-조직 외부 사용자가 사용할 수 있는 응용 프로그램을 빌드하는 경우 회사의 디렉터리에 등록 해야 하지만 응용 프로그램을 사용할 각 조직의 디렉터리에도 등록 해야 합니다. 해당 디렉터리에서 애플리케이션을 사용할 수 있게 만들려면 고객이 애플리케이션에 동의할 수 있게 하는 등록 프로세스를 포함하면 됩니다. 사용자가 애플리케이션에 등록할 때 애플리케이션에 필요한 권한을 보여 주는 대화 상자가 나타난 다음 동의하는 옵션이 나타납니다. 필요한 권한에 따라, 다른 조직의 관리자가 동의해야 할 수도 있습니다. 사용자 또는 관리자가 동의하면 애플리케이션이 이들의 디렉터리에 등록됩니다.

## <a name="token-expiration"></a>토큰 만료

Azure AD에서 발급 한 토큰의 수명이 만료 되 면 사용자의 세션이 만료 됩니다. 원한다면 비활성 기간을 기준으로 사용자를 로그아웃시키는 등, 애플리케이션이 세션 만료 시간을 단축할 수 있습니다. 세션이 만료되면 사용자에게는 다시 로그인하라는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

* 다른 [애플리케이션 유형 및 시나리오](app-types.md)에 대해 자세히 알아보기
* Azure AD [인증 기본 사항](v1-authentication-scenarios.md) 에 대해 알아보기
