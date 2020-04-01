---
title: Azure 서비스 패브릭 클러스터 배포 계획
description: Azure에 대한 프로덕션 서비스 패브릭 클러스터 배포를 계획하고 준비하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: ad6a7a6ea9a90bea4a3b6bc553da67a46144dc03
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422274"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>클러스터 배포 계획 및 준비

프로덕션 클러스터 배포를 계획하고 준비하는 것은 매우 중요합니다.  고려해야 할 여러 가지 요소가 있습니다.  이 문서에서는 클러스터 배포를 준비하는 단계를 안내합니다.

## <a name="read-the-best-practices-information"></a>모범 사례 정보 읽기
Azure Service Fabric 응용 프로그램 및 클러스터를 성공적으로 관리하려면 프로덕션 환경의 안정성을 최적화하기 위해 수행하는 것이 좋습니다.  자세한 내용은 [서비스 패브릭 응용 프로그램 및 클러스터 모범 사례를](service-fabric-best-practices-overview.md)참조하십시오.

## <a name="select-the-os-for-the-cluster"></a>클러스터에 대한 OS 선택
Azure Service Fabric을 사용하면 Windows Server 또는 Linux를 실행하는 VM 또는 컴퓨터에서 Service Fabric 클러스터를 만들 수 있습니다.  클러스터를 배포하기 전에 OS: Windows 또는 Linux를 선택해야 합니다.  클러스터의 모든 노드(가상 시스템)는 동일한 OS를 실행하므로 동일한 클러스터에서 Windows 및 Linux VM을 혼합할 수 없습니다.

## <a name="capacity-planning"></a>용량 계획
프로덕션 배포의 경우 용량 계획은 중요한 단계입니다. 다음은 해당 프로세스의 일부로 고려해야 할 몇 가지 사항입니다.

* 클러스터의 초기 노드 유형 수 
* 각 노드 유형의 속성(크기, 인스턴스 수, 기본, 인터넷 직면, VM 수 등)
* 클러스터의 안정성 및 지속성 특성

### <a name="select-the-initial-number-of-node-types"></a>초기 노드 유형 수 선택
먼저, 만드는 클러스터를 어디에 사용할지 결정해야 합니다. 이 클러스터에 어떤 종류의 애플리케이션을 배포할 계획인가요? 애플리케이션이 여러 서비스를 보유하고 해당 서비스 중 일부를 공용 또는 인터넷에 연결해야 하나요? 서비스(애플리케이션을 구성함)에는 더 유용한 RAM 또는 더 높은 CPU 사용률과 같은 서로 다른 인프라가 필요한가요? Service Fabric 클러스터는 기본 노드 유형과 하나 이상의 비기본 노드 유형등 두 개 이상의 노드 유형으로 구성될 수 있습니다. 각 노드 유형은 가상 머신 확장 집합에 매핑됩니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다. [특정][placementconstraints] 서비스를 특정 노드 유형으로 제한하도록 노드 속성 및 배치 제약 조건을 설정할 수 있습니다.  자세한 내용은 [클러스터에서 시작해야 하는 노드 유형 수를](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with)으로 읽어보세요.

### <a name="select-node-properties-for-each-node-type"></a>각 노드 유형에 대한 노드 속성 선택
노드 유형은 연결된 축척 집합에서 VM의 VM SKU, 번호 및 속성을 정의합니다.

각 노드 유형에 대한 최소 VM 크기는 노드 유형에 대해 선택한 [내구성 계층에][durability] 따라 결정됩니다.

주 노드 유형에 대한 최소 VM 수는 선택한 [안정성 계층][reliability]에 따라 결정됩니다.

[기본 노드 유형에](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance)대한 최소 권장 사항, [비기본 노드 유형에 대한 상태 관리 워크로드](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)및 [비기본 노드 유형에 대한 상태 비관리 워크로드를](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads)참조하십시오.

최소 노드 수는 이 노드 유형에서 실행하려는 응용 프로그램/서비스의 복제본 수를 기반으로 해야 합니다.  [Service Fabric 응용 프로그램에 대한 용량 계획을 사용하면](service-fabric-capacity-planning.md) 응용 프로그램을 실행하는 데 필요한 리소스를 예측할 수 있습니다. 언제든지 클러스터를 위또는 아래로 확장하여 응용 프로그램 워크로드 변경에 맞게 조정할 수 있습니다. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>가상 시스템 스케일 세트에 임시 OS 디스크 사용

*임시 OS 디스크는* 로컬 가상 시스템(VM)에서 생성된 저장소이며 원격 Azure 저장소에 저장되지 않습니다. 기존의 영구 OS 디스크, 임시 OS 디스크와 비교하기 때문에 모든 Service Fabric 노드 유형(기본 및 보조)에 권장됩니다.

* 읽기/쓰기 대기 시간을 OS 디스크로 줄입니다.
* 노드 관리 운영 의 신속한 재설정/이미지 재설정 작업 지원
* 전체 비용 절감(디스크는 무료이며 추가 스토리지 비용 없음)

임시 OS 디스크는 특정 서비스 패브릭 기능이 아니라 서비스 패브릭 노드 유형에 매핑되는 Azure *가상 컴퓨터 크기 집합의* 기능입니다. 서비스 패브릭과 함께 사용하려면 클러스터 Azure 리소스 관리자 템플릿에서 다음을 수행해야 합니다.

1. 노드 유형이 임시 OS 디스크에 대해 [지원되는 Azure VM 크기를](../virtual-machines/windows/ephemeral-os-disks.md) 지정하고 VM 크기에 OS 디스크 크기를 지원하기에 충분한 캐시 크기가 있는지 확인합니다(아래 *참고* 참조). 예를 들어:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > 캐시 크기가 VM 자체의 OS 디스크 크기보다 크거나 큰 VM 크기를 선택해야 하며, 그렇지 않으면 Azure 배포에 오류가 발생할 수 있습니다(처음에 수락된 경우에도 마찬가지임).

2. 다음 중 의`vmssApiVersion` `2018-06-01` 가상 시스템 스케일 집합 버전 () 지정:

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. 배포 템플릿의 가상 시스템 축척 집합 `Local` 섹션에서 `diffDiskSettings`다음 에 대한 옵션을 지정합니다.

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
> OS 업그레이드의 경우 OS 디스크가 손실되므로 사용자 응용 프로그램에는 OS 디스크에 종속성/파일/아티팩트가 없어야 합니다.
> 따라서 [패치오케스트라를](https://github.com/microsoft/Service-Fabric-POA) 사용하지 않는 것이 좋습니다임시 디스크응용 프로그램.
>

> [!NOTE]
> 임시 디스크를 사용하기 위해 기존 비임시 VMSS를 업그레이드할 수 없습니다.
> 마이그레이션하려면 임시 디스크가 있는 새 nodeType을 [추가하고](./virtual-machine-scale-set-scale-node-type-scale-out.md) 워크로드를 새 nodeType으로 이동하여 기존 nodeType을 [제거해야](./service-fabric-how-to-remove-node-type.md) &.
>

자세한 정보 및 추가 구성 옵션은 [Azure VM에 대한 임시 OS 디스크를](../virtual-machines/windows/ephemeral-os-disks.md) 참조하십시오. 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>클러스터의 내구성 및 안정성 수준 선택
지속성 계층은 기본 Azure 인프라와 함께 VM에 있는 권한을 이 시스템에 표시하는 데 사용됩니다. 주 노드 유형에서 이 권한을 사용하면 Service Fabric이 시스템 서비스 및 상태 저장 서비스에 대한 쿼럼 요구 사항에 영향을 주는 VM 수준 인프라 요청(VM 다시 부팅, VM 이미지로 다시 설치, 또는 VM 마이그레이션 등)을 일시 중지할 수 있습니다. 주가 아닌 노드 형식에서 이 권한을 사용하면 Service Fabric이 상태 저장 서비스에 대한 쿼럼 요구 사항에 영향을 주는 VM 수준 인프라 요청(예: VM 다시 부팅, VM 이미지로 다시 설치, VM 마이그레이션)을 일시 중지할 수 있습니다.  사용할 수준과 권장 사항의 장점은 [클러스터의 내구성 특성을][durability]참조하십시오.

안정성 계층은 주 노드 유형에서 이 클러스터에서 실행하려는 시스템 서비스의 복제본 수를 설정하는 데 사용됩니다. 복제본 수가 많을수록 클러스터에서 시스템 서비스를 신뢰할 수 있습니다.  사용할 수준과 권장 사항의 이점은 [클러스터의 안정성 특성을][reliability]참조하십시오. 

## <a name="enable-reverse-proxy-andor-dns"></a>역방향 프록시 및/또는 DNS 사용
클러스터의 노드는 동일한 로컬 네트워크에 있으므로 클러스터 내에서 서로 연결하고 있는 서비스는 다른 서비스의 엔드포인트에 직접 액세스할 수 있습니다. 서비스 간 연결을 더 쉽게 하기 위해 Service Fabric은 [DNS 서비스](service-fabric-dnsservice.md) 및 [역방향 프록시 서비스라는](service-fabric-reverseproxy.md)추가 서비스를 제공합니다.  클러스터를 배포할 때 두 서비스를 모두 사용할 수 있습니다.

많은 서비스, 특히 컨테이너화된 서비스는 기존 URL 이름을 가질 수 있기 때문에 명명 서비스 프로토콜이 아닌 표준 DNS 프로토콜을 사용하여 이러한 문제를 해결할 수 있어 특히 응용 프로그램 "리프트 앤 시프트" 시나리오에서 편리합니다. 바로 이것이 DNS 서비스의 용도입니다. DNS 서비스를 통해 DNS 이름을 서비스 이름에 매핑할 수 있으므로 엔드포인트 IP 주소를 확인할 수 있습니다.

역방향 프록시는 HTTPS를 포함하여 HTTP 끝점을 노출하는 클러스터의 서비스를 해결합니다. 역방향 프록시는 특정 URI 형식을 제공하여 다른 서비스 호출을 크게 단순화합니다.  역방향 프록시는 한 서비스가 다른 서비스와 통신하는 데 필요한 확인, 연결 및 재시도 단계를 처리합니다.

## <a name="prepare-for-disaster-recovery"></a>재해 복구 준비
고가용성 전달의 중요한 부분은 서비스가 다른 모든 유형의 오류를 감당할 수 있는지 확인하는 것입니다. 계획되지 않으며 사용자 통제 하에 있지 않은 오류의 경우 특히 이러한 과정이 중요합니다. [재해 복구 준비는](service-fabric-disaster-recovery.md) 올바르게 모델링및 관리되지 않으면 재해가 될 수 있는 몇 가지 일반적인 오류 모드를 설명합니다. 또한 재해가 발생했을 때 취해야 할 완화 및 조치에 대해서도 설명합니다.

## <a name="production-readiness-checklist"></a>프로덕션 준비 검사 목록
애플리케이션 및 클러스터가 프로덕션 트래픽을 허용할 준비가 되었나요? 클러스터를 프로덕션에 배포하기 전에 [프로덕션 준비 검사 목록을](service-fabric-production-readiness-checklist.md)실행합니다. 이 검사 목록의 항목을 통해 작업하여 응용 프로그램과 클러스터를 원활하게 실행합니다. 생산에 들어가기 전에 이러한 모든 항목을 체크 오프하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
* [Windows를 실행하는 서비스 패브릭 클러스터 만들기](service-fabric-best-practices-overview.md)
* [Linux를 실행하는 서비스 패브릭 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster