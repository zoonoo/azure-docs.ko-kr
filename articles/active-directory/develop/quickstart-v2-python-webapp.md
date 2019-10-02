---
title: Microsoft ID 플랫폼 Python 웹앱 빠른 시작 | Azure
description: OAuth2를 사용하여 Python 웹앱에서 Microsoft 로그인을 구현하는 방법 알아보기
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 587e7a82e2a9cde8ff6d08274928ab22aa969061
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309620"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>빠른 시작: Python 웹앱에 Microsoft로 로그인 추가

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

이 자습서에서는 Python 웹 애플리케이션을 Microsoft ID 플랫폼에 통합하는 방법을 알아봅니다. 개발자의 앱은 사용자를 로그인하고, Microsoft Graph API를 호출하기 위한 액세스 토큰을 가져오고, Microsoft Graph API를 요청합니다.

이 가이드를 완료했으면 애플리케이션에서 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 또는 학교 계정뿐만 아니라 개인 Microsoft 계정(outlook.com, live.com 등)의 로그인을 수락하게 됩니다.

![이 빠른 시작에서 생성된 샘플 앱의 작동 방식 표시](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>필수 조건

이 샘플을 실행하려면 다음이 필요합니다.

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) 또는 [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-세션](https://pythonhosted.org/Flask-Session/), [요청](https://2.python-requests.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) 
- Azure AD(Azure Active Directory) 테넌트. Azure AD 테넌트를 가져오는 방법에 대한 자세한 내용은 [Azure AD 테넌트를 가져오는 방법](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)을 참조하세요.

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>빠른 시작 앱 등록 및 다운로드
>
> 빠른 시작 애플리케이션을 시작할 수 있는 기본(옵션 1) 및 수동(옵션 2)의 두 가지 옵션이 있습니다.
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>옵션 1: 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드
>
> 1. [Azure Portal - 앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)으로 이동합니다.
> 1. **새 등록**을 선택합니다.
> 1. 애플리케이션 이름을 입력하고 **등록**을 선택합니다.
> 1. 지침에 따라 새 애플리케이션을 다운로드하고 자동으로 구성합니다.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>옵션 2: 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성
>
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
>
> 애플리케이션을 등록하고 앱의 등록 정보를 솔루션에 수동으로 추가하려면 다음 단계를 따르세요.
>
> 1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
> 1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
> 1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
> 1. **새 등록**을 선택합니다.
> 1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
>      - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `python-webapp`)을 입력합니다.
>      - **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
>      - **리디렉션 URI** 섹션에 있는 드롭다운 목록에서 **웹** 플랫폼을 선택한 다음, 해당 값을 `http://localhost:5000/getAToken`로 설정합니다.
>      - **등록**을 선택합니다. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 기록해 둡니다.
> 1. 왼쪽 메뉴에서 **인증서 및 비밀**을 선택하고 **클라이언트 비밀** 섹션에서 **새 클라이언트 비밀**을 클릭합니다.
>
>      - 키 설명(인스턴스 앱 비밀)을 입력합니다.
>      - **1년 후** 키 기간을 선택합니다.
>      - **추가**를 클릭하면 키 값이 표시됩니다.
>      - 키 값을 복사합니다. 이 시간은 나중에 필요합니다.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
>
> 빠른 시작용 코드 샘플이 작동하려면 다음을 수행해야 합니다.
>
> 1. 회신 URL을 `http://localhost:5000/getAToken`으로 입력합니다.
> 1. 클라이언트 비밀을 만듭니다.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [자동 변경]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-aspnet-webapp/green-check.png) 이 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-your-project"></a>2단계: 프로젝트 다운로드

[코드 샘플 다운로드](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>3단계: 애플리케이션 구성

1. Zip 파일을 루트 폴더에 가까운 로컬 폴더(예: **C:\Azure-Samples**)로 추출합니다.
1. 통합 개발 환경을 사용하는 경우 원하는 IDE에서 샘플을 엽니다(선택 사항).
1. 루트 폴더에 있는 **app_config.py** 파일을 열고 다음 코드 조각으로 바꿉니다.

```python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
```

> [!div renderon="docs"]
> 위치:
>
> - `Enter_the_Application_Id_here` - 등록한 애플리케이션의 애플리케이션 ID입니다.
> - `Enter_the_Client_Secret_Here` - 등록한 애플리케이션의 **인증서 및 비밀**에서 만든 **클라이언트 비밀**입니다.

#### <a name="step-4-run-the-code-sample"></a>4단계: 코드 샘플 실행

1. 다음과 같이 pip를 사용하여 MSAL Python 라이브러리, Flask 프레임워크, 서버 쪽 세션 관리 및 요청을 위한 Flask 세션을 설치해야 합니다.

   ```Shell
   pip install -r requirements.txt
   ```

2. 셸 또는 명령줄에서 app.py를 실행합니다.

   ```Shell
   python app.py
   ```

## <a name="next-steps"></a>다음 단계

사용자가 로그인한 다음, 웹 API를 호출하는 웹앱에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [시나리오: 사용자가 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
