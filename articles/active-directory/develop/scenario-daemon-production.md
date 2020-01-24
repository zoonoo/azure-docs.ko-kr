---
title: 웹 Api를 호출 하는 디먼 앱을 프로덕션으로 이동-Microsoft id 플랫폼 | Microsoft
description: 웹 Api를 호출 하는 디먼 앱을 프로덕션으로 이동 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 990273e84bfceb9f4a19eae8bf5890e8303a5857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702269"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>웹 Api를 호출 하는 디먼 앱-프로덕션으로 이동

서비스 간 호출에 대 한 토큰을 획득 하 고 사용 하는 방법을 배웠으므로 이제 앱을 프로덕션으로 이동 하는 방법을 알아보세요.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>배포-다중 테 넌 트 디먼 앱의 사례

여러 테 넌 트에서 실행할 수 있는 디먼 응용 프로그램을 만드는 ISV 인 경우 테 넌 트 관리자가 다음을 확인 해야 합니다.

- 응용 프로그램에 대 한 서비스 주체를 프로 비전 합니다.
- 응용 프로그램에 동의를 부여 합니다.

이러한 작업을 수행 하는 방법을 고객에 게 설명 해야 합니다. 자세한 내용은 [전체 테 넌 트에 대 한 동의 요청](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)을 참조 하세요.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 링크를 참조 하세요.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- 아직 시작 하지 않은 경우 빠른 시작에서 토큰 획득을 시도 하 [고 앱의 id를 사용 하 여 콘솔 앱에서 MICROSOFT GRAPH API를 호출](./quickstart-v2-netcore-daemon.md)합니다.
- 참조 설명서:
  - [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) 인스턴스화
  - [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder) 호출
- 기타 샘플/자습서:
  - [microsoft](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) ---platform-디먼 기능 Microsoft Graph를 쿼리 하는 테 넌 트의 사용자를 표시 하는 간단한 .net Core 디먼 콘솔 응용 프로그램입니다.

    ![토폴로지](media/scenario-daemon-app/daemon-app-sample.svg)

    동일한 샘플은 인증서를 사용 하는 변형도 보여 줍니다.

    ![토폴로지](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [webapp-디먼](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) 기능은 사용자 대신 응용 프로그램의 id를 사용 하 여 Microsoft Graph에서 데이터를 동기화 하는 ASP.NET MVC 웹 응용 프로그램을 제공 합니다. 또한이 샘플에서는 관리자 동의 프로세스를 보여 줍니다.

    ![토폴로지](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

[응용 프로그램의 id를 사용 하 여 빠른 시작에서 토큰 획득을 시도 하 고 Python 콘솔 앱에서 MICROSOFT GRAPH API를 호출](./quickstart-v2-python-daemon.md)합니다.

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java는 현재 공개 미리 보기로 제공 됩니다. 자세한 내용은 [Msal Java dev 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)을 참조 하세요.

---
