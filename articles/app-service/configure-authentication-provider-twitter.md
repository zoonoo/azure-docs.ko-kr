---
title: Twitter 인증 구성
description: 앱 서비스 앱의 ID 공급자로 트위터 인증을 구성하는 방법을 알아보세요.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom: seodec18
ms.openlocfilehash: 794f671b36b5aeb9f19cf5d80e488500cedb1098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207145"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>트위터 로그인을 사용하도록 앱 서비스 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 트위터를 인증 공급자로 사용하도록 Azure 앱 서비스를 구성하는 방법을 보여 주며 있습니다.

이 문서의 절차를 완료하려면 확인된 이메일 주소와 전화 번호가 있는 트위터 계정이 필요합니다. 새 Twitter 계정을 만들려면 [twitter.com]으로 이동합니다.

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Twitter를 사용하여 애플리케이션 등록

1. [Azure 포털에] 로그인하고 응용 프로그램으로 이동합니다. **URL**을 복사합니다. 트위터 앱을 구성하는 데 사용할 수 있습니다.
1. [트위터 개발자] 웹 사이트로 이동하여 트위터 계정 자격 증명으로 로그인한 다음 **앱 만들기를**선택합니다.
1. 새 **앱에** 대한 앱 이름과 **응용 프로그램 설명을** 입력합니다. 응용 프로그램의 **URL을** 웹 **사이트 URL** 필드에 붙여넣습니다. **콜백 URL** 섹션에서 앱 서비스 앱의 HTTPS URL을 입력하고 `/.auth/login/twitter/callback`경로를 연결합니다. `https://contoso.azurewebsites.net/.auth/login/twitter/callback`)을 입력합니다.
1. 페이지 하단에 **이 앱이 어떻게 사용되는지 알려주세요에**100자 이상을 입력한 다음 **만들기를**선택합니다. 팝업에서 다시 **만들기를** 클릭합니다. 응용 프로그램 세부 정보가 표시됩니다.
1. **Keys and Access Tokens** 탭을 선택합니다.

   다음 값을 기록해 둡을 확인합니다.
   - API 키
   - API 비밀 키

   > [!NOTE]
   > API 비밀 키는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마세요.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>애플리케이션에 Twitter 정보 추가

1. [Azure 포털의]응용 프로그램으로 이동합니다.
1. **설정** > **인증/권한 부여를**선택하고 **앱 서비스 인증이** **켜졌는지**확인합니다.
1. **트위터를**선택합니다.
1. 이전에 얻은 `API key` 값과 `API secret key` 에 붙여넣습니다.
1. **확인**을 선택합니다.

   ![모바일 앱 트위터 설정의 스크린 샷][1]

   기본적으로 App Service는 인증을 제공하지만 사이트 콘텐츠 및 API에 대한 권한 있는 액세스를 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.

1. (선택 사항) Twitter에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **Twitter**에 **요청이 인증되지 않으면 수행할 동작**을 설정합니다. 이 기능을 설정하면 앱에서 모든 요청을 인증해야 합니다. 또한 인증을 위해 인증되지 않은 모든 요청을 트위터로 리디렉션합니다.

   > [!CAUTION]
   > 이러한 방식으로 액세스를 제한하는 것은 앱에 대한 모든 호출에 적용되며, 이는 많은 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지가 있는 앱에는 바람직하지 않을 수 있습니다. 이러한 응용 프로그램의 경우 앱이 수동으로 인증 자체를 시작하도록 **익명 요청 허용(작업 없음)이** 선호될 수 있습니다. 자세한 내용은 [인증 흐름을](overview-authentication-authorization.md#authentication-flow)참조하십시오.

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
[Azure 포털]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
