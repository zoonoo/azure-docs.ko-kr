---
title: 웹 API를 호출하는 웹앱 빌드 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 웹앱을 빌드하는 방법에 대해 알아봅니다(개요).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1fdbdada54320ef28f6a4b04a7f415c835acc9dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756290"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>시나리오: 웹 API를 호출하는 웹앱 빌드

Microsoft ID 플랫폼에 사용자를 로그인하는 웹앱을 빌드한 다음 로그인한 사용자를 대신하여 웹 API를 호출 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 시나리오에서는 [시나리오: 사용자에 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)을 이미 완료했다고 가정합니다.

## <a name="overview"></a>개요

로그인한 사용자를 대신하여 사용자를 로그인하고 웹 API를 호출할 수 있도록 웹앱에 인증을 추가합니다.

![웹 API를 호출하는 웹앱](./media/scenario-webapp/web-app.svg)

웹 API를 호출하는 웹앱은 기밀 클라이언트 응용 프로그램입니다.
따라서 Azure Active Directory (Azure AD)를 사용하여 비밀(응용 프로그램 암호 또는 인증서)을 등록합니다. 이 비밀은 토큰을 가져오기 위해 Azure AD를 호출하는 동안 전달됩니다.

## <a name="specifics"></a>특수 적용 사항

> [!NOTE]
> 웹앱에 로그인 추가는 웹앱 자체를 보호하는 조치입니다. 이러한 보호는 MSAL(Microsoft 인증 라이브러리)이 아닌 *미들웨어* 라이브러리를 사용하여 구현됩니다. 앞의 시나리오에서는 [사용자를 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)이 해당 주제를 다뤘습니다.
>
> 이 시나리오에서는 웹앱에서 웹 API를 호출하는 방법을 설명합니다. 이러한 웹 API에 대한 액세스 토큰을 가져와야 합니다. MSAL 라이브러리를 사용하여 토큰을 가져옵니다.

이 시나리오에 개발에는 다음과 같은 특정 작업이 포함됩니다.

- [애플리케이션 등록](scenario-web-app-call-api-app-registration.md) 중에 Azure AD와 공유할 회신 URI, 비밀 또는 인증서를 제공해야 합니다. 앱을 여러 위치에 배포하는 경우 각 위치에 회신 URI를 제공합니다.
- [응용 프로그램 구성](scenario-web-app-call-api-app-configuration.md) 에서는 애플리케이션 등록 중에Azure AD와 공유된 클라이언트 자격 증명을 제공해야 합니다.

## <a name="recommended-reading"></a>추천 자료

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [앱 등록](scenario-web-app-call-api-app-registration.md)으로 이동합니다.