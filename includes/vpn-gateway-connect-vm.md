---
title: 파일 포함
description: 파일 포함
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a8d1b27fc040e6aed0bdeeb86b2e6c4df13f87c3
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540903"
---
VM에 원격 데스크톱 연결을 만들어 VNet에 배포된 VM에 연결할 수 있습니다. 처음에 VM에 연결할 수 있는지 확인하는 가장 좋은 방법은 컴퓨터 이름이 아닌 개인 IP 주소를 사용하여 연결하는 것입니다. 이 방법을 사용하면 연결할 수 있는지, 이름 확인이 제대로 구성되었는지 테스트할 수 있습니다.

1. 개인 IP 주소를 찾습니다. Azure Portal에서 VM의 속성을 살펴보거나 PowerShell을 사용하여 VM의 개인 IP 주소를 찾을 수 있습니다.

   * Azure Portal - Azure Portal에서 가상 머신을 찾습니다. VM 속성을 봅니다. 개인 IP 주소가 나열됩니다.

   * PowerShell - 예제를 사용하여 리소스 그룹의 VM 및 개인 IP 주소 목록을 봅니다. 이 예제는 수정하지 않고 그냥 사용하면 됩니다.

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

1. 지점 및 사이트 간 VPN 연결을 사용하여 VNet에 연결되었는지 확인합니다.
1. 작업 표시줄에서 검색 상자에 "RDP" 또는 "원격 데스크톱 연결"을 입력하여 **원격 데스크톱 연결** 을 열고 원격 데스크톱 연결을 선택합니다. PowerShell에서 'mstsc' 명령을 사용하여 원격 데스크톱 연결을 열 수도 있습니다. 
1. 원격 데스크톱 연결에서 VM의 개인 IP 주소를 입력합니다. "옵션 표시"를 클릭하여 추가 설정을 조정한 다음 연결할 수 있습니다.

**연결 문제 해결**

VPN 연결을 통해 가상 머신에 연결하는 데 문제가 있는 경우 다음 항목을 확인합니다.

* VPN 연결이 성공했는지 확인합니다.

* VM의 개인 IP 주소에 연결하고 있는지 확인합니다.

* 컴퓨터 이름이 아닌 개인 IP 주소를 사용하여 VM에 연결할 수 있는 경우 DNS를 올바르게 구성했는지 확인합니다. 이름 확인이 VM에서 작동하는 방법에 대한 자세한 내용은 [VM에서 이름 확인](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

* RDP 연결에 대한 자세한 내용은 [VM에 대한 원격 데스크톱 연결 문제 해결](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)을 참조하세요.