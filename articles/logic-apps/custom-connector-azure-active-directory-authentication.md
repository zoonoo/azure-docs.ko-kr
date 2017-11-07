---
title: "Azure AD로 사용자 정의 커넥터 보호 - Azure Logic Apps | Microsoft Docs"
description: "Azure AD(Azure Active Directory)를 사용하여 API 및 커넥터에 보안 및 인증 추가"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: bcee842cb880002daaa3ae9d9110ee1734941b6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>Azure AD(Azure Active Directory)를 사용하여 API 및 커넥터에 보안 추가

API와 사용자 지정 커넥터간의 호출을 보호하려면 Azure AD 인증을 Web API와 커넥터에 추가합니다. 이 시나리오에서는 두 개의 Azure AD(Azure Active Directory) 앱을 만듭니다. 한 가지 Azure AD 앱은 Web API를 보호하고 다른 Azure AD 앱은 커넥터 등록을 보호하고 위임된 액세스를 추가합니다. 

이 자습서는 Azure Resource Manager API를 예로 들어 설명합니다. Azure Resource Manager는 데이터베이스, 가상 컴퓨터 및 웹앱과 같이 Azure에 구축한 솔루션의 구성 요소를 관리하는 데 도움이 됩니다. Azure Resource Manager의 사용자 지정 커넥터는 워크플로에서 Azure 리소스를 관리하려는 경우에 유용할 수 있습니다. 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없는 경우 [무료 Azure 계정](https://azure.microsoft.com/free/)으로 시작할 수 있습니다. 아니면 [종량제 구독](https://azure.microsoft.com/pricing/purchase-options/)에 등록하세요.

* 이 자습서는 [Azure Resource Manager용 샘플 OpenAPI 파일](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)

  > [!NOTE] 
  > 샘플 OpenAPI 파일은 모든 Azure Resource Manager 작업을 정의하지는 않으며 현재 [모든 구독 나열](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List)의 작업만 포함합니다. [온라인 OpenAPI 편집기](http://editor.swagger.io/)를 사용하여 OpenAPI를 편집하거나 다른 OpenAPI 파일을 만들 수 있습니다.
  >
  > 이 자습서는 Azure AD 인증을 사용하려는 RESTful API에 적용할 수 있습니다.

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1. Web API 보안을 위한 첫 번째 Azure AD 앱 만들기

첫 번째 Azure AD 앱은 사용자 지정 커넥터가 API(이 예제의 경우 Azure Resource Manager API)를 호출할 때 인증을 수행합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다. Azure Active Directory 테넌트가 둘 이상인 경우 사용자 이름 아래 디렉터리를 확인하여 올바른 디렉터리에 로그인했는지 확인합니다. 

   ![디렉터리 확인](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > 디렉터리를 변경하려면 사용자 이름을 선택한 다음 원하는 디렉터리를 선택합니다.

2. 현재 디렉터리를 볼 수 있도록 기본 Azure 메뉴에서 **Azure Active Directory**를 선택합니다.

   !["Azure Active Directory"를 선택합니다.](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > Azure 기본 메뉴에 **Azure Active Directory**가 표시되지 않으면 **More services**를 선택합니다. **필터** 상자에 "Azure Active Directory"를 필터로 입력한 다음 **Azure Active Directory**를 선택합니다.

3. 디렉터리 메뉴의 **관리** 아래에서 **앱 등록**을 선택합니다. 등록된 앱 목록에서 **+ 새 응용 프로그램 등록**을 선택합니다.

   !["앱 등록", "+ 새 응용 프로그램 등록"을 선택합니다.](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. **만들기**에서 테이블의 설명대로 Azure AD 앱의 세부 정보를 제공한 다음 **만들기**를 선택합니다. 

   ![Azure AD 앱 만들기](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **Name** | *web-api-app-name* | Web API의 Azure AD 앱의 이름 | 
   | **응용 프로그램 유형** | **웹앱/API** | 앱 유형 | 
   | **로그온 URL** | `https://login.windows.net` | | 
   |||| 

5. 디렉터리의 **앱 등록** 목록으로 돌아오면 Azure AD 앱을 선택합니다.

   ![목록에서 Azure AD 앱을 선택합니다.](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. 앱의 세부 정보 페이지가 나타나면 **앱의 *응용 프로그램 ID*를 안전한 곳에 복사하고 저장**해야 합니다. 이 ID는 나중에 사용하기 위해 필요합니다.

   ![나중에 사용할 수 있도록 "응용 프로그램 ID" 저장](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. 이제 Azure AD 앱에 대한 회신 URL을 제공합니다. 앱의 **설정** 메뉴에서 **회신 URL**을 선택합니다. 이 URL을 입력한 다음 **저장**을 선택합니다.

   ![회신 URL](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **회신 URL** | 자신의 API인 경우 다음 URL을 입력합니다. </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **위임된 권한** | {not necessary} | | 
   | **클라이언트 키** | {not necessary} | | 
   |||| 

   > [!TIP]
   > **설정** 메뉴가 이전에 표시되지 않은 경우 여기에서 **설정**을 선택합니다.
   >
   > !["설정" 선택](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2. 사용자 지정 커넥터용 두 번째 Azure AD 앱 만들기

두 번째 Azure AD 앱은 사용자 지정 커넥터 등록을 보호하고 첫 번째 Azure AD 앱으로 보호되는 Web API에 위임된 액세스를 추가합니다. 

> [!IMPORTANT]
> 두 Azure AD 앱이 모두 같은 디렉터리에 있는지 확인합니다.

1. **앱 등록** 목록으로 돌아가서 **+ 새 응용 프로그램 등록**을 다시 선택합니다.

   !["앱 등록", "+ 새 응용 프로그램 등록"을 선택합니다.](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. **만들기**에 테이블의 설명대로 두 번째 Azure AD 앱에 대한 세부 정보를 제공한 다음 **만들기**를 선택합니다. 

   ![Azure AD 앱 만들기](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | 설정 | 제안 값 | 설명 | 
   | ------- | --------------- | ----------- | 
   | **Name** | *your-connector-name* | 커넥터의 Azure AD 앱 이름 | 
   | **응용 프로그램 유형** | **웹앱/API** | 앱 유형 | 
   | **로그온 URL** | `https://login.windows.net` | | 
   |||| 

3. 디렉터리의 **앱 등록** 목록으로 돌아오면 두 번째 Azure AD 앱을 선택합니다.

   ![목록에서 두 번째 Azure AD 앱을 선택합니다.](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. 앱의 세부 정보 페이지가 나타나면 **이 앱의 *응용 프로그램 ID*도 안전한 곳에 복사하고 저장**해야 합니다. 이 ID는 나중에 사용하기 위해 필요합니다.

   ![나중에 사용할 수 있도록 "응용 프로그램 ID" 저장](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. 이제 Azure AD 앱에 대한 회신 URL을 제공합니다. 앱의 **설정** 메뉴에서 **회신 URL**을 선택합니다. 이 URL을 입력한 다음 **저장**을 선택합니다.

   | 설정 | 제안 값 | 
   | ------- | --------------- | 
   | **회신 URL** | Azure Resource Manager 사용자 지정 커넥터에 대해 다음 URL을 입력합니다. `https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > **설정** 메뉴가 이전에 표시되지 않은 경우 여기에서 **설정**을 선택합니다.
   >
   > !["설정" 선택](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. **설정** 메뉴로 돌아와서 **필수 권한** > **추가**를 선택합니다.

   ![필수 권한 > 추가](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. **API 액세스 추가** 메뉴가 열리면 **Select an API** > 
**Windows Azure Service Management API** > **선택**을 선택합니다.

   ![API 선택](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. **API 액세스 추가** 메뉴에서 **권한 선택**을 선택합니다. **위임된 권한**에서 **Azure 서비스 관리에 조직 사용자로 액세스** > **선택**을 선택합니다.

   !["위임된 권한" > "Azure 서비스 관리에 조직 사용자로 액세스" 선택](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   다른 옵션:

   | 옵션 | 제안 값 | 설명 | 
   | ------ | --------------- | ----------- | 
   | **위임된 권한** | | Web API에 대한 위임된 액세스 권한 선택 | 
   |||| 

9. **API 액세스 추가** 메뉴에서 **완료**를 선택합니다.

   !["API 액세스 추가" 메뉴 > "완료"](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. 이제 커넥터의 Azure AD 앱에 대해 *클라이언트 키* 또는 "비밀"을 생성합니다. 

    1. **설정** 메뉴로 돌아가서 **키**를 선택합니다. 
    키 이름을 16자 이하로 입력하고 만료 기간을 선택한 다음 **저장**을 선택합니다.

       ![클라이언트 키 만들기](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. 생성한 키가 나타나면 **키** 페이지를 나가지 전에 **이 키를 복사하여 안전한 곳에 저장**합니다.
    
       ![수동으로 키 복사 및 저장](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. 키를 저장한 후 **설정** 메뉴를 안전하게 닫을 수 있습니다.

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>3. Azure AD 인증을 Web API 앱에 추가

이제 첫 번째 Azure AD 앱을 사용하여 Web API에 대한 인증을 켭니다. 자세한 내용은 [Azure Active Directory 로그인을 사용하도록 App Service 응용 프로그램을 구성하는 방법](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 [Web API에서 사용자 지정 커넥터 만들기](../logic-apps/custom-connector-build-web-api-app-tutorial.md)에서 이전에 게시한 Web API 앱을 찾습니다.

2. **설정**에서 **인증/권한 부여**를 선택합니다. 

   1. **App Service 인증**에서 **켜기**를 선택합니다.
   2. **요청이 인증되지 않은 경우 수행할 작업**에서 **Azure Active Directory로 로그인**을 선택합니다.
   3. **인증 공급자**에서 **Azure Active Directory**를 선택합니다. 

   !["인증/권한 부여" 선택](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. **Azure Active Directory 설정** 페이지에서:

   1. **관리 모드**에 **기본**을 선택합니다.

   2. 이제 Web API 앱이 인증에 사용하는 Azure AD 앱을 선택합니다. 
   **기존 AD 앱 선택** > **Azure AD 앱**을 선택합니다.

   3. **Azure AD 응용 프로그램**에서 Web API 앱에 대해 만들어 놓은 Azure AD 앱을 선택합니다. 
   
   4. **인증/권한 부여** 페이지로 돌아갈 때가지 **확인**을 클릭합니다.

   예:

   ![Web API 앱을 나타내는 Azure AD 앱 선택](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. **인증/권한 부여** 페이지에서 **저장**을 선택합니다.

   ![인증 설정 저장](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   이제 Web API 앱에서 Azure AD를 인증에 사용할 수 있습니다.

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4. Web API의 OpenAPI 파일에서 Azure AD 인증 설정

`host` 속성 아래 `securityDefintions` 개체와 Azure AD 인증을 추가할 수 있도록 Web API 앱에 대한 OpenAPI 파일을 엽니다. 다음은 `host` 속성과 `securityDefinitions` 개체를 보여주는 예입니다.

``` json
// Your OpenAPI file header appears here...

"host": "{your-web-api-app-root-url}",
"schemes": [
    "https" // Make sure this is https!
],
"securityDefinitions": {
    "AAD": {
        "type": "oauth2",
        "flow": "accessCode",
        "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
        "tokenUrl": "https://login.windows.net/common/oauth2/token",
        "scopes": {}
    }
},

// Your OpenAPI document continues here...
```
이제 사용자 지정 커넥터를 만들고 등록할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

* [커넥터 등록](../logic-apps/logic-apps-custom-connector-register.md)
* [사용자 지정 커넥터 FAQ](../logic-apps/custom-connector-faq.md)
