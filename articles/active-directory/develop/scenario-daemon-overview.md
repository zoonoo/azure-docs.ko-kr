---
title: 웹 Api를 호출 하는 디먼 앱 빌드-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 디먼 앱을 빌드하는 방법을 알아봅니다.
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70a8c97ee1e10d53cd8da9687a9109eb4ce8ae3d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965571"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>시나리오: 웹 Api를 호출 하는 디먼 응용 프로그램

웹 Api를 호출 하는 디먼 응용 프로그램을 빌드하는 데 필요한 모든 것을 알아보세요.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Áttekintés

응용 프로그램은 사용자를 대신 하 여 웹 API를 대신 호출 하는 토큰을 획득할 수 있습니다. 이 시나리오는 디먼 응용 프로그램에 유용 합니다. 표준 OAuth 2.0 [클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md) 부여를 사용 하 고 있습니다.

![Démonalkalmazások](./media/scenario-daemon-app/daemon-app.svg)

다음은 디먼 앱에 대 한 사용 사례의 몇 가지 예입니다.

- 사용자를 프로 비전 또는 관리 하거나 디렉터리에서 일괄 처리를 수행 하는 데 사용 되는 웹 응용 프로그램
- 백그라운드에서 실행 되는 운영 체제 서비스 또는 배치 작업을 수행 하는 데스크톱 응용 프로그램 (예: Windows의 windows 서비스 또는 Linux의 디먼 프로세스)
- 특정 사용자가 아닌 디렉터리를 조작 해야 하는 웹 Api

비 디먼 응용 프로그램이 클라이언트 자격 증명을 사용 하는 또 다른 일반적인 경우는 다음과 같습니다. 사용자를 대신 하 여 작동 하는 경우에도 기술적인 이유로 웹 API 또는 리소스에 액세스 해야 합니다. 예는 KeyVault의 암호 또는 캐시에 대 한 Azure SQL database에 대 한 액세스입니다.

고유 id에 대 한 토큰을 획득 하는 응용 프로그램:

- 기밀 클라이언트 응용 프로그램입니다. 이러한 앱은 사용자와 독립적으로 리소스에 액세스 하는 경우 id를 증명 해야 합니다. 또한 이러한 앱은 Azure Active Directory (Azure AD) 테 넌 트 관리자가 승인 해야 하는 중요 한 앱입니다.
- Azure AD에 비밀 (응용 프로그램 암호 또는 인증서)을 등록 했습니다. 이 암호는 토큰을 가져오기 위해 Azure AD를 호출 하는 동안 전달 됩니다.

## <a name="specifics"></a>자세히

> [!IMPORTANT]
>
> - 사용자 상호 작용은 디먼 응용 프로그램에서 가능 하지 않습니다. 디먼 응용 프로그램에는 고유한 id가 필요 합니다. 이러한 유형의 응용 프로그램은 응용 프로그램 id를 사용 하 고 응용 프로그램 ID, 자격 증명 (암호 또는 인증서) 및 응용 프로그램 ID URI를 Azure AD에 제공 하 여 액세스 토큰을 요청 합니다. 성공적으로 인증 된 후 디먼은 Microsoft id 플랫폼 끝점에서 액세스 토큰 및 새로 고침 토큰을 수신 합니다 .이 끝점은 웹 API를 호출 하는 데 사용 되며 필요에 따라 새로 고쳐집니다.
> - 사용자 상호 작용은 가능 하지 않으므로 증분 동의가 가능 하지 않습니다. 모든 필수 API 권한은 응용 프로그램 등록 시 구성 해야 하며, 응용 프로그램 코드는 정적으로 정의 된 사용 권한만 요청 합니다. 이는 디먼 응용 프로그램이 증분 동의를 지원 하지 않는다는 것을 의미 하기도 합니다.

개발자를 위해이 시나리오에 대 한 종단 간 환경에는 다음과 같은 측면이 있습니다.

- 디먼 응용 프로그램은 Azure AD 테 넌 트에서만 작동할 수 있습니다. Microsoft 개인 계정을 조작 하려고 하는 디먼 응용 프로그램을 빌드하는 것은 의미가 없습니다. LOB (기간 업무) 앱 개발자 인 경우 테 넌 트에서 디먼 앱을 만듭니다. ISV 인 경우 다중 테 넌 트 데몬 응용 프로그램을 만드는 것이 좋습니다. 각 테 넌 트 관리자가 동의한 해야 합니다.
- [응용 프로그램 등록](./scenario-daemon-app-registration.md)중에는 **회신 URI** 가 필요 하지 않습니다. 암호 또는 인증서 또는 서명 된 어설션을 Azure AD와 공유 해야 하며, 응용 프로그램 사용 권한을 요청 하 고 해당 앱 사용 권한을 사용 하도록 관리자 동의를 부여 해야 합니다.
- 응용 프로그램 [구성](./scenario-daemon-app-configuration.md) 에서는 응용 프로그램을 등록 하는 동안 Azure AD와 공유 되는 클라이언트 자격 증명을 제공 해야 합니다.
- 클라이언트 자격 증명 흐름을 사용 하 여 토큰을 획득 하는 데 사용 되는 [범위](scenario-daemon-acquire-token.md#scopes-to-request) 는 정적 범위 여야 합니다.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [디먼 앱-앱 등록](./scenario-daemon-app-registration.md)
