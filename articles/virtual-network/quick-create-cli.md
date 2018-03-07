---
title: "가상 네트워크 만들기 - Azure CLI | Microsoft Docs"
description: "Azure CLI를 사용하여 가상 네트워크를 만드는 방법을 빠르게 알아봅니다. 가상 네트워크를 사용하면 다양한 유형의 Azure 리소스가 서로 개인적으로 통신할 수 있습니다."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 792b92731f89f3d0bab4f23221223e469ddf9550
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Azure CLI를 사용하여 가상 네트워크 만들기

이 문서에서는 가상 네트워크를 만드는 방법을 설명합니다. 가상 네트워크를 만든 후, 가상 네트워크에 두 개의 가상 머신을 배포하고 둘 간의 개인 네트워크 통신을 테스트합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. 모든 Azure 리소스는 Azure 위치(또는 지역) 내에서 만들어집니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 명령을 사용하여 가상 네트워크를 만듭니다. 다음 예제에서는 *default*라는 하나의 서브넷이 있는 *myVirtualNetwork*라는 기본 가상 네트워크를 만듭니다. 위치가 지정되지 않으므로 Azure는 리소스 그룹이 있는 동일한 위치에 가상 네트워크를 만듭니다.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

가상 네트워크를 만든 후 반환되는 정보 중 일부는 다음과 같습니다.

```azurecli
"newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/16"
```

모든 가상 네트워크에는 하나 이상의 주소 접두사가 할당됩니다. 가상 네트워크를 만들 때는 주소 접두사가 지정되지 않으므로 Azure는 기본적으로 10.0.0.0/16 주소 공간을 정의합니다. 주소 공간은 CIDR 표기법으로 지정됩니다. 주소 공간 10.0.0.0/16은 10.0.0.0-10.0.255.254를 포함합니다.

반환되는 정보의 또 다른 부분은 명령에 지정된 *default* 서브넷에 대한 **addressPrefix** *10.0.0.0/24*입니다. 가상 네트워크는 0개 이상의 서브넷을 포함합니다. 이 명령은 *default*라는 단일 서브넷을 만들었지만 이 서브넷에 대해서는 주소 접두사가 지정되지 않았습니다. 가상 네트워크 또는 서브넷에 대해 주소 접두사를 지정하지 않은 경우 Azure는 기본적으로 첫 번째 서브넷에 대한 주소 접두사로 10.0.0.0/24를 정의합니다. 그 결과, 서브넷은 10.0.0.0-10.0.0.254를 포함하지만, Azure가 각 서브넷에서 처음 4개 주소(0-3)와 마지막 주소를 예약하기 때문에 10.0.0.4-10.0.0.254만 사용할 수 있습니다.

## <a name="test-network-communication"></a>네트워크 통신 테스트

가상 네트워크를 사용하면 여러 유형의 Azure 리소스가 서로 개인적으로 통신할 수 있습니다. 가상 네트워크에 배포할 수 있는 한 가지 유형의 리소스는 가상 머신입니다. 가상 네트워크에서 두 가상 머신을 만들어 이후 단계에서 둘 간의 개인 통신 유효성을 검사할 수 있도록 합니다.

### <a name="create-virtual-machines"></a>가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create) 명령을 사용하여 가상 머신을 만듭니다. 다음 예제에서는 *myVm1*이라는 가상 머신을 만듭니다. 또한 기본 키 위치에 SSH 키가 없는 경우 해당 명령이 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다. `--no-wait` 옵션은 백그라운드에서 가상 머신을 만들기 때문에 다음 단계를 계속 진행할 수 있습니다.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

Azure에서는 *myVirtualNetwork* 가상 네트워크의 *default* 서브넷에 가상 머신을 자동으로 만듭니다. 이 가상 네트워크는 리소스 그룹에 존재하며, 명령에 가상 네트워크나 서브넷을 지정하지 않았기 때문입니다. Azure DHCP는 10.0.0.4가 *default* 서브넷에서 사용 가능한 첫 번째 주소이므로 이 주소를 가상 머신 생성 중에 자동으로 할당했습니다. 가상 머신이 생성되는 위치는 가상 네트워크가 있는 동일한 위치여야 합니다. 이 문서에는 그렇게 설명되어 있지만 가상 머신을 가상 네트워크와 동일한 리소스 그룹에 배치할 필요는 없습니다.

두 번째 가상 머신을 만듭니다. 기본적으로 Azure는 이 가상 머신을 *default* 서브넷에 만듭니다.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

가상 머신을 만드는 데 몇 분 정도 걸립니다. 가상 머신을 만든 후 Azure CLI는 다음 예제와 비슷한 출력을 반환합니다. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

이 예제에서는 **privateIpAddress**가 *10.0.0.5*로 표시됩니다. Azure DHCP는 *10.0.0.5*가 *default* 서브넷에서 사용 가능한 다음 주소이므로 이 주소를 가상 머신에 자동으로 할당했습니다. **publicIpAddress**를 기록해 둡니다. 이 주소는 이후 단계에서 인터넷을 통해 가상 컴퓨터에 액세스하는 데 사용됩니다. 공용 IP 주소는 가상 네트워크 또는 서브넷 주소 접두사 내에서 할당되지 않습니다. 공용 IP 주소는 [각 Azure 지역에 할당된 주소 풀](https://www.microsoft.com/download/details.aspx?id=41653)에서 할당됩니다. Azure는 가상 머신에 할당된 공용 IP 주소를 알고 있지만, 가상 머신에서 실행되는 운영 체제는 할당된 공용 IP 주소를 인식하지 못합니다.

### <a name="connect-to-a-virtual-machine"></a>가상 머신에 연결

다음 명령을 사용하여 *myVm2* 가상 머신과의 SSH 세션을 만듭니다. `<publicIpAddress>`를 가상 머신의 공용 IP 주소로 바꿉니다. 위의 예제에서 IP 주소는 *40.68.254.142*입니다.

```bash 
ssh <publicIpAddress>
```

### <a name="validate-communication"></a>통신 유효성 검사

*myVm2*에서 *myVm1*과의 통신을 확인하려면 다음 명령을 사용합니다.

```bash
ping myVm1 -c 4
```

*10.0.0.4*에서 4개의 응답을 받게 됩니다. 두 가상 머신은 *default* 서브넷에서 할당된 개인 IP 주소가 있으므로 *myVm2*에서 *myVm1*으로 통신할 수 있습니다. Azure는 가상 네트워크 내의 모든 호스트에 대해 DNS 이름 확인을 제공하므로 호스트 이름으로 Ping할 수 있습니다.

인터넷으로의 아웃바운드 통신을 확인하려면 다음 명령을 사용합니다.

```bash
ping bing.com -c 4
```

bing.com에서 4개의 응답을 받게 됩니다. 기본적으로 가상 네트워크의 모든 가상 머신은 인터넷으로 아웃바운드 통신을 할 수 있습니다.

VM에 대한 SSH 세션을 종료합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 1개의 서브넷이 있는 기본 가상 네트워크를 배포했습니다. 다중 서브넷으로 사용자 지정 가상 네트워크를 만드는 방법을 알아보려면 사용자 지정 가상 네트워크를 만들기 위한 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [사용자 지정 가상 네트워크 만들기](virtual-networks-create-vnet-arm-pportal.md#azure-cli)
