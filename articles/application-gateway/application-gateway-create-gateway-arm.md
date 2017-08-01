---
title: "Azure Application Gateway 만들기 및 관리 - PowerShell | Microsoft Docs"
description: "이 페이지에서는 Azure Resource Manager를 사용하여 Azure 응용 프로그램 게이트웨이를 만들고, 구성하고, 시작하고, 삭제하기 위한 지침을 제공합니다."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 866e9b5f-0222-4b6a-a95f-77bc3d31d17b
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 6d38dd6802a25b147fd014b4d26ca432ca87a07d
ms.contentlocale: ko-kr
ms.lasthandoff: 07/25/2017

---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Azure 리소스 관리자를 사용하여 응용 프로그램 게이트웨이 만들기, 시작 또는 삭제

> [!div class="op_single_selector"]
> * [쉬운 테이블](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure 클래식 PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager 템플릿](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Azure 응용 프로그램 게이트웨이는 계층 7 부하 분산 장치입니다. 클라우드 또는 온-프레미스이든 상관없이 서로 다른 서버 간에 장애 조치(Failover), 성능 라우팅 HTTP 요청을 제공합니다.
응용 프로그램 게이트웨이는 HTTP 부하 분산, 쿠키 기반 세션 선호도, SSL(Secure Sockets Layer) 오프로드, 사용자 지정 상태 프로브, 다중 사이트 지원 및 기타를 포함하여 많은 ADC(Application Delivery Controller)를 제공합니다.

지원되는 기능의 전체 목록을 찾으려면 [Application Gateway 개요](application-gateway-introduction.md)

이 문서는 응용 프로그램 게이트웨이를 생성, 구성, 시작 및 삭제하는 단계를 안내합니다.

> [!IMPORTANT]
> Azure 리소스로 작업하기 전에 Azure에는 현재 Resource Manager와 클래식 모드의 두 가지 배포 모델이 있다는 것을 이해해야 합니다. Azure 리소스로 작업하기 전에 [배포 모델 및 도구](../azure-classic-rm.md) 를 이해해야 합니다. 이 문서의 윗부분에 있는 탭을 클릭하여 다양한 도구에 대한 설명서를 볼 수 있습니다. 이 문서에서는 Azure Resource Manager를 사용하여 응용 프로그램 게이트웨이 만들기를 설명합니다. 클래식 버전을 사용하려면 [PowerShell을 사용하여 응용 프로그램 게이트웨이 클래식 배포 만들기](application-gateway-create-gateway.md)로 이동합니다.

## <a name="before-you-begin"></a>시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하는 Azure PowerShell cmdlet의 최신 버전을 설치합니다. **다운로드 페이지** 의 [Windows PowerShell](https://azure.microsoft.com/downloads/)섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
1. 기존 가상 네트워크가 있는 경우 기존의 빈 서브넷을 선택하거나 응용 프로그램 게이트웨이에서 사용할 기존 가상 네트워크에만 서브넷을 만듭니다. 응용 프로그램 게이트웨이 뒤에 배포하려는 리소스가 아닌 다른 가상 네트워크에 응용 프로그램 게이트웨이를 배포할 수 없습니다.
1. 응용 프로그램 게이트웨이를 사용하도록 구성된 서버가 존재하거나 가상 네트워크나 공용 IP/VIP가 할당된 해당 끝점이 만들어져야 합니다.

## <a name="what-is-required-to-create-an-application-gateway"></a>응용 프로그램 게이트웨이를 만드는 데 필요한 것은 무엇입니까?

* **백 엔드 서버 풀:** 백 엔드 서버의 IP 주소, FQDN 또는 NIC 목록입니다. 사용되는 IP 주소는 가상 네트워크 서브넷에 속하거나 공용 IP/VIP여야 합니다.
* **백 엔드 서버 풀 설정:** 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
* **프런트 엔드 포트:** 이 포트는 응용 프로그램 게이트웨이에 열려 있는 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백 엔드 서버 중의 하나로 리디렉트됩니다.
* **수신기:** 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 이 값은 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다.
* **규칙:** 규칙은 수신기와 백 엔드 서버 풀을 바인딩하고 특정 수신기에 도달했을 때 트래픽이 전달되어야 하는 백 엔드 서버 풀을 정의합니다.

## <a name="create-an-application-gateway"></a>응용 프로그램 게이트웨이 만들기

Azure Classic 및 Azure Resource Manager 간의 차이점은 응용 프로그램 게이트웨이를 만드는 순서와 구성할 항목입니다.

Resource Manager를 사용하면 응용 프로그램 게이트웨이를 만드는 모든 항목이 개별적으로 구성된 후 응용 프로그램 게이트웨이 리소스를 만드는 데 사용됩니다.

다음은 Application Gateway를 만드는 데 필요한 단계입니다.

## <a name="create-a-resource-group-for-resource-manager"></a>리소스 관리자에 대한 리소스 그룹 만들기

Azure PowerShell의 최신 버전을 사용하고 있는지 확인합니다. 자세한 내용은 [Resource Manager에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

### <a name="step-1"></a>1단계:

Azure에 로그인

```powershell
Login-AzureRmAccount
```

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.

### <a name="step-2"></a>2단계:

계정에 대한 구독을 확인합니다.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>3단계:

사용할 Azure 구독을 선택합니다.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>4단계:

리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

위 예제에서는 **appgw-RG**라는 리소스 그룹과 **미국 서부**라는 위치를 만들었습니다.

> [!NOTE]
> 응용 프로그램 게이트웨이에 대한 사용자 지정 프로브를 구성해야 하는 경우 [PowerShell을 사용하여 사용자 지정 프로브로 응용 프로그램 게이트웨이 만들기](application-gateway-create-probe-ps.md)를 참조하세요. 자세한 내용은 [사용자 지정 프로브 및 상태 모니터링](application-gateway-probe-overview.md) 을 확인합니다.

## <a name="create-a-virtual-network-and-a-subnet"></a>가상 네트워크 및 서브넷 만들기

다음 예제에서는 리소스 관리자를 사용하여 가상 네트워크를 만드는 방법을 보여 줍니다. 이 예제에서는 Application Gateway에 대한 VNET을 만듭니다. Application Gateway에는 자체의 서브넷이 필요하며, 이러한 이유로 Application Gateway에 대해 만들어진 서브넷은 VNET 주소 공간보다 작습니다. 더 작은 서브넷을 사용하면 동일한 VNET에서 구성할 웹 서버를 포함하지만 이에 제한되지 않는 다른 리소스를 사용할 수 있습니다.

### <a name="step-1"></a>1단계

주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 데 사용할 서브넷 변수에 할당합니다. 이 단계에서는 다음 예제에서 사용되는 Application Gateway에 대한 서브넷 구성 개체를 만듭니다.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>2단계

접두사 10.0.0.0/16과 서브넷 10.0.0.0/24를 사용하여 미국 서부 지역에 리소스 그룹 **appgw-rg**에서 **appgwvnet**이라는 가상 네트워크를 만듭니다. 이 단계에서는 배치할 Application Gateway에 대한 단일 서브넷을 사용하여 VNET 구성을 완료합니다.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>3단계

다음 단계의 서브넷 변수를 할당합니다. 이 변수는 이후 단계에서 `New-AzureRMApplicationGateway` cmdlet에 전달됩니다.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

미국 서부 지역에 리소스 그룹 **appgw-rg**에서 공용 IP 리소스 **publicIP01**을 만듭니다. Application Gateway는 공용 IP 주소, 내부 IP 주소 또는 둘 다를 사용하여 부하 분산 요청을 받을 수 있습니다.  이 예제에서는 공용 IP 주소만 사용합니다. 다음 예제에는 공용 IP 주소를 만들도록 구성된 DNS 이름이 없습니다.  Application Gateway는 공용 IP 주소에서 사용자 지정 DNS 이름을 지원하지 않습니다.  공용 끝점에 사용자 지정 이름이 필요한 경우 공용 IP 주소에 대해 자동으로 생성된 DNS 이름을 가리키도록 CNAME 레코드를 만들어야 합니다.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

> [!NOTE]
> 서비스를 시작할 때 응용 프로그램 게이트웨이에 IP 주소가 할당됩니다.

## <a name="create-the-application-gateway-configuration-objects"></a>응용 프로그램 게이트웨이 구성 개체 만들기

응용 프로그램 게이트웨이를 만들기 전에 모든 구성 항목을 설정해야 합니다. 다음 단계 응용 프로그램 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

### <a name="step-1"></a>1단계:

**gatewayIP01**이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. 응용 프로그램 게이트웨이는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>2단계

**pool01**이라는 백 엔드 IP 주소 풀을 **pool1**의 IP 주소로 구성합니다. 이러한 IP 주소는 응용 프로그램 게이트웨이에서 보호될 웹 응용 프로그램을 호스팅하는 리소스의 IP 주소입니다. 이러한 백 엔드 풀 멤버는 모두 기본 프로브인지 아니면 사용자 지정 프로브인지에 관계 없이 프로브를 통해 유효성이 정상 상태인 것으로 검사되었습니다.  그런 다음 요청이 응용 프로그램 게이트웨이로 들어오면 트래픽이 라우팅됩니다. 백 엔드 풀은 응용 프로그램 게이트웨이 내에서 여러 규칙을 통해 사용될 수 있습니다. 즉, 하나의 백 엔드 풀이 동일한 호스트에 있는 여러 웹 응용 프로그램에 사용될 수 있습니다.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

이 예제에서는 URL 경로를 기반으로 네트워크 트래픽을 라우트하는 두 개의 백 엔드 풀이 있습니다. 하나의 풀은 URL 경로 "/video"에서 트래픽을 수신하고 또 다른 풀은 경로 "/image"에서 트래픽을 수신합니다. 사용자 고유의 응용 프로그램 IP 주소 끝점을 추가하려면 이전 IP 주소를 바꿉니다.

### <a name="step-3"></a>3단계

백 엔드 풀에서 부하가 분산된 네트워크 트래픽에 대해 응용 프로그램 게이트웨이 설정 **poolsetting**을 구성합니다. 각 백 엔드 풀에는 고유한 백 엔드 풀 설정이 있을 수 있습니다.  백 엔드 HTTP 설정은 올바른 백 엔드 풀 멤버에게 트래픽을 라우팅하는 규칙에서 사용되며, 백 엔드 풀 멤버에 트래픽을 보낼 때 사용되는 프로토콜과 포트를 결정합니다. 쿠키 기반 세션도 백 엔드 HTTP 설정에 따라 결정됩니다.  이 설정이 활성화되면 쿠키 기반 세션 선호도는 각 패킷에 대한 이전 요청과 동일한 백 엔드로 트래픽을 보냅니다.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
```

### <a name="step-4"></a>4단계

응용 프로그램 게이트웨이에 대한 프런트 엔드 포트를 구성합니다. 프런트 엔드 포트 구성 개체는 Application Gateway에서 수신기의 트래픽을 수신 대기하는 포트를 정의하기 위해 수신기에서 사용합니다.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

### <a name="step-5"></a>5단계

공용 IP 끝점으로 프런트 엔드 IP를 구성합니다. 프런트 엔드 IP 구성 개체는 외부 연결 IP 주소와 수신기의 관계를 설정하기 위해 수신기에서 사용합니다.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-6"></a>6단계

수신기를 구성합니다. 이 단계에서는 들어오는 네트워크 트래픽을 수신하는 데 사용되는 공용 IP 주소 및 포트에 대한 수신기를 구성합니다. 다음 예제에서는 이전에 구성한 프런트 엔드 IP 구성, 프런트 엔드 포트 구성 및 프로토콜(http 또는 https)을 사용하여 수신기를 구성합니다. 이 예제에서 수신기는 이전에 만든 공용 IP 주소의 80 포트에서 HTTP 트래픽을 수신 대기합니다.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-7"></a>7단계

부하 분산 장치 동작을 구성하는 **rule01**이라는 부하 분산 장치 라우팅 규칙을 만듭니다. 이전 단계에서 만든 백 엔드 풀 설정, 수신기 및 백 엔드 풀이 규칙을 구성합니다. 정의된 기준에 따라 트래픽이 적절한 백 엔드로 라우팅됩니다.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-8"></a>8단계

응용 프로그램 게이트웨이에 대한 크기 및 인스턴스 수를 구성합니다.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> **InstanceCount** 의 기본값은 2이고, 최대값은 10입니다. **GatewaySize** 의 기본값은 보통입니다. **Standard_Small**, **Standard_Medium** 및 **Standard_Large** 간에 선택할 수 있습니다.

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

이전 단계의 모든 구성 항목으로 응용 프로그램 게이트웨이를 만듭니다. 이 예제에서는 응용 프로그램 게이트웨이를 **appgwtest**라고 합니다.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

응용 프로그램 게이트웨이에 연결된 공용 IP 리소스에서 응용 프로그램 게이트웨이의 DNS 및 VIP 세부 정보를 검색합니다.

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  
```

## <a name="delete-the-application-gateway"></a>응용 프로그램 게이트웨이 삭제

응용 프로그램 게이트웨이를 삭제하려면 다음 단계를 따릅니다.

### <a name="step-1"></a>1단계:

응용 프로그램 게이트웨이 개체를 가져오고 `$getgw` 변수에 연결합니다.

```powershell
$getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>2단계:

`Stop-AzureRmApplicationGateway`를 사용하여 응용 프로그램 게이트웨이를 중지합니다.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw
```

응용 프로그램 게이트웨이가 중지됨 상태가 되면 `Remove-AzureRmApplicationGateway` cmdlet을 사용하여 서비스를 제거합니다.

```powershell
Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force
```

> [!NOTE]
> **-force** 스위치를 사용하여 제거 확인 메시지가 표시되지 않도록 할 수 있습니다.

서비스가 제거되었는지 확인하려면 `Get-AzureRmApplicationGateway` cmdlet을 사용합니다. 이 단계는 필요 하지 않습니다.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

## <a name="get-application-gateway-dns-name"></a>응용 프로그램 게이트웨이 DNS 이름 가져오기

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다. 공용 IP를 사용할 때 Application Gateway는 식별 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 최종 사용자가 Application Gateway를 누를 수 있도록 하려면 CNAME 레코드를 사용하여 Application Gateway의 공용 끝점을 가리키도록 합니다. [Azure에서 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md). 동적으로 만들어진 DNS 이름을 찾으려면 응용 프로그램 게이트웨이에 연결된 PublicIPAddress 요소를 사용하여 응용 프로그램 게이트웨이와 관련 IP/DNS 이름의 세부 정보를 검색합니다. 응용 프로그램 게이트웨이의 DNS 이름은 두 개의 웹 응용 프로그램을 이 DNS 이름으로 가리키는 CNAME 레코드를 만드는 데 사용됩니다. A 레코드를 사용할 경우 응용 프로그램 게이트웨이 다시 시작 시 VIP가 변경될 수 있으므로 이는 권장되지 않습니다.

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

## <a name="delete-all-resources"></a>모든 리소스 삭제

이 문서에서 만든 모든 리소스를 삭제하려면 다음 단계를 완료합니다.

```powershell
Remove-AzureRmResourceGroup -Name appgw-RG
```

## <a name="next-steps"></a>다음 단계

SSL 오프로드를 구성하려는 경우 [SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성](application-gateway-ssl.md)을 참조하세요.

내부 부하 분산 장치에서 사용하도록 응용 프로그램 게이트웨이를 구성하려면 [ILB(내부 부하 분산 장치)를 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-ilb.md)를 참조하세요.

부하 분산 옵션에 대한 자세한 정보는 다음을 방문하세요.

* [Azure 부하 분산 장치](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure 트래픽 관리자](https://azure.microsoft.com/documentation/services/traffic-manager/)


