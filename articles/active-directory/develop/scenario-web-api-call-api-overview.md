---
title: 웹 Api를 호출 하는 web API 빌드-Microsoft identity platform | Microsoft
description: 다운스트림 웹 Api (개요)를 호출 하는 web API를 빌드하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 88a0177755fbd913bdaaf0ecf3e12c62dee294c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885075"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>시나리오: web Api를 호출 하는 web API

Web Api를 호출 하는 web API를 빌드하기 위해 알아야 할 내용을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

보호 되는 web API가 웹 api를 호출 하는이 시나리오는 "웹 API 보호" 시나리오 위에 빌드됩니다. 이 기본 시나리오에 대 한 자세한 내용은 [시나리오: 보호 된 웹 API](scenario-protected-web-api-overview.md)를 참조 하세요.

## <a name="overview"></a>개요

- 웹, 데스크톱, 모바일 또는 단일 페이지 응용 프로그램 클라이언트 (함께 제공 된 다이어그램에 표시 되지 않음)는 보호 된 웹 API를 호출 하 고 "Authorization" HTTP 헤더에 JSON Web Token (JWT) 전달자 토큰을 제공 합니다.
- 보호 된 웹 API는 토큰의 유효성을 검사 하 고 MSAL (Microsoft Authentication Library) 메서드를 사용 하 여 `AcquireTokenOnBehalfOf` Azure Active Directory (AZURE AD)에서 다른 토큰을 요청 합니다. 따라서 보호 된 웹 api는 사용자를 대신 하 여 두 번째 WEB api 또는 다운스트림 웹 api를 호출할 수 있습니다.
- 보호 된 웹 API는 나중에 `AcquireTokenSilent` 를 호출 하 여 동일한 사용자를 대신 하 여 다른 다운스트림 api에 대 한 토큰을 요청할 수도 있습니다. `AcquireTokenSilent`필요한 경우 토큰을 새로 고칩니다.

![Web API를 호출 하는 web API 다이어그램](media/scenarios/web-api.svg)

## <a name="specifics"></a>특수 적용 사항

API 권한과 관련 된 앱 등록 파트는 클래식입니다. 앱 구성에는 다운스트림 API에 대 한 토큰에 대해 JWT 전달자 토큰을 교환 하기 위해 OAuth 2.0 for flow를 사용 하는 작업이 포함 됩니다. 이 토큰은 웹 API의 컨트롤러에서 사용할 수 있는 토큰 캐시에 추가 되 고, 자동으로 토큰을 획득 하 여 다운스트림 Api를 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-web-api-call-api-app-registration.md)
