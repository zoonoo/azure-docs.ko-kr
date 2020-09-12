---
title: AKS (Azure Kubernetes Service) 노드 자동 복구
description: 노드 자동 복구 기능 및 AKS에서 중단 된 작업자 노드를 수정 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 781a1ffebb40b0cce9f18699d308db90633e8626
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89490108"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>AKS (Azure Kubernetes Service) 노드 자동 복구

AKS는 작업자 노드의 상태를 지속적으로 확인 하 고 비정상 상태가 되 면 노드의 자동 복구를 수행 합니다. 이 문서에서는 Windows 및 Linux 노드 모두에서 자동 노드 복구 기능이 작동 하는 방식에 대해 운영자에 게 알립니다. AKS 복구 외에도 Azure VM 플랫폼은 문제를 경험 하는 [Virtual Machines에 대 한 유지 관리를 수행][vm-updates] 합니다. AKS와 Azure Vm은 함께 작동 하 여 클러스터에 대 한 서비스 중단을 최소화 합니다.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS에서 비정상 노드를 확인 하는 방법

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

위의 규칙을 기반으로 비정상 상태이 고 연속으로 10 분 동안 비정상 상태로 유지 되는 노드는 다음 작업이 수행 됩니다.

1. 노드 다시 부팅
1. 다시 부팅할 수 없는 경우 노드를 이미지로 다시 설치 합니다.
1. 이미지로 다시 설치 하는 작업이 실패 하면 새 노드를 만들고 이미지로 다시 설치 합니다.

모든 작업이 성공 하지 못한 경우에는 AKS 엔지니어가 추가 재구성 조사 됩니다. 상태 검사 중에 여러 노드가 비정상 상태인 경우에는 다른 복구가 시작 되기 전에 각 노드가 개별적으로 복구 됩니다.

## <a name="next-steps"></a>다음 단계

[가용성 영역][availability-zones]을 사용하여 AKS 클러스터 워크로드를 통해 고가용성을 개선합니다.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
