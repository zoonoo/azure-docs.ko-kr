---
title: 갤러리 이미지를 Azure의 테 넌 트에서 공유 | Microsoft Docs
description: 공유 이미지 갤러리를 사용 하 여 Azure 테 넌 트에서 VM 이미지를 공유 하는 방법에 알아봅니다.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: c26abe948fa415c780d543c615c34af2091cfbc7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709171"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Azure 테 넌 트에서 VM 이미지 갤러리 공유

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> 다른 azure 테 넌 트의 이미지에서 VM을 배포 하려면 포털을 사용할 수 없습니다. 테 넌 트 간에 공유 되는 이미지에서 VM을 만들려면 사용 해야 합니다 [Azure CLI](../linux/share-images-across-tenants.md) 또는 Powershell입니다.

## <a name="create-a-vm-using-powershell"></a>PowerShell을 사용 하 여 VM 만들기


응용 프로그램 ID를 사용 하 여 두 테 넌 트에 로그인 암호 및 테 넌 트 id입니다. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

앱 등록에 대 한 사용 권한이 있는 리소스 그룹에 VM을 만듭니다. 이 예제 정보를 사용자 고유의로 바꿉니다.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $image `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

## <a name="next-steps"></a>다음 단계

만들 수도 있습니다 공유 이미지 갤러리 리소스를 사용 하 여 [Azure portal](shared-images-portal.md)합니다.