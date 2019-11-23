---
title: Microsoft 계정 인증 구성 - Azure App Service
description: App Service 앱에 대 한 Microsoft 계정 인증을 구성 하는 방법을 알아봅니다.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 70af534e6bcd0039dbc602a5ebc3fc35fb145e79
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176944"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>Microsoft 계정 로그인을 사용 하도록 App Service 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Microsoft 계정을 인증 공급자로 사용하도록 Azure App Service를 구성하는 방법을 보여 줍니다. 

## <a name="register-microsoft-account"> </a>Microsoft 계정을 사용하여 앱 등록

1. Azure Portal [**앱 등록**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 으로 이동 합니다. 필요한 경우 Microsoft 계정를 사용 하 여 로그인 합니다.
1. **새 등록**을 선택 하 고 응용 프로그램 이름을 입력 합니다.
1. **리디렉션 uri**에서 **웹**을 선택한 다음 `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`를 입력 합니다. *\<앱 도메인 이름 >* 를 앱의 도메인 이름으로 바꿉니다.  `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`) 을 입력합니다. URL에서 HTTPS 체계를 사용 해야 합니다.

1. **등록**을 선택합니다.
1. **응용 프로그램 (클라이언트) ID**를 복사 합니다. 나중에 필요합니다.
1. 왼쪽 창에서 **인증서 & 비밀** > **새 클라이언트 암호**를 선택 합니다. 설명을 입력 하 고, 유효 기간을 선택 하 고, **추가**를 선택 합니다.
1. **인증서 & 암호** 페이지에 표시 되는 값을 복사 합니다. 페이지를 나가면 다시 표시 되지 않습니다.

    > [!IMPORTANT]
    > 암호는 중요한 보안 자격 증명입니다. 다른 사람과 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.

## <a name="secrets"></a>App Service 애플리케이션에 Microsoft 계정 정보 추가

1. [Azure Portal]에서 응용 프로그램으로 이동 합니다.
1. 설정 > **인증/권한 부여**를 선택 하 고 **App Service 인증이** **설정** 되어 있는지 확인 **합니다.**
1. **인증 공급자**아래에서 **Microsoft 계정**을 선택 합니다. 이전에 얻은 응용 프로그램 (클라이언트) ID와 클라이언트 암호를 붙여넣습니다. 응용 프로그램에 필요한 모든 범위를 사용 하도록 설정 합니다.
1. **확인**을 선택합니다.

   App Service는 인증을 제공 하지만 사이트 콘텐츠 및 Api에 대 한 권한 있는 액세스를 제한 하지 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.

1. 필드 Microsoft 계정 사용자에 대 한 액세스를 제한 하려면 **Microsoft 계정을 사용 하 여 로그인**할 수 있도록 **요청이 인증 되지 않은 경우 수행할 작업** 을 설정 합니다. 이 기능을 설정 하면 앱에서 모든 요청을 인증 해야 합니다. 또한 인증을 위해 인증 되지 않은 모든 요청을 Microsoft 계정로 리디렉션합니다.

   > [!CAUTION]
   > 이러한 방식으로 액세스를 제한 하는 것은 앱에 대 한 모든 호출에 적용 됩니다 .이는 여러 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지가 있는 앱에는 바람직하지 않을 수 있습니다. 이러한 응용 프로그램의 경우 앱이 수동으로 인증을 시작 하도록 **익명 요청 허용 (작업 없음)을 사용** 하는 것이 좋습니다. 자세한 내용은 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조 하세요.

1. **저장**을 선택합니다.

이제 앱에서 Microsoft 계정을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
