---
title: 외부 리디렉션을 포함하는 애플리케이션 게이트웨이 만들기 - Azure PowerShell | Microsoft Docs
description: Azure Powershell을 사용하여 웹 트래픽을 외부 사이트로 리디렉션하는 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: victorh
ms.openlocfilehash: e2c3dbef6204c1b6b75c362ead066b3465a8a5a9
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307057"
---
# <a name="create-an-application-gateway-with-external-redirection-using-azure-powershell"></a>Azure PowerShell을 사용하여 외부 리디렉션을 포함하는 애플리케이션 게이트웨이 만들기

Azure PowerShell을 사용하여 [애플리케이션 게이트웨이](application-gateway-introduction.md)를 만들 때 [웹 트래픽 리디렉션](application-gateway-multi-site-overview.md)을 구성할 수 있습니다. 이 자습서에서는 애플리케이션 게이트웨이에 도착하는 웹 트래픽을 외부 사이트에 리디렉션하는 수신기 및 규칙을 구성합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 수신기 및 리디렉션 규칙 만들기
> * 애플리케이션 게이트웨이 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 설치 하 고 PowerShell을 로컬로 사용 하려는 경우 이상. 버전을 확인하려면 ` Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 사용 하 여 Azure 리소스 그룹을 만듭니다 [새로 만들기-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)합니다.  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

서브넷 구성을 만듭니다 *myAGSubnet* 사용 하 여 [새로 만들기-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)합니다. 이라는 가상 네트워크를 만듭니다 *myVNet* 사용 하 여 [새로 만들기-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 서브넷 구성을 사용 하 여 합니다. 마지막으로 사용 하 여 공용 IP 주소를 만듭니다 [새로 만들기-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)합니다. 이러한 리소스는 애플리케이션 게이트웨이 및 연결된 리소스에 대한 네트워크 연결을 제공하는 데 사용됩니다.

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP 구성 및 프론트 엔드 포트 만들기

연결할 *myAGSubnet* 사용 하 여 응용 프로그램 게이트웨이를 이전에 만든 [새로 만들기-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration)합니다. 공용 IP 주소를 사용 하 여 응용 프로그램 게이트웨이에 할당할 [새로 만들기-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)합니다. 사용 하 여 HTTP 포트를 만들 수 있습니다 [새로 만들기-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport)합니다.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>백 엔드 풀 및 설정 만들기

이라는 백 엔드 풀을 만듭니다 *defaultPool* 사용 하 여 응용 프로그램 게이트웨이 [새로 만들기-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)합니다. 사용 하 여 풀 설정을 구성 [새로 만들기-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings)합니다.

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name defaultPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-rule"></a>수신기 및 규칙 만들기

애플리케이션 게이트웨이에서 트래픽을 적절히 라우팅하려면 수신기가 필요합니다. 사용 하 여 수신기를 만듭니다 [새로 만들기-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) 프런트 엔드 구성 및 이전에 만든 프런트 엔드 포트를 사용 하 여 합니다. 만든 라우팅 규칙에 추가할 수 있는 리디렉션 구성을 만들어 트래픽을 리디렉션할 수 있습니다.

수신기가 들어오는 트래픽을 보낼 위치를 알려면 라우팅 규칙이 필요합니다. 이라는 기본 규칙을 만듭니다 *redirectRule* 사용 하 여 [새로 만들기-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) 리디렉션 구성을 포함 합니다.

```azurepowershell-interactive
$defaultListener = New-AzApplicationGatewayHttpListener `
  -Name defaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$redirectConfig = New-AzApplicationGatewayRedirectConfiguration `
  -Name myredirect `
  -RedirectType Temporary `
  -TargetUrl "https://bing.com"
$redirectRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name redirectRule `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -RedirectConfiguration $redirectConfig
```

### <a name="create-the-application-gateway"></a>Application Gateway 만들기

이제 필요한 지원 리소스를 만들었으므로 라는 응용 프로그램 게이트웨이에 대 한 매개 변수를 지정할 *myAppGateway* 를 사용 하 여 [새로 만들기-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), 하 를사용하여만들어야[AzApplicationGateway 새](/powershell/module/az.network/new-azapplicationgateway)합니다.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $redirectRule `
  -RedirectConfigurations $redirectConfig `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

사용할 수 있습니다 [Get AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) 응용 프로그램 게이트웨이의 공용 IP 주소를 가져오려고 합니다. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

브라우저에 *bing.com*이 표시되어야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 네트워크 설정
> * 수신기 및 리디렉션 규칙 만들기
> * 애플리케이션 게이트웨이 만들기

> [!div class="nextstepaction"]
> [애플리케이션 게이트웨이 통해 수행할 수 있는 작업에 대한 자세한 정보](./application-gateway-introduction.md)
