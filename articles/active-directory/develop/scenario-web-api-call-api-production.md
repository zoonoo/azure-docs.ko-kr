---
title: 웹 API를 호출하는 웹 API를 프로덕션으로 이동 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 웹 API를 프로덕션으로 이동하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675875"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>웹 API를 호출하는 웹 API: 프로덕션으로 이동

웹 API를 호출하는 토큰을 얻은 후에는 애플리케이션을 프로덕션 환경으로 이동할 때 고려해야 할 몇 가지 사항이 있습니다.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

사용자 고유의 웹 API에서 웹 API를 호출하는 방법의 기본 사항을 알게 되었으므로 다음 자습서에서 웹 API를 호출하는 보호된 웹 API를 빌드하는 데 사용되는 코드를 알아봅니다.

| 샘플 | 플랫폼 | Description |
|--------|----------|-------------|
| [active directory-aspnetcore-webapi-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) 챕터 1 | ASP.NET Core 웹 API, 데스크톱(WPF) | ASP.NET Core 웹 API는 Microsoft ID 플랫폼을 사용하여 WPF 애플리케이션에서 호출하는 Microsoft Graph를 호출합니다. |
