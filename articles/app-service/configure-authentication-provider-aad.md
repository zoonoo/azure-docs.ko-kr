---
title: Azure AD 인증 구성
description: 앱 서비스 또는 Azure Functions 앱의 ID 공급자로 Azure Active Directory 인증을 구성하는 방법을 알아봅니다.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 4b42f0966288e4ee72b689ddce6313a41e91f13e
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438028"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Azure AD 로그인을 사용하도록 앱 서비스 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Azure App Service 또는 Azure 함수를 인증 공급자로 Azure Active Directory(Azure AD)를 사용하도록 구성하는 방법을 보여 주며 있습니다.

> [!NOTE]
> 현재 Azure [Active Directory v2.0(MSAL](../active-directory/develop/v2-overview.md) 포함)은 Azure 앱 서비스 및 Azure 함수에 대해 지원되지 않습니다. [MSAL](../active-directory/develop/msal-overview.md) 업데이트를 다시 확인하시기 바랍니다.
>

앱 및 인증을 설정할 때 다음 모범 사례를 따르십시오.

- 각 앱 서비스 앱에 자체 권한과 동의를 부여합니다.
- 자체 등록을 통해 각 앱 서비스 앱을 구성합니다.
- 별도의 배포 슬롯에 대해 별도의 앱 등록을 사용하여 환경 간에 권한 공유를 방지합니다. 새 코드를 테스트할 때 이 방법을 사용하면 프로덕션 앱에 영향을 미치는 문제를 방지할 수 있습니다.

## <a name="configure-with-express-settings"></a><a name="express"> </a>기본 설정을 사용하여 구성

> [!NOTE]
> 정부 클라우드에는 **익스프레스** 옵션을 사용할 수 없습니다. 

1. Azure [포털에서] **앱 서비스를**검색하고 선택한 다음 앱을 선택합니다.
2. 왼쪽 탐색에서 **인증/권한 부여** > **를 선택합니다.**
3. **Azure Active 디렉터리** > **익스프레스를**선택합니다.

   대신 기존 앱 등록을 선택하려면 다음을 수행하십시오.

   1. **기존 AD 앱 선택을**선택한 다음 Azure **AD 앱을**클릭합니다.
   2. 기존 앱 등록을 선택하고 **확인을**클릭합니다.

3. **확인**을 선택하여 Azure Active Directory에 App Service 앱을 등록합니다. 새 앱 등록이 만들어집니다.
   
    ![Azure Active 디렉터리에서 익스프레스 설정](./media/configure-authentication-provider-aad/express-settings.png)
   
4. (선택 사항) 기본적으로 App Service는 인증을 제공하지만 사이트 콘텐츠 및 API에 대한 권한 있는 액세스를 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다. Azure Active Directory에서 인증한 사용자에 대해서만 앱 액세스를 제한하려면 Azure **Active Directory를 사용하여 로그인하도록** **요청이 인증되지 않은 경우 취할 작업을** 설정합니다. 이 기능을 설정하면 앱에서 모든 요청을 인증해야 합니다. 또한 인증을 위해 인증되지 않은 모든 것을 Azure Active Directory로 리디렉션합니다.

    > [!CAUTION]
    > 이러한 방식으로 액세스를 제한하는 것은 앱에 대한 모든 호출에 적용되며, 이는 많은 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지가 있는 앱에는 바람직하지 않을 수 있습니다. 이러한 응용 프로그램의 경우 앱이 수동으로 로그인을 시작하는 경우 **익명 요청 허용(작업 없음)이** 선호될 수 있습니다. 자세한 내용은 [인증 흐름을](overview-authentication-authorization.md#authentication-flow)참조하십시오.
5. **저장**을 선택합니다.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>고급 설정을 사용하여 구성

다른 Azure AD 테넌트의 앱 등록을 사용하려는 경우 앱 설정을 수동으로 구성할 수 있습니다. 이 사용자 지정 구성을 완료하려면 다음을 수행하십시오.

1. Azure AD에서 등록을 만듭니다.
2. 앱 서비스에 등록 세부 정보 중 일부를 제공합니다.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>앱 서비스 앱에 대한 Azure AD에서 앱 등록 만들기

앱 서비스 앱을 구성할 때 다음 정보가 필요합니다.

- 클라이언트 ID
- 테넌트 ID
- 클라이언트 보안(선택 사항)
- 응용 프로그램 ID URI

다음 단계를 수행합니다.

1. [Azure 포털에]로그인하고 **앱 서비스를**검색하고 선택한 다음 앱을 선택합니다. 앱의 **URL을**확인합니다. Azure Active Directory 앱 등록을 구성하는 데 사용합니다.
1. **Azure Active Directory** > **앱 등록** > **새 등록을**선택합니다.
1. 응용 **프로그램 등록** 페이지에서 앱 등록의 **이름을** 입력합니다.
1. **URI 리디렉션에서** **웹을** 선택하고 `<app-url>/.auth/login/aad/callback`을 입력합니다. `https://contoso.azurewebsites.net/.auth/login/aad/callback`)을 입력합니다. 
1. **만들기**를 선택합니다.
1. 앱 등록을 만든 후 **나중에 응용 프로그램(클라이언트) ID와** **디렉터리(테넌트) ID를** 복사합니다.
1. **인증**을 선택합니다. **암시적 부여에서** **ID 토큰을** 사용하여 앱 서비스에서 OpenID Connect 사용자 로그인을 허용합니다.
1. (선택 사항) 브랜딩 을 **선택합니다.** **홈 페이지 URL에서**앱 서비스 앱의 URL을 입력하고 **저장을**선택합니다.
1. **API** > **집합**노출을 선택합니다. 앱 서비스 앱의 URL에 붙여넣은 다음 **저장을**선택합니다.

   > [!NOTE]
   > 이 값은 앱 등록의 **응용 프로그램 ID URI입니다.** 웹 앱에서 클라우드의 API에 액세스해야 하는 경우 클라우드 앱 서비스 리소스를 구성할 때 웹 앱의 **응용 프로그램 ID URI가** 필요합니다. 예를 들어 클라우드 서비스에서 웹 앱에 대한 액세스 권한을 명시적으로 부여하려는 경우 이 방법을 사용할 수 있습니다.

1. **범위 추가를**선택합니다.
   1. **범위 이름에서** *user_impersonation*을 입력합니다.
   1. 텍스트 상자에 동의 범위 이름과 설명을 입력하여 사용자가 동의 페이지에 표시하도록 합니다. 예를 들어 *내 앱 에 대한 액세스를 입력합니다.* 
   1. **범위 추가를**선택합니다.
1. (선택 사항) 클라이언트 비밀을 만들려면 인증서 & 비밀 > **새 클라이언트 암호** > **추가**를 **선택합니다.** 페이지에 표시된 클라이언트 비밀 값을 복사합니다. 다시 표시되지 않습니다.
1. (선택 사항) 여러 **회신 URL을**추가하려면 **인증을**선택합니다.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>앱 서비스 앱에서 Azure Active 디렉터리 사용

1. Azure [포털에서] **앱 서비스를**검색하고 선택한 다음 앱을 선택합니다. 
1. 왼쪽 창에서 **설정**에서 **인증 / 권한 부여를** > **선택합니다.**
1. (선택 사항) 기본적으로 앱 서비스 인증을 사용하면 앱에 대한 인증되지 않은 액세스가 허용됩니다. 사용자 인증을 적용하려면 Azure **Active Directory를 사용하여 로그인할** **때 요청이 인증되지 않은 경우 취할 작업을** 설정합니다.
1. **인증 공급자**에서 **Azure Active Directory**를 선택합니다.
1. **관리 모드에서** **고급** 을 선택하고 다음 표에 따라 앱 서비스 인증을 구성합니다.

    |필드|Description|
    |-|-|
    |클라이언트 ID| 앱 등록의 **응용 프로그램(클라이언트) ID를** 사용합니다. |
    |발급자 URL| 을 `https://login.microsoftonline.com/<tenant-id>`사용하고 * \<테넌트 id>* 앱 등록의 **디렉터리(테넌트) ID로** 바꿉니다. 이 값은 사용자를 올바른 Azure AD 테넌트로 리디렉션하고 적절한 메타데이터를 다운로드하여 적절한 토큰 서명 키 및 토큰 발급자 클레임 값을 결정하는 데 사용됩니다. |
    |클라이언트 보안(선택 사항)| 앱 등록에서 생성한 클라이언트 비밀을 사용합니다.|
    |허용된 토큰 잠재고객| 클라우드 또는 서버 앱이고 웹 앱에서 인증 토큰을 허용하려는 경우 여기에 웹 앱의 **응용 프로그램 ID URI를** 추가합니다. 구성된 **클라이언트 ID는** *항상* 암시적으로 허용된 대상으로 간주됩니다. |

2. **확인**을 선택하고 **저장**을 선택합니다.

이제 앱 서비스 앱에서 인증에 Azure Active Directory를 사용할 준비가 되었습니다.

## <a name="configure-a-native-client-application"></a>네이티브 클라이언트 애플리케이션 구성

네이티브 클라이언트를 등록하여 **Active Directory 인증 라이브러리와**같은 클라이언트 라이브러리를 사용하여 앱에서 호스팅되는 Web API에 대한 인증을 허용할 수 있습니다.

1. Azure [포털에서] **Active Directory** > 앱 등록**새 등록을****선택합니다.** > 
1. 응용 **프로그램 등록** 페이지에서 앱 등록의 **이름을** 입력합니다.
1. **URI 리디렉션에서** **공용 클라이언트(모바일 & 데스크톱)를** `<app-url>/.auth/login/aad/callback`선택하고 URL을 입력합니다. `https://contoso.azurewebsites.net/.auth/login/aad/callback`)을 입력합니다.

    > [!NOTE]
    > Microsoft Store 응용 프로그램의 경우 [패키지 SID를](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) URI로 대신 사용합니다.
1. **만들기**를 선택합니다.
1. 앱 등록을 만든 후 응용 **프로그램(클라이언트) ID**값을 복사합니다.
1. **API 권한** > 선택**권한 권한** > **내 API**추가 .
1. 앱 서비스 앱에 대해 이전에 만든 앱 등록을 선택합니다. 앱 등록이 표시되지 않으면 [앱 서비스 앱에 대한 Azure AD에서 앱 등록 만들기에서](#register) **user_impersonation** 범위를 추가했는지 확인합니다.
1. **user_impersonation**을 선택한 다음 **권한 추가를 선택합니다.**

이제 사용자를 대신하여 앱 서비스 앱에 액세스할 수 있는 네이티브 클라이언트 응용 프로그램을 구성했습니다.

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
