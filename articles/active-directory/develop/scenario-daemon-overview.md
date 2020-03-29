---
title: 웹 API를 호출하는 데몬 앱 빌드 - Microsoft ID 플랫폼 | Azure
description: 웹 API를 호출하는 데몬 앱을 빌드하는 방법 알아보기
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
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5718a23e5669de6ba16354a718d72b68d14bbf49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78894546"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>시나리오: 웹 API를 호출하는 데몬 응용 프로그램

웹 API를 호출하는 데몬 응용 프로그램을 빌드하는 데 필요한 모든 것을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>개요

응용 프로그램은 사용자를 대신하지 않고 자신을 대신하여 웹 API를 호출하는 토큰을 획득할 수 있습니다. 이 시나리오는 데몬 응용 프로그램에 유용합니다. 표준 OAuth 2.0 [클라이언트 자격 증명 부여를](v2-oauth2-client-creds-grant-flow.md) 사용합니다.

![디먼 앱](./media/scenario-daemon-app/daemon-app.svg)

다음은 데몬 앱의 사용 사례의 몇 가지 예입니다.

- 사용자를 프로비전 또는 관리하거나 디렉터리에서 일괄 처리 프로세스를 수행하는 데 사용되는 웹 응용 프로그램
- 일괄 처리 작업을 수행하는 데스크톱 응용 프로그램(예: Windows의 Windows 서비스 또는 Linux의 데몬 프로세스) 또는 백그라운드에서 실행되는 운영 체제 서비스
- 특정 사용자가 아닌 디렉터리를 조작해야 하는 웹 API

비데몬 응용 프로그램이 클라이언트 자격 증명을 사용하는 또 다른 일반적인 경우가 있습니다: 사용자를 대신하여 작동하더라도 기술적 이유로 웹 API 또는 자체 ID로 리소스에 액세스해야 합니다. 예를 들어 Azure 키 볼트의 암호 또는 캐시에 대한 Azure SQL 데이터베이스에 대한 액세스가 있습니다.

자신의 ID에 대한 토큰을 획득하는 응용 프로그램:

- 기밀 클라이언트 응용 프로그램입니다. 이러한 앱은 사용자와 독립적으로 리소스에 액세스한다는 점을 감안할 때 자신의 ID를 증명해야 합니다. 그들은 또한 오히려 민감한 애플 리 케이 션. Azure Active Directory(Azure AD) 테넌트 관리자의 승인을 받아야 합니다.
- Azure AD에 비밀(응용 프로그램 암호 또는 인증서)을 등록했습니다. 이 비밀은 토큰을 얻기 위해 Azure AD를 호출하는 동안 전달됩니다.

## <a name="specifics"></a>구체적인

> [!IMPORTANT]
>
> - 사용자는 데몬 응용 프로그램과 상호 작용할 수 없습니다. 데몬 응용 프로그램에는 고유한 ID가 필요합니다. 이러한 유형의 응용 프로그램은 해당 응용 프로그램 ID를 사용하고 응용 프로그램 ID, 자격 증명(암호 또는 인증서) 및 응용 프로그램 ID URI를 Azure AD에 표시하여 액세스 토큰을 요청합니다. 인증에 성공한 데몬은 Microsoft ID 플랫폼 끝점에서 액세스 토큰(및 새로 고침 토큰)을 받습니다. 그런 다음 이 토큰은 웹 API를 호출하는 데 사용되며 필요에 따라 새로 고쳐집니다.
> - 사용자가 데몬 응용 프로그램과 상호 작용할 수 없으므로 증분 동의는 불가능합니다. 필요한 모든 API 권한은 응용 프로그램 등록 시 구성해야 합니다. 응용 프로그램의 코드는 정적으로 정의된 사용 권한을 요청합니다. 이는 또한 데몬 응용 프로그램이 증분 동의를 지원하지 않는다는 것을 의미합니다.

개발자의 경우 이 시나리오의 종단 간 경험에는 다음과 같은 측면이 있습니다.

- Daemon 응용 프로그램은 Azure AD 테넌에서만 작동할 수 있습니다. Microsoft 개인 계정을 조작하려고 시도하는 데몬 응용 프로그램을 빌드하는 것은 의미가 없습니다. LOB(업무용) 앱 개발자인 경우 테넌트에서 데몬 앱을 만듭니다. ISV인 경우 다중 테넌트 데몬 응용 프로그램을 만들 수 있습니다. 각 테넌트 관리자는 동의를 제공해야 합니다.
- [응용 프로그램 등록](./scenario-daemon-app-registration.md)중에 회신 URI가 필요하지 않습니다. Azure AD와 암호 또는 인증서 또는 서명된 어설션을 공유해야 합니다. 또한 응용 프로그램 권한을 요청하고 이러한 앱 권한을 사용하려면 관리자의 동의를 얻어야 합니다.
- [응용 프로그램 구성은](./scenario-daemon-app-configuration.md) 응용 프로그램 등록 중에 Azure AD와 공유되는 클라이언트 자격 증명을 제공해야 합니다.
- 클라이언트 자격 증명 흐름이 있는 토큰을 획득하는 데 사용되는 [범위는](scenario-daemon-acquire-token.md#scopes-to-request) 정적 범위여야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데몬 앱 - 앱 등록](./scenario-daemon-app-registration.md)
