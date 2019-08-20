---
title: Azure 오스트레일리아의 azure VPN Gateway
description: Azure 오스트레일리아에서 VPN Gateway를 구현 하 여 ISM을 준수 하 고 오스트레일리아 정부 기관을 효과적으로 보호
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 00588042fa11ace51eef40cdedbae14c1bd99801
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575427"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure 오스트레일리아의 azure VPN Gateway

공용 클라우드를 사용 하는 중요 한 서비스는 클라우드 리소스와 서비스를 기존 온-프레미스 시스템에 안전 하 게 연결 하는 것입니다. Azure에서이 기능을 제공 하는 서비스는 Azure VPN Gateway입니다. 이 문서에서는 오스트레일리아 신호 Directorate의 (ASD) [ISM (Information Security Manual) 컨트롤](https://acsc.gov.au/infosec/ism/)을 준수 하도록 VPN gateway를 구성할 때 고려해 야 할 주요 사항을 간략하게 설명 합니다.

VPN 게이트웨이는 Azure의 가상 네트워크와 다른 네트워크 간에 암호화 된 트래픽을 보내는 데 사용 됩니다. VPN 게이트웨이에서는 세 가지 시나리오를 다룹니다.

- 사이트 간 (S2S)
- 지점 및 사이트 간 (P2S)
- 네트워크-네트워크

이 문서에서는 S2S VPN gateway에 대해 집중적으로 설명 합니다. 다이어그램 1은 S2S VPN gateway 구성의 예를 보여 줍니다.

![다중 사이트 연결을 사용 하는 VPN gateway](media/vpngateway-multisite-connection-diagram.png)

*다이어그램 1 – Azure S2S VPN Gateway*

## <a name="key-design-considerations"></a>주요 디자인 고려 사항

Azure를 오스트레일리아 정부 고객에 게 연결 하는 세 가지 네트워킹 옵션이 있습니다.

- 아이콘과
- Azure ExpressRoute
- 공용 인터넷

오스트레일리아 사이버 Security Center의 [Azure에 대 한 소비자 가이드](https://servicetrust.microsoft.com/viewpage/Australia) 는이 세 가지 네트워킹 옵션과 함께 VPN Gateway (또는 동등한 보호 된 인증 타사 서비스)를 사용 하는 것을 권장 합니다. 이러한 권장 사항은 연결이 암호화 및 무결성을 위해 ISM 컨트롤을 따르는지 확인 하는 것입니다.

### <a name="encryption-and-integrity"></a>암호화 및 무결성

기본적으로 VPN은 IKE 핸드셰이크의 일부로 연결을 설정 하는 동안 암호화 및 무결성 알고리즘 및 매개 변수를 협상 합니다. IKE 핸드셰이크 중에 기본 설정의 구성 및 순서는 VPN gateway가 개시자 인지 또는 응답자 인지에 따라 달라 집니다. 이 지정은 VPN 장치를 통해 제어 됩니다. 연결의 최종 구성은 VPN 장치의 구성에 의해 제어 됩니다. 유효성이 확인 된 VPN 장치 및 해당 구성에 대 한 자세한 내용은 [vpn 서비스 정보](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)를 참조 하세요.

VPN 게이트웨이는 연결에 대 한 사용자 지정 IPsec/IKE 정책을 구성 하 여 암호화 및 무결성을 제어할 수 있습니다.

### <a name="resource-operations"></a>리소스 작업

VPN 게이트웨이는 공용 인터넷을 통해 Azure 및 비 Azure 환경 간의 연결을 만듭니다. ISM에는 연결의 명시적 권한 부여와 관련 된 컨트롤이 있습니다. 기본적으로 VPN gateway를 사용 하 여 보안 환경에 무단 터널을 만들 수 있습니다. 조직에서는 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 VPN 게이트웨이 및 해당 연결을 만들고 수정할 수 있는 사람을 제어 하는 것이 중요 합니다. Azure에는 VPN 게이트웨이를 관리할 수 있는 기본 제공 역할이 없으므로 사용자 지정 역할이 필요 합니다.

소유자, 참가자 및 네트워크 기여자 역할에 대 한 액세스는 긴밀 하 게 제어 됩니다. 또한 보다 세분화 된 액세스 제어를 위해 Azure Active Directory Privileged Identity Management를 사용 하는 것이 좋습니다.

### <a name="high-availability"></a>고가용성

Azure VPN gateway는 여러 연결을 포함 하 고 동일한 온-프레미스 환경에 대 한 여러 온-프레미스 VPN 장치를 지원할 수 있습니다. 다이어그램 1을 참조 하세요.

Azure의 가상 네트워크에는 독립, 능동-수동 또는 활성-활성 구성으로 배포할 수 있는 여러 VPN 게이트웨이가 있을 수 있습니다.

모든 VPN 게이트웨이를 [항상 사용 가능한 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)으로 배포 하는 것이 좋습니다. 예를 들어 활성-수동 또는 활성-활성 모드의 두 VPN gateway에 연결 된 두 개의 온-프레미스 VPN 장치가 있습니다. 다이어그램 2를 참조 하세요.

![VPN gateway 중복 연결](media/dual-redundancy.png)

*다이어그램 2 – 활성-활성 VPN 게이트웨이 및 두 개의 VPN 장치*

### <a name="forced-tunneling"></a>강제 터널링

강제 터널링은 검사 및 감사를 위해 VPN gateway를 통해 모든 인터넷 바인딩된 트래픽을 온-프레미스 환경으로 다시 리디렉션하거나 강제 합니다. 강제 터널링 없이 Azure의 Vm에서 인터넷 바인딩된 트래픽은 트래픽을 검사 하거나 감사 하는 옵션 없이 Azure 네트워크 인프라를 공용 인터넷으로 직접 트래버스 합니다. 조직에서 보안 보안 게이트웨이 (SIG)를 사용 하 여 환경을 사용 해야 하는 경우 강제 터널링이 중요 합니다.

## <a name="detailed-configuration"></a>자세한 구성

### <a name="service-attributes"></a>서비스 특성

오스트레일리아 정부에 대해 구성 된 S2S 연결에 대 한 VPN 게이트웨이에는 다음 특성이 있어야 합니다.

|특성 | 먼저|
|--- | --- |
|gatewayType | VPN|
|

보호할 ISM 컨트롤을 준수 하는 데 필요한 특성 설정은 다음과 같습니다.

|특성 | 먼저|
|--- |---|
|vpnType |경로 기반|
|Vpnclientconfiguration.zip/vpnClientProtocols | IkeV2|
|

Azure VPN 게이트웨이는 IPsec 및 IKE 프로토콜 표준에서 제공 되는 다양 한 암호화 알고리즘을 지원 합니다. 기본 정책은 다양 한 타사 VPN 장치와의 최대 상호 운용성을 설정 합니다. 따라서 IKE 핸드셰이크 중에 비규격 구성이 교섭 될 수 있습니다. VPN gateway에서 Vpnclientconfiguration.zip에 [사용자 지정 IPsec/IKE 정책](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) 매개 변수를 적용 하 여 연결이 Azure에 대 한 온-프레미스 환경 연결에 대 한 ISM 컨트롤을 충족 하는지 확인 하는 것이 좋습니다. 키 특성은 다음 표에 나와 있습니다.

|특성|보아야|먼저|
|---|---|---|
|saLifeTimeSeconds|< 14400 초|> 300 초|
|saDataSizeKilobytes| |> 102KB|
|암호 암호화| |AES256-GCMAES256|
|개인 무결성| |SHA256-GCMAES256|
|ikeEncryption| |AES256-GCMAES256|
|ikeIntegrity| |SHA256-GCMAES256|
|dhGroup|DHGroup14, DHGroup24, ECP256, ECP384|DHGroup2|
|pfsGroup|PFS2048, PFS24, ECP256, ECP384||
|

이전 표에서 dhGroup 및 pfsGroup의 경우 다른 설정을 사용할 수 있는 경우에도 ECP256 및 ECP384를 사용 하는 것이 좋습니다.

### <a name="related-services"></a>관련 서비스

Azure VPN gateway를 디자인 하 고 구성 하는 경우 여러 관련 서비스도 존재 하 고 구성 해야 합니다.

|서비스 | 필요한 동작|
|--- | ---|
|가상 네트워크 | VPN 게이트웨이는 가상 네트워크에 연결 됩니다. 새 VPN gateway를 만들기 전에 가상 네트워크를 만듭니다.|
|공용 IP 주소 | S2S VPN gateway에는 온-프레미스 VPN 장치와 VPN gateway 간에 연결을 설정 하는 공용 IP 주소가 필요 합니다. S2S VPN gateway를 만들기 전에 공용 IP 주소를 만듭니다.|
|Subnet | VPN gateway에 대 한 가상 네트워크의 서브넷을 만듭니다.|
|

## <a name="implementation-steps-using-powershell"></a>PowerShell을 사용 하 여 구현 단계

### <a name="role-based-access-control"></a>역할 기반 액세스 제어

1. 사용자 지정 역할을 만듭니다. 예를 들면 virtualNetworkGateway 기여자입니다. VPN 게이트웨이를 만들고 수정할 수 있는 사용자에 게 할당할 역할을 만듭니다. 사용자 지정 역할은 다음과 같은 작업을 허용 해야 합니다.

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. 네트워크/연결/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/publicIPAddresses/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. VPN 게이트웨이 및 온-프레미스 환경에 대 한 연결을 만들고 관리할 수 있는 사용자에 게 사용자 지정 역할을 추가 합니다.

### <a name="create-a-vpn-gateway"></a>VPN 게이트웨이 만들기

이러한 단계는 가상 네트워크를 이미 만든 것으로 가정 합니다.

1. 새 공용 IP 주소를 만듭니다.
2. VPN gateway 서브넷을 만듭니다.
3. VPN gateway IP 구성 파일을 만듭니다.
4. VPN gateway를 만듭니다.
5. 온-프레미스 VPN 장치에 대 한 로컬 네트워크 게이트웨이를 만듭니다.
6. IPsec 정책을 만듭니다. 이 단계에서는 사용자 지정 IPsec/IKE 정책을 사용 하 고 있다고 가정 합니다.
7. IPsec 정책을 사용 하 여 VPN 게이트웨이와 로컬 네트워크 게이트웨이 간의 연결을 만듭니다.

### <a name="enforce-tunneling"></a>터널링 적용

강제 터널링이 필요한 경우 VPN gateway를 만들기 전에 다음을 수행 합니다.

1. 경로 테이블 및 경로 규칙을 만듭니다.
2. 적절 한 서브넷에 경로 테이블을 연결 합니다.

VPN gateway를 만든 후 다음을 수행 합니다.

- VPN gateway에서 GatewayDefaultSite를 온-프레미스 환경으로 설정 합니다.

### <a name="example-powershell-script"></a>PowerShell 스크립트 예

사용자 지정 IPsec/IKE 정책을 만드는 데 사용 되는 PowerShell 스크립트의 예는 오스트레일리아로 보호 된 보안 분류를 위한 ISM 컨트롤을 준수 합니다.

가상 네트워크, VPN gateway 및 로컬 게이트웨이가 존재 한다고 가정 합니다.

#### <a name="create-an-ipsecike-policy"></a>IPsec/IKE 정책 만들기

다음 샘플 스크립트는 다음 알고리즘 및 매개 변수를 사용하여 IPsec/IKE 정책을 만듭니다.

- IKEv2: AES256, SHA256, DHGroup ECP256
- IPsec: AES256, SHA256, PFS ECP256, SA 수명 14400 초 및 1억240만 KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>사용자 지정 IPsec/IKE 정책을 사용 하 여 S2S VPN 연결 만들기

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 전송 중에 오스트레일리아 정부 보호 데이터를 보호 하기 위해 정보 보안 설명서에 지정 된 요구 사항을 충족 하는 VPN Gateway의 특정 구성에 대해 설명 했습니다. VPN gateway를 구성 하는 방법에 대 한 단계는 다음을 참조 하세요.

- [Azure virtual network 게이트웨이 개요](https://docs.microsoft.com/azure/vpn-gateway/)  
- [VPN Gateway 이란?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [PowerShell을 사용 하 여 사이트 간 VPN 연결로 가상 네트워크 만들기](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [VPN 게이트웨이 만들기 및 관리](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)