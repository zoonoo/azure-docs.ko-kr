---
title: 예약된 IP 주소로 자동 크기 조정, 영역 중복 응용 프로그램 게이트웨이 만들기 - Azure PowerShell
description: Azure Powershell을 사용하여 예약된 IP 주소로 자동 크기 조정, 영역 중복 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 9/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d86ce2e1bac2fb58df8df748381a00eac21e65cb
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48016937"
---
# <a name="tutorial-create-an-autoscaling-zone-redundant-application-gateway-with-a-reserved-virtual-ip-address-using-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 예약된 가상 IP 주소로 자동 크기 조정, 영역 중복 응용 프로그램 게이트웨이 만들기

이 자습서에서는 Azure PowerShell cmdlet 및 Azure Resource Manager 배포 모델을 사용하여 Azure Application Gateway를 만드는 방법에 대해 설명합니다. 이 자습서는 기존 표준 SKU와 비교하여 새 자동 크기 조정 SKU의 차이점에 중점을 둡니다. 특히 자동 크기 조정, 영역 중복 및 예약된 VIP(고정 IP)를 지원하는 기능에 집중합니다.

응용 프로그램 게이트웨이 자동 크기 조정 및 영역 중복에 대한 자세한 내용은 [자동 크기 조정 및 영역 중복 Application Gateway(공개 미리 보기)](application-gateway-autoscaling-zone-redundant.md)를 참조하세요.

> [!IMPORTANT]
> 자동 크기 조정 및 영역 중복 응용 프로그램 게이트웨이 SKU는 현재 공개 미리 보기로 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 자동 크기 조정 구성 매개 변수 설정
> * 영역 매개 변수 사용
> * 정적 VIP 사용
> * Application Gateway 만들기


Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 자습서에서는 Azure PowerShell을 로컬로 실행해야 합니다. Azure PowerShell 모듈 버전 6.9.0 이상이 설치되어 있어야 합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)를 참조하세요. PowerShell 버전을 확인한 후 `Login-AzureRmAccount`를 실행하여 Azure와의 연결을 만듭니다.

## <a name="sign-in-to-your-azure-account"></a>Azure 계정에 로그인

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -Subscription "<sub name>"
```
## <a name="create-a-resource-group"></a>리소스 그룹 만들기
사용 가능한 위치 중 하나에 리소스 그룹을 만듭니다.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzureRmResourceGroup -Name $rg -Location $location
```

## <a name="create-a-vnet"></a>VNet 만들기
자동 크기 조정 응용 프로그램 게이트웨이에 대한 하나의 전용 서브넷이 있는 VNet을 만듭니다. 현재 각 전용 서브넷에는 하나의 자동 크기 조정 응용 프로그램 게이트웨이만 배포할 수 있습니다.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>예약된 공용 IP 만들기

PublicIPAddress의 할당 메서드를 **Static**으로 지정합니다. 자동 크기 조정 응용 프로그램 게이트웨이 VIP는 정적일 수만 있습니다. 동적 IP는 지원되지 않습니다. 표준 PublicIpAddress SKU만 지원됩니다.

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>세부 정보 검색

응용 프로그램 게이트웨이 IP 구성 세부 정보를 만들기 위해 로컬 개체에서 리소스 그룹, 서브넷 및 IP에 대한 세부 정보를 검색합니다.

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```
## <a name="configure-application-gateway-infrastructure"></a>응용 프로그램 게이트웨이 인프라 구성
IP 구성, 프런트 엔드 IP 구성, 백 엔드 풀, http 설정, 인증서, 포트, 수신기 및 규칙을 기존 표준 Application Gateway와 동일한 형식으로 구성합니다. 새 SKU는 표준 SKU와 동일한 개체 모델을 따릅니다.

```azurepowershell
$ipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzureRmApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzureRmApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "scrap" -AsPlainText -Force
$sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "D:\Networking\ApplicationGateway\scrap.pfx"
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>자동 크기 조정 지정

이제 응용 프로그램 게이트웨이에 대한 자동 크기 조정 구성을 지정할 수 있습니다. 지원되는 두 가지 자동 크기 조정 구성 유형은 다음과 같습니다.

- **고정 용량 모드**. 이 모드에서는 응용 프로그램 게이트웨이가 자동으로 크기 조정되지 않고 고정된 배율 단위 용량에서 작동합니다.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```
- **자동 크기 조정 모드**. 이 모드에서는 응용 프로그램 게이트웨이가 응용 프로그램 트래픽 패턴에 따라 자동으로 크기 조정됩니다.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

Application Gateway를 만들고 중복 영역을 포함합니다. 

영역 구성은 Azure 영역을 사용할 수 있는 지역에서만 지원됩니다. Azure 영역을 사용할 수 없는 지역에서는 영역 매개 변수를 사용할 수 없습니다. 또한 응용 프로그램 게이트웨이는 단일 영역, 두 영역 또는 세 영역 모두에 배포할 수도 있습니다. 단일 영역 응용 프로그램 게이트웨이에 대한 PublicIPAddress는 동일한 영역에 바인딩되어야 합니다. 2-3개 영역 중복 응용 프로그램 게이트웨이의 경우 PublicIPAddress도 영역 중복되어야 하므로 영역이 지정되지 않습니다.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>응용 프로그램 게이트웨이 테스트

[Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 응용 프로그램 게이트웨이의 공용 IP 주소를 가져옵니다. 공용 IP 주소 또는 DNS 이름을 복사한 다음, 브라우저의 주소 표시줄에 붙여넣습니다.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>리소스 정리
먼저 응용 프로그램 게이트웨이를 사용하여 만든 리소스를 탐색한 다음, 더 이상 필요하지 않은 경우 `Remove-AzureRmResourceGroup` 명령을 사용하여 리소스 그룹, 응용 프로그램 게이트웨이 및 모든 관련 리소스를 제거할 수 있습니다.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 정적 VIP 사용
> * 자동 크기 조정 구성 매개 변수 설정
> * 영역 매개 변수 사용
> * Application Gateway 만들기

> [!div class="nextstepaction"]
> [URL 경로 기반 라우팅 규칙을 사용하여 응용 프로그램 게이트웨이 만들기](./tutorial-url-route-powershell.md)
