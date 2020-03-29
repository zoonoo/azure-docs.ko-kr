---
title: 웹 API - Microsoft ID 플랫폼을 호출하는 웹 API 구축 | Azure
description: 다운스트림 웹 API(개요)를 호출하는 웹 API를 빌드하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 467ff2f789cc83bc5651d831838da0b5c922c839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701742"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>시나리오: 웹 API를 호출하는 웹 API

웹 API를 호출하는 웹 API를 빌드하기 위해 알아야 할 사항자세히 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

보호된 웹 API가 웹 API를 호출하는 이 시나리오는 "웹 API 보호" 시나리오 를 기반으로 합니다. 이 기본 시나리오에 대한 자세한 내용은 [시나리오: 보호된 웹 API](scenario-protected-web-api-overview.md)를 참조하십시오.

## <a name="overview"></a>개요

- 웹, 데스크톱, 모바일 또는 단일 페이지 응용 프로그램 클라이언트(함께 제공되는 다이어그램에 표시되지 않음)는 보호된 웹 API를 호출하고 "권한 부여" HTTP 헤더에 JSON 웹 토큰(JWT) 베어러 토큰을 제공합니다.
- 보호된 웹 API는 토큰의 유효성을 검사하고 MSAL(Microsoft 인증 라이브러리) `AcquireTokenOnBehalfOf` 메서드를 사용하여 Azure Active Directory(Azure AD)에서 다른 토큰을 요청하여 보호된 웹 API가 사용자를 대신하여 두 번째 웹 API 또는 다운스트림 웹 API를 호출할 수 있도록 합니다.
- 보호된 웹 API는 `AcquireTokenSilent`나중에 호출하여 동일한 사용자를 대신하여 다른 다운스트림 API에 대한 토큰을 요청할 수도 있습니다. `AcquireTokenSilent`필요할 때 토큰을 새로 고칩니다.

![웹 API를 호출하는 웹 API 다이어그램](media/scenarios/web-api.svg)

## <a name="specifics"></a>구체적인

API 권한과 관련된 앱 등록 부분은 클래식입니다. 앱 구성에는 OAuth 2.0 On-For-Of flow를 사용하여 JWT 베어러 토큰을 다운스트림 API의 토큰과 교환하는 작업이 포함됩니다. 이 토큰은 웹 API의 컨트롤러에서 사용할 수 있는 토큰 캐시에 추가된 다음 자동으로 토큰을 획득하여 다운스트림 API를 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-web-api-call-api-app-registration.md)
