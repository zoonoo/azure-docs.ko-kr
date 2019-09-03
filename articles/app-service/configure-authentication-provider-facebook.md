---
title: Facebook 인증 구성 - Azure App Service
description: App Service 앱에 대 한 Facebook 인증을 구성 하는 방법을 알아봅니다.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 410d769d0d9abe3a0a0f9c45e3cf67bb94ec9f4d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232068"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Facebook 로그인을 사용하도록 App Service 애플리케이션을 구성하는 방법
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Facebook을 인증 공급자로 사용하도록 Azure App Service를 구성하는 방법을 보여 줍니다.

이 토픽의 절차를 완료하려면 검증된 메일 주소와 휴대폰 번호가 포함된 Facebook 계정이 있어야 합니다. 새 Facebook 계정을 만들려면 [facebook.com]으로 이동하세요.

## <a name="register"></a>Facebook을 사용하여 애플리케이션 등록
1. [Facebook 개발자] 웹 사이트로 이동한 다음 Facebook 계정 자격 증명으로 로그인합니다.
3. 필드 개발자 용 Facebook 계정이 없는 경우 **시작**을 클릭 하 고 등록 단계를 따릅니다.
4. **내 앱** > **새 앱 추가**를 클릭 합니다.
5. **표시 이름**에 고유한 앱 이름을 입력합니다. 또 **연락처 이메일**을 입력한 다음, **앱 ID 만들기**를 클릭하고 보안 검사를 완료합니다. 새 Facebook 앱에 대 한 개발자 대시보드가 열립니다.
6. **대시보드**Facebook로그인 > 설정웹을 클릭 합니다. >  > 
1. **Facebook 로그인**아래의 왼쪽 탐색 영역에서 **설정**을 클릭 합니다.
1. **유효한 OAuth 리디렉션 uri**에서 `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`  *\<앱 이름 >* 를 Azure App Service 앱의 이름으로 입력 하 고 바꿉니다. 클릭 **변경 내용을 저장**합니다.
8. 왼쪽 탐색 창에서 **설정** > **기본**을 클릭합니다. **앱 비밀** 필드에서 **표시**를 클릭 합니다. **앱 ID** 및 **앱 암호**의 값을 복사 합니다. 나중에 Azure에서 App Service 앱을 구성 하는 데 사용 합니다.
   
   > [!IMPORTANT]
   > 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.
   > 
   > 
9. 응용 프로그램을 등록 하는 데 사용한 Facebook 계정은 앱의 관리자입니다. 지금은 관리자만 이 애플리케이션에 로그인할 수 있습니다. 다른 facebook 계정을 인증 하려면 **앱 검토** 를 클릭 하 고 **앱 \<이름 > 공개** 로 설정을 사용 하도록 설정 하 여 Facebook 인증을 사용 하는 일반 공용 액세스를 사용 하도록 설정 합니다.

## <a name="secrets"></a>애플리케이션에 Facebook 정보 추가
1. [Azure Portal] 에 로그인 하 여 App Service 앱으로 이동 합니다. **설정** > **인증 / 권한 부여**를 클릭하고 **App Service 인증**이 **켜기**인지 확인합니다.
2. **Facebook**을 클릭 하 고 이전에 가져온 앱 ID 및 앱 암호 값을 붙여넣은 다음 필요에 따라 응용 프로그램에 필요한 모든 범위를 사용 하도록 설정 하 고 **확인**을 클릭 합니다.
   
    ![][0]
   
    기본적으로 App Service는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.
3. (옵션) Facebook에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **Facebook**에 **요청이 인증되지 않으면 수행할 동작**을 설정합니다. 이렇게 하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Facebook에 리디렉션되어야 합니다.
 
> [!CAUTION]
> 이러한 방식으로 액세스를 제한 하는 것은 앱에 대 한 모든 호출에 적용 됩니다 .이는 여러 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지를 사용 하는 앱에는 바람직하지 않을 수 있습니다. 이러한 응용 프로그램의 경우 [여기](overview-authentication-authorization.md#authentication-flow)에 설명 된 대로 **익명 요청 허용 (작업 없음)** 을 사용 하는 것이 좋습니다. 앱은 수동으로 로그인을 시작 합니다.

4. 인증 구성이 완료되면 **저장**을 클릭합니다.

이제 앱에서 Facebook을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook 개발자]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
