---
title: Facebook 인증 구성
description: App Service 또는 Azure Functions 앱에 대 한 id 공급자로 Facebook 인증을 구성 하는 방법에 대해 알아봅니다.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: b6aad323c0d6fa8f59c9fad203640c477b162503
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80519951"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Facebook 로그인을 사용 하도록 App Service 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Facebook을 인증 공급자로 사용 하도록 Azure App Service 또는 Azure Functions를 구성 하는 방법을 보여 줍니다.

이 문서의 절차를 완료 하려면 확인 된 전자 메일 주소와 휴대폰 번호가 포함 된 Facebook 계정이 필요 합니다. 새 Facebook 계정을 만들려면 [facebook.com]으로 이동하세요.

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Facebook을 사용하여 애플리케이션 등록

1. [Facebook 개발자] 웹 사이트로 이동한 다음 facebook 계정 자격 증명으로 로그인 합니다.

   개발자 용 Facebook 계정이 없는 경우 **시작** 을 선택 하 고 등록 단계를 따릅니다.
1. **내 앱**에서  >  **새 앱 추가**를 선택 합니다.
1. **표시 이름** 필드:
   1. 앱에 대 한 고유한 이름을 입력 합니다.
   1. **연락처 전자 메일**을 제공 합니다.
   1. **앱 ID 만들기**를 선택합니다.
   1. 보안 검사를 완료 합니다.

   새 Facebook 앱에 대 한 개발자 대시보드가 열립니다.
1. **대시보드**  >  **Facebook 로그인**  >  **설정**  >  **웹**을 선택 합니다.
1. **Facebook 로그인**아래의 왼쪽 탐색 영역에서 **설정**을 선택 합니다.
1. **유효한 OAuth 리디렉션 uri** 필드에를 입력 `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` 합니다. 을 `<app-name>` Azure App Service 앱의 이름으로 바꾸어야 합니다.
1. **변경 내용 저장**을 선택합니다.
1. 왼쪽 창에서 **설정**  >  **기본**을 선택 합니다. 
1. **앱 비밀** 필드에서 **표시**를 선택 합니다. **앱 ID** 및 **앱 암호**의 값을 복사 합니다. 나중에 Azure에서 App Service 앱을 구성 하는 데 사용 합니다.

   > [!IMPORTANT]
   > 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.
   >

1. 응용 프로그램을 등록 하는 데 사용한 Facebook 계정은 앱의 관리자입니다. 이 시점에서 관리자만이 응용 프로그램에 로그인 할 수 있습니다.

   다른 Facebook 계정을 인증 하려면 **앱 검토** 를 선택 하 고 ** \<your-app-name> 공용** 으로 설정을 사용 하도록 설정 하 여 일반 공용이 Facebook 인증을 사용 하 여 앱에 액세스할 수 있도록 합니다.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>애플리케이션에 Facebook 정보 추가

1. [Azure Portal] 에 로그인 하 여 App Service 앱으로 이동 합니다.
1. **설정** > **인증 / 권한 부여**를 선택하고 **App Service 인증**이 **켜기**인지 확인합니다.
1. **Facebook**을 선택 하 고 이전에 가져온 앱 ID 및 앱 암호 값을 붙여넣습니다. 응용 프로그램에 필요한 모든 범위를 사용 하도록 설정 합니다.
1. **확인**을 선택합니다.

   ![모바일 앱 Facebook 설정의 스크린샷][0]

    기본적으로 App Service는 인증을 제공 하지만 사이트 콘텐츠 및 Api에 대 한 권한 있는 액세스를 제한 하지 않습니다. 앱 코드에서 사용자에 게 권한을 부여 해야 합니다.
1. 필드 Facebook에서 인증 된 사용자 에게만 액세스를 제한 하려면 **facebook**에 **요청이 인증 되지 않은 경우 수행할 작업** 을 설정 합니다. 이 기능을 설정하면 앱에서 모든 요청을 인증해야 합니다. 또한 인증을 위해 모든 인증 되지 않은 요청을 Facebook으로 리디렉션합니다.

   > [!CAUTION]
   > 이러한 방식으로 액세스를 제한하면 모든 앱 호출에 제한이 적용되며, 여러 단일 페이지 애플리케이션이 그렇듯이 공개적으로 사용 가능한 홈페이지가 있는 앱에는 이 방법이 바람직하지 않을 수 있습니다. 이러한 애플리케이션에서는 **익명 요청 허용(작업 없음)** 으로 설정하고, 앱에서 수동으로 인증을 시작하는 것이 더 좋은 방법일 수 있습니다. 자세한 내용은 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조하세요.

1. **저장**을 선택합니다.

이제 앱에서 인증을 위해 Facebook을 사용할 준비가 되었습니다.

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook 개발자]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
