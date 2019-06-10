---
title: Azure PowerShell 스크립트 샘플 - 스냅샷에서 관리 디스크 만들기 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - 스냅샷에서 관리 디스크 만들기
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
ms.openlocfilehash: 83f94a65838b562a978a547bc1195695d1c96b6b
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728153"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>PowerShell을 사용하여 스냅샷에서 관리 디스크 만들기

이 스크립트는 스냅샷에서 관리 디스크를 만듭니다. 이를 사용하여 OS 및 데이터 디스크의 스냅샷에서 가상 머신을 복원합니다. 각 스냅샷에서 OS 및 데이터 관리 디스크를 만든 다음, 관리 디스크를 연결하여 새 가상 머신을 만듭니다. 스냅샷에서 만든 데이터 디스크를 연결하여 기존 VM의 데이터 디스크를 복원할 수도 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 스냅샷에서 관리 디스크를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/Get-AzSnapshot) | 스냅샷 속성을 가져옵니다.  |
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | 디스크 만들기에 사용되는 디스크 구성을 만듭니다. 부모 스냅샷의 리소스 ID, 부모 스냅샷의 위치와 같은 위치 및 저장소 형식을 포함합니다.  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | 매개 변수로 전달된 디스크 구성, 디스크 이름 및 리소스 그룹 이름을 사용하여 디스크를 만듭니다. |

## <a name="next-steps"></a>다음 단계


[관리 디스크에서 가상 머신 만들기](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Linux VM 설명서](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
