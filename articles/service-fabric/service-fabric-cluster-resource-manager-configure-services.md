<properties
   pageTitle="서비스 패브릭 클러스터 리소스 관리자를 사용하여 서비스 구성 | Microsoft Azure"
   description="메트릭, 배치 제약 조건 및 기타 배치 정책을 지정하여 서비스 패브릭 서비스를 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>


# 서비스 패브릭 서비스에 대한 클러스터 리소스 관리자 설정 구성
Service Fabric Cluster Resource Manager를 사용하면 개별적으로 명명된 모든 서비스를 제어하는 규칙을 매우 정밀하게 제어할 수 있습니다. 각 명명된 서비스 인스턴스는 클러스터에 할당되는 방법에 대한 특정 규칙을 지정할 수 있으며 해당 서비스에 대한 중요성을 포함하여 보고할 메트릭 집합을 정의할 수 있습니다. 일반적으로 서비스 구성은 세 가지 작업으로 구분됩니다.

1. 배치 제약 조건 구성
2. 메트릭 구성
3. 고급 배치 정책 구성(덜 일반적임)

각각에 대해 차례로 살펴보겠습니다.

## 배치 제약 조건
배치 제약 조건은 클러스터에서 서비스가 실제로 실행될 수 있는 노드를 제어하는 데 사용됩니다. 일반적으로 명명된 특정 서비스 인스턴스 또는 특정 노드 형식에서 실행되도록 제약된 지정된 형식의 모든 서비스를 볼 수 있습니다. 즉, 배치 제약 조건은 확장 가능하므로 노드 형식에 따라 속성 집합을 정의한 다음, 서비스가 생성될 때 제약 조건을 사용해서 선택할 수 있습니다. 배치 제약 조건은 서비스가 진행되는 동안 동적으로 업데이트할 수 있으므로 클러스터의 변화에 응답할 수 있습니다. 지정된 노드의 속성을 클러스터에서 동적으로 업데이트할 수도 있습니다. 배치 제약 조건 및 구성 방법에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties)에서 찾을 수 있습니다.

## 메트릭
메트릭은 명명된 특정 서비스 인스턴스에 필요한 리소스 집합으로, 해당 서비스의 각 상태 저장 복제본 또는 상태 비저장 인스턴스가 기본적으로 소비하는 해당 리소스 양에 대한 정보를 포함합니다. 또한 메트릭에는 조정이 필요한 경우 서비스에 대한 해당 메트릭의 부하 분산 중요도를 나타내는 가중치도 포함됩니다.

## 기타 배치 규칙
주로 지리적으로 분산된 클러스터 또는 덜 일반적인 시나리오에서 유용한 몇 가지 배치 규칙이 있습니다. 이러한 배치 규칙은 상관 관계 또는 정책을 통해 구성됩니다. 많은 시나리오에서 사용되지는 않지만 완벽을 기하기 위해 설명하겠습니다.

## 다음 단계
- 메트릭은 서비스 패브릭 클러스터 리소스 관리자가 클러스터의 소비와 용량을 관리하는 방법입니다. 메트릭 및 구성 방법에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)를 확인하세요.
- 선호도는 서비스에 대해 구성할 수 있는 하나의 모드입니다. 일반적이지 않지만 필요한 경우 [여기](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)에서 알아볼 수 있습니다.
- 추가 시나리오를 처리하기 위해 서비스에 구성할 수 있는 다양한 배치 규칙이 있습니다. 이러한 기타 배치 정책은 [여기](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)에서 알아볼 수 있습니다.
- 처음부터 시작 및 [서비스 패브릭 클러스터 리소스 관리자 소개](service-fabric-cluster-resource-manager-introduction.md)
- 클러스터 리소스 관리자가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)에 대한 문서를 확인하세요.
- 클러스터 리소스 관리자에는 클러스터를 설명하기 위한 많은 옵션이 있습니다. 이에 대해 자세히 알아보려면 [서비스 패브릭 클러스터를 설명](service-fabric-cluster-resource-manager-cluster-description.md)하는 이 문서를 확인하세요.

<!---HONumber=AcomDC_0824_2016-->