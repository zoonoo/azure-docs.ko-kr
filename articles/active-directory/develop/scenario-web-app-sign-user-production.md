---
title: 웹 앱 로그인 사용자 (프로덕션으로 이동)-Microsoft id 플랫폼
description: (프로덕션으로 이동) 사용자가 로그인 하는 웹 앱을 빌드하는 방법을 알아봅니다
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
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074712"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>로그인 한 사용자-프로덕션 환경으로 이동 하는 웹 앱

이제 web Api를 호출 하는 토큰을 획득 하는 방법을 알았으므로 프로덕션으로 이동 하는 방법에 알아봅니다.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

### <a name="calling-web-apis-scenario"></a>호출 웹 Api 시나리오

한 번 웹 앱 로그인 사용자, 로그인 한 사용자 대신 웹 Api를 호출할 수 있습니다 것. 웹 앱에서 호출 웹 Api는 다음 시나리오의 개체:

> [!div class="nextstepaction"]
> [웹 Api를 호출 하는 웹 앱](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>딥 다이브 분석-웹 앱 자습서

ASP.NET Core 자습서를 사용 하 여 로그인 사용자의 다른 방법에 알아봅니다: [ms-id-aspnetcore-webapp-자습서](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)합니다. 로그인을 추가 하는 방법을 포함 하 여 웹 앱에 대 한 프로덕션 준비 코드로 점진적 자습서입니다.

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
