---
title: AKS (Azure Kubernetes Service) 노드 자동 복구
description: 노드 자동 복구 기능 및 AKS에서 중단 된 작업자 노드를 수정 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 91384461567634faabaaa1dd588d6e7ec6ece60e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84735628"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>AKS (Azure Kubernetes Service) 노드 자동 복구

AKS는 작업자 노드의 상태를 지속적으로 확인 하 고 비정상 상태가 되 면 노드의 자동 복구를 수행 합니다. 이 문서에서는 자동 노드 복구 기능의 작동 방식에 대해 운영자에 게 알립니다. AKS 복구 외에도 Azure VM 플랫폼은 문제를 경험 하는 [Virtual Machines에 대 한 유지 관리를 수행][vm-updates] 합니다. AKS와 Azure Vm은 함께 작동 하 여 클러스터에 대 한 서비스 중단을 최소화 합니다.

> [!Important]
> 노드 자동 복구 기능은 현재 Windows Server 노드 풀에서 지원 되지 않습니다.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS에서 비정상 노드를 확인 하는 방법

> [!Note]
> AKS 사용자 계정 **AKS-remediator**를 사용 하 여 노드에서 복구 작업을 수행 합니다.

AKS는 규칙을 사용 하 여 노드가 비정상 이며 복구 해야 하는지 여부를 확인 합니다. AKS는 다음 규칙을 사용 하 여 자동 복구가 필요한 지 여부를 결정 합니다.

* 노드가 10 분 기간 내에 연속 검사에 대해 **Notready** 상태를 보고 합니다.
* 노드가 10 분 내에 상태를 보고 하지 않습니다.

Kubectl를 사용 하 여 노드의 상태를 수동으로 확인할 수 있습니다. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>자동 복구 작동 방법

> [!Note]
> AKS 사용자 계정 **AKS-remediator**를 사용 하 여 복구 작업을 시작 합니다.

자동 복구는 VM 집합 유형이 **Virtual Machine Scale Sets**인 클러스터에 대해 기본적으로 지원 됩니다. 노드가 위의 규칙을 기반으로 비정상으로 확인 되는 경우 AKS는 10 개의 연속 비정상 분 후에 노드를 다시 부팅 합니다. 초기 복구 작업 후 노드가 비정상 상태로 유지 되는 경우에는 AKS 엔지니어가 추가 재구성 조사 됩니다.
  
상태 검사 중에 여러 노드가 비정상 상태인 경우에는 다른 복구가 시작 되기 전에 각 노드가 개별적으로 복구 됩니다.

## <a name="next-steps"></a>다음 단계

[가용성 영역][availability-zones]을 사용하여 AKS 클러스터 워크로드를 통해 고가용성을 개선합니다.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
