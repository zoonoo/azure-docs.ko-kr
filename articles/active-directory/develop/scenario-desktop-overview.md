---
title: 웹 API를 호출하는 데스크톱 앱 빌드 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 데스크톱 앱을 빌드하는 방법 알아보기(개요)
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
ms.openlocfilehash: 196102769c1f5f68df316918a63079b09baad32d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702150"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>시나리오: 웹 API를 호출하는 데스크톱 앱

웹 API를 호출하는 데스크톱 앱을 빌드하는 데 필요한 모든 것을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>시작

아직 등록하지 않은 경우 .NET 데스크톱 빠른 시작, UWP(유니버설 Windows 플랫폼) 빠른 시작 또는 macOS 네이티브 앱 빠른 시작을 따라 첫 번째 응용 프로그램을 만듭니다.

> [!div class="nextstepaction"]
> [빠른 시작: Windows 데스크톱 앱에서 토큰 가져오기 및 Microsoft Graph API 호출](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [빠른 시작: 토큰을 획득하고 UWP 앱에서 Microsoft 그래프 API를 호출합니다.](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [빠른 시작: 토큰을 획득하고 macOS 네이티브 앱에서 Microsoft 그래프 API를 호출합니다.](./quickstart-v2-ios.md)

## <a name="overview"></a>개요

데스크톱 응용 프로그램을 작성하고 사용자를 응용 프로그램에 로그인하고 Microsoft 그래프, 다른 Microsoft API 또는 사용자 고유의 웹 API와 같은 웹 API를 호출하려고 합니다. 다음과 같은 몇 가지 가능성이 있습니다.

- 대화형 토큰 수집을 사용할 수 있습니다.

  - 데스크톱 응용 프로그램이 그래픽 컨트롤을 지원하는 경우(예: Windows.Form 응용 프로그램, WPF 응용 프로그램 또는 macOS 네이티브 응용 프로그램인 경우).
  - 또는 .NET Core 응용 프로그램이고 Azure Active Directory(Azure AD)와의 인증 상호 작용이 시스템 브라우저에서 발생하는 데 동의하는 경우.

- Windows 호스팅 응용 프로그램의 경우 Windows 도메인에 조인된 컴퓨터에서 실행되는 응용 프로그램또는 Azure AD가 통합 Windows 인증을 사용하여 자동으로 토큰을 획득할 수도 있습니다.
- 마지막으로 권장되지는 않지만 공용 클라이언트 응용 프로그램에서 사용자 이름과 암호를 사용할 수 있습니다. DevOps와 같은 일부 시나리오에서는 여전히 필요합니다. 응용 프로그램에 제약 조건을 적용합니다. 예를 들어 다단계 인증(조건부 액세스)을 수행해야 하는 사용자는 로그인할 수 없습니다. 또한 응용 프로그램은 단일 사인온(SSO)의 이점을 얻지 못합니다.

  이 흐름은 최신 인증 원칙에 반하며, 레거시 용도로만 제공됩니다.

  ![데스크톱 응용 프로그램](media/scenarios/desktop-app.svg)

- Linux 또는 Mac에서 실행되는 .NET Core 응용 프로그램인 휴대용 명령줄 도구를 작성하고 해당 인증이 시스템 브라우저에 위임되는 경우 대화형 인증을 사용할 수 있습니다. .NET Core는 웹 [브라우저를](https://aka.ms/msal-net-uses-web-browser)제공하지 않으므로 시스템 브라우저에서 인증이 수행됩니다. 그렇지 않으면 이 경우 가장 좋은 방법은 장치 코드 흐름을 사용하는 것입니다. 이 흐름은 IoT 응용 프로그램과 같은 브라우저가 없는 응용 프로그램에도 사용됩니다.

  ![브라우저없는 응용 프로그램](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>구체적인

데스크톱 응용 프로그램에는 여러 가지 특이성이 있습니다. 응용 프로그램에서 대화형 인증을 사용하는지 여부에 따라 달라집니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데스크톱 앱: 앱 등록](scenario-desktop-app-registration.md)
