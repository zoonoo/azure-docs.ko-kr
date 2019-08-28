---
title: 웹 Api를 호출 하는 데스크톱 앱 (개요)-Microsoft identity platform
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5288fe57b2f83522b140f65fa62e08f9c63a7af5
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852679"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>시나리오: Web API를 호출하는 데스크톱 앱

웹 Api를 호출 하는 데스크톱 앱을 빌드하는 데 필요한 모든 것을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>시작

아직 수행 하지 않은 경우 .NET 데스크톱 빠른 시작 또는 UWP 빠른 시작을 수행 하 여 첫 번째 응용 프로그램을 만듭니다.

> [!div class="nextstepaction"]
> [빠른 시작: Windows 데스크톱 앱에서 토큰 가져오기 및 Microsoft Graph API 호출](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [빠른 시작: UWP 앱에서 토큰을 획득 하 고 Microsoft Graph API를 호출 합니다.](./quickstart-v2-uwp.md)

## <a name="overview"></a>개요

데스크톱 응용 프로그램을 작성 하 고 응용 프로그램에 사용자를 로그인 하 고 Microsoft Graph, 다른 Microsoft Api 또는 고유한 web API와 같은 웹 Api를 호출 하려고 합니다. 다음과 같은 여러 가지 가능성이 있습니다.

- 대화형 토큰 획득을 사용할 수 있습니다.

  - 데스크톱 응용 프로그램에서 그래픽 컨트롤을 지 원하는 경우 (예를 들어, Windows forms 응용 프로그램 또는 WPF 응용 프로그램)
  - .NET Core 응용 프로그램 이며 Azure AD와의 인증 상호 작용이 시스템 브라우저에서 발생 하는 것에 동의 하는 경우

- Windows 호스팅된 응용 프로그램의 경우 windows 도메인 또는 AAD에 가입 된 컴퓨터에서 실행 되는 응용 프로그램에서 windows 통합 인증을 사용 하 여 토큰을 자동으로 획득 하는 것도 가능 합니다.
- 마지막으로, 권장 되지 않지만 공용 클라이언트 응용 프로그램에서 사용자 이름/암호를 사용할 수 있습니다. 일부 시나리오 (예: DevOps)에서 여전히 필요 하지만,이를 사용 하면 응용 프로그램에 제약 조건이 적용 됩니다. 예를 들어 multi-factor authentication (조건부 액세스)을 수행 해야 하는 사용자는 로그인 할 수 없습니다. 또한 응용 프로그램은 SSO (single sign-on)의 이점을 누릴 수 없습니다.

  이 흐름은 최신 인증 원칙에 반하며, 레거시 용도로만 제공됩니다.

  ![데스크톱 응용 프로그램](media/scenarios/desktop-app.svg)

- Linux 또는 Mac에서 실행 되는 .NET Core 응용 프로그램 일 수 있는 이식 가능한 명령줄 도구를 작성 하는 경우, 인증을 시스템 브라우저에 위임 하는 경우 대화형 인증을 사용할 수 있습니다. .NET Core는 아직 [웹 브라우저](https://aka.ms/msal-net-uses-web-browser) 를 제공 하지 않으므로 시스템 브라우저에서 인증이 발생 합니다. 그렇지 않은 경우에는 장치 코드 흐름을 사용 하는 것이 가장 좋습니다. 이 흐름은 IoT 응용 프로그램과 같은 브라우저가 없는 응용 프로그램에도 사용 됩니다.

  ![브라우저 less 응용 프로그램](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>자세히

데스크톱 응용 프로그램에는 응용 프로그램에서 대화형 인증을 사용 하는지 여부에 따라 주로 달라 지는 수많은 specificities 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데스크톱 앱-앱 등록](scenario-desktop-app-registration.md)
