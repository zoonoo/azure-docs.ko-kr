---
title: Facebook 인증 구성
description: App Service 또는 Azure Functions 앱의 ID 공급자로 Facebook 인증을 구성하는 방법에 대해 알아봅니다.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: bc639eaea76b3309d6ed047e73c726040da19639
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078012"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Facebook 로그인을 사용하도록 App Service 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Facebook을 인증 공급자로 사용하도록 Azure App Service 또는 Azure Functions을 구성하는 방법을 보여 줍니다.

이 문서의 절차를 완료하려면 검증된 메일 주소와 휴대폰 번호가 포함된 Facebook 계정이 있어야 합니다. 새 Facebook 계정을 만들려면 [facebook.com]으로 이동하세요.

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Facebook을 사용하여 애플리케이션 등록

1. [Facebook 개발자] 웹 사이트로 이동한 다음, Facebook 계정 자격 증명으로 로그인합니다.

   개발자용 Facebook 계정이 없는 경우 **시작** 을 선택하고 등록 단계를 따릅니다.
1. **내 앱** > **새 앱 추가** 를 선택합니다.
1. **표시 이름** 필드에서 다음을 수행합니다.
   1. 앱의 고유한 이름을 입력합니다.
   1. **연락처 메일** 을 제공합니다.
   1. **앱 ID 만들기** 를 선택합니다.
   1. 보안 검사를 완료합니다.

   새 Facebook 앱에 대한 개발자 대시보드가 열립니다.
1. **대시보드** > **Facebook 로그인** > **설정** > **웹** 을 선택합니다.
1. **Facebook 로그인** 아래의 왼쪽 탐색 영역에서 **설정** 을 선택합니다.
1. **유효한 OAuth 리디렉션 URI** 필드에 `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`을 입력합니다. `<app-name>`을 Azure App Service 앱의 이름으로 바꾸어야 합니다.
1. **변경 내용 저장** 을 선택합니다.
1. 왼쪽 창에서 **설정** > **기본** 을 선택합니다. 
1. **앱 비밀** 필드에서 **표시** 를 선택합니다. **앱 ID** 및 **앱 비밀** 의 값을 복사합니다. 나중에 Azure에서 App Service 앱을 구성하는 데 이러한 항목을 사용합니다.

   > [!IMPORTANT]
   > 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.
   >

1. 애플리케이션을 등록하는 데 사용된 Facebook 계정이 앱의 관리자입니다. 지금은 관리자만 이 애플리케이션에 로그인할 수 있습니다.

   다른 Facebook 계정을 인증하려면 **앱 검토** 를 선택하고 **\<your-app-name> 퍼블릭 설정** 을 사용하도록 지정하여 Facebook 인증을 사용하여 앱에 대한 일반 퍼블릭 액세스를 사용하도록 설정합니다.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>애플리케이션에 Facebook 정보 추가

1. [Azure Portal]에 로그인하고 IoT Hub로 이동합니다.
1. 왼쪽 메뉴에서 **인증** 을 선택합니다. **ID 공급자 추가** 를 클릭합니다.
1. ID 공급자 드롭다운에서 **Facebook** 을 선택합니다. 앞에서 얻은 앱 ID 및 앱 비밀 값을 붙여 넣습니다.

    비밀은 `FACEBOOK_PROVIDER_AUTHENTICATION_SECRET`으로 명명된 슬롯 고정이란 [애플리케이션 설정](./configure-common.md#configure-app-settings)으로 저장됩니다. Azure Key Vault에서 비밀을 관리하려는 경우 나중에 [Key Vault 참조](./app-service-key-vault-references.md)를 사용하도록 해당 설정을 업데이트할 수 있습니다.

1. 이것이 애플리케이션에 대해 구성된 첫 번째 ID 공급자인 경우 **App Service 인증 설정** 섹션도 표시됩니다. 그렇지 않으면 다음 단계로 이동할 수 있습니다.
    
    이러한 옵션은 애플리케이션이 인증되지 않은 요청에 응답하는 방법을 결정하며 기본 선택 항목은 이 새 공급자를 사용하여 로그인하도록 모든 요청을 리디렉션합니다. 지금 이 동작의 사용자 지정을 변경하거나, 나중에 기본 **인증** 화면에서 **인증 설정** 옆의 **편집** 을 선택하여 이 설정을 조정할 수 있습니다. 이러한 옵션에 대한 자세한 정보는 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조하세요.

1. (선택 사항) **다음: 범위** 를 클릭하고 애플리케이션에 필요한 모든 범위를 추가합니다. 해당 요청은 브라우저 기반 흐름의 로그인 시 요청됩니다.
1. **추가** 를 클릭합니다.

이제 앱에서 Facebook을 인증에 사용할 준비가 되었습니다. 공급자는 **인증** 화면에 나열됩니다. 여기에서 공급자 구성을 편집하거나 삭제할 수 있습니다.

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->
[Facebook 개발자]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
