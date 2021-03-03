---
title: Service Fabric 관리 클러스터 구성 (미리 보기)
description: 자동 OS 업그레이드, NSG 규칙 등에 대해 Service Fabric 관리 클러스터를 구성 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 44b1b949fe314231cb44f190c31b53903e47a904
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732635"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Service Fabric 관리 클러스터 (미리 보기) 구성 옵션

클러스터를 만들 때 [Service Fabric 관리 클러스터 SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) 를 선택 하는 것 외에도 다양 한 방법으로 구성할 수 있습니다. 현재 미리 보기에서 다음을 수행할 수 있습니다.

* 클러스터에 대 한 [네트워킹 옵션](how-to-managed-cluster-networking.md) 구성
* 노드 유형에 [가상 머신 확장 집합 확장](how-to-managed-cluster-vmss-extension.md) 추가
* 노드 유형에 서 [관리 되는 id](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) 구성
* 노드에 대해 [자동 OS 업그레이드](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) 사용
* 노드에서 [OS 및 데이터 디스크 암호화](how-to-enable-managed-cluster-disk-encryption.md) 를 사용 하도록 설정

## <a name="enable-automatic-os-image-upgrades"></a>자동 OS 이미지 업그레이드 사용

관리 되는 클러스터 노드를 실행 하는 가상 컴퓨터에 대 한 자동 OS 이미지 업그레이드를 사용 하도록 선택할 수 있습니다. 가상 머신 확장 집합 리소스는 Service Fabric 관리 클러스터를 사용 하 여 대신 관리 되지만 클러스터 노드에 대해 자동 OS 이미지 업그레이드를 사용 하도록 선택할 수 있습니다. [클래식 Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) 클러스터와 마찬가지로 관리 되는 클러스터 노드는 클러스터에 대 한 의도 하지 않은 중단을 방지 하기 위해 기본적으로 업그레이드 되지 않습니다.

자동 OS 업그레이드를 사용 하도록 설정 하려면:

* `2021-01-01-preview`이상 버전의 *ServiceFabric/managedclusters* 및 *ServiceFabric/managedclusters/nodetypes* 리소스를 사용 합니다.
* 클러스터의 속성을 `enableAutoOSUpgrade` *true* 로 설정 합니다.
* 클러스터 nodeTypes의 리소스 속성 `vmImageVersion` 을 *최신* 으로 설정

다음은 그 예입니다. 

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
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

이 기능을 사용 하도록 설정 하면 관리 되는 클러스터에서 OS 이미지 버전 쿼리 및 추적을 시작할 Service Fabric. 새 OS 버전을 사용할 수 있는 경우 클러스터 노드 형식 (가상 머신 확장 집합)은 한 번에 하나씩 업그레이드 됩니다. Service Fabric 런타임 업그레이드는 진행 중인 클러스터 노드를 확인 한 후에만 수행 됩니다.

업그레이드가 실패 하는 경우 최대 3 회 다시 시도 하 여 24 시간 후에 Service Fabric을 다시 시도 합니다. 클래식 (관리 되지 않는) Service Fabric 업그레이드와 마찬가지로 비정상 앱 또는 노드가 OS 이미지 업그레이드를 차단할 수 있습니다.

이미지 업그레이드에 대 한 자세한 내용은 [Azure virtual machine scale sets를 사용 하 여 자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Service Fabric 관리 클러스터 개요](overview-managed-cluster.md)

[Service Fabric 클러스터 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates)
