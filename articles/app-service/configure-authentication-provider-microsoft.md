---
title: Microsoft 인증 구성
description: 앱 서비스 앱의 ID 공급자로 Microsoft 계정 인증을 구성하는 방법을 알아봅니다.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 95c603d4a10eb0e4d0817e20755c0f9b36baa96f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842336"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>Microsoft 계정 로그인을 사용하도록 앱 서비스 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 AAD를 사용하여 개인 Microsoft 계정 로그인을 지원하도록 Azure 앱 서비스를 구성하는 방법을 보여 주며 있습니다.

> [!NOTE]
> 개인 Microsoft 계정과 조직 계정 모두 AAD ID 공급자를 사용합니다. 지금은 두 유형의 로그인을 모두 지원하도록 이 ID 공급자를 구성할 수 없습니다.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Microsoft 계정을 사용하여 앱 등록

1. Azure 포털의 [**앱 등록으로 이동합니다.**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 필요한 경우 Microsoft 계정으로 로그인합니다.
1. **새 등록을**선택한 다음 응용 프로그램 이름을 입력합니다.
1. **지원되는 계정 유형에서** **조직 디렉터리(모든 Azure AD 디렉터리 - 다중 테넌트) 및 개인 Microsoft 계정(예: Skype, Xbox)에서 계정을 선택합니다.**
1. **리디렉션 URI에서** **웹을**선택한 다음 `https://<app-domain-name>/.auth/login/aad/callback`을 입력합니다. * \<앱 도메인 이름>* 앱의 도메인 이름으로 바꿉니다.  `https://contoso.azurewebsites.net/.auth/login/aad/callback`)을 입력합니다. URL에서 HTTPS 스키마를 사용해야 합니다.

1. **등록을**선택합니다.
1. 응용 **프로그램(클라이언트) ID를**복사합니다. 나중에 필요합니다.
1. 왼쪽 창에서 인증서 **& 비밀** > **새 클라이언트 비밀을**선택합니다. 설명을 입력하고 유효 기간을 선택한 다음 **에 추가를**선택합니다.
1. 인증서 & 비밀 페이지에 나타나는 값을 **복사합니다.** 페이지를 나가면 다시 표시되지 않습니다.

    > [!IMPORTANT]
    > 클라이언트 암호 값(암호)은 중요한 보안 자격 증명입니다. 다른 사람과 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>App Service 애플리케이션에 Microsoft 계정 정보 추가

1. [Azure 포털의]응용 프로그램으로 이동합니다.
1. **설정** > **인증/권한 부여를**선택하고 **앱 서비스 인증이** **켜졌는지**확인합니다.
1. **인증 공급자**에서 **Azure Active Directory**를 선택합니다. **관리 모드에서** **고급** 모드를 선택합니다. 이전에 가져온 응용 프로그램(클라이언트) ID 및 클라이언트 보안 에 붙여넣습니다. **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** **발급자 URL** 필드에 사용합니다.
1. **확인**을 선택합니다.

   앱 서비스는 인증을 제공하지만 사이트 콘텐츠 및 API에 대한 권한 있는 액세스를 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.

1. (선택 사항) Microsoft 계정 사용자에 대한 액세스를 제한하려면 Azure **Active Directory를 사용하여 로그인할** **때 요청이 인증되지 않은 경우 취할 작업을** 설정합니다. 이 기능을 설정하면 앱에서 모든 요청을 인증해야 합니다. 또한 인증에 AAD를 사용하도록 인증되지 않은 모든 요청을 리디렉션합니다. Microsoft 계정 테넌트를 사용하도록 **발급자 URL을** 구성했으므로 개인 acc카운트만 성공적으로 인증됩니다.

   > [!CAUTION]
   > 이러한 방식으로 액세스를 제한하는 것은 앱에 대한 모든 호출에 적용되며, 이는 많은 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지가 있는 앱에는 바람직하지 않을 수 있습니다. 이러한 응용 프로그램의 경우 앱이 수동으로 인증 자체를 시작하도록 **익명 요청 허용(작업 없음)이** 선호될 수 있습니다. 자세한 내용은 [인증 흐름을](overview-authentication-authorization.md#authentication-flow)참조하십시오.

1. **저장**을 선택합니다.

이제 앱에서 Microsoft 계정을 인증에 사용할 준비가 되었습니다.

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 포털]: https://portal.azure.com/
