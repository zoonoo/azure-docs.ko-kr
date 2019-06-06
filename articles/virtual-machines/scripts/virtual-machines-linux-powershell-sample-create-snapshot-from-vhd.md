---
title: Azure PowerShell 스크립트 샘플 - VHD에서 스냅샷을 만들어 약간의 시간 동안 같은 관리 디스크 여러 개 만들기 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - VHD에서 스냅샷을 만들어 약간의 시간 동안 같은 관리 디스크 여러 개 만들기
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 694e4259c80cd4d5aec6c354dd028db942511e6b
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728141"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>PowerShell을 사용하여 VHD에서 스냅샷을 만들어 약간의 시간 동안 같은 관리 디스크 여러 개 만들기

이 스크립트는 같은 구독 또는 다른 구독의 저장소 계정에 VHD 파일의 스냅샷을 만듭니다. 이 스크립트를 사용하여 특수화된(일반화되지 않은/sysprep을 실행하지 않은) VHD를 스냅샷으로 가져온 다음, 스냅샷을 사용하여 약간의 시간 동안 같은 관리 디스크를 여러 개 만듭니다. 또한 이 스크립트를 사용하여 데이터 VHD를 스냅샷으로 가져온 다음, 스냅샷을 사용하여 약간의 시간 동안 관리 디스크를 여러 개 만듭니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 다른 구독의 VHD에서 관리 디스크를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | 디스크 만들기에 사용되는 디스크 구성을 만듭니다. 저장소 형식, 위치, 부모 VHD가 저장된 저장소 계정의 리소스 ID 및 부모 VHD의 VHD URI를 포함합니다. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | 매개 변수로 전달된 디스크 구성, 디스크 이름 및 리소스 그룹 이름을 사용하여 디스크를 만듭니다. |

## <a name="next-steps"></a>다음 단계

[스냅샷에서 관리 디스크 만들기](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Linux VM 설명서](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
