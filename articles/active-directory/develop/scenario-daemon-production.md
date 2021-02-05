---
title: 웹 Api를 호출 하는 디먼 앱을 프로덕션으로 이동 | Microsoft
titleSuffix: Microsoft identity platform
description: 웹 Api를 호출 하는 디먼 앱을 프로덕션으로 이동 하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8dc9bff86a07f3d4a0ec6fd224de6d5633165a6d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582844"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>웹 Api를 호출 하는 디먼 앱-프로덕션으로 이동

서비스 간 호출에 대 한 토큰을 획득 하 고 사용 하는 방법을 배웠으므로 이제 앱을 프로덕션으로 이동 하는 방법을 알아보세요.

## <a name="deployment---multitenant-daemon-apps"></a>배포-다중 테 넌 트 디먼 앱

여러 테 넌 트에서 실행할 수 있는 디먼 응용 프로그램을 만드는 ISV 인 경우 테 넌 트 관리자가 다음을 확인 해야 합니다.

- 응용 프로그램에 대 한 서비스 주체를 프로 비전 합니다.
- 응용 프로그램에 동의를 부여 합니다.

이러한 작업을 수행 하는 방법을 고객에 게 설명 해야 합니다. 자세한 내용은 [전체 테 넌 트에 대 한 동의 요청](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)을 참조 하세요.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>코드 샘플

# <a name="net"></a>[.NET](#tab/dotnet)

- 참조 설명서:
  - [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)인스턴스화.
  - [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)를 호출 합니다.
- 기타 샘플/자습서:
  - [microsoft-](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) --platform-디먼 기능 Microsoft Graph 쿼리 하는 테 넌 트의 사용자를 표시 하는 작은 .net Core 디먼 콘솔 응용 프로그램입니다.

    ![샘플 디먼 앱 토폴로지](media/scenario-daemon-app/daemon-app-sample.svg)

    동일한 샘플은 인증서를 사용 하는 변형도 보여 줍니다.

    ![샘플 디먼 앱 토폴로지-인증서](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [webapp-디먼](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) 기능은 사용자 대신 응용 프로그램의 id를 사용 하 여 Microsoft Graph에서 데이터를 동기화 하는 ASP.NET MVC 웹 응용 프로그램입니다. 또한이 샘플은 관리자 동의 프로세스를 보여 줍니다.

    ![토폴로지](media/scenario-daemon-app/damon-app-sample-web.svg)

---

## <a name="next-steps"></a>다음 단계

자세히 알아보는 데 도움이 되는 몇 가지 링크는 다음과 같습니다.

# <a name="python"></a>[Python](#tab/python)

[응용 프로그램의 id를 사용 하 여 빠른 시작에서 토큰 획득을 시도 하 고 Python 콘솔 앱에서 MICROSOFT GRAPH API를 호출](./quickstart-v2-python-daemon.md)합니다.

# <a name="java"></a>[Java](#tab/java)

퀵 스타트에서 [토큰 획득을 시도 하 고 앱의 id를 사용 하 여 Java 콘솔 앱에서 MICROSOFT GRAPH API를 호출](./quickstart-v2-java-daemon.md)합니다.

---
