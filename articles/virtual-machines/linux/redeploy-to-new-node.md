---
title: "Azure에서 Linux 가상 컴퓨터 다시 배포 | Microsoft Docs"
description: "Azure에서 가상 컴퓨터를 다시 배포하여 SSH 연결 문제를 완화하는 방법"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: eeb1c6f95a254245d5598f56fb615e1c83de23d5
ms.lasthandoff: 04/03/2017


---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>새 Azure 노드로 Linux 가상 컴퓨터 다시 배포
Azure에서 SSH 문제 또는 Linux Azure VM(가상 컴퓨터)에 대한 응용 프로그램 액세스 문제를 해결하는 데 어려움이 있는 경우 VM을 다시 배포하는 것이 도움이 될 수 있습니다. VM을 다시 배포하면 VM이 Azure 인프라 내의 새 노드로 이동된 다음 전원이 켜지고, 모든 구성 옵션 및 관련 리소스는 그대로 유지됩니다. 이 문서에서는 Azure CLI 또는 Azure 포털을 사용하여 VM을 다시 배포하는 방법을 보여 줍니다.

> [!NOTE]
> VM을 다시 배포한 후에 임시 디스크가 손실되고 가상 네트워크 인터페이스와 연결된 동적 IP 주소가 업데이트됩니다. 

다음 옵션 중 하나를 사용하여 VM을 다시 배포할 수 있습니다. VM을 다시 배포하려면 하나의 옵션만을 선택해야 합니다.

- [Azure CLI 2.0](#azure-cli-20)
- [Azure CLI 1.0](#azure-cli-10)
- [Azure Portal](#using-azure-portal)

## <a name="azure-cli-20"></a>Azure CLI 2.0
최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/#login)을 사용하여 Azure 계정에 로그인합니다.

[az vm redeploy](/cli/azure/vm#redeploy)를 사용하여 VM을 다시 배포합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 다시 배포합니다.

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="azure-cli-10"></a>Azure CLI 1.0
[최신 Azure CLI 1.0](../../cli-install-nodejs.md)을 설치하고 Azure 계정에 로그인하고 리소스 관리자 모드에 있는지 확인합니다(`azure config mode arm`).

다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 다시 배포합니다.

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>다음 단계
VM에 연결하는 데 문제가 있는 경우 [SSH 연결 문제 해결](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 또는 [자세한 SSH 문제 해결 단계](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 특정 도움말을 찾을 수 있습니다. VM에서 실행 중인 응용 프로그램에 액세스할 수 없는 경우 [응용 프로그램 문제 해결](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 읽어볼 수도 있습니다.


