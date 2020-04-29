---
title: AKS (Azure Kubernetes Service) 노드 자동 복구
description: 노드 자동 복구 기능 및 AKS에서 중단 된 작업자 노드를 수정 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80284843"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>AKS (Azure Kubernetes Service) 노드 자동 복구

AKS는 작업자 노드의 상태를 지속적으로 확인 하 고 비정상 상태가 되 면 노드의 자동 복구를 수행 합니다. 이 설명서에서는 AKS (Azure Kubernetes Service)가 작업자 노드를 모니터링 하 고 비정상 작업자 노드를 복구 하는 방법에 대해 설명 합니다.  이 문서에서는 노드 복구 기능의 동작에 대 한 AKS 운영자에 게 알리는 방법을 설명 합니다. 또한 Azure 플랫폼에서 문제가 발생 하는 [Virtual Machines에 대 한 유지 관리를 수행][vm-updates] 하는 것이 중요 합니다. AKS와 Azure는 함께 작동 하 여 클러스터에 대 한 서비스 중단을 최소화 합니다.

> [!Important]
> 노드 자동 복구 기능은 현재 Windows Server 노드 풀에서 지원 되지 않습니다.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS에서 비정상 노드를 확인 하는 방법

> [!Note]
> AKS 사용자 계정 **AKS-remediator**를 사용 하 여 노드에서 복구 작업을 수행 합니다.

AKS는 규칙을 사용 하 여 노드가 비정상 상태 이며 복구가 필요한 지 여부를 확인 합니다. AKS는 다음 규칙을 사용 하 여 자동 복구가 필요한 지 여부를 결정 합니다.

* 노드가 10 분 기간 내에 연속 검사에 대해 **Notready** 상태를 보고 합니다.
* 노드가 10 분 내에 상태를 보고 하지 않습니다.

Kubectl를 사용 하 여 노드의 상태를 수동으로 확인할 수 있습니다. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>자동 복구 작동 방법

> [!Note]
> AKS 사용자 계정 **AKS-remediator**를 사용 하 여 노드에서 복구 작업을 수행 합니다.

이 동작은 **Virtual Machine Scale Sets**에 대 한 것입니다.  자동 복구는 끊어진 노드를 복구 하는 몇 가지 단계를 수행 합니다.  노드가 비정상으로 확인 되 면 AKS은 몇 가지 수정 단계를 시도 합니다.  단계는 다음 순서 대로 수행 됩니다.

1. 컨테이너 런타임이 10 분 동안 응답 하지 않게 되 면 실패 한 런타임 서비스가 노드에서 다시 시작 됩니다.
2. 노드가 10 분 이내에 준비 되지 않으면 노드가 다시 부팅 됩니다.
3. 노드가 30 분 내에 준비 되지 않은 경우에는 노드를 다시 이미지로 다시 설치할 수 있습니다.

> [!Note]
> 비정상 상태인 노드가 여러 개 있으면 하나씩 복구 됩니다.

## <a name="next-steps"></a>다음 단계

[가용성 영역][availability-zones] 를 사용 하 여 AKS 클러스터 워크 로드를 통해 고가용성을 높일 수 있습니다.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
