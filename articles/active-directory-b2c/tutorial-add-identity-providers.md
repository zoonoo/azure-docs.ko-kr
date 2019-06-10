---
title: 자습서 - 애플리케이션에 ID 공급자 추가 - Azure Active Directory B2C | Microsoft Docs
description: Azure Portal을 사용하여 Azure Active Directory B2C의 애플리케이션에 ID 공급자를 추가하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 76e12dc6bf9bcb50dc58e7730f3a08dd6a9d4440
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512206"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C의 애플리케이션에 ID 공급자 추가

애플리케이션에서 사용자가 다른 ID 공급자로 로그인하도록 허용할 수 있습니다. ‘ID 공급자’는 애플리케이션에 인증 서비스를 제공하는 동시에 ID 정보를 만들고 유지 관리합니다.  Azure Portal을 사용하여 Azure AD(Azure Active Directory) B2C에서 지원하는 ID 공급자를 [사용자 흐름](active-directory-b2c-reference-policies.md)에 추가할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * ID 공급자 애플리케이션 만들기
> * 테넌트에 ID 공급자 추가
> * 사용자 흐름에 ID 공급자 추가

일반적으로 애플리케이션에서 하나의 ID 공급자만 사용하지만, ID 공급자를 더 추가할 수 있습니다. 이 자습서에서는 Azure AD ID 공급자 및 Facebook ID 공급자를 애플리케이션에 추가하는 방법을 보여 줍니다. 애플리케이션에 두 ID 공급자를 추가하는 것은 선택 사항입니다. [Amazon](active-directory-b2c-setup-amzn-app.md), [Github](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md) 또는 [Twitter](active-directory-b2c-setup-twitter-app.md)와 같은 다른 ID 공급자를 추가할 수도 있습니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

사용자가 애플리케이션에 등록 및 로그인할 수 있도록 하는 [사용자 흐름을 만듭니다](tutorial-create-user-flows.md). 

## <a name="create-applications"></a>애플리케이션 만들기

ID 공급자 애플리케이션은 Azure AD B2C 테넌트와 통신할 수 있도록 하는 식별자와 키를 제공합니다. 이 자습서 섹션에서는 테넌트에 ID 공급자를 추가하기 위해 식별자와 키를 가져오는 Azure AD 애플리케이션 및 Facebook 애플리케이션을 만듭니다. ID 공급자 중 하나만 추가하는 경우 해당 공급자에 대한 애플리케이션만 만들면 됩니다.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 애플리케이션 만들기

Azure AD에서 사용자 로그인을 허용하려면 Azure AD 테넌트 내에 애플리케이션을 등록해야 합니다. Azure AD 테넌트는 Azure AD B2C 테넌트와 다릅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 맨 위 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 Azure AD 테넌트가 포함된 디렉터리를 선택하여 Azure AD 테넌트가 포함된 디렉터리를 사용합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
4. **새 애플리케이션 등록**을 선택합니다.
5. 애플리케이션의 이름을 입력합니다. 예: `Azure AD B2C App`.
6. **애플리케이션 종류**에서 `Web app / API`를 선택합니다.
7. **Sign-On URL**의 경우, 다음 URL을 모두 소문자로 입력합니다. 여기서 `your-B2C-tenant-name`는 Azure AD B2C 테넌트의 이름으로 바뀝니다.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```
    
    예: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
    
    이제 모든 URL은 [b2clogin.com](b2clogin.md)을 사용해야 합니다.

8. **만들기**를 클릭합니다. 나중에 사용할 **애플리케이션 ID**를 복사합니다.
9. 애플리케이션을 선택하고 **설정**을 선택합니다.
10. **키**를 선택하고 키 설명을 입력하고 기간을 선택한 다음, **저장**을 클릭합니다. 이 자습서의 뒷부분에서 사용할 수 있도록 키 값을 복사합니다.

### <a name="create-a-facebook-application"></a>Facebook 애플리케이션 만들기

Azure AD B2C의 ID 공급자로 Facebook 계정을 사용하려면 Facebook에서 애플리케이션을 만들어야 합니다. Facebook 계정이 없는 경우 [https://www.facebook.com/](https://www.facebook.com/)에서 얻을 수 있습니다.

1. Facebook 계정 자격 증명으로 [개발자용 Facebook](https://developers.facebook.com/)에 로그인합니다.
2. 아직 등록하지 않은 경우 Facebook 개발자로 등록해야 합니다. 등록하려면 페이지의 오른쪽 위에서 **등록**을 선택하고 Facebook의 정책에 동의한 다음, 등록 단계를 완료합니다.
3. **내 앱**을 선택한 후 **새 앱 추가**를 클릭합니다. 
4. **표시 이름** 및 유효한 **연락처 전자 메일**을 제공합니다.
5. **앱 ID 만들기**를 클릭합니다. Facebook 플랫폼 정책에 동의하고 온라인 보안 검사를 완료해야 할 수도 있습니다.
6. **설정** > **기본**을 선택합니다.
7. `Business and Pages` 등의 **범주**를 선택합니다. 이 값은 Facebook의 경우 필수이지만 Azure AD B2C에서는 사용되지 않습니다.
8. 페이지의 맨 아래에서 **플랫폼 추가**를 선택한 후 **웹 사이트**를 선택합니다.
9. **사이트 URL**에 `https://your-tenant-name.b2clogin.com/`을 입력합니다. 여기서 `your-tenant-name`은 실제 테넌트의 이름으로 바꾸세요. **개인 정보 취급 방침 URL**의 URL(예: `http://www.contoso.com`)을 입력합니다. 정책 URL은 응용 프로그램에 대한 개인 정보를 제공하기 위해 유지 관리하는 페이지입니다.
10. **변경 내용 저장**을 선택합니다.
11. 페이지의 맨 위에서 **앱 ID** 값을 복사합니다. 
12. **표시**를 클릭하고 **앱 비밀** 값을 복사합니다. 테넌트에서 Facebook을 ID 공급자로 구성하려면 둘 다 사용합니다. **앱 암호** 는 중요한 보안 자격 증명입니다.
13. **제품**을 선택한 후 **Facebook 로그인** 아래에서 **설정**을 선택합니다.
14. **Facebook 로그인** 아래에서 **설정**을 선택합니다.
15. **유효한 OAuth 리디렉션 URI**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로 바꿉니다. 페이지 아래쪽에 있는 **변경 내용 저장** 을 클릭합니다.
16. Azure AD B2C에서 Facebook 애플리케이션을 사용할 수 있도록 하려면 페이지 오른쪽 위에 있는 **상태** 선택기를 클릭하고 **켬**으로 설정합니다. **확인**을 클릭합니다. 이때 상태가 **개발**에서 **라이브**로 변경됩니다.

## <a name="add-the-identity-providers"></a>ID 공급자 추가

추가하려는 ID 공급자에 대한 애플리케이션을 만든 후 ID 공급자를 테넌트에 추가합니다.

### <a name="add-the-azure-active-directory-identity-provider"></a>Azure Active Directory ID 공급자 추가

1. 맨 위 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택하여 Azure AD B2C 테넌트가 포함된 디렉터리를 사용합니다.
2. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
3. **ID 공급자**를 선택한 다음, **추가**를 선택합니다.
4. **이름**을 입력합니다. 예를 들어 *Contoso Azure AD*를 입력합니다.
5. **ID 공급자 유형**을 선택하고 **Open ID Connect(미리 보기)** 를 선택한 다음, **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭합니다.
7. **메타데이터 URL**에 대해 다음 URL을 입력합니다. 여기서 `your-AD-tenant-domain`는 Azure AD 테넌트의 도메인 이름으로 대체됩니다.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    예: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

8. **클라이언트 ID**에 이전에 기록한 애플리케이션 ID를 입력하고, **클라이언트 암호**에 이전에 기록한 키 값을 입력합니다.
9. 선택적으로 **Domain_hint**의 값을 입력합니다. 예: `ContosoAD`. 
10. **확인**을 클릭합니다.
11. **이 ID 공급자의 클레임을 매핑**하고 다음 클레임을 설정하세요.
    
    - **사용자 ID**에 `oid`를 입력합니다.
    - **표시 이름**에 `name`을 입력합니다.
    - **이름**에 `given_name`을 입력합니다.
    - **성**에 `family_name`을 입력합니다.
    - **메일**에 대해 `unique_name`을 입력합니다.

12. **확인**을 클릭한 다음, **만들기**를 클릭하여 구성을 저장합니다.

### <a name="add-the-facebook-identity-provider"></a>Facebook ID 공급자 추가

1. **ID 공급자**를 선택한 다음, **추가**를 선택합니다.
2. **이름**을 입력합니다. 예를 들어 *Facebook*을 입력합니다.
3. **ID 공급자 형식**을 선택하고 **Facebook**을 선택한 다음, **확인**을 클릭합니다.
4. **이 ID 공급자 설정**을 선택하고 이전에 기록한 앱 ID를 **클라이언트 ID**로 입력합니다. 기록한 앱 비밀을 **클라이언트 암호**로 입력합니다.
5. **확인**, **만들기**를 차례로 클릭하여 Facebook 구성을 저장합니다.

## <a name="update-the-user-flow"></a>사용자 흐름 업데이트

필수 조건의 일부로 완료한 자습서에서 등록 및 로그인을 위한 사용자 흐름(*B2C_1_signupsignin1*)을 만들었습니다. 이 섹션에서는 *B2C_1_signupsignin1* 사용자 흐름에 ID 공급자를 추가합니다.

1. **사용자 흐름(정책)** 을 선택한 다음, *B2C_1_signupsignin1* 사용자 흐름을 선택합니다.
2. **ID 공급자**를 선택한 다음, 추가한 **Facebook** 및 **Contoso Azure AD** ID 공급자를 선택합니다.
3. **저장**을 선택합니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름의 개요 페이지에서 **사용자 흐름 실행**을 선택합니다.
2. **애플리케이션**으로 이전에 등록한 *webapp1*이라는 웹 애플리케이션을 선택합니다. **회신 URL**에는 `https://jwt.ms`가 표시되어야 합니다.
3. **사용자 흐름 실행**을 클릭하고 이전에 추가한 ID 공급자로 로그인합니다.
4. 추가한 다른 ID 공급자에 대해 1-3단계를 반복합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * ID 공급자 애플리케이션 만들기
> * 테넌트에 ID 공급자 추가
> * 사용자 흐름에 ID 공급자 추가

> [!div class="nextstepaction"]
> [Azure Active Directory B2C에서 애플리케이션의 사용자 인터페이스 사용자 지정](tutorial-customize-ui.md)