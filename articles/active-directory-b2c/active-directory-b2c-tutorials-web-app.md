---
title: 자습서 - Azure Active Directory B2C를 사용하여 웹 애플리케이션이 계정을 인증하도록 설정 | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 ASP.NET 웹 애플리케이션에 대한 사용자 로그인을 제공하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 714d733e765f28d1244f6ee1c7b1cb237c0c4b1f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198353"
---
# <a name="tutorial-enable-a-web-application-to-authenticate-with-accounts-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 웹 애플리케이션이 계정을 인증하도록 설정

이 자습서에서는 Azure AD(Azure Active Directory) B2C를 사용하여 로그인하고 ASP.NET 웹앱에서 사용자를 로그인하고 등록하는 방법을 보여 줍니다. Azure AD B2C를 사용하면 개방형 표준 프로토콜을 사용하여 소셜 계정, 엔터프라이즈 계정 및 Azure Active Directory 계정을 인증할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트에게 샘플 ASP.NET 웹앱을 등록합니다.
> * 사용자 가입, 로그인, 프로필 편집 및 암호 재설정에 대한 사용자 흐름을 만듭니다.
> * Azure AD B2C 테넌트를 사용하도록 샘플 웹앱을 구성합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* 사용자 고유의 [Azure AD B2C 테넌트](active-directory-b2c-get-started.md)를 만듭니다.
* **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.

## <a name="register-web-app"></a>웹앱 등록

Azure Active Directory에서 [액세스 토큰](../active-directory/develop/developer-glossary.md#access-token)을 받으려면 먼저 애플리케이션을 테넌트에 [등록](../active-directory/develop/developer-glossary.md#application-registration)해야 합니다. 앱을 등록하면 테넌트에서 앱에 대한 [응용 프로그램 ID](../active-directory/develop/developer-glossary.md#application-id-client-id)가 만들어집니다. 

Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다. 이전 자습서에서 만든 테넌트를 사용해야 합니다. 

2. B2C 설정에서 **응용 프로그램**, **추가**를 차례로 클릭합니다. 

    샘플 웹앱을 테넌트에 등록하려면 다음 설정을 사용합니다.

    ![새 앱 추가](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)
    
    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | 내 샘플 웹앱 | 소비자에게 앱을 설명하는 **이름**을 입력합니다. | 
    | **웹앱/웹 API 포함** | yes | 웹앱에 대해 **예**를 선택합니다. |
    | **암시적 흐름 허용** | yes | 앱에서 [OpenID Connect 로그인](active-directory-b2c-reference-oidc.md)을 사용하므로 **예**를 선택합니다. |
    | **회신 URL** | `https://localhost:44316` | 회신 URL은 Azure AD B2C에서 앱이 요청한 토큰을 반환하는 엔드포인트입니다. 이 자습서에서는 샘플이 로컬(로컬 호스트)에서 실행되고 44316 포트에서 수신 대기합니다. |
    | **네이티브 클라이언트 포함** | 아니요 | 이는 웹앱이지만 기본 클라이언트가 아니기 때문에 [아니요]를 선택합니다. |
    
3. **만들기** 를 클릭하여 앱을 등록합니다.

등록된 앱은 Azure AD B2C 테넌트에 대한 응용 프로그램 목록에 표시됩니다. 목록에서 웹앱을 선택합니다. 웹앱의 속성 창이 표시됩니다.

![웹앱 속성](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

**응용 프로그램 ID**를 적어 둡니다. ID는 앱을 고유하게 식별하며 자습서의 뒷부분에서 앱을 구성할 때 필요합니다.

### <a name="create-a-client-password"></a>클라이언트 암호 만들기

Azure AD B2C는 [클라이언트 응용 프로그램](../active-directory/develop/developer-glossary.md#client-application)에 OAuth2 인증을 사용합니다. 웹앱은 [기밀 클라이언트](../active-directory/develop/developer-glossary.md#web-client)이며 클라이언트 ID 또는 응용 프로그램 ID와 클라이언트 비밀, 클라이언트 암호 또는 응용 프로그램 키가 필요합니다.

1. 등록된 웹앱에 대한 [키] 페이지를 선택하고 **키 생성**을 클릭합니다.

2. **저장**을 클릭하여 앱 키를 표시합니다.

    ![앱 일반 키 페이지](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

키는 포털에서 한 번만 표시되므로 키 값을 복사하여 저장하는 것이 중요합니다. 앱 구성에 이 값이 필요합니다. 키는 안전하게 보관하고, 공개적으로 공유하지 마세요.

## <a name="create-user-flows"></a>사용자 흐름 만들기

Azure AD B2C 사용자 흐름은 ID 작업에 대한 사용자 환경을 정의합니다. 예를 들어 로그인, 가입, 암호 변경 및 프로필 편집은 일반적인 사용자 흐름입니다.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>가입 또는 로그인 사용자 흐름 만들기

액세스할 사용자를 가입시킨 다음, 웹앱에 로그인하려면 **가입 또는 로그인 사용자 흐름**을 만듭니다.

1. Azure AD B2C 포털 페이지에서 **사용자 흐름**을 선택하고 **새 사용자 흐름**을 클릭합니다.
2. **추천** 탭에서 **가입 및 로그인**을 클릭합니다.

    사용자 흐름을 구성하려면 다음 설정을 사용합니다.

    ![가입 또는 로그인 사용자 흐름 추가](media/active-directory-b2c-tutorials-web-app/add-susi-user-flow.png)

    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SiUpIn | 사용자 흐름에 대한 **이름**을 입력합니다. 사용자 흐름 이름 앞에는 **b2c_1_** 접두사가 붙습니다. 샘플 코드에서 사용하는 전체 사용자 흐름 이름은 **b2c_1_SiUpIn**입니다. | 
    | **ID 공급자** | 이메일 등록 | ID 공급자는 사용자를 고유하게 식별하는 데 사용됩니다. |

3. **사용자 특성 및 클레임** 아래에서 **자세히 표시**를 클릭하고 다음 설정을 선택합니다.

    ![가입 또는 로그인 사용자 흐름 추가](media/active-directory-b2c-tutorials-web-app/add-attributes-and-claims.png)

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
    | **Name** | SiPe | 사용자 흐름에 대한 **이름**을 입력합니다. 사용자 흐름 이름 앞에는 **b2c_1_** 접두사가 붙습니다. 샘플 코드에서 사용하는 전체 사용자 흐름 이름은 **b2c_1_SiPe**입니다. | 
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

1. Azure AD B2C 포털 페이지에서 **암호 재설정 정책**을 선택하고 **추가**를 클릭합니다.
2. **추천** 탭에서 **암호 재설정**을 클릭합니다.

    사용자 흐름을 구성하려면 다음 설정을 사용합니다.

    | 설정      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name** | SSPR | 사용자 흐름에 대한 **이름**을 입력합니다. 사용자 흐름 이름 앞에는 **b2c_1_** 접두사가 붙습니다. 샘플 코드에서 사용하는 전체 사용자 흐름 이름은 **b2c_1_SSPR**입니다. | 
    | **ID 공급자** | 이메일 주소를 사용하여 암호 재설정 | 사용자를 고유하게 식별하는 데 사용되는 ID 공급자입니다. |

3. **애플리케이션 클레임** 아래에서 **자세히 표시**를 클릭하고 다음 설정을 선택합니다.
    | 열      | 제안 값  | 설명                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **클레임 반환** | 사용자의 개체 ID | 암호를 성공적으로 다시 설정한 후에 [액세스 토큰](../active-directory/develop/developer-glossary.md#access-token)에 포함하려는 [클레임](../active-directory/develop/developer-glossary.md#claim)을 선택합니다. |

4. **확인**을 클릭합니다.
5. **만들기**를 클릭하여 사용자 흐름을 만듭니다. 

## <a name="update-web-app-code"></a>웹앱 코드 업데이트

이제 웹앱이 등록되고 사용자 흐름이 만들어졌으므로 Azure AD B2C 테넌트를 사용하도록 앱을 구성해야 합니다. 이 자습서에서는 GitHub에서 다운로드할 수 있는 샘플 웹앱을 구성합니다. 

GitHub에서 [Zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)하거나 샘플 웹앱을 복제합니다. 경로의 총 문자 길이가 260자 미만인 폴더에 샘플 파일을 추출합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

샘플 ASP.NET 웹앱은 할 일 목록을 만들고 업데이트하는 간단한 작업 목록 앱입니다. 앱에서 [Microsoft OWIN 미들웨어 구성 요소](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/)를 사용하여 사용자가 Azure AD B2C 테넌트에서 이 앱을 사용하기 위해 등록할 수 있도록 합니다. Azure AD B2C 사용자 흐름을 만들면 사용자가 소셜 계정을 사용하거나 자신의 ID로 사용할 계정을 만들어 앱에 액세스할 수 있습니다. 

샘플 솔루션에는 두 개의 프로젝트가 있습니다.

**웹앱 샘플 앱(TaskWebApp):** 작업 목록을 만들고 편집할 웹앱입니다. 웹앱에서 **가입 또는 로그인** 사용자 흐름을 사용하여 사용자를 가입 또는 로그인시킵니다.

**Web API 샘플 앱(TaskService):** 작업 목록 만들기, 읽기, 업데이트 및 삭제 기능을 지원하는 Web API입니다. 웹 API는 Azure AD B2C를 통해 보호되고 웹앱에서 호출됩니다.

테넌트에서 앱 등록을 사용하도록 앱을 변경해야 하며, 여기에는 클라이언트 ID와 이전에 기록한 키가 포함됩니다. 또한 만든 사용자 흐름도 구성해야 합니다. 샘플 웹앱은 Web.config 파일에서 구성 값을 앱 설정으로 정의합니다. 앱 설정을 변경하려면 다음을 수행합니다.

1. Visual Studio에서 **B2C-WebAPI-DotNet** 솔루션을 엽니다.

2. **TaskWebApp** 웹앱 프로젝트에서 **Web.config** 파일을 엽니다. `ida:Tenant`의 값을 앞에서 만든 테넌트 이름으로 바꿉니다. `ida:ClientId`의 값을 앞에서 기록한 응용 프로그램 ID로 바꿉니다. `ida:ClientSecret`의 값을 앞에서 기록한 키로 바꿉니다.

3. **Web.config** 파일에서 `ida:SignUpSignInPolicyId`의 값을 `b2c_1_SiUpIn`으로 바꿉니다. `ida:EditProfilePolicyId`의 값을 `b2c_1_SiPe`로 바꿉니다. `ida:ResetPasswordPolicyId`의 값을 `b2c_1_SSPR`로 바꿉니다.

## <a name="run-the-sample-web-app"></a>샘플 웹앱 실행

[솔루션 탐색기]에서 **TaskWebApp** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭합니다.

**F5** 키를 눌러 웹앱을 시작합니다. 기본 브라우저가 로컬 웹 사이트 주소(`https://localhost:44316/`)에서 시작됩니다. 

샘플 앱은 등록, 로그인, 프로필 수정 및 암호 재설정을 지원합니다. 이 자습서에서는 사용자가 이메일 주소를 사용하여 앱에 등록하고 사용하는 방법을 중점적으로 설명합니다. 다른 시나리오를 직접 탐색할 수 있습니다.

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

1. 위쪽 배너에서 **등록/로그인** 링크를 클릭하여 웹앱의 사용자로 등록합니다. 여기서는 이전 단계에서 정의한 **b2c_1_SiUpIn** 사용자 흐름을 사용합니다.

2. Azure AD B2C에서 등록 링크가 있는 로그인 페이지를 제공합니다. 아직 계정이 없으므로 **지금 등록** 링크를 클릭합니다. 

3. 등록 워크플로에서 이메일 주소를 사용하여 사용자의 ID를 수집하고 확인하는 페이지를 제공합니다. 또한 가입 워크플로에서도 사용자 흐름에 정의된 사용자의 암호와 요청된 특성을 수집합니다.

    유효한 이메일 주소를 사용하고 확인 코드를 사용하여 유효성을 검사합니다. 암호를 설정합니다. 요청된 특성에 대한 값을 입력합니다. 

    ![등록 워크플로](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. **만들기**를 클릭하여 로컬 계정을 Azure AD B2C 테넌트에 만듭니다.

이제 사용자는 이메일 주소를 사용하여 로그인하고 웹앱을 사용할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure AD B2C 자습서를 사용하려는 경우 Azure AD B2C 테넌트를 사용할 수 있습니다. 더 이상 필요하지 않으면 [Azure AD B2C 테넌트를 삭제](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure AD B2C 테넌트를 만들고, 사용자 흐름을 만들고, Azure AD B2C 테넌트를 사용하도록 웹앱 샘플을 업데이트하는 방법을 알아보았습니다. Azure AD B2C 테넌트에서 보호되는 ASP.NET 웹 API를 등록, 구성 및 호출하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [자습서: Azure Active Directory B2C를 사용하여 ASP.NET 웹 API 보호](active-directory-b2c-tutorials-web-api.md)
