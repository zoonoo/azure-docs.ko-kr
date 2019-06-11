---
title: Azure CLI 스크립트 샘플 - 관리 디스크를 OS 디스크로 연결하여 VM 만들기 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 관리 디스크를 OS 디스크로 연결하여 VM 만들기
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 3ec3d152864ab8caf9f0c68966b6d4f8fd9e64dd
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695239"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>CLI와 기존 관리 OS 디스크를 사용하여 가상 머신 만들기

이 스크립트는 기존 관리 디스크를 OS 디스크로 연결하여 가상 머신을 만듭니다. 이전 시나리오에서는 이 스크립트를 사용합니다.
* 다른 구독의 관리 디스크에서 복사된 기존의 관리 OS 디스크에서 VM 만들기
* 특수화된 VHD 파일에서 만든 기존 관리 디스크에서 VM 만들기 
* 스냅샷에서 만든 기존의 관리 OS 디스크에서 VM 만들기 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Create VM from a managed disk")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 관리 디스크 속성을 가져오고 관리 디스크를 새 VM에 연결하며 VM을 만듭니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | 디스크 이름 및 리소스 그룹 이름을 사용하여 관리 디스크 속성을 가져옵니다. Id 속성은 새 VM에 관리 디스크를 연결하는 데 사용됩니다. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | 관리 OS 디스크를 사용하여 VM을 만듭니다. |
## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 가상 머신 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
