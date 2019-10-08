---
title: 자습서 - 원시 클라이언트 애플리케이션에서 인증을 사용하도록 설정 - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 .NET 데스크톱 애플리케이션에 대한 사용자 로그인을 제공하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3740a032db6ca9fd0fb88ce348610684d9f895bc
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326323"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 원시 클라이언트 애플리케이션에서 인증을 사용하도록 설정

이 자습서에서는 Azure AD B2C(Azure Active Directory B2C)를 사용하여 WPF(Windows Presentation Foundation) 데스크톱 애플리케이션에서 사용자를 로그인하고 등록하는 방법을 보여줍니다. Azure AD B2C를 사용하면 애플리케이션이 개방형 표준 프로토콜을 사용하여 소셜 계정, 엔터프라이즈 계정 및 Azure Active Directory 계정을 인증할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 원시 클라이언트 애플리케이션 추가
> * 애플리케이션을 사용하도록 샘플 구성
> * 사용자 흐름을 사용하여 등록

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

- [사용자 흐름을 생성](tutorial-create-user-flows.md)하여 애플리케이션에 사용자 환경을 사용하도록 설정합니다.
- **.NET 데스크톱 개발**과 **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019](https://www.visualstudio.com/downloads/)를 설치합니다.

## <a name="add-the-native-client-application"></a>원시 클라이언트 애플리케이션 추가

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

이후 단계에서 사용할 **애플리케이션 ID**를 기록합니다.

## <a name="configure-the-sample"></a>샘플 구성

이 자습서에서는 GitHub에서 다운로드할 수 있는 샘플을 구성합니다. 샘플 WPF 데스크톱 애플리케이션은 가입, 로그인을 보여주며 Azure AD B2C에서 보호되는 웹 API를 호출합니다. GitHub에서 [zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip)하고, [리포지토리를 찾거나](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) 샘플을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

앱 설정을 변경하려면 `<your-tenant-name>`을 테넌트 이름으로 바꾸고 `<application-ID`>를 기록해 둔 애플리케이션 ID로 바꿉니다.

1. Visual Studio에서 `active-directory-b2c-wpf` 솔루션을 엽니다.
2. `active-directory-b2c-wpf` 프로젝트에서 **App.xaml.cs** 파일을 열고 다음과 같이 업데이트합니다.

    ```csharp
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. **PolicySignUpSignIn** 변수를 생성한 사용자 흐름의 이름으로 업데이트합니다.

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>샘플 실행

**F5** 키를 눌러 샘플을 빌드 및 실행합니다.

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

1. **로그인**을 클릭하여 사용자로 등록합니다. 여기서는 **B2C_1_signupsignin1** 사용자 흐름을 사용합니다.
2. Azure AD B2C에서 등록 링크가 있는 로그인 페이지를 제공합니다. 아직 계정이 없으므로 **지금 등록** 링크를 클릭합니다.
3. 등록 워크플로에서 이메일 주소를 사용하여 사용자의 ID를 수집하고 확인하는 페이지를 제공합니다. 또한 가입 워크플로에서도 사용자 흐름에 정의된 사용자의 암호와 요청된 특성을 수집합니다.

    유효한 이메일 주소를 사용하고 확인 코드를 사용하여 유효성을 검사합니다. 암호를 설정합니다. 요청된 특성에 대한 값을 입력합니다.

    ![로그인/등록 워크플로의 일부로 표시되는 등록 페이지](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

4. **만들기**를 클릭하여 로컬 계정을 Azure AD B2C 테넌트에 만듭니다.

이제 사용자는 이메일 주소를 사용하여 로그인하고 데스크톱 앱을 사용할 수 있습니다.

> [!NOTE]
> **API 호출** 단추를 클릭하면 "권한 없음" 오류가 표시됩니다. 데모 테넌트에서 리소스에 액세스하려고 시도하기 때문에 이 오류가 표시되는 것입니다. 액세스 토큰이 Azure AD 테넌트에만 유효하므로 API 호출에 권한이 부여되지 않습니다. 그 다음 자습서를 계속 진행하여 테넌트의 보호되는 웹 API 키를 만드세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 원시 클라이언트 애플리케이션 추가
> * 애플리케이션을 사용하도록 샘플 구성
> * 사용자 흐름을 사용하여 등록

> [!div class="nextstepaction"]
> [자습서: Azure Active Directory B2C를 사용하여 데스크톱 앱에서 Node.js Web API에 대한 액세스 권한 부여](active-directory-b2c-tutorials-spa-webapi.md)
