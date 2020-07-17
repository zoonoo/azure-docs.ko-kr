---
title: '자습서: 네이티브 클라이언트 애플리케이션에서 사용자 인증'
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C를 사용하여 .NET 데스크톱 애플리케이션에 대한 사용자 로그인을 제공하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78186202"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 네이티브 데스크톱 클라이언트에서 사용자 인증

이 자습서에서는 Azure AD B2C(Azure Active Directory B2C)를 사용하여 WPF(Windows Presentation Foundation) 데스크톱 애플리케이션에서 사용자를 로그인하고 등록하는 방법을 보여줍니다. Azure AD B2C를 사용하면 애플리케이션이 개방형 표준 프로토콜을 사용하여 소셜 계정, 엔터프라이즈 계정 및 Azure Active Directory 계정을 인증할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 원시 클라이언트 애플리케이션 추가
> * 애플리케이션을 사용하도록 샘플 구성
> * 사용자 흐름을 사용하여 등록

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

- [사용자 흐름을 생성](tutorial-create-user-flows.md)하여 애플리케이션에 사용자 환경을 사용하도록 설정합니다.
- **.NET 데스크톱 개발**과 **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019](https://www.visualstudio.com/downloads/)를 설치합니다.

## <a name="add-the-native-client-application"></a>원시 클라이언트 애플리케이션 추가

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.

## <a name="configure-the-sample"></a>샘플 구성

이 자습서에서는 GitHub에서 다운로드할 수 있는 샘플을 구성합니다. 샘플 WPF 데스크톱 애플리케이션은 가입, 로그인을 보여주며 Azure AD B2C에서 보호되는 웹 API를 호출할 수 있습니다. GitHub에서 [zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip)하고, [리포지토리를 찾거나](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) 샘플을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Azure AD B2C 테넌트와 함께 작동하도록 애플리케이션을 업데이트하고 기본 데모 테넌트의 사용자 흐름 대신 해당 사용자 흐름을 호출하려면

1. Visual Studio에서 **active-directory-b2c-wpf** 솔루션(`active-directory-b2c-wpf.sln`)을 엽니다.
2. **active-directory-b2c-wpf** 프로젝트에서 *App.xaml.cs* 파일을 열고 다음 변수 정의를 찾습니다. `{your-tenant-name}`을 Azure AD B2C 테넌트 이름으로 바꾸고 `{application-ID}`를 이전에 기록한 애플리케이션 ID로 바꿉니다.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. 필수 조건의 일부로 만든 사용자 흐름의 이름으로 정책 이름 변수를 업데이트합니다. 다음은 그 예입니다.

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>샘플 실행

**F5** 키를 눌러 샘플을 빌드 및 실행합니다.

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

1. **로그인**을 선택하여 사용자로 가입합니다. 여기서는 **B2C_1_signupsignin1** 사용자 흐름을 사용합니다.
2. Azure AD B2C에서 **지금 가입** 링크가 있는 로그인 페이지를 제공합니다. 아직 계정이 없으므로 **지금 가입** 링크를 선택합니다.
3. 등록 워크플로에서 이메일 주소를 사용하여 사용자의 ID를 수집하고 확인하는 페이지를 제공합니다. 또한 가입 워크플로에서도 사용자 흐름에 정의된 사용자의 암호와 요청된 특성을 수집합니다.

    유효한 이메일 주소를 사용하고 확인 코드를 사용하여 유효성을 검사합니다. 암호를 설정합니다. 요청된 특성에 대한 값을 입력합니다.

    ![로그인/등록 워크플로의 일부로 표시되는 등록 페이지](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. **만들기**를 선택하여 로컬 계정을 Azure AD B2C 테넌트에 만듭니다.

이제 사용자는 이메일 주소를 사용하여 로그인하고 데스크톱 애플리케이션을 사용할 수 있습니다. 성공적으로 가입 또는 로그인한 후에는 토큰 정보가 WPF 앱의 아래쪽 창에 표시됩니다.

![WPF 데스크톱 애플리케이션의 아래쪽 창에 표시되는 토큰 정보](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

**API 호출** 단추를 선택하는 경우 **오류 메시지**가 표시됩니다. 현재 상태에서 애플리케이션이 데모 테넌트인 `fabrikamb2c.onmicrosoft.com`에 의해 보호되는 API에 액세스하려고 하기 때문에 오류가 발생합니다. 액세스 토큰이 Azure AD B2C 테넌트에만 유효하므로 API 호출에 권한이 부여되지 않습니다.

사용자의 테넌트에 보호되는 웹 API를 등록하고 **API 호출** 기능을 사용하도록 설정하려면 다음 자습서로 계속 진행합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 원시 클라이언트 애플리케이션 추가
> * 애플리케이션을 사용하도록 샘플 구성
> * 사용자 흐름을 사용하여 등록

다음으로 **API 호출** 단추 기능을 사용하도록 설정하려면 WPF 데스크톱 애플리케이션에 사용자의 Azure AD B2C 테넌트에 등록된 웹 API에 대한 액세스 권한을 부여합니다.

> [!div class="nextstepaction"]
> [자습서: 데스크톱 앱에서 Node.js 웹 API에 대한 액세스 권한 부여 >](tutorial-desktop-app-webapi.md)
