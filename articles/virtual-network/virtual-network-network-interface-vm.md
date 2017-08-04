---
title: "Azure 가상 컴퓨터에서 네트워크 인터페이스 추가 또는 제거 | Microsoft Docs"
description: "가상 컴퓨터에서 네트워크 인터페이스를 추가하거나 제거하는 방법에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4273a8ec344c83d36d3383f991d1764d2285beeb
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>가상 컴퓨터에서 네트워크 인터페이스 추가 또는 제거

VM을 만들 때 기존 네트워크 인터페이스를 추가하는 방법 또는 중지됨(할당 취소됨) 상태에 있는 기존 VM에서 네트워크 인터페이스를 추가하거나 제거하는 방법에 대해 알아봅니다. 네트워크 인터페이스를 사용하면 Azure VM(가상 컴퓨터)에서 인터넷, Azure 및 온-프레미스 리소스와 통신할 수 있습니다. 하나의 VM에 하나 이상의 네트워크 인터페이스가 있을 수 있습니다. 

네트워크 인터페이스에 대한 IP 주소를 추가, 변경 또는 제거해야 하는 경우 [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md) 문서를 참조하세요. 네트워크 인터페이스를 만들기, 변경 또는 삭제해야 하는 경우 [네트워크 인터페이스 관리](virtual-network-network-interface.md) 문서를 참조하세요.

## <a name="before"></a>시작하기 전에

이 문서에서 설명하는 섹션의 모든 단계를 수행하려면 다음 작업을 완료해야 합니다.

- [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 크기 문서를 검토하여 각 Linux 및 Windows VM 크기에서 지원하는 네트워크 인터페이스의 수에 대해 알아봅니다.
- Azure 계정이 있는 Azure Portal, Azure CLI(명령줄 인터페이스) 또는 Azure PowerShell에 로그인합니다. 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- PowerShell 명령을 사용하여 이 문서의 작업을 수행하는 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서의 단계를 완료하여 Azure PowerShell을 설치하고 구성합니다. 최신 버전의 Azure PowerShell commandlet을 설치했는지 확인합니다. 예제와 함께 PowerShell 명령에 대한 도움말을 보려면 `get-help <command> -full`을 입력합니다.
- Azure CLI(명령줄 인터페이스) 명령을 사용하여 이 문서의 작업을 수행하는 경우 [Azure CLI 설치 및 구성 방법](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서의 단계를 완료하여 Azure CLI를 설치하고 구성합니다. 최신 버전의 Azure CLI를 설치했는지 확인합니다. CLI 명령에 대한 도움말을 보려면 `az <command> --help`를 입력합니다.

## <a name="about"></a>네트워크 인터페이스 및 VM 정보

네트워크 인터페이스가 현재 다른 VM에 연결되어 있지 않으면 VM을 만들 때 이 VM에 기존 네트워크 인터페이스를 추가(연결)할 수 있습니다. VM이 중지됨(할당 취소됨) 상태에 있으면 기존 VM에서 네트워크 인터페이스를 추가하거나 제거(분리)할 수 있습니다. Azure Portal을 사용하여 VM을 만드는 경우 포털에서 사용자에 대한 기본 설정이 포함된 네트워크 인터페이스를 만듭니다. 포털에서는 다음 작업을 수행할 수 없습니다.

- VM을 만들 때 추가할 기존 네트워크 인터페이스 지정
- 여러 네트워크 인터페이스를 사용하는 VM 만들기
- 네트워크 인터페이스에 대한 이름 지정(포털에서 기본 이름과 함께 네트워크 인터페이스 생성)

Azure PowerShell 또는 CLI를 사용하여 포털을 사용할 수 없는 이전의 모든 특성이 포함된 네트워크 인터페이스 또는 VM을 만들 수 있습니다. 다음 섹션의 작업을 수행하기 전에 다음 제약 조건과 동작을 고려하세요.

- 모든 VM 크기는 두 개 이상의 네트워크 인터페이스를 지원하지만, 일부 VM 크기는 세 개 이상의 네트워크 인터페이스를 지원합니다. 과거에는 일부 VM 크기가 하나의 네트워크 인터페이스만 지원했습니다. 각 VM 크기에서 지원하는 네트워크 인터페이스의 수에 대한 자세한 내용은 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 크기 문서를 참조하세요. 
- 이전에는 여러 네트워크 인터페이스를 지원하고 두 개 이상의 네트워크 인터페이스를 사용하여 생성된 VM에만 네트워크 인터페이스를 추가할 수 있었습니다. VM 크기가 여러 네트워크 인터페이스를 지원하는 경우에도 하나의 네트워크 인터페이스를 사용하여 만들어진 VM에 네트워크 인터페이스를 추가하지 못했습니다. 반대로 두 개 이상의 네트워크 인터페이스로 만든 VM에는 항상 두 개 이상의 네트워크 인터페이스가 있어야 하므로 세 개 이상의 네트워크 인터페이스가 있는 VM에서만 네트워크 인터페이스를 제거할 수 있었습니다. 이러한 제약 조건은 더 이상 적용되지 않습니다. 이제 VM에 항상 네트워크 인터페이스가 하나 이상 있으면 VM 크기에서 지원하는 수까지에 해당하는 수의 네트워크 인터페이스가 있는 VM을 만들고 중지됨(할당 취소됨) 상태의 VM에서 네트워크 인터페이스를 추가하거나 제거할 수 있습니다.
- 기본적으로 VM에서 첫 번째 네트워크 인터페이스는 *기본* 네트워크 인터페이스로 정의됩니다. VM의 다른 모든 네트워크 인터페이스는 *보조* 네트워크 인터페이스입니다.
- 기본 네트워크 인터페이스에는 Azure DHCP 서버에서 기본 게이트웨이를 할당하지만, 보조 네트워크 인터페이스에는 그렇지 않습니다. 보조 네트워크 인터페이스에는 기본 게이트웨이가 할당되지 않으므로 이 네트워크 인터페이스는 기본적으로 서브넷 외부의 리소스와 통신할 수 없습니다. Windows VM의 보조 네트워크 인터페이스가 서브넷 외부의 리소스와 통신할 수 있게 하려면 Windows 명령줄에서 `route add` 명령을 사용하여 운영 체제에 경로를 추가합니다. Linux VM의 경우 기본 동작에서 취약한 호스트 라우팅을 사용하므로 보조 네트워크 인터페이스의 트래픽을 단일 서브넷으로 제한하는 것이 좋습니다. 보조 네트워크 인터페이스의 서브넷 외부에 연결해야 하는 경우 정책 기반 라우팅을 사용하도록 설정하여 수신 및 송신 트래픽에서 동일한 네트워크 인터페이스를 사용하도록 합니다.
- 기본적으로 VM의 모든 아웃바운드 트래픽은 기본 네트워크 인터페이스의 기본 IP 구성에 할당된 IP 주소로 보내집니다. VM 운영 체제 내에서 아웃바운드 트래픽에 사용되는 IP 주소는 기본적으로 기본 네트워크 인터페이스를 통해 제어할 수 있습니다.
- 과거에는 동일한 가용성 집합 내의 모든 VM에 단일 또는 다중 네트워크 인터페이스가 있어야 했습니다. 여러 개의 네트워크 인터페이스가 있는 VM은 이제 VM 크기에서 지원하는 수의 동일한 가용성 집합에 있을 수 있습니다. VM을 만들 때 가용성 집합에만 VM을 추가할 수 있습니다. 가용성 집합에 대한 자세한 내용은 [Azure에서 VM의 가용성 관리](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) 문서를 참조하세요.
- 동일한 VM에 있는 네트워크 인터페이스는 VNet 내의 다른 서브넷에 연결할 수 있지만, 해당 네트워크 인터페이스는 모두 동일한 VNet에 연결되어야 합니다.
- 기본 또는 보조 네트워크 인터페이스의 IP 구성에 대한 IP 주소를 Azure Load Balancer 백 엔드 풀에 추가할 수 있습니다. 이전에 기본 네트워크 인터페이스의 기본 IP 주소만 백 엔드 풀에 추가할 수 있었습니다. IP 주소 및 구성에 대한 자세한 내용은 [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md) 문서를 참조하세요.
- VM을 삭제해도 연결된 네트워크 인터페이스는 삭제되지 않습니다. VM이 삭제되면 VM에서 네트워크 인터페이스가 분리됩니다. 네트워크 인터페이스를 다른 VM에 추가하거나 삭제할 수 있습니다.

## <a name="vm-create"></a>새 VM에 기존 네트워크 인터페이스 추가
포털을 통해 VM을 만들면 포털에서 기본 설정이 포함된 네트워크 인터페이스를 만들고 이 네트워크 인터페이스를 사용자에 대한 VM에 연결합니다. Azure Portal에서는 기존 네트워크 인터페이스를 새 VM에 추가하거나 여러 네트워크 인터페이스가 있는 VM을 만들 수 없습니다. 둘 다 CLI 또는 PowerShell을 사용하여 수행할 수 있습니다. 만드는 VM 크기에서 지원하는 만큼 많은 네트워크 인터페이스를 VM에 추가할 수 있습니다. 각 VM 크기에서 지원하는 네트워크 인터페이스의 수에 대한 자세한 내용은 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 크기 문서를 참조하세요. VM에 추가한 네트워크 인터페이스는 현재 다른 VM에 연결할 수 없습니다. 네트워크 인터페이스 만들기에 대한 자세한 내용은 [네트워크 인터페이스 관리](virtual-network-network-interface.md#create-nic) 문서를 참조하세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>기존 VM에 기존 네트워크 인터페이스 추가

네트워크 인터페이스를 추가하는 VM 크기에서 지원하는 만큼 많은 네트워크 인터페이스를 VM에 추가할 수 있습니다. 각 VM 크기에서 지원하는 네트워크 인터페이스의 수에 대한 자세한 내용은 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 크기 문서를 참조하세요. 네트워크 인터페이스를 추가하려는 VM은 여러 네트워크 인터페이스를 지원하고 중지(할당 취소)된 상태여야 합니다. 추가하려는 네트워크 인터페이스는 현재 다른 VM에 연결할 수 없습니다. Azure Portal에서는 기존 VM에 네트워크 인터페이스를 추가할 수 없습니다. CLI 또는 PowerShell을 사용하여 기존 VM에 네트워크 인터페이스를 추가해야 합니다.

|도구|명령|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|[VM에 NIC 추가](../virtual-machines/windows/multiple-nics.md#add-a-nic-to-an-existing-vm)에 대한 자세한 단계|

## <a name="vm-view-nic"></a> VM에 대한 네트워크 인터페이스 보기

VM에 현재 연결된 네트워크 인터페이스를 보고 각 네트워크 인터페이스의 구성 및 각 네트워크 인터페이스에 할당된 IP 주소를 확인할 수 있습니다. 

1. 구독에 대한 소유자, 참가자 또는 네트워크 참가자 역할이 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 역할을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 문서를 참조하세요.
2. Azure Portal 위쪽의 *리소스 검색* 텍스트가 있는 상자에서 *가상 컴퓨터*를 입력합니다. 검색 결과에 표시된 **가상 컴퓨터**를 클릭합니다.
3. 표시되는 **가상 컴퓨터** 블레이드에서 네트워크 인터페이스를 보려는 VM의 이름을 클릭합니다.
4. 선택한 VM에 대해 표시되는 가상 컴퓨터 블레이드의 **설정** 섹션에서 **네트워크 인터페이스**를 클릭합니다. 네트워크 인터페이스 설정 및 변경 방법에 대한 자세한 내용은 [네트워크 인터페이스 관리](virtual-network-network-interface.md) 문서를 참조하세요. 네트워크 인터페이스에 할당된 IP 주소를 추가, 변경 또는 제거하는 방법에 대한 자세한 내용은 [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md) 문서를 참조하세요.

**명령**

|도구|명령|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic">VM에서 네트워크 인터페이스 제거</a>

네트워크 인터페이스를 제거하려는 VM은 중지됨(할당 취소됨) 상태여야 하며, 현재 두 개 이상의 네트워크 인터페이스가 연결되어 있어야 합니다. 네트워크 인터페이스는 모두 제거할 수 있지만, VM에는 항상 네트워크 인터페이스가 하나 이상 연결되어 있어야 합니다. 기본 네트워크 인터페이스를 제거하면 Azure에서는 가장 오랫동안 VM에 연결어 있는 네트워크 인터페이스에 기본 특성을 할당합니다. 네트워크 인터페이스를 기본 네트워크 인터페이스로 직접 지정할 수 있습니다. Azure Portal을 사용하여 VM에서 네트워크 인터페이스를 제거하거나 네트워크 인터페이스의 기본 특성을 설정할 수는 없지만, CLI 또는 PowerShell을 사용하여 두 작업을 모두 수행할 수 있습니다. 

**명령**

|도구|명령|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|[VM에 NIC 제거](../virtual-machines/windows/multiple-nics.md#remove-a-nic-from-an-existing-vm)에 대한 자세한 단계|
## <a name="next-steps"></a>다음 단계
여러 네트워크 인터페이스 또는 IP 주소가 있는 VM을 만들려면 다음 문서를 참조하세요.

**명령**

|작업|도구|
|---|---|
|여러 NIC를 사용하여 VM 만들기|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|여러 IP 주소가 있는 단일 NIC VM 만들기|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

