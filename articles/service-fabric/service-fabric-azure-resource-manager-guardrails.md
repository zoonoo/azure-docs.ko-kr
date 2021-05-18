---
title: Service Fabric Azure Resource Manager 배포 가드 레일
description: 이 문서에서는 Azure Resource Manager를 통해 Service Fabric 클러스터를 배포할 때 발생하는 일반적인 실수와 이를 방지하는 방법에 대한 개요를 제공합니다.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: aa1499c57ead28bfcee90a2f224ef9c3bb1d7f58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86247827"
---
# <a name="service-fabric-guardrails"></a>Service Fabric 가드 레일 
Service Fabric 클러스터를 배포할 때 가드 레일이 배치되어 잘못된 클러스터의 경우 Azure Resource Manager 배포에 실패합니다. 다음 섹션에서는 일반적인 클러스터 구성 문제에 대한 개요와 이러한 문제를 완화하는 데 필요한 단계를 제공합니다. 

## <a name="durability-mismatch"></a>내구성 불일치
### <a name="overview"></a>개요
Service Fabric 노드 형식의 내구성 값은 Azure Resource Manager 템플릿의 서로 다른 두 섹션에서 정의됩니다. 가상 머신 확장 집합 리소스의 가상 머신 확장 집합 확장 섹션 및 Service Fabric 클러스터 리소스의 노드 형식 섹션. 이러한 섹션의 내구성 값이 일치해야 합니다. 일치하지 않으면 리소스 배포가 실패합니다.

다음 섹션에는 가상 머신 확장 집합 확장 내구성 설정과 Service Fabric 노드 형식 내구성 설정 간의 내구성 불일치 예가 포함되어 있습니다.  

**가상 머신 확장 집합 내구성 설정**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Service Fabric 노드 형식 내구성 설정** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>오류 메시지
* 가상 머신 확장 집합 내구성이 현재 Service Fabric 노드 형식 내구성 수준과 일치하지 않습니다.
* 가상 머신 확장 집합 내구성이 대상 Service Fabric 노드 형식 내구성 수준과 일치하지 않습니다.
* 가상 머신 확장 집합 내구성이 현재 Service Fabric 내구성 수준 또는 대상 Service Fabric 노드 형식 내구성 수준과 일치합니다. 

### <a name="mitigation"></a>완화 방법
위의 오류 메시지 중 하나로 표시되는 내구성 불일치를 수정하려면:
1. 값이 일치하도록 Azure Resource Manager 템플릿의 가상 머신 확장 집합 확장 또는 Service Fabric 노드 형식 섹션에서 내구성 수준을 업데이트합니다.
2. 업데이트된 값으로 Azure Resource Manager 템플릿을 다시 배포합니다.


## <a name="seed-node-deletion"></a>시드 노드 삭제 
### <a name="overview"></a>개요
Service Fabric 클러스터에는 클러스터의 주 노드 형식에서 실행되는 시스템 서비스의 복제본 수를 결정하는 데 사용되는 [안정성 계층](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) 속성이 있습니다. 필요한 복제본 수는 클러스터의 주 노드 형식에서 유지 관리해야 하는 최소 노드 수를 결정합니다. 주 노드 형식의 노드 수가 안정성 계층에 필요한 최소 개수보다 적어지면 클러스터가 불안정해집니다.  

### <a name="error-messages"></a>오류 메시지 
시드 노드 제거 작업이 검색되었으며 거부됩니다. 
* 이 작업을 수행하면 {0} 잠재적 시드 노드만 클러스터에 남아 있는 반면 {1}이(가) 최소한으로 필요합니다.
* {1}에서 {0} 시드 노드를 제거하면 시드 노드 쿼럼이 손실되어 클러스터가 중단됩니다. 한 번에 제거할 수 있는 최대 시드 노드 수는 {2}입니다.
 
### <a name="mitigation"></a>완화 방법 
주 노드 형식에 클러스터에 지정된 안정성에 대한 충분한 가상 머신이 있는지 확인합니다. 지정된 안정성 계층에 대한 최소 노드 수 미만으로 가상 머신 확장 집합을 가져오는 경우 가상 머신을 제거할 수 없습니다.
* 안정성 계층이 올바르게 지정된 경우 안정성 계층에 필요한 만큼 주 노드 형식에 충분한 노드가 있는지 확인합니다. 
* 안정성 계층이 올바르지 않으면 가상 머신 확장 집합 작업을 시작하기 전에 먼저 안정성 수준을 낮추기 위해 Service Fabric 리소스의 변경을 시작하고 완료될 때까지 기다립니다.
* 안정성 계층이 브론즈인 경우 다음 [단계](./service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) 에 따라 클러스터를 정상적으로 스케일 인하세요.

## <a name="next-steps"></a>다음 단계
* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* Service Fabric 문제 해결:[문제 해결 가이드](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
