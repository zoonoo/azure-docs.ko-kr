---
title: "SSL 오프로드 구성 - Azure Application Gateway - PowerShell | Microsoft Docs"
description: "이 문서에서는 Azure Resource Manager를 사용하여 SSL 오프로드와 함께 응용 프로그램 게이트웨이를 만드는 지침을 제공합니다."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 4bcd002a805f1584323f5baf75f791f2fb64201f
ms.contentlocale: ko-kr
ms.lasthandoff: 08/30/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure 클래식 PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway 구성을 사용하여 웹 팜에서 발생하는 비용이 많이 드는 SSL(Secure Sockets Layer) 암호 해독 작업을 방지하기 위한 게이트웨이에서 SSL 세션을 종료합니다. SSL 오프로드는 또한 프런트 엔드 서버 설치 및 웹 응용 프로그램의 관리를 간소화합니다.

## <a name="before-you-begin"></a>시작하기 전에

1. 웹 플랫폼 설치 관리자를 사용하는 Azure PowerShell cmdlet의 최신 버전을 설치합니다. **다운로드 페이지** 의 [Windows PowerShell](https://azure.microsoft.com/downloads/)섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다.
2. 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷을 만듭니다. 서브넷을 사용 중인 가상 컴퓨터 또는 클라우드 배포가 없는지 확인합니다. 응용 프로그램 게이트웨이는 가상 네트워크 서브넷에서 단독이어야 합니다.
3. 응용 프로그램 게이트웨이를 사용하도록 구성된 서버가 존재하거나 가상 네트워크나 공용 IP 주소 또는 VIP(가상 IP 주소)가 할당된 해당 끝점이 만들어져야 합니다.

## <a name="what-is-required-to-create-an-application-gateway"></a>응용 프로그램 게이트웨이를 만드는 데 필요한 것은 무엇입니까?

* **백 엔드 서버 풀**: 백 엔드 서버의 IP 주소 목록입니다. 나열된 IP 주소는 가상 네트워크 서브넷에 속하거나 공용 IP/VIP이어야 합니다.
* **백 엔드 서버 풀 설정**: 모든 풀에는 포트, 프로토콜 및 쿠키 기반의 선호도와 같은 설정이 있습니다. 이러한 설정은 풀에 연결 및 풀 내의 모든 서버에 적용 됩니다.
* **프런트 엔드 포트**: 이 포트는 응용 프로그램 게이트웨이에 열려 있는 공용 포트입니다. 트래픽이 이 포트에 도달하면, 백 엔드 서버 중의 하나로 리디렉트됩니다.
* **수신기**: 수신기에는 프런트 엔드 포트, 프로토콜(Http 또는 Https, 대/소문자 구분) 및 SSL 인증서 이름(SSL 오프로드를 구성하는 경우)이 있습니다.
* **규칙**: 규칙은 수신기와 백 엔드 서버 풀을 바인딩하고 특정 수신기에 도달했을 때 트래픽을 이동하는 백 엔드 서버 풀을 정의합니다. 현재는 *기본* 규칙만 지원 됩니다. *기본* 규칙은 라운드 로빈 부하 분산입니다.

**추가 구성 정보**

SSL 인증서 구성에서 **HttpListener** 의 프로토콜은 **Https** (대/소문자 구분)로 바꿔야 합니다. **SslCertificate** 요소를 SSL 인증서에 대해 구성된 변수 값과 함께 **HttpListener**에 추가합니다. 프런트 엔드 포트는 **443**으로 업데이트되어야 합니다.

**쿠키 기반 선호도를 사용하도록 설정**: 클라이언트 세션의 요청이 항상 웹 팜에 있는 동일한 VM으로 전송되도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. 이를 완료하려면 게이트웨이에서 트래픽을 적절하게 지시할 수 있는 세션 쿠키를 삽입합니다. 쿠키 기반 선호도를 사용하려면 **BackendHttpSettings** 요소에서 **CookieBasedAffinity**를 **Enabled**로 설정합니다.

## <a name="create-an-application-gateway"></a>응용 프로그램 게이트웨이 만들기

Azure 클래식 배포 모델 사용 및 Azure Resource Manager 사용 간의 차이점은 응용 프로그램 게이트웨이를 만드는 순서와 구성할 항목입니다.

리소스 관리자를 사용하면 응용 프로그램 게이트웨이의 모든 구성 요소가 개별적으로 구성된 후 응용 프로그램 게이트웨이 리소스를 만드는 데 사용됩니다.

다음은 응용 프로그램 게이트웨이를 만드는 데 필요한 단계입니다.

1. [리소스 관리자에 대한 리소스 그룹 만들기](#create-a-resource-group-for-resource-manager)
2. [응용 프로그램 게이트웨이에 대한 가상 네트워크, 서브넷 및 공용 IP 만들기](#create-virtual-network-subnet-and-public-IP-for-the-application-gateway)
3. [응용 프로그램 게이트웨이 구성 개체 만들기](#create-an-application-gateway-configuration-object)
4. [응용 프로그램 게이트웨이 리소스 만들기](#create-an-application-gateway-resource)

## <a name="create-a-resource-group-for-resource-manager"></a>리소스 관리자에 대한 리소스 그룹 만들기

Azure 리소스 관리자 cmdlet을 사용하려면 PowerShell 모드로 전환해야 합니다. 자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

   1. 다음 명령을 입력합니다.

   ```powershell
   Login-AzureRmAccount
   ```

   2. 계정에 대한 구독을 확인하려면 다음 명령을 입력합니다.

   ```powershell
   Get-AzureRmSubscription
   ```

   자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.

   3. 사용할 Azure 구독을 선택하려면 다음 명령을 입력합니다.

   ```powershell
   Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
   ```

   4. 리소스 그룹을 만들려면 다음 명령을 입력합니다. (기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뜁니다.)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 설정은 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 응용 프로그램 게이트웨이를 만들기 위한 모든 명령이 동일한 리소스 그룹을 사용하는지 확인합니다.

이전 예제에서는 **appgw-RG**라는 리소스 그룹과 **미국 서부**라는 위치를 만들었습니다.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 예제에서는 Resource Manager를 사용하여 가상 네트워크를 만드는 방법을 보여 줍니다.

   1. 다음 명령을 입력합니다.

   ```powershell
   $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
   ```

   이 샘플은 주소 범위 **10.0.0.0/24**를 가상 네트워크를 만드는 데 사용할 서브넷 변수에 할당합니다.

   2. 다음 명령을 입력합니다.

   ```powershell
   $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

   이 샘플은 접두사 **10.0.0.0/16**과 서브넷 **10.0.0.0/24**를 사용하여 **미국 서부** 지역에 리소스 그룹 **appgw-rg**에서 **appgwvnet**이라는 가상 네트워크를 만듭니다.

   3. 다음 명령을 입력합니다.

   ```powershell
   $subnet = $vnet.Subnets[0]
   ```

   이 샘플은 다음 단계를 위해 **$subnet** 변수에 서브넷 개체를 할당합니다.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>프런트 엔드 구성에 대한 공용 IP 주소 만들기

프런트 엔드 구성에 대한 공용 IP 주소를 만들려면 다음 명령을 입력합니다.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

이 샘플은 **미국 서부** 지역에 리소스 그룹 **appgw-rg**에서 공용 IP 리소스 **publicIP01**을 만듭니다.

## <a name="create-an-application-gateway-configuration-object"></a>응용 프로그램 게이트웨이 구성 개체 만들기

   1. 응용 프로그램 게이트웨이 구성 개체를 만들려면 다음 명령을 입력합니다.

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
   ```

   이 샘플은 **gatewayIP01**이라는 Application Gateway IP 구성을 만듭니다. Application Gateway가 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

   2. 다음 명령을 입력합니다.

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
   ```

   이 샘플은 IP 주소가 **134.170.185.46**, **134.170.188.221** 및 **134.170.185.50**인 **pool01**이라는 백 엔드 IP 주소 풀을 구성합니다. 이러한 값은 프런트 엔드 IP 끝점에서 들어오는 네트워크 트래픽을 수신하는 IP 주소입니다. 앞의 예제에서 IP 주소를 웹 응용 프로그램 끝점의 IP 주소로 대체합니다.

   3. 다음 명령을 입력합니다.

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
   ```

   이 샘플은 백 엔드 풀에서 부하가 분산된 네트워크 트래픽에 대해 Application Gateway 설정 **poolsetting01**을 구성합니다.

   4. 다음 명령을 입력합니다.

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
   ```

   이 샘플은 공용 IP 끝점에 대해 **frontendport01**이라는 프런트 엔드 IP 포트를 구성합니다.

   5. 다음 명령을 입력합니다.

   ```powershell
   $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
   ```

   이 샘플은 SSL 연결에 사용된 인증서를 구성합니다. 인증서는 PFX 형식으로 4~12자 사이의 암호이어야 합니다.

   6. 다음 명령을 입력합니다.

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
   ```

   이 샘플은 **fipconfig01**이라는 프런트 엔드 IP 구성을 만들고 프런트 엔드 IP 구성에 공용 IP 주소를 연결합니다.

   7. 다음 명령을 입력합니다.

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
   ```

   이 샘플은 **listener01**이라는 수신기를 만들고 프런트 엔드 IP 구성 및 인증서에 프런트 엔드 포트를 연결합니다.

   8. 다음 명령을 입력합니다.

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   이 샘플은 **rule01**이라는 부하 분산 장치 라우팅 규칙을 만들고 부하 분산 장치 동작을 구성합니다.

   9. 다음 명령을 입력합니다.

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   이 샘플은 응용 프로그램 게이트웨이의 인스턴스 크기를 구성합니다.

   > [!NOTE]
   > **InstanceCount**의 기본값은 **2**이고, 최대값은 10입니다. **GatewaySize**에 대한 기본값은 **보통**입니다. Standard_Small, Standard_Medium 및 Standard_Large 간에 선택할 수 있습니다.

   10. 다음 명령을 입력합니다.

   ```powershell
   $policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
   ```

   이 단계는 Application Gateway에서 사용할 SSL 정책을 정의합니다. 자세한 내용은 [Application Gateway에서 SSL 정책 버전 및 암호 그룹 구성](application-gateway-configure-ssl-policy-powershell.md)을 참조하세요.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>New-AzureApplicationGateway를 사용하여 응용 프로그램 게이트웨이 만들기

다음 명령을 입력합니다.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

이 샘플은 이전 단계의 모든 구성 항목을 가진 응용 프로그램 게이트웨이를 만듭니다. 이 예제에서는 Application Gateway를 **appgwtest**라고 합니다.

## <a name="get-the-application-gateway-dns-name"></a>응용 프로그램 게이트웨이 DNS 이름 가져오기

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다. 공용 IP를 사용할 때 Application Gateway는 친근한 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 최종 사용자가 응용 프로그램 게이트웨이를 누를 수 있도록 하려면 CNAME 레코드를 사용하여 응용 프로그램 게이트웨이의 공용 끝점을 가리키도록 합니다. 자세한 내용은 [Azure에서 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요. 

응용 프로그램 게이트웨이 DNS 이름을 가져오려면 응용 프로그램 게이트웨이에 연결된 **PublicIPAddress** 요소를 사용하여 응용 프로그램 게이트웨이와 관련 IP/DNS 이름의 세부 정보를 검색합니다. 응용 프로그램 게이트웨이의 DNS 이름을 사용하여 두 개의 웹 응용 프로그램을 이 DNS 이름으로 가리키는 CNAME 레코드를 만듭니다. A 레코드를 사용할 경우 응용 프로그램 게이트웨이 다시 시작 시 VIP가 변경될 수 있으므로 이는 권장되지 않습니다.


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

내부 부하 분산 장치에서 사용되도록 응용 프로그램 게이트웨이를 구성하려면 [내부 부하 분산 장치를 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-ilb.md)를 참조하세요.

일반적 부하 분산 옵션에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 부하 분산 장치](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

