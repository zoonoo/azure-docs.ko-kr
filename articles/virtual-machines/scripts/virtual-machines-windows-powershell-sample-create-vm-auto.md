---
title: "Azure에서 New-AzVM을 사용하여 Windows VM 만들기 | Microsoft Docs"
description: "Azure PowerShell 스크립트 샘플 - New-AzVM cmdlet을 사용하여 Windows VM을 만듭니다."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/29/2017
ms.author: cynthn
ms.openlocfilehash: 36660ea1e8609ea8a03f4fa63e6f12f765bf9721
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-fully-configured-virtual-machine-with-the-new-azvm-powershell-cmdlet-preview"></a>New-AzVM PowerShell cmdlet으로 완벽히 구성된 가상 컴퓨터 만들기(미리 보기)

이 스크립트는 Cloud Shell에서 New-AzVM cmdlet을 사용하여 Windows Server 2016에서 실행 중인 Azure Virtual Machine을 만듭니다. 스크립트를 실행하면 RDP를 통해 가상 컴퓨터에 액세스할 수 있습니다. Cloud Shell에는 기본적으로 설치된 New-AzVM이 포함된 모듈이 있어 새 기능을 더 간편하게 확인할 수 있습니다.

이 샘플에서는 미리 보기 상태인 New-AzVM cmdlet을 사용합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

## <a name="sample-script"></a>샘플 스크립트


```azurepowershell-interactive
New-AzVM -Name myVM `
    -VirtualNetworkName myVNet `
    -Location westeurope `
    -SecurityGroupName myNSG `
    -PublicIpAddressName myPIP 
```


## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```


## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 컴퓨터 PowerShell 스크립트 샘플은 [Azure Windows VM 설명서](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
