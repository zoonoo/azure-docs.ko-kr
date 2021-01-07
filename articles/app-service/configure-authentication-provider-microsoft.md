---
title: Microsoft 인증 구성
description: App Service 또는 Azure Functions 앱의 ID 공급자로 Microsoft 계정 인증을 구성하는 방법에 대해 알아봅니다.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 9ec9c102680496407106a3bf9b7683890c7a63ee
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043245"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Microsoft 계정 로그인을 사용하도록 App Service 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Azure App Service 또는 Azure Functions를 구성하여 AAD를 사용하고 개인 Microsoft 계정 로그인을 지원하는 방법을 보여줍니다.

> [!NOTE]
> 개인 Microsoft 계정과 조직 계정 모두 AAD ID 공급자를 사용합니다. 현재 이 ID 공급자를 구성하여 두 가지 유형의 로그인을 모두 지원할 수는 없습니다.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Microsoft 계정을 사용하여 앱 등록

1. Azure Portal의 [**앱 등록**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)으로 이동합니다. 필요한 경우 Microsoft 계정으로 로그인합니다.
1. **새 등록** 을 선택하고 애플리케이션 이름을 입력합니다.
1. **지원되는 계정 유형** 에서 **모든 조직 디렉터리의 계정(모든 Azure AD 디렉터리 - 다중 테넌트) 및 개인 Microsoft 계정(예: Skype, Xbox)** 을 선택합니다.
1. **리디렉션 URI** 에서 **웹** 을 선택한 다음, `https://<app-domain-name>/.auth/login/aad/callback`를 입력합니다. 를 *\<app-domain-name>* 앱의 도메인 이름으로 바꿉니다.  `https://contoso.azurewebsites.net/.auth/login/aad/callback`)을 입력합니다. URL에서 HTTPS 체계를 사용해야 합니다.

1. **등록** 을 선택합니다.
1. **애플리케이션(클라이언트) ID** 를 복사합니다. 나중에 필요합니다.
1. 왼쪽 창에서 **인증서 및 암호** > **새 클라이언트 암호** 를 선택합니다. 설명을 입력하고 유효 기간을 선택한 다음 **추가** 를 선택합니다.
1. **인증서 및 암호** 페이지에 표시되는 값을 복사합니다. 페이지를 나간 후에는 값이 다시 표시되지 않습니다.

    > [!IMPORTANT]
    > 클라이언트 암호 값(암호)는 중요한 보안 자격 증명입니다. 다른 사람과 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"></a>App Service 애플리케이션에 Microsoft 계정 정보 추가

1. [Azure Portal]에서 애플리케이션으로 이동합니다.
1. **설정** > **인증 / 권한 부여** 를 선택하고 **App Service 인증** 이 **켜기** 인지 확인합니다.
1. **인증 공급자** 에서 **Azure Active Directory** 를 선택합니다. **관리 모드** 에서 **고급** 을 선택합니다. 이전에 얻은 애플리케이션(클라이언트) ID와 클라이언트 암호를 붙여넣습니다. **발급자 URL** 필드에 **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** 을 사용합니다.
1. **확인** 을 선택합니다.

   App Service는 인증을 제공하지만, 사이트 콘텐츠 및 API에 대한 권한 있는 액세스를 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.

1. (선택 사항) Microsoft 계정 사용자만 액세스하도록 제한하려면 **요청이 인증되지 않으면 수행할 동작** 을 **Azure Active Directory로 로그인** 으로 설정합니다. 이 기능을 설정하면 앱에서 모든 요청을 인증해야 합니다. 또한 인증되지 않은 모든 요청을 리디렉션하여 인증에 AAD를 사용합니다. Microsoft 계정 테 넌 트를 사용 하도록 **발급자 Url** 을 구성 했으므로 개인 계정만 성공적으로 인증 됩니다.

   > [!CAUTION]
   > 이러한 방식으로 액세스를 제한하면 모든 앱 호출에 제한이 적용되며, 여러 단일 페이지 애플리케이션이 그렇듯이 공개적으로 사용 가능한 홈페이지가 있는 앱에는 이 방법이 바람직하지 않을 수 있습니다. 이러한 애플리케이션에서는 **익명 요청 허용(작업 없음)** 으로 설정하고, 앱에서 수동으로 인증을 시작하는 것이 더 좋은 방법일 수 있습니다. 자세한 내용은 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조하세요.

1. **저장** 을 선택합니다.

이제 앱에서 Microsoft 계정을 인증에 사용할 준비가 되었습니다.

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
