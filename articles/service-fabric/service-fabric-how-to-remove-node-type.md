---
title: Azure Service Fabric에서 노드 유형을 제거하는 방법 | Microsoft Docs
description: Azure Service Fabric에서 노드 유형을 제거하는 방법 알아보기
services: service-fabric
documentationcenter: .net
author: v-steg
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/26/2018
ms.author: v-steg
ms.openlocfilehash: 3704a356763b16a30285baee1aabffdd3aa3f8aa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53981889"
---
# <a name="remove-a-service-fabric-node-type"></a>Service Fabric 노드 유형 제거

[Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype)을 사용하여 Service Fabric 노드 유형을 제거합니다.

Remove-AzureRmServiceFabricNodeType이 호출되면 발생하는 두 가지 작업은 다음과 같습니다.
1.  노드 유형 뒤 VMSS(Virtual Machine Scale Set)가 삭제됩니다.
2.  이 노드 유형에 포함된 모든 노드는, 해당 노드에 대한 전체 상태가 시스템에서 제거됩니다. 해당 노드에 서비스가 있으면 먼저 서비스가 다른 노드로 옮겨집니다. 클러스터 관리자가 복제본/서비스에 대한 노드를 찾을 수 없으면 작업이 지연/차단됩니다.

> [!NOTE]
> Remove-AzureRmServiceFabricNodeType을 사용하여 프로덕션 클러스터에서 노드 유형을 제거하는 것은 자주 사용하지 않는 것이 좋습니다. 이 경우 기본적으로 노드 유형 뒤에 Virtual Machine Scale Set 리소스가 삭제되므로 매우 위험한 명령입니다. Remove-AzureRmServiceFabricNodeType을 호출하면, 시스템은 제거가 안전한지 여부를 알 수 있는 방법이 없습니다. 

## <a name="durability-characteristics"></a>내구성 특징
Remove-AzureRmServiceFabricNodeType을 사용하는 경우 안전성이 속도보다 우선합니다. Silver 또는 Gold [내구성 특징](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)이 권장됩니다. 그 이유는:
- Bronze는 상태 정보 저장이 전혀 보장되지 않습니다.
- Silver와 Gold 내구성은 VMSS에 대한 모든 내용을 보관합니다.
- Gold의 경우 VMSS 아래에 있는 Azure 업데이트를 제어할 수 있습니다.

Service Fabric은 데이터가 손실되지 않도록 내부 변경 및 업데이트를 "오케스트레이션"합니다. 단, Bronze 내구성으로 노드를 제거하면 상태 정보가 손실될 수 있습니다. 주 노드 유형을 제거하는 경우 애플리케이션이 상태 비저장이면 Bronze가 허용됩니다. 프로덕션에서 상태 저장 워크로드를 실행하는 경우에는 최소 구성이 Silver여야 합니다. 마찬가지로 프로덕션 시나리오의 경우 주 노드 유형은 항상 Silver 또는 Gold여야 합니다.

### <a name="more-about-bronze-durability"></a>Bronze 내구성에 대한 자세한 정보

Bronze 노드 유형을 제거하면 노드 유형의 모든 노드가 즉시 다운됩니다. Service Fabric은 Bronze 노드 VMSS 업데이트를 보관하지 않기 때문에 모든 VM이 즉시 다운됩니다. 해당 노드에 상태 저장 항목이 있으면 데이터가 손실됩니다. 이제 상태 비저장이더라도 Service Fabric의 모든 노드가 링에 참여하기 때문에 전체 환경이 손실되어 클러스터 자체에 영향을 줄 수 있습니다.

단일 노드 제거와 달리, 이론상으로, 노드는 한 번에 하나씩 제거할 수 있기 때문에, 다른 노드를 제거하기 전에 복제본과 서비스가 옮겨질 때까지 기다리고, 시스템이 안정화될 때까지 기다리는 단계가 반복됩니다.  하지만, 한 번에 여러 노드를 동시 제거하면 클러스터가 다운될 수 있습니다. (Service Fabric에는 Bronze 내구성으로 VMSS 업데이트가 보관되지 않기 때문입니다.)

## <a name="recommended-node-type-removal-process"></a>권장되는 노드 유형 제거 프로세스

가장 안전하고 신속하게 노드를 제거하려면:
1.  Bronze 내구성을 사용하는 경우 또는 노트 유형을 삭제하면 손실되는 상태 정보가 포함된 애플리케이션을 시스템에서 옮기지 않도록 하려면, 먼저 노드 유형 제거로 인해 영향을 받게 될 노드에서 상태 저장 데이터를 비웁니다.
2.  제거할 노드마다 [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps)를 실행합니다.
3.  노드 유형 제거로 인해 영향을 받게 될 VM에 [Remove-AzureRmVmss](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#remove-vms-from-a-scale-set)를 실행합니다.
4. [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype)을 실행하여 노드 유형을 제거합니다.

## <a name="next-steps"></a>다음 단계
- 클러스터 [내구성 특징](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)에 대해 자세히 알아봅니다.
- [노드 유형과 Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md)에 대해 자세히 알아봅니다.
- [Service Fabric 클러스터 크기 조정](service-fabric-cluster-scaling.md)에 대해 자세히 알아봅니다.