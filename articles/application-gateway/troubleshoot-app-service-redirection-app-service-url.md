---
title: App Service URL로 리디렉션 문제 해결
titleSuffix: Azure Application Gateway
description: 이 문서에서는 Azure App Service에서 Azure Application Gateway를 사용할 때 리디렉션 문제를 해결하는 방법에 대한 정보를 제공합니다
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 1cc7df755198461643703cac988c8c31f2ac25db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182889"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Application Gateway에서 App Service 문제 해결

Azure Application Gateway에서 Azure App Service가 백 엔드 대상으로 사용될 때 발생할 수 있는 문제를 진단하고 해결하는 방법에 대해 알아봅니다.

## <a name="overview"></a>개요

이 문서에서는 다음 문제의 해결 방법에 대해 알아봅니다.

* 리디렉션될 때 앱 서비스 URL이 브라우저에 표시됩니다.
* 앱 서비스 ARRAffinity 쿠키 도메인이 앱 서비스 호스트 이름으로 설정됩니다(예: 원래의 호스트 대신 .azurewebsites.net으로 설정됨).

백 엔드 애플리케이션이 리디렉션 응답을 전송할 때 백 엔드 애플리케이션에서 지정한 것과 다른 URL로 클라이언트를 리디렉션할 수 있습니다. 앱 서비스가 애플리케이션 게이트웨이 뒤에서 호스트되고 클라이언트가 상대 경로로 리디렉션해야 하는 경우 이를 수행할 수 있습니다. 예를 들어 contoso.azurewebsites.net/path1에서 contoso.azurewebsites.net/path2로의 리디렉션이 있습니다. 

앱 서비스가 리디렉션 응답을 보낼 때 애플리케이션 게이트웨이에서 수신하는 요청과 응답의 위치 헤더가 동일한 호스트 이름을 사용합니다. 예를 들어 클라이언트는 애플리케이션 게이트웨이 contoso.com/path2를 거치지 않고 contoso.azurewebsites.net/path2로 직접 요청합니다. 애플리케이션 게이트웨이를 바이패스하지 않으려고 합니다.

이 문제는 다음과 같은 주요 이유로 인해 발생할 수 있습니다.

- 앱 서비스에 리디렉션이 구성되어 있습니다. 리디렉션은 요청에 후행 슬래시를 추가하는 것처럼 간단할 수 있습니다.
- 리디렉션을 유발하는 Azure Active Directory 인증이 있습니다.

또한 애플리케이션 게이트웨이 뒤에서 앱 서비스를 사용하는 경우 애플리케이션 게이트웨이와 연결된 도메인 이름(example.com)은 앱 서비스의 도메인 이름(예: example.azurewebsites.net)과 다릅니다. 앱 서비스에서 설정한 ARRAffinity 쿠키 도메인 값은 적합하지 않은 example.azurewebsites.net 도메인 이름을 전달합니다. 쿠키의 도메인 이름 값은 원래 호스트 이름인 example.com이어야 합니다.

## <a name="sample-configuration"></a>샘플 구성

- HTTP 수신기: 기본 또는 다중 사이트
- 백 엔드 주소 풀: App Service
- HTTP 설정: **백 엔드 주소에서 호스트 이름 선택** 사용
- 프로브: **HTTP 설정에서 호스트 이름 선택** 사용

## <a name="cause"></a>원인

App Service는 다중 테넌트 서비스로, 요청의 호스트 헤더를 사용하여 요청을 올바른 엔드포인트로 라우팅합니다. App Service의 기본 도메인 이름은 *.azurewebsites.net(예: contoso.azurewebsites.net)이며 이것은 애플리케이션 게이트웨이 도메인 이름과는 다릅니다(예: contoso.com). 

클라이언트의 원래 요청에는 애플리케이션 게이트웨이 도메인 이름인 contoso.com이 호스트 이름으로 되어 있습니다. 요청을 앱 서비스 백 엔드로 라우팅할 때 애플리케이션 게이트웨이가 원래 요청의 호스트 이름을 앱 서비스 호스트 이름으로 변경하도록 구성해야 합니다. 애플리케이션 게이트웨이의 HTTP 설정 구성에서 **백 엔드 주소에서 호스트 이름 선택** 스위치를 사용합니다. 상태 프로브 구성에서 **백 엔드 HTTP 설정에서 호스트 이름 선택** 스위치를 사용합니다.



![애플리케이션 게이트웨이가 호스트 이름 변경](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

앱 서비스가 리디렉션될 때 별도로 구성되지 않은 경우에는 원래 호스트 이름인 contoso.com 대신 위치 헤더에서 재정의된 호스트 이름 contoso.azurewebsites.net을 사용합니다. 다음의 요청 및 응답 헤더 예제를 참조하십시오.
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
이전 예제에서는 응답 헤더에 리디렉션을 위한 상태 코드 301이 있는 것을 살펴보았습니다. 위치 헤더에 원래 호스트 이름 대신 앱 서비스의 호스트 이름이 있습니다 `www.contoso.com`.

## <a name="solution-rewrite-the-location-header"></a>솔루션: 위치 헤더 다시 쓰기

위치 헤더의 호스트 이름을 애플리케이션 게이트웨이 도메인 이름으로 설정합니다. 이 작업을 수행하려면 응답의 위치 헤더에 azurewebsites.net이 포함되었는지를 평가하는 조건으로 [다시 쓰기 규칙](./rewrite-http-headers.md)을 만듭니다. 또한 애플리케이션 게이트웨이의 호스트 이름을 갖도록 위치 헤더를 다시 쓰는 작업을 수행해야 합니다. 자세한 내용은 [위치 헤더 다시 쓰기](./rewrite-http-headers.md#modify-a-redirection-url) 지침을 참조하세요.

> [!NOTE]
> HTTP 헤더 다시 쓰기 지원은 Application Gateway의 [Standard_v2 및 WAF_v2 SKU](./application-gateway-autoscaling-zone-redundant.md)에서만 가능합니다. 만약 v1 SKU를 사용하는 경우 [v1에서 v2로 마이그레이션](./migrate-v1-v2.md)을 권장합니다. v2 SKU에서 사용 가능한 다시 쓰기와 다른 [고급 기능](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku)을 사용하려고 합니다.

## <a name="alternate-solution-use-a-custom-domain-name"></a>대체 솔루션: 사용자 지정 도메인 사용하기

만약 v1 SKU를 사용하는 경우 위치 헤더를 다시 쓸 수 없습니다. 이 기능은 v2 SKU에서만 사용 가능합니다. 리디렉션 문제를 해결하려면 호스트 재정의를 수행하는 대신, 애플리케이션 게이트웨이가 수신하는 호스트 이름과 동일한 호스트 이름을 앱 서비스에 전달합니다.

이제 앱 서비스는 자체가 아닌 애플리케이션 게이트웨이를 가리키는 동일한 원래 호스트 헤더에서 리디렉션(해당하는 경우)을 수행합니다.

사용자 지정 도메인을 소유한 상태에서 다음 프로세스를 따르십시오.

- 도메인을 앱 서비스의 사용자 지정 도메인 목록에 등록합니다. 사용자 지정 도메인에 앱 서비스의 FQDN을 가리키는 CNAME이 있어야 합니다. 자세한 내용은 [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](../app-service/app-service-web-tutorial-custom-domain.md)을 참조하세요.

    ![앱 서비스의 사용자 지정 도메인 목록](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 앱 서비스가 호스트 이름 `www.contoso.com`을 적용할 준비가 되었습니다. DNS의 CNAME 항목을 변경하여 애플리케이션 게이트웨이의 FQDN을 다시 가리키도록 합니다(예: `appgw.eastus.cloudapp.azure.com`).

- DNS 쿼리를 수행할 때 `www.contoso.com` 도메인이 애플리케이션 게이트웨이의 FQDN으로 확인되는지 확인합니다.

- 사용자 지정 프로브에서 **백 엔드 HTTP 설정에서 호스트 이름 선택** 을 사용하지 않도록 설정합니다. Azure Portal에서 프로브 설정 확인란의 선택을 취소합니다. PowerShell의 **Set-AzApplicationGatewayProbeConfig** 명령에서 **-PickHostNameFromBackendHttpSettings** 스위치를 사용하지 마세요. 프로브의 호스트 이름 필드에 앱 서비스의 FQDN example.azurewebsites.net을 입력합니다. 애플리케이션 게이트웨이에서 전송된 프로브 요청은 호스트 헤더에 이 FQDN을 전달합니다.

  > [!NOTE]
  > 다음 단계에서는 사용자 지정 프로브가 백 엔드 HTTP 설정에 연결되어 있지 않은지 확인합니다. 현재 HTTP 설정의 **백 엔드 주소에서 호스트 이름 선택** 스위치를 여전히 사용할 수 있습니다.

- 애플리케이션 게이트웨이의 HTTP 설정에서 **백 엔드 주소에서 호스트 이름 선택** 을 사용하지 않도록 설정합니다. Azure Portal에서 확인란의 선택을 취소합니다. PowerShell의 **Set-AzApplicationGatewayBackendHttpSettings** 명령에서 **-PickHostNameFromBackendAddress** 스위치를 사용하지 마세요.

- 사용자 지정 프로브를 백 엔드 HTTP 설정에 다시 연결하고 백 엔드가 정상 상태인지 확인합니다.

- 이제 애플리케이션 게이트웨이는 동일한 호스트 이름 `www.contoso.com`을 앱 서비스에 전달해야 합니다. 리디렉션이 동일한 호스트 이름에서 발생합니다. 다음의 요청 및 응답 헤더 예제를 참조하십시오.

기존 설정에 PowerShell을 사용하여 이전 단계를 구현하려면 다음의 PowerShell 스크립트 샘플을 사용합니다. 프로브 및 HTTP 설정 구성에서 **-PickHostname** 스위치를 사용하지 않은 방법을 확인하십시오.

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

이전 단계로 문제가 해결되지 않으면 [지원 티켓](https://azure.microsoft.com/support/options/)을 엽니다.