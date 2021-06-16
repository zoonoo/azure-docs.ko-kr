---
title: Azure PowerShell을 사용하여 Azure Stack Edge Pro R 디바이스에서 VPN 구성
description: Azure 리소스를 만들기 위해 Azure PowerShell 스크립트를 사용하여 Azure Stack Edge Pro R 디바이스에서 VPN을 구성하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 750fd8e64c1c42b6b6452ef20041c2b34d5ef287
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110701204"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>Azure PowerShell을 통해 Azure Stack Edge Pro R 디바이스에서 VPN 구성

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN 옵션은 Azure Stack Edge Pro R 디바이스에서 Azure로 *TLS* 를 통해 이동 중인 데이터에 대한 두 번째 암호화 계층을 제공합니다. Azure Portal 또는 Azure PowerShell을 통해 Azure Stack Edge Pro R 디바이스에 VPN을 구성할 수 있습니다.

이 문서에서는 Azure PowerShell을 사용하여 클라우드에 구성을 생성하는 Azure Stack Edge Pro R 디바이스에서 VPN을 구성하는 데 필요한 단계를 설명합니다.

## <a name="about-vpn-setup"></a>VPN 설정 정보

크로스-프레미스 VPN 연결은 Azure VPN Gateway, 온-프레미스 VPN 디바이스 및 이 둘을 연결하는 IPsec S2S VPN 터널로 구성됩니다. 일반적인 워크플로에는 다음 단계가 포함됩니다.

- 필수 조건을 구성합니다.
- Azure에서 필요한 리소스를 설정합니다.
    - Azure VPN Gateway(Virtual Network Gateway)를 만들고 구성합니다.
    - 온-프레미스 네트워크 및 VPN 디바이스를 나타내는 Azure 로컬 네트워크 게이트웨이를 만들고 구성합니다.
    - Azure VPN Gateway와 로컬 네트워크 게이트웨이 간에 Azure VPN 연결을 만들고 구성합니다.
    - Azure Firewall을 설정하고 네트워크 및 앱 규칙을 추가합니다.
    - Azure 라우팅 테이블을 만들고 경로를 추가합니다.
- 디바이스의 로컬 웹 UI에서 VPN을 설정합니다. Azure VPN Gateway와의 실제 S2S VPN 터널을 설정하도록 로컬 네트워크 게이트웨이가 나타내는 온-프레미스 VPN 디바이스를 구성합니다.

자세한 내용은 다음 섹션에 나와 있습니다.

## <a name="configure-prerequisites"></a>필수 조건 구성

1. [Azure Stack Edge Pro R 디바이스 설치](azure-stack-edge-pro-r-deploy-install.md)의 지침에 따라 설치된 Azure Stack Edge Pro R 디바이스에 대한 액세스 권한이 있어야 합니다. 이 디바이스는 Azure와 VPN으로 연결하는 온-프레미스 VPN 디바이스 역할을 합니다. 

2. VPN 디바이스에 고정 공용 IP 주소(외부)가 있어야 합니다. 이 주소는 NAT일 수 없습니다.

3. Azure에서 Azure Stack Edge 서비스에 대해 활성화된 유효한 Azure 구독에 대한 액세스 권한이 있어야 합니다. 이 구독을 사용하여 Azure에서 해당 리소스를 만들어 Azure Stack Edge Pro R 디바이스를 관리합니다.  

4. Azure Stack Edge Pro R 디바이스에 액세스하는 데 사용할 Windows 클라이언트에 액세스할 수 있습니다. 이 클라이언트를 사용하여 클라우드에서 프로그래밍 방식으로 구성합니다.

    1. Windows 클라이언트에 필요한 PowerShell 버전을 설치하려면 다음 명령을 실행합니다.

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Azure 계정 및 구독에 연결하려면 다음 명령을 실행합니다.

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        VPN을 구성하기 위해 Azure Stack Edge Pro R 디바이스와 함께 사용하는 Azure 구독 이름을 제공합니다.

    3. 클라우드에서 구성하는 데 필요한 [스크립트를 다운로드](https://aka.ms/ase-vpn-deployment)합니다. 스크립트는 다음을 수행합니다.
        
        - Azure 가상 네트워크와 *GatewaySubnet* 및 *AzureFirewallSubnet* 서브넷을 만듭니다.
        - Azure VPN 게이트웨이를 만들어 구성합니다.
        - Azure 로컬 네트워크 게이트웨이를 만들어 구성합니다.
        - Azure VPN 게이트웨이와 로컬 네트워크 게이트웨이 간에 Azure VPN 연결을 만들어 구성합니다.
        - Azure Firewall을 만들고 네트워크 규칙, 앱 규칙을 추가합니다.
        - Azure 경로 테이블을 만들고 경로를 추가합니다.


## <a name="use-the-script"></a>스크립트 사용

먼저 `parameters.json` 매개 변수를 입력하도록 파일을 수정합니다. 그런 다음, 수정된 json 파일을 사용하여 스크립트를 실행합니다.

이러한 각 단계는 다음 섹션에서 설명합니다.

### <a name="download-service-tags-file"></a>서비스 태그 파일 다운로드

스크립트를 다운로드한 폴더에 `ServiceTags.json` 파일이 이미 있을 수 있습니다. 그렇지 않은 경우 서비스 태그 파일을 다운로드할 수 있습니다.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>매개 변수 파일 수정

첫 번째 단계는 `parameters.json` 파일을 수정하고 변경 내용을 저장하는 것입니다. 


만든 Azure 리소스의 경우 다음 이름을 제공합니다.

|매개 변수 이름  |Description  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure Virtual Network 이름        |
|azureFirewalls_firewall_name     | Azure Firewall 이름        |
|routeTables_routetable_name     | Azure 경로 테이블 이름        |
|publicIPAddresses_VNGW_public_ip_name     | 가상 네트워크 게이트웨이의 공용 IP 이름입니다.       |
|virtualNetworkGateways_VNGW_name    | Azure VPN 게이트웨이(가상 네트워크 게이트웨이) 이름        |
|publicIPAddresses_firewall_public_ip_name     | Azure Firewall의 공용 IP 주소 이름         |
|localNetworkGateways_LNGW_name    |Azure 로컬 네트워크 게이트웨이 이름          |
|connections_vngw_lngw_name    | Azure VPN 연결 이름 가상 네트워크 게이트웨이와 로컬 네트워크 게이트웨이 간의 연결입니다.       |
|위치     |가상 네트워크를 만들려는 지역입니다. 디바이스와 연결된 지역을 선택합니다.         |

다음 IP 주소 및 주소 공간은 가상 네트워크 및 관련 서브넷(default, firewall, GatewaySubnet)을 포함하여 생성한 Azure 리소스와 관련이 있습니다.

|매개 변수 이름  |Description  |
|---------|---------|
|VnetIPv4AddressSpace    | 가상 네트워크와 연결된 주소 공간입니다.       |
|DefaultSubnetIPv4AddressSpace    |가상 네트워크의 `Default` 서브넷과 연결된 주소 공간입니다.         |
|FirewallSubnetIPv4AddressSpace    |가상 네트워크의 `Firewall` 서브넷과 연결된 주소 공간입니다.          |
|GatewaySubnetIPv4AddressSpace    |가상 네트워크의 `GatewaySubnet`과 연결된 주소 공간입니다.          |
|GatewaySubnetIPv4bgpPeeringAddress    | BGP 통신용으로 예약된 IP 주소이며 가상 네트워크의 `GatewaySubnet`과 연결된 주소 공간을 기반으로 합니다.          |

다음 IP 주소와 주소 공간은 온-프레미스 네트워크(Azure Stack Edge Pro R 디바이스가 배포된 위치)와 관련이 있습니다.

|매개 변수 이름  |Description  |
|---------|---------|
|CustomerNetworkAddressSpace    |  개인 IP 주소에 대한 주소 공간입니다.       |
|CustomerPublicNetworkAddressSpace    |  공용 IP 주소에 대한 주소 공간입니다.       |
|DbeIOTNetworkAddressSpace    |이 IP 주소는 IoT 서비스용으로 예약되어 있습니다. 이 매개 변수를 변경하지 마세요.        |
|AzureVPNsharedKey    |이 공유 키는 Azure VPN 연결 리소스를 만드는 동안 사용됩니다. 이 키는 로컬 웹 UI VPN 구성 중에 VPN 공유 비밀로도 제공됩니다.         |
|DBE-Gateway-ipaddress   | Azure Stack Edge Pro R 디바이스에 대한 공용 IP 주소입니다. 이는 알려지지 않았을 수 있으며, 자리 표시자 IP 주소를 사용하여 스크립트를 실행할 수 있습니다. 나중에 실제 IP 주소를 사용하여 로컬 네트워크 게이트웨이를 편집합니다.     |

#### <a name="caveats-to-keep-in-mind"></a>주의할 사항:

- Azure Stack Edge Pro R 디바이스의 IP 주소는 없습니다. 자리 표시자 IP 주소를 사용하여 리소스를 만든 후 나중에 실제 디바이스 IP 주소 및 디바이스의 로컬 네트워크에 대한 주소 공간을 할당하도록 Azure 로컬 네트워크 게이트웨이를 수정할 수 있습니다.
- IETF(Internet Assigned Numbers Authority)에서 IANA의 방향에 따라 172.16.x.y~172.24.z.a의 서브넷을 사용하세요. Azure 네트워크에 대한 172.24 IPv4 주소 범위를 예약합니다.

### <a name="run-the-script"></a>스크립트 실행

다음 단계에 따라 수정된 `parameters.json`을 사용하고 스크립트를 실행하여 Azure 리소스를 만듭니다.

1. PowerShell을 관리자 권한으로 실행합니다.

2. 스크립트가 있는 디렉터리로 전환합니다.

3. 스크립트를 실행합니다.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    샘플 출력은 다음과 같습니다.

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    이 스크립트를 실행하는 데 약 90분이 걸립니다. 스크립트가 완료되면 스크립트가 있는 동일한 폴더에 배포 로그가 생성됩니다.


## <a name="verify-the-azure-resources"></a>Azure 리소스 확인

스크립트를 성공적으로 실행한 후 모든 리소스가 Azure에서 생성되었는지 확인합니다.

그런 다음, 디바이스의 로컬 웹 UI에서 VPN을 구성합니다.


## <a name="vpn-configuration-on-the-device"></a>디바이스의 VPN 구성

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>VPN 확인

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>VPN을 통한 데이터 전송 유효성 검사

VPN이 작동하는지 확인하려면 데이터를 SMB 공유로 복사합니다. Azure Stack Edge Pro R 디바이스에서 [공유 추가](azure-stack-edge-gpu-manage-shares.md#add-a-share)의 단계를 따릅니다. 

1. 파일(예: \data\pictures\waterfall.jpg)을 클라이언트 시스템에 탑재한 SMB 공유에 복사합니다. 
2. 이 파일이 클라우드의 스토리지 계정에 표시되는지 확인합니다.

데이터가 VPN을 통과하는지 확인하려면 다음을 수행합니다.

1. 리소스 그룹에 있는 연결 리소스를 엽니다. 

2. 데이터 입력 및 데이터 출력 값을 확인합니다.
 
3. 리소스 그룹에서 Virtual Network 게이트웨이를 엽니다. **총 터널 수신** 및 **총 터널 송신** 에 대 한 차트를 봅니다.
 
## <a name="debug-issues"></a>문제 디버그

문제를 디버그하려면 다음 명령을 사용합니다.

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

[Azure Stack Edge Pro R 디바이스의 로컬 UI를 통해 VPN 구성](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
