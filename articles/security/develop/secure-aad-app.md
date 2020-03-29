---
title: 안전한 Azure AD 웹 응용 프로그램 개발 | 마이크로 소프트 문서
description: 이 간단한 샘플 앱은 Azure에서 개발할 때 응용 프로그램 및 조직의 보안 상태를 개선하는 보안 모범 사례를 구현합니다.
keywords: na
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 11bf7c0ae05c2e52d59efb32be47ce6bd96fac4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937974"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Azure AD 앱용 보안 앱 개발
## <a name="overview"></a>개요

이 샘플은 Azure에서 앱을 개발하기 위한 보안 리소스에 연결되는 웹 앱이 있는 간단한 Azure Active Directory입니다. 앱은 Azure에서 앱을 개발할 때 응용 프로그램 및 조직의 보안 상태를 개선하는 데 도움이 되는 보안 모범 사례를 구현합니다.

배포 스크립트는 인프라를 설정합니다. 배포 스크립트를 실행한 후 Azure Portal에서 구성 요소와 서비스를 함께 연결하기 위해 몇 가지 수동 구성을 수행해야 합니다. 이 샘플은 소매 업계에서 작업하고 안전한 Azure 인프라를 사용하여 보안 Azure Active Directory를 구축하려는 Azure의 숙련된 개발자를 대상으로 합니다. 


이 앱을 개발하고 배포할 때, 
- Azure Key Vault 인스턴스를 만들고, 저장하고, 비밀에서 검색합니다.
- 프런트 엔드 방화벽 액세스를 통해 격리된 Azure 웹 앱을 배포합니다. 
- OWASP 상위 10개 규칙 집합을 사용하는 방화벽을 사용하여 Azure 응용 프로그램 게이트웨이 인스턴스를 만들고 구성합니다. 
- Azure 서비스를 사용하여 전송 중 및 미사용 데이터의 암호화를 사용하도록 설정합니다. 
- Azure 정책 및 보안 센터를 설정하여 준수 여부를 평가합니다. 

이 앱을 개발하고 배포한 후에는 설명된 구성 및 보안 조치와 함께 다음 샘플 웹 앱을 설정하게 됩니다.

## <a name="architecture"></a>Architecture
응용 프로그램은 세 계층과 일반적인 n 계층 응용 프로그램입니다. 모니터링 및 비밀 관리 구성 요소가 통합된 프런트 엔드, 백 엔드 및 데이터베이스 계층은 다음과 같습니다.

![앱 아키텍처](./media/secure-aad-app/architecture.png)

이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 배포 아키텍처에 대한 자세한 내용은 배포 아키텍처 섹션에 있습니다. 

아키텍처는 이러한 구성 요소로 구성됩니다.

- [Azure Application Gateway](../../application-gateway/index.yml). 응용 프로그램 아키텍처에 대한 게이트웨이 및 방화벽을 제공합니다.
- [응용 프로그램 인사이트](../../azure-monitor/app/app-insights-overview.md). 여러 플랫폼에서 확장 가능한 응용 프로그램 성능 관리(APM) 서비스를 제공합니다.
- [Azure 키 볼트](../../key-vault/index.yml). 앱의 비밀을 저장하고 암호화하고 앱 주변의 액세스 정책 생성을 관리합니다.
- [Azure 활성 디렉터리](../../active-directory/fundamentals/active-directory-whatis.md). 클라우드 기반 ID 및 액세스 관리 서비스, 로그인 및 리소스 액세스 서비스를 제공합니다.
- [Azure 도메인 이름 시스템](../../dns/dns-overview.md). 도메인을 호스트하는 서비스를 제공합니다.
- [Azure 로드 밸러터](../../load-balancer/load-balancer-overview.md). 응용 프로그램을 확장하고 서비스에 대한 고가용성을 생성할 수 있습니다.
- [Azure 웹 앱](../../app-service/overview.md).  웹 응용 프로그램을 호스팅하기 위한 HTTP 기반 서비스를 제공합니다.
- [Azure 보안 센터](../../security-center/index.yml). 클라우드의 하이브리드 워크로드 전반에 걸쳐 고급 위협 보호 기능을 제공합니다.
- [Azure 정책](../../governance/policy/overview.md). 할당된 정책을 준수하지 않는지 리소스를 평가합니다.

## <a name="threat-model"></a>위협 모델
위협 모델링은 비즈니스 및 응용 프로그램에 대한 잠재적보안 위협을 식별한 다음 적절한 완화 계획을 수립하는 프로세스입니다.

이 샘플에서는 [Microsoft 위협 모델링 도구를](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) 사용하여 보안 샘플 앱에 대한 위협 모델링을 구현했습니다. 구성 요소와 데이터 흐름을 다이어그램으로 만들어 개발 프로세스 초기에 문제와 위협을 식별할 수 있습니다. 시간과 돈을 나중에 이것을 사용하여 저장됩니다.

다음은 샘플 앱의 위협 모델입니다.

![위협 모델](./media/secure-aad-app/threat-model.png)

위협 모델링 도구가 생성하는 일부 샘플 위협 및 잠재적 취약점은 다음 스크린샷에 나와 있습니다. 위협 모델은 노출된 공격 노출 에 대한 개요를 제공하고 개발자가 문제를 완화하는 방법에 대해 생각하라는 메시지를 표시합니다.

![위협 모델 출력](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>사전 요구 사항
응용 프로그램을 실행하려면 다음 도구를 설치해야 합니다.

- 응용 프로그램 코드를 수정하고 볼 수 있는 코드 편집기입니다. [비주얼 스튜디오 코드는](https://code.visualstudio.com/) 오픈 소스 옵션입니다.
- 개발 컴퓨터에서 [Azure CLI를](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) 제공합니다.
- 시스템에 [Git.](https://git-scm.com/) Git은 소스 코드를 로컬로 복제하는 데 사용됩니다.
- [jq](https://stedolan.github.io/jq/), 사용자 친화적 인 방법으로 JSON을 쿼리하기위한 UNIX 도구입니다.

샘플 앱의 리소스를 배포하려면 Azure 구독이 필요합니다. Azure 구독이 없는 경우 [무료 계정을 만들어](https://azure.microsoft.com/free/) 샘플 앱을 테스트할 수 있습니다.

이러한 도구를 설치한 후 Azure에서 앱을 배포할 준비가 된 것입니다.

### <a name="implementation-guidance"></a>구현 지침
배포 스크립트는 네 단계로 나눌 수 있는 하나의 스크립트입니다. 각 단계는 [아키텍처 다이어그램에](#architecture)있는 Azure 리소스를 배포하고 구성합니다.

네 단계는

- Azure 키 자격 증명 모음을 배포합니다.
- Azure 웹 앱을 배포합니다.
- 웹 응용 프로그램 방화벽을 통해 응용 프로그램 게이트웨이를 배포합니다.
- 배포된 앱으로 Azure AD를 구성합니다.

각 단계는 이전에 배포된 리소스의 구성을 사용하여 이전 단계에 기반을 두고 있습니다.

구현 단계를 완료하려면 [필수 구성 조건](#prerequisites)아래에 나열된 도구를 설치했는지 확인합니다.

#### <a name="deploy-azure-key-vault"></a>Azure 키 볼트 배포
이 섹션에서는 비밀 및 인증서를 저장하는 데 사용되는 Azure Key Vault 인스턴스를 만들고 배포합니다.

배포를 완료하면 Azure에 배포된 Azure 키 자격 증명 모음 인스턴스가 있습니다.

전원 셸을 사용하여 Azure 키 자격 증명 모음을 배포하려면
 
1. Azure 키 자격 증명 모음에 대한 변수를 선언합니다.
2. Azure 키 볼트 공급자를 등록합니다.
3. 인스턴스에 대한 리소스 그룹을 만듭니다.
4. 3단계에서 만든 리소스 그룹에 Azure 키 볼트 인스턴스를 만듭니다.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>아래 Azure AD 사용자는 키 자격 증명 모음에 대한 관리자 권한을 갖습니다.
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Az 공급자 등록
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Azure 키 볼트 인스턴스 만들기
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>키 자격 증명 모음에 관리자 정책 추가
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>사용자가 사용자 주 체이름을 알고 있는 경우 암호화 키, 인증서 및 비밀을 얻고 나열할 수 있도록 액세스 정책을 만들려면 다음을 수행하십시오.
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Key Vault를 사용하여 리소스에 액세스하는 앱의 Azure 리소스에 관리되는 ID를 사용하는 것이 좋습니다. Key Vault에 대한 액세스 키가 코드 또는 구성에 저장되지 않을 때 보안 태세가 증가합니다.

루트 인증서가 컨테이너에 포함됩니다. 인증서를 얻기 위해 취한 단계는 다음과 같습니다.

1. 인증서 기관에서 인증서 파일을 [다운로드합니다.](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)
2. 인증서 파일 디코딩:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
이 스크립트는 MSI와 함께 코드 또는 구성에서 하드 코딩 암호 없이 Azure Key Vault와 상호 작용하는 데 사용할 수 있는 앱 서비스 인스턴스에 대해 할당된 ID를 만듭니다.

포털의 Azure Key Vault 인스턴스로 이동하여 액세스 정책 탭에서 할당된 ID를 **승인합니다.** **선택 보안 주체에서**만든 앱 서비스 인스턴스의 이름과 유사한 응용 프로그램 이름을 검색합니다.
응용 프로그램에 연결된 서비스 주체가 표시되어야 합니다. 다음 스크린샷과 같이 액세스 정책 페이지를 선택하고 저장합니다.

응용 프로그램은 키만 검색하기만 하면 되므로 비밀 옵션에서 권한 **받기를** 선택하여 권한을 부여받은 권한을 줄이면서 액세스를 허용합니다.

![Key Vault 액세스 정책](./media/secure-aad-app/kv-access-policy.png)

*키 볼트 액세스 정책 만들기*

액세스 정책을 저장한 다음 **액세스 정책** 탭에 새 변경 된 변경 을 저장 하여 정책을 업데이트합니다.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>웹 응용 프로그램 방화벽을 사용하도록 설정한 응용 프로그램 게이트웨이 배포
웹 앱에서는 인터넷에서 외부 세계에 직접 서비스를 노출하지 않는 것이 좋습니다.
로드 밸런싱 및 방화벽 규칙은 들어오는 트래픽을 보다 세밀하고 제어하고 관리하는 데 도움을 줍니다.

응용 프로그램 게이트웨이 인스턴스를 배포하려면

1. 응용 프로그램 게이트웨이를 수용할 리소스 그룹을 만듭니다.
2. 게이트웨이에 연결할 가상 네트워크를 프로비전합니다.
3. 가상 네트워크에서 게이트웨이용 서브넷을 만듭니다.
4. 공용 IP 주소를 프로비전합니다.
5. 응용 프로그램 게이트웨이를 프로비전합니다.
6. 게이트웨이에서 웹 응용 프로그램 방화벽을 활성화합니다.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Azure 웹 앱 배포
Azure App Service를 사용하면 Python, Ruby, C#및 Java와 같은 언어를 사용하여 웹 앱을 빌드하고 호스팅할 수 있습니다. 또한 Azure는 거의 모든 프로그래밍 언어를 Azure App Service 플랫폼에서 실행할 수 있는 사용자 지정 컨테이너도 지원합니다.

#### <a name="create-an-app-service-plan-in-free-tier"></a>프리 티어로 앱 서비스 요금제 만들기
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>웹앱 만들기
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>계속하기 전에 사용자 지정 도메인의 Azure 도메인 이름 시스템 구성 https://aka.ms/appservicecustomdns UI로 이동하여 호스트 이름 "www"에 대한 CNAME 레코드를 구성하고 웹 앱의 기본 도메인 이름을 가리키는 지침을 따릅니다.

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>앱 서비스 계획을 공유 계층으로 업그레이드(사용자 지정 도메인에서 필요한 최소)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>웹 앱에 사용자 지정 도메인 이름 추가
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>지침 및 권장 사항

### <a name="network"></a>네트워크
배포를 완료한 후에는 웹 응용 프로그램 방화벽이 활성화된 응용 프로그램 게이트웨이가 있습니다.

게이트웨이 인스턴스는 HTTPS에 대한 포트 443을 노출합니다. 이 구성을 통해 HTTPS를 통해 포트 443에서만 앱에 액세스할 수 있습니다.

사용하지 않는 포트를 차단하고 공격 노출을 제한하는 것이 보안 모범 사례입니다.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>앱 서비스 인스턴스에 네트워크 보안 그룹 추가

앱 서비스 인스턴스를 가상 네트워크와 통합할 수 있습니다. 이 통합을 통해 앱의 수신 및 나가는 트래픽을 관리하는 네트워크 보안 그룹 정책으로 구성할 수 있습니다.

1. 이 기능을 사용하려면 Azure 앱 서비스 인스턴스 블레이드에서 **설정에서** **네트워킹을**선택합니다. 오른쪽 창에서 **VNet 통합에서**구성합니다.

   ![새로운 가상 네트워크 통합](./media/secure-web-app/app-vnet-menu.png)

    *앱 서비스를 위한 새로운 가상 네트워크 통합*
1. 다음 페이지에서 **VNET 추가(미리 보기)를**선택합니다.

1. 다음 메뉴에서 `aad-vnet`로 시작하는 배포에서 만든 가상 네트워크를 선택합니다. 새 서브넷을 만들거나 기존 서브넷을 선택할 수 있습니다.
   이 경우 새 서브넷을 만듭니다. 주소 **범위를** **10.0.3.0/24로** 설정하고 서브넷 **앱 서브넷의**이름을 지정합니다.

   ![앱 서비스 가상 네트워크 구성](./media/secure-web-app/app-vnet-config.png)

    *앱 서비스를 위한 가상 네트워크 구성*

이제 가상 네트워크 통합을 사용하도록 설정되었으므로 앱에 네트워크 보안 그룹을 추가할 수 있습니다.

1. 검색 상자를 사용하여 **네트워크 보안 그룹을**검색합니다. 결과에서 **네트워크 보안 그룹을** 선택합니다.

    ![네트워크 보안 그룹 검색](./media/secure-web-app/nsg-search-menu.png)

    *네트워크 보안 그룹 검색*

2. 다음 메뉴에서 **추가를**선택합니다. NSG의 **이름과** NSG가 있는 **리소스 그룹을** 입력합니다. 이 NSG는 응용 프로그램 게이트웨이의 서브넷에 적용됩니다.

    ![NSG 만들기](./media/secure-web-app/nsg-create-new.png)

    *NSG 만들기*

3. NSG를 만든 후 선택합니다. 블레이드에서 **설정에서** **인바운드 보안 규칙을**선택합니다. 포트 443을 통해 응용 프로그램 게이트웨이로 들어오는 연결을 허용하도록 이러한 설정을 구성합니다.

   ![NSG 구성](./media/secure-web-app/nsg-gateway-config.png)

   *NSG 구성*

4. 게이트웨이 NSG의 아웃바운드 규칙에서 서비스 태그를 대상으로 하는 규칙을 만들어 앱 서비스 인스턴스에 아웃바운드 연결을 허용하는 규칙을 추가합니다.`AppService`

   ![NSG에 대한 아웃바운드 규칙 추가](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *NSG에 대한 아웃바운드 규칙 추가*

    게이트웨이가 아웃바운드 규칙을 가상 네트워크에 보낼 수 있도록 다른 아웃바운드 규칙을 추가합니다.

   ![다른 아웃바운드 규칙 추가](./media/secure-web-app/nsg-outbound-vnet.png)

    *다른 아웃바운드 규칙 추가*

5. NSG의 서브넷 블레이드에서 연결 **,** 배포에서 만든 가상 네트워크를 선택하고 **gw-서브넷이라는**게이트웨이 서브넷을 선택합니다. NSG는 서브넷에 적용됩니다.

6. 앱 서비스 인스턴스에 대해 이전 단계와 마찬가지로 이번에는 다른 NSG를 만듭니다. 이름을 지정합니다. 응용 프로그램 게이트웨이 NSG에 대해했던 것처럼 포트 443에 대한 인바운드 규칙을 추가합니다.

   이 앱의 경우와 는 달리 앱 서비스 환경 인스턴스에 배포된 앱 서비스 인스턴스가 있는 경우 앱 서비스 NSG의 인바운드 보안 그룹에 포트 454-455를 열어 Azure Service 상태 프로브를 허용하는 인바운드 규칙을 추가할 수 있습니다. 구성은 다음과 같습니다.

   ![Azure 서비스 상태 프로브에 대한 규칙 추가](./media/secure-web-app/nsg-create-healthprobes.png)

    *Azure 서비스 상태 프로브에 대한 규칙 추가(앱 서비스 환경만)*

공격 표면을 제한하려면 응용 프로그램 게이트웨이만 응용 프로그램에 액세스할 수 있도록 App Service 네트워크 설정을 수정합니다.
설정을 적용하려면 앱 서비스 네트워크 탭으로 이동하여 **IP 제한** 탭을 선택하고 응용 프로그램 게이트웨이의 IP만 서비스에 직접 액세스할 수 있도록 허용하는 규칙을 만듭니다. 개요 페이지에서 게이트웨이의 IP 주소를 검색할 수 있습니다. IP **주소 CIDR** 탭에서 이 형식으로 IP `<GATEWAY_IP_ADDRESS>/32`주소를 입력합니다.

![게이트웨이만 허용](./media/secure-web-app/app-allow-gw-only.png)

*게이트웨이 IP만 앱 서비스에 액세스할 수 있도록 허용*

### <a name="azure-domain-name-system"></a>Azure 도메인 이름 시스템 
Azure 도메인 이름 시스템 또는 Azure 도메인 이름 시스템은 웹 사이트 또는 서비스 이름을 IP 주소로 변환(또는 해결)합니다. Azure 도메인 이름https://docs.microsoft.com/azure/dns/dns-overview) 시스템(Azure 인프라)은 Azure 인프라를 사용하여 이름 확인을 제공하는 도메인 이름 시스템 도메인의 호스팅 서비스입니다. Azure에서 도메인을 호스팅함으로써 사용자는 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 청구를 사용하여 도메인 이름 시스템 레코드를 관리할 수 있습니다. Azure 도메인 이름 시스템은 개인 도메인 이름 시스템 도메인도 지원합니다.

### <a name="azure-disk-encryption"></a>Azure 디스크 암호화
Azure Disk Encryption은 Windows의 BitLocker 기능을 활용하여 데이터 디스크에 대한 볼륨 암호화를 제공합니다. 이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키를 제어하고 관리하는 데 유용합니다.

### <a name="identity-management"></a>ID 관리
다음 기술은 Azure 환경에서 카드 소지자 데이터에 대한 액세스를 관리하는 기능을 제공합니다.
- Azure Active Directory는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. 이 솔루션의 모든 사용자는 Azure WebApp에 액세스하는 사용자를 포함하여 Azure Active Directory에서 만들어집니다.
- Azure 역할 기반 액세스 제어를 사용하면 관리자가 세분화된 액세스 권한을 정의하여 사용자가 자신의 작업을 수행하는 데 필요한 액세스 권한만 부여할 수 있습니다. 관리자는 모든 사용자에게 Azure 리소스에 대한 무제한 권한을 부여하는 대신 카드 소유자 데이터에 액세스하기 위한 특정 작업만 허용할 수 있습니다. 구독 액세스는 구독 관리자로 제한됩니다.
- Azure Active Directory Privileged Identity Management를 사용하면 고객이 특정 정보(예: 카드 소유자 데이터)에 액세스할 수 있는 사용자 수를 최소화할 수 있습니다. 관리자는 Azure Active Directory Privileged Identity Management를 사용하여 권한 있는 ID와 리소스에 대한 액세스를 검색, 제한 및 모니터링할 수 있습니다. 필요한 경우 이 기능을 사용하여 요청 시 JIT(Just-In-Time) 관리 액세스를 적용할 수도 있습니다.
- Azure Active Directory ID 보호는 조직의 ID에 영향을 미치는 잠재적 취약점을 감지하고, 조직의 ID와 관련된 의심스러운 작업을 검색하기 위해 자동화된 응답을 구성하고, 의심스러운 작업을 조사합니다. 적절한 조치를 취할 수 있습니다.
### <a name="secrets-management"></a>비밀 관리
이 솔루션은 Azure Key Vault를 사용하여 키와 비밀을 관리합니다. Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. 다음 Azure Key Vault 기능은 고객이 이러한 데이터를 보호하고 액세스하는 데 도움이 됩니다.
   - 필요에 따라 고급 액세스 정책이 구성됩니다.
   - Key Vault 액세스 정책은 키와 비밀에 대한 최소 필수 권한으로 정의됩니다.
   - Key Vault의 모든 키와 비밀에는 만료 날짜가 있습니다.
   - Key Vault의 모든 키는 특별한 하드웨어 보안 모듈을 통해 보호됩니다. 키 유형은 하드웨어 보안 모듈(HSM) 보호된 2048비트 RSA 키입니다.
   - Key Vault를 사용하면 키와 비밀(예: 인증 키, 저장소 계정 키, 데이터 암호화 키) 을 암호화할 수 있습니다. 하드웨어 보안 모듈(HSM)에 의해 보호되는 키를 사용하여 PFX 파일 및 암호)를 사용합니다. 
   - RBAC(역할 기반 액세스 제어)를 사용하여 특정 범위의 사용자, 그룹 및 응용 프로그램에 권한을 할당합니다.     
   - 키 볼트를 사용하여 자동 갱신을 통해 TLS 인증서를 관리합니다. 
   - Key Vault에 대한 진단 로그는 365일 이상의 보존 기간 동안 사용하도록 설정됩니다.
   - 키에 허용되는 암호화 작업은 필요한 것으로 제한됩니다
### <a name="azure-security-center"></a>Azure Security Center
고객은 Azure Security Center를 통해 여러 워크로드에 걸쳐 보안 정책을 중앙에서 적용 및 관리하고, 위협에 대한 노출을 제한하고, 공격을 검색하여 대응할 수 있습니다. 또한 
   - Azure Security Center는 Azure 서비스의 기존 구성에 액세스하여 보안 상태를 개선하고 데이터를 보호하는 데 도움이 되는 구성 및 서비스 권장 사항을 제공합니다.
   - Azure Security Center는 다양한 검색 기능을 사용하여 고객에게 환경을 대상으로 하는 잠재적인 공격에 대해 경고합니다. 이러한 경고는 트리거된 경고, 대상으로 지정된 리소스 및 공격의 출처에 대한 중요한 정보를 포함합니다. Azure Security Center에는 위협 또는 의심스러운 활동이 발생할 때 트리거되는 미리 정의된 보안 경고 집합이 있습니다. Azure Security Center에서 사용자 지정 경고 규칙을 사용하면 고객은 자신의 환경에서 이미 수집된 데이터를 기반으로 하는 새 보안 경고를 정의할 수 있습니다.
   - Azure Security Center에서는 우선 순위가 지정된 보안 경고 및 인시던트를 제공하여 고객이 잠재적인 보안 문제를 더 쉽게 검색하고 해결하게 합니다. 위협 인텔리전스 보고서는 인시던트 대응 팀이 위협을 조사하고 수정하도록 지원하기 위해 탐지된 각 위협에 대해 생성됩니다.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   이 아키텍처는 웹 애플리케이션 방화벽이 구성되어 있고 OWASP 규칙 집합을 사용할 수 있는 Azure Application Gateway를 통해 보안 취약성의 위험을 줄입니다. 추가 기능에는 다음이 포함됩니다.
   - 종단 간 SSL.
   - TLS v1.0 및 v1.1을 사용하지 않도록 설정합니다.
   - TLSv1.2를 활성화합니다.
   - 웹 응용 프로그램 방화벽(방지 모드).
   - OWASP 3.0 규칙 집합이 있는 예방 모드입니다.
   - 진단 로깅을 사용하도록 설정합니다.
   - 사용자 정의 상태 프로브.
   - Azure 보안 센터 와 Azure Advisor는 추가 보호 및 알림을 제공합니다. Azure Security Center는 평판 시스템도 제공합니다.
### <a name="logging-and-auditing"></a>로깅 및 감사
Azure 서비스는 시스템 및 사용자 활동, 시스템 상태를 광범위하게 기록합니다.
   - 활동 로그: [활동 로그는 구독의](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) 리소스에서 수행된 작업에 대한 통찰력을 제공합니다. 활동 로그는 작업의 개시 장치, 발생 시간 및 상태를 결정하는 데 도움이 될 수 있습니다.
   - 진단 로그: [진단 로그에는](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 모든 리소스에서 내보내는 모든 로그가 포함됩니다. 이러한 로그에는 Windows 이벤트 시스템 로그, Azure 저장소 로그, 키 볼트 감사 로그, 응용 프로그램 게이트웨이 액세스 및 방화벽 로그가 포함됩니다. 모든 진단 로그는 보관을 위해 암호화된 중앙 집중식 Azure Storage 계정에 기록됩니다. 보존은 조직 특정 보존 요구 사항에 맞게 최대 730일까지 사용자가 구성할 수 있습니다.
### <a name="azure-monitor-logs"></a>Azure Monitor 로그
   이러한 로그는 처리, 저장 및 대시보드 보고를 위해 [Azure Monitor 로그에](https://azure.microsoft.com/services/log-analytics/) 통합됩니다. 수집이 완료되면 데이터는 Log Analytics 작업 영역 내의 각 데이터 형식에 대해 별도 테이블로 구성되어 원본에 관계없이 모든 데이터가 함께 분석되도록 합니다. 또한 Azure Security Center는 Azure Monitor 로그와 통합되어 고객이 Kusto 쿼리를 사용하여 보안 이벤트 데이터에 액세스하고 다른 서비스의 데이터와 결합할 수 있습니다.

   다음 Azure [모니터링 솔루션은](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) 이 아키텍처의 일부로 포함됩니다.

   - [활성 디렉터리 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory 상태 검사 솔루션은 정기적으로 서버 환경의 위험과 상태를 평가하고 배포된 서버 인프라와 관련된 우선 순위 목록을 제공합니다.
   - [에이전트 상태:](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)에이전트 상태 솔루션은 배포된 에이전트 수와 해당 지리적 분포, 응답하지 않는 에이전트 수 및 운영 데이터를 제출하는 에이전트 수를 보고합니다.
   - [활동 로그 분석](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): 활동 로그 분석 솔루션은 고객에 대한 모든 Azure 구독에서 Azure 활동 로그를 분석하는 데 도움을 줍니다.
### <a name="azure-monitor"></a>Azure Monitor
   [Azure Monitor를](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)사용하면 Azure 리소스에서 API 호출 추적을 포함하여 조직에서 모니터링, 경고 생성 및 아카이브 데이터를 사용할 수 있으므로 사용자가 성능을 추적하고 보안을 유지 하며 추세를 식별할 수 있습니다.
### <a name="application-insights"></a>애플리케이션 정보 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 애플리케이션 성능 관리 서비스입니다. Application Insights는 성능 이상을 감지하며, 고객이 라이브 웹 애플리케이션을 모니터링하는 데 사용할 수 있습니다. 고객이 문제를 진단하고 실제로 앱을 사용하여 수행하는 작업을 파악할 수 있는 강력한 분석 도구를 포함하고 있습니다. 고객이 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

### <a name="azure-key-vault"></a>Azure Key Vault
   키를 저장할 조직에 대한 자격 증명 모음을 만들고 아래와 같은 운영 작업에 대한 책임을 유지합니다.

   - 키 볼트에 저장된 데이터는 다음과 같습니다.   
   - Application Insight 키
   - 데이터 스토리지 액세스 키
   - 연결 문자열
   - 데이터 테이블 이름
   - 사용자 자격 증명
   - 필요에 따라 고급 액세스 정책이 구성됩니다.
   - 키 볼트 액세스 정책은 키 및 비밀에 대한 최소 필수 권한으로 정의됩니다.
   - Key Vault의 모든 키와 비밀에는 만료 날짜가 있습니다.
   - 키 볼트의 모든 키는 하드웨어 보안 모듈(HSM)[키 유형 = 하드웨어 보안 모듈(HSM) 보호)에 의해 보호됩니다.       
     2048비트 RSA 키]
   - 모든 사용자/ID에는 RBAC(역할 기반 액세스 제어)를 사용하여 최소 필수 권한이 부여됩니다.
   - 애플리케이션이 서로 신뢰하고 런타임 시 동일한 비밀에 액세스할 필요가 없으면 애플리케이션에서 Key Vault를 공유하지 않습니다.
   - Key Vault에 대한 진단 로그는 365일 이상의 보존 기간 동안 사용하도록 설정됩니다.
   - 키에 허용되는 암호화 작업은 필요한 것으로 제한됩니다

### <a name="vpn-and-expressroute"></a>VPN 및 ExpressRoute
   이 PaaS 웹 응용 프로그램 참조 아키텍처의 일부로 배포된 리소스에 대한 연결을 안전하게 설정하여 보안 VPN 터널 또는 [ExpressRoute를](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 구성해야 했습니다. VPN 또는 ExpressRoute를 적절히 설정하면 고객이 전송 중인 데이터에 대한 보호 계층을 추가할 수 있습니다.

   Azure를 통해 보안 VPN 터널을 구현하면 온-프레미스 네트워크와 Azure Virtual Network 간의 가상 프라이빗 연결을 만들 수 있습니다. 이 연결은 인터넷을 통해 이루어지며 고객이 고객의 네트워크와 Azure 간의 암호화된 링크 내에서 정보를 안전하게 "터널링"할 수 있도록 합니다. 사이트 간 VPN은 수십 년 동안 모든 규모의 기업에서 배포한 안전하고 완성도가 높은 기술입니다. IPsec 터널 모드는 이 옵션에서 암호화 메커니즘으로 사용됩니다.

   VPN 터널 내의 트래픽은 사이트 간 VPN을 사용하여 인터넷을 통과하므로 Microsoft는 훨씬 더 안전한 또 다른 연결 옵션을 제공합니다. Azure ExpressRoute는 Azure 및 온-프레미스 위치 또는 Exchange 호스팅 공급자 간의 전용 WAN 링크입니다. ExpressRoute 연결은 인터넷을 통해 수행되지 않으므로 인터넷을 통한 일반적인 연결보다 높은 안정성, 빠른 속도, 짧은 대기 시간 및 높은 보안을 제공합니다. 또한 이 연결은 고객의 통신 공급자의 직접 연결이므로 데이터는 인터넷을 통해 이동하지 않으며, 따라서 노출되지 않습니다.

   온-프레미스 네트워크를 Azure로 확장하는 보안 하이브리드 네트워크를 구현하는 모범 사례를 [사용할 수 있습니다](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

#### <a name="implement-azure-active-directory-oidc"></a>Azure Active Directory OIDC 구현

1. 소스 코드 리포지토리를 복제하려면 이 Git 명령을 사용합니다.

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>리디렉션 URL 업데이트
1.  Azure Portal로 다시 이동합니다. 왼쪽 탐색 창에서 Azure Active Directory 서비스를 선택한 다음 앱 등록을 선택합니다.
2.  결과 화면에서 WebApp-OpenIDConnect-DotNet-코드-v2 응용 프로그램을 선택합니다.
3.  인증 탭 o 리디렉션 URIIs 섹션에서 콤보 상자에서 웹을 선택하고 다음 리디렉션 URI를 추가합니다.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.nethttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o 고급 설정 섹션에서 로그아웃 URL을https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  브랜딩 탭 o 예를 들어 https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net홈 페이지 URL을 앱 서비스의 주소로 업데이트합니다.
        o 구성을 저장합니다.
5.  응용 프로그램이 웹 API를 호출하는 경우 프로젝트 appsettings.json에 필요한 변경 내용을 적용하여 localhost 대신 게시된 API URL을 호출합니다.
샘플 게시
    1.  App Service의 개요 탭에서 게시 프로필 가져오기 링크를 클릭하여 게시 프로필을 다운로드하고 저장합니다. 원본 제어에서와 같이 다른 배포 메커니즘도 사용할 수 있습니다.
    2.  비주얼 스튜디오로 전환하고 WebApp-OpenIDConnect-DotNet-코드-v2 프로젝트로 이동합니다. 솔루션 탐색기에서 마우스 오른쪽 단추로 프로젝트를 클릭하고, 게시를 선택합니다. 아래쪽 표시줄에서 프로필 가져오기를 클릭하고, 이전에 다운로드한 게시 프로필을 가져옵니다.
    3.  구성 및 연결 탭에서 대상 URL을 업데이트하여 홈 페이지 URL의 https가 https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net되도록 클릭합니다. 다음을 클릭합니다.
    4.  설정 탭에서 조직 인증 사용이 선택되어 있지 않은지 확인합니다. 저장을 클릭합니다. 주 화면에서 게시를 클릭합니다.
    5.  Visual Studio에서 프로젝트를 게시하고 브라우저를 프로젝트의 URL로 자동으로 엽니다. 프로젝트의 기본 웹 페이지가 표시되면 게시가 성공적으로 수행된 것입니다.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Azure Active 디렉터리용 다단계 인증 구현
   관리자는 포털의 구독 계정을 보호해야 합니다. 구독은 생성한 리소스를 관리하기 때문에 공격에 취약합니다. 구독을 보호하려면 구독의 **Azure Active Directory** 탭에서 다단계 인증을 사용하도록 설정합니다.

   Azure AD는 특정 기준에 맞는 사용자 또는 사용자 그룹에 적용되는 정책을 기반으로 작동합니다.
Azure는 관리자가 포털에 로그인하기 위해 2단계 인증이 필요하도록 지정하는 기본 정책을 만듭니다.
이 정책을 사용하도록 설정하면 Azure 포털에 로그아웃하고 다시 로그인하라는 메시지가 표시될 수 있습니다.

관리자 로그인에 대해 MFA를 사용하려면

   1. Azure 포털의 **Azure Active 디렉터리** 탭으로 이동
   2. 보안 범주에서 조건부 액세스를 선택합니다. 이 화면이 표시됩니다.

       ![조건부 액세스 - 정책](./media/secure-aad-app/ad-mfa-conditional-add.png)

새 정책을 만들 수 없는 경우

   1. **MFA** 탭으로 이동합니다.
   2. Azure AD **프리미엄 무료 평가판** 링크를 선택하여 무료 평가판에 가입합니다.

   ![Azure AD 프리미엄 무료 평가판](./media/secure-aad-app/ad-trial-premium.png)

조건부 액세스 화면으로 돌아갑니다.

   1. 새 정책 탭을 선택합니다.
   2. 정책 이름을 입력합니다.
   3. MFA를 사용하도록 설정할 사용자 또는 그룹을 선택합니다.
   4. **액세스 컨트롤에서** **권한 부여** 탭을 선택한 다음 **다단계 인증 필요(원하는** 경우 다른 설정)를 선택합니다.

   ![MFA 요구](./media/secure-aad-app/ad-mfa-conditional-add.png)

   화면 상단의 확인란을 선택하거나 **조건부 액세스** 탭에서 정책을 활성화할 수 있습니다. 정책을 사용하도록 설정하면 사용자는 MFA가 포털에 로그인해야 합니다.

   모든 Azure 관리자에 대해 MFA가 필요한 기준 정책이 있습니다. 포털에서 즉시 활성화할 수 있습니다. 이 정책을 사용하도록 설정하면 현재 세션이 무효화되고 다시 로그인해야 할 수 있습니다.

   기준 정책을 사용할 수 없는 경우
   1.   **관리자의 경우 MFA 필요를 선택합니다.**
   2.   **정책 사용을 즉시**선택합니다.

   ![즉시 정책 사용 선택](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Azure Sentinel을 사용하여 앱 및 리소스 모니터링

   응용 프로그램이 증가함에 따라 리소스에서 받은 모든 보안 신호와 메트릭을 집계하여 작업 지향적인 방식으로 유용하게 만들기가 어려워집니다.

   Azure Sentinel은 데이터를 수집하고 가능한 위협 유형을 검색하며 보안 인시던트에 대한 가시성을 제공하도록 설계되었습니다.
수동 개입을 기다리는 동안 Azure Sentinel은 미리 작성된 플레이북에 의존하여 경고 및 인시던트 관리 프로세스를 시작할 수 있습니다.

   샘플 앱은 Azure Sentinel이 모니터링할 수 있는 여러 리소스로 구성됩니다.
Azure Sentinel을 설정하려면 먼저 다양한 리소스에서 수집된 모든 데이터를 저장하는 Log Analytics 작업 영역을 만들어야 합니다.

이 작업 영역을 만들려면

   1. Azure 포털의 검색 상자에서 **로그 분석을**검색합니다. **Log Analytics 작업 영역**을 선택합니다.

   ![로그 분석 작업 영역 검색](./media/secure-aad-app/sentinel-log-analytics.png)

   *로그 분석 작업 영역 검색*

   2. 다음 페이지에서 **추가를** 선택한 다음 작업 공간에 대한 이름, 리소스 그룹 및 위치를 제공합니다.
   ![Log Analytics 작업 영역 만들기](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *로그 분석 작업 영역 만들기*

   3. 검색 상자를 사용하여 **Azure Sentinel**을 검색합니다.

   ![Azure Sentinel 검색](./media/secure-aad-app/sentinel-add.png)

   *Azure 센티넬 검색*

   4. **추가를** 선택한 다음 이전에 만든 로그 분석 작업 영역을 선택합니다.

   ![로그 분석 작업 영역 추가](./media/secure-aad-app/sentinel-workspace-add.png)

   *로그 분석 작업 영역 추가*

   5. Azure **Sentinel - 데이터 커넥터** 페이지에서 **구성**에서 **데이터 커넥터를 선택합니다.** Azure Sentinel에서 분석을 위해 로그 애널리틱스 저장소 인스턴스에 연결할 수 있는 Azure 서비스 배열이 표시됩니다.

   ![로그 분석 데이터 커넥터](./media/secure-aad-app/sentinel-connectors.png)

      *Azure Sentinel에 데이터 커넥터 추가*

   예를 들어 응용 프로그램 게이트웨이를 연결하려면 다음 단계를 수행합니다.

   1. Azure 응용 프로그램 게이트웨이 인스턴스 블레이드를 엽니다.
   2. **모니터링** 아래에서 **진단 설정**을 선택합니다.
   3. **진단 설정 추가를**선택합니다.

   ![응용 프로그램 게이트웨이 진단 추가](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *응용 프로그램 게이트웨이 진단 추가*

   4. 진단 **설정** 페이지에서 사용자가 만든 로그 분석 작업 영역을 선택한 다음 수집하여 Azure Sentinel으로 보낼 모든 메트릭을 선택합니다. **저장**을 선택합니다.

   ![Azure 센티넬 커넥터 설정](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>비용 고려 사항
   Azure 계정이 아직 없는 경우 무료 계정을 만들 수 있습니다. [무료 계정 페이지로](https://azure.microsoft.com/free/) 이동하여 시작하고, 무료 Azure 계정으로 수행할 수 있는 작업을 확인하고, 12개월 동안 어떤 제품이 무료인지 알아보세요.

   보안 기능을 사용하여 샘플 앱에 리소스를 배포하려면 일부 프리미엄 기능에 대한 비용을 지불해야 합니다. 응용 프로그램 확장 및 Azure에서 제공하는 프리 계층 및 평가판을 응용 프로그램 요구 사항을 충족하도록 업그레이드해야 하므로 비용이 증가할 수 있습니다. Azure [가격 계산기를](https://azure.microsoft.com/pricing/calculator/) 사용하여 비용을 예측합니다.

## <a name="next-steps"></a>다음 단계
   다음 문서에서는 보안 응용 프로그램을 설계, 개발 및 배포하는 데 도움이 될 수 있습니다.

- [디자인](secure-design.md)
- [개발](secure-develop.md)
- [배포](secure-deploy.md)
