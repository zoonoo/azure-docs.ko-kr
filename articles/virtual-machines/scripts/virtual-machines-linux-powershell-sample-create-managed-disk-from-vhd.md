---
title: Azure PowerShell 스크립트 샘플 - 동일한 또는 다른 구독에 있는 저장소 계정의 VHD 파일에서 관리 디스크 만들기 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - 동일한 또는 다른 구독에 있는 저장소 계정의 VHD 파일에서 관리 디스크 만들기
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
ms.openlocfilehash: 209ba9202845232aba1d878452899eaf219f2bde
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730114"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>PowerShell을 사용하여 동일한 또는 다른 구독에 있는 저장소 계정의 VHD 파일에서 관리 디스크 만들기

이 스크립트는 같은 구독 또는 다른 구독의 저장소 계정에 VHD 파일의 관리 디스크를 만듭니다. 이 스크립트를 사용하여 관리 OS 디스크에 특수화된(일반화/sysprep되지 않음) VHD를 가져와 가상 머신을 만듭니다 또한 데이터 VHD를 관리되는 데이터 디스크로 가져오는 데 사용합니다.

VHD 파일에서 단시간에 동일한 관리 디스크를 여러 개 만들지 마세요. Vhd 파일에서 관리 디스크를 만들기 위해 vhd 파일에서 Blob 스냅샷이 만들어진 후 관리 디스크를 만드는 데 사용됩니다. 1분에 하나의 Blob 스냅샷만 만들 수 있으므로 제한으로 인해 디스크 생성 오류가 발생합니다. 이 제한을 피하려면 [vhd 파일에서 관리 스냅샷](virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 만든 후 관리 스냅샷을 사용하여 단시간에 여러 관리 디스크를 만듭니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 다른 구독의 VHD에서 관리 디스크를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | 디스크 만들기에 사용되는 디스크 구성을 만듭니다. 저장소 형식, 위치, 부모 VHD가 저장된 저장소 계정의 리소스 ID, 부모 VHD의 VHD URI를 포함합니다. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | 매개 변수로 전달된 디스크 구성, 디스크 이름 및 리소스 그룹 이름을 사용하여 디스크를 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Linux VM 설명서](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
