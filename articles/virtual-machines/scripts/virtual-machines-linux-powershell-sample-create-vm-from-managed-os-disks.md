---
title: Azure PowerShell 스크립트 샘플 - 관리 디스크를 OS 디스크로 연결하여 VM 만들기 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - 관리 디스크를 OS 디스크로 연결하여 VM 만들기
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: f0a1184daa41a30bb0c47764a3a47af68a6a9251
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58442796"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>PowerShell과 기존 관리 OS 디스크를 사용하여 가상 머신 만들기

이 스크립트는 기존 관리 디스크를 OS 디스크로 연결하여 가상 컴퓨터를 만듭니다. 이전 시나리오에서는 이 스크립트를 사용합니다.
* 다른 구독의 관리 디스크에서 복사된 기존의 관리 OS 디스크에서 VM 만들기
* 특수화된 VHD 파일에서 만든 기존 관리 디스크에서 VM 만들기 
* 스냅샷에서 만든 기존의 관리 OS 디스크에서 VM 만들기 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 관리 디스크 속성을 가져오고 관리 디스크를 새 VM에 연결하며 VM을 만듭니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/Get-AzDisk) | 디스크의 이름 및 리소스 그룹에 따라 디스크 개체를 가져옵니다. 반환된 디스크 개체의 Id 속성은 디스크를 새 VM에 연결하는 데 사용됩니다. |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | VM 구성을 만듭니다. 이 구성은 VM 이름, 운영 체제 및 관리자 자격 증명 등의 정보를 포함합니다. 이 구성은 VM을 만드는 중에 사용됩니다. |
| [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) | 디스크의 Id 속성을 사용하여 관리 디스크를 OS 디스크로 새 가상 컴퓨터에 연결합니다. |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | 공용 IP 주소를 만듭니다. |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | 네트워크 인터페이스를 만듭니다. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | 가상 머신을 만듭니다. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

Marketplace 이미지에는 [Set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan)을 사용하여 계획 정보를 설정합니다.

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Linux VM 설명서](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
