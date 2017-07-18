---
title: "Azure Logic Apps에 대한 웹 API 및 REST API 배포, 호출 및 인증 | Microsoft Docs"
description: "Azure Logic Apps를 사용하여 시스템 통합을 위한 워크플로에서 웹 API 및 REST API를 배포, 인증 및 호출합니다."
keywords: "웹 API, REST API, 커넥터, 워크플로, 시스템 통합, 인증"
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: 
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 88c62d5ab046d8cf4bce5d23b776e517bb0e1d87
ms.contentlocale: ko-kr
ms.lasthandoff: 06/08/2017

---

# <a name="deploy-call-and-authenticate-custom-apis-as-connectors-for-logic-apps"></a>사용자 지정 앱 API를 논리 앱용 커넥터로 배포, 호출 및 인증

논리 앱 워크플로에서 사용할 동작이나 트리거를 제공하는 [사용자 지정 API를 만든](./logic-apps-create-api-app.md) 후에는 API를 호출하기 전에 먼저 배포해야 합니다. 그리고 논리 앱에서 API를 호출할 수 있지만 최상의 환경을 위해 API 작업 및 매개 변수를 설명하는 [Swagger 메타데이터](http://swagger.io/specification/)를 추가합니다. 이 Swagger 파일을 사용하면 API가 더 잘 작동하고 논리 앱과 더 쉽게 통합될 수 있습니다.

API를 [웹앱](../app-service-web/app-service-web-overview.md)으로 배포할 수 있지만 [API 앱](../app-service-api/app-service-api-apps-why-best-platform.md)으로 배포하는 것이 좋습니다. 이렇게 하면 클라우드에서 API를 빌드, 호스팅 및 사용할 때 작업을 더 쉽게 수행할 수 있습니다. API에서 코드를 변경할 필요가 없이 API 앱에 코드를 배포하기만 하면 됩니다. API 호스팅을 위한 가장 쉽고, 확장성 있는, 최상의 방법 중 하나를 제공하는 PaaS(Platform-as-a-Service) 제품인 [Azure App Service](../app-service/app-service-value-prop-what-is.md)에서 API를 호스팅할 수 있습니다.

논리 앱에서 API로의 호출을 인증하려면 코드를 업데이트할 필요가 없이 Azure Portal에서 Azure Active Directory를 설정하면 됩니다. 또는 API 코드를 통해 인증을 요구하고 적용할 수 있습니다.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>API를 웹앱 또는 API 앱으로 배포

논리 앱에서 사용자 지정 API를 호출하려면 먼저 API를 웹앱 또는 API 앱으로 Azure App Service에 배포해야 합니다. 또한 Logic App Designer에서 Swagger 문서를 읽을 수 있게 하려면 API 정의 속성을 설정하고 웹앱 또는 API 앱에 대해 [CORS(원본 간 리소스 공유)](../app-service-api/app-service-api-cors-consume-javascript.md#corsconfig)를 사용하도록 설정합니다.

1. Azure Portal에서 웹앱 또는 API 앱을 선택합니다.

2. 열린 블레이드의 **API** 아래에서 **API 정의**을 선택합니다. **API 정의 위치**를 swagger.json 파일의 URL로 설정합니다.

   일반적으로 URL은 `https://{name}.azurewebsites.net/swagger/docs/v1)` 형식으로 표시됩니다.

   ![사용자 지정 API를 위한 Swagger 파일에 연결](media/logic-apps-custom-hosted-api/custom-api-swagger-url.png)

3. **API** 아래에서 **CORS**를 선택합니다. **허용된 원본**에 대한 CORS 정책을 **'*'**(모두 허용)로 설정합니다.

   이 설정은 Logic App Designer의 요청을 허용합니다.

   ![Logic App Designer에서 사용자 지정 API로의 요청 허용](media/logic-apps-custom-hosted-api/custom-api-cors.png)

자세한 내용은 다음 문서를 참조하세요.

* [ASP.NET 웹 API에 대한 Swagger 메타데이터 추가](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)
* [Azure App Service에 ASP.NET 웹 API 배포](../app-service-api/app-service-api-dotnet-get-started.md)

## <a name="call-your-custom-api-from-logic-app-workflows"></a>논리 앱 워크플로에서 사용자 지정 API 호출

API 정의 속성과 CORS를 설정한 후에는 사용자 지정 API의 트리거 및 동작은 논리 앱 워크플로에 포함할 수 있습니다. 

*  Swagger URL이 있는 웹 사이트를 보려면 Logic Apps Designer에서 구독 웹 사이트를 찾아보면 됩니다.

*  Swagger 문서를 가리켜 사용할 수 있는 동작과 입력을 보려면 [HTTP + Swagger 동작](../connectors/connectors-native-http-swagger.md)을 사용합니다.

*  Swagger 문서가 없거나 공개되지 않는 API를 포함하여 모든 API를 호출하려면 언제든지 [HTTP 동작](../connectors/connectors-native-http.md)을 사용하여 요청을 만들면 됩니다.

## <a name="authenticate-calls-to-your-custom-api"></a>사용자 지정 API에 대한 호출 인증

다음과 같은 방법으로 사용자 지정 API에 대한 호출을 보호할 수 있습니다.

* [코드 변경 없음](#no-code) - Azure Portal을 통해 [Azure AD(Azure Active Directory)](../active-directory/active-directory-whatis.md)로 API를 보호하므로 코드를 업데이트하거나 API를 다시 배포할 필요가 없습니다.

  > [!NOTE]
  > 기본적으로 Azure Portal에서 설정하는 Azure AD 인증은 세분화된 권한 부여를 수행하지 않습니다. 예를 들어 이 인증을 통해 특정 사용자 또는 앱이 아니라 특정 테넌트에 대한 API를 잠급니다. 

* [API 코드 업데이트](#update-code) - 코드를 통해 [인증서 인증](#certificate), [기본 인증](#basic) 또는 [Azure AD 인증](#azure-ad-code)을 적용하여 API를 보호합니다.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>코드를 변경하지 않고 API에 대한 호출 인증

이 방법의 일반적인 단계는 다음과 같습니다.

1. 두 개의 [Azure AD(Azure Active Directory) 응용 프로그램 ID](../app-service-api/app-service-api-dotnet-service-principal-auth.md)를 만듭니다. 하나는 논리 앱용이고 다른 하나는 웹앱용(또는 API 앱용)입니다.

2. API에 대한 호출을 인증하려면 논리 앱의 Azure AD 응용 프로그램 ID와 연결된 [서비스 주체](../app-service-api/app-service-api-dotnet-service-principal-auth.md)의 자격 증명(클라이언트 ID 및 비밀)을 사용합니다.

3. 논리 앱 정의에 응용 프로그램 ID를 포함합니다.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>1부: 논리 앱의 Azure AD 응용 프로그램 ID 만들기

논리 앱은 이 Azure AD 응용 프로그램 ID를 사용하여 Azure AD에 대해 인증합니다. 디렉터리에 대해 이 ID를 한 번만 설정하면 됩니다. 예를 들어 논리 앱마다 고유한 ID를 만들 수 있더라도 모든 논리 앱에 대해 동일한 ID를 사용하도록 선택할 수 있습니다. Azure Portal, [Azure 클래식 포털](#app-identity-logic-classic) 또는 [PowerShell](#powershell)을 사용하여 이러한 ID를 설정할 수 있습니다.

**Azure Portal에서 논리 앱에 대한 응용 프로그램 ID 만들기**

1. [Azure Portal](https://portal.azure.com "https://portal.azure.com")에서 **Azure Active Directory**를 선택합니다. 

2. 웹앱 또는 API 앱과 동일한 디렉터리에 있는지 확인합니다.

   > [!TIP]
   > 디렉터리를 전환하려면 프로필을 클릭하고 다른 디렉터리를 선택합니다. 또는 **개요** > **디렉터리 전환**을 차례로 선택합니다.

3. 디렉터리 메뉴의 **관리** 아래에서 **앱 등록** > **새 응용 프로그램 등록**을 차례로 선택합니다.

   > [!TIP]
   > 기본적으로 앱 등록 목록에는 디렉터리의 모든 앱 등록이 표시됩니다. 앱 등록만 보려면 검색 상자 옆에 있는 **내 앱**을 선택합니다. 

   ![새 앱 등록 만들기](./media/logic-apps-custom-hosted-api/new-app-registration-azure-portal.png)

4. 응용 프로그램 ID의 이름을 지정하고, **응용 프로그램 종류**를 **웹앱/API**로 설정하며, **로그인 URL**의 도메인으로 형식이 지정된 고유 문자열을 제공하고, **만들기**를 선택합니다.

   ![응용 프로그램 ID의 이름 및 로그인 URL 제공](./media/logic-apps-custom-hosted-api/logic-app-identity-azure-portal.png)

   논리 앱에 대해 만든 응용 프로그램 ID가 이제 앱 등록 목록에 표시됩니다.

   ![논리 앱의 응용 프로그램 ID](./media/logic-apps-custom-hosted-api/logic-app-identity-created.png)

5. 앱 등록 목록에서 새 응용 프로그램 ID를 선택합니다. 3부에서 논리 앱에 대한 "클라이언트 ID"로 사용할 **응용 프로그램 ID**를 복사하여 저장합니다.

   ![논리 앱의 응용 프로그램 ID 복사 및 저장](./media/logic-apps-custom-hosted-api/logic-app-application-id.png)

6. 응용 프로그램 ID 설정이 표시되지 않으면 **설정** 또는 **모든 설정**을 선택합니다.

7. **API 액세스** 아래에서 **키**를 선택합니다. **설명** 아래에서 키의 이름을 입력합니다. **만료** 아래에서 키의 기간을 선택합니다.

   만드는 키는 논리 앱에 대한 응용 프로그램 ID의 "비밀" 또는 암호 역할을 합니다.

   ![논리 앱 ID의 키 만들기](./media/logic-apps-custom-hosted-api/create-logic-app-identity-key-secret-password.png)

8. 도구 모음에서 **저장**을 선택합니다. 이제 **값** 아래에서 키가 표시됩니다. 
키 블레이드를 떠날 때 키가 숨겨지므로 나중에 사용할 수 있도록 **키를 복사하여 저장해야 합니다**.

   3부에서 논리 앱을 구성할 때 이 키를 "비밀" 또는 암호로 지정합니다.

   ![나중을 위한 키 복사 및 저장](./media/logic-apps-custom-hosted-api/logic-app-copy-key-secret-password.png)

<a name="app-identity-logic-classic"></a>

**Azure 클래식 포털에서 논리 앱의 응용 프로그램 ID 만들기**

1. Azure 클래식 포털에서 [**Active Directory**](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)를 선택합니다.

2. 웹앱 또는 API 앱에 사용하는 것과 동일한 디렉터리를 선택합니다.

3. **응용 프로그램** 탭의 페이지 아래쪽에서 **추가**를 선택합니다.

4. 응용 프로그램 ID의 이름을 지정하고 **다음**(오른쪽 화살표)를 선택합니다.

5. **앱 속성** 아래에서 **로그인 URL** 및 **앱 ID URI**에 대한 도메인으로 형식이 지정된 고유 문자열을 제공하고 **완료**를 선택합니다(확인 표시).

6. **구성** 탭에서 3부에서 사용할 논리 앱의 **클라이언트 ID**를 복사하여 저장합니다.

7. **키** 아래에서 **기간 선택** 목록을 엽니다. 키의 기간을 선택합니다.

   만드는 키는 논리 앱에 대한 응용 프로그램 ID의 "비밀" 또는 암호 역할을 합니다.

8. 페이지 아래쪽에서 **저장**을 선택합니다. 몇 초 정도 기다려야 할 수도 있습니다.

9. **키** 아래에서 현재 표시된 키를 복사하여 저장합니다. 

   3부에서 논리 앱을 구성할 때 이 키를 "비밀" 또는 암호로 지정합니다.

자세한 내용은 [Azure Active Directory 로그인을 사용하도록 App Service 응용 프로그램을 구성하는 방법](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)을 참조하세요.

<a name="powershell"></a>

**PowerShell에서 논리 앱의 응용 프로그램 ID 만들기**

PowerShell과 함께 Azure Resource Manager를 통해 이 작업을 수행할 수 있습니다. PowerShell에서 다음 명령을 실행합니다.

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. 사용한 **테넌트 ID**(Azure AD 테넌트의 GUID), **응용 프로그램 ID** 및 암호를 복사합니다.

자세한 내용은 [PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기](../azure-resource-manager/resource-group-authenticate-service-principal.md)를 참조하세요.

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>2부: 웹앱 또는 API 앱의 Azure AD 응용 프로그램 ID 만들기

웹앱 또는 API 앱이 이미 배포된 경우 Azure Portal에서 인증을 설정하고 응용 프로그램 ID를 만들 수 있습니다. 그렇지 않으면 [Azure Resource Manager 템플릿으로 배포할 때 인증을 설정](#authen-deploy)할 수 있습니다. 

**Azure Portal에서 배포된 앱의 응용 프로그램 ID 만들기 및 인증 설정**

1. [Azure Portal](https://portal.azure.com "https://portal.azure.com")에서 웹앱 또는 API 앱을 찾아 선택합니다. 

2. **설정**에서 **인증/권한 부여**를 선택합니다. **App Service 인증** 아래에서 인증을 **설정**으로 지정합니다. **인증 공급자** 아래에서 **Azure Active Directory**를 선택합니다.

   ![인증 설정](./media/logic-apps-custom-hosted-api/custom-web-api-app-authentication.png)

3. 이제 다음과 같이 웹앱 또는 API 앱의 응용 프로그램 ID를 만듭니다. **Azure Active Directory 설정** 블레이드에서 **관리 모드**를 **기본**으로 설정합니다. **새 AD 앱 만들기**를 선택합니다. 응용 프로그램 ID의 이름을 지정하고 **확인**을 선택합니다. 

   ![웹앱 또는 API 앱의 응용 프로그램 ID 만들기](./media/logic-apps-custom-hosted-api/custom-api-application-identity.png)

4. **인증/권한 부여** 블레이드에서 **저장**을 선택합니다.

이제 웹앱 또는 API 앱과 연결된 응용 프로그램 ID에 대한 클라이언트 ID 및 테넌트 ID를 찾아야 합니다. 이러한 ID는 3부에서 사용됩니다. 따라서 Azure Portal 또는 [Azure 클래식 포털](#find-id-classic)에 대해 이러한 단계를 계속합니다.

**Azure Portal에서 웹앱 또는 API 앱에 대한 응용 프로그램 ID의 클라이언트 ID 및 테넌트 ID 찾기**

1. **인증 공급자** 아래에서 **Azure Active Directory**를 선택합니다. 

   !["Azure Active Directory"를 선택합니다.](./media/logic-apps-custom-hosted-api/custom-api-app-identity-client-id-tenant-id.png)

2. **Azure Active Directory 설정** 블레이드에서 **관리 모드**를 **고급**으로 설정합니다.

3. **클라이언트 ID**를 복사하고 3부에서 사용할 해당 GUID를 저장합니다.

   > [!TIP] 
   > **클라이언트 ID** 및 **발급자 URL**이 표시되지 않으면 Azure Portal을 새로 고치고 1단계를 반복합니다.

4. **발급자 URL** 아래에서 3부에서 사용할 GUID만 복사하여 저장합니다. 필요한 경우 웹앱 또는 API 앱의 배포 템플릿에서 이 GUID를 사용할 수도 있습니다.

   이 GUID는 특정 테넌트의 GUID("테넌트 ID")이며 `https://sts.windows.net/{GUID}` URL에 표시되어야 합니다.

5. 변경 내용을 저장하지 않고 **Azure Active Directory 설정** 블레이드를 닫습니다.

<a name="find-id-classic"></a>

**Azure 클래식 포털에서 웹앱 또는 API 앱에 대한 응용 프로그램 ID의 클라이언트 ID 및 테넌트 ID 찾기**

1. Azure 클래식 포털에서 [**Active Directory**](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)를 선택합니다.

2.  웹앱 또는 API 앱에 사용할 디렉터리를 선택합니다.

3. **검색** 상자에서 웹앱 또는 API 앱의 응용 프로그램 ID를 찾아 선택합니다.

4. **구성** 탭에서 **클라이언트 ID**를 복사하고 3부에서 사용할 해당 GUID를 저장합니다.

5. 클라이언트 ID를 가져온 후 **구성** 탭 아래쪽에서 **끝점 보기**를 선택합니다.

6. **페더레이션 메타데이터 문서**의 URL을 복사하고 해당 URL을 찾습니다.

7. 열린 메타데이터 문서에서 `https://sts.windows.net/{GUID}` 형식의 **entityID** 특성이 있는 루트 **EntityDescriptor ID** 요소를 찾습니다. 

      이 특성의 GUID는 특정 테넌트의 GUID(테넌트 ID)입니다.

8. 테넌트 ID를 복사하고, 3부에서 사용할 뿐만 아니라 필요한 경우 웹앱 또는 API 앱의 배포 템플릿에서도 사용할 해당 ID를 저장합니다.

자세한 내용은 다음 항목을 참조하세요.

* [Azure App Service의 API 앱에 대한 사용자 인증](../app-service-api/app-service-api-dotnet-user-principal-auth.md)
* [Azure 앱 서비스의 인증 및 권한 부여](../app-service/app-service-authentication-overview.md)

<a name="authen-deploy"></a>

**Azure Resource Manager 템플릿으로 배포할 때 인증 설정**

논리 앱의 앱 ID와 다른 웹앱 또는 API 앱의 Azure AD 응용 프로그램 ID도 만들어야 합니다. 응용 프로그램 ID를 만들려면 Azure Portal에 대해 2부의 이전 단계를 수행합니다. 1부의 단계를 수행할 수도 있지만 **로그인 URL** 및 **앱 ID URI**에 대한 웹앱 또는 API 앱의 실제 `https://{URL}`을 사용해야 합니다. 이러한 단계에서 앱의 배포 템플릿 및 3부에서 사용할 클라이언트 ID와 테넌트 ID를 모두 저장해야 합니다.

> [!NOTE]
> 웹앱 또는 API 앱의 Azure AD 응용 프로그램 ID를 만들 때는 PowerShell 대신 Azure Portal 또는 Azure 클래식 포털을 사용해야 합니다. PowerShell commandlet은 웹 사이트에 사용자가 로그인하는 데 필요한 권한을 설정하지 않습니다.

클라이언트 ID와 테넌트 ID를 가져온 후에는 이러한 ID를 웹앱 또는 API 앱의 하위 리소스로 배포 템플릿에 포함합니다.

   ```
   "resources": [
       {
           "apiVersion": "2015-08-01",
           "name": "web",
           "type": "config",
           "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
           "properties": {
               "siteAuthEnabled": true,
               "siteAuthSettings": {
                 "clientId": "{client-ID}",
                 "issuer": "https://sts.windows.net/{tenant-ID}/",
               }
           }
       }
   ]
   ```

Azure Active Directory 인증과 함께 빈 웹앱과 논리 앱을 자동으로 배포하려면 [여기에서 전체 템플릿을 보거나](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json) 아래의 **Azure에 배포**를 클릭합니다.

[![Azure에 배포](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>3부: 논리 앱의 권한 부여 섹션 채우기

이전 템플릿에는 이 권한 부여 섹션이 설정되어 있지만, 논리 앱을 직접 작성하는 경우 전체 권한 부여 섹션을 포함해야 합니다.

코드 보기에서 논리 앱 정의를 열고 **HTTP** 동작 섹션으로 이동하여 **권한 부여** 섹션을 찾아 다음 줄을 포함합니다.

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| 요소 | 설명 |
| ------- | ----------- |
| tenant |Azure AD 테넌트의 GUID |
| audience |필수입니다. 액세스하려는 대상 리소스의 GUID, 즉 웹앱 또는 API 앱에 대한 응용 프로그램 ID의 클라이언트 ID |
| clientId |액세스를 요청하는 클라이언트의 GUID, 즉 논리 앱에 대한 응용 프로그램 ID의 클라이언트 ID |
| secret |필수입니다. 액세스 토큰을 요청하는 클라이언트에 대한 응용 프로그램 ID의 키 또는 암호 |
| type |인증 유형입니다. ActiveDirectoryOAuth 인증의 경우 이 값은 `ActiveDirectoryOAuth`입니다. |

예:

```
{
   ...
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>코드를 통한 API 호출 보호

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>인증서 인증

논리 앱에서 웹앱 또는 API 앱으로 들어오는 요청의 유효성을 검사하기 위해 클라이언트 인증서를 사용할 수 있습니다. 코드를 설정하려면 [TLS 상호 인증을 구성하는 방법](../app-service-web/app-service-web-configure-tls-mutual-auth.md)을 참조하세요.

**권한 부여** 섹션에서 다음 줄을 포함합니다. 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| 요소 | 설명 |
| ------- | ----------- |
| type |필수입니다. 인증 유형입니다. SSL 클라이언트 인증서의 경우 이 값은 `ClientCertificate`여야 합니다. |
| password |필수입니다. 클라이언트 인증서(PFX 파일)에 액세스하기 위한 암호 |
| pfx |필수입니다. 클라이언트 인증서(PFX 파일)의 Base64로 인코딩된 콘텐츠 |

<a name="basic"></a>

#### <a name="basic-authentication"></a>기본 인증

논리 앱에서 웹앱 또는 API 앱으로 들어오는 요청의 유효성을 검사하기 위해 사용자 이름 및 암호와 같은 기본 인증을 사용할 수 있습니다. 기본 인증은 일반적인 패턴이며, 웹앱 또는 API 앱을 빌드하는 데 사용되는 언어에서 이 인증을 사용할 수 있습니다.

**권한 부여** 섹션에서 다음 줄을 포함합니다.

`{"type": "basic", "username": "username", "password": "password"}`.

| 요소 | 설명 |
| --- | --- |
| type |필수입니다. 인증 유형입니다. 기본 인증의 경우 값은 `Basic`이어야 합니다. |
| username |필수입니다. 인증을 위한 사용자 이름입니다. |
| password |필수입니다. 인증을 위한 암호입니다. |

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>코드를 통한 Azure Active Directory 인증

기본적으로 Azure Portal에서 설정하는 Azure AD 인증은 세분화된 권한 부여를 수행하지 않습니다. 예를 들어 이 인증을 통해 특정 사용자 또는 앱이 아니라 특정 테넌트에 대한 API를 잠급니다. 

코드를 통해 논리 앱에 대한 API 액세스를 제한하려면 JWT(JSON Web Token)가 포함된 헤더를 추출합니다. 호출자의 ID를 확인하고 일치하지 않는 요청을 거부합니다.

Azure Portal을 사용하지 않고 자신의 코드에서 이 인증을 완전히 구현하려면 [Azure 앱에서 온-프레미스 Active Directory를 사용하여 인증](../app-service-web/web-sites-authentication-authorization.md)하는 방법을 참조하세요.

논리 앱의 응용 프로그램 ID를 만들고 해당 ID를 사용하여 API를 호출하려면 이전 단계를 따라야 합니다.

## <a name="next-steps"></a>다음 단계

* [진단 로그 및 경고를 사용하여 논리 앱 성능 확인](logic-apps-monitor-your-logic-apps.md)
