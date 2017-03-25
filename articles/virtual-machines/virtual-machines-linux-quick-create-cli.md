---
title: "Azure 빠른 시작 - VM 만들기 CLI | Microsoft Docs"
description: "Azure CLI를 사용하여 가상 컴퓨터를 만드는 방법을 빠르게 이해합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 3ad45837a8c3f0e37e8571fb9b894df53ecb5f56
ms.lasthandoff: 03/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 Linux 가상 컴퓨터 만들기

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI 2.0이 사용됩니다. 이 가이드에서는 Azure CLI를 사용하여 Ubuntu 14.04 LTS가 실행되는 가상 컴퓨터를 배포하는 방법을 자세히 설명합니다.

시작하기 전에 Azure CLI가 설치되었는지 확인합니다. 자세한 내용은 [Azure CLI 설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="log-in-to-azure"></a>Azure에 로그인 

[az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제는 `westeurope` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

[az vm create](/cli/azure/vm#create) 명령을 사용하여 VM을 만듭니다. 

다음 예제에서는 `myVM`이라는 VM을 만들고 기본 키 위치에 SSH 키가 없는 경우 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

VM을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. 공용 IP 주소를 기록해 둡니다. 이 주소는 VM에 액세스하는 데 사용됩니다.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>가상 컴퓨터에 연결

다음 명령을 사용하여 가상 컴퓨터와의 SSH 세션을 만듭니다. 해당 IP 주소를 가상 컴퓨터의 공용 IP 주소로 바꿉니다.

```bash 
ssh <Public IP Address>
```

## <a name="delete-virtual-machine"></a>가상 컴퓨터 삭제

더 이상 필요하지 않은 경우 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

[고가용성 가상 컴퓨터 만들기 자습서](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[VM 배포 CLI 샘플 탐색](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
