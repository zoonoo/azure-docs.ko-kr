---
title: Google 인증 구성 - Azure App Service
description: App Service 앱에 대해 Google 인증을 구성 하는 방법을 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232139"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Google 로그인을 사용하도록 App Service 애플리케이션을 구성하는 방법
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Google을 인증 공급자로 사용하도록 Azure App Service를 구성하는 방법을 보여 줍니다.

이 항목의 절차를 완료하려면 검증된 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)으로 이동하십시오.

## <a name="register"></a>Google을 사용하여 애플리케이션 등록
1. [서버 쪽 앱에 대 한 Google 로그인](https://developers.google.com/identity/sign-in/web/server-side-flow) 의 google 설명서에 따라 다음 정보를 사용 하 여 클라이언트 ID 및 클라이언트 암호를 만듭니다 (코드를 변경 하지 않아도).
    - **권한 있는 JavaScript 원본**에 대해 `https://<app-name>.azurewebsites.net`  *\<앱 이름 >* 에서 앱 이름으로를 사용 합니다.
    - **권한 있는 리디렉션 URI**의 경우 `https://<app-name>.azurewebsites.net/.auth/login/google/callback`를 사용 합니다.
1. 클라이언트 ID 및 클라이언트 암호를 만든 후에는 해당 값을 복사 합니다.

    > [!IMPORTANT]
    > 클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.


## <a name="secrets"></a>애플리케이션에 Google 정보 추가
1. [Azure Portal]에서 App Service 앱으로 이동합니다. 왼쪽 메뉴에서 **인증/권한 부여**를 선택 합니다.
2. 인증/권한 부여 기능이 사용하도록 설정되지 않은 경우 스위치를 **On**으로 전환합니다.
3. **Google**을 클릭합니다. 이전에 가져온 앱 ID 및 앱 암호 값을 붙여넣고 필요에 따라 응용 프로그램에 필요한 모든 범위를 사용 하도록 설정 합니다. 그런 다음 **확인**을 클릭합니다.

   App Service는 인증을 제공 하지만 사이트 콘텐츠 및 Api에 대 한 권한 있는 액세스를 제한 하지 않습니다. 자세한 내용은 [사용자 권한 부여 또는 거부](app-service-authentication-how-to.md#authorize-or-deny-users)를 참조 하세요.
4. (선택 사항) Google에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **Google**에 **요청이 인증되지 않으면 수행할 동작**을 설정합니다. 이렇게 하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Google에 리디렉션되어야 합니다.

    > [!NOTE]
    > 이러한 방식으로 액세스를 제한 하는 것은 앱에 대 한 모든 호출에 적용 됩니다 .이는 여러 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지를 사용 하는 앱에는 바람직하지 않을 수 있습니다. 이러한 응용 프로그램의 경우 [여기](overview-authentication-authorization.md#authentication-flow)에 설명 된 대로 **익명 요청 허용 (작업 없음)** 을 사용 하는 것이 좋습니다. 앱은 수동으로 로그인을 시작 합니다.
    
5. **Save**을 클릭합니다.

이제 앱에서 Google을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

