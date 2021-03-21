---
title: 보호 된 웹 API를 프로덕션으로 이동 | Microsoft
titleSuffix: Microsoft identity platform
description: 보호 된 웹 API (프로덕션으로 이동)를 빌드하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 98be47200c12ab3097f9416b5647abf6465a89e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675977"
---
# <a name="protected-web-api---move-to-production"></a>보호 된 웹 API-프로덕션으로 이동

이제 web API를 보호 하는 방법을 배웠으므로 응용 프로그램을 프로덕션 환경으로 이동할 때 고려해 야 할 몇 가지 사항이 있습니다.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

시나리오에서 다운스트림 API를 호출 하는 방법에 대해 알아봅니다. 웹 api를 [호출 하는 웹 api](scenario-web-api-call-api-overview.md)입니다.


GitHub의 자습서 및 샘플을 통해 자세히 알아보세요.

- [디먼을 사용 하 여 보호 된 API 호출](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/2-Call-OwnApi)
- [ASP.NET Core web API 자습서](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
- [ASP.NET Web API 샘플](https://github.com/azureadquickstarts/appmodelv2-nativeclient-dotnet)
