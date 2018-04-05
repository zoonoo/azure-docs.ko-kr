---
title: Mobile Engagement REST API를 사용한 인증
description: Azure Mobile Engagement REST API를 사용한 인증 방법 설명
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 5979ded9afaa31054f835b5f16fe525809f5730d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Mobile Engagement REST API를 사용한 인증
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

## <a name="overview"></a>개요

이 문서에서는 Mobile Engagement REST API를 사용하여 인증할 수 있는 유효한 Azure AD(Azure Active Directory) OAuth 토큰을 가져오는 방법에 대해 설명합니다.

이 절차에서는 유효한 Azure 구독이 있고 [개발자 자습서](mobile-engagement-windows-store-dotnet-get-started.md)중 하나를 사용하여 Mobile Engagement 앱을 만들었다고 가정합니다.

## <a name="authentication"></a>인증

Microsoft Azure Active Directory 기반 OAuth 토큰이 인증에 사용됩니다. 

API 요청을 인증하려면 인증 헤더를 모든 요청에 추가해야 합니다. 인증 헤더의 형식은 다음과 같습니다.

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Azure Active Directory 토큰은 1시간 후에 만료됩니다.
> 
> 

토큰을 얻는 몇 가지 방법이 있습니다. API가 클라우드 서비스에서 호출되므로 API 키를 사용하려 할 것입니다. Azure 용어에서 API 키는 서비스 사용자 암호라고 합니다. 이 키를 수동으로 설정하는 방법 중 하나를 설명하는 절차는 다음과 같습니다.

### <a name="one-time-setup-using-a-script"></a>일회성 설정(스크립트 사용)

PowerShell 스크립트를 사용하여 설정을 수행하려면 다음 지침에 따른 단계를 수행합니다. PowerShell 스크립트는 설정하는 데 필요한 시간이 가장 짧지만 허용되는 기본값을 사용합니다. 

필요에 따라 Azure Portal에서 직접 수행하는 [수동 설정](mobile-engagement-api-authentication-manual.md)의 지침을 따를 수도 있습니다. Azure Portal에서 설정하면 더 자세히 구성할 수 있습니다.

1. 최신 버전의 Azure PowerShell을 [다운로드](http://aka.ms/webpi-azps)하여 가져옵니다. 다운로드 지침에 대한 자세한 내용은 [이 개요](/powershell/azure/overview)를 참조하세요.

2. Azure PowerShell이 설치되면 다음 명령을 사용하여 **Azure 모듈**이 설치되어 있는지 확인합니다.

    a. 사용할 수 있는 모듈 목록에서 Azure PowerShell 모듈을 사용할 수 있도록 합니다.

        Get-Module –ListAvailable

    ![사용 가능한 Azure 모듈][1]

    나. 위 목록에서 Azure PowerShell 모듈을 찾지 못한 경우 다음을 실행해야 합니다.

        Import-Module Azure
3. 다음 명령을 실행하여 PowerShell에서 Azure Resource Manager에 로그인합니다. Azure 계정에 대한 사용자 이름과 암호를 입력합니다. 

        Login-AzureRmAccount
4. 구독이 여러 개인 경우 다음 단계를 수행합니다.

    a. 모든 구독 목록을 가져옵니다. 그런 다음 사용하려는 구독의 **SubscriptionId**를 복사합니다. 이 구독에는 Mobile Engagement 앱이 있어야 합니다. 이 앱을 사용하여 API와 상호 작용할 것입니다. 

        Get-AzureRmSubscription

    나. 다음 명령을 실행합니다. **SubscriptionId**를 제공하여 사용하려는 구독을 구성합니다.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) 스크립트의 텍스트를 로컬 컴퓨터에 복사합니다. 그런 다음 PowerShell cmdlet(예: `APIAuth.ps1`)으로 저장하고 실행합니다.

         `.\APIAuth.ps1`.

6. 스크립트에서 **principalName**에 대한 입력을 제공하도록 요청합니다. Active Directory 응용 프로그램에 사용할 적절한 이름(예: APIAuth)을 제공합니다. 

7. 스크립트 실행이 완료되면 다음 네 가지 값이 표시됩니다. Active Directory를 사용하여 프로그래밍 방식으로 인증해야 하므로 반드시 복사해야 합니다. 

   - **TenantId**
   - **SubscriptionId**
   - **ApplicationId**
   - **비밀**

   TenantId를 `{TENANT_ID}`로, ApplicationId를 `{CLIENT_ID}`로, Secret을 `{CLIENT_SECRET}`으로 사용합니다.

   > [!NOTE]
   > 기본 보안 정책으로 인해 PowerShell 스크립트가 실행되지 않을 수 있습니다. 이 경우 다음 명령을 사용하여 스크립트 실행을 허용하도록 실행 정책을 일시적으로 구성합니다.
   > 
   > Set-ExecutionPolicy RemoteSigned
8. PowerShell cmdlet 집합이 다음과 같이 표시됩니다.
    ![PowerShell cmdlet][3]
9. Azure Portal에서 Active Directory로 이동하고, **앱 등록**을 선택한 다음, 앱을 검색하여 존재하는지 확인합니다.
    ![앱 검색][4]

### <a name="steps-to-get-a-valid-token"></a>유효한 토큰을 얻는 단계

1. 다음 매개 변수를 사용하여 API를 호출합니다. **TENANT\_ID**, **CLIENT\_ID** 및 **CLIENT\_SECRET**을 대체해야 합니다.
   
   * **요청 URL**: `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`

   * **HTTP Content-Type 헤더**: `application/x-www-form-urlencoded`
   
   * **HTTP 요청 본문**: `grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    다음은 요청 예제입니다.
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    다음은 응답 예제입니다.
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     이 예제에서는 `resource` 값이 실제로 `https://management.core.windows.net/`인 POST 매개 변수의 URL 인코딩이 포함됩니다. 또한 `{CLIENT_SECRET}` URL 인코딩에는 특수 문자가 포함될 수 있으므로 주의해야 합니다.

     > [!NOTE]
     > 테스트를 위해 [Fiddler](http://www.telerik.com/fiddler) 또는 [Chrome Postman 확장](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop)과 같은 HTTP 클라이언트 도구를 사용할 수 있습니다. 
     > 
     > 
2. 이제 모든 API 호출에 권한 부여 요청 헤더를 포함합니다.
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    요청에 401 상태 코드가 반환되면 응답 본문을 확인합니다. 토큰이 만료되었음을 알 수 있습니다. 토큰이 만료된 경우 새 토큰을 가져옵니다.

## <a name="use-the-apis"></a>API 사용
이제 유효한 토큰이 있으니 API 호출을 만들 준비가 완료되었습니다.

1. 각 API 요청에서 유효하고 만료되지 않은 토큰을 전달해야 합니다. 이전 섹션에서 만료되지 않은 토큰을 얻었습니다.

2. 응용 프로그램을 식별하는 요청 URI에 일부 매개 변수를 연결합니다. 요청 URI는 다음 코드와 같습니다.
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    매개 변수를 가져오려면 응용 프로그램 이름을 선택합니다. 그런 다음 **대시보드**를 선택합니다. 다음과 같이 세 가지 매개 변수가 모두 포함된 페이지가 표시됩니다.
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** 리소스 그룹 이름은 새로 만들지 않은 한 **MobileEngagement**입니다. 

> [!NOTE]
> API 루트 주소는 이전 API에 대한 것이므로 무시합니다.
> 
> Azure Portal을 사용하여 앱을 만든 경우 앱 이름 자체와 다른 응용 프로그램 리소스 이름을 사용해야 합니다. Azure Portal에서 앱을 만든 경우 앱 이름을 사용해야 합니다. 새 포털에서 만든 앱에 대한 응용 프로그램 리소스 이름과 앱 이름은 구분되지 않습니다.
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



