---
title: '자습서: 데스크톱 애플리케이션에서 Node.js 웹 API에 대한 액세스 권한 부여'
description: Active Directory B2C를 사용하여 Node.js 웹 API를 보호하고 .NET 데스크톱 앱에서 호출하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8ebfbeeb4533f21bc0fa10a5fee7b88ef069c262
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298853"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 데스크톱 앱에서 Node.js Web API에 대한 액세스 권한 부여

이 자습서에서는 Azure AD B2C(Azure Active Directory B2C)로 보호되는 WPF(Windows Presentation Foundation)에서 Azure AD B2C로 보호되는 Node.js Web API 리소스를 호출하는 방법을 보여 줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 웹 API 애플리케이션 추가
> * 웹 API에 대한 범위 구성
> * 웹 API에 사용 권한 부여
> * 애플리케이션을 사용하도록 샘플 업데이트

## <a name="prerequisites"></a>필수 구성 요소

[자습서: 네이티브 데스크톱 클라이언트에서 사용자 인증](tutorial-desktop-app.md)의 단계와 필수 조건을 완료합니다.

## <a name="add-a-web-api-application"></a>웹 API 애플리케이션 추가

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>범위 구성

범위는 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 일부 사용자는 읽기 및 쓰기 권한을 모두 가질 수 있지만, 다른 사용자는 읽기 전용 권한을 가질 수 있습니다. 이 자습서에서는 웹 API에 대한 읽기 및 쓰기 권한을 정의합니다.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

나중의 단계에서 데스크톱 애플리케이션을 구성할 때 사용할 `demo.read` 범위에 대해 **SCOPES** 아래의 값을 기록합니다. 전체 범위 값은 `https://contosob2c.onmicrosoft.com/api/demo.read`와 비슷합니다.

## <a name="grant-permissions"></a>권한 부여

네이티브 클라이언트 애플리케이션에서 보호된 웹 API를 호출하려면 등록된 네이티브 클라이언트 애플리케이션에 Azure AD B2C에 등록된 웹 API에 대한 권한을 부여해야 합니다.

필수 조건 자습서에서 *nativeapp1*이라는 네이티브 클라이언트 애플리케이션을 등록했습니다. 다음 단계에서는 이전 섹션에서 *webapi1*을 위해 노출한 API 범위를 사용하여 이 네이티브 애플리케이션 등록을 구성합니다. 이렇게 하면 데스크톱 애플리케이션이 Azure AD B2C에서 액세스 토큰을 가져올 수 있으며, 웹 API는 이 액세스 토큰을 사용하여 Azure AD B2C 리소스에 대한 범위가 지정된 액세스를 확인하고 제공할 수 있습니다. 자습서의 뒷부분에서 데스크톱 애플리케이션과 웹 API의 코드 샘플을 모두 구성하고 실행합니다.

Azure AD B2C 테넌트에 애플리케이션을 등록하려면 새로운 통합 **앱 등록** 환경 또는 레거시 **애플리케이션(레거시)** 환경을 사용하면 됩니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. **앱 등록**을 선택한 다음, API에 대한 액세스 권한이 있어야 하는 네이티브 클라이언트 애플리케이션을 선택합니다. 예를 들어 *nativeapp1*과 같습니다.
1. **관리** 아래에서 **API 권한**을 선택합니다.
1. **구성된 사용 권한** 아래에서 **권한 추가**를 선택합니다.
1. **내 API** 탭을 선택합니다.
1. 네이티브 클라이언트 애플리케이션에 액세스 권한을 부여할 API를 선택합니다. 예를 들어 *webapi1*과 같습니다.
1. **권한** 아래에서 **데모**를 확장한 다음, 앞에서 정의한 범위를 선택합니다. *demo.read* 및 *demo.write*가 예입니다.
1. **권한 추가**를 선택합니다. 안내에 따라 몇 분 정도 기다린 후 다음 단계를 진행하세요.
1. **(테넌트 이름)에 대한 관리자 동의 허용**을 선택합니다.
1. 현재 로그인된 관리자 계정을 선택하거나 Azure AD B2C 테넌트에서 최소한 *클라우드 애플리케이션 관리자* 역할이 할당된 계정으로 로그인합니다.
1. **수락**을 선택합니다.
1. **새로 고침**을 선택한 다음, 두 범위 모두 **상태** 아래에 "...에 대해 허용됨"이 표시되는지 확인합니다. 권한이 전파되려면 몇 분 정도 걸릴 수 있습니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. **애플리케이션(레거시)** 을 선택한 다음, *nativeapp1*을 선택합니다.
1. **API 액세스**를 선택한 다음, **추가**를 선택합니다.
1. **API 선택** 드롭다운에서 *webapi1*을 선택합니다.
1. **범위 선택** 드롭다운에서 이전에 정의한 범위를 선택합니다. *demo.read* 및 *demo.write*가 예입니다.
1. **확인**을 선택합니다.

* * *

사용자는 Azure AD B2C로 인증하여 WPF 데스크톱 애플리케이션을 사용합니다. 데스크톱 애플리케이션은 Azure AD B2C에서 권한 부여를 받아 보호되는 웹 API에 액세스합니다.

## <a name="configure-the-samples"></a>샘플 구성

웹 API가 등록되었고 범위 및 권한을 구성했으므로 이제 Azure AD B2C 테넌트를 사용하는 데스크톱 애플리케이션 및 웹 API 샘플을 구성합니다.

### <a name="update-the-desktop-application"></a>데스크톱 애플리케이션 업데이트

이 문서의 필수 조건에서 [WPF 데스크톱 애플리케이션](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)를 수정하여 Azure AD B2C 테넌트의 사용자 흐름을 사용하여 로그인할 수 있도록 설정했습니다. 이 섹션에서는 앞서 등록한 웹 API를 참조하도록 동일한 애플리케이션 *webapi1*을 업데이트합니다.

1. Visual Studio에서 **active-directory-b2c-wpf** 솔루션(`active-directory-b2c-wpf.sln`)을 엽니다.
1. **active-directory-b2c-wpf** 프로젝트에서 *App.xaml.cs* 파일을 열고 다음 변수 정의를 찾습니다.
    1. `ApiScopes` 변수의 값을 앞서 **demo.read** 범위를 정의할 때 기록한 값으로 바꿉니다.
    1. `ApiEndpoint` 변수의 값을 앞서 테넌트에 웹 API(예: *webapi1*)를 등록할 때 기록한 **리디렉션 URI**로 바꿉니다.

    예를 들면 다음과 같습니다.

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Node.js API 샘플 가져오기 및 업데이트

다음으로, GitHub에서 Node.js 웹 API 코드 샘플을 가져와 Azure AD B2C 테넌트에 등록한 웹 API를 사용하도록 구성합니다.

GitHub에서 [Zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)하거나 샘플 웹앱을 복제합니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Node.js Web API 샘플에서는 Passport.js 라이브러리를 사용하여 Azure AD B2C가 API 호출을 보호하도록 설정합니다.

1. `index.js` 파일을 엽니다.
1. 다음 값을 사용하여 이러한 변수 정의를 업데이트합니다. `<web-API-application-ID>`를 앞서 등록한 웹 API(*webapi1*)의 **애플리케이션(클라이언트) ID**로 변경합니다. `<your-b2c-tenant>`를 Azure AD B2C 테넌트의 이름으로 변경합니다.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. API를 로컬에서 실행하고 있으므로 update the path in the route for the GET 메서드의 라우팅에서 경로를 데모 앱의 `/hello` 위치 대신 `/`로 업데이트합니다.

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>샘플 실행

### <a name="run-the-nodejs-web-api"></a>Node.js Web API 실행

1. Node.js 명령 프롬프트를 시작합니다.
2. Node.js 샘플이 포함된 디렉터리로 변경합니다. 예: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. 다음 명령을 실행합니다.
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>데스크톱 애플리케이션 실행

1. Visual Studio에서 **active-directory-b2c-wpf** 솔루션을 엽니다.
2. **F5** 키를 눌러 데스크톱 앱을 실행합니다.
3. [데스크톱 앱에서 Azure Active Directory B2C를 사용하여 사용자 인증 자습서](tutorial-desktop-app.md)에서 사용한 이메일 주소와 암호를 사용하여 로그인합니다.
4. **API 호출** 단추를 선택합니다.

데스크톱 애플리케이션은 로컬에서 실행되는 웹 API에 대해 요청을 수행하고 유효한 액세스 토큰이 확인되면 로그인한 사용자의 표시 이름을 보여 줍니다.

![WPF 데스크톱 애플리케이션의 위쪽 창에 표시된 표시 이름](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Azure AD B2C에 의해 보호되는 데스크톱 애플리케이션은 역시 Azure AD B2C에 의해 보호되며 로컬에서 실행되는 웹 API를 호출합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 웹 API 애플리케이션 추가
> * 웹 API에 대한 범위 구성
> * 웹 API에 사용 권한 부여
> * 애플리케이션을 사용하도록 샘플 업데이트

> [!div class="nextstepaction"]
> [자습서: Azure Active Directory B2C의 애플리케이션에 ID 공급자 추가](tutorial-add-identity-providers.md)
