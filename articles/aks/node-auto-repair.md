---
title: AKS(Azure Kubernetes Service) 노드 자동 복구
description: 노드 자동 복구 기능 및 AKS에서 중단된 작업자 노드를 수정하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 95783000739fd42e39ef1bbce88c3b8e62d4a8dc
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110073201"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>AKS(Azure Kubernetes Service) 노드 자동 복구

AKS는 작업자 노드의 상태를 지속적으로 모니터링하고 비정상 상태가 되면 자동 노드 복구를 수행합니다. Azure VM(가상 머신) 플랫폼은 문제가 발생한 [VM에서 유지 관리를 수행][vm-updates]합니다. 

AKS와 Azure VM은 함께 작동하여 클러스터에 대한 서비스 중단을 최소화합니다.

이 문서에서는 Windows 및 Linux 노드 둘 다에서 자동 노드 복구 기능이 어떻게 작동하는지 알아봅니다. 

## <a name="how-aks-checks-for-unhealthy-nodes"></a>AKS에서 비정상 노드를 확인하는 방법

AKS는 다음 규칙을 사용하여 노드가 비정상이고 복구가 필요한지 여부를 확인합니다. 
* 노드가 10분 이내에 연속 확인하여 **준비되지 않음** 상태를 보고합니다.
* 노드가 10분 내에 상태를 보고하지 않습니다.

kubectl을 사용하여 노드의 상태를 수동으로 확인할 수 있습니다.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>자동 복구 작동 방법

> [!Note]
> AKS는 사용자 계정 **aks-remediator** 로 복구 작업을 시작합니다.

AKS가 10분 동안 비정상 상태로 유지되는 비정상 노드를 식별하는 경우 AKS는 다음 작업을 수행합니다.

1. 노드를 다시 부팅합니다.
1. 다시 부팅할 수 없는 경우 노드를 이미지로 다시 설치합니다.
1. 이미지로 다시 설치하지 못한 경우 새 노드를 만들고 이미지로 다시 설치합니다.

자동 복구에 실패한 경우 대체 수정 사항은 AKS 엔지니어가 조사합니다. 

AKS가 상태 확인 중에 여러 비정상 노드를 발견하면 다른 복구가 시작되기 전에 각 노드가 개별적으로 복구됩니다.


## <a name="limitations"></a>제한 사항

대부분의 경우 AKS는 노드가 비정상인지 확인하고 문제를 복구하려고 시도할 수 있지만 AKS가 문제를 복구할 수 없거나 문제가 있음을 검색할 수 없는 경우가 있습니다. 예를 들어 네트워크 구성에서 오류로 인해 노드 상태가 보고되지 않는 경우 AKS는 문제를 검색할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[가용성 영역][availability-zones]을 사용하여 AKS 클러스터 워크로드를 통해 고가용성을 개선합니다.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
