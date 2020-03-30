---
title: 서비스 패브릭 Azure 리소스 관리자 배포 가드레일
description: 이 문서에서는 Azure 리소스 관리자를 통해 서비스 패브릭 클러스터를 배포할 때 의 일반적인 실수와 이를 방지하는 방법에 대한 개요를 제공합니다.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368570"
---
# <a name="service-fabric-guardrails"></a>서비스 패브릭 가드레일 
서비스 패브릭 클러스터를 배포할 때 가드레일이 배치되어 잘못된 클러스터 구성의 경우 Azure Resource Manager 배포에 실패합니다. 다음 섹션에서는 일반적인 클러스터 구성 문제와 이러한 문제를 완화하는 데 필요한 단계에 대한 개요를 제공합니다. 

## <a name="durability-mismatch"></a>내구성 불일치
### <a name="overview"></a>개요
서비스 패브릭 노드 유형에 대한 내구성 값은 Azure 리소스 관리자 템플릿의 두 개의 서로 다른 섹션에 정의됩니다. 가상 시스템 배율 집합 리소스의 가상 시스템 축척 집합 확장 섹션 및 서비스 패브릭 클러스터 리소스의 노드 유형 섹션입니다. 이 섹션의 내구성 값이 일치해야 하며 그렇지 않으면 리소스 배포가 실패합니다.

다음 섹션에는 가상 시스템 확장 집합 확장 내구성 설정과 서비스 패브릭 노드 유형 내구성 설정 간의 내구성 불일치에 대한 예가 포함되어 있습니다.  

**가상 기계 스케일 세트 내구성 설정**
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

**서비스 패브릭 노드 유형 내구성 설정** 
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
* 가상 머신 스케일 세트 내구성 불일치가 현재 서비스 패브릭 노드 유형 내구성 수준과 일치하지 않음
* 가상 기계 스케일 세트 내구성이 대상 서비스 패브릭 노드 유형 내구성 수준과 일치하지 않음
* 가상 기계 스케일 세트 내구성은 현재 서비스 패브릭 내구성 수준 또는 대상 서비스 패브릭 노드 유형 내구성 수준과 일치합니다. 

### <a name="mitigation"></a>완화 방법
위의 오류 메시지 중 한 가지로 표시되는 내구성 불일치를 해결하려면 다음을 수행하십시오.
1. Azure 리소스 관리자 템플릿의 가상 시스템 확장 집합 확장 또는 서비스 패브릭 노드 유형 섹션에서 내구성 수준을 업데이트하여 값이 일치하는지 확인합니다.
2. 업데이트된 값으로 Azure 리소스 관리자 템플릿을 다시 배포합니다.


## <a name="seed-node-deletion"></a>시드 노드 삭제 
### <a name="overview"></a>개요
Service Fabric 클러스터에는 클러스터의 기본 노드 유형에서 실행되는 시스템 서비스의 복제본 수를 결정하는 데 사용되는 [안정성 계층](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) 속성이 있습니다. 필요한 복제본 의 수는 클러스터의 기본 노드 유형에서 유지 관리해야 하는 최소 노드 수를 결정합니다. 기본 노드 유형의 노드 수가 안정성 계층에 필요한 최소 값 아래로 떨어지면 클러스터가 불안정해집니다.  

### <a name="error-messages"></a>오류 메시지 
시드 노드 제거 작업이 검색되었으며 거부됩니다. 
* 이 작업을 수행하면 {0} 최소로 필요하지만 {1} 클러스터에 남아 있는 잠재적시드 노드만 발생합니다.
* 시드 {0} 노드를 {1} 제거하면 시드 노드 쿼럼손실로 인해 클러스터가 다운됩니다. 한 번에 제거할 수 있는 시드 노드의 {2}최대 수는 .
 
### <a name="mitigation"></a>완화 방법 
기본 노드 유형에 클러스터에 지정된 안정성에 충분한 가상 시스템이 있는지 확인합니다. 가상 머신이 지정된 안정성 계층의 최소 노드 수 미만으로 가상 머신 스케일 집합을 가져오는 경우 가상 컴퓨터를 제거할 수 없습니다.
* 안정성 계층이 올바르게 지정된 경우 안정성 계층에 필요한 대로 기본 노드 유형에 충분한 노드가 있는지 확인합니다. 
* 안정성 계층이 올바르지 않은 경우 서비스 패브릭 리소스에 대한 변경을 시작하여 먼저 안정성 수준을 낮추고 가상 시스템 규모 집합 작업을 시작하기 전에 완료될 때까지 기다립니다.
* 안정성 계층이 브론즈인 경우 다음 [단계를](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) 수행하여 클러스터를 정상적으로 축소하십시오.

## <a name="next-steps"></a>다음 단계
* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* 서비스 패브릭 문제 해결: [문제 해결 가이드](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
