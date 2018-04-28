---
title: 가속화된 네트워킹을 사용하는 Azure 가상 머신 만들기 | Microsoft Docs
description: 가속 네트워킹을 사용하는 Linux 가상 머신을 만드는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 718990b69cc75709af819ad7df9a77ad0f8f33ce
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>가속 네트워킹을 사용하는 Linux 가상 머신 만들기

> [!IMPORTANT] 
> 가속 네트워킹을 사용하여 가상 머신을 만들어야 합니다. 기존 가상 머신에서는 이 기능을 사용할 수 없습니다. 가속 네트워킹을 사용하려면 다음 단계를 완료합니다.
>   1. 가상 머신을 삭제합니다.
>   2. 가속 네트워킹을 사용하는 가상 머신을 다시 만듭니다.
>

이 자습서에서는 가속 네트워킹을 사용하는 Linux VM(가상 머신)을 만드는 방법에 대해 알아봅니다. 가속화된 네트워킹을 사용하면 VM에 대한 단일 루트 I/O 가상화(SR-IOV)를 구현할 수 있어 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로는 데이터 경로에서 호스트를 우회함으로써 대기 시간, 지터 및 CPU 사용률을 줄이므로 지원되는 VM 유형에서 가장 까다로운 네트워크 워크로드에 사용합니다. 다음 그림에서는 가속 네트워킹을 사용할 때와 사용하지 않을 때 두 개의 VM 간의 통신을 보여줍니다.

![비교](./media/create-vm-accelerated-networking/accelerated-networking.png)

가속화된 네트워킹을 사용하지 않는 경우 VM에서 들어오고 나가는 모든 네트워킹 트래픽이 호스트와 가상 스위치를 통과해야 합니다. 가상 스위치는 네트워크 보안 그룹, 액세스 제어 목록, 격리 및 네트워크 트래픽에 대한 다른 네트워크 가상화 서비스 등, 모든 정책 적용을 제공합니다. 가상 스위치에 대한 자세한 내용은 [Hyper-V 네트워크 가상화 및 가상 스위치](https://technet.microsoft.com/library/jj945275.aspx) 문서를 참조하세요.

가속화된 네트워킹을 사용하는 경우 네트워크 트래픽이 VM의 NIC(네트워크 인터페이스)에 도착한 다음 VM으로 전달됩니다. 이제 가상 스위치가 적용되는 모든 네트워크 정책은 오프로드되어 하드웨어에 적용됩니다. 하드웨어에서 정책을 적용하면 NIC는 호스트에 적용된 모든 정책을 유지하면서 VM에 직접 네트워크 트래픽을 전달할 수 있으므로 호스트 및 가상 스위치를 우회할 수 있습니다.

가속화된 네트워킹의 이점은 사용하도록 설정된 VM에만 적용된다는 것입니다. 최상의 결과를 얻으려면 동일한 Azure VNet(Virtual Network)에 연결된 둘 이상의 VM에서 이 기능을 사용하도록 설정하는 것이 좋습니다. VNet에서 통신하거나 온-프레미스에 연결할 때 이 기능을 사용하면 전체 대기 시간에 미치는 영향을 최소화할 수 있습니다.

## <a name="benefits"></a>이점
* **더 낮은 대기 시간/더 높은 초당 패킷 수(pps):** 데이터 경로에서 가상 스위치를 제거하면 패킷이 정채 처리를 위해 호스트에서 소요하는 시간이 제거되고 VM 내에서 처리될 수 있는 패킷 수가 늘어납니다.
* **감소된 지터:** 가상 스위치 처리는 적용해야 하는 정책의 양과 처리를 수행하는 CPU의 워크로드에 따라 달라집니다. 정책 적용을 하드웨어로 오프로드하면 패킷이 VM으로 직접 전달되고, 호스트-VM 통신과 모든 소프트웨어 인터럽트 및 컨텍스트 전환이 제거되어 이러한 가변성이 해소됩니다.
* **CPU 사용률 감소:** 호스트의 가상 스위치를 무시하면 네트워크 트래픽 처리에 사용되는 CPU가 감소됩니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제
* **Ubuntu 16.04**: 4.11.0-1013 이상 커널 버전
* **SLES 12 SP3**: 4.4.92-6.18 이상 커널 버전
* **RHEL 7.4**: 7.4.2017120423 이상 커널 버전
* **CentOS 7.4**: 7.4.20171206 이상 커널 버전

## <a name="supported-vm-instances"></a>지원되는 VM 인스턴스
가속 네트워킹은 가장 일반적인 용도로 4개 이상의 vCPU가 포함된 계산 최적화 인스턴스 크기에서 지원됩니다. 하이퍼스레딩을 지원하는 D/DSv3 또는 E/ESv3과 같은 인스턴스에서 가속 네트워킹은 8개 이상의 vCPU가 포함된 VM 인스턴스에서 지원됩니다.  지원되는 계열은 D/DSv2, D/DSv3, E/ESv3, F/Fs/Fsv2 및 Ms/Mms입니다. 

VM 인스턴스에 대한 자세한 내용은 [Linux VM 크기](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

## <a name="regions"></a>영역
모든 공용 Azure 지역 및 Azure Government 클라우드에서 사용할 수 있습니다.

## <a name="limitations"></a>제한 사항
이 기능을 사용하는 경우 다음과 같은 제한이 적용됩니다.

* **네트워크 인터페이스 만들기:** 가속화된 네트워킹은 새 NIC에서만 사용할 수 있으며, 기존 NIC에서는 사용할 수 없습니다.
* **VM 만들기:** VM을 만들 때 가속화된 네트워킹을 사용하도록 설정된 NIC만 VM에 연결할 수 있습니다. 기존 VM에는 이 NIC를 연결할 수 없습니다. VM을 기존 가용성 집합에 추가하는 경우 가용성 집합의 모든 VM에서도 가속화된 네트워킹을 사용할 수 있어야 합니다.
* **Azure Resource Manager를 통해서만 배포:** 가속 네트워킹을 사용하여 가상 머신(클래식)을 배포할 수 없습니다.

이 문서에서는 Azure CLI를 통해 가속 네트워킹을 사용하는 가상 머신을 만드는 단계를 안내하지만, [Azure Portal을 통해 가속 네트워킹을 사용하는 가상 머신 만들기](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)도 가능합니다. 포털에서 가상 머신을 만드는 경우 **설정**의 **가속 네트워킹** 아래에서 **사용**을 선택합니다. [지원되는 운영 체제](#supported-operating-systems) 및 [VM 크기](#supported-vm-instances)를 선택하지 않으면 가속 네트워킹을 사용하는 옵션이 포털에 나타나지 않습니다. 가상 머신이 만들어진 후에는 [가속 네트워킹을 사용할 수 있는지 확인](#confirm-that-accelerated-networking-is-enabled)의 지침을 완료해야 합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인합니다. 다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *myNic*, *myVm*이 포함됩니다.

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *centralus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location centralus
```

[Linux 가속 네트워킹](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)에 나열된 지원되는 Linux 지역을 선택해야 합니다.

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 하나의 서브넷을 포함한 *myVnet*이라는 가상 네트워크를 만듭니다.

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기
[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *myNetworkSecurityGroup*이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

네트워크 보안 그룹에는 여러 가지 기본 규칙이 포함됩니다. 그 중 하나는 인터넷의 모든 인바운드 액세스를 사용하지 않도록 설정합니다. [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)를 사용하여 가상 머신에 대한 SSH 액세스를 허용하도록 포트를 엽니다.

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>가속 네트워킹을 사용하여 네트워크 인터페이스 만들기

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)를 사용하여 공용 IP 주소를 만듭니다. 인터넷에서 가상 머신에 액세스하려는 계획이 없다면 공용 IP 주소가 필요하지 않지만 이 문서의 단계를 완료하려면 필요합니다.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

가속 네트워킹을 사용하여 [az network nic create](/cli/azure/network/nic#az_network_nic_create)로 네트워크 인터페이스를 만듭니다. 다음 예제에서는 *myVnet* 가상 네트워크의 *mySubnet*이라는 서브넷에서 *myNic*이라는 네트워크 인터페이스를 만들고, *myNetworkSecurityGroup* 네트워크 보안 그룹을 네트워크 인터페이스에 연결합니다.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nic"></a>VM 만들기 및 NIC 연결
VM을 만들 때 `--nics`로 만든 NIC를 지정합니다. [Linux 가속 네트워킹](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)에 나열된 크기 및 배포를 선택해야 합니다. 

[az vm create](/cli/azure/vm#az_vm_create)로 VM을 만듭니다. 다음 예제에서는 가속 네트워킹(*Standard_DS4_v2*)을 지원하는 UbuntuLTS 이미지 및 크기를 사용하여 *myVM*이라는 VM을 만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

모든 VM 크기 및 특성 목록은 [Linux VM 크기](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

VM을 만들면 다음 예제 출력과 유사한 출력이 반환됩니다. **publicIpAddress**를 기록해 둡니다. 이 주소는 이후 단계에서 VM에 액세스하는 데 사용됩니다.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="confirm-that-accelerated-networking-is-enabled"></a>가속 네트워킹을 사용할 수 있는지 확인합니다.

다음 명령을 사용하여 VM으로 SSH 세션을 만듭니다. `<your-public-ip-address>`를 만든 가상 머신에 할당된 공용 IP 주소와 바꾸고, VM을 만들 때 `--admin-username`에 다른 값을 사용한 경우 *azureuser*를 바꿉니다.

```bash
ssh azureuser@<your-public-ip-address>
```

Bash 셸에서 `uname -r`을 입력하고, 커널 버전이 다음 버전 이상인지 확인합니다.

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


`lspci` 명령을 사용하여 Mellanox VF 장치를 VM에 노출했는지 확인합니다. 반환되는 출력은 다음 출력과 유사합니다.

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

`ethtool -S eth0 | grep vf_` 명령을 사용하여 VF(가상 함수)에서 작업을 확인합니다. 다음 샘플 출력과 비슷한 출력을 수신하면 가속 네트워킹을 사용하고 작동시킬 수 있습니다.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
이제 가속화된 네트워킹을 VM에 사용할 수 있습니다.
