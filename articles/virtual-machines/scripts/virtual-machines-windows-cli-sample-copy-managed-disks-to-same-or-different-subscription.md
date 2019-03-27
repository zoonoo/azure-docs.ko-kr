---
title: 'Azure CLI 스크립트 샘플 - 관리 디스크를 동일한 구독이나 다른 구독으로 복사(이동) | Microsoft Docs '
description: Azure CLI 스크립트 샘플 - 관리 디스크를 동일한 구독이나 다른 구독으로 복사(이동)
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: abb051e9646d547907384ed06413439845a29d5e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249675"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>CLI를 사용하여 관리 디스크를 동일한 구독이나 다른 구독으로 복사

이 스크립트는 관리 디스크를 동일한 구독이나 같은 지역에 있지만 다른 구독으로 복사합니다. 이러한 복사는 구독이 동일한 AAD 테넌트의 일부일 때만 수행됩니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 원본 관리 디스크의 ID를 사용하여 대상 구독에 새 관리 디스크를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | 관리 디스크의 이름 및 리소스 그룹 속성을 사용하여 관리 디스크의 모든 속성을 가져옵니다. Id 속성은 관리 디스크를 다른 구독으로 복사하는 데 사용됩니다.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | 부모 관리 디스크의 Id 및 이름을 사용하여 다른 구독에서 새 관리 디스크를 만들어 관리 디스크를 복사합니다.  |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 가상 머신 및 관리 디스크 CLI 스크립트 샘플은 [Azure Windows VM 설명서](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
