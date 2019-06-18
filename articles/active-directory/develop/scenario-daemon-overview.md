---
title: 디먼 앱 웹 Api 호출 (개요)-Microsoft id 플랫폼
description: 웹 Api를 호출 하는 디먼 앱을 빌드하는 방법을 알아봅니다
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
ms.openlocfilehash: 578b7cdb38b7df3fab5885d773354a36f76a4cfb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075882"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>시나리오: 웹 Api를 호출 하는 디먼 응용 프로그램

웹 Api를 호출 하는 디먼 응용 프로그램을 만드는 데 필요한 모든에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>개요

응용 프로그램 (사용자) 대신 하지 자체 대신 web API를 호출 하는 토큰을 획득할 수 있습니다. 이 시나리오는 디먼 응용 프로그램에 유용 합니다. 표준 OAuth 2.0을 사용 중인 [클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md) 권한을 부여 합니다.

![디먼 앱](./media/scenario-daemon-app/daemon-app.svg)

디먼 앱의 사용 사례 중 몇 가지 예는 다음과 같습니다.

- 프로 비전 하는 데 사용 됩니다 또는 사용자를 관리 하는 웹 응용 프로그램 또는 디렉터리에 프로세스를 일괄 처리 수행
- 일괄 처리 작업 또는 백그라운드에서 실행 되는 운영 체제 서비스를 수행 하는 (Windows, windows 서비스) 또는 linux 디먼 프로세스와 같은 데스크톱 응용 프로그램
- 디렉터리에 특정 하지 않은 사용자를 조작 해야 하는 web Api

다른 일반적인 사례 아닌 디먼 응용 프로그램에서 클라이언트 자격 증명을 사용 하는 위치는: 기술적인 이유로 웹 API 또는 해당 id 사용 하 여 리소스에 액세스 하는 데 필요한 사용자를 대신 하 여 역할을 하는 경우에 합니다. 예로 캐시에 대 한 key Vault 또는 Azure SQL database의 비밀에 액세스 합니다.

자체 id에 대 한 토큰을 획득 하는 응용 프로그램의 경우:

- 기밀 클라이언트 응용 프로그램입니다. 이러한 앱을 사용자와 독립적으로 리소스에 액세스는 신원을 증명 하기 위해 필요 합니다. Azure Active Directory (Azure AD) 테 넌 트 관리자가 승인 해야 하는 대신 중요 한 앱 또한 하기도 합니다.
- Azure AD를 사용 하 여 비밀 (응용 프로그램 암호 또는 인증서)을 등록 했습니다. 이 비밀 토큰을 가져오려면 Azure AD 호출 하는 동안 전달 됩니다.

## <a name="specifics"></a>세부 정보

> [!IMPORTANT]
>
> - 사용자 상호 작용 디먼 응용 프로그램을 사용 하 여 수는 없습니다. 디먼 응용 프로그램에는 고유한 id에 필요합니다. 해당 응용 프로그램 ID, 자격 증명 (암호 또는 인증서) 및 응용 프로그램 제공 및 해당 응용 프로그램 id를 사용 하 여 액세스 토큰을 요청 하는 이러한 유형의 응용 프로그램 ID URI이 Azure AD입니다. 인증이 성공 하면 디먼은 다음 웹 API를 호출 하는 데 사용 됩니다 (및 필요에 따라 새로 고쳐집니다)는 Microsoft id 플랫폼 끝점에서 액세스 토큰 (및 새로 고침 토큰) 받습니다.
> - 사용자 상호 작용 불가능 하기 때문에 증분 동의 수 없습니다. 필요한 모든 API 권한 응용 프로그램 등록 시 구성 해야 하 고 응용 프로그램의 코드를 정적으로 정의 된 사용 권한 요청 뿐입니다. 즉, 증분 동의 디먼 응용 프로그램을 지원 하지 않습니다.

개발자를 위한이 시나리오에 대 한 종단 간 환경에 다음과 같은 측면에 있습니다.

- 디먼 응용 프로그램에서 Azure AD 테 넌 트 에서만 작동할 수 있습니다. Microsoft 개인 계정 조작 하려고 하는 디먼 응용 프로그램을 빌드하려면 바람직하지 않습니다. 기간 업무 (LOB) 앱 개발자 인 경우 테 넌 트의 디먼 앱을 만들어야 합니다. ISV 인 경우 다중 테 넌 트 디먼 응용 프로그램을 만드는 것이 좋습니다. 각 테 넌 트 관리자가 승인 필요
- 중 합니다 [응용 프로그램 등록](./scenario-daemon-app-registration.md)서 **회신 URI** 필요 하지 않습니다. Azure AD를 사용 하 여 암호 또는 인증서를 공유 해야 하 고 응용 프로그램 사용 권한을 요청 하 고 해당 앱 사용 권한을 사용 하도록 관리자 동의 부여 해야 합니다.
- 합니다 [응용 프로그램 구성](./scenario-daemon-app-configuration.md) 응용 프로그램 등록 하는 동안 Azure AD를 사용 하 여 공유로 클라이언트 자격 증명을 제공 해야 합니다.
- 합니다 [범위](scenario-daemon-acquire-token.md#scopes-to-request) 정적 범위를 흐름에 필요한 클라이언트 자격 증명을 사용 하 여 토큰을 획득 하는 데 사용 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [디먼 앱-앱 등록](./scenario-daemon-app-registration.md)
