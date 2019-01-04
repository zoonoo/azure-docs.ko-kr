---
title: Azure CLI 샘플 - 스토리지 계정에 관리 디스크 복사
description: Azure CLI 샘플 - 관리 디스크를 스토리지 계정에 내보내거나 복사합니다.
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 66dcb8d38f8c469656d36ff1da1ac03f900069b2
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583139"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Azure CLI를 사용하여 관리 디스크를 스토리지 계정에 내보내기/복사

이 스크립트는 관리 디스크의 기본 VHD를 같거나 다른 지역의 저장소 계정으로 내보냅니다. 먼저 관리 디스크의 SAS URI를 생성한 다음, 이를 사용하여 저장소 계정으로 VHD를 복사합니다. 이 스크립트를 사용하여 지역별 확장을 위해 관리 디스크를 복사합니다. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 관리 디스크의 SAS URI를 생성하고 SAS URI를 사용하여 저장소 계정에 기본 VHD를 복사합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az disk grant-access](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | 기본 VHD 파일을 저장소 계정으로 복사하거나 온-프레미스로 다운로드하는 데 사용되는 읽기 전용 SAS를 생성합니다.  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | 하나의 저장소 계정에서 다른 저장소 계정으로 Blob을 비동기적으로 복사 |

## <a name="next-steps"></a>다음 단계

[VHD에서 관리 디스크 만들기](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[관리 디스크에서 가상 머신 만들기](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 가상 머신 및 관리 디스크 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
