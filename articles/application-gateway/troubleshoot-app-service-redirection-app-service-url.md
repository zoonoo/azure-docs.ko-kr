---
title: 앱 서비스 URL로 리디렉션문제 해결
titleSuffix: Azure Application Gateway
description: 이 문서에서는 Azure 응용 프로그램 게이트웨이Azure 앱 서비스에서 사용 되는 경우 리디렉션 문제를 해결 하는 방법에 대 한 정보를 제공 합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 961ed17bcef19b445c2546a557725bb6bd8653cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293533"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>응용 프로그램 게이트웨이에서 앱 서비스 문제 해결

Azure 앱 서비스가 Azure 응용 프로그램 게이트웨이를 사용하여 백 엔드 대상으로 사용될 때 발생할 수 있는 문제를 진단하고 해결하는 방법을 알아봅니다.

## <a name="overview"></a>개요

이 문서에서는 다음과 같은 문제를 해결하는 방법을 배웁니다.

> [!div class="checklist"]
> * 리디렉션이 있을 때 앱 서비스 URL이 브라우저에 노출됩니다.
> * 앱 서비스 ARRAffinity 쿠키 도메인은 원래 호스트 대신 앱 서비스 호스트 이름(example.azurewebsites.net)으로 설정됩니다.

백 엔드 응용 프로그램이 리디렉션 응답을 보내는 경우 클라이언트를 백 엔드 응용 프로그램에서 지정한 URL과 다른 URL로 리디렉션할 수 있습니다. 앱 서비스가 응용 프로그램 게이트웨이 뒤에서 호스팅되고 클라이언트가 상대 경로로 리디렉션을 수행해야 하는 경우 이 작업을 수행할 수 있습니다. 예를 들어 contoso.azurewebsites.net/path1 contoso.azurewebsites.net/path2 리디렉션이 있습니다. 

앱 서비스가 리디렉션 응답을 보내면 해당 응답의 위치 헤더에서 응용 프로그램 게이트웨이에서 수신하는 요청의 호스트 이름과 동일한 호스트 이름을 사용합니다. 예를 들어 클라이언트는 응용 프로그램 게이트웨이 contoso.com/path2 거치지 않고 contoso.azurewebsites.net/path2 직접 요청을 합니다. 응용 프로그램 게이트웨이를 우회하지 않으려고 합니다.

이 문제는 다음과 같은 주요 이유로 발생할 수 있습니다.

- 앱 서비스에 리디렉션이 구성되었습니다. 리디렉션은 요청에 후행 슬래시를 추가하는 것만큼 간단할 수 있습니다.
- 리디렉션을 유발하는 Azure Active Directory 인증이 있습니다.

또한 응용 프로그램 게이트웨이 뒤에 앱 서비스를 사용할 때 응용 프로그램 게이트웨이(example.com)와 연결된 도메인 이름은 앱 서비스의 도메인 이름(예: example.azurewebsites.net)과 다릅니다. 앱 서비스에서 설정한 ARRAffinity 쿠키의 도메인 값은 example.azurewebsites.net 도메인 이름을 전달하므로 바람직하지 않습니다. 원래 호스트 이름 example.com 쿠키의 도메인 이름 값이어야 합니다.

## <a name="sample-configuration"></a>샘플 구성

- HTTP 수신기: 기본 또는 다중 사이트
- 백 엔드 주소 풀: 앱 서비스
- HTTP 설정: **백 엔드 주소에서 호스트 이름 선택** 사용
- 프로브: **HTTP 설정에서 호스트 이름 선택** 사용

## <a name="cause"></a>원인

앱 서비스는 다중 테넌트 서비스이므로 요청의 호스트 헤더를 사용하여 요청을 올바른 끝점으로 라우팅합니다. 앱 서비스의 기본 도메인 이름인 *.azurewebsites.net(예: contoso.azurewebsites.net)은 응용 프로그램 게이트웨이의 도메인 이름(예: contoso.com)과 다릅니다. 

클라이언트의 원래 요청에는 호스트 이름으로 contoso.com 응용 프로그램 게이트웨이의 도메인 이름이 있습니다. 요청을 앱 서비스 백 엔드로 라우팅할 때 원래 요청의 호스트 이름을 앱 서비스의 호스트 이름으로 변경하도록 응용 프로그램 게이트웨이를 구성해야 합니다. 응용 프로그램 게이트웨이의 HTTP 설정 **구성에서 백 엔드 주소에서 호스트 이름 선택** 스위치를 사용합니다. 상태 프로브 **구성에서 백 엔드 HTTP 설정에서 호스트 이름 선택** 스위치를 사용합니다.



![응용 프로그램 게이트웨이 변경 호스트 이름](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

앱 서비스가 리디렉션을 수행하면 달리 구성되지 않는 한 원래 호스트 이름 contoso.com 대신 위치 헤더에서 재정의된 호스트 이름 contoso.azurewebsites.net 사용합니다. 다음 예제 요청 및 응답 헤더를 확인합니다.
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
이전 예제에서는 응답 헤더에 리디렉션을 위한 상태 코드(301)가 있습니다. 위치 헤더에는 원래 호스트 이름 대신 앱 서비스의 `www.contoso.com`호스트 이름이 있습니다.

## <a name="solution-rewrite-the-location-header"></a>해결: 위치 헤더 다시 작성

위치 헤더의 호스트 이름을 응용 프로그램 게이트웨이의 도메인 이름으로 설정합니다. 이렇게 하려면 응답의 위치 헤더에 azurewebsites.net 포함되어 있는지 평가하는 조건으로 [다시 작성 규칙을](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) 만듭니다. 또한 응용 프로그램 게이트웨이의 호스트 이름을 갖도록 위치 헤더를 다시 작성하는 작업을 수행해야 합니다. 자세한 내용은 [위치 헤더를 다시 작성하는 방법에](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)대한 지침을 참조하십시오.

> [!NOTE]
> HTTP 헤더 다시 쓰기 지원은 응용 프로그램 게이트웨이의 [Standard_v2 및 WAF_v2 SKU에서만](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 사용할 수 있습니다. v1 SKU를 사용하는 경우 [v1에서 v2로 마이그레이션하는](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2)것이 좋습니다. v2 SKU에서 사용할 수 있는 재작성 및 기타 [고급 기능을](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) 사용하려고 합니다.

## <a name="alternate-solution-use-a-custom-domain-name"></a>대체 솔루션: 사용자 지정 도메인 이름 사용

v1 SKU를 사용하는 경우 위치 헤더를 다시 작성할 수 없습니다. 이 기능은 v2 SKU에서만 사용할 수 있습니다. 리디렉션 문제를 해결하려면 호스트 재정의를 수행하는 대신 응용 프로그램 게이트웨이가 받는 것과 동일한 호스트 이름을 앱 서비스에 전달합니다.

이제 앱 서비스는 자체 가 아닌 응용 프로그램 게이트웨이를 가리키는 동일한 원래 호스트 헤더에서 리디렉션(있는 경우)을 수행합니다.

사용자 지정 도메인을 소유하고 다음 프로세스를 따라야 합니다.

- 앱 서비스의 사용자 지정 도메인 목록에 도메인을 등록합니다. 앱 서비스의 FQDN을 가리키는 사용자 지정 도메인에 CNAME이 있어야 합니다. 자세한 내용은 [Azure 앱 서비스에 기존 사용자 지정 DNS 이름 매핑을](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)참조하세요.

    ![앱 서비스 사용자 지정 도메인 목록](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 앱 서비스는 호스트 이름을 `www.contoso.com`수락할 준비가 되었습니다. 예를 들어 응용 프로그램 게이트웨이의 FQDN을 가리키도록 DNS의 CNAME `appgw.eastus.cloudapp.azure.com`항목을 변경합니다.

- DNS 쿼리를 `www.contoso.com` 수행할 때 도메인이 응용 프로그램 게이트웨이의 FQDN으로 확인되는지 확인합니다.

- 사용자 지정 프로브를 설정하여 **백 엔드 HTTP 설정에서 호스트 이름 선택을**사용하지 않도록 설정합니다. Azure 포털에서 프로브 설정에서 확인란을 선택 취소합니다. PowerShell에서는 **설정-AzApplication게이트웨이ProbeConfig** 명령에서 **-PickHostNameFromBackendHttpSettings** 스위치를 사용하지 마십시오. 프로브의 호스트 이름 필드에 앱 서비스의 FQDN, example.azurewebsites.net 입력합니다. 응용 프로그램 게이트웨이에서 보낸 프로브 요청은 이 FQDN을 호스트 헤더로 전달합니다.

  > [!NOTE]
  > 다음 단계에서는 사용자 지정 프로브가 백 엔드 HTTP 설정에 연결되어 있지 않은지 확인합니다. HTTP 설정은 여전히 이 시점에서 **백 엔드 주소 스위치에서 호스트 이름 선택이** 활성화되어 있습니다.

- 응용 프로그램 게이트웨이의 HTTP 설정을 설정하여 **백 엔드 주소에서 호스트 이름 선택을**사용하지 않도록 설정합니다. Azure 포털에서 확인란을 선택 취소합니다. PowerShell에서는 **Set-AzApplication게이트웨이백엔드HttpSettings** 명령에서 **-PickHostNameFromBackendAddress** 스위치를 사용하지 마십시오.

- 사용자 지정 프로브를 백 엔드 HTTP 설정에 다시 연결하고 백 엔드가 정상인지 확인합니다.

- 이제 응용 프로그램 게이트웨이는 동일한 `www.contoso.com`호스트 이름, 즉 로 케이 션 서비스에 전달 해야 합니다. 리디렉션은 동일한 호스트 이름에서 발생합니다. 다음 예제 요청 및 응답 헤더를 확인합니다.

기존 설정에 PowerShell을 사용하여 이전 단계를 구현하려면 다음 의 샘플 PowerShell 스크립트를 사용합니다. 프로브 및 HTTP 설정 구성에서 **-PickHostname** 스위치를 사용하지 않은 방법을 참고하십시오.

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

앞의 단계에서 문제가 해결되지 않으면 [지원 티켓을](https://azure.microsoft.com/support/options/)엽니다.
