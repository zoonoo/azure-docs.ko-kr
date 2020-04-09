---
title: 웹 API를 호출하는 웹 앱 프로덕션으로 이동 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 웹 앱을 프로덕션으로 이동하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 6404d00b87b9ee745b9e3a92c646404e574417c1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881664"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>웹 API를 호출하는 웹 앱: 프로덕션으로 이동

이제 웹 API를 호출하는 토큰을 획득하는 방법을 배웠으니 프로덕션으로 이동하는 방법을 알아봅니다.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

ASP.NET 코어 웹 앱에 대한 전체 점진적 자습서를 시도하여 자세히 알아보십시오. 이 자습서의 내용은 다음과 같습니다.

- 여러 대상 또는 국가 클라우드에 사용자를 로그인하거나 소셜 ID를 사용하여 로그인하는 방법을 보여 합니다.
- 마이크로 소프트 그래프를 호출합니다.
- 여러 Microsoft API를 호출합니다.
- 증분 동의를 처리합니다.
- 사용자 고유의 웹 API를 호출합니다.

> [!div class="nextstepaction"]
> [ASP.NET 코어 웹 앱 자습서](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
