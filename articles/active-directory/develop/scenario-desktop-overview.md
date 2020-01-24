---
title: 웹 Api를 호출 하는 데스크톱 앱 빌드-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 데스크톱 앱을 빌드하는 방법 알아보기 (개요)
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702150"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>시나리오: 웹 Api를 호출 하는 데스크톱 앱

웹 Api를 호출 하는 데스크톱 앱을 빌드하는 데 필요한 모든 것을 알아보세요.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>시작하기

아직 수행 하지 않은 경우 .NET desktop 빠른 시작, UWP (유니버설 Windows 플랫폼) 빠른 시작 또는 macOS 네이티브 앱 빠른 시작을 수행 하 여 첫 번째 응용 프로그램을 만듭니다.

> [!div class="nextstepaction"]
> [빠른 시작: Windows 데스크톱 앱에서 토큰 획득 및 Microsoft Graph API 호출](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [빠른 시작: UWP 앱에서 토큰 획득 및 Microsoft Graph API 호출](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [빠른 시작: macOS 네이티브 앱에서 토큰 획득 및 Microsoft Graph API 호출](./quickstart-v2-ios.md)

## <a name="overview"></a>개요

데스크톱 응용 프로그램을 작성 하 고 응용 프로그램에 사용자를 로그인 하 고 Microsoft Graph, 다른 Microsoft Api 또는 고유한 web API와 같은 웹 Api를 호출 하려고 합니다. 다음과 같은 여러 가지 가능성이 있습니다.

- 대화형 토큰 획득을 사용할 수 있습니다.

  - 데스크톱 응용 프로그램에서 예를 들어, Windows forms 응용 프로그램, WPF 응용 프로그램 또는 macOS 네이티브 응용 프로그램의 경우 그래픽 컨트롤을 지원 합니다.
  - 또는 .NET Core 응용 프로그램을 사용 하는 경우 시스템 브라우저에서 Azure Active Directory (Azure AD)와의 인증 상호 작용이 발생 하는 것에 동의 하는 것입니다.

- Windows 호스팅된 응용 프로그램의 경우 windows 도메인 또는 Azure AD에 가입 된 컴퓨터에서 실행 되는 응용 프로그램에서 windows 통합 인증을 사용 하 여 토큰을 자동으로 획득 하도록 할 수도 있습니다.
- 마지막으로, 권장 되지 않지만 공용 클라이언트 응용 프로그램에서 사용자 이름과 암호를 사용할 수 있습니다. DevOps와 같은 일부 시나리오에서는 계속 필요 합니다. 이를 사용 하면 응용 프로그램에 제약 조건이 부과 됩니다. 예를 들어 다단계 인증 (조건부 액세스)을 수행 해야 하는 사용자는 로그인 할 수 없습니다. 또한 응용 프로그램은 SSO (Single Sign-On)를 활용 하지 않습니다.

  이 흐름은 최신 인증 원칙에 반하며, 레거시 용도로만 제공됩니다.

  ![데스크톱 응용 프로그램](media/scenarios/desktop-app.svg)

- Linux 또는 Mac에서 실행 되는 .NET Core 응용 프로그램 인 휴대용 명령줄 도구를 작성 하는 경우, 해당 인증을 시스템 브라우저에 위임 하는 경우 대화형 인증을 사용할 수 있습니다. .NET Core는 [웹 브라우저](https://aka.ms/msal-net-uses-web-browser)를 제공 하지 않으므로 시스템 브라우저에서 인증이 수행 됩니다. 그렇지 않은 경우에는 장치 코드 흐름을 사용 하는 것이 가장 좋습니다. 이 흐름은 IoT 응용 프로그램과 같은 브라우저가 없는 응용 프로그램에도 사용 됩니다.

  ![브라우저 less 응용 프로그램](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>자세히

데스크톱 응용 프로그램에는 여러 가지 specificities 있습니다. 주로 응용 프로그램이 대화형 인증을 사용 하는지 여부에 따라 달라 집니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데스크톱 앱: 앱 등록](scenario-desktop-app-registration.md)
