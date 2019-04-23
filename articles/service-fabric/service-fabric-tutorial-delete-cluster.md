---
title: Azure에서 Service Fabric 클러스터 삭제 | Microsoft Docs
description: 이 자습서에서는 Azure에 호스팅된 Service Fabric 클러스터 및 모든 해당 리소스를 삭제하는 방법을 알아봅니다. 클러스터를 포함하는 리소스 그룹을 삭제하거나 선택적으로 리소스를 삭제할 수 있습니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 0e5137a8183f378ee5960846e281222c6ecaaa47
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995713"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>자습서: Azure에서 실행되는 Service Fabric 클러스터를 제거

이 자습서는 시리즈의 5부로서 Azure에서 실행되는 Service Fabric 클러스터를 삭제하는 방법을 보여 줍니다. Service Fabric 클러스터를 완벽하게 삭제하려면 클러스터에서 사용한 리소스도 삭제해야 합니다. 이 작업을 수행하는 데 두 가지 옵션이 있습니다. 클러스터가 속한 리소스 그룹을 삭제(리소스 그룹의 다른 모든 리소스와 클러스터 리소스 삭제)하거나 특히 클러스터 리소스와 관련 리소스(리소스 그룹의 다른 리소스 제외)를 삭제합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 리소스 그룹 및 해당하는 모든 리소스 삭제
> * 리소스 그룹에서 리소스를 선택적으로 삭제

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 템플릿을 사용하여 Azure에서 보안 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 만들기
> * [클러스터 모니터링](service-fabric-tutorial-monitor-cluster.md)
> * [클러스터 규모 확장 또는 규모 감축](service-fabric-tutorial-scale-cluster.md)
> * [클러스터의 런타임 업그레이드](service-fabric-tutorial-upgrade-cluster.md)
> * 클러스터 삭제


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Azure Powershell](https://docs.microsoft.com/powershell/azure//install-Az-ps) 또는 [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.
* Azure에서 보안 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 만들기

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>또한 Service Fabric 클러스터를 포함하는 리소스 그룹 삭제
클러스터 및 클러스터에서 사용하는 모든 리소스를 삭제하는 가장 간단한 방법은 리소스 그룹을 삭제하는 것입니다.

Azure에 로그인하고, 클러스터를 제거하려는 구독 ID를 선택합니다.  [Azure Portal](https://portal.azure.com)에 로그인하여 구독 ID를 찾을 수 있습니다. [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet 또는 [az group delete](/cli/azure/group?view=azure-cli-latest) 명령을 사용하여 리소스 그룹 및 모든 클러스터 리소스를 삭제합니다.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>클러스터 리소스 및 관련 리소스를 선택적으로 삭제
리소스 그룹에 삭제하려는 서비스 패브릭 클러스터와 관련된 리소스만 있는 경우 리소스 그룹 전체를 삭제하기 훨씬 쉽습니다. 리소스 그룹에서 리소스를 선택적으로 삭제하고 리소스를 클러스터와 연결되지 않도록 유지하려는 경우 이러한 단계를 따릅니다.

리소스 그룹에 리소스를 나열합니다.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

삭제하려는 각 리소스에 대해 다음 스크립트를 실행합니다.

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

클러스터 리소스를 삭제하려면 다음 스크립트를 실행합니다.

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 리소스 그룹 및 해당하는 모든 리소스 삭제
> * 리소스 그룹에서 리소스를 선택적으로 삭제

이제 이 자습서를 완료했으니 다음을 시도하세요.
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)를 사용하여 Service Fabric 클러스터를 검사 및 관리하는 방법을 알아봅니다.
* 클러스터 노드의 [Windows 운영 체제를 패치](service-fabric-patch-orchestration-application.md)하는 방법을 알아보세요.
* [Windows 클러스터](service-fabric-diagnostics-event-aggregation-wad.md)에 대한 이벤트를 집계하거나 수집하고, [Log Analytics를 설정](service-fabric-diagnostics-oms-setup.md)하여 클러스터 이벤트를 모니터링하는 방법을 알아보세요.
