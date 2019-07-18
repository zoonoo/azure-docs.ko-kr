---
title: Azure CLI 샘플 - 스냅샷을 다른 지역의 스토리지 계정에 복사 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 스냅샷을 VHD로 동일한 지역 또는 다른 지역의 스토리지 계정으로 내보내기/복사합니다.
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
ms.custom: mvc,seodec18
ms.openlocfilehash: 9a1e0058e440f9cea60361a8b6b64dd4c7ab789b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249705"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>CLI를 사용하여 스냅샷을 다른 지역의 스토리지 계정으로 내보내기/복사

이 스크립트는 다른 지역의 저장소 계정으로 관리 스냅샷을 내보냅니다. 먼저 스냅샷의 SAS URI를 생성한 다음, 이를 사용하여 다른 지역의 저장소 계정으로 스냅샷을 복사합니다. 이 스크립트를 사용하여 재해 복구를 위해 다른 지역에서 관리 디스크의 백업을 유지 관리합니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 관리 스냅샷의 SAS URI를 생성하고 SAS URI를 사용하여 저장소 계정에 스냅샷을 복사합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot) | 기본 VHD 파일을 저장소 계정으로 복사하거나 온-프레미스로 다운로드하는 데 사용되는 읽기 전용 SAS를 생성합니다.  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | 하나의 저장소 계정에서 다른 저장소 계정으로 Blob을 비동기적으로 복사 |

## <a name="next-steps"></a>다음 단계

[VHD에서 관리 디스크 만들기](virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 가상 머신 및 관리 디스크 CLI 스크립트 샘플은 [Azure Windows VM 설명서](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
