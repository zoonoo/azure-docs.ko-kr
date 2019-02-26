---
title: Azure PowerShell 스크립트 샘플 - 네이티브 Azure 인증서 인증을 사용하여 지점 및 사이트 간 VPN 구성 | Microsoft Docs
description: 자체 서명된 인증서를 사용하여 네이티브 Azure 인증서 인증으로 지점 및 사이트 간 VPN을 구성합니다. 이 문서에서는 PowerShell을 사용합니다.
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/17/2018
ms.author: alzam
ms.openlocfilehash: a20a71ad527ee4f6e0a52a6c59881af0aa7fded2
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415666"
---
# <a name="create-a-vpn-gateway-and-add-point-to-site-configuration-using-powershell"></a>PowerShell을 사용하여 VPN Gateway를 만들고 지점 및 사이트 간 구성 추가

이 스크립트는 경로 기반 VPN Gateway를 만들고 네이티브 Azure 인증서 인증을 사용하여 지점 및 사이트 간 구성을 추가합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "10.0.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $BESubPrefix = "10.1.1.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "192.168.0.0/24"
  $RG = "TestRG1"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"
  $GWIPconfName = "gwipconf"
# Create a resource group
New-AzResourceGroup -Name TestRG1 -Location EastUS
# Create a virtual network
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
# Create a subnet configuration
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
 -Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
# Add the VPN client address pool
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
# Create a self-signed root certificate
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
 -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
 -HashAlgorithm sha256 -KeyLength 2048 `
 -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
# Export the root certificate to "C:\cert\P2SRootCert.cer"
# Upload the root certificate public key information
$P2SRootCertName = "P2SRootCert.cer"
$filePathForCert = "C:\cert\P2SRootCert.cer"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
$CertBase64 = [system.convert]::ToBase64String($cert.RawData)
$p2srootcert = New-AzVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName `
 -VirtualNetworkGatewayname "VNet1GW" `
 -ResourceGroupName "TestRG1" -PublicCertData $CertBase64

```

## <a name="clean-up-resources"></a>리소스 정리

만든 리소스가 더 이상 필요하지 않으면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹을 삭제합니다. 그러면 리소스 그룹 및 포함된 모든 리소스가 삭제됩니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 서브넷 구성을 추가합니다. 이 구성은 가상 네트워크 만들기 프로세스에서 사용됩니다. |
| [Add-AzVpnClientRootCertificate](/powershell/module/az.network/add-azvpnclientrootcertificate) | 루트 인증서 공개 키 정보를 VPN 게이트웨이에 업로드합니다.|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 가상 네트워크 세부 정보를 불러옵니다. |
| [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | 가상 네트워크 게이트웨이 세부 정보를 불러옵니다. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 가상 네트워크 서브넷 구성 세부 정보를 불러옵니다. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 서브넷 구성을 만듭니다. 이 구성은 가상 네트워크 만들기 프로세스에서 사용됩니다. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 가상 네트워크를 만듭니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 공용 IP 주소를 만듭니다. |
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 게이트웨이 IP 구성을 새로 만듭니다. |
| [New-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgateway?view=azurermps-6.8.1) | VPN 게이트웨이를 만듭니다. |
| [New-SelfSignedCertificate]https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate?view=win10-ps) | 자체 서명된 루트 인증서를 새로 만듭니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 가상 네트워크에 대한 서브넷 구성을 설정합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.
