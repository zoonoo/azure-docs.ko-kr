---
title: "사용자 지정 프로브 만들기 - Azure Application Gateway - PowerShell | Microsoft Docs"
description: "리소스 관리자에서 PowerShell을 사용하여 응용 프로그램 게이트웨이에 대한 사용자 지정 프로브를 만드는 방법에 대해 알아봅니다."
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 794797d9c42ec7f2fc351bab109147e45ce06070


---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Azure 리소스 관리자에 대해 PowerShell을 사용하여 Azure 응용 프로그램 게이트웨이에 대한 사용자 지정 프로브 만들기

> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 클래식 PowerShell](application-gateway-create-probe-classic-ps.md)

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다.  이 문서에서는 Resource Manager 배포 모델 사용을 설명하며 Microsoft에서는 대부분의 새로운 배포에 대해 [클래식 배포 모델](application-gateway-create-probe-classic-ps.md) 대신 이 모델을 사용하도록 권장합니다.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>1단계:

`Login-AzureRmAccount`을 사용하여 인증합니다.

```powershell
Login-AzureRmAccount
```

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

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷 만들기

다음 단계는 응용 프로그램 게이트웨이에 대한 가상 네트워크 및 서브넷을 만듭니다.

### <a name="step-1"></a>1단계

주소 범위 10.0.0.0/24를 가상 네트워크를 만드는 데 사용할 서브넷 변수에 할당합니다.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>2단계

접두사 10.0.0.0/16과 서브넷 10.0.0.0/24를 사용하여 미국 서부 지역에 리소스 그룹 **appgw-rg**에서 **appgwvnet**이라는 가상 네트워크를 만듭니다.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>3단계:

응용 프로그램 게이트웨이를 만드는 다음 단계에 서브넷 변수를 할당합니다.

```powershell
$subnet = $vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>프런트 엔드 구성에 대한 공용 IP 주소 만들기

미국 서부 지역에 리소스 그룹 **appgw-rg**에서 공용 IP 리소스 **publicIP01**을 만듭니다.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>사용자 지정 프로브를 사용하여 응용 프로그램 게이트웨이 구성 개체 만들기

Application Gateway를 만들기 전에 모든 구성 항목을 설정합니다. 다음 단계 응용 프로그램 게이트웨이 리소스에 필요한 구성 항목을 만듭니다.

### <a name="step-1"></a>1단계:

**gatewayIP01**이라는 응용 프로그램 게이트웨이 IP 구성을 만듭니다. 응용 프로그램 게이트웨이는 시작되면 구성된 서브넷에서 IP 주소를 선택하고 백 엔드 IP 풀의 IP 주소로 네트워크 트래픽을 라우팅합니다. 인스턴스마다 하나의 IP 주소를 사용합니다.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>2단계

IP 주소가 **134.170.185.46, 134.170.188.221, 134.170.185.50**인 **pool01**이라는 백 엔드 IP 주소 풀을 구성합니다. 이러한 값은 프런트 엔드 IP 끝점에서 들어오는 네트워크 트래픽을 수신하는 IP 주소입니다. 사용자 고유의 응용 프로그램 IP 주소 끝점을 추가하려면 위의 IP 주소를 바꿉니다.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

### <a name="step-3"></a>3단계

이 단계에서는 사용자 지정 프로브를 구성합니다.

사용되는 매개 변수:

* **Interval** - 프로브 간격 확인(초)을 구성합니다.
* **Timeout** - HTTP 응답 확인을 위한 프로브 시간 제한을 정의합니다.
* **호스트 이름 및 경로** - Application Gateway가 인스턴스의 상태를 확인하기 위해 호출하는 전체 URL 경로입니다. 예: 웹 사이트가 **http://contoso.com/**인 경우 프로브를 확인하여 성공적으로 HTTP에 응답하도록 **http://contoso.com/path/custompath.htm**에 대해 사용자 지정 프로브를 구성할 수 있습니다.
* **UnhealthyThreshold** - 백 엔드 인스턴스를 **unhealthy**로 표시하는 데 필요한 실패한 HTTP 응답 수입니다.

```powershell
$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-4"></a>4단계

백 엔드 풀에서 트래픽에 대해 Application Gateway 설정 **poolsetting01**을 구성합니다. 또한 이 단계에서 응용 프로그램 게이트웨이 요청에 대한 백 엔드 풀 응답의 시간 제한을 구성합니다. 백 엔드 응답이 시간 제한에 도달하면 응용 프로그램 게이트웨이 요청을 취소합니다. 이 값은 프로브 확인에 대한 백 엔드 응답만을 위한 프로브 시간 제한과는 다릅니다.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80
```

### <a name="step-5"></a>5단계

공용 IP 끝점에 대해 **frontendport01**이라는 프런트 엔드 IP 포트를 구성합니다.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80
```

### <a name="step-6"></a>6단계

**fipconfig01**이라는 프런트 엔드 IP 구성을 만들고 프런트 엔드 IP 구성에 공용 IP 주소를 연결합니다.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-7"></a>7단계

**listener01**이라는 수신기를 만들고 프런트 엔드 IP 구성에 프런트 엔드 포트를 연결합니다.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-8"></a>8단계

부하 분산 장치 동작을 구성하는 **rule01**이라는 부하 분산 장치 라우팅 규칙을 만듭니다.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-9"></a>9단계

응용 프로그램 게이트웨이의 인스턴스 크기를 구성합니다.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> **InstanceCount** 의 기본값은 2이고, 최대값은 10입니다. **GatewaySize** 의 기본값은 보통입니다. **Standard_Small**, **Standard_Medium** 및 **Standard_Large** 간에 선택할 수 있습니다. 

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>New-AzureRmApplicationGateway를 사용하여 응용 프로그램 게이트웨이 만들기

위 단계의 모든 구성 항목으로 응용 프로그램 게이트웨이를 만듭니다. 이 예제에서는 응용 프로그램 게이트웨이를 **appgwtest**라고 합니다.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>기존 응용 프로그램 게이트웨이에 프로브 추가

다음 네 단계에 따라 기존 응용 프로그램 게이트웨이에 사용자 지정 프로브를 추가합니다.

### <a name="step-1"></a>1단계

`Get-AzureRmApplicationGateway`를 사용하여 PowerShell 변수에 Application Gateway 리소스를 로드합니다.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>2단계

기존 게이트웨이 구성에 프로브를 추가합니다.

```powershell
$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

예제에서 사용자 지정 프로브는 URL 경로 contoso.com/path/custompath.htm에 대해 30초마다 확인하도록 구성되어 있습니다. 시간 제한 임계값 120초가 실패한 프로브 요청 최대 수 8과 함께 구성됩니다.

### <a name="step-3"></a>3단계

`Set-AzureRmApplicationGatewayBackendHttpSettings`을 사용하여 백 엔드 풀 설정 구성 및 시간 제한에 프로브를 추가합니다.

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120
```

### <a name="step-4"></a>4단계

`Set-AzureRmApplicationGateway`를 사용하여 Application Gateway에 구성을 저장합니다.

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>기존 응용 프로그램 게이트웨이에서 프로브 제거

다음은 기존 응용 프로그램 게이트웨이에서 사용자 지정 프로브를 제거하는 단계입니다.

### <a name="step-1"></a>1단계

`Get-AzureRmApplicationGateway`를 사용하여 PowerShell 변수에 Application Gateway 리소스를 로드합니다.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>2단계

`Remove-AzureRmApplicationGatewayProbeConfig`를 사용하여 Application Gateway에서 프로브 구성을 제거합니다.

```powershell
$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name
```

### <a name="step-3"></a>3단계

백 엔드 풀 설정을 업데이트하여 `Set-AzureRmApplicationGatewayBackendHttpSettings`를 사용하여 프로브 및 시간 제한 설정을 제거합니다.

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled
```

### <a name="step-4"></a>4단계

`Set-AzureRmApplicationGateway`를 사용하여 Application Gateway에 구성을 저장합니다. 

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>응용 프로그램 게이트웨이 DNS 이름 가져오기

게이트웨이가 생성되면 다음 단계는 통신에 대한 프런트 엔드를 구성하는 것입니다. 공용 IP를 사용할 때 Application Gateway는 식별 이름이 아닌 동적으로 할당된 DNS 이름이 필요합니다. 최종 사용자가 Application Gateway를 누를 수 있도록 하려면 CNAME 레코드를 사용하여 Application Gateway의 공용 끝점을 가리키도록 합니다. [Azure에서 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md). 이 작업을 수행하려면 Application Gateway에 연결된 PublicIPAddress 요소를 사용하여 Application Gateway 및 관련 IP/DNS 이름에 대한 세부 정보를 검색합니다. 응용 프로그램 게이트웨이의 DNS 이름은 두 개의 웹 응용 프로그램을 이 DNS 이름으로 가리키는 CNAME 레코드를 만드는 데 사용됩니다. A 레코드를 사용할 경우 응용 프로그램 게이트웨이 다시 시작 시 VIP가 변경될 수 있으므로 이는 권장되지 않습니다.

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

[SSL 오프로드 구성](application-gateway-ssl-arm.md)을 방문하여 SSL 오프로드를 구성하는 방법을 알아봅니다.




<!--HONumber=Jan17_HO4-->


