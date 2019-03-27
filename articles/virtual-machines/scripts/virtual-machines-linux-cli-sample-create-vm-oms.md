---
title: Azure CLI 스크립트 샘플 - Azure Monitor를 사용하여 Linux VM 만들기 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - Azure Monitor를 사용하여 Linux VM 만들기
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6aed5f40d689fc84c82f37760e78fc7c3fe94dd1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535624"
---
# <a name="monitor-a-vm-with-azure-monitor"></a>Azure Monitor를 사용하여 VM 모니터링

이 스크립트는 Azure Virtual Machine을 만들고 Log Analytics 에이전트를 설치하고 Log Analytics 작업 영역을 사용하여 시스템을 등록합니다. 스크립트를 실행하면 가상 머신이 콘솔에 표시됩니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-monitor-oms/create-vm-monitor-oms.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 컴퓨터 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | 가상 머신을 만들고 네트워크 카드, 가상 네트워크, 서브넷 및 NSG에 연결합니다. 또한 이 명령은 사용할 가상 머신 이미지와 관리 자격 증명을 지정합니다.  |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension) | 가상 머신에 대한 VM 확장을 실행합니다. 이 경우에 Azure Monitor 에이전트 확장은 Log Analytics 에이전트를 설치하고 Log Analytics 작업 영역에서 VM을 등록하는 데 사용됩니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 가상 머신 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
