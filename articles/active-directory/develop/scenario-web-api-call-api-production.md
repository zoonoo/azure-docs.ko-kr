---
title: 웹 API는 호출 web Api를 (프로덕션으로 이동)-Microsoft id 플랫폼
description: 웹 호출 다운스트림 웹 Api (프로덕션으로 이동) 되는 API를 빌드하는 방법에 알아봅니다.
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7de892a0851e556c5046fe4b3691f39b42a9d237
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074757"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>웹 Api를 호출 하는 웹 API를 프로덕션으로 이동

웹 Api를 호출 하는 토큰을 구입한 후 프로덕션 앱을 이동할 수 있습니다.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>자세한 정보

확인할 수 있습니다, 사용자 고유의 웹 API에서에서 웹 Api를 호출 하는 방법의 기본 사항을 배웠으므로이 자습서에서는 보호 된 web API를 빌드하는 데는 코드를 설명 하는 호출 하는 웹 Api입니다.

| 샘플 | 플랫폼 | 설명 |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | 2.2 ASP.NET Core Web API 데스크톱 (WPF) | Microsoft id 플랫폼 (v2.0)를 사용 하 여 WPF 응용 프로그램에서 호출 자체는 Microsoft Graph 호출 2.2 ASP.NET Core Web API |
