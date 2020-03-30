---
title: 웹 API를 프로덕션으로 호출하는 데몬 앱 이동 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 프로덕션으로 호출하는 데몬 앱을 이동하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: db5f52c95daf4e93c140b4c93f39dad19971319d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262621"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>웹 API를 호출하는 데몬 앱 - 프로덕션으로 이동

이제 서비스 간 호출에 토큰을 획득하고 사용하는 방법을 배웠으니 앱을 프로덕션으로 이동하는 방법을 알아봅니다.

## <a name="deployment---multitenant-daemon-apps"></a>배포 - 다중 테넌트 데몬 앱

ISV가 여러 테넌트에서 실행할 수 있는 데몬 응용 프로그램을 만드는 경우 테넌트 관리자가 있는지 확인해야 합니다.

- 응용 프로그램에 대한 서비스 주체를 프로비전합니다.
- 응용 프로그램에 대한 동의를 부여합니다.

이러한 작업을 수행하는 방법을 고객에게 설명해야 합니다. 자세한 내용은 [전체 테넌트에 대한 동의 요청을](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)참조하십시오.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음과 같은 몇 가지 링크입니다.

# <a name="net"></a>[.NET](#tab/dotnet)

- 빠른 시작: [토큰을 획득하고 앱의 ID를 사용하여 콘솔 앱에서 Microsoft 그래프 API를 호출합니다.](./quickstart-v2-netcore-daemon.md)
- 다음을 위한 참조 문서:
  - [정보 인스턴스화클라이언트응용 프로그램](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - [호출 acquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- 기타 샘플/자습서:
  - [마이크로소프트-아이덴티티-플랫폼-콘솔-데몬은](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) 마이크로소프트 그래프를 쿼리하는 테넌트의 사용자를 표시하는 간단한 .NET 코어 데몬 콘솔 응용 프로그램을 특징으로 한다.

    ![샘플 데몬 앱 토폴로지](media/scenario-daemon-app/daemon-app-sample.svg)

    동일한 샘플에서도 인증서의 변형을 보여 줍니다.

    ![샘플 데몬 앱 토폴로지 - 인증서](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-ID-플랫폼-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) 은 사용자를 대신하는 대신 응용 프로그램의 ID를 사용하여 Microsoft 그래프의 데이터를 동기화하는 ASP.NET MVC 웹 응용 프로그램을 제공합니다. 이 샘플에서는 관리자 동의 프로세스도 보여 줍니다.

    ![토폴로지](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

빠른 시작을 시도하고 [토큰을 획득하고 응용 프로그램의 ID를 사용하여 파이썬 콘솔 앱에서 마이크로 소프트 그래프 API를 호출합니다.](./quickstart-v2-python-daemon.md)

# <a name="java"></a>[Java](#tab/java)

MSAL Java는 현재 공개 미리 보기 상태입니다. 자세한 내용은 [MSAL Java 개발자 샘플을](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)참조하십시오.

---
