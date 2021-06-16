---
title: Azure PowerShell을 사용하여 Azure DDoS Protection 계획 만들기 및 구성
description: Azure PowerShell을 사용하여 DDoS Protection 계획을 만드는 방법을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f48848edc6e42ccf39f608b103deb4311810d177
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110702658"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure DDoS Protection Standard 만들기 및 구성

Azure PowerShell을 사용하여 Azure DDoS Protection Standard를 시작합니다. 

DDoS 보호 계획은 구독 전반에 걸쳐 DDoS 보호 표준을 사용하도록 설정된 일단의 가상 네트워크를 정의합니다. 조직에 대해 하나의 DDoS 보호 계획을 구성하고 여러 구독의 가상 네트워크를 동일한 계획에 연결할 수 있습니다. 

이 빠른 시작에서는 DDoS 보호 계획을 만들고 가상 네트워크에 연결합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>DDoS 보호 계획 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 이 예에서는 리소스 그룹 이름을 _MyResourceGroup_ 으로 지정하고 _미국 동부_ 위치를 사용합니다.

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

이제 _MyDdosProtectionPlan_ 이라는 DDoS 보호 계획을 만듭니다.

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>가상 네트워크에 DDoS 사용

### <a name="enable-ddos-for-a-new-virtual-network"></a>새 가상 네트워크에 대한 DDoS 사용

가상 네트워크를 만들 때 DDoS 보호를 사용하도록 설정할 수 있습니다. 이 예에서는 가상 네트워크 이름을 _MyVnet_ 으로 지정합니다. 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>기존 가상 네트워크에 DDoS 사용

DDoS 보호 계획을 만들 때 기존 가상 네트워크를 연결할 수 있습니다.

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>유효성 검사 및 테스트

먼저 DDoS 보호 계획의 세부 정보를 확인합니다.

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

명령이 DDoS 보호 계획의 올바른 세부 정보를 반환하는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 위해 리소스를 보관할 수 있습니다. 더는 필요 없으면 _MyResourceGroup_ 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 DDoS 보호 계획과 모든 관련 리소스도 함께 삭제됩니다. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

가상 네트워크에 DDoS 보호를 사용하지 않으려면 다음을 수행합니다. 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

DDoS 보호 계획을 삭제하려면 먼저 모든 가상 네트워크를 해당 계획에서 분리해야 합니다.

## <a name="next-steps"></a>다음 단계

DDoS 보호 계획에 대한 원격 분석을 보고 구성하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [DDoS 보호 원격 분석 보기 및 구성](telemetry.md)
