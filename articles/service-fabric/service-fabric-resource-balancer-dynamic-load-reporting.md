<properties
   pageTitle="동적 부하 보고 | Microsoft Azure"
   description="리소스 분산 장치에 동적 부하 보고에 대한 개요"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# 동적 부하 보고 개요

런타임 중 상태 저장 및 상태 비저장 서비스 개체는 ReportLoad 메서드(IStatefulServicePartition 및 IStatelessServicePartition 인터페이스의 멤버)를 통해 부하를 보고할 수 있습니다. 런타임 부하 값을 보고하는 것은 중요합니다. 노드의 서비스에서 부하가 발생할 때, 노드에 대해 서비스의 정확한 압축을 사용하고 Azure 서비스 패브릭에서 중앙 리소스 분산 장치가 리소스 사용률을 정확하게 추적하는지 확인합니다.

복제본이 부하를 보고할 때 먼저 다른 부하 보고서를 로컬에서 일괄 처리한 다음 단일 보고서가 리소스 분산 장치로 전송됩니다. 이 프로세스는 서비스가 반복적으로 그리고 매우 빠르게 부하를 보고하는 경우 마지막 보고서만 실제로 리소스 분산 장치로 전송됨을 의미합니다.

서비스 패브릭 리소스 분산 장치가 실행되면 모든 노드로부터 집계되는 부하 정보를 모두 검토하고 일부는 검사합니다. 이러한 검사에는 클러스터 매니페스트에 정의된 대로 메트릭에 대한 분산 임계값과 활동 임계값이 포함됩니다. 이들 검사는 클러스터가 리소스 분산 장치를 실행하기 충분할 정도로 불균형 상태인지 그리고 특정 노드가 정의된 용량을 가진 메트릭의 용량을 초과하는지 판단합니다. 초과 용량의 경우 리소스 분산 장치는 먼저 초과 용량의 메트릭을 공유하는 노드 또는 용량 초과 노드의 서비스만 고려합니다. 클러스터 불균형의 경우 리소스 분산 장치는 메트릭에 의해 불균형 메트릭을 보고하는 서비스와 관련된 모든 서비스를 고려합니다. 리소스 분산 장치가 이러한 상황이 발생했는지 판단하는 경우 더 나은 서비스 배열을 찾기 위해 시뮬레이션을 실행합니다.

부하가 변경될 때마다 서비스에서 부하를 보고합니다. 자체적으로 부하 보고를 집계하거나 다듬으면 안 됩니다.

서비스에서 부하를 보고할 때 해당 부하 보고서는 서비스가 만들어질 때 정의된 주 및 보조 부하 값을 대체합니다. 이러한 값은 서비스 패브릭 리소스 분산 장치가 분산 또는 배치에 대한 결정을 내릴 때 사용하는 새 부하 값이 됩니다.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

자세한 내용은 [리소스 분산 장치 아키텍처](service-fabric-resource-balancer-architecture.md)를 참조하세요.

<!---HONumber=AcomDC_1223_2015-->