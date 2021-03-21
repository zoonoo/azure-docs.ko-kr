---
title: 웹 Api를 호출 하는 웹 앱을 프로덕션으로 이동 | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 Api를 호출 하는 웹 앱을 프로덕션으로 이동 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675943"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>웹 Api를 호출 하는 웹 앱: 프로덕션으로 이동

이제 웹 Api를 호출 하는 토큰을 획득 하는 방법을 배웠으므로 응용 프로그램을 프로덕션 환경으로 이동할 때 고려해 야 할 몇 가지 사항이 있습니다.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

ASP.NET Core 웹 앱에 대 한 전체 프로그레시브 자습서를 사용해 보세요. 이 자습서의 내용은 다음과 같습니다.

- 사용자를 여러 대상 또는 국가별 클라우드로 로그인 하거나 소셜 id를 사용 하 여 로그인 하는 방법을 보여 줍니다.
- Microsoft Graph를 호출 합니다.
- 는 여러 Microsoft Api를 호출 합니다.
- 증분 동의를 처리 합니다.
- 사용자 고유의 web API를 호출 합니다.

[ASP.NET Core 웹앱 자습서](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
