---
title: "Azure CLI 스크립트 샘플 - OMS 모니터링을 사용하여 Linux VM 만들기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - OMS 모니터링을 사용하여 Linux VM 만들기"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 07d91dfa905d22334bd051f9d5f3d936d38efc88
ms.openlocfilehash: 6eae6db78e85bd4d02678c0875e9bf9163af5276
ms.lasthandoff: 02/28/2017

---

# <a name="monitor-a-vm-with-operations-management-suite"></a>Operations Management Suite를 사용하여 VM 모니터링

이 스크립트는 Azure Virtual Machine을 만들고 OMS(Operations Management Suite) 에이전트를 설치하고 OMS 작업 영역을 사용하여 시스템을 등록합니다. 스크립트를 실행하면 가상 컴퓨터가 OMS 콘솔에 표시됩니다.

이 스크립트를 실행하기 전에 `az login` 명령을 사용하여 Azure와의 연결이 생성되었는지 확인합니다. 또한 OMS 작업 영역 ID와 작업 영역 키를 스크립트에서 업데이트해야 합니다.

이 샘플은 Bash 셸에서 작동합니다. Windows 클라이언트에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 실행](../virtual-machines-windows-cli-options.md)을 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[기본](../../../cli_scripts/virtual-machine/create-vm-monitor-oms/create-vm-monitor-oms.sh "VM 빠른 생성")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 컴퓨터 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 가상 컴퓨터를 만들고 네트워크 카드, 가상 네트워크, 서브넷 및 NSG에 연결합니다. 또한 이 명령은 사용할 가상 컴퓨터 이미지와 관리 자격 증명을 지정합니다.  |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | 가상 컴퓨터에 대한 VM 확장을 실행합니다. 이 경우에 Operations Management Suite 에이전트 확장은 OMS 에이전트를 설치하고 OMS 작업 영역에서 VM을 등록하는 데 사용됩니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 가상 컴퓨터 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.

