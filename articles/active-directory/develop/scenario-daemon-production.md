---
title: 디먼 앱 호출 web Api (프로덕션으로 이동)-Microsoft id 플랫폼
description: 디먼 앱을 작성 하는 방법은 웹 Api (프로덕션으로 이동)를 호출 되는
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 627dab0cb23800664c5fb5b3df9c61f5071d4b87
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545398"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>웹을 호출 하는 디먼 앱 Api-프로덕션 환경으로 이동

가져올 서비스-투-서비스 호출에 대 한 토큰을 사용 하는 방법을 배웠으므로 프로덕션 앱을 이동 하는 방법에 알아봅니다.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>배포-다중 테 넌 트 디먼 앱의 경우

있는지 확인 해야 여러 테 넌 트를 실행할 수 있는 디먼 응용 프로그램을 만드는 ISV 인 경우 테 넌 트 관리자:

- 응용 프로그램에 대 한 서비스 주체를 프로 비전
- 응용 프로그램에 부여 동의

이러한 작업을 수행 하는 방법에 고객에 게 설명 해야 합니다. 자세한 내용은 참조 하세요. [전체 테 넌 트에 대 한 동의 요청](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)합니다.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>다음 단계

자세한 정보에 대 한 몇 가지 링크는 다음과 같습니다.

### <a name="net"></a>.NET

- 아직 없는 경우 빠른 시작을 시도 [토큰을 획득 하 고 앱의 id를 사용 하 여 콘솔 앱에서 Microsoft Graph API 호출](./quickstart-v2-netcore-daemon.md)합니다.
- 에 대 한 설명서를 참조 합니다.
  - 인스턴스화 [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - 호출 [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- 다른 샘플/자습서:
  - [microsoft identity-플랫폼-콘솔-디먼](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) 간단한.NET Core 디먼 콘솔 응용 프로그램을 Microsoft Graph를 쿼리 하는 테 넌 트의 사용자를 표시 하는 기능입니다.

    ![토폴로지](media/scenario-daemon-app/daemon-app-sample.svg)

    동일한 샘플에는 인증서를 사용 하 여 변형을 보여 줍니다.

    ![토폴로지](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) 대신 사용자를 대신 하 여 응용 프로그램의 id를 사용 하 여 Microsoft Graph에서 데이터를 동기화 하는 ASP.NET MVC 웹 응용 프로그램 기능입니다. 이 샘플에는 관리자 동의 프로세스를 보여 줍니다.

    ![토폴로지](media/scenario-daemon-app/damon-app-sample-web.svg)

### <a name="python"></a>Python

MSAL Python은 현재 공개 미리 보기로 제공 됩니다. 자세한 내용은 참조 하세요. [MSAL Python 클라이언트 자격 증명 저장소에서 샘플](https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/dev/sample/client_credentials_sample.py)합니다.

### <a name="java"></a>Java

MSAL Python은 현재 공개 미리 보기로 제공 됩니다. 자세한 내용은 참조 하세요. [MSAL Java 저장소에서 샘플](https://github.com/AzureAD/azure-activedirectory-library-for-java/tree/dev/src/samples)합니다.