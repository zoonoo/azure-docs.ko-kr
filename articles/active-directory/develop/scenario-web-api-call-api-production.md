---
title: 웹 api를 호출 하는 웹 api를 프로덕션으로 이동-Microsoft id 플랫폼 | Microsoft
description: 웹 Api를 호출 하는 web API를 프로덕션으로 이동 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 078ed3e5f3a19bfa4350f9edea858b717c69e3f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81537154"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>웹 Api를 호출 하는 web API: 프로덕션으로 이동

웹 Api를 호출 하는 토큰을 얻은 후에는 앱을 프로덕션으로 이동할 수 있습니다.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>자세한 정보

사용자 고유의 web API에서 web Api를 호출 하는 방법의 기본 사항을 배웠으므로 웹 Api를 호출 하는 보호 된 웹 API를 빌드하는 데 사용 되는 코드를 설명 하는 다음 자습서를 참조할 수 있습니다.

| 예제 | 플랫폼 | 설명 |
|--------|----------|-------------|
| [aspnetcore-webapi-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 웹 API, 데스크톱 (WPF) | ASP.NET Core 2.2 웹 API는 v2.0 (Microsoft identity platform)를 사용 하 여 WPF 응용 프로그램에서 호출 하는 Microsoft Graph를 호출 합니다. |
