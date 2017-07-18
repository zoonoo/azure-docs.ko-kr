---
title: "Azure CLI 스크립트 샘플 - 관리 디스크를 동일한 구독이나 다른 구독으로 복사(이동) | Microsoft Docs "
description: "Azure CLI 스크립트 샘플 - 관리 디스크를 동일한 구독이나 다른 구독으로 복사(이동)"
services: managed-disks-linux
documentationcenter: storage
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: managed-disks-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 90228d66150cf961557b6dbeed11308932677d83
ms.contentlocale: ko-kr
ms.lasthandoff: 05/23/2017

---

# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>CLI를 사용하여 관리 디스크를 동일한 구독이나 다른 구독으로 복사

이 스크립트는 관리 디스크를 동일한 구독이나 같은 지역에 있지만 다른 구독으로 복사합니다. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/storage/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "관리 디스크 복사")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 원본 관리 디스크의 ID를 사용하여 대상 구독에 새 관리 디스크를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#show) | 관리 디스크의 이름 및 리소스 그룹 속성을 사용하여 관리 디스크의 모든 속성을 가져옵니다. Id 속성은 관리 디스크를 다른 구독으로 복사하는 데 사용됩니다.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | 부모 관리 디스크의 Id 및 이름을 사용하여 다른 구독에서 새 관리 디스크를 만들어 관리 디스크를 복사합니다.  |

## <a name="next-steps"></a>다음 단계

[관리 디스크에서 가상 컴퓨터 만들기](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 가상 컴퓨터 및 관리 디스크 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.

