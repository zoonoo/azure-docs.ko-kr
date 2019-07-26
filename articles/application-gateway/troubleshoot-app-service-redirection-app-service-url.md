---
title: App Service를 사용 하 Azure 애플리케이션 게이트웨이 문제 해결-App Service의 URL로 리디렉션
description: 이 문서에서는 Azure 애플리케이션 Gateway를 사용할 때 리디렉션 문제를 해결 하는 방법에 대 한 정보를 제공 Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 7baadfb549b19bb12757c82d723578202b5cf8ad
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347888"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>App Service 문제 해결 Application Gateway

Application Gateway에서 앱 서버를 백 엔드 대상으로 사용 하는 경우 발생 하는 문제를 진단 하 고 해결 하는 방법을 알아봅니다.

## <a name="overview"></a>개요

이 문서에서는 다음과 같은 문제를 해결 하는 방법에 대해 설명 합니다.

> [!div class="checklist"]
> * 리디렉션이 있을 때 브라우저에 표시 되는 App Service의 URL
> * App Service의 ARRAffinity 쿠키 도메인이 원래 호스트 대신 example.azurewebsites.net (App Service hostname)로 설정 되어 있습니다.

백 엔드 응용 프로그램에서 리디렉션 응답을 보내는 경우 백 엔드 응용 프로그램에서 지정한 URL과 다른 URL로 클라이언트를 리디렉션할 수 있습니다. 예를 들어 app service가 응용 프로그램 게이트웨이 뒤에서 호스트 되는 경우이 작업을 수행 하 고 클라이언트가 상대 경로에 대 한 리디렉션을 수행 해야 할 수 있습니다. 예를 들어 contoso.azurewebsites.net/path1에서 contoso.azurewebsites.net/path2로의 리디렉션이 있습니다. App service는 리디렉션 응답을 보낼 때 응용 프로그램 게이트웨이에서 수신 하는 요청의 위치 헤더에 있는 것과 동일한 호스트 이름을 사용 합니다. 따라서 클라이언트는 application gateway (contoso.com/path2)를 통하지 않고 contoso.azurewebsites.net/path2에 직접 요청을 만듭니다. Application gateway를 무시 하는 것은 바람직하지 않습니다.

이 문제는 다음과 같은 주요 이유로 인해 발생할 수 있습니다.

- App Service에 리디렉션이 구성 되어 있습니다. 리디렉션은 요청에 후행 슬래시를 추가 하는 것 처럼 간단할 수 있습니다.
- 리디렉션을 발생 시키는 Azure AD 인증이 있습니다.

또한 Application Gateway 뒤에 App Services를 사용 하는 경우에는 Application Gateway (example.com)와 연결 된 도메인 이름이 App Service의 도메인 이름 (예 example.azurewebsites.net)과 다를 것입니다. App Service에 의해 설정 된 ARRAffinity 쿠키의 도메인 값은 바람직하지 않은 "example.azurewebsites.net" 도메인 이름을 포함 합니다. 원래 호스트 이름 (example.com)은 쿠키의 도메인 이름 값 이어야 합니다.

## <a name="sample-configuration"></a>샘플 구성

- HTTP 수신기: 기본 또는 다중 사이트
- 백 엔드 주소 풀: App Service
- HTTP 설정: "백 엔드 주소에서 호스트 이름 선택" 사용
- 프로브가 "HTTP 설정에서 호스트 이름 선택" 사용

## <a name="cause"></a>원인

App Service은 다중 테 넌 트 서비스 이므로 요청에서 호스트 헤더를 사용 하 여 요청을 올바른 끝점으로 라우팅합니다. 그러나 App services의 기본 도메인 이름 *. azurewebsites.net (예 contoso.azurewebsites.net)는 application gateway의 도메인 이름 (예를 들어 contoso.com)과 다릅니다. 클라이언트의 원래 요청에는 호스트 이름으로 응용 프로그램 게이트웨이의 도메인 이름 (contoso.com)이 있으므로 요청을 라우팅할 때 원래 요청의 호스트 이름을 app services의 호스트 이름으로 변경 하도록 응용 프로그램 게이트웨이를 구성 해야 합니다. app service 백 엔드입니다.  Application Gateway의 HTTP 설정 구성에서 "백 엔드 주소에서 호스트 이름 선택" 스위치를 사용 하 고 상태 프로브 구성에서 "백 엔드 HTTP 설정에서 호스트 이름 선택" 스위치를 사용 하 여이를 달성할 수 있습니다.



![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

이로 인해 App Service는 리디렉션을 수행 하는 경우 달리 구성 되지 않은 한 원래 호스트 이름 "contoso.com" 대신 Location 헤더에서 재정의 된 호스트 이름 "contoso.azurewebsites.net"를 사용 합니다. 아래 예제 요청 및 응답 헤더를 확인할 수 있습니다.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
위의 예제에서 응답 헤더의 상태 코드는 리디렉션에 대해 301이 고 location 헤더에는 원래 호스트 이름 "www.contoso.com" 대신 App Service의 호스트 이름이 있습니다.

## <a name="solution-rewrite-the-location-header"></a>해결책: 위치 헤더 다시 작성

Location 헤더의 호스트 이름을 application gateway의 도메인 이름으로 설정 해야 합니다. 이렇게 하려면 응답의 location 헤더에 azurewebsites.net가 포함 되어 있는지 여부를 평가 하 고 응용 프로그램 게이트웨이의 호스트 이름을 갖도록 location 헤더를 다시 작성 하는 작업을 수행 하는 [다시 쓰기 규칙](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) 을 만듭니다.  [Location 헤더를 다시 작성 하는 방법](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)에 대 한 지침을 참조 하세요.

> [!NOTE]
> HTTP 헤더 재작성 지원은 Application Gateway [Standard_V2 및 WAF_V2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 에서만 사용할 수 있습니다. V1 SKU를 사용 하는 경우 v 1에서 v 2로 [마이그레이션하여](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) v2 sku에서 제공 하는 재작성 및 기타 [고급 기능](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) 을 사용할 수 있도록 하는 것이 좋습니다.

## <a name="alternate-solution-use-app-services-custom-domain-instead-of-default-domain-name"></a>대체 솔루션: 기본 도메인 이름 대신 App service의 사용자 지정 도메인 사용

V1 SKU를 사용 하는 경우이 기능은 V2 SKU에만 사용할 수 있으므로 location 헤더를 다시 작성할 수 없습니다. 따라서 리디렉션 문제를 해결 하려면 호스트 재정의를 수행 하는 대신 Application Gateway 수신 하는 동일한 호스트 이름을 App Service에 전달 해야 합니다.

이렇게 하면 App Service는 동일한 원래 호스트 헤더에 대 한 리디렉션 (있는 경우)을 수행 하 여 자체가 아닌 Application Gateway를 가리킵니다.

이를 위해서는 사용자 지정 도메인을 소유 하 고 아래에 설명 된 프로세스를 수행 해야 합니다.

- App Service의 사용자 지정 도메인 목록에 도메인을 등록 합니다. 이를 위해 사용자 지정 도메인에 App Service의 FQDN을 가리키는 CNAME이 있어야 합니다. 자세한 내용은 [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)을 참조 하세요.

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 이 작업이 완료 되 면 App Service 호스트 이름 "www.contoso.com"를 받아들일 준비가 된 것입니다. 이제 DNS에서 CNAME 항목을 변경 하 Application Gateway의 FQDN으로 다시 지정 합니다. 예를 들면 "appgw.eastus.cloudapp.azure.com"입니다.

- DNS 쿼리를 수행할 때 도메인 "www.contoso.com"가 Application Gateway의 FQDN으로 확인 되는지 확인 합니다.

- 사용자 지정 프로브를 설정 하 여 "백 엔드 HTTP 설정에서 호스트 이름 선택"을 사용 하지 않도록 설정 합니다. AzApplicationGatewayProbeConfig 명령에서-PickHostNameFromBackendHttpSettings 스위치를 사용 하지 않고 프로브 설정 및 PowerShell에서 확인란의 선택을 취소 하 여 포털에서이 작업을 수행할 수 있습니다. 프로브의 호스트 이름 필드에 App Service의 FQDN "example.azurewebsites.net"를 입력 합니다. Application Gateway에서 전송 된 프로브 요청은 호스트 헤더에이를 전달 합니다.

  > [!NOTE]
  > 다음 단계를 수행 하는 동안 HTTP 설정에 "백 엔드 주소에서 호스트 이름 선택" 스위치를 사용 하도록 설정 되어 있으므로 사용자 지정 프로브가 백 엔드 HTTP 설정에 연결 되어 있지 않은지 확인 하세요.

- "백 엔드 주소에서 호스트 이름 선택"을 사용 하지 않도록 Application Gateway의 HTTP 설정을 설정 합니다. AzApplicationGatewayBackendHttpSettings 명령에서-PickHostNameFromBackendAddress 스위치를 사용 하지 않고 확인란을 선택 취소 하 여 포털에서이 작업을 수행할 수 있습니다.

- 사용자 지정 프로브를 백 엔드 HTTP 설정에 다시 연결 하 고 정상 상태인 경우 백엔드 상태를 확인 합니다.

- 이 작업이 완료 되 면 Application Gateway에서 이제 App Service에 동일한 호스트 이름 "www.contoso.com"를 전달 하 고 리디렉션이 동일한 호스트 이름에 대해 수행 됩니다. 아래 예제 요청 및 응답 헤더를 확인할 수 있습니다.

기존 설치를 위해 PowerShell을 사용 하 여 위에서 언급 한 단계를 구현 하려면 아래의 샘플 PowerShell 스크립트를 따르세요. 프로브 및 HTTP 설정 구성에서-PickHostname 스위치를 사용 하지 않은 경우를 확인 합니다.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>다음 단계

앞의 단계에서 문제가 해결되지 않으면 [지원 티켓](https://azure.microsoft.com/support/options/)을 엽니다.
