---
title: "Azure Logic Apps에서 사용자 지정 API 호출 | Microsoft Docs"
description: "Azure Logic Apps를 사용하여 Azure App Service에 호스트된 고유한 사용자 지정 API 호출"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: fc509ef8b30fadb6e026f346d4adbd6ef759624a
ms.openlocfilehash: 74aae9f757f56e94b583069a1fdee7efaafe467c
ms.lasthandoff: 02/16/2017


---
# <a name="call-custom-apis-hosted-on-azure-app-service-with-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Azure App Service에 호스트된 사용자 지정 API 호출

Azure Logic Apps에는 다양한 서비스에 대해 40개가 넘는 커넥터가 있으나 고유의 코드를 실행할 수 있는 고유의 사용자 지정 API를 호출하려 할 수 있습니다. Azure App Service는 고유의 사용자 지정 Web API를 호스트하는 가장 쉽고 확장성이 뛰어난 방법 중 하나를 제공합니다. 이 문서에서는 앱 서비스 API 앱, 웹앱 또는 모바일 앱에서 호스트되는 Web API를 호출하는 방법을 설명합니다.
[Logic Apps에서 API를 트리거 또는 작업으로 빌드하는 방법](../logic-apps/logic-apps-create-api-app.md)을 알아보세요.

## <a name="deploy-your-web-app"></a>웹앱 배포

우선 Azure App Service에서 API를 웹앱으로 배포해야 합니다. [ASP.NET 웹앱을 만들 때의 기본 배포](../app-service-web/web-sites-dotnet-get-started.md)에 대해 자세히 알아보세요. 논리 앱에서 API를 호출할 수 있지만 최상의 경험을 위해 논리 앱 작업과 쉽게 통합하도록 Swagger 메타데이터를 추가하는 것이 좋습니다. [Swagger 메타데이터 추가](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)에 대해 자세히 알아보세요.

### <a name="api-settings"></a>API 설정

논리 앱 디자이너에서 Swagger를 구문 분석하려면 CORS를 사용하도록 설정하고 웹앱에 대한 API 정의 속성을 설정해야 합니다. 

1.    Azure Portal에서 웹앱을 선택합니다.
2.    열리는 블레이드에서 **API**를 찾은 후 **API 정의**를 선택합니다. **API 정의 위치**를 swagger.json 파일의 URL로 설정합니다.

    일반적으로 이 URL은 https://{name}.azurewebsites.net/swagger/docs/v1)입니다.

3.    논리 앱 디자이너의 요청을 허용하려면 **API**에서 **CORS**를 선택하고 '*'에 대한 CORS 정책을 설정합니다.

## <a name="call-into-your-custom-api"></a>사용자 지정 API 호출

CORS 및 API 정의 속성을 설정한 경우 논리 앱 포털에서 워크플로에 사용자 지정 API 작업을 쉽게 추가할 수 있어야 합니다. Logic Apps 디자이너에서 구독 웹 사이트를 탐색하여 Swagger URL이 정의된 웹 사이트를 나열할 수 있습니다. 또한 HTTP + Swagger 작업을 사용하여 Swagger를 가리키고 사용 가능한 작업 및 입력을 나열할 수 있습니다. 마지막으로 언제나 HTTP 작업을 사용하여 Swagger 문서가 없거나 이 문서를 노출하지 않는 API를 막론하고 어떤 API도 호출할 수 있는 요청을 만들 수 있습니다.

API를 보호하려면 다음과 같은 몇 가지 다른 방법을 사용할 수 있습니다.

*    코드 변경이 필요 없습니다. Azure Active Directory를 사용하여 코드 변경 또는 다시 배포 없이 API를 보호할 수 있습니다.
*    API 코드에서 기본 인증, Azure Active Directory 인증 또는 인증서 인증을 적용합니다.

## <a name="secure-calls-to-your-api-without-changing-code"></a>코드를 변경하지 않고 API에 대한 호출 보안 유지

이 섹션에서는 하나의 논리 앱 및 하나의 웹앱, 총 두 가지 Azure Active Directory 응용 프로그램을 만듭니다. 논리 앱의 Azure Active Directory 앱과 연결된 서비스 주체(클라이언트 ID 및 암호)를 사용하여 웹앱 호출을 인증합니다. 마지막으로 논리 앱 정의에 응용 프로그램 ID를 포함합니다.

### <a name="part-1-set-up-an-application-identity-for-your-logic-app"></a>1부: 논리 앱용 응용 프로그램 ID 설치

논리 앱은 이 응용 프로그램 ID를 사용하여 Azure Active Directory에서 인증을 받습니다. 디렉터리에 대해 이 ID를 한 번만 설정하면 됩니다. 예를 들어 모든 논리 앱에 대해 같은 ID를 사용하도록 선택할 수 있지만 논리 앱마다 고유한 ID를 만들 수도 있습니다. Azure Portal에서 또는 PowerShell 사용하여 이러한 ID를 설정할 수 있습니다.

#### <a name="create-the-application-identity-in-the-azure-classic-portal"></a>Azure 클래식 포털에서 응용 프로그램 ID 만들기

1. Azure 클래식 포털에서 [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)로 이동합니다. 
2. 웹앱에 사용할 디렉터리를 선택합니다.
3. **응용 프로그램** 탭을 선택합니다. 페이지 아래쪽의 명령 모음에서 **추가**를 클릭합니다.
5. 앱 ID에 이름을 지정하고 다음 화살표를 클릭합니다.
6. **앱 속성**에서 도메인으로 형식이 지정된 고유 문자열에 넣고 확인 표시를 클릭합니다.
7. **구성** 탭을 선택합니다. **클라이언트 ID**로 이동한 후 논리 앱에서 사용하기 위해 클라이언트 ID를 복사합니다.
8. **키** 아래에서 **기간 선택** 목록을 열고 키에 대한 지속 시간을 선택합니다.
9. 페이지 맨 아래에 있는 **저장**을 클릭합니다. 몇 초 정도 기다려야 할 수도 있습니다.
10. 논리 앱에서 사용할 수 있으므로 이제 **키** 아래에 표시되는 키를 복사해야 합니다.

#### <a name="create-the-application-identity-using-powershell"></a>PowerShell을 사용하여 응용 프로그램 ID 만들기

1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. **테넌트 ID**, **응용 프로그램 ID** 및 사용한 암호를 복사합니다.

### <a name="part-2-protect-your-web-app-with-an-azure-active-directory-app-identity"></a>2부: Azure Active Directory 앱 ID로 웹앱 보호

웹앱이 이미 배포된 경우 Azure Portal에서 권한 부여를 사용하도록 설정할 수 있습니다. 그렇지 않은 경우 Azure Resource Manager 배포의 권한 부여 일부를 사용할 수 있습니다.

#### <a name="enable-authorization-in-the-azure-portal"></a>Azure Portal에서 권한 부여 사용

1. 웹앱을 찾아서 선택합니다. **설정**에서 **인증/권한 부여**를 선택합니다.
2. **App Service 인증**에서 인증 을 **설정**으로 지정하고 **저장**을 선택합니다.

이 시점에서 응용 프로그램은 사용자에 대해 자동으로 만들어집니다. 이 응용 프로그램의 클라이언트 ID는 3부에 필요하므로 다음 단계를 수행해야 합니다.

1. Azure 클래식 포털에서 [Azure Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)로 이동합니다.
2.    디렉터리를 선택합니다.
3. 검색 상자에서 앱을 찾습니다.
4. 목록에서 앱을 선택합니다.
5. **구성** 탭을 선택하여 **클라이언트 ID**를 표시합니다.

#### <a name="deploy-your-web-app-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 웹앱 배포

먼저 논리 앱에 사용되는 응용 프로그램과는 다른 응용 프로그램을 웹앱에 대해 만들어야 합니다. 1부의 이전 단계를 진행하지만 **HomePage** 및 **IdentifierUris**의 경우 웹앱의 실제 https://**URL**을 사용합니다.

> [!NOTE]
> 웹앱에 대한 응용 프로그램을 만들 때 [Azure 클래식 포털](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)을 사용해야 합니다. PowerShell commandlet은 웹 사이트에 사용자가 로그인하는 데 필요한 권한을 설정하지 않습니다.

클라이언트 ID 및 테넌트 ID가 있으면 배포 템플릿에서 웹앱의 하위 리소스로 포함합니다.

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
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Azure Active Directory를 사용하는 빈 웹앱 및 논리 앱을 함께 배포하는 배포를 자동으로 실행하려면 **Azure에 배포**를 클릭합니다.

[![Azure에 배포](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

전체 템플릿은 [Azure Active Directory에서 보호하고 앱 서비스에서 호스트되는 사용자 지정 API로 논리 앱 호출](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json)을 참조하세요.

### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>3부: 논리 앱의 권한 부여 섹션 채우기

**HTTP** 동작의 **권한 부여** 섹션:

`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| 요소 | 설명 |
| ------- | ----------- |
| type |인증 유형입니다. ActiveDirectoryOAuth 인증의 경우 이 값은 `ActiveDirectoryOAuth`입니다. |
| tenant |AD 테넌트를 식별하는 데 사용되는 테넌트 ID입니다. |
| audience |필수입니다. 연결 중인 리소스입니다. |
| clientID |Azure AD 응용 프로그램의 클라이언트 ID입니다. |
| secret |필수입니다. 토큰을 요청하는 클라이언트의 암호입니다. |

이전 템플릿에는 이 권한 부여 섹션이 이미 설정되어 있지만 논리 앱에 직접 권한을 부여하려면 전체 권한 부여 섹션을 포함해야 합니다.

## <a name="secure-your-api-in-code"></a>코드에서 API 보호

### <a name="certificate-authentication"></a>인증서 인증

클라이언트 인증서를 사용하여 웹앱에 들어오는 요청을 확인할 수 있습니다. 코드를 설정하는 방법은 [웹앱에 대한 TLS 상호 인증 구성 방법](../app-service-web/app-service-web-configure-tls-mutual-auth.md)을 참조하세요.

**권한 부여** 섹션에서 다음을 포함해야 합니다. 

`{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`

| 요소 | 설명 |
| ------- | ----------- |
| type |필수입니다. 인증 유형입니다. SSL 클라이언트 인증서의 경우 이 값은 `ClientCertificate`여야 합니다. |
| pfx |필수입니다. PFX 파일의 Base64 인코딩 콘텐츠입니다. |
| password |필수입니다. PFX 파일에 액세스하기 위한 암호입니다. |

### <a name="basic-authentication"></a>기본 인증

들어오는 요청의 유효성을 검사하려면 기본 인증(예: 사용자 이름 및 암호)을 사용할 수 있습니다. 기본 인증은 일반 패턴이며 앱을 빌드하는 데 사용하는 어떤 언어로도 이 인증을 사용할 수 있습니다.

**권한 부여** 섹션에서 다음을 포함해야 합니다.

`{"type": "basic","username": "test","password": "test"}`.

| 요소 | 설명 |
| --- | --- |
| type |필수입니다. 인증 유형입니다. 기본 인증의 경우 이 값은 `Basic`입니다. |
| username |필수입니다. 인증하기 위한 사용자 이름입니다. |
| password |필수입니다. 인증하기 위한 암호입니다. |

### <a name="handle-azure-active-directory-authentication-in-code"></a>코드의 Azure Active Directory 인증 처리

기본적으로 Azure Portal에서 사용할 수 있는 Azure Active Directory 인증은 세분화된 권한 부여를 수행하지 않습니다. 예를 들어 이 인증은 API를 특정 사용자 또는 앱에 잠금을 만들지 않지만 특정 테넌트에는 잠금을 만듭니다.

API를 논리 앱으로 제한하려면 예를 들어 코드에서 JWT가 있는 헤더를 추출합니다. 호출자의 ID를 확인하고 일치하지 않는 요청을 거부합니다.

계속해서 사용자 고유에서 이 인증을 구현하고 Azure Portal 기능을 사용하지 않으려면 [Azure 앱에서 온-프레미스 Active Directory를 사용하여 인증](../app-service-web/web-sites-authentication-authorization.md)을 참조하세요.
논리 앱에 대한 응용 프로그램 ID를 만들고 해당 ID를 사용하여 API를 호출하려면 위의 단계를 따라야 합니다.
