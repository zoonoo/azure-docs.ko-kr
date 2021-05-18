---
title: 웹 API를 호출하는 웹앱을 프로덕션으로 이동 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 웹앱을 프로덕션으로 이동하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cf32274a49cb1b790e9d872efe36f2e1cb188d1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675943"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>웹 API를 호출하는 웹앱: 프로덕션으로 이동

이제 웹 API를 호출하는 토큰을 획득하는 방법을 배웠으므로 다음 애플리케이션을 프로덕션 환경으로 이동할 때 고려해야 할 몇 가지 사항을 확인합니다.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

ASP.NET Core 웹앱에 대한 점진적 자습서를 참조하세요. 이 자습서의 내용은 다음과 같습니다.

- 여러 대상 또는 국가별 클라우드로 로그인하거나 소셜 ID를 사용하여 로그인하는 방법을 보여 줍니다.
- Microsoft Graph를 호출합니다.
- 여러 Microsoft API를 호출합니다.
- 증분 동의를 처리합니다.
- 사용자 고유의 웹 API를 호출합니다.

[ASP.NET Core 웹앱 자습서](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
