---
title: Azure Active Directory의 네이티브 앱
description: 네이티브 앱을 소개하고 이 앱 유형에 대한 프로토콜 흐름, 등록 및 토큰 만료 기본 사항을 설명합니다.
services: active-directory
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
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80154800"
---
# <a name="native-apps"></a>네이티브 앱

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

네이티브 앱은 사용자 대신 웹 API를 호출하는 애플리케이션입니다. 이 시나리오는 [OAuth 2.0 사양](https://tools.ietf.org/html/rfc6749)의 섹션 4.1에 설명된 대로 공용 클라이언트의 OAuth 2.0 인증 코드 권한 유형을 기반으로 구축되었습니다. 네이티브 애플리케이션은 OAuth 2.0 프로토콜을 사용하여 사용자에 대한 액세스 토큰을 가져옵니다. 그런 다음 이 액세스 토큰은 웹 API에 대한 요청으로 전송됩니다. 그러면 사용자가 인증되고 원하는 리소스가 반환됩니다.

## <a name="diagram"></a>다이어그램

![네이티브 애플리케이션-Web API 다이어그램](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>프로토콜 흐름

AD 인증 라이브러리를 사용하는 경우 아래에서 설명하는 브라우저 팝업, 토큰 캐싱, 토큰 새로 고침 처리와 같은 프로토콜 세부 정보의 대부분은 자동으로 처리됩니다.

1. 네이티브 애플리케이션이 브라우저 팝업을 사용하여 Azure AD의 권한 부여 엔드포인트에 요청합니다. 이 요청에는 Azure Portal에 표시된 네이티브 애플리케이션의 애플리케이션 ID와 리디렉션 URI 및 웹 API에 대한 애플리케이션 ID URI가 포함됩니다. 사용자가 아직 로그인 하지 않은 경우에는 다시 로그인 하 라는 메시지가 표시 됩니다.
1. Azure AD가 사용자를 인증합니다. 다중 테 넌 트 응용 프로그램이 고 응용 프로그램을 사용 하는 데 동의 해야 하는 경우 사용자가 아직 수행 하지 않은 경우 동의 해야 합니다. 동의를 부여 하 고 인증에 성공 하면 Azure AD는 클라이언트 응용 프로그램의 리디렉션 URI에 대 한 인증 코드 응답을 발급 합니다.
1. Azure AD가 인증 코드 응답을 리디렉션 URI에 발급하면 클라이언트 애플리케이션이 브라우저 조작을 중지하고 응답에서 인증 코드를 추출합니다. 이 인증 코드를 사용 하 여 클라이언트 응용 프로그램은 인증 코드, 클라이언트 응용 프로그램에 대 한 세부 정보 (응용 프로그램 ID 및 리디렉션 URI) 및 원하는 리소스 (web API의 응용 프로그램 ID URI)를 포함 하는 요청을 Azure AD의 토큰 끝점에 보냅니다.
1. 클라이언트 애플리케이션 및 웹 API에 대한 정보와 인증 코드는 Azure AD에서 유효성이 검사됩니다. 유효성 검사가 성공하면 Azure AD는 JWT 액세스 토큰과 JWT 새로 고침 토큰 등 두 가지 토큰을 반환합니다. 또한 Azure AD는 표시 이름 및 테넌트 ID와 같이 사용자에 대한 기본적인 정보를 반환합니다.
1. HTTPS를 통해 클라이언트 응용 프로그램은 반환 된 JWT 액세스 토큰을 사용 하 여 웹 API에 대 한 요청의 권한 부여 헤더에 "전달자"로 지정 된 JWT 문자열을 추가 합니다. 그러면 웹 API에서 JWT 토큰의 유효성을 검사하여 유효성 검사가 성공하면 원하는 리소스를 반환합니다.
1. 액세스 토큰이 만료되면 클라이언트 애플리케이션에서 사용자가 다시 인증하도록 요구하는 오류가 표시됩니다. 애플리케이션에 유효한 새로 고침 토큰이 있는 경우 사용자에게 다시 로그인하도록 하지 않고도 이 토큰을 사용하여 새 액세스 토큰을 획득할 수 있습니다. 새로 고침 토큰이 만료되는 경우 애플리케이션이 대화형으로 사용자를 다시 한 번 인증해야 합니다.

> [!NOTE]
> Azure AD에서 발급된 새로 고침 토큰은 여러 리소스에 액세스하는 데 사용할 수 있습니다. 예를 들어 두 개의 웹 API를 호출하는 권한을 가진 클라이언트 애플리케이션이 있는 경우 새로 고침 토큰을 사용하여 다른 웹 API에 대한 액세스 토큰도 가져올 수 있습니다.

## <a name="code-samples"></a>코드 샘플

네이티브 애플리케이션-Web API 시나리오에 대한 코드 샘플을 참조하세요. 그리고 새로운 샘플이 자주 추가되므로 자주 확인해 보세요. [웹 API에 대 한 네이티브 응용 프로그램](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api)입니다.

## <a name="app-registration"></a>앱 등록

응용 프로그램을 Azure AD v 1.0 끝점에 등록 하려면 [앱 등록](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조 하세요.

* 단일 테넌트 - 네이티브 애플리케이션과 웹 API를 Azure AD의 동일한 디렉터리에 등록해야 합니다. 웹 API는 해당 리소스에 대 한 네이티브 응용 프로그램의 액세스를 제한 하는 데 사용 되는 사용 권한 집합을 노출 하도록 구성할 수 있습니다. 그런 다음 클라이언트 응용 프로그램은 Azure Portal의 "다른 응용 프로그램에 대 한 사용 권한" 드롭다운 메뉴에서 원하는 권한을 선택 합니다.
* 다중 테 넌 트-먼저 네이티브 응용 프로그램은 개발자 또는 게시자의 디렉터리에만 등록 됩니다. 그런 다음, 네이티브 애플리케이션이 작동에 필요한 권한을 나타내도록 구성됩니다. 이러한 필수 권한 목록은 대상 디렉터리의 사용자나 관리자가 애플리케이션에 동의하여 애플리케이션을 조직에서 사용할 수 있도록 만들면 대화 상자에 표시됩니다. 일부 애플리케이션에는 조직의 모든 사용자가 동의할 수 있는 사용자 수준 권한만 필요합니다. 또 일부 애플리케이션에는 조직의 사용자가 동의할 수 없는 수준인 관리자 수준 권한이 필요합니다. 이 수준의 권한이 요구되는 애플리케이션에 동의할 수 있는 사람은 디렉터리 관리자뿐입니다. 사용자 또는 관리자가 동의하면 웹 API만 이들의 디렉터리에 등록됩니다. 

## <a name="token-expiration"></a>토큰 만료

네이티브 애플리케이션이 인증 코드를 사용하여 JWT 액세스 토큰을 가져오는 경우 JWT 새로 고침 토큰도 수신합니다. 액세스 토큰이 만료되면 사용자에게 다시 로그인하도록 요구하지 않고 새로 고침 토큰을 사용하여 사용자를 다시 인증할 수 있습니다. 이 새로 고침 토큰을 사용하여 사용자를 인증하면 새 액세스 토큰 및 새로 고침 토큰을 얻습니다.

## <a name="next-steps"></a>다음 단계

- 다른 [애플리케이션 유형 및 시나리오](app-types.md)에 대해 자세히 알아보기
- Azure AD [인증 기본 사항](v1-authentication-scenarios.md) 에 대해 알아보기
