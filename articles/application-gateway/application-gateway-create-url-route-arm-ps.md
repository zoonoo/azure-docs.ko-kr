---
title: "URL 라우팅 규칙을 사용하여 응용 프로그램 게이트웨이 만들기 | Microsoft Docs"
description: "이 페이지에서는 URL 라우팅 규칙을 사용하여 Azure 응용 프로그램 게이트웨이를 만들고 구성하기 위한 지침을 제공합니다."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: b1ed7d5693ff7e6730255462411d462694b730e1
ms.contentlocale: ko-kr
ms.lasthandoff: 09/15/2017

---
# <a name="create-an-application-gateway-by-using-path-based-routing"></a>경로 기반 라우팅을 사용하여 응용 프로그램 게이트웨이 만들기

> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

경로 기반 라우팅에서는 HTTP 요청의 URL 경로에 따라 경로가 연결됩니다. 그리고 응용 프로그램 게이트웨이에 있는 URL용으로 구성된 백 엔드 풀에 대한 경로가 있는지 확인한 다음 정의된 백 엔드 풀로 네트워크 트래픽을 전송합니다. URL 기반 라우팅의 일반적인 용도는 여러 콘텐츠 형식에 대한 요청의 부하를 여러 백 엔드 서버 풀에 분산하는 것입니다.

Azure Application Gateway에는 기본 라우팅 및 경로 기반 라우팅의 두 가지 규칙 유형이 있습니다. 기본 라우팅에서는 백 엔드 풀용 라운드 로빈 서비스가 제공됩니다. 경로 기반 라우팅에서는 라운드 로빈 배포뿐 아니라 요청 URL의 경로 패턴도 사용하여 백 엔드 풀을 선택합니다.

## <a name="scenario"></a>시나리오

다음 예제에서 Application Gateway는 두 개의 백 엔드 서버 풀(비디오 서버 풀 및 이미지 서버 풀)을 사용하여 contoso.com의 트래픽을 처리합니다.

http://contoso.com/image*에 대한 요청은 이미지 서버 풀(**pool1**)로 라우팅되고 http://contoso.com/video*에 대한 요청은 비디오 서버 풀(**pool2**)로 라우팅됩니다. 일치하는 경로 패턴이 없는 경우 기본 서버 풀(**pool1**)이 선택됩니다.

![URL 경로](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하는 Azure PowerShell cmdlet의 최신 버전을 설치합니다. **다운로드 페이지** 의 [Windows PowerShell](https://azure.microsoft.com/downloads/)섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷을 만듭니다. 해당 서브넷을 사용 중인 가상 컴퓨터 또는 클라우드 배포가 없는지 확인합니다. 응용 프로그램 게이트웨이는 가상 네트워크 서브넷에서 단독이어야 합니다.
3. 응용 프로그램 게이트웨이용 백 엔드 풀에 추가된 서버가 있거나, 가상 네트워크에서 또는 공용 IP/VIP가 할당된 상태로 이러한 서버의 끝점이 생성되었는지 확인합니다.

## <a name="requirements-to-create-an-application-gateway"></a>응용 프로그램 게이트웨이를 만들기 위한 요구 사항

* **백 엔드 서버 풀**: 백 엔드 서버의 IP 주소 목록입니다. 나열된 IP 주소는 가상 네트워크 서브넷에 속하거나 공용 IP/VIP여야 합니다.
* **백 엔드 서버 풀 설정**: 포트, 프로토콜, 쿠키 기반 선호도 등의 설정입니다. 이러한 설정은 풀에 연결되며 풀 내의 모든 서버에 적용됩니다.
* **프런트 엔드 포트:** 응용 프로그램 게이트웨이에서 열려 있는 공용 포트입니다. 트래픽은 이 포트에 도달한 후 백 엔드 서버 중 하나로 리디렉션됩니다.
* **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다.
* **규칙:** 규칙은 수신기와 백 엔드 서버 풀을 바인딩하고 특정 수신기에 도달했을 때 트래픽이 이동되는 풀을 정의합니다.

## <a name="create-an-application-gateway"></a>응용 프로그램 게이트웨이 만들기

Azure 클래식 포털과 Azure Resource Manager 간의 차이점은 응용 프로그램 게이트웨이를 만드는 순서와 구성해야 하는 항목입니다.

Resource Manager를 사용하면 응용 프로그램 게이트웨이를 만드는 모든 항목이 개별적으로 구성된 후 응용 프로그램 게이트웨이 리소스를 만드는 데 사용됩니다.

응용 프로그램 게이트웨이를 만들려면 다음 단계를 따릅니다.

1. 리소스 관리자에 대한 리소스 그룹을 만듭니다.
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크, 서브넷 및 공용 IP를 만듭니다.
3. 응용 프로그램 게이트웨이 구성 개체를 만듭니다.
4. 응용 프로그램 게이트웨이 리소스를 만듭니다.

## <a name="create-a-resource-group-for-resource-manager"></a>리소스 관리자에 대한 리소스 그룹 만들기

Azure PowerShell의 최신 버전을 사용하는지 확인합니다. 자세한 내용은 [Resource Manager에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

### <a name="step-1"></a>1단계

Azure에 로그인합니다.

```powershell
Login-AzureRmAccount
```

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.<BR>

### <a name="step-2"></a>2단계

계정에 대한 구독을 확인합니다.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>3단계:

사용할 Azure 구독을 선택합니다. <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>4단계

리소스 그룹을 만듭니다. (기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뜁니다.)

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

또는 응용 프로그램 게이트웨이의 리소스 그룹에 대한 태그를 만들 수도 있습니다.

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

Azure Resource Manager에서는 리소스 그룹이 해당 그룹의 모든 리소스에 사용되는 기본 위치를 지정해야 합니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

이전 예제에서는 "appgw-RG" 리소스 그룹을 만들었으며 "미국 서부"를 위치로 사용했습니다.

> [!NOTE]
> 응용 프로그램 게이트웨이에 대해 사용자 지정 프로브를 구성해야 하는 경우 [PowerShell을 사용하여 사용자 지정 프로브로 응용 프로그램 게이트웨이 만들기](application-gateway-create-probe-ps.md)를 참조하세요. 자세한 내용은 [Application Gateway 상태 모니터링 개요](application-gateway-probe-overview.md)를 참조하세요.
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 예제에서는 리소스 관리자를 사용하여 가상 네트워크를 만드는 방법을 보여 줍니다. 이 예제에서는 응용 프로그램 게이트웨이용 가상 네트워크를 만듭니다. Application Gateway에는 자체 서브넷이 필요합니다. 이러한 이유로 응용 프로그램 게이트웨이용으로 생성되는 서브넷은 가상 네트워크 주소 공간보다 작습니다. 따라서 웹 서버를 비롯한 기타 리소스를 동일한 가상 네트워크에서 구성할 수 있습니다.

### <a name="step-1"></a>1단계

주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 데 사용할 서브넷 변수에 할당합니다.  그러면 다음 예제에서 사용되는 응용 프로그램 게이트웨이용 서브넷 구성 개체가 생성됩니다.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>2단계

접두사 10.0.0.0/16과 서브넷 10.0.0.0/24를 사용하여 미국 서부 지역의 리소스 그룹 **appgw-rg**에 가상 네트워크 **appgwvnet**을 만듭니다. 이것으로 응용 프로그램 게이트웨이가 상주할 단일 서브넷이 포함된 가상 네트워크 구성이 완료되었습니다.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>3단계

다음 단계용으로 서브넷 변수를 할당합니다. 이 변수는 이후 단계에서 `New-AzureRMApplicationGateway` cmdlet으로 전달됩니다.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>프런트 엔드 구성에 대한 공용 IP 주소 만들기

미국 서부 지역에 리소스 그룹 **appgw-rg**에서 공용 IP 리소스 **publicIP01**을 만듭니다. Application Gateway는 공용 IP 주소, 내부 IP 주소 또는 둘 다를 사용하여 부하 분산 요청을 받을 수 있습니다.  이 예제에서는 공용 IP 주소만 사용합니다. 다음 예제에서는 공용 IP 주소를 생성하기 위해 DNS 이름을 구성하지 않습니다. Application Gateway에서는 공용 IP 주소에 대해 사용자 지정 DNS 이름을 지원하지 않기 때문입니다.  공용 끝점에 사용자 지정 이름이 필요한 경우 공용 IP 주소에 대해 자동으로 생성된 DNS 이름을 가리키도록 CNAME 레코드를 만듭니다.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

서비스를 시작할 때 응용 프로그램 게이트웨이에 IP 주소가 할당됩니다.

## <a name="create-the-application-gateway-configuration"></a>응용 프로그램 게이트웨이 구성 만들기

응용 프로그램 게이트웨이를 만들기 전에 모든 구성 항목을 설정해야 합니다. 다음 단계에서는 응용 프로그램 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

### <a name="step-1"></a>1단계

**gatewayIP01**이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. Application Gateway는 시작 시 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>2단계

**pool1** 및 **pool2**의 IP 주소용으로 백 엔드 IP 주소 풀 **pool1** 및 **pool2**를 구성합니다. 이러한 IP 주소는 응용 프로그램 게이트웨이에서 보호될 웹 응용 프로그램을 호스트하는 리소스의 IP 주소입니다. 이러한 백 엔드 풀 멤버는 기본 프로브 또는 사용자 지정 프로브를 통해 모두 정상 상태인 것으로 유효성이 검사된 항목입니다. 그런 다음 요청이 응용 프로그램 게이트웨이로 들어오면 트래픽이 라우팅됩니다. 응용 프로그램 게이트웨이 내의 여러 규칙이 백 엔드 풀을 사용할 수 있습니다. 즉, 동일한 호스트에 상주하는 여러 웹 응용 프로그램에 백 엔드 풀 하나를 사용할 수 있습니다.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

이 예제에서는 두 개의 백 엔드 풀이 URL 경로를 기반으로 네트워크 트래픽을 라우팅합니다. 풀 하나는 URL 경로 "/video"에서, 다른 하나는 경로 "/image"에서 트래픽을 수신합니다. 사용자 고유의 응용 프로그램 IP 주소 끝점을 추가하려면 이전 IP 주소를 바꿉니다. 

### <a name="step-3"></a>3단계

백 엔드 풀에서 부하가 분산된 네트워크 트래픽에 대해 응용 프로그램 게이트웨이 설정 **poolsetting01** 및 **poolsetting02**를 구성합니다. 이 예제에서는 백 엔드 풀에 대한 다른 백 엔드 풀 설정을 구성합니다. 각 백 엔드 풀에는 고유한 설정이 있을 수 있습니다.  규칙은 백 엔드 HTTP 설정을 사용하여 올바른 백 엔드 풀 멤버로 트래픽을 라우팅합니다. 이 과정을 통해 백 엔드 풀 멤버로 트래픽을 보낼 때 사용되는 프로토콜과 포트가 결정됩니다. 쿠키 기반 세션도 백 엔드 HTTP 설정에 따라 결정됩니다. 쿠키 기반 세션 선호도가 사용하도록 설정되어 있으면 각 패킷에 대해 이전 요청과 동일한 백 엔드로 트래픽이 전송됩니다.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>4단계

공용 IP 끝점으로 프런트 엔드 IP를 구성합니다. 수신기는 프런트 엔드 IP 구성 개체를 사용하여 외부 연결 IP 주소와 수신기 간의 관계를 설정합니다.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>5단계

응용 프로그램 게이트웨이에 대한 프런트 엔드 포트를 구성합니다. 수신기는 프런트 엔드 포트 구성 개체를 사용하여 응용 프로그램 게이트웨이가 수신기에서 트래픽을 수신 대기하는 포트를 정의합니다.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>6단계

수신기에서 들어오는 네트워크 트래픽을 수신하는 데 사용되는 공용 IP 주소 및 포트를 구성합니다. 다음 예제에서는 이전에 구성한 프런트 엔드 IP 구성, 프런트 엔드 포트 구성 및 프로토콜(Http 또는 Https, 대/소문자 구분)을 가져와 수신기를 구성합니다. 이 예제에서 수신기는 이전에 만든 공용 IP 주소의 80 포트에서 HTTP 트래픽을 수신 대기합니다.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>7단계

백 엔드 풀에 대한 URL 규칙 경로를 구성합니다. 이 단계에서는 Application Gateway에서 사용되는 상대 경로를 구성하고 URL 경로와 들어오는 트래픽을 처리하는 데 할당되는 백 엔드 풀 간의 매핑을 정의합니다.

> [!IMPORTANT]
> 각 경로는 "/"로 시작해야 하며 별표는 경로 끝에만 추가할 수 있습니다. 사용 가능한 예는 /xyz, /xyz* 또는 /xyz/*입니다. 경로 검사기에 제공되는 문자열에는 첫 번째 "?" 또는 "#" 뒤의 텍스트가 포함되지 않으며, 이러한 문자는 사용할 수 없습니다. 

다음 예제에서는 두 개의 규칙을 만듭니다. 그중 하나는 백 엔드 **pool1**로 트래픽을 라우팅하는 "/image/" 경로용 규칙이고, 다른 하나는 백 엔드 **pool2**로 트래픽을 라우팅하는 "/video/" 경로용 규칙입니다. 이러한 규칙을 사용하면 각 URL 집합에 대한 트래픽이 백 엔드로 라우팅됩니다. 예를 들어 http://contoso.com/image/figure1.jpg는 **pool1**로 라우팅되고 http://contoso.com/video/example.mp4는 **pool2**로 라우팅됩니다.

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

또한 경로가 미리 정의된 경로 규칙과 일치하지 않으면 규칙 경로 맵 구성은 기본 백 엔드 주소 풀을 구성합니다. 예를 들어 http://contoso.com/shoppingcart/test.html은 일치하지 않는 트래픽에 대한 기본 풀로 정의된 **pool1**로 이동합니다.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>8단계

규칙 설정을 만듭니다. 이 단계에서는 URL 경로 기반 라우팅을 사용하도록 응용 프로그램 게이트웨이를 구성합니다. 이전 단계에서 정의된 `$urlPathMap` 변수는 이제 경로 기반 규칙을 만드는 데 사용됩니다. 이 단계에서는 앞에서 만든 수신기 및 URL 경로 매핑과 규칙을 연결합니다.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>9단계

응용 프로그램 게이트웨이에 대한 크기 및 인스턴스 수를 구성합니다.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-an-application-gateway"></a>응용 프로그램 게이트웨이 만들기

이전 단계의 모든 구성 개체로 응용 프로그램 게이트웨이를 만듭니다.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-an-application-gateway-dns-name"></a>응용 프로그램 게이트웨이 DNS 이름 가져오기

게이트웨이를 만든 후에는 통신용 프런트 엔드를 구성합니다. 공용 IP를 사용할 때 Application Gateway는 친근한 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 고객이 응용 프로그램 게이트웨이에 연결할 수 있도록 하려면 CNAME 레코드를 사용하여 응용 프로그램 게이트웨이의 공용 끝점을 가리킵니다. 자세한 내용은 [Azure 클라우드 서비스에 대한 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요.

프런트 엔드 IP CNAME 레코드를 구성하려면 응용 프로그램 게이트웨이에 연결된 PublicIPAddress 요소를 사용하여 응용 프로그램 게이트웨이 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 응용 프로그램 게이트웨이의 DNS 이름을 사용하여 CNAME 레코드를 만듭니다. A 레코드를 사용하는 경우 응용 프로그램 게이트웨이 다시 시작 시 VIP가 변경될 수 있으므로 사용하지 않는 것이 좋습니다.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>다음 단계

SSL(Secure Sockets Layer) 오프로드에 대해 알아보려는 경우 [Azure Resource Manager를 사용하여 SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성](application-gateway-ssl-arm.md)을 참조하세요.


