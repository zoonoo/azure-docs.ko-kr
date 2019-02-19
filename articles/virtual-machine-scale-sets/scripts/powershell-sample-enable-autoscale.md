---
title: Azure PowerShell 샘플 - 호스트 기반 자동 크기 조정 사용 | Microsoft Docs
description: Azure PowerShell 샘플
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: bf424cabdfee4e325078594b8b0cc09fe26e9625
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980638"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-powershell"></a>PowerShell을 사용하여 가상 머신 확장 집합의 크기를 자동으로 조정
이 스크립트는 Windows Server 2016을 실행하는 가상 머신 확장 집합을 만들고, CPU 로드 변경에 따라 호스트 기반 메트릭을 사용하여 자동으로 크기 조정합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>샘플 스크립트


[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.ps1 "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>배포 정리
다음 명령을 실행하여 리소스 그룹, 확장 집합 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명
이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | 가상 네트워크, 부하 분산 장치 및 NAT 규칙을 포함하여 가상 머신 확장 집합 및 모든 지원 리소스를 만듭니다. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | 가상 머신 확장 집합에 대한 정보를 가져옵니다. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | 사용자 지정 스크립트용 VM 확장을 추가하여 기본 웹 애플리케이션을 설치합니다. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | VM 확장을 적용하기 위해 가상 머신 확장 집합 모델을 업데이트합니다. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | 부하 분산 장치에서 사용하는 할당된 공용 IP 주소에 대한 정보를 가져옵니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계
Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 머신 확장 집합 PowerShell 스크립트 샘플은 [Azure 가상 머신 확장 집합 설명서](../powershell-samples.md)에서 찾을 수 있습니다.
