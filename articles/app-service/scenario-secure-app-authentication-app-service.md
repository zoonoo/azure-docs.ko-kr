---
title: 자습서 - Azure App Service의 웹앱에 인증 추가 | Azure
description: 이 자습서에서는 Azure App Service에서 실행되는 웹앱에 인증 및 권한 부여를 사용하도록 설정하는 방법에 대해 알아봅니다.  웹앱에 액세스할 수 있는 사람을 조직 내 사용자로 제한합니다.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: a1b2809371a01b1c6fd822e5c4aaa197d0de3c1b
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428408"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>자습서: Azure App Service에서 실행되는 웹앱에 인증 추가

Azure App Service에서 실행되는 웹앱에 인증을 사용하도록 설정하고, 액세스 권한을 조직 내 사용자로 제한하는 방법을 알아봅니다.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="사용자 로그인" border="false":::

Azure App Service는 내장된 인증 및 권한 부여 지원을 제공하므로 웹앱에서 코드를 최소한으로 작성하거나 전혀 작성하지 않고도 사용자를 로그인하고 데이터에 액세스할 수 있습니다.  App Service 인증/권한 부여 모듈을 반드시 사용해야 하는 것은 아니지만 앱의 인증 및 권한 부여를 간소화하는 데 도움이 됩니다. 이 문서에서는 Azure Active Directory를 ID 공급자로 사용하여 App Service 인증/권한 부여 모듈로 웹앱을 보호하는 방법을 보여줍니다.

인증/권한 부여 모듈은 Azure Portal 및 앱 설정을 통해 사용하도록 설정하고 구성할 수 있습니다. SDK 및 특정 언어가 필요하거나 애플리케이션 코드를 변경할 필요가 없습니다. 다양한 ID 공급자가 지원되며 여기에는 Azure AD, Microsoft Account, Facebook, Google, Twitter 등이 포함됩니다. 인증/권한 부여 모듈을 사용하도록 설정하면 모든 수신 HTTP 요청이 이 모듈을 통과한 후 앱 코드에 의해 처리됩니다.  자세한 내용은 [Azure App Service에서 인증 및 권한 부여](overview-authentication-authorization.md)를 참조하세요.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
>
> * 웹앱에 대한 인증 구성
> * 웹앱에 액세스할 수 있는 사람을 조직 내 사용자로 제한

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>App Service에서 웹앱 만들기 및 게시

이 자습서에서는 Azure App Service에 배포된 웹앱이 필요합니다.  기존 웹앱을 사용할 수도 있고, [ASP.NET Core 빠른 시작](quickstart-dotnetcore.md)에 따라 새 웹앱을 만들고 App Service에 게시할 수도 있습니다.

기존 웹앱을 사용하든 새 웹앱을 만들든, 웹앱 이름과 웹앱이 배포되는 리소스 그룹의 이름을 기록해 두어야 합니다. 이 자습서 전체에서 이 이름이 필요합니다. 이 자습서 전체에서 프로시저 및 스크린샷의 예제 이름에는 *SecureWebApp* 이 포함되어 있습니다.

## <a name="configure-authentication-and-authorization"></a>인증 및 권한 부여 구성

이제 App Service에서 실행되는 웹앱이 생겼습니다.  다음으로 웹앱에 인증 및 권한 부여를 사용하도록 설정합니다. Azure Active Directory를 ID 공급자로 사용합니다. 자세한 내용은 [App Services 애플리케이션에 대해 Azure Active Directory 인증 구성](configure-authentication-provider-aad.md)을 참조하세요.

*Azure Portal* 메뉴에서 [리소스 그룹](https://portal.azure.com)을 선택하거나 검색하여 어느 페이지에서든 **리소스 그룹** 을 선택합니다.

**리소스 그룹** 에서 리소스 그룹을 찾아 선택합니다. **개요** 에서 앱의 관리 페이지를 선택합니다.

:::image type="content" alt-text="앱 서비스 선택" source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

앱의 왼쪽 메뉴에서 **인증/권한 부여** 을 선택한 다음, **켜기** 를 선택하여 App Service 인증을 사용하도록 설정합니다.

**요청이 인증되지 않은 경우 수행할 작업** 에서 **Azure Active Directory로 로그인** 을 선택합니다.

**인증 공급자** 에서 **Azure Active Directory** 를 선택합니다. **기본** 을 선택하고 기본 설정을 그대로 사용하여 새 AD 앱을 만든 후 **확인** 을 선택합니다.

:::image type="content" alt-text="기본 인증" source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

**인증/권한 부여** 페이지에서 **저장** 을 선택합니다.

`Successfully saved the Auth Settings for <app-name> App` 메시지가 포함된 알림이 표시되면 포털 페이지를 새로 고칩니다.

이제 App Service 인증 및 권한 부여를 통해 보호되는 앱이 생겼습니다.

## <a name="verify-limited-access-to-the-web-app"></a>액세스를 웹앱으로 제한

App Service 인증 및 권한 부여 모듈을 사용하도록 설정하면 Azure AD 테넌트에서 앱 등록이 생성됩니다.  앱 등록의 표시 이름은 웹앱과 동일합니다. 설정을 확인하려면 포털 메뉴에서 **Azure Active Directory** 를 선택하고 **앱 등록** 을 선택합니다.  이전에 생성된 앱 등록을 선택합니다.  [개요]에서 **지원되는 계정 유형** 이 **내 조직만** 으로 설정되어 있는지 확인합니다.

:::image type="content" alt-text="액세스 확인" source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

앱에 액세스할 수 있는 사람이 조직 내 사용자로 제한되는지 확인하려면 브라우저를 incognito 또는 프라이빗 모드로 시작하고 `https://<app-name>.azurewebsites.net`으로 이동합니다.  보안 로그인 페이지로 이동됩니다. 이 페이지에서 인증되지 않은 사용자가 사이트에 액세스할 수 없는 것을 확인할 수 있습니다.  사이트에 대한 액세스 권한을 얻으려면 조직 내 사용자로 로그인합니다.  또한 새 브라우저를 시작하고 개인 계정으로 로그인하면 조직 외부의 사용자가 액세스할 수 없는 것을 확인할 수 있습니다.  

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 마친 후 웹앱 또는 관련 리소스가 더 이상 필요하지 않은 경우 [이 자습서에서 만든 리소스를 정리](scenario-secure-app-clean-up-resources.md)합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> * 웹앱에 대한 인증 구성
> * 웹앱에 액세스할 수 있는 사람을 조직 내 사용자로 제한

> [!div class="nextstepaction"]
> [App Service에서 스토리지 액세스](scenario-secure-app-access-storage.md)
