---
title: Azure Vm에 네트워크 인터페이스 추가 또는 제거
description: 가상 머신에서 네트워크 인터페이스를 추가하거나 제거하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: f7253be2844f40ca52df2f9b3bc9cbba552fea2b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85480136"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>가상 머신에 네트워크 인터페이스 추가 또는 제거

Azure VM (가상 머신)을 만들 때 기존 네트워크 인터페이스를 추가 하는 방법에 대해 알아봅니다. 또한 중지 됨 (할당 취소 됨) 상태의 기존 VM에서 네트워크 인터페이스를 추가 하거나 제거 하는 방법에 대해 알아봅니다. 네트워크 인터페이스를 사용 하면 Azure VM이 인터넷, Azure 및 온-프레미스 리소스와 통신할 수 있습니다. VM에는 하나 이상의 네트워크 인터페이스가 있습니다. 

네트워크 인터페이스용 IP 주소를 추가, 변경 또는 제거해야 하는 경우 [네트워크 인터페이스 IP 주소 관리](virtual-network-network-interface-addresses.md)를 참조하세요. 네트워크 인터페이스를 만들거나, 변경 하거나, 삭제 하려면 [네트워크 인터페이스 관리](virtual-network-network-interface.md)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

계정이 없으면 활성 구독으로 Azure 계정을 설정 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 이 문서의 나머지 부분을 시작 하기 전에 다음 작업 중 하나를 완료 합니다.

- **포털 사용자**: Azure 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

- **Powershell 사용자**: [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행 하거나 컴퓨터에서 powershell을 실행 합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. Azure Cloud Shell 브라우저 탭에서 **환경 선택** 드롭다운 목록을 찾은 다음 아직 선택 하지 않은 경우 **PowerShell** 을 선택 합니다.

    PowerShell을 로컬로 실행 하는 경우 Azure PowerShell 모듈 버전 1.0.0 이상을 사용 합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az.Network`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. `Connect-AzAccount`를 실행하여 Azure와 연결합니다.

- **AZURE cli (명령줄 인터페이스) 사용자**: [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행 하거나 컴퓨터에서 CLI를 실행 합니다. Azure CLI를 로컬로 실행 하는 경우 Azure CLI 버전 2.0.26 이상을 사용 합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. `az login`를 실행하여 Azure와 연결합니다.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>새 VM에 기존 네트워크 인터페이스 추가

포털을 통해 가상 컴퓨터를 만들면 포털에서 기본 설정이 포함 된 네트워크 인터페이스를 만들고 네트워크 인터페이스를 VM에 연결 합니다. 포털을 사용 하 여 기존 네트워크 인터페이스를 새 VM에 추가 하거나 여러 네트워크 인터페이스가 있는 VM을 만들 수 없습니다. CLI 또는 PowerShell을 사용 하 여 둘 다 수행할 수 있습니다. [제약 조건을](#constraints)숙지 해야 합니다. 여러 네트워크 인터페이스로 VM을 만드는 경우 VM을 만든 후 올바르게 사용되도록 운영 체제도 구성해야 합니다. 여러 네트워크 인터페이스에 대해 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 또는 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics)를 구성하는 방법을 알아보세요.

### <a name="commands"></a>명령

VM을 만들기 전에 [네트워크 인터페이스를 만듭니다](virtual-network-network-interface.md#create-a-network-interface).

|도구|명령|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>기존 VM에 네트워크 인터페이스 추가

가상 컴퓨터에 네트워크 인터페이스를 추가 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 기존 가상 머신을 찾습니다. **가상 머신**을 검색하여 선택합니다.

2. VM의 이름을 선택 합니다. 추가하려는 네트워크 인터페이스 수를 VM이 지원해야 합니다. 각 VM 크기가 지 원하는 네트워크 인터페이스 수를 확인 하려면 Azure에서 [Linux vm](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows vm](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대 한 크기를 참조 하세요.

3. VM 명령 모음에서 **중지**를 선택 하 고 확인 대화 상자에서 **확인** 을 선택 합니다. 그런 다음 VM **상태가** **중지 됨 (할당 취소 됨)** 으로 변경 될 때까지 기다립니다.

4. VM 메뉴 모음에서 **네트워킹**  >  **네트워크 인터페이스 연결**을 선택 합니다. 그런 다음 **기존 네트워크 인터페이스 연결**에서 연결 하려는 네트워크 인터페이스를 선택 하 고 **확인**을 선택 합니다.

    >[!NOTE]
    >선택한 네트워크 인터페이스는 가속화 된 네트워킹을 사용할 수 없고, IPv6 주소를 할당할 수 없으며, 현재 VM에 연결 된 네트워크 인터페이스가 있는 동일한 가상 네트워크에 있어야 합니다.

    기존 네트워크 인터페이스가 없는 경우 하나 만들어야 합니다. 이렇게 하려면 **네트워크 인터페이스 만들기**를 선택합니다. 네트워크 인터페이스를 만드는 방법에 대한 자세한 내용은 [네트워크 인터페이스 만들기](virtual-network-network-interface.md#create-a-network-interface)를 참조하세요. 네트워크 인터페이스를 가상 머신에 추가할 때 적용되는 제약 조건에 대한 자세한 내용은 [제약 조건](#constraints)을 참조하세요.

5. VM 메뉴 모음에서 **개요**  >  **시작** 을 선택 하 여 가상 머신을 다시 시작 합니다.

이제 여러 네트워크 인터페이스를 올바르게 사용 하도록 VM 운영 체제를 구성할 수 있습니다. 여러 네트워크 인터페이스에 대해 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 또는 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics)를 구성하는 방법을 알아보세요.

### <a name="commands"></a>명령

|도구|명령|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (reference); [자세한 단계](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (참조); [자세한 단계](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>VM의 네트워크 인터페이스 보기

VM에 현재 연결된 네트워크 인터페이스를 보고 각 네트워크 인터페이스의 구성 및 각 네트워크 인터페이스에 할당된 IP 주소를 확인할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 기존 가상 머신을 찾습니다. **가상 머신**을 검색하여 선택합니다.

    >[!NOTE]
    >구독에 대 한 소유자, 참가자 또는 네트워크 참가자 역할이 할당 된 계정을 사용 하 여 로그인 합니다. 계정에 역할을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)을 참조하세요.

2. 연결 된 네트워크 인터페이스를 보려는 VM의 이름을 선택 합니다.

3. VM 메뉴 모음에서 **네트워킹**을 선택 합니다.

네트워크 인터페이스 설정 및 변경 방법에 대한 자세한 내용은 [네트워크 인터페이스 관리](virtual-network-network-interface.md)를 참조하세요. 네트워크 인터페이스에 할당된 IP 주소를 추가, 변경 또는 제거하는 방법을 알아보려면 [네트워크 인터페이스 IP 주소 관리](virtual-network-network-interface-addresses.md)를 참조하세요.

### <a name="commands"></a>명령

|도구|명령|
|---|---|
|CLI|[az vm nic list](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>VM에서 네트워크 인터페이스 제거

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 기존 가상 머신을 찾습니다. **가상 머신**을 검색하여 선택합니다.

2. 연결 된 네트워크 인터페이스를 보려는 VM의 이름을 선택 합니다.

3. VM 도구 모음에서 **중지**를 선택 합니다.

4. VM의 **상태**가 **중지됨(할당 취소됨)** 으로 바뀔 때까지 기다립니다.

5. VM 메뉴 모음에서 **네트워킹**  >  **네트워크 인터페이스 분리**를 선택 합니다.

6. **네트워크 인터페이스 분리** 대화 상자에서 분리 하려는 네트워크 인터페이스를 선택 합니다. 그런 다음, **확인**을 선택합니다.

    >[!NOTE]
    >네트워크 인터페이스가 하나만 나열 되는 경우 가상 머신에는 항상 하나 이상의 네트워크 인터페이스가 연결 되어 있어야 하므로 분리할 수 없습니다.

### <a name="commands"></a>명령

|도구|명령|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (reference); [자세한 단계](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (reference); [자세한 단계](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>제약 조건

- VM에는 하나 이상의 네트워크 인터페이스가 연결되어 있어야 합니다.

- VM은 VM 크기에서 지원하는 수만큼 네트워크 인터페이스를 연결할 수 있습니다. 각 VM 크기가 지 원하는 네트워크 인터페이스 수에 대 한 자세한 내용은 [Linux vm](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows vm](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대 한 Azure의 크기를 참조 하세요. 모든 크기는 적어도 두 개의 네트워크 인터페이스를 지원합니다.

- VM에 추가 하는 네트워크 인터페이스는 현재 다른 VM에 연결할 수 없습니다. 네트워크 인터페이스를 만드는 방법에 대한 자세한 내용은 [네트워크 인터페이스 만들기](virtual-network-network-interface.md#create-a-network-interface)를 참조하세요.

- 이전에는 여러 네트워크 인터페이스를 지원 하 고 두 개 이상의 네트워크 인터페이스를 사용 하 여 만든 Vm에만 네트워크 인터페이스를 추가할 수 있었습니다. 단일 네트워크 인터페이스로 만든 VM에 네트워크 인터페이스를 추가할 수 없습니다 .이는 VM 크기가 둘 이상의 네트워크 인터페이스를 지 원하는 경우에도 마찬가지입니다. 반대로 두 개 이상의 네트워크 인터페이스로 만든 VM에는 항상 두 개 이상의 네트워크 인터페이스가 있어야 하므로 세 개 이상의 네트워크 인터페이스가 있는 VM에서만 네트워크 인터페이스를 제거할 수 있었습니다. 이러한 제약 조건은 더 이상 적용 되지 않습니다. 이제 (VM 크기에서 지원하는 수 이내에서) 원하는 수의 네트워크 인터페이스가 있는 VM을 만들 수 있습니다.

- 기본적으로 VM에 연결 된 첫 번째 네트워크 인터페이스는 *기본* 네트워크 인터페이스입니다. VM의 다른 모든 네트워크 인터페이스는 *보조* 네트워크 인터페이스입니다.

- 아웃 바운드 트래픽을 보내는 네트워크 인터페이스를 제어할 수 있습니다. 그러나 VM은 기본적으로 기본 네트워크 인터페이스의 기본 IP 구성에 할당 된 IP 주소에 모든 아웃 바운드 트래픽을 보냅니다.

- 과거에는 동일한 가용성 집합 내의 모든 VM에 단일 또는 다중 네트워크 인터페이스가 있어야 했습니다. 여러 개의 네트워크 인터페이스가 있는 VM은 이제 VM 크기에서 지원하는 수의 동일한 가용성 집합에 있을 수 있습니다. VM을 만들 때 가용성 집합에만 VM을 추가할 수 있습니다. 가용성 집합에 대한 자세한 내용은 [Azure에서 VM의 가용성 관리](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)를 참조하세요.

- 동일한 VM에 있는 네트워크 인터페이스를 가상 네트워크 내의 다른 서브넷에 연결할 수 있습니다. 그러나 네트워크 인터페이스는 모두 동일한 가상 네트워크에 연결 되어 있어야 합니다.

- 기본 또는 보조 네트워크 인터페이스의 IP 구성에 대한 IP 주소를 Azure Load Balancer 백 엔드 풀에 추가할 수 있습니다. 이전에 기본 네트워크 인터페이스의 기본 IP 주소만 백 엔드 풀에 추가할 수 있었습니다. IP 주소 및 구성에 대한 자세한 내용은 [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md)를 참조하세요.

- VM을 삭제 해도 연결 된 네트워크 인터페이스는 삭제 되지 않습니다. VM을 삭제하면 VM에서 네트워크 인터페이스가 분리됩니다. 이러한 네트워크 인터페이스를 다른 Vm에 추가 하거나 삭제할 수 있습니다.

- 문서화 된 최적의 성능을 얻으려면 가속화 된 네트워킹이 필요 합니다. 경우에 따라 [Windows](create-vm-accelerated-networking-powershell.md) 또는 [Linux](create-vm-accelerated-networking-cli.md) 가상 컴퓨터에 대 한 가속 네트워킹을 명시적으로 사용 하도록 설정 해야 합니다.

## <a name="next-steps"></a>다음 단계

여러 네트워크 인터페이스 또는 IP 주소가 있는 VM을 만들려면 다음을 참조 하세요.

|Task|도구|
|---|---|
|여러 NIC를 사용하여 VM 만들기|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|여러 IPv4 주소가 있는 단일 NIC VM 만들기|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Azure Load Balancer 뒤에 프라이빗 IPv6 주소가 있는 단일 NIC VM 만들기|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager 템플릿](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
