---
title: 자습서 - Azure Active Directory B2C를 사용하여 데스크톱 애플리케이션에서 Node.js Web API로의 액세스 권한 부여 | Microsoft Docs
description: Active Directory B2C를 사용하여 Node.js web api를 보호하고 .NET 데스크톱 앱에서 호출하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8c4b2d818549da07faf9ecd28f61b4ed5315f745
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679334"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 데스크톱 앱에서 Node.js Web API에 대한 액세스 권한 부여

이 자습서에서는 WPF(Windows Presentation Foundation)에서 Azure AD B2C(Azure Active Directory B2C)로 보호되는 Node.js Web API 리소스를 호출하는 방법을 보여줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 웹 API 애플리케이션 추가
> * 웹 API에 대한 범위 구성
> * 웹 API에 사용 권한 부여
> * 애플리케이션을 사용하도록 샘플 업데이트

## <a name="prerequisites"></a>필수 조건

[자습서: Azure Active Directory B2C를 사용하여 데스크톱 앱으로 계정을 인증하도록 설정](active-directory-b2c-tutorials-desktop-app.md)의 단계 및 필수 조건을 완료합니다.

## <a name="add-a-web-api-application"></a>웹 API 애플리케이션 추가

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>범위 구성

범위는 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 일부 사용자는 읽기 및 쓰기 권한을 모두 가질 수 있지만, 다른 사용자는 읽기 전용 권한을 가질 수 있습니다. 이 자습서에서는 Web API에 대한 읽기 권한을 정의합니다.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>권한 부여

애플리케이션에서 보호된 웹 API를 호출하려면 애플리케이션 사용 권한을 API에 부여해야 합니다. 필수 조건 자습서에서 *app1*이라는 웹 애플리케이션을 Azure AD B2C에 만들었습니다. 이 애플리케이션은 웹 API를 호출하는 데 사용합니다.

1. **애플리케이션**을 선택하고 *nativeapp1*을 선택합니다.
1. **API 액세스**를 선택한 후 **추가**를 선택합니다.
1. **API 선택** 드롭다운에서 *webapi1*을 선택합니다.
1. **범위 선택** 드롭다운에서 이전에 정의한 범위를 선택합니다. *demo.read* 및 *demo.write*가 예입니다.
1. **확인**을 선택합니다.

사용자는 Azure AD B2C로 인증하여 WPF 데스크톱 애플리케이션을 사용합니다. 데스크톱 애플리케이션은 Azure AD B2C에서 권한 부여를 받아 보호되는 웹 API에 액세스합니다.

## <a name="configure-the-sample"></a>샘플 구성

이제 웹 API가 등록되었고 범위가 정의되었으므로 웹 API 코드에서 Azure AD B2C 테넌트를 사용하도록 구성합니다. 이 자습서에서는 GitHub에서 다운로드할 수 있는 샘플 Node.js 웹 애플리케이션을 구성합니다.

GitHub에서 [Zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)하거나 샘플 웹앱을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Node.js Web API 샘플에서는 Passport.js 라이브러리를 사용하여 Azure AD B2C가 API 호출을 보호하도록 설정합니다.

1. `index.js` 파일을 엽니다.
2. Azure AD B2C 테넌트 등록 정보를 사용하여 샘플을 구성합니다. 다음 코드 줄을 변경합니다.

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>샘플 실행

1. Node.js 명령 프롬프트를 시작합니다.
2. Node.js 샘플이 포함된 디렉터리로 변경합니다. 예: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. 다음 명령을 실행합니다.
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>데스크톱 애플리케이션 실행

1. Visual Studio에서 **active-directory-b2c-wpf** 솔루션을 엽니다.
2. **F5** 키를 눌러 데스크톱 앱을 실행합니다.
3. [데스크톱 앱에서 Azure Active Directory B2C를 사용하여 사용자 인증 자습서](active-directory-b2c-tutorials-desktop-app.md)에서 사용한 이메일 주소와 암호를 사용하여 로그인합니다.
4. **API 호출** 단추를 클릭합니다.

데스크톱 애플리케이션이 웹 API에 요청을 보내고 로그인한 사용자의 표시 이름을 사용하여 응답을 가져옵니다. 보호되는 데스크톱 애플리케이션이 Azure AD B2C 테넌트에서 보호되는 웹 API를 호출합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 웹 API 애플리케이션 추가
> * 웹 API에 대한 범위 구성
> * 웹 API에 사용 권한 부여
> * 애플리케이션을 사용하도록 샘플 업데이트

> [!div class="nextstepaction"]
> [자습서: Azure Active Directory B2C의 애플리케이션에 ID 공급자 추가](tutorial-add-identity-providers.md)
