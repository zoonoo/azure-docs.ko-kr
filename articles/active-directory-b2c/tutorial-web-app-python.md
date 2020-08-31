---
title: '자습서: Python 웹 애플리케이션에서 인증 사용'
titleSuffix: Azure AD B2C
description: 이 자습서에서는 Azure Active Directory B2C를 사용하여 Python Flask 웹 애플리케이션에 대한 사용자 로그인을 제공하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: 6a113b3a3df475853f3690a0e932378bc0e2ab02
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87844629"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>자습서: Python 웹 애플리케이션에서 Azure AD B2C를 사용하여 인증 사용

이 자습서에서는 Azure AD B2C(Azure Active Directory B2C)를 사용하여 Python Flask 웹 애플리케이션에서 사용자를 가입하고 로그인하는 방법을 보여 줍니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트에 등록된 애플리케이션에 회신 URL 추가
> * GitHub에서 코드 샘플 다운로드
> * 해당 테넌트에서 작동하도록 샘플 애플리케이션의 코드 수정
> * 가입/로그인 사용자 흐름을 사용하여 가입

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 단계를 진행하려면 다음과 같은 Azure AD B2C 리소스가 있어야 합니다.

* [Azure AD B2C 테넌트](tutorial-create-tenant.md)
* 테넌트에서 [등록된 애플리케이션](tutorial-register-applications.md) 및 해당 *애플리케이션(클라이언트) ID* 및 *클라이언트 암호*
* 테넌트에서 [만든 사용자 흐름](tutorial-create-user-flows.md)

또한 로컬 개발 환경에서 다음 항목이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/) 또는 다른 코드 편집기
* [Python](https://nodejs.org/en/download/) 2.7+ 또는 3+

## <a name="add-a-redirect-uri"></a>리디렉션 URI 추가

필수 조건의 일부로 완료한 두 번째 자습서에서 Azure AD B2C에 웹 애플리케이션을 등록했습니다. 이 자습서에서 코드 샘플과의 통신을 사용하도록 설정하려면 애플리케이션 등록에 회신 URL(리디렉트 URI라고도 함)을 추가해야 합니다.

Azure AD B2C 테넌트에 애플리케이션을 업데이트하려면 새로운 통합 **앱 등록** 환경 또는 레거시 **애플리케이션(레거시)** 환경을 사용하면 됩니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **앱 등록**을 선택하고 **소유한 애플리케이션** 탭을 선택한 다음, *webapp1* 애플리케이션을 선택합니다.
1. **관리**에서 **인증**을 선택합니다.
1. **웹** 아래에서 **URI 추가** 링크를 선택한 다음, 텍스트 상자에 `http://localhost:5000/getAToken`을 입력합니다.
1. **저장**을 선택합니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **Azure AD B2C**를 검색하여 선택합니다.
1. **애플리케이션(레거시)** 을 선택한 다음, *webapp1* 애플리케이션을 선택합니다.
1. **회신 URL** 아래에서 `http://localhost:5000/getAToken`을 추가합니다.
1. **저장**을 선택합니다.
* * *

## <a name="get-the-sample-code"></a>샘플 코드 가져오기

이 자습서에서는 GitHub에서 다운로드한 코드 샘플을 B2C 테넌트에서 작동하도록 구성합니다. 이 샘플은 Python Flask 웹 애플리케이션에서 사용자 가입 및 로그인에 Azure AD B2C를 사용하는 방법을 보여 줍니다.

[.ZIP 보관 파일을 다운로드](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)하거나 GitHub에서 [코드 샘플 리포지토리](https://github.com/Azure-Samples/ms-identity-python-webapp)를 복제합니다.

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>샘플 업데이트

샘플을 얻은 후에는 Azure AD B2C 테넌트, 애플리케이션 등록 및 사용자 흐름을 사용하도록 애플리케이션을 구성합니다.

프로젝트의 루트 디렉터리에서 다음을 수행합니다.

1. *app_config* 파일의 이름을 *app_config.py.OLD*로 바꿉니다.
1. *app_config_b2c.py*의 이름을 *app_config.py*로 바꿉니다.

새로 이름이 바뀐 *app_config.py*를 필수 구성 요소의 일부로 만든 Azure AD B2C 테넌트 및 애플리케이션 등록 값으로 업데이트합니다.

1. 편집기에서 *app_config.py* 파일을 엽니다.
1. `b2c_tenant` 값을 Azure AD B2C 테넌트의 이름으로 업데이트합니다(예: *contosob2c*).
1. 필수 구성 요소의 일부로 만든 사용자 흐름의 이름과 일치하도록 각 `*_user_flow` 값을 업데이트합니다.
1. `CLIENT_ID` 값을 필수 구성 요소의 일부로 등록한 웹 애플리케이션의 **애플리케이션(클라이언트) ID**로 업데이트합니다.
1. `CLIENT_SECRET` 값을 필수 구성 요소에서 만든 **클라이언트 암호** 값으로 업데이트합니다. 보안 강화를 위해 설명에 권장된 대로 **환경 변수**에 대신 저장하는 것이 좋습니다.

*app_config.py*의 맨 위 섹션은 이제 다음 코드 조각과 유사하게 표시됩니다.

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> 코드 조각 설명에 나와 있는 것처럼 애플리케이션 코드에 **비밀을 일반 텍스트로 저장하지 않는 것**이 좋습니다. 하드 코딩된 변수는 *편의를 위해* 코드 샘플에서 사용됩니다. 환경 변수 또는 Azure Key Vault와 같은 비밀 저장소를 사용하는 것이 좋습니다.

## <a name="run-the-sample"></a>샘플 실행

1. 콘솔 또는 터미널에서 샘플이 포함된 디렉터리로 전환합니다. 예를 들면 다음과 같습니다.

    ```console
    cd ms-identity-python-webapp
    ```
1. 다음 명령을 실행하여 PyPi에서 필요한 패키지를 설치하고 로컬 머신에서 웹앱을 실행합니다.

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    콘솔 창에 로컬에서 실행 중인 애플리케이션의 포트 번호가 표시됩니다.

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. `http://localhost:5000`으로 이동하여 로컬 머신에서 실행 중인 웹 애플리케이션을 봅니다.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="로컬로 실행되는 Python Flask 웹 애플리케이션을 보여 주는 웹 브라우저":::

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

이 샘플 애플리케이션은 가입, 로그인 및 암호 재설정을 지원합니다. 이 자습서에서는 이메일 주소를 사용하여 가입합니다.

1. **로그인**을 선택하여 이전 단계에서 지정한 *B2C_1_signupsignin1* 사용자 흐름을 시작합니다.
1. Azure AD B2C에서 가입 링크가 있는 로그인 페이지를 제공합니다. 아직 계정이 없으므로 **지금 가입** 링크를 선택합니다.
1. 가입 워크플로에서 이메일 주소를 사용하여 사용자의 ID를 수집하고 확인하는 페이지를 제공합니다. 또한 가입 워크플로에서는 사용자 흐름에 정의된 사용자의 암호와 요청된 특성을 수집합니다.

    유효한 이메일 주소를 사용하고 확인 코드를 사용하여 유효성을 검사합니다. 암호를 설정합니다. 요청된 특성에 대한 값을 입력합니다.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Azure AD B2C 사용자 흐름에 표시된 가입 페이지":::

1. **만들기**를 선택하여 로컬 계정을 Azure AD B2C 디렉터리에 만듭니다.

**만들기**를 선택하면 애플리케이션이 로그인한 사용자의 이름을 표시합니다.

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="사용자가 로그인한 Python Flask 웹 애플리케이션을 보여 주는 웹 브라우저":::

로그인을 테스트하려면 **로그아웃** 링크를 선택한 다음, **로그인**을 선택하고 가입할 때 입력한 이메일 주소와 암호를 사용하여 로그인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가입 및 로그인 기능을 제공하기 위해 Azure AD B2C 테넌트의 사용자 흐름과 연동되는 Python Flask 웹 애플리케이션을 구성했습니다. 다음 단계를 완료했습니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트에 등록된 애플리케이션에 회신 URL을 추가했습니다.
> * GitHub에서 코드 샘플을 다운로드했습니다.
> * 해당 테넌트에서 작동하도록 샘플 애플리케이션의 코드를 수정했습니다.
> * 가입/로그인 사용자 흐름을 사용하여 가입했습니다.

다음으로 Azure AD B2C로 사용자에게 표시되는 사용자 흐름 페이지의 UI를 사용자 지정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: Azure AD B2C의 사용자 환경 인터페이스 사용자 지정](tutorial-customize-ui.md)
