---
title: "Linux 가상 컴퓨터 다시 배포 | Microsoft Docs"
description: "가상 컴퓨터를 다시 배포하여 SSH 연결 문제를 완화하는 방법을 설명합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 1cd81944ff1e8b6048315946220adee4bf68e576


---
# <a name="redeploy-virtual-machine-to-new-azure-node"></a>새 Azure 노드로 가상 컴퓨터 다시 배포
SSH 문제 또는 Azure VM(가상 컴퓨터)에 대한 응용 프로그램 액세스 문제를 해결하는 데 어려움이 있는 경우 VM을 다시 배포하는 것이 도움이 될 수 있습니다. VM을 다시 배포하면 VM이 Azure 인프라 내의 새 노드로 이동된 다음 전원이 켜지고, 모든 구성 옵션 및 관련 리소스는 그대로 유지됩니다. 이 문서에서는 Azure CLI 또는 Azure 포털을 사용하여 VM을 다시 배포하는 방법을 보여 줍니다.

> [!NOTE]
> VM을 다시 배포한 후에 임시 디스크가 손실되고 가상 네트워크 인터페이스와 연결된 동적 IP 주소가 업데이트됩니다. 
> 
> 

## <a name="using-azure-cli"></a>Azure CLI 사용
컴퓨터에 [최신 Azure CLI가 설치](../xplat-cli-install.md)되어 있는지 여부와 Resource Manager 모드인지(`azure config mode arm`)를 확인합니다.

가상 컴퓨터를 다시 배포하려면 다음 Azure CLI 명령을 사용합니다.

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

다시 배포 프로세스를 거치는 동안 VM의 상태가 달라지는 것을 볼 수 있습니다. VM은 새 호스트로 다시 배포되는 과정을 겪으면서 해당 `PowerState` 상태가 '실행 중'에서 '업데이트 중'으로 바뀐 다음 마지막으로 '실행 중'으로 바뀝니다. 다음 명령을 사용하여 리소스 그룹 내의 VM 상태를 확인합니다.

```bash
azure vm list -g <resourcegroup>
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>다음 단계
VM에 연결하는 데 문제가 있는 경우 [SSH 연결 문제 해결](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 또는 [자세한 SSH 문제 해결 단계](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 특정 도움말을 찾을 수 있습니다. VM에서 실행 중인 응용 프로그램에 액세스할 수 없는 경우 [응용 프로그램 문제 해결](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 읽어볼 수도 있습니다.




<!--HONumber=Nov16_HO3-->


