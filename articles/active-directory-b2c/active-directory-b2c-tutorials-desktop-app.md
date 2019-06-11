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
ms.openlocfilehash: 474aad3b4b2019c9f79f8f1316dd88769e0fabbd
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508212"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 원시 클라이언트 애플리케이션에서 인증을 사용하도록 설정

이 자습서에서는 Azure AD(Azure Active Directory) B2C를 사용하여 WPF(Windows Presentation Foundation) 데스크톱 애플리케이션에서 사용자를 로그인하고 등록하는 방법을 보여줍니다. Azure AD B2C를 사용하면 애플리케이션이 개방형 표준 프로토콜을 사용하여 소셜 계정, 엔터프라이즈 계정 및 Azure Active Directory 계정을 인증할 수 있습니다.

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

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
5. 애플리케이션의 이름을 입력합니다. 예를 들어 *nativeapp1*과 같습니다.
6. **웹앱/웹 API 포함**에 **아니요**를 선택합니다.
7. **원시 클라이언트 포함**에 **예**를 선택합니다.
8. **리디렉션 URI**에 사용자 지정 체계를 사용하는 유효한 리디렉션 URI를 입력합니다. 리디렉션 URI를 선택하는 경우 다음과 같은 두 가지 중요한 고려 사항이 있습니다.

    - **고유** - 리디렉션 URI의 체계는 모든 애플리케이션에 대해 고유해야 합니다. 보기 `com.onmicrosoft.contoso.appname://redirect/path`에서 `com.onmicrosoft.contoso.appname`은 구성표입니다. 이 패턴을 따라야 합니다. 두 애플리케이션이 동일한 체계를 공유하는 경우 애플리케이션을 선택하기 위한 옵션이 제공됩니다. 사용자가 잘못된 항목을 선택하면 로그인이 실패합니다.
    - **전체** - 리디렉션 URI에는 체계 및 경로가 있어야 합니다. 경로는 도메인 다음에 하나 이상의 포워드 슬래시를 포함해야 합니다. 예를 들어 `//contoso/`는 실행되고 `//contoso`는 실행되지 않습니다. 리디렉션 URI가 밑줄 등의 특수 문자를 포함하지 않는지 확인합니다.

9. **만들기**를 클릭합니다.
10. 속성 페이지에서 샘플을 구성할 때 사용할 애플리케이션 ID를 기록합니다.

## <a name="configure-the-sample"></a>샘플 구성

이 자습서에서는 GitHub에서 다운로드할 수 있는 샘플을 구성합니다. 샘플 WPF 데스크톱 애플리케이션은 가입, 로그인을 보여주며 Azure AD B2C에서 보호되는 웹 API를 호출합니다. GitHub에서 [zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip)하고, [리포지토리를 찾거나](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) 샘플을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

앱 설정을 변경하려면 `<your-tenant-name>`을 테넌트 이름으로 바꾸고 `<application-ID`>를 기록해 둔 애플리케이션 ID로 바꿉니다.

1. Visual Studio에서 `active-directory-b2c-wpf` 솔루션을 엽니다.
2. `active-directory-b2c-wpf` 프로젝트에서 **App.xaml.cs** 파일을 열고 다음과 같이 업데이트합니다.

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. **PolicySignUpSignIn** 변수를 생성한 사용자 흐름의 이름으로 업데이트합니다.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>샘플 실행

**F5** 키를 눌러 샘플을 빌드 및 실행합니다.

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

1. **로그인**을 클릭하여 사용자로 등록합니다. 여기서는 **B2C_1_signupsignin1** 사용자 흐름을 사용합니다.
2. Azure AD B2C에서 등록 링크가 있는 로그인 페이지를 제공합니다. 아직 계정이 없으므로 **지금 등록** 링크를 클릭합니다. 
3. 등록 워크플로에서 이메일 주소를 사용하여 사용자의 ID를 수집하고 확인하는 페이지를 제공합니다. 또한 가입 워크플로에서도 사용자 흐름에 정의된 사용자의 암호와 요청된 특성을 수집합니다.

    유효한 이메일 주소를 사용하고 확인 코드를 사용하여 유효성을 검사합니다. 암호를 설정합니다. 요청된 특성에 대한 값을 입력합니다. 

    ![등록 워크플로](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

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
