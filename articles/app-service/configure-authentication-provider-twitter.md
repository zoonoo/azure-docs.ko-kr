---
title: Twitter 인증 구성
description: App Service 또는 Azure Functions 앱의 ID 공급자로 Twitter 인증을 구성하는 방법에 대해 알아봅니다.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6ecce954991d9f3901c54a6f87fc803b32469862
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077978"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Twitter 로그인을 사용하도록 App Service 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Twitter를 인증 공급자로 사용하도록 Azure App Service 또는 Azure Functions을 구성하는 방법을 보여 줍니다.

이 문서의 절차를 완료하려면 검증된 메일 주소 및 전화 번호가 포함된 Twitter 계정이 있어야 합니다. 새 Twitter 계정을 만들려면 [twitter.com]으로 이동합니다.

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Twitter를 사용하여 애플리케이션 등록

1. [Azure Portal]에 로그인하고 애플리케이션으로 이동합니다. **URL** 을 복사합니다. Twitter 앱을 구성하는 데 사용합니다.
1. [Twitter 개발자] 웹 사이트로 이동하고 Twitter 계정 자격 증명을 사용하여 로그인하고 **앱 만들기** 를 선택합니다.
1. 새 앱에 대해 **앱 이름** 및 **애플리케이션 설명** 을 입력합니다. 애플리케이션의 **URL** 을 **웹 사이트 URL** 필드에 붙여넣습니다. **콜백 URL** 섹션에서 App Service 앱의 HTTPS URL을 입력하고 경로 `/.auth/login/twitter/callback`을 추가합니다. 예: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. 페이지 맨 아래에 **이 앱의 사용 방법 알리기** 에 100자 이상을 입력하고 **만들기** 를 선택합니다. 팝업에서 **만들기** 를 다시 클릭합니다. 애플리케이션 세부 정보가 표시됩니다.
1. **Keys and Access Tokens** 탭을 선택합니다.

   다음 값을 기록해 둡니다.
   - API 키
   - API 비밀 키

   > [!IMPORTANT]
   > API 비밀 키는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마세요.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>애플리케이션에 Twitter 정보 추가

1. [Azure Portal]에 로그인하고 IoT Hub로 이동합니다.
1. 왼쪽 메뉴에서 **인증** 을 선택합니다. **ID 공급자 추가** 를 클릭합니다.
1. ID 공급자 드롭다운에서 **Twitter** 를 선택합니다. 앞에서 얻은 `API key` 및 `API secret key` 값을 붙여넣습니다.

    비밀은 `TWITTER_PROVIDER_AUTHENTICATION_SECRET`으로 명명된 슬롯 고정이란 [애플리케이션 설정](./configure-common.md#configure-app-settings)으로 저장됩니다. Azure Key Vault에서 비밀을 관리하려는 경우 나중에 [Key Vault 참조](./app-service-key-vault-references.md)를 사용하도록 해당 설정을 업데이트할 수 있습니다.

1. 이것이 애플리케이션에 대해 구성된 첫 번째 ID 공급자인 경우 **App Service 인증 설정** 섹션도 표시됩니다. 그렇지 않으면 다음 단계로 이동할 수 있습니다.
    
    이러한 옵션은 애플리케이션이 인증되지 않은 요청에 응답하는 방법을 결정하며 기본 선택 항목은 이 새 공급자를 사용하여 로그인하도록 모든 요청을 리디렉션합니다. 지금 이 동작의 사용자 지정을 변경하거나, 나중에 기본 **인증** 화면에서 **인증 설정** 옆의 **편집** 을 선택하여 이 설정을 조정할 수 있습니다. 이러한 옵션에 대한 자세한 정보는 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조하세요.

1. **추가** 를 클릭합니다.

이제 앱에서 Twitter를 인증에 사용할 준비가 되었습니다. 공급자는 **인증** 화면에 나열됩니다. 여기에서 공급자 구성을 편집하거나 삭제할 수 있습니다.

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Twitter 개발자]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
