---
title: Google 인증 구성
description: 앱 서비스 또는 Azure Functions 앱의 ID 공급자로 Google 인증을 구성하는 방법을 알아봅니다.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: e8a9fbe6072f3628d755ad3ad5aa5a623fc3ab23
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519939"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Google 로그인을 사용하도록 앱 서비스 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Google을 인증 공급자로 사용하도록 Azure 앱 서비스 또는 Azure 함수를 구성하는 방법을 보여 주며 있습니다.

이 항목의 절차를 완료하려면 검증된 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)으로 이동하세요.

## <a name="register-your-application-with-google"></a><a name="register"> </a>Google을 사용하여 애플리케이션 등록

1. [서버 쪽 앱의 경우 Google 로그인에서](https://developers.google.com/identity/sign-in/web/server-side-flow) Google 설명서를 따라 클라이언트 ID 및 클라이언트 비밀을 만듭니다. 코드를 변경할 필요가 없습니다. 다음 정보를 사용하십시오.
    - **공인 자바스크립트 출처의** `https://<app-name>.azurewebsites.net` 경우 * \<앱 *이름>앱 이름과 함께 사용하십시오.
    - **권한 있는 리디렉션 URI의**경우 을 사용합니다. `https://<app-name>.azurewebsites.net/.auth/login/google/callback`
1. 앱 ID와 앱 비밀 값을 복사합니다.

    > [!IMPORTANT]
    > 앱 비밀은 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>애플리케이션에 Google 정보 추가

1. Azure [포털에서]앱 서비스 앱으로 이동합니다.
1. **설정** > **인증/권한 부여를**선택하고 **앱 서비스 인증이** **켜졌는지**확인합니다.
1. **Google을**선택한 다음 이전에 획득한 앱 ID 및 앱 비밀 값에 붙여넣습니다. 응용 프로그램에 필요한 모든 범위를 활성화합니다.
1. **확인**을 선택합니다.

   앱 서비스는 인증을 제공하지만 사이트 콘텐츠 및 API에 대한 권한 있는 액세스를 제한하지는 않습니다. 자세한 내용은 [사용자 권한 부여 또는 거부를](app-service-authentication-how-to.md#authorize-or-deny-users)참조하십시오.

1. (선택 사항) Google에서 인증한 사용자에게만 사이트 액세스를 제한하려면 **Google에 요청이 인증되지 않은 경우 취할 조치를** 설정합니다. **Google** 이 기능을 설정하면 앱에서 모든 요청을 인증해야 합니다. 또한 인증을 위해 인증되지 않은 모든 요청을 Google로 리디렉션합니다.

    > [!CAUTION]
    > 이러한 방식으로 액세스를 제한하는 것은 앱에 대한 모든 호출에 적용되며, 이는 많은 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지가 있는 앱에는 바람직하지 않을 수 있습니다. 이러한 응용 프로그램의 경우 앱이 수동으로 인증 자체를 시작하도록 **익명 요청 허용(작업 없음)이** 선호될 수 있습니다. 자세한 내용은 [인증 흐름을](overview-authentication-authorization.md#authentication-flow)참조하십시오.

1. **저장**을 선택합니다.

이제 앱에서 Google을 인증에 사용할 준비가 되었습니다.

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure portal]: https://portal.azure.com/

