---
title: Azure PowerShell 샘플 - 기본 가상 머신 확장 집합 만들기
description: 이 스크립트는 RDP를 통해 액세스할 수 있는 Windows Server 2016을 실행하는 Azure 가상 머신 확장 집합을 만듭니다.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: sample
ms.date: 03/27/2018
ms.author: jushiman
ms.custom: mvc
ms.openlocfilehash: 380b3d25bdcd5df5f095b2d87e832bf73422759e
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011486"
---
# <a name="create-a-basic-virtual-machine-scale-set-with-powershell"></a>PowerShell을 사용하여 가상 머신 확장 집합 만들기
이 스크립트는 Windows Server 2016을 실행하는 가상 머신 확장 집합을 만듭니다. 스크립트를 실행한 후에는 RDP를 통해 VM 인스턴스에 액세스할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>샘플 스크립트


[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.ps1 "Create a simple virtual machine scale set")]

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
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계
Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 머신 확장 집합 PowerShell 스크립트 샘플은 [Azure 가상 머신 확장 집합 설명서](../powershell-samples.md)에서 찾을 수 있습니다.
