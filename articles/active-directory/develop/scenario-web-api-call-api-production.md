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
ms.openlocfilehash: aea3a173fd07b66021d35142a84499ae9c66c014
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518201"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>웹 Api를 호출 하는 web API: 프로덕션으로 이동

웹 Api를 호출 하는 토큰을 얻은 후에는 앱을 프로덕션으로 이동할 수 있습니다.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>자세한 정보

사용자 고유의 web API에서 web Api를 호출 하는 방법의 기본 사항을 배웠으므로 웹 Api를 호출 하는 보호 된 웹 API를 빌드하는 데 사용 되는 코드를 설명 하는 다음 자습서를 참조할 수 있습니다.

| 예제 | 플랫폼 | 설명 |
|--------|----------|-------------|
| [active directory-aspnetcore-webapi-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) chapter 1 | ASP.NET Core web API, Desktop (WPF) | ASP.NET Core web API는 v2.0 (Microsoft identity platform)를 사용 하 여 WPF 응용 프로그램에서 호출 하는 Microsoft Graph를 호출 합니다. |
