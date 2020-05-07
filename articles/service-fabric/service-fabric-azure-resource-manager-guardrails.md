---
title: Service Fabric Azure Resource Manager 배포 guardrails
description: 이 문서에서는 Azure Resource Manager를 통해 Service Fabric 클러스터를 배포할 때 발생 하는 일반적인 실수와이를 방지 하는 방법에 대 한 개요를 제공 합니다.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: 04c6444723180c34f6605810260f5f865dff2d12
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790918"
---
# <a name="service-fabric-guardrails"></a>Service Fabric guardrails 
Service Fabric 클러스터를 배포 하는 경우 guardrails이 배치 되며,이는 잘못 된 클러스터 구성의 경우 Azure Resource Manager 배포에 실패 합니다. 다음 섹션에서는 일반적인 클러스터 구성 문제와 이러한 문제를 완화 하는 데 필요한 단계에 대 한 개요를 제공 합니다. 

## <a name="durability-mismatch"></a>내구성 불일치
### <a name="overview"></a>개요
Service Fabric 노드 형식에 대 한 내구성 값은 Azure Resource Manager 템플릿의 서로 다른 두 섹션에서 정의 됩니다. 가상 머신 확장 집합 리소스의 가상 머신 확장 집합 확장 섹션 및 Service Fabric 클러스터 리소스의 노드 유형 섹션 이러한 섹션의 내구성 값이 일치 해야 합니다. 그렇지 않으면 리소스 배포가 실패 합니다.

다음 섹션에는 가상 머신 확장 집합 확장 내구성 설정과 Service Fabric 노드 유형 내구성 설정 간의 내구성 불일치의 예가 포함 되어 있습니다.  

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
* 가상 머신 확장 집합 내구성 불일치가 현재 Service Fabric 노드 유형 내구성 수준과 일치 하지 않습니다.
* 가상 머신 확장 집합 내구성이 대상 Service Fabric 노드 유형 내구성 수준과 일치 하지 않습니다.
* 가상 머신 확장 집합 내구성은 현재 Service Fabric 내구성 수준 또는 대상 Service Fabric 노드 유형 내구성 수준과 일치 합니다. 

### <a name="mitigation"></a>완화
위의 오류 메시지에 표시 되는 내구성 불일치를 해결 하려면 다음을 수행 합니다.
1. Azure Resource Manager 템플릿의 가상 머신 확장 집합 확장 또는 Service Fabric 노드 유형 섹션에서 내구성 수준을 업데이트 하 여 값이 일치 하는지 확인 합니다.
2. 업데이트 된 값으로 Azure Resource Manager 템플릿을 다시 배포 합니다.


## <a name="seed-node-deletion"></a>시드 노드 삭제 
### <a name="overview"></a>개요
Service Fabric 클러스터에는 클러스터의 주 노드 유형에 서 실행 되는 시스템 서비스의 복제본 수를 결정 하는 데 사용 되는 [안정성 계층](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) 속성이 있습니다. 필요한 복제본 수는 클러스터의 주 노드 형식에서 유지 관리 해야 하는 최소 노드 수를 결정 합니다. 주 노드 형식의 노드 수가 안정성 계층에 필요한 최소 미만이 면 클러스터가 불안정 해지기 때문입니다.  

### <a name="error-messages"></a>오류 메시지 
Seed 노드 제거 작업이 검색 되었으며 거부 됩니다. 
* 이 작업을 수행 하면 {0} 잠재적인 시드 노드만 클러스터에 남아 있을 수 {1} 있으며, 최소한의 경우에는 필요 합니다.
* 에서 {0} {1} 시드 노드를 제거 하면 시드 노드 쿼럼이 손실 되어 클러스터가 중단 됩니다. 한 번에 제거할 수 있는 최대 시드 노드 수는 {2}입니다.
 
### <a name="mitigation"></a>완화 
주 노드 형식에 클러스터에 지정 된 안정성에 대 한 충분 한 Virtual Machines 있는지 확인 합니다. 가상 컴퓨터 확장 집합을 지정 된 안정성 계층에 대 한 최소 노드 수 아래로 가져오는 경우 가상 컴퓨터를 제거할 수 없습니다.
* 안정성 계층이 올바르게 지정 된 경우 안정성 계층에 필요한 대로 주 노드 형식에 충분 한 노드가 있는지 확인 합니다. 
* 안정성 계층이 올바르지 않으면 가상 머신 확장 집합 작업을 시작 하기 전에 먼저 안정성 수준을 낮추고 Service Fabric 리소스의 변경을 시작 하 고 완료 될 때까지 기다립니다.
* 안정성 계층이 청동 인 경우 다음 [단계](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-in-out#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) 에 따라 클러스터를 정상적으로 확장 하세요.

## <a name="next-steps"></a>다음 단계
* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* Service Fabric 문제 해결: [문제 해결 가이드](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
