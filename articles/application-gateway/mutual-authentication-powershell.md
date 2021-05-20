---
title: PowerShell을 통해 Azure Application Gateway에서 상호 인증 구성
description: PowerShell을 통해 상호 인증을 갖도록 Application Gateway를 구성하는 방법 알아보기
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fdebc0cf22b317fa33b5d65383ef3bcc9b57e905
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108315978"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-powershell-preview"></a>PowerShell을 통해 Application Gateway를 사용하여 상호 인증 구성(미리 보기)
이 문서에서는 PowerShell을 사용하여 Application Gateway에서 상호 인증을 구성하는 방법을 설명합니다. 상호 인증은 Application Gateway가 Application Gateway에 업로드한 클라이언트 인증서를 사용하여 요청을 보내는 클라이언트를 인증함을 의미합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 문서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="before-you-begin"></a>시작하기 전에

Application Gateway를 사용하여 상호 인증을 구성하려면 게이트웨이에 업로드할 클라이언트 인증서가 필요합니다. 클라이언트 인증서는 클라이언트가 Application Gateway에 제공할 인증서의 유효성을 검사하는 데 사용됩니다. 테스트용으로 자체 서명된 인증서를 만들 수 있습니다. 그러나 이 방법은 관리가 어렵고 완전히 안전하지 않으므로 프로덕션 워크로드에는 권장되지 않습니다.

특히 업로드할 수 있는 클라이언트 인증서의 종류에 대한 자세한 내용은 [Application Gateway를 사용한 상호 인증 개요](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

먼저 구독에 새 리소스 그룹을 만듭니다. 

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location -Tags @{ testtag = "APPGw tag"}
```
## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

Application Gateway에 배포할 가상 네트워크를 배포합니다.

```azurepowershell
$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet
$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -VirtualNetwork $vnet
```

## <a name="create-a-public-ip"></a>공용 IP 만들기

Application Gateway에서 사용할 공용 IP를 만듭니다. 

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -sku Standard
```

## <a name="create-the-application-gateway-ip-configuration"></a>Application Gateway IP 구성 만들기

IP 구성 및 프론트 엔드 포트를 만듭니다. 

```azurepowershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip
$port = New-AzApplicationGatewayFrontendPort -Name $frontendPortName  -Port 443
```

## <a name="configure-frontend-ssl"></a>프런트 엔드 SSL 구성 

Application Gateway에 대한 SSL 인증서를 구성합니다.

```azurepowershell
$password = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
$sslCertPath = $basedir + "/ScenarioTests/Data/ApplicationGatewaySslCert1.pfx"
$sslCert = New-AzApplicationGatewaySslCertificate -Name $sslCertName -CertificateFile $sslCertPath -Password $password
```

## <a name="configure-client-authentication"></a>클라이언트 인증 구성 

Application Gateway에서 클라이언트 인증을 구성합니다. 여기에서 사용할 신뢰할 수 있는 클라이언트 CA 인증서 체인을 추출하는 방법에 대한 자세한 내용은 [신뢰할 수 있는 클라이언트 CA 인증서 체인을 추출하는 방법](./mutual-authentication-certificate-management.md)을 참조하세요.

> [!IMPORTANT]
> 전체 클라이언트 CA 인증서 체인을 한 파일에 업로드하고 파일당 하나의 체인만 업로드하는지 확인하세요.  

> [!NOTE]
> TLS 1.2는 나중에 위임될 것이므로 상호 인증과 함께 TLS 1.2를 사용하는 것이 좋습니다. 

```azurepowershell
$clientCertFilePath = $basedir + "/ScenarioTests/Data/TrustedClientCertificate.cer"
$trustedClient01 = New-AzApplicationGatewayTrustedClientCertificate -Name $trustedClientCert01Name -CertificateFile $clientCertFilePath
$sslPolicy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S"
$clientAuthConfig = New-AzApplicationGatewayClientAuthConfiguration -VerifyClientCertIssuerDN
$sslProfile01 = New-AzApplicationGatewaySslProfile -Name $sslProfile01Name -SslPolicy $sslPolicy -ClientAuthConfiguration $clientAuthConfig -TrustedClientCertificates $trustedClient01
$listener = New-AzApplicationGatewayHttpListener -Name $listenerName -Protocol Https -SslCertificate $sslCert -FrontendIPConfiguration $fipconfig -FrontendPort $port -SslProfile $sslProfile01
```

## <a name="configure-the-backend-pool-and-settings"></a>백 엔드 풀 및 설정 구성

Application Gateway에 대한 백 엔드 풀 및 설정을 설정합니다. 필요에 따라 엔드투엔드 SSL 암호화를 위한 신뢰할 수 있는 백 엔드 루트 인증서를 설정합니다.  

```azurepowershell
$certFilePath = $basedir + "/ScenarioTests/Data/ApplicationGatewayAuthCert.cer"
$trustedRoot = New-AzApplicationGatewayTrustedRootCertificate -Name $trustedRootCertName -CertificateFile $certFilePath
$pool = New-AzApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses www.microsoft.com, www.bing.com
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name $poolSettingName -Port 443 -Protocol Https -CookieBasedAffinity Enabled -PickHostNameFromBackendAddress -TrustedRootCertificate $trustedRoot
```

## <a name="configure-the-rule"></a>규칙 구성

Application Gateway에 대한 규칙을 설정합니다.

```azurepowershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

## <a name="set-up-default-ssl-policy-for-future-listeners"></a>이후 수신기에 대한 기본 SSL 정책 설정

상호 인증을 설정하는 동안 수신기별 SSL 정책을 설정했습니다. 이 단계에서는 필요에 따라 사용자가 만드는 이후 수신기에 대한 기본 SSL 정책을 설정할 수 있습니다. 

```azurepowershell
$sslPolicyGlobal = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401"
```

## <a name="create-the-application-gateway"></a>Application Gateway 만들기

위에서 만든 모든 항목을 사용하여 Application Gateway를 배포합니다.

```azurepowershell
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Zone 1,2 -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $port -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicyGlobal -TrustedRootCertificate $trustedRoot -AutoscaleConfiguration $autoscaleConfig -TrustedClientCertificates $trustedClient01 -SslProfiles $sslProfile01 -SslCertificates $sslCert
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, 애플리케이션 게이트웨이 및 모든 관련 리소스를 제거합니다.

```azurepowershell
Remove-AzResourceGroup -Name $rgname
```

## <a name="renew-expired-client-ca-certificates"></a>만료된 클라이언트 CA 인증서 갱신

클라이언트 CA 인증서가 만료된 경우 다음 단계를 통해 게이트웨이에서 인증서를 업데이트할 수 있습니다. 

1. Azure에 로그인
    ```azurepowershell
    Connect-AzAccount
    Select-AzSubscription -Subscription "<sub name>"
    ```
2. Application Gateway 구성 가져오기
    ```azurepowershell
    $gateway = Get-AzApplicationGateway -Name "<gateway-name>" -ResourceGroupName "<resource-group-name>"
    ```
3. 게이트웨이에서 신뢰할 수 있는 클라이언트 인증서 제거 
    ```azurepowershell
    Remove-AzApplicationGatewayTrustedClientCertificate -Name "<name-of-client-certificate>" -ApplicationGateway $gateway
    ``` 
4. 게이트웨이에 새 인증서 추가 
    ```azurepowershell
    Add-AzApplicationGatewayTrustedClientCertificate -ApplicationGateway $gateway -Name "<name-of-new-cert>" -CertificateFile "<path-to-certificate-file>"
    ```
5. 새 인증서로 게이트웨이 업데이트 
    ```azurepowershell
    Set-AzApplicationGateway -ApplicationGateway $gateway
    ```

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)
