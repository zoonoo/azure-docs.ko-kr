---
title: AZURE Kubernetes 서비스(AKS) 노드 자동 복구
description: 노드 자동 복구 기능및 AKS가 손상된 작업자 노드를 수정하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284843"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes 서비스(AKS) 노드 자동 복구

AKS는 작업자 노드의 상태를 지속적으로 확인하고 비정상 상태가 되면 노드의 자동 복구를 수행합니다. 이 설명서에서는 Azure Kubernetes 서비스(AKS)가 작업자 노드를 모니터링하고 비정상 작업자 노드를 복구하는 방법을 설명합니다.  설명서는 AKS 운영자에게 노드 복구 기능의 동작을 알리는 것입니다. 또한 Azure 플랫폼은 문제가 발생하는 [가상 머신에서 유지 관리를 수행한다는][vm-updates] 점에 유의해야 합니다. AKS와 Azure는 클러스터의 서비스 중단을 최소화하기 위해 함께 작동합니다.

> [!Important]
> 노드 자동 복구 기능은 현재 Windows Server 노드 풀에서 지원되지 않습니다.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS가 비정상 노드를 확인하는 방법

> [!Note]
> AKS는 사용자 계정 **aks-remediato와**노드에 대한 복구 작업을 수행합니다.

AKS는 규칙을 사용하여 노드가 비정상 상태이며 복구가 필요한지 확인합니다. AKS는 다음 규칙을 사용하여 자동 복구가 필요한지 확인합니다.

* 노드는 10분 의 기간 내에 연속된 검사에서 **NotReady** 의 상태를 보고합니다.
* 노드가 10분 이내에 상태를 보고하지 않습니다.

kubectl을 사용 하 고 노드의 상태를 수동으로 확인할 수 있습니다. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>자동 수리 작동 방식

> [!Note]
> AKS는 사용자 계정 **aks-remediato와**노드에 대한 복구 작업을 수행합니다.

이 동작은 **가상 시스템 크기 조정 집합에**대한 것입니다.  자동 복구는 손상된 노드를 복구하는 몇 가지 단계를 수행합니다.  노드가 비정상으로 확인되면 AKS는 여러 수정 단계를 시도합니다.  단계는 다음 순서로 수행됩니다.

1. 컨테이너 런타임이 10분 동안 응답하지 않으면 노드에서 실패한 런타임 서비스가 다시 시작됩니다.
2. 노드가 10분 이내에 준비되지 않으면 노드가 재부팅됩니다.
3. 30분 이내에 노드가 준비되지 않으면 노드가 다시 이미지화됩니다.

> [!Note]
> 여러 노드가 비정상인 경우 하나씩 복구됩니다.

## <a name="next-steps"></a>다음 단계

[가용성 영역을][availability-zones] 사용하여 AKS 클러스터 워크로드를 사용하여 고가용성을 높일 수 있습니다.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
