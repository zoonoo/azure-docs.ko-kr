---
title: Twitter 인증 구성
description: App Service 또는 Azure Functions 앱에 대 한 id 공급자로 Twitter 인증을 구성 하는 방법에 대해 알아봅니다.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 11c913b12b4dcb7d2a5ffa532064b347b82904ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80519901"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Twitter 로그인을 사용 하도록 App Service 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Twitter를 인증 공급자로 사용 하도록 Azure App Service 또는 Azure Functions를 구성 하는 방법을 보여 줍니다.

이 문서의 절차를 완료 하려면 확인 된 전자 메일 주소 및 전화 번호를 포함 하는 Twitter 계정이 필요 합니다. 새 Twitter 계정을 만들려면 [twitter.com]으로 이동합니다.

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Twitter를 사용하여 애플리케이션 등록

1. [Azure Portal] 에 로그인 하 고 응용 프로그램으로 이동 합니다. **URL**을 복사합니다. Twitter 앱을 구성 하는 데 사용 합니다.
1. [Twitter 개발자] 웹 사이트로 이동 하 고 twitter 계정 자격 증명을 사용 하 여 로그인 한 다음 **앱 만들기**를 선택 합니다.
1. **앱 이름** 및 새 앱에 대 한 **응용 프로그램 설명을** 입력 합니다. 응용 프로그램의 **url** 을 **웹 사이트 url** 필드에 붙여넣습니다. **콜백 url** 섹션에서 App Service 앱의 HTTPS url을 입력 하 고 경로를 추가 `/.auth/login/twitter/callback` 합니다. 예들 들어 `https://contoso.azurewebsites.net/.auth/login/twitter/callback`입니다.
1. 페이지 맨 아래에 **이 앱을 사용 하는 방법**에 대 한 정보를 100 자 이상 입력 하 고 **만들기**를 선택 합니다. 팝업에서 **만들기** 를 다시 클릭 합니다. 응용 프로그램 세부 정보가 표시 됩니다.
1. **Keys and Access Tokens** 탭을 선택합니다.

   다음 값을 적어둡니다.
   - API 키
   - API 비밀 키

   > [!NOTE]
   > API 비밀 키는 중요 한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마세요.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>애플리케이션에 Twitter 정보 추가

1. [Azure Portal]에서 애플리케이션으로 이동합니다.
1. **설정** > **인증 / 권한 부여**를 선택하고 **App Service 인증**이 **켜기**인지 확인합니다.
1. **Twitter**를 선택 합니다.
1. `API key`이전에 얻은 및 `API secret key` 값을 붙여넣습니다.
1. **확인**을 선택합니다.

   ![모바일 앱 Twitter 설정의 스크린샷][1]

   기본적으로 App Service는 인증을 제공 하지만 사이트 콘텐츠 및 Api에 대 한 권한 있는 액세스를 제한 하지 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.

1. (선택 사항) Twitter에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **Twitter**에 **요청이 인증되지 않으면 수행할 동작**을 설정합니다. 이 기능을 설정하면 앱에서 모든 요청을 인증해야 합니다. 또한 인증을 위해 모든 인증 되지 않은 요청을 Twitter로 리디렉션합니다.

   > [!CAUTION]
   > 이러한 방식으로 액세스를 제한하면 모든 앱 호출에 제한이 적용되며, 여러 단일 페이지 애플리케이션이 그렇듯이 공개적으로 사용 가능한 홈페이지가 있는 앱에는 이 방법이 바람직하지 않을 수 있습니다. 이러한 애플리케이션에서는 **익명 요청 허용(작업 없음)** 으로 설정하고, 앱에서 수동으로 인증을 시작하는 것이 더 좋은 방법일 수 있습니다. 자세한 내용은 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조하세요.

1. **저장**을 선택합니다.

이제 앱에서 Twitter를 인증에 사용할 준비가 되었습니다.

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter 개발자]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
