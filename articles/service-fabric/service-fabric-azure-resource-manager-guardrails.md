---
title: Service Fabric Azure Resource Manager 배포 guardrails
description: 이 문서에서는 Azure Resource Manager를 통해 Service Fabric 클러스터를 배포할 때 발생 하는 일반적인 실수와이를 방지 하는 방법에 대 한 개요를 제공 합니다.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: pepogors
ms.openlocfilehash: fe5ff2a5eeb4b2c73165d1577702eb6af7079b61
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426745"
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


### <a name="mitigation"></a>완화 방법
위의 오류 메시지에 표시 되는 내구성 불일치를 해결 하려면 다음을 수행 합니다.
1. Azure Resource Manager 템플릿의 가상 머신 확장 집합 확장 또는 Service Fabric 노드 유형 섹션에서 내구성 수준을 업데이트 하 여 값이 일치 하는지 확인 합니다.
2. 업데이트 된 값으로 Azure Resource Manager 템플릿을 다시 배포 합니다.

## <a name="next-steps"></a>다음 단계
* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* Service Fabric 문제 해결: [문제 해결 가이드](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
