---
title: 웹 Api를 호출 하는 웹 앱을 프로덕션으로 이동-Microsoft id 플랫폼 | Microsoft
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
ms.openlocfilehash: f38755e669a62e9fe74aa6281daccdf39e1f117c
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443571"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>웹 Api를 호출 하는 웹 앱: 프로덕션으로 이동

이제 web Api를 호출 하는 토큰을 획득 하는 방법을 배웠으므로 프로덕션으로 이동 하는 방법을 알아보세요.

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
