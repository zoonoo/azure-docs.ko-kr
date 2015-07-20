<properties
   pageTitle="노드 버퍼 백분율"
   description="리소스 분산 장치에서 노드 버퍼 백분율 역할에 대한 개요"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="abhic"/>

# 노드 버퍼 백분율 개요

현재, 고객은 리소스 분산 장치가 제약 조건 우선순위를 따르는 제약 조건으로서 노드 용량 제한을 지정할 수 있습니다. 용량 제약 조건 우선순위가 높은 경우(노드 용량을 초과할 수 없음)와 클러스터 노드를 자주 사용하는 경우, 장애 조치가 즉시 이루어지지 않거나 해당 노드 용량을 초과할 수 있습니다.

주 복제본을 가진 노드의 용량이 감소하고 보조 복제본을 가진 노드가 노드 용량 제한에 거의 도달할 경우 문제를 검색합니다. 이 경우, 기본 부하가 보조 부하보다 크면, 노드를 오버 커밋하거나 복제본을 복사하지 않고는 보조 복제본을 즉시 승격시킬 수 없습니다.

사전 압축 논리를 실행하면 클러스터 노드 수가 높을수록 노드 용량 제한에 도달하게 됩니다. 노드 버퍼 백분율은 고객이 자유롭게 유지해야할 노즈의 비율을 지정하도록 함으로써 장애 조치 시간 증가나 노드 오버 커밋을 방지하는 기능입니다. 새 서비스의 복제본은 노드 버퍼 공간에 추가될 수 없지만 리소스 분산 장치는 장애 조치 및 누락된 복제본 추가를 위해 총 노드 용량(회계 버퍼 공간)를 사용할 수 있어야 합니다.

이 기능은 사전 메트릭 압축 기능이 꺼졌을 때에도 사용할 수 있습니다.

이 코드 예에서는 클러스터 매니페스트 내 FabricSettings 요소를 통해 메트릭당 구성되는 메트릭에 대한 노드 버퍼 백분율을 보여줍니다.

``` xml
<FabricSettings>
  <Section Name=" NodeBufferPercentage">
    <Parameter Name="MetricName" Value="0.1"/>
  </Section>
</FabricSettings>

```

"MetricName"라는 메트릭의 0.1 값은메트릭 "MetricName"의 모든 노드 용량의 10%가 자유로운 상태로 유지되어야 함을 의미합니다.

이 섹션에서 값을 지정하지 않으면, 0이 기본값으로 사용됩니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

자세한 내용: [리소스 분산 아키텍처](service-fabric-resource-balancer-architecture.md)
 

<!---HONumber=July15_HO2-->