---
title: Azure PowerShell를 사용 하 여 Azure Stack Edge Pro R 장치에서 VPN 구성
description: Azure PowerShell 스크립트를 사용 하 여 Azure 리소스를 만들어 Azure Stack Edge Pro R 장치에서 VPN을 구성 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 2139080367cdce9a5f018afab0970a7bd0e7504c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467310"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>Azure PowerShell를 통해 Azure Stack Edge Pro R 장치에서 VPN을 구성 합니다.

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN 옵션은 Azure Stack Edge Pro R 장치에서 Azure로의 *TLS* 를 통한 데이터 이동에 대 한 두 번째 암호화 계층을 제공 합니다. Azure Portal 또는 Azure PowerShell를 통해 Azure Stack Edge Pro R 장치에서 VPN을 구성할 수 있습니다.

이 문서에서는 Azure PowerShell를 사용 하 여 클라우드에서 구성을 만들어 Azure Stack Edge Pro R 장치에서 VPN을 구성 하는 데 필요한 단계를 설명 합니다.

## <a name="about-vpn-setup"></a>VPN 설정 정보

크로스-프레미스 VPN 연결은 Azure VPN Gateway, 온-프레미스 VPN 디바이스 및 이 둘을 연결하는 IPsec S2S VPN 터널로 구성됩니다. 일반적인 워크플로에는 다음 단계가 포함됩니다.

- 필수 구성 요소를 구성 합니다.
- Azure에서 필요한 리소스를 설정 합니다.
    - Azure VPN gateway (가상 네트워크 게이트웨이)를 만들고 구성 합니다.
    - 온-프레미스 네트워크 및 VPN 장치를 나타내는 Azure 로컬 네트워크 게이트웨이를 만들고 구성 합니다.
    - Azure VPN 게이트웨이와 로컬 네트워크 게이트웨이 간에 Azure VPN 연결을 만들고 구성 합니다.
    - Azure 방화벽을 설정 하 고 네트워크 및 앱 규칙을 추가 합니다.
    - Azure 라우팅 테이블을 만들고 경로를 추가 합니다.
- 장치의 로컬 웹 UI에서 VPN을 설정 합니다. 로컬 네트워크 게이트웨이에서 나타내는 온-프레미스 VPN 장치를 구성 하 여 Azure VPN gateway와 실제 S2S VPN 터널을 설정 합니다.

자세한 단계는 다음 섹션에서 제공 됩니다.

## <a name="configure-prerequisites"></a>필수 조건 구성

1. [Azure Stack Edge Pro r 장치 설치](azure-stack-edge-pro-r-deploy-install.md)의 지침에 따라 설치 된 Azure Stack Edge pro r 장치에 액세스할 수 있어야 합니다. 이 장치는 온-프레미스 VPN 장치 역할을 하 여 Azure와 VPN 연결을 만듭니다. 

2. VPN 장치에 고정 공용 IP 주소 (외부)가 있어야 합니다. 이 주소는 NAT 일 수 없습니다.

3. Azure에서 Azure Stack Edge 서비스에 대해 사용 하도록 설정 된 유효한 Azure 구독에 대 한 액세스 권한이 있어야 합니다. 이 구독을 사용 하 여 Azure에서 Azure Stack Edge Pro R 장치를 관리 하는 해당 리소스를 만듭니다.  

4. Azure Stack Edge Pro R 장치에 액세스 하는 데 사용 하는 Windows 클라이언트에 액세스할 수 있습니다. 이 클라이언트를 사용 하 여 클라우드에서 구성 프로그램을 프로그래밍 방식으로 만듭니다.

    1. Windows 클라이언트에 필요한 PowerShell 버전을 설치 하려면 다음 명령을 실행 합니다.

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Azure 계정 및 구독에 연결 하려면 다음 명령을 실행 합니다.

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Azure Stack Edge Pro R 장치에서 VPN을 구성 하는 데 사용 하는 Azure 구독 이름을 제공 합니다.

    3. 클라우드에서 구성을 만드는 데 필요한 [스크립트를 다운로드](https://aka.ms/ase-vpn-deployment) 합니다. 스크립트는 다음을 수행합니다.
        
        - Azure 가상 네트워크 및 서브넷, 즉 *AzureFirewallSubnet* *서브넷* 을 만듭니다.
        - Azure VPN gateway를 만들고 구성 합니다.
        - Azure 로컬 네트워크 게이트웨이를 만들고 구성 합니다.
        - Azure VPN 게이트웨이와 로컬 네트워크 게이트웨이 간에 Azure VPN 연결을 만들고 구성 합니다.
        - Azure 방화벽을 만들고 네트워크 규칙, 앱 규칙을 추가 합니다.
        - Azure 라우팅 테이블을 만들고 경로를 추가 합니다.


## <a name="use-the-script"></a>스크립트 사용

먼저 `parameters.json` 매개 변수를 입력 하도록 파일을 수정 합니다. 그런 다음 수정 된 json 파일을 사용 하 여 스크립트를 실행 합니다.

이러한 각 단계는 다음 섹션에 설명 되어 있습니다.

### <a name="download-service-tags-file"></a>서비스 태그 파일 다운로드

`ServiceTags.json`스크립트를 다운로드 한 폴더에 파일이 이미 있을 수 있습니다. 그렇지 않은 경우 서비스 태그 파일을 다운로드할 수 있습니다.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>매개 변수 파일 수정

첫 번째 단계는 파일을 수정 하 `parameters.json` 고 변경 내용을 저장 하는 것입니다. 


만든 Azure 리소스의 경우 다음 이름을 제공 합니다.

|매개 변수 이름  |Description  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure Virtual Network 이름        |
|azureFirewalls_firewall_name     | Azure 방화벽 이름        |
|routeTables_routetable_name     | Azure 경로 테이블 이름        |
|publicIPAddresses_VNGW_public_ip_name     | 가상 네트워크 게이트웨이에 대 한 공용 IP 주소 이름       |
|virtualNetworkGateways_VNGW_name    | Azure VPN gateway (가상 네트워크 게이트웨이) 이름        |
|publicIPAddresses_firewall_public_ip_name     | Azure 방화벽에 대 한 공용 IP 주소 이름         |
|localNetworkGateways_LNGW_name    |Azure 로컬 네트워크 게이트웨이 이름          |
|connections_vngw_lngw_name    | Azure VPN 연결 이름입니다. 가상 네트워크 게이트웨이와 로컬 네트워크 게이트웨이 간의 연결입니다.       |
|위치     |가상 네트워크를 만들려는 지역입니다. 장치와 연결 된 지역을 선택 합니다.         |

다음 IP 주소와 주소 공간은 가상 네트워크 및 관련 서브넷 (기본값, 방화벽, 게이트웨이 서브넷)을 포함 하 여 생성 된 Azure 리소스와 관련이 있습니다.

|매개 변수 이름  |Description  |
|---------|---------|
|VnetIPv4AddressSpace    | 가상 네트워크와 연결 된 주소 공간입니다.       |
|DefaultSubnetIPv4AddressSpace    |`Default`가상 네트워크에 대 한 서브넷과 연결 된 주소 공간입니다.         |
|FirewallSubnetIPv4AddressSpace    |`Firewall`가상 네트워크에 대 한 서브넷과 연결 된 주소 공간입니다.          |
|GatewaySubnetIPv4AddressSpace    |가상 네트워크에 대 한와 연결 된 주소 공간입니다 `GatewaySubnet` .          |
|GatewaySubnetIPv4bgpPeeringAddress    | BGP 통신용으로 예약 된 IP 주소 이며, 가상 네트워크의와 연결 된 주소 공간을 기준으로 합니다 `GatewaySubnet` .          |

다음 IP 주소와 주소 공간은 온-프레미스 네트워크 (Azure Stack Edge Pro R 장치가 배포 된 위치)와 관련이 있습니다.

|매개 변수 이름  |Description  |
|---------|---------|
|CustomerNetworkAddressSpace    |  개인 IP 주소에 대 한 주소 공간입니다.       |
|CustomerPublicNetworkAddressSpace    |  공용 IP 주소에 대 한 주소 공간입니다.       |
|DbeIOTNetworkAddressSpace    |이 IP 주소는 IoT 서비스에 예약 되어 있습니다. 이 매개 변수를 변경 하지 마십시오.        |
|AzureVPNsharedKey    |이 공유 키는 Azure VPN 연결 리소스를 만드는 동안 사용 됩니다. 로컬 웹 UI VPN 구성 중에이 키가 VPN 공유 암호로 제공 됩니다.         |
|Ipbe 게이트웨이-ipaddress   | Azure Stack Edge Pro R 장치에 대 한 공용 IP 주소입니다. 이는 알려지지 않을 수 있으며 자리 표시자 IP 주소를 사용 하 여 스크립트를 실행할 수 있습니다. 나중에 실제 IP 주소를 사용 하 여 로컬 네트워크 게이트웨이를 편집 합니다.     |

#### <a name="caveats-to-keep-in-mind"></a>유의 해야 할 주의 사항은 다음과 같습니다.

- Azure Stack Edge Pro R 장치의 IP 주소가 없습니다. 자리 표시자 IP 주소를 사용 하 여 리소스를 만든 후 나중에 장치에 대 한 로컬 네트워크의 주소 공간 및 실제 장치 IP 주소를 할당 하도록 Azure 로컬 네트워크 게이트웨이를 수정할 수 있습니다.
- IETF의 방향에 따라 IANA (인터넷 할당 기관)에서 172.16의 모든 서브넷을 사용 하 여 172.24. z.a. Azure 네트워크에 대 한 172.24 IPv4 주소 범위를 예약 합니다.

### <a name="run-the-script"></a>스크립트 실행

수정 된를 사용 하 고 스크립트를 실행 하 여 Azure 리소스를 만들려면 다음 단계를 수행 합니다 `parameters.json` .

1. PowerShell을 관리자 권한으로 실행합니다.

2. 스크립트가 있는 디렉터리로 전환 합니다.

3. 스크립트를 실행합니다.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    샘플 출력은 다음과 같습니다.

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    스크립트를 실행 하는 데 약 90 분이 소요 됩니다. 스크립트가 완료 되 면 스크립트가 있는 동일한 폴더에 배포 로그가 생성 됩니다.


## <a name="verify-the-azure-resources"></a>Azure 리소스 확인

스크립트를 성공적으로 실행 한 후에는 Azure에서 모든 리소스를 만들었는지 확인 합니다.

그런 다음 장치의 로컬 웹 UI에서 VPN을 구성 합니다.


## <a name="vpn-configuration-on-the-device"></a>장치의 VPN 구성

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>VPN 확인

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>VPN을 통한 데이터 전송 유효성 검사

VPN이 작동 하는지 확인 하려면 데이터를 SMB 공유로 복사 합니다. Azure Stack Edge Pro R 장치에서 [공유 추가](azure-stack-edge-j-series-manage-shares.md#add-a-share) 의 단계를 따릅니다. 

1. 예를 들어 클라이언트 시스템에 탑재 한 SMB 공유에 \data\pictures\waterfall.jpg 파일을 복사 합니다. 
2. 이 파일이 클라우드의 저장소 계정에 표시 되는지 확인 합니다.

데이터가 VPN을 통과 하는지 확인 하려면 다음을 수행 합니다.

1. 리소스 그룹에 있는 연결 리소스를 엽니다. 

2. 데이터 및 데이터 출력 값을 확인 합니다.
 
3. 리소스 그룹에서 Virtual Network 게이트웨이를 엽니다. **총 터널 수신** 및 **총 터널 송신** 에 대 한 차트를 봅니다.
 
## <a name="debug-issues"></a>문제 디버그

모든 문제를 디버그 하려면 다음 명령을 사용 합니다.

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

샘플 출력은 다음과 같습니다.


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro R 장치에서 로컬 UI를 통해 VPN 구성](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
