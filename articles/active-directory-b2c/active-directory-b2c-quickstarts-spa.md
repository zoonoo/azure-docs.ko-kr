---
title: "Azure AD B2C 단일 페이지 응용 프로그램 시험 사용 | Microsoft Docs"
description: "테스트 Azure AD B2C 환경을 사용하여 로그인, 등록, 프로필 편집 및 암호 재설정 사용자 경험을 시험 사용"
services: active-directory-b2c
documentationcenter: 
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 5a8a46af-28bb-4b70-a7f0-01a5240d0255
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 22da1ae317ba685d32f93d3331cf794b568891ec
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-single-page-application-configured-with-azure-ad-b2c"></a>Azure AD B2C로 구성된 단일 페이지 응용 프로그램 시험 사용

## <a name="about-this-sample"></a>이 샘플 정보

Azure Active Directory B2C는 응용 프로그램, 비즈니스 및 고객을 보호하기 위한 클라우드 ID 관리 기능을 제공합니다.  이 빠른 시작에서는 샘플 단일 페이지 응용 프로그램을 사용하여 다음 작업을 보여 줍니다.

* **등록 또는 로그인** 정책을 사용하여 전자 메일 주소로 소셜 ID 공급자 또는 로컬 계정을 만들고 이러한 계정으로 로그인합니다. 
* **API를 호출**하여 Azure AD B2C 보호 리소스에서 표시 이름을 검색합니다.

## <a name="prerequisites"></a>필수 조건

* 다음 워크로드와 함께 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.
    - **ASP.NET 및 웹 배포**

* [Node.js](https://nodejs.org/en/download/)

* Facebook, Google, Microsoft 또는 Twitter의 소셜 계정입니다. 소셜 계정이 없는 경우 유효한 전자 메일 주소가 필요합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>샘플 다운로드

GitHub에서 [샘플 응용 프로그램을 다운로드 또는 복제](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)합니다.

## <a name="run-the-sample-application"></a>샘플 응용 프로그램 실행

Node.js 명령 프롬프트에서 이 샘플을 실행하려면 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

콘솔 창에는 컴퓨터에서 실행 중인 웹 응용 프로그램의 포트 번호가 표시됩니다.

```
Listening on port 6420...
```

웹 브라우저에서 `http://localhost:6420`을 열어 웹 응용 프로그램에 액세스합니다.


![브라우저의 샘플 앱](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>계정 만들기

**로그인** 단추를 클릭하여 Azure AD B2C **등록 또는 로그인** 워크플로를 시작합니다. 계정을 만들 때 기존 소셜 ID 공급자 계정 또는 전자 메일 계정을 사용할 수 있습니다.

### <a name="sign-up-using-a-social-identity-provider"></a>소셜 ID 공급자를 사용하여 등록

소셜 ID 공급자를 사용하여 등록하려면 사용할 ID 공급자의 단추를 클릭합니다. 전자 메일 주소를 사용하려는 경우 [전자 메일 주소를 사용하여 등록](#sign-up-using-an-email-address) 섹션으로 이동합니다.

![로그인 또는 등록 공급자](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

소셜 계정 자격 증명을 사용하여 인증(로그인)하고 응용 프로그램에 사용자의 소셜 계정에서 정보를 읽도록 권한을 부여해야 합니다. 액세스를 부여하면 응용 프로그램은 이름 및 구/군/시와 같은 소셜 계정의 프로필 정보를 검색할 수 있습니다. 

![소셜 계정을 사용하여 인증 및 권한 부여](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

새 계정 프로필 세부 정보는 소셜 계정의 정보로 미리 채워집니다. 

![새 계정 등록 프로필 세부 정보](media/active-directory-b2c-quickstarts-spa/new-account-sign-up-profile-details-spa.png)

표시 이름, 직책 및 구/군/시 필드를 업데이트하고 **계속**을 클릭합니다.  입력하는 값은 Azure AD B2C 사용자 계정 프로필에 사용됩니다.

ID 공급자를 사용하는 새 Azure AD B2C 사용자 계정을 성공적으로 만들었습니다. 

다음 단계: [리소스 호출](#call-a-resource) 섹션

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

소셜 계정을 사용하여 인증을 제공하지 않으려는 경우 유효한 전자 메일 주소를 사용하여 Azure AD B2C 사용자 계정을 만들 수 있습니다. Azure AD B2C 로컬 사용자 계정은 ID 공급자로 Azure Active Directory를 사용합니다. 전자 메일 주소를 사용하려면 **계정이 없으세요? 지금 등록하세요.** 링크를 클릭합니다.

![전자 메일을 사용하여 로그인 또는 등록](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-email-spa.png)

유효한 전자 메일 주소를 입력하고 **확인 코드 보내기**를 클릭합니다. 유효한 전자 메일 주소는 Azure AD B2C에서 확인 코드를 받는 데 필요합니다. 

전자 메일에서 수신한 확인 코드를 입력하고 **코드 확인**을 클릭합니다.

프로필 정보를 추가하고 **만들기**를 클릭합니다.

![전자 메일을 사용하여 새 계정 등록](media/active-directory-b2c-quickstarts-spa/sign-up-new-account-profile-email-web.png)

새 Azure AD B2C 로컬 사용자 계정을 성공적으로 만들었습니다.

## <a name="call-a-resource"></a>리소스 호출

로그인한 후 **웹 API 호출** 단추를 클릭하여 웹 API 호출에서 반환된 표시 이름을 JSON 개체로 지정할 수 있습니다. 

![웹 API 응답](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

## <a name="next-steps"></a>다음 단계

다음 단계는 자체 Azure AD B2C 테넌트를 만들고 해당 테넌트를 사용하여 실행되도록 샘플을 구성하는 것입니다. 

> [!div class="nextstepaction"]
> [Azure Portal에서 Azure Active Directory B2C 테넌트 만들기](active-directory-b2c-get-started.md)