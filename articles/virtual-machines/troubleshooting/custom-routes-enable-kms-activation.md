---
title: Azure 사용자 지정 경로를 사용하여 강제 터널링으로 KMS 정품 인증 | Microsoft Docs
description: Azure에서 Azure 사용자 지정 경로를 사용하여 강제 터널링으로 KMS 정품 인증을 수행하는 방법을 보여줍니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: f1e2ab6a954361a7807d78dc2baf5d24af52a679
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797586"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>강제 터널링 시나리오에서 Windows 정품 인증 실패

이 문서에서는 사이트 간 VPN 연결 또는 ExpressRoute 시나리오에서 강제 터널링을 사용하도록 설정했을 때 발생할 수 있는 KMS 정품 인증 문제를 해결하는 방법을 설명합니다.

## <a name="symptom"></a>증상

Azure Virtual Network 서브넷에서 [강제 터널링](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)을 사용하도록 설정하여 모든 인터넷 바인딩 트래픽을 온-프레미스 네트워크로 다시 전송합니다. 이 시나리오에서 Windows Server 2012 R2 이상을 실행하는 Azure VM(Virtual Machines)은 Windows를 성공적으로 정품 인증할 수 있습니다. 그러나 이전 버전의 Windows를 실행하는 VM은 Windows 정품을 인증하지 못합니다. 

## <a name="cause"></a>원인

Azure Windows VM은 Windows 정품 인증을 위해 Azure KMS 서버에 연결해야 합니다. 이러한 정품 인증을 위해서는 Azure 공용 IP 주소에서 정품 인증 요청을 수행해야 합니다. 강제 터널링 시나리오에서는 정품 인증 요청이 Azure 공용 IP가 아닌 온-프레미스 네트워크에서 수행되므로 정품 인증에 실패합니다. 

## <a name="solution"></a>해결 방법

이 문제를 해결하려면 Azure 사용자 지정 경로를 사용하여 정품 인증 트래픽을 Azure KMS 서버(23.102.135.246)로 보냅니다. 

IP 주소 23.102.135.246은 Azure 글로벌 클라우드용 KMS 서버의 IP 주소입니다. 해당 DNS 이름은 kms.core.windows.net입니다. Azure Germany 등의 다른 Azure 플랫폼을 사용하는 경우 해당 KMS 서버의 IP 주소를 사용해야 합니다. 자세한 내용은 다음 표를 참조하세요.

|플랫폼| KMS DNS|KMS IP|
|------|-------|-------|
|Azure 글로벌|kms.core.windows.net|23.102.135.246|
|Azure Germany|kms.core.cloudapi.de|51.4.143.248|
|Azure 미국 정부|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


사용자 지정 경로를 추가하려면 다음 단계를 수행합니다.

### <a name="for-resource-manager-vms"></a>Resource Manager VM

1. Azure PowerShell을 연 다음, [Azure 구독에 로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)합니다.
2. 다음 명령을 실행합니다.

    ```powershell
    # First, we will get the virtual network hosts the VMs that has activation problems. In this case, I get virtual network ArmVNet-DM in Resource Group ArmVNet-DM

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, we create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out

    $RouteTable = New-AzureRmRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzureRmRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzureRmRouteTable -RouteTable $RouteTable
    ```
3. 정품 인증 문제가 있는 VM으로 이동한 후 [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)을 사용하여 KMS 서버에 연결할 수 있는지 테스트합니다.

        psping kms.core.windows.net:1688

4. Windows 정품 인증을 시도하여 문제가 해결되었는지 확인합니다.

### <a name="for-classic-vms"></a>클래식 VM

1. Azure PowerShell을 연 다음, [Azure 구독에 로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)합니다.
2. 다음 명령을 실행합니다.

    ```powershell
    # First, we will create a new route table
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the routetable that was created
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply KMS route table to the subnet that host the problem VMs (in this case, I will apply it to the subnet named Subnet-1)
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. 정품 인증 문제가 있는 VM으로 이동한 후 [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)을 사용하여 KMS 서버에 연결할 수 있는지 테스트합니다.

        psping kms.core.windows.net:1688

4. Windows 정품 인증을 시도하여 문제가 해결되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [KMS 클라이언트 설정 키](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [정품 인증 방법 검토 및 선택](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)