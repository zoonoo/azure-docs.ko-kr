---
title: 자바 스크립트 단일 페이지 응용 프로그램 시나리오 - 마이크로 소프트 ID 플랫폼 | Azure
description: Microsoft ID 플랫폼을 사용하여 단일 페이지 응용 프로그램(시나리오 개요)을 빌드하는 방법을 알아봅니다.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3ead0ea58c6860519f027eb6a7450df37396bd89
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885177"
---
# <a name="scenario-single-page-application"></a>시나리오: 단일 페이지 응용 프로그램

단일 페이지 응용 프로그램(SPA)을 빌드하는 데 필요한 모든 내용을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>시작

JavaScript SPA 빠른 시작을 따라 첫 번째 응용 프로그램을 만들 수 있습니다.

> [!div class="nextstepaction"]
> [빠른 시작: 단일 페이지 응용 프로그램](./quickstart-v2-javascript.md)

## <a name="overview"></a>개요

많은 최신 웹 응용 프로그램은 클라이언트 쪽 단일 페이지 응용 프로그램으로 빌드됩니다. 개발자는 자바 스크립트 또는 각도, Vue.js 및 React.js와 같은 SPA 프레임 워크를 사용하여 작성합니다. 이러한 응용 프로그램은 웹 브라우저에서 실행되며 기존 서버 쪽 웹 응용 프로그램과 는 다른 인증 특성을 갖습니다. 

Microsoft ID 플랫폼을 사용하면 단일 페이지 응용 프로그램이 [OAuth 2.0 암시적 흐름을](./v2-oauth2-implicit-grant-flow.md)사용하여 사용자에게 로그인하고 토큰을 사용하여 백 엔드 서비스 또는 웹 API에 액세스할 수 있습니다. 암시적 흐름을 통해 응용 프로그램은 ID 토큰을 사용하여 인증된 사용자를 나타내고 보호된 API를 호출하는 데 필요한 토큰에 액세스할 수 있습니다.

![단일 페이지 애플리케이션](./media/scenarios/spa-app.svg)

이 인증 흐름에는 전자 및 React-Native와 같은 플랫폼 간 JavaScript 프레임워크를 사용하는 응용 프로그램 시나리오는 포함되지 않습니다. 네이티브 플랫폼과의 상호 작용을 위해 추가 기능이 필요합니다.

## <a name="specifics"></a>구체적인

응용 프로그램에 대해 이 시나리오를 사용하려면 다음이 필요합니다.

* Azure Active Directory(Azure AD)를 사용한 응용 프로그램 등록입니다. 이 등록에는 암시적 흐름을 사용하도록 설정하고 토큰이 반환되는 리디렉션 URI를 설정하는 작업이 포함됩니다.
* 응용 프로그램 ID와 같은 등록된 응용 프로그램 속성이 있는 응용 프로그램 구성입니다.
* MSAL(Microsoft 인증 라이브러리)을 사용하여 인증 흐름을 수행하여 로그인하고 토큰을 획득합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 등록](scenario-spa-app-registration.md)
