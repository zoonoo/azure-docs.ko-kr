---
title: Facebook 인증 구성
description: 앱 서비스 또는 Azure Functions 앱의 ID 공급자로 Facebook 인증을 구성하는 방법에 대해 알아봅니다.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9a2d390a5647ed90284730e9186e981b8e699d10
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438011"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Facebook 로그인을 사용하도록 앱 서비스 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Facebook을 인증 공급자로 사용하도록 Azure 앱 서비스 또는 Azure 함수를 구성하는 방법을 보여 주며 있습니다.

이 문서의 절차를 완료하려면 확인된 이메일 주소와 휴대폰 번호가 있는 Facebook 계정이 필요합니다. 새 Facebook 계정을 만들려면 [facebook.com]으로 이동하세요.

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Facebook을 사용하여 애플리케이션 등록

1. [Facebook 개발자] 웹 사이트로 이동하여 Facebook 계정 자격 증명으로 로그인합니다.

   개발자를 위한 Facebook 계정이 없는 경우 **시작하기를** 선택하고 등록 단계를 따르세요.
1. **내 앱** > **추가 새 앱을**선택합니다.
1. **이름 표시** 필드:
   1. 앱에 고유한 이름을 입력합니다.
   1. 연락처 **전자 메일을**제공합니다.
   1. **앱 ID 만들기를**선택합니다.
   1. 보안 검사를 완료합니다.

   새 Facebook 앱의 개발자 대시보드가 열립니다.
1. **대시보드** > **페이스 북 로그인** > **웹 설정** > 을**선택합니다.**
1. **페이스 북 로그인에서**왼쪽 탐색에서 **설정을**선택합니다.
1. 유효한 **OAuth 리디렉션 URIS** 필드에 `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`를 입력합니다. Azure 앱 `<app-name>` 서비스 앱의 이름으로 바꿔야 합니다.
1. **변경 내용 저장**을 선택합니다.
1. 왼쪽 창에서**기본** **설정을** > 선택합니다. 
1. 앱 **보안 정보** 필드에서 **표시를**선택합니다. **앱 ID** 및 **앱 보안**정보의 값을 복사합니다. 나중에 이를 사용하여 Azure에서 앱 서비스 앱을 구성합니다.

   > [!IMPORTANT]
   > 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.
   >

1. 응용 프로그램을 등록하는 데 사용한 Facebook 계정은 앱관리자입니다. 이 시점에서 관리자만 이 응용 프로그램에 로그인할 수 있습니다.

   다른 Facebook 계정을 인증하려면 **앱 검토를** 선택하고 **앱 이름> 공개로 \<설정하여** 일반 대중이 Facebook 인증을 사용하여 앱에 액세스할 수 있도록 설정합니다.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>애플리케이션에 Facebook 정보 추가

1. [Azure 포털에] 로그인하고 앱 서비스 앱으로 이동합니다.
1. **설정** > **인증/권한 부여를**선택하고 **앱 서비스 인증이** **켜졌는지**확인합니다.
1. **Facebook을**선택한 다음 이전에 획득한 앱 ID 및 앱 비밀 값에 붙여넣습니다. 응용 프로그램에 필요한 모든 범위를 활성화합니다.
1. **확인**을 선택합니다.

   ![모바일 앱 페이스 북 설정의 스크린 샷][0]

    기본적으로 App Service는 인증을 제공하지만 사이트 콘텐츠 및 API에 대한 권한 있는 액세스를 제한하지는 않습니다. 앱 코드에서 사용자에게 권한을 부여해야 합니다.
1. (선택 사항) Facebook에서 인증한 사용자에 대해서만 액세스를 제한하려면 요청이 **Facebook에** **인증되지 않은 경우 취할 조치를** 설정합니다. 이 기능을 설정하면 앱에서 모든 요청을 인증해야 합니다. 또한 인증을 위해 인증되지 않은 모든 요청을 Facebook으로 리디렉션합니다.

   > [!CAUTION]
   > 이러한 방식으로 액세스를 제한하는 것은 앱에 대한 모든 호출에 적용되며, 이는 많은 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지가 있는 앱에는 바람직하지 않을 수 있습니다. 이러한 응용 프로그램의 경우 앱이 수동으로 인증 자체를 시작하도록 **익명 요청 허용(작업 없음)이** 선호될 수 있습니다. 자세한 내용은 [인증 흐름을](overview-authentication-authorization.md#authentication-flow)참조하십시오.

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
[Azure portal]: https://portal.azure.com/
