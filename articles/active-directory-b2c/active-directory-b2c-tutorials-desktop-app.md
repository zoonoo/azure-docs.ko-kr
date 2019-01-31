---
title: 자습서 - Azure Active Directory B2C를 사용하여 데스크톱 앱으로 계정을 인증하도록 설정 | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 .NET 데스크톱 앱에 대한 사용자 로그인을 제공하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a99e141a59be654d6d4285be73b0bea60b1e813b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55166973"
---
# <a name="tutorial-enable-desktop-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 데스크톱 앱으로 계정을 인증하도록 설정

이 자습서에서는 Azure AD(Azure Active Directory) B2C를 사용하여 WPF(Windows Presentation Foundation) 데스크톱 애플리케이션에서 사용자를 로그인하고 등록하는 방법을 보여줍니다. Azure AD B2C를 사용하면 개방형 표준 프로토콜을 사용하여 소셜 계정, 엔터프라이즈 계정 및 Azure Active Directory 계정을 인증할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트에게 샘플 데스크톱 앱을 등록합니다.
> * 사용자 가입, 로그인, 프로필 편집 및 암호 재설정에 대한 사용자 흐름을 만듭니다.
> * Azure AD B2C 테넌트를 사용하도록 응용 프로그램 예제를 구성합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* 사용자 고유의 [Azure AD B2C 테넌트](active-directory-b2c-get-started.md)를 만듭니다.
* **.NET 데스크톱 개발** 및 **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.

## <a name="register-desktop-app"></a>데스크톱 앱 등록

Azure Active Directory에서 [액세스 토큰](../active-directory/develop/developer-glossary.md#access-token)을 받으려면 먼저 애플리케이션을 테넌트에 [등록](../active-directory/develop/developer-glossary.md#application-registration)해야 합니다. 앱을 등록하면 테넌트에서 앱에 대한 [응용 프로그램 ID](../active-directory/develop/developer-glossary.md#application-id-client-id)가 만들어집니다. 

Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Azure Portal의 서비스 목록에서 **Azure AD B2C**를 선택합니다. 

2. B2C 설정에서 **응용 프로그램**, **추가**를 차례로 클릭합니다. 

    샘플 웹앱을 테넌트에 등록하려면 다음 설정을 사용합니다.
    
    ![새 앱 추가](media/active-directory-b2c-tutorials-desktop-app/desktop-app-registration.png)
    
    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | 내 샘플 WPF 앱 | 소비자에게 앱을 설명하는 **이름**을 입력합니다. | 
    | **웹앱/웹 API 포함** | 아니요 | 데스크톱 앱에 대해 **아니요**를 선택합니다. |
    | **네이티브 클라이언트 포함** | yes | 이 앱은 데스크톱 앱이므로 네이티브 클라이언트로 간주됩니다. |
    | **URI 리디렉션** | 기본값 | Azure AD B2C가 OAuth 2.0 응답에서 사용자 에이전트를 리디렉션하는 고유한 식별자입니다. |
    | **사용자 지정 리디렉션 URI** | `com.onmicrosoft.contoso.appname://redirect/path` | `com.onmicrosoft.<your tenant name>.<any app name>://redirect/path`를 입력합니다. 그러면 사용자 흐름에서 이 URI에 토큰을 보냅니다. |
    
3. **만들기** 를 클릭하여 앱을 등록합니다.

등록된 앱은 Azure AD B2C 테넌트에 대한 응용 프로그램 목록에 표시됩니다. 목록에서 해당 데스크톱 앱을 선택합니다. 등록된 데스크톱 앱의 속성 창이 표시됩니다.

![데스크톱 앱 속성](./media/active-directory-b2c-tutorials-desktop-app/b2c-desktop-app-properties.png)

**응용 프로그램 클라이언트 ID**를 적어 둡니다. ID는 앱을 고유하게 식별하며 자습서의 뒷부분에서 앱을 구성할 때 필요합니다.

## <a name="create-user-flows"></a>사용자 흐름 만들기

Azure AD B2C 사용자 흐름은 ID 작업에 대한 사용자 환경을 정의합니다. 예를 들어 로그인, 가입, 암호 변경 및 프로필 편집은 일반적인 사용자 흐름입니다.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>가입 또는 로그인 사용자 흐름 만들기

액세스할 사용자를 가입시킨 다음, 데스크톱 앱에 로그인하려면 **가입 또는 로그인 사용자 흐름**을 만듭니다.

1. Azure AD B2C 포털 페이지에서 **사용자 흐름**을 선택하고 **새 사용자 흐름**을 클릭합니다.
2. **추천** 탭에서 **가입 및 로그인**을 클릭합니다.

    사용자 흐름을 구성하려면 다음 설정을 사용합니다.

    ![가입 또는 로그인 사용자 흐름 추가](media/active-directory-b2c-tutorials-desktop-app/add-susi-user-flow.png)

    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiUpIn | 사용자 흐름에 대한 **이름**을 입력합니다. 사용자 흐름 이름 앞에는 **B2C_1_** 접두사가 붙습니다. 샘플 코드에서 사용하는 전체 사용자 흐름 이름은 **B2C_1_SiUpIn**입니다. | 
    | **ID 공급자** | 이메일 등록 | ID 공급자는 사용자를 고유하게 식별하는 데 사용됩니다. |

3.  **사용자 특성 및 클레임** 아래에서 **자세히 표시**를 클릭하고 다음 설정을 선택합니다.

    ![사용자 특성 및 클레임 추가](media/active-directory-b2c-tutorials-desktop-app/add-attributes-and-claims.png)

    | 열      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **특성 수집** | 표시 이름 및 우편 번호 | 등록 시 사용자로부터 수집할 특성을 선택합니다. |
    | **클레임 반환** | 표시 이름, 우편 번호, 새 사용자, 사용자의 개체 ID | [액세스 토큰](../active-directory/develop/developer-glossary.md#access-token)에 포함하려는 [클레임](../active-directory/develop/developer-glossary.md#claim)을 선택합니다. |

4. **확인**을 클릭합니다.

5. **만들기**를 클릭하여 사용자 흐름을 만듭니다. 

### <a name="create-a-profile-editing-user-flow"></a>프로필 편집 사용자 흐름 만들기

사용자가 직접 자신의 사용자 프로필 정보를 다시 설정할 수 있게 하려면 **프로필 편집 사용자 흐름**을 만듭니다.

1. Azure AD B2C 포털 페이지에서 **사용자 흐름**을 선택하고 **새 사용자 흐름**을 클릭합니다.
2. **추천** 탭에서 **프로필 편집**을 클릭합니다.

    사용자 흐름을 구성하려면 다음 설정을 사용합니다.

    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiPe | 사용자 흐름에 대한 **이름**을 입력합니다. 사용자 흐름 이름 앞에는 **B2C_1_** 접두사가 붙습니다. 샘플 코드에서 사용하는 전체 사용자 흐름 이름은 **B2C_1_SiPe**입니다. | 
    | **ID 공급자** | 로컬 계정 로그인 | ID 공급자는 사용자를 고유하게 식별하는 데 사용됩니다. |

3. **사용자 특성** 아래에서 **자세히 표시**를 클릭하고 다음 설정을 선택합니다.

    | 열      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **특성 수집** | 표시 이름 및 우편 번호 | 사용자가 프로필 편집 시 수정할 수 있는 특성을 선택합니다. |
    | **클레임 반환** | 표시 이름, 우편 번호, 사용자의 개체 ID | 프로필을 성공적으로 편집한 후에 [액세스 토큰](../active-directory/develop/developer-glossary.md#access-token)에 포함하려는 [클레임](../active-directory/develop/developer-glossary.md#claim)을 선택합니다. |

4. **확인**을 클릭합니다.
5. **만들기**를 클릭하여 사용자 흐름을 만듭니다. 

### <a name="create-a-password-reset-user-flow"></a>암호 재설정 사용자 흐름 만들기

애플리케이션에서 암호 재설정을 사용하도록 설정하려면 **암호 재설정 사용자 흐름**을 만들어야 합니다. 이 사용자 흐름은 암호 재설정 시의 소비자 환경 및 애플리케이션에서 성공적인 완료를 받는 토큰의 콘텐츠를 설명합니다.

1. Azure AD B2C 포털 페이지에서 **사용자 흐름**을 선택하고 **새 사용자 흐름**을 클릭합니다.
2. **추천** 탭에서 **암호 재설정**을 클릭합니다.

    사용자 흐름을 구성하려면 다음 설정을 사용합니다.

    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SSPR | 사용자 흐름에 대한 **이름**을 입력합니다. 사용자 흐름 이름 앞에는 **B2C_1_** 접두사가 붙습니다. 샘플 코드에서 사용하는 전체 사용자 흐름 이름은 **B2C_1_SSPR**입니다. | 
    | **ID 공급자** | 이메일 주소를 사용하여 암호 재설정 | 사용자를 고유하게 식별하는 데 사용되는 ID 공급자입니다. |

3. **애플리케이션 클레임** 아래에서 **자세히 표시**를 클릭하고 다음 설정을 선택합니다.

    | 열      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **클레임 반환** | 사용자의 개체 ID | 암호를 성공적으로 다시 설정한 후에 [액세스 토큰](../active-directory/develop/developer-glossary.md#access-token)에 포함하려는 [클레임](../active-directory/develop/developer-glossary.md#claim)을 선택합니다. |

4. **확인**을 클릭합니다.
5. **만들기**를 클릭하여 사용자 흐름을 만듭니다. 

## <a name="update-desktop-app-code"></a>데스크톱 앱 코드 업데이트

이제 데스크톱 앱이 등록되고 사용자 흐름이 만들어졌으므로 Azure AD B2C 테넌트를 사용하도록 앱을 구성해야 합니다. 이 자습서에서는 샘플 데스크톱 앱을 구성합니다. 

GitHub에서 [zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip)하고, [리포지토리를 찾거나](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) 샘플을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

샘플 WPF 데스크톱 앱은 데스크톱 앱에서 등록, 로그인 및 보호되는 Web API 호출에 Azure AD B2C를 사용하는 방법을 보여줍니다.

앱에서 테넌트의 앱 등록을 사용하도록 변경하고 만든 사용자 흐름을 구성해야 합니다. 

앱 설정을 변경하려면 다음을 수행합니다.

1. Visual Studio에서 `active-directory-b2c-wpf` 솔루션을 엽니다.

2. `active-directory-b2c-wpf` 프로젝트에서 **App.xaml.cs** 파일을 열고 다음과 같이 업데이트합니다.

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "The Application ID for your desktop app registered in your tenant";
    ```

3. **PolicySignUpSignIn** 변수를 이전 단계에서 만든 *가입 또는 로그인 사용자 흐름* 이름으로 업데이트합니다. *B2C_1_* 접두사를 포함해야 합니다.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_SiUpIn";
    ```

## <a name="run-the-sample-desktop-application"></a>샘플 데스크톱 애플리케이션 실행

**F5** 키를 눌러 데스크톱 앱을 빌드 및 실행합니다. 

샘플 앱은 등록, 로그인, 프로필 수정 및 암호 재설정을 지원합니다. 이 자습서에서는 사용자가 이메일 주소를 사용하여 앱에 등록하고 사용하는 방법을 중점적으로 설명합니다. 다른 시나리오를 직접 탐색할 수 있습니다.

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

1. **로그인** 단추를 클릭하여 데스크톱 앱의 사용자로 등록합니다. 여기서는 이전 단계에서 정의한 **B2C_1_SiUpIn** 사용자 흐름을 사용합니다.

2. Azure AD B2C에서 등록 링크가 있는 로그인 페이지를 제공합니다. 아직 계정이 없으므로 **지금 등록** 링크를 클릭합니다. 

3. 등록 워크플로에서 이메일 주소를 사용하여 사용자의 ID를 수집하고 확인하는 페이지를 제공합니다. 또한 가입 워크플로에서도 사용자 흐름에 정의된 사용자의 암호와 요청된 특성을 수집합니다.

    유효한 이메일 주소를 사용하고 확인 코드를 사용하여 유효성을 검사합니다. 암호를 설정합니다. 요청된 특성에 대한 값을 입력합니다. 

    ![등록 워크플로](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. **만들기**를 클릭하여 로컬 계정을 Azure AD B2C 테넌트에 만듭니다.

이제 사용자는 이메일 주소를 사용하여 로그인하고 데스크톱 앱을 사용할 수 있습니다.

> [!NOTE]
> **API 호출** 단추를 클릭하면 "권한 없음" 오류가 표시됩니다. 데모 테넌트에서 리소스에 액세스하려고 시도하기 때문에 이 오류가 표시되는 것입니다. 액세스 토큰이 Azure AD 테넌트에만 유효하므로 API 호출에 권한이 부여되지 않습니다. 그 다음 자습서를 계속 진행하여 테넌트의 보호되는 웹 API 키를 만드세요. 

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure AD B2C 자습서를 사용하려는 경우 Azure AD B2C 테넌트를 사용할 수 있습니다. 더 이상 필요하지 않으면 [Azure AD B2C 테넌트를 삭제](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure AD B2C 테넌트를 만들고, 사용자 흐름을 만들고, Azure AD B2C 테넌트를 사용하도록 데스크톱 앱 샘플을 업데이트하는 방법을 알아보았습니다. 그 다음 자습서로 넘어가서 데스크톱 앱에서 보호되는 Web API를 등록, 구성 및 호출하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> 
