---
title: "Mobile Engagement REST API를 사용한 인증"
description: "Azure Mobile Engagement REST API를 사용한 인증 방법 설명"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: b05181d9252c0a804648e01b4058019278ae5abe
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Mobile Engagement REST API를 사용한 인증
## <a name="overview"></a>개요
이 문서에서는 REST API를 사용하여 Mobile Engagement를 인증하도록 유효한 AAD Oauth 토큰을 가져오는 방법을 설명합니다. 

여러분이 유효한 Azure 구독을 보유하고 있고 [개발자 자습서](mobile-engagement-windows-store-dotnet-get-started.md)중 하나를 사용하여 Mobile Engagement 앱을 만들었다고 가정합니다.

## <a name="authentication"></a>인증
Microsoft Azure Active Directory 기반 OAuth 토큰을 사용하여 인증합니다. 

API 요청을 인증하려면 다음 형식인 모든 요청에 인증 헤더를 추가해야 합니다.

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Azure Active Directory 토큰은 1시간 후에 만료됩니다.
> 
> 

토큰을 얻는 몇 가지 방법이 있습니다. API가 일반적으로 클라우드 서비스에서 호출되므로 API 키를 사용하려 할 것입니다. Azure 용어에서는 API 키를 서비스 주체 암호라고 부릅니다. 다음은 키를 수동으로 설정하는 방법을 설명하는 절차입니다.

### <a name="one-time-setup-using-script"></a>일 회 설정(스크립트 사용)
설치에 대한 최소 시간이 걸리지만 허용될 수 있는 기본값을 사용하는 PowerShell 스크립트를 사용하여 설치를 수행하려면 아래 일련의 지침을 따라야 합니다. 필요에 따라  Azure 포털에서 이를 직접 수행하기 위해 [수동 설치](mobile-engagement-api-authentication-manual.md) 의 지침에 따르고 세밀하게 구성합니다. 

1. [여기](http://aka.ms/webpi-azps)에서 Azure PowerShell 최신 버전을 받습니다. 다운로드 지침에 대한 자세한 내용은 [링크](/powershell/azure/overview)에서 확인할 수 있습니다.  
2. Azure PowerShell을 설치하면 다음 명령을 사용하여 **Azure 모듈** 을 설치하도록 합니다.
   
    a. 사용할 수 있는 모듈 목록에서 Azure PowerShell 모듈을 사용할 수 있도록 합니다. 
   
        Get-Module –ListAvailable 
   
    ![사용 가능한 Azure 모듈][1]
   
    b. 위 목록에서 Azure PowerShell 모듈을 찾지 못한 경우 다음을 실행해야 합니다.
   
        Import-Module Azure 
3. 다음 명령을 실행하고 Azure 계정에 사용자 이름 및 암호를 제공하여 PowerShell에서 Azure Resource Manager에 로그인합니다. 
   
        Login-AzureRmAccount
4. 여러 구독이 있다면 다음을 실행해야 합니다.
   
    a. 모든 구독 목록을 가져오고 사용하려는 구독의 SubscriptionId를 복사합니다. API를 사용하여 상호 작용할 Mobile Engagement 앱을 갖는 동일한 구독인지 확인합니다. 
   
        Get-AzureRmSubscription
   
    b. SubscriptionId를 제공하는 다음 명령을 실행하여 사용할 구독을 구성합니다.
   
        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) 스크립트의 텍스트를 로컬 컴퓨터에 복사하여 PowerShell cmdlet(예: `APIAuth.ps1`)으로 저장하고 실행합니다. `.\APIAuth.ps1` 
6. 스크립트는 **principalName**에 대한 입력을 제공하도록 요청합니다. Active Directory 응용 프로그램(예: APIAuth)을 만드는 데 사용할 적절한 이름을 제공합니다. 
7. 스크립트가 완료되면 AD를 사용하여 프로그래밍 방식으로 인증해야 하는 다음 네 개의 값을 표시하고 복사하도록 합니다. 
   
    **TenantId**, **SubscriptionId**, **ApplicationId** 및 **Secret**입니다.
   
    TenantId를 `{TENANT_ID}`로, ApplicationId `{CLIENT_ID}`로, Secret을 `{CLIENT_SECRET}`으로 사용합니다.
   
   > [!NOTE]
   > PowerShell 스크립트를 실행할 수 없도록 기본 보안 정책이 설정되었을 수 있습니다. 이 경우 다음 명령을 사용하여 스크립트 실행을 허용하도록 실행 정책을 일시적으로 구성합니다.
   > 
   > Set-ExecutionPolicy RemoteSigned
   > 
   > 
8. PS cmdlet의 집합의 모양은 다음과 같습니다. 
   
    ![][3]
9. **회사가 보유한 응용 프로그램 표시**에서 **principalName**라는 스크립트에 제공한 이름으로 새 AD 응용 프로그램을 만든 Azure 관리 포털에서 확인합니다.
   
    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>유효한 토큰을 얻는 단계
1. 다음 매개 변수를 사용하여 API를 호출하고 TENANT\_ID, CLIENT\_ID 및 CLIENT\_SECRET을 대체해야 합니다.
   
   * **요청 URL** *https://login.microsoftonline.com/{TENANT\_ID}/oauth2/token*
   * **application/x-www-form-urlencoded** 인 *HTTP 콘텐츠 형식 헤더*
   * **HTTP 요청 본문** *grant\_type=client\_credentials&client_id={CLIENT\_ID}&client_secret={CLIENT\_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F*
     
     다음은 요청 예제입니다.
     
       POST /{TENANT_ID}/oauth2/token HTTP/1.1   Host: login.microsoftonline.com   Content-Type: application/x-www-form-urlencoded   grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso   urce=https%3A%2F%2Fmanagement.core.windows.net%2F
     
     다음은 응답 예제입니다.
     
       HTTP/1.1 200 OK   Content-Type: application/json; charset=utf-8   Content-Length: 1234
     
       {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144   5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
     
     이 예제에서는 POST 매개 변수의 URL 인코딩을 포함했으며, `resource` 값은 실제로 `https://management.core.windows.net/`입니다. 또한 URL에 특수 문자가 포함될 수 있으므로 URL 인코드 `{CLIENT_SECRET}` 에 주의합니다.
     
     > [!NOTE]
     > 테스트를 위해 [Fiddler](http://www.telerik.com/fiddler) 또는 [Chrome Postman 확장](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop)과 같은 HTTP 클라이언트 도구를 사용할 수 있습니다. 
     > 
     > 
2. 이제 모든 API 호출에 권한 부여 요청 헤더를 포함합니다.
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    401 상태 코드가 반환되면 응답 본문을 검사합니다. 토큰이 만료되었을 수도 있습니다. 토큰이 만료된 경우 새 토큰을 가져옵니다.

## <a name="using-the-apis"></a>API 사용
이제 유효한 토큰이 있으니 API 호출을 만들 준비가 완료되었습니다.

1. 각 API 요청에서, 이전 섹션에서 얻은 만료되지 않은 유효한 토큰을 전달해야 합니다.
2. 응용 프로그램을 식별하는 일부 매개 변수를 요청 URI에 연결해야 합니다. 요청 URI의 모양은 다음과 같습니다.
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    매개 변수를 가져오려면 응용 프로그램 이름을 클릭하고 대시보드를 클릭합니다. 그러면 다음과 같은 페이지에 매개 변수 3개가 모두 표시될 것입니다.
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** 리소스 그룹 이름은 새로 만들지 않은 한 **MobileEngagement**입니다. 
     
     ![Mobile Engagement API URI 매개 변수][2]

> [!NOTE]
> <br/>
> 
> 1. 이 API 루트 주소는 이전 API의 것이므로 무시합니다.<br/>
> 2. Azure 클래식 포털을 사용하여 앱을 만든 경우 응용 프로그램 이름과 다른 응용 프로그램 리소스 이름을 사용해야 합니다. Azure 포털에서 앱을 만든 경우 앱 이름을 사용해야 합니다(새 포털에서 만든 앱의 앱 이름과 응용 프로그램 리소스 이름은 차이가 없음).  
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



