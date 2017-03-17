---
title: "Azure CLI 스크립트 샘플 - VM 다시 시작 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 태그 및 ID로 VM 다시 시작"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: b292a02770fa74812e74fa38f870e47ed7473b63
ms.lasthandoff: 03/04/2017

---

# <a name="restart-vms-by-tag"></a>태그로 VM 다시 시작

이 예제에서는 여러 리소스 그룹의 지정된 태그로 가상 컴퓨터를 만듭니다.
가상 컴퓨터는 `--no-wait`를 사용하여 병렬로 만들어진 다음 전체적으로 완료될 때까지 대기합니다.

만들어진 가상 컴퓨터는 두 가지 다른 쿼리 메커니즘을 사용하여 다시 시작됩니다.

첫 번째는 비동기 생성을 대기하는 데 사용한 것과 동일한 쿼리로 VM을 다시 시작합니다.
```bash
az vm restart --ids $(az vm list --query "join(' ', ${GROUP_QUERY}] | [].id)" \
    -o tsv) $1>/dev/null
```

두 번째는 일반 리소스 목록 및 쿼리를 사용하여 태그로 ID를 가져옵니다.
```bash
az vm restart --ids $(az resource list --tag ${TAG} \
    --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv) $1>/dev/null
```

이 샘플은 Bash 셸에서 작동합니다. Windows 클라이언트에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 실행](../virtual-machines-windows-cli-options.md)을 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart-by-tag.sh "태그로 VM 다시 시작")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete -n GROUP1 --no-wait --yes && \ 
az group delete -n GROUP2 --no-wait --yes && \
az group delete -n GROUP3 --no-wait --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 컴퓨터, 가용성 집합, 부하 분산 장치 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | 가상 컴퓨터를 만듭니다.  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#list) | VM을 다시 시작하기 전에 VM이 프로비전되도록 하기 위해 `--query`와 함께 사용됩니다. |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#list) | VM을 다시 시작합니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/overview)를 참조하세요.

추가 가상 컴퓨터 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.

