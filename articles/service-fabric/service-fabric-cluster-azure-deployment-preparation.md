---
title: Azure Service Fabric 클러스터 배포 계획
description: Azure에 프로덕션 Service Fabric 클러스터를 배포하기 위해 계획하고 준비하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: e6b7c0a14f6e5f63e84c8efa484347cd4ff322f4
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110789546"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>클러스터 배포를 위한 계획 및 준비

프로덕션 클러스터 배포를 계획하고 준비하는 작업이 매우 중요합니다.  고려해야 할 요인이 많습니다.  이 문서에서는 클러스터 배포를 준비하는 단계를 설명합니다.

## <a name="read-the-best-practices-information"></a>모범 사례 정보 읽기
Azure Service Fabric 애플리케이션 및 클러스터를 관리하기 위해서는 프로덕션 환경의 안정성을 최적화할 수 있도록 수행하는 것이 좋은 작업이 있습니다.  자세한 내용은 [Service Fabric 애플리케이션 및 클러스터 모범 사례](./service-fabric-best-practices-security.md)를 참조하세요.

## <a name="select-the-os-for-the-cluster"></a>클러스터용 OS 선택
Azure Service Fabric을 사용하면 Windows Server 또는 Linux를 실행하는 VM 또는 컴퓨터에서 Service Fabric 클러스터를 만들 수 있습니다.  클러스터를 배포하기 전에 Windows OS 또는 Linux OS를 선택해야 합니다.  클러스터의 모든 노드(가상 머신)는 동일한 OS를 실행하며, 동일한 클러스터에서 Windows 및 Linux VM을 혼합할 수 없습니다.

## <a name="capacity-planning"></a>용량 계획
프로덕션 배포의 경우 용량 계획은 중요한 단계입니다. 다음은 해당 프로세스의 일부로 고려해야 할 몇 가지 사항입니다.

* 클러스터용 초기 노드 형식의 수 
* 각 노드 형식의 속성(크기, 인스턴스 수, 기본, 인터넷 연결, VM 수 등)
* 클러스터의 안정성 및 지속성 특성

### <a name="select-the-initial-number-of-node-types"></a>초기 노드 형식의 수를 선택합니다.
먼저, 만드는 클러스터를 어디에 사용할지 결정해야 합니다. 이 클러스터에 어떤 종류의 애플리케이션을 배포할 계획인가요? 애플리케이션이 여러 서비스를 보유하고 해당 서비스 중 일부를 공용 또는 인터넷에 연결해야 하나요? 서비스(애플리케이션을 구성함)에는 더 유용한 RAM 또는 더 높은 CPU 사용률과 같은 서로 다른 인프라가 필요한가요? Service Fabric 클러스터는 둘 이상의 노드 형식(주 노드 형식 하나, 주 노드가 아닌 하나 이상의 노드 형식)으로 구성될 수 있습니다. 각 노드 유형은 가상 머신 확장 집합에 매핑됩니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다. [노드 속성 및 배치 제약 조건][placementconstraints]을 설정하여 특정 서비스를 특정 노드 형식으로 제한할 수 있습니다.  자세한 내용은 [Service Fabric 클러스터 용량 계획](service-fabric-cluster-capacity.md)을 참조하세요.

### <a name="select-node-properties-for-each-node-type"></a>각 노드 형식에 대한 노드 속성 선택
노드 형식은 연결된 확장 집합에 있는 VM의 VM SKU, 수, 속성을 정의합니다.

각 노드 형식에 대한 최소 VM 크기는 노드 형식에서 선택한 [내구성 계층][durability]에 따라 결정됩니다. VM SKU를 선택하기 전에 향후 다른 VM SKU가 필요하다고 판단되는 경우 [수직 크기 조정](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)에 필요한 단계를 이해해야 합니다.

주 노드 유형에 대한 최소 VM 수는 선택한 [안정성 계층][reliability]에 따라 결정됩니다.

[주 노드 형식](service-fabric-cluster-capacity.md#primary-node-type)에 대한 최소 권장 사항, [주 노드가 아닌 노드 형식에 대한 상태 저장 워크로드](service-fabric-cluster-capacity.md#stateful-workloads) 및 [주 노드가 아닌 노드 형식에 대한 상태 비저장 워크로드](service-fabric-cluster-capacity.md#stateless-workloads)를 참조하세요.

최소 노드 수는 이 노드 형식에서 실행하려는 애플리케이션/서비스의 복제본 수를 기준으로 해야 합니다.  [Service Fabric 애플리케이션에 대한 용량 계획](service-fabric-capacity-planning.md)을 통해 애플리케이션을 실행하는 데 필요한 리소스를 예상할 수 있습니다. 나중에 언제든지 클러스터를 확장하거나 축소하여 변화하는 애플리케이션 워크로드에 맞게 조정할 수 있습니다. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>가상 머신 확장 집합에 대한 임시 OS 디스크 사용

*임시 OS 디스크* 는 로컬 VM(가상 머신)에 만들어지며, 원격 Azure Storage에 저장되지 않습니다. 기존 영구 OS 디스크에 비해 임시 OS 디스크는 다음과 같으므로, 모든 Service Fabric 노드 형식(주/보조)에 권장됩니다.

* OS 디스크에 대한 읽기/쓰기 대기 시간 단축
* 다시 설정/이미지로 다시 설치 노드 관리 작업을 더 빠르게 사용하도록 설정
* 전체 비용 절감(디스크는 무료이며 추가 스토리지 비용이 발생하지 않음)

임시 OS 디스크는 특정 Service Fabric 기능이 아니라 Service Fabric 노드 형식에 매핑되는 Azure *가상 머신 확장 집합* 의 기능입니다. Service Fabric과 함께 사용하려면 클러스터 Azure Resource Manager 템플릿에서 다음을 수행해야 합니다.

1. 노드 형식에서 임시 OS 디스크에 [지원되는 Azure VM 크기](../virtual-machines/ephemeral-os-disks.md)를 지정하고, VM 크기에는 OS 디스크 크기를 지원하기에 충분한 캐시 크기가 있는지 확인합니다(아래 *참고* 참조). 예:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > 캐시 크기가 VM 자체의 OS 디스크 크기보다 크거나 같은 VM 크기를 선택해야 합니다. 그렇지 않으면 Azure 배포에서 오류가 발생할 수 있습니다(처음에 허용된 경우에도 발생 가능).

2. `2018-06-01` 이상의 가상 머신 확장 집합 버전(`vmssApiVersion`)을 지정합니다.

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. 배포 템플릿의 가상 머신 확장 집합 섹션에서 `diffDiskSettings`용 `Local` 옵션을 지정합니다.

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

> [!NOTE]
> OS를 업그레이드하는 경우 OS 디스크가 손실되므로, 사용자 애플리케이션에는 OS 디스크에 대한 종속성/파일/아티팩트가 없어야 합니다.

> [!NOTE]
> 기존의 비임시 VMSS는 임시 디스크를 사용하도록 현재 위치에서 업그레이드할 수 없습니다.
> 마이그레이션하려면 사용자가 임시 디스크로 새 nodeType을 [추가](./virtual-machine-scale-set-scale-node-type-scale-out.md)하고, 워크로드를 새 nodeType으로 이동하여 기존 nodetype을 [제거](./service-fabric-how-to-remove-node-type.md)해야 합니다.
>

자세한 정보 및 추가 구성 옵션은 [Azure VM용 임시 OS 디스크](../virtual-machines/ephemeral-os-disks.md)를 참조하세요. 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>클러스터의 내구성 및 안정성 수준 선택
지속성 계층은 기본 Azure 인프라와 함께 VM에 있는 권한을 이 시스템에 표시하는 데 사용됩니다. 주 노드 유형에서 이 권한을 사용하면 Service Fabric이 시스템 서비스 및 상태 저장 서비스에 대한 쿼럼 요구 사항에 영향을 주는 VM 수준 인프라 요청(VM 다시 부팅, VM 이미지로 다시 설치, 또는 VM 마이그레이션 등)을 일시 중지할 수 있습니다. 주가 아닌 노드 형식에서 이 권한을 사용하면 Service Fabric이 상태 저장 서비스에 대한 쿼럼 요구 사항에 영향을 주는 VM 수준 인프라 요청(예: VM 다시 부팅, VM 이미지로 다시 설치, VM 마이그레이션)을 일시 중지할 수 있습니다.  다양한 수준의 장점과 사용할 수준 및 시기에 대한 권장 사항은 [클러스터의 내구성 특성][durability]을 참조하세요.

안정성 계층은 주 노드 유형에서 이 클러스터에서 실행하려는 시스템 서비스의 복제본 수를 설정하는 데 사용됩니다. 복제본 수가 많을수록 클러스터에서 시스템 서비스를 신뢰할 수 있습니다.  다양한 수준의 장점과 사용할 수준 및 시기에 대한 권장 사항은 [클러스터의 안정성 특성][reliability]을 참조하세요. 

## <a name="enable-reverse-proxy-andor-dns"></a>역방향 프록시 및/또는 DNS 사용
클러스터의 노드는 동일한 로컬 네트워크에 있으므로 클러스터 내에서 서로 연결하고 있는 서비스는 다른 서비스의 엔드포인트에 직접 액세스할 수 있습니다. 서비스 간을 쉽게 연결할 수 있도록 Service Fabric에서는 추가 서비스([DNS 서비스](service-fabric-dnsservice.md) 및 [역방향 프록시 서비스](service-fabric-reverseproxy.md))를 제공합니다.  클러스터를 배포할 때 두 서비스를 모두 사용할 수 있습니다.

많은 서비스, 특히 컨테이너화된 서비스는 기존 URL 이름을 사용할 수 있으므로 Naming Service 프로토콜 대신 표준 DNS 프로토콜을 사용하여 이러한 이름을 확인할 수 있는 경우, 특히 애플리케이션 "리프트 앤 시프트" 시나리오에서 편리합니다. 바로 이것이 DNS 서비스의 용도입니다. DNS 서비스를 통해 DNS 이름을 서비스 이름에 매핑할 수 있으므로 엔드포인트 IP 주소를 확인할 수 있습니다.

HTTPS를 포함하여 HTTP 엔드포인트를 노출하는 클러스터의 역방향 프록시 주소 서비스입니다. 역방향 프록시는 특정 URI 형식을 제공하여 다른 서비스의 호출 작업을 크게 간소화해 줍니다.  또한 역방향 프록시는 한 서비스가 다른 서비스와 통신하는 데 필요한 해결, 연결, 다시 시도 단계를 처리합니다.

## <a name="prepare-for-disaster-recovery"></a>재해 복구 준비
고가용성 전달의 중요한 부분은 서비스가 다른 모든 유형의 오류를 감당할 수 있는지 확인하는 것입니다. 계획되지 않으며 사용자 통제 하에 있지 않은 오류의 경우 특히 이러한 과정이 중요합니다. [재해 복구 준비](service-fabric-disaster-recovery.md)에서는 올바르게 모델링/관리되지 않으면 재해가 될 수 있는 몇 가지 일반적인 오류 모드에 대해 설명합니다. 또한 재해가 발생할 경우 수행할 완화 조치 및 작업을 설명합니다.

## <a name="production-readiness-checklist"></a>프로덕션 준비 검사 목록
애플리케이션 및 클러스터가 프로덕션 트래픽을 허용할 준비가 되었나요? 프로덕션 환경에 클러스터를 배포하기 전에 [프로덕션 준비 검사 목록](service-fabric-production-readiness-checklist.md)을 실행합니다. 이 검사 목록에 있는 항목을 진행하여 애플리케이션 및 클러스터가 계속 원활하게 실행되도록 합니다. 프로덕션으로 전환하기 전에 이러한 모든 항목을 확인하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
* [Windows를 실행하는 Service Fabric 클러스터 만들기](./service-fabric-best-practices-security.md)
* [Linux를 실행하는 Service Fabric 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster