---
title: Service Fabric 관리형 클러스터 구성
description: 자동 OS 업그레이드, NSG 규칙 등에 대해 Service Fabric 관리 클러스터를 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: 5fd2736917517f20d3d093492c6011d6fcf716cf
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110067441"
---
# <a name="service-fabric-managed-cluster-configuration-options"></a>Service Fabric 관리 클러스터 구성 옵션

클러스터를 만들 때 [Service Fabric 관리 클러스터 SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus)를 선택하는 것 외에도 다음을 포함하여 여러 가지 다른 방법으로 클러스터를 구성할 수 있습니다.

* 노드 유형에 [가상 머신 확장 집합 확장](how-to-managed-cluster-vmss-extension.md) 추가
* 클러스터 [가용성 영역 스패닝](how-to-managed-cluster-availability-zones.md) 구성
* 클러스터 [NSG 규칙 및 기타 네트워킹 옵션](how-to-managed-cluster-networking.md) 구성
* 클러스터 노드 유형에서 [관리 ID](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) 구성
* 클러스터 노드에 대해 [자동 OS 업그레이드](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) 사용
* 클러스터 노드에서 [OS 및 데이터 디스크 암호화](how-to-enable-managed-cluster-disk-encryption.md) 사용
* 클러스터 [관리 디스크 유형](how-to-managed-cluster-managed-disk.md) SKU 선택
* 런타임 업데이트를 위한 클러스터 [업그레이드 옵션](how-to-managed-cluster-upgrades.md) 구성

## <a name="enable-automatic-os-image-upgrades"></a>자동 OS 이미지 업그레이드 사용

관리 클러스터 노드를 실행하는 가상 머신으로 자동 OS 이미지 업그레이드를 사용하도록 선택할 수 있습니다. 가상 머신 확장 집합 리소스는 Service Fabric 관리 클러스터를 대신하여 관리되지만 클러스터 노드에 대해 자동 OS 이미지 업그레이드를 사용하도록 설정하는 것이 좋습니다. [클래식 서비스 패브릭](service-fabric-best-practices-infrastructure-as-code.md#virtual-machine-os-automatic-upgrade-configuration) 클러스터와 마찬가지로 관리 클러스터 노드는 클러스터에 대한 의도하지 않은 중단을 방지하기 위해 기본적으로 업그레이드되지 않습니다.

자동 OS 업그레이드를 사용하도록 설정하려면:

* *Microsoft.ServiceFabric/managedclusters* 및 *Microsoft.ServiceFabric/managedclusters/nodetypes* 리소스의 `2021-05-01` 이상 버전을 사용합니다.
* 클러스터의 속성 `enableAutoOSUpgrade`를 *true* 로 설정합니다.
* 클러스터 nodeTypes의 리소스 속성 `vmImageVersion`을 *latest* 로 설정합니다.

예를 들면 다음과 같습니다.

```json
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

사용하도록 설정되면 Service Fabric은 관리형 클러스터에서 OS 이미지 버전을 쿼리하고 추적하기 시작합니다. 새 OS 버전을 사용할 수 있는 경우 클러스터 노드 유형(가상 머신 확장 집합)이 한 번에 하나씩 업그레이드됩니다. Service Fabric 런타임 업그레이드는 진행 중인 클러스터 노드 OS 이미지 업그레이드가 없음을 확인한 후에만 수행됩니다.

업그레이드가 실패하면 Service Fabric은 24시간 후에 최대 3번의 다시 시도를 위해 다시 시도합니다. 클래식(관리되지 않는) Service Fabric 업그레이드와 마찬가지로 비정상 앱 또는 노드가 OS 이미지 업그레이드를 차단할 수 있습니다.

이미지 업그레이드에 대한 자세한 내용은 [Azure Virtual Machine Scale Sets를 사용한 자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Service Fabric 관리 클러스터 개요](overview-managed-cluster.md)

[Service Fabric 클러스터 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates)
