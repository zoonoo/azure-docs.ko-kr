---
title: "Azure PowerShell 스크립트 샘플 - 스냅숏에서 관리 디스크 만들기 | Microsoft Docs"
description: "Azure PowerShell 스크립트 샘플 - 스냅숏에서 관리 디스크 만들기"
services: managed-disks-windows
documentationcenter: storage
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: managed-disks-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: ab5f047923271b4d2f79e7eb35067295fff18cda
ms.contentlocale: ko-kr
ms.lasthandoff: 06/08/2017

---

# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>PowerShell을 사용하여 스냅숏에서 관리 디스크 만들기

이 스크립트는 스냅숏에서 관리 디스크를 만듭니다. 이 스크립트를 사용하여 OS 및 데이터 디스크의 스냅숏에서 가상 컴퓨터를 복원합니다. 각 스냅숏에서 OS 및 데이터 관리 디스크를 만든 다음 관리 디스크를 연결하여 새 가상 컴퓨터를 만듭니다. 스냅숏에서 만든 데이터 디스크를 연결하여 기존 VM의 데이터 디스크를 복원할 수도 있습니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/storage/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "스냅숏에서 관리 디스크 만들기")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 스냅숏에서 관리 디스크를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [Get-AzureRmSnapshot](/powershell/module/azurerm.compute/Get-AzureRmSnapshot) | 스냅숏 속성을 가져옵니다.  |
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | 디스크 만들기에 사용되는 디스크 구성을 만듭니다. 부모 스냅숏의 리소스 ID, 부모 스냅숏의 위치와 같은 위치 및 저장소 형식을 포함합니다.  |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | 매개 변수로 전달된 디스크 구성, 디스크 이름 및 리소스 그룹 이름을 사용하여 디스크를 만듭니다. |


## <a name="next-steps"></a>다음 단계

[관리 디스크에서 가상 컴퓨터 만들기](./../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 컴퓨터 PowerShell 스크립트 샘플은 [Azure Windows VM 설명서](../../virtual-machines/windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
