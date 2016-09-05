<properties
   pageTitle="서비스 패브릭 클러스터 리소스 관리자 - 선호도 | Microsoft Azure"
   description="서비스 패브릭 서비스에 대한 선호도 구성의 개요"
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

# 서비스 패브릭에서 서비스 선호도 구성 및 사용

선호도는 주로 더 큰 모놀리식 응용 프로그램을 클라우드 및 마이크로 서비스 환경으로 쉽게 전환하도록 해주는 컨트롤입니다. 부작용이 있더라도 서비스 성능 향상을 위한 합법적인 최적화로 특정 경우에 사용될 수도 있습니다.

더 큰 앱 또는 마이크로 서비스를 염두에 두지 않고 설계한 앱을 서비스 패브릭으로 가져오는 경우를 가정해봅니다. 이러한 전환은 실제로는 흔하게 나타나며 이러한 상황에 있는 고객(내부 및 외부)이 다수 있었습니다. 먼저 전체 앱을 환경 속에 넣고 패키지하여 실행합니다. 그런 다음 여러 개의 작은 서비스로 나눈 다음 서로 통신할 수 있도록 합니다.

그러면 "Oops..."가 나타납니다. "Oops"는 일반적으로 이러한 범주 중 하나에 해당합니다.

1. 모놀리식 앱의 구성 요소 X는 구성 요소 Y에 대해 문서화되지 않은 종속성을 가지며, 이러한 구성 요소들을 별도의 서비스로 전환했습니다. 현재 이러한 구성 요소들은 클러스터의 다른 노드에서 실행되고 있으므로 분리된 상태입니다.
2.	이들은 (파이프라는 이름의 로컬 항목 | 공유 메모리 | 디스크의 파일)을 통해 통신하지만 개별적으로 업데이트하여 속도를 조금 높일 수 있습니다. 나중에 강한 종속성을 제거하겠습니다.
3.	모든 것이 좋지만, 이들 두 구성 요소는 실제 매우 통신이 잦고 성능에 매우 민감한 것으로 나타났습니다. 이들을 별도의 서비스로 이동시켰을 때 응용 프로그램의 전반적인 성능이 나빠지거나 대기 시간이 늘어났습니다. 결과적으로, 전체 응용 프로그램은 기대를 충족하지 못하게 됩니다.

이러한 경우에 리팩터링 작업을 손실하는 것과 모놀리식으로 되돌아가는 것은 좋지 않지만, 어느 정도의 지역성은 필요합니다. 자연스럽게 서비스로 작동하도록 구성 요소를 다시 디자인할 수 있거나, 가능한 경우 다른 방법으로 성능 기대치를 해결할 수 있게 될 때까지 이러한 상황은 계속 유지됩니다.

그렇다면 어떻게 해야 할까요? 이제 선호도를 켤 수 있습니다.

## 선호도를 구성하는 방법
선호도를 설정하려면 두 개의 서로 다른 서비스 간의 선호도 관계를 정의합니다. 선호도를 다른 서비스에 있는 한 서비스를 "가리키고" "이 서비스는 해당 서비스가 실행되고 있는 장소에서만 실행될 수 있습니다."라고 생각할 수 있습니다. 경우에 따라 부모 자식 관계(부모에 있는 자식을 가리키는 경우)를 의미하기도 합니다. 선호도는 한 서비스의 복제본 또는 인스턴스가 서비스의 복제본 또는 인스턴스와 같은 노드에 배치되도록 합니다.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## 서로 다른 선호도 옵션
선호도는 몇 가지 상관 관계 구성표 중 하나를 통해 표시되며, 두 가지 모드가 있습니다. 선호도의 가장 일반적인 모드는 NonAlignedAffinity라고 하는 것입니다. NonAlignedAffinity에서 서로 다른 서비스의 복제본 또는 인스턴스는 같은 노드에 배치됩니다. 다른 모드는 AlignedAffinity입니다. 정렬된 선호도는 상태 저장 서비스에서만 유용합니다. 선호도가 정렬된 두 개의 상태 저장 서비스를 구성하면 이러한 서비스의 주 복제본이 서로 동일한 노드에 배치될 수 있습니다. 또한 해당 서비스에 대한 각 보조 복제본 쌍이 동일한 노드에 배치됩니다. 상태 저장 서비스를 위해 NonAlignedAffinity를 구성할 수도 있습니다(덜 일반적임). NonAlignedAffinity의 경우 두 상태 저장 서비스의 서로 다른 복제본이 같은 노드에 배치되지만 주 복제본 또는 보조 복제본을 정렬하려고 시도되지는 않습니다.

![선호도 모드 및 그 영향][Image1]

### 최상의 노력이 필요한 상태
선호도와 모놀리식 아키텍처 간에는 몇 가지 차이점이 있습니다. 그 중 대부분은 선호도 관계를 위해 최상의 노력이 필요하기 때문에 발생합니다. 선호도 관계의 서비스는 실패할 수 있으며 독립적으로 이동할 수 있는 기본적으로 다른 엔터티입니다. 선호도 관계가 깨질 수 있는 이유도 있습니다. 예를 들어 선호도 관계의 일부 서비스 개체만 지정된 노드에 맞을 수 있는 용량 제한 문제도 원인이 됩니다. 이러한 경우 선호도 관계가 준비되었더라도 다른 제약 조건으로 인해 적용될 수 없습니다. 나중에 다른 모든 제약 조건 및 선호도를 적용할 수 있는 경우 선호도 제약 위반 문제는 자동으로 해결됩니다.

### 체인 모양과 별 모양의 비교
현재 선호도 관계의 체인을 모델링할 수는 없습니다. 즉, 한 선호도 관계에서 자식인 서비스는 다른 선호도 관계에서 부모가 될 수 없습니다. 이러한 형식의 관계를 모델링하려는 경우 체인이 아닌 별 모양으로 효과적으로 모델링해야 합니다. 이 작업을 수행하기 위해 최하위 자식은 "중간" 자식 부모의 부모가 될 수 있습니다. 서비스의 정렬에 따라 여러 자식 요소에 대한 부모로 제공하기 위해 "자리 표시자" 서비스를 만들어야 할 수 있습니다.

![선호도 관계의 컨텍스트에서 체인 모양과 별 모양의 비교][Image2]

또 다른 사항으로 현재의 선호도 관계에는 방향성이 있다는 점입니다. 즉, "선호도" 규칙은 자식이 부모가 있는 위치에 있도록 합니다. 예를 들어 부모가 갑자기 장애 조치(failover)되면 클러스터 Resource Manager는 자식이 부모와 함께 있지 않다는 사실을 알게 될 때까지 실제로 잘못된 부분이 있다고 생각하지 않습니다. 이러한 관계는 즉시 적용되지 않게 됩니다.

### 분할 지원
마지막으로 선호도에 대해 알아두어야 할 사항으로 선호도 관계는 부모가 분할된 곳에서는 지원되지 않습니다. 이는 나중에 지원하겠지만, 지금은 허용되지 않습니다.

## 다음 단계
- 서비스 구성에 사용할 수 있는 기타 옵션에 대한 자세한 내용은 [서비스 구성에 대해 알아보기](service-fabric-cluster-resource-manager-configure-services.md)에서 다른 클러스터 Resource Manager 구성에 대한 항목을 확인하세요.
- 사람들이 서비스를 작은 집합의 컴퓨터로 제한하고 서비스 컬렉션의 부하를 집계하려는 것과 같은 선호도를 사용하려는 많은 이유는 응용 프로그램 그룹을 통해 더 잘 지원되기 때문입니다. [응용 프로그램 그룹](service-fabric-cluster-resource-manager-application-groups.md) 확인

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!---HONumber=AcomDC_0824_2016-->