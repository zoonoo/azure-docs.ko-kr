---
title: 웹 API를 호출하는 디먼 앱을 프로덕션으로 이동 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 디먼 앱을 프로덕션으로 이동하는 방법을 알아봅니다
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
ms.openlocfilehash: d903f04055d1607ee782bd502d99a8fd9cde87ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578450"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>웹 API를 호출하는 디먼 앱 - 프로덕션으로 이동

서비스 간 호출에 대한 토큰을 획득하고 사용하는 방법을 배웠으므로 이제 앱을 프로덕션으로 이동하는 방법을 알아 보세요.

## <a name="deployment---multitenant-daemon-apps"></a>배포 - 다중 테넌트 디먼 앱

ISV가 여러 테넌트에서 실행할 수 있는 디먼 애플리케이션을 만드는 경우 테넌트 관리자가 다음을 확인해야 합니다.

- 애플리케이션의 서비스 주체를 프로비저닝합니다.
- 애플리케이션에 동의를 부여합니다.

이러한 작업을 수행하는 방법을 고객에게 설명해야 합니다. 자세한 내용은 [전체 테넌트에 대한 동의 요청](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)을 참조하세요.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>코드 샘플

# <a name="net"></a>[.NET](#tab/dotnet)

- 다음 항목에 대한 참조 설명서:
  - [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)인스턴스화.
  - [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder) 호출.
- 기타 샘플/자습서:
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon)은 Microsoft Graph를 쿼리하는 테넌트 사용자를 표시하는 소규모 .NET 코어 디먼 콘솔 애플리케이션입니다.

    ![샘플 디먼 앱 토폴로지](media/scenario-daemon-app/daemon-app-sample.svg)

    같은 샘플에서 인증서를 사용한 변형도 보여 줍니다.

    ![샘플 디먼 앱 토폴로지 - 인증서](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon)은 사용자 대신 애플리케이션의 ID를 사용하여 Microsoft Graph에서 데이터를 동기화하는 ASP.NET MVC 웹 애플리케이션입니다. 또한 이 샘플에서는 관리자 동의 프로세스를 보여 줍니다.

    ![토폴로지](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="java"></a>[Java](#tab/java)

[앱의 ID를 사용하는 Java 콘솔 앱에서 토큰 획득 및 Microsoft Graph API 호출](quickstart-v2-java-daemon.md) 빠른 시작을 시도해 보세요.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

- 자세한 내용은 다음을 참조하세요.
  - [구성](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md) 이해
  - [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) 인스턴스화
  - [FAQ](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/faq.md)
- 기타 샘플/자습서:
  - [MSAL 노드 콘솔 디먼 샘플](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console)

# <a name="python"></a>[Python](#tab/python)

[앱의 ID를 사용하는 Python 콘솔 앱에서 토큰 획득 및 Microsoft Graph API 호출](quickstart-v2-python-daemon.md) 빠른 시작을 시도해 보세요.

---

## <a name="next-steps"></a>다음 단계

자세한 정보를 알아보는 데 도움이 되는 몇 가지 링크는 다음과 같습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

[앱의 ID를 사용하는 .NET 코어 콘솔 앱에서 토큰 획득 및 Microsoft Graph API 호출](quickstart-v2-netcore-daemon.md) 빠른 시작을 시도해 보세요.

# <a name="java"></a>[Java](#tab/java)

[앱의 ID를 사용하는 Java 콘솔 앱에서 토큰 획득 및 Microsoft Graph API 호출](quickstart-v2-java-daemon.md) 빠른 시작을 시도해 보세요.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

[앱의 ID를 하는 Node.js 콘솔 앱에서 토큰 획득 및 Microsoft Graph API 호출](quickstart-v2-nodejs-console.md) 빠른 시작을 시도해 보세요.

# <a name="python"></a>[Python](#tab/python)

[앱의 ID를 사용하는 Python 콘솔 앱에서 토큰 획득 및 Microsoft Graph API 호출](quickstart-v2-python-daemon.md) 빠른 시작을 시도해 보세요.

---
