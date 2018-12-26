---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1fae3c3889242dfbf8f270d3762ea7434ceda6da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004171"
---
VNet이 없으면 PowerShell을 사용하여 신속하게 만들 수 있습니다. Azure Portal을 사용하여 가상 네트워크를 만들 수도 있습니다.

* 사용자가 만든 VNet에 대한 주소 공간이 연결하려는 다른 VNet의 주소 범위 또는 온-프레미스 네트워크 주소 공간과 겹치지 않도록 해야 합니다. 
* VNet이 이미 있는 경우에는 필요한 조건을 충족하는지 가상 네트워크 게이트웨이가 없는지 확인합니다.

이 문서에서 "사용해 보세요."를 클릭하고 PowerShell 콘솔을 열어서 VNet을 손쉽게 만들 수 있습니다. 값을 조정한 다음, 명령을 복사하여 콘솔 창에 붙여 넣습니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

PowerShell 명령을 조정한 다음, 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>VNet 만들기

PowerShell 명령을 조정하여 사용자 환경에 호환되는 VNet을 만듭니다.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```