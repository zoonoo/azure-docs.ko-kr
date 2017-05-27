---
title: "Azure CLI 스크립트 샘플 - 스냅숏에서 VM 만들기 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 스냅숏에서 VM 만들기"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: b5e33d7dcee5b5241de796d632a9b6069b991f7b
ms.contentlocale: ko-kr
ms.lasthandoff: 05/16/2017

---

# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>CLI를 사용하여 스냅숏에서 가상 컴퓨터 만들기

이 스크립트는 OS 디스크의 스냅숏에서 가상 컴퓨터를 만듭니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[기본](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "스냅숏에서 VM 만들기")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 관리 디스크, 가상 컴퓨터 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#show) | 스냅숏 이름 및 리소스 그룹 이름을 사용하여 스냅숏을 가져옵니다. 반환된 개체의 Id 속성은 관리 디스크를 만드는 데 사용됩니다.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | 스냅숏 Id, 디스크 이름, 저장소 유형 및 크기를 사용하여 스냅숏에서 관리 디스크를 만듭니다.  |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 관리 OS 디스크를 사용하여 VM을 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 가상 컴퓨터 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.

