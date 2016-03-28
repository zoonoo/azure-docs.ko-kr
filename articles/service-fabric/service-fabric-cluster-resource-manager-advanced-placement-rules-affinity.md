<properties
   pageTitle="서비스 패브릭 클러스터 리소스 관리자 - 선호도 | Microsoft Azure"
   description="서비스 패브릭 서비스에 대한 추가 배치 정책 및 규칙 개요"
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
   ms.date="03/10/2016"
   ms.author="masnider"/>

# 서비스 패브릭에서 서비스 선호도 구성 및 사용

선호도는 적어도 얼핏 보기에 마이크로 서비스 환경에는 그다지 적합하지 않은 항목 중 하나입니다. 그리고 마이크로 서비스 환경에서는 그다지 적합하지 않기 때문입니다. 선호도는 주로 이전의 더 큰 모놀리식 응용 프로그램을 클라우드 및 마이크로 서비스 환경으로 쉽게 전환하도록 해주는 컨트롤입니다.

더 큰 앱 또는 마이크로 서비스를 염두에 두지 않고 설계한 앱을 서비스 패브릭으로 가져오는 경우를 가정해봅니다. 이는 실제로 상당히 흔한 경우이며 이러한 상황에 있는 고객(내부 및 외부)이 다수 있었습니다. 먼저 전체 앱을 환경 속에 넣고 패키지하여 실행합니다. 그런 다음 여러 개의 작은 서비스로 나눈 다음 서로 통신할 수 있도록 합니다.

그런 다음 "Oops" 있습니다. "Oops"는 일반적으로 이러한 범주 중 하나에 해당합니다.

1. 모놀리식 앱의 구성 요소 X에는 방금 서비스로 전환되어 클러스터 전반으로 이동된 구성 요소 Y에 대한 문서화되지 않은 종속성이 있는 것으로 나타났습니다. 이제 이것은 끊깁니다.
2.	이들은 (파이프라는 이름의 로컬 항목 | 공유 메모리 | 디스크의 파일)을 통해 통신하지만 개별적으로 업데이트하여 속도를 조금 높일 수 있습니다.
3.	모든 것이 좋지만, 이들 두 구성 요소는 실제 매우 통신이 잦고 성능에 매우 민감한 것으로 나타났으며, 특히 이들을 별도의 서비스로 이동시켰을 때 성능이 나빠졌습니다.

이러한 경우는 모두 리팩터링 작업이 손실되지 않고 모니리식으로 되돌아가지 않길 원하지만, 모든 것이 수정될 때까지 로컬 항목처럼 "느껴지도록" 항목들을 다시 원래대로 모으려고 합니다.

그렇다면 어떻게 해야 할까요? 이제 선호도를 켤 수 있습니다.

## 선호도 작동 원리
선호도를 설정하려면 두 개의 서로 다른 서비스 간의 선호도 관계를 정의합니다. 일반적으로 이를 다른 서비스에 있는 한 서비스를 "가리키고" "이 서비스는 해당 서비스가 실행되고 있는 장소에서만 실행될 수 있습니다."라고 생각할 수 있습니다. 경우에 따라 부모 자식 관계(부모에 있는 자식을 가리키는 경우)를 의미하기도 합니다. 이는 한 서비스의 복제본 또는 인스턴스가 선호도가 높은 서비스의 복제본 또는 인스턴스와 같은 노드에 배치하도록 합니다.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## 서로 다른 선호도 옵션
선호도는 몇 가지 상관 관계 구성표 중 하나를 통해 표시되며, 두 가지 모드가 있습니다. 선호도의 가장 일반적인 모드는 NonAlignedAffinity라고 하는 것입니다. NonAlignedAffinity에서 서로 다른 서비스의 복제본 또는 인스턴스는 같은 노드에 배치됩니다. 다른 모드는 AlignedAffinity입니다. 정렬된 선호도는 상태 저장 서비스와 함께 사용됩니다. 한 상태 저장 서비스의 선호도를 다른 상태 저장 서비스의 선호도와 정렬되도록 구성하면 이들 서비스의 주 복제본은 서로 같은 노드에 배치되고 각 보조 복제본 쌍도 서로 동일한 노드에 배치되도록 해줍니다. 상태 저장 서비스를 위해 NonAlignedAffinity를 구성할 수도 있습니다(덜 일반적임). 이 경우 상태 비저장 서비스와 거의 유사하게 동작합니다. 즉, 두 상태 저장 서비스의 서로 다른 복제본이 동일한 노드에 있을 수 있지만, 주 복제본과 보조 데이터베이스가 동일한 노드에 함께 배치되도록 하지는 않습니다.

![선호도 모드 및 그 영향][Image1]

### 최상의 노력이 필요한 상태
선호도와 모놀리식 아키텍처 간에는 몇 가지 차이점이 있습니다. 두 서비스는 선호도 관계가 최상의 노력이라는 점에서 거의 같지만, 기본적으로 서로 다른 서비스이기 때문에 이들은 각각 실패할 수 있습니다. 또 한 가지 사항으로 용량 제한 등으로 인해 서비스의 서로 다른 복제본이 분리될 수 있습니다.


### 체인 모양과 별 모양의 비교
현재 선호도 관계의 체인을 모델링할 수는 없습니다. 즉, 한 선호도 관계에서 자식인 서비스는 다른 선호도 관계에서 부모가 될 수 없습니다. 이러한 유형의 관계를 모델링하려는 경우, "중간" 자식의 부모에 최하위 자식을 부모로 지정하여 체인 모양보다는 별 모양으로 모델링해야 효과적입니다.

![선호도 관계의 컨텍스트에서 체인 모양과 별 모양의 비교][Image2]

또 다른 사항으로 현재의 선호도 관계에는 방향성이 있다는 점입니다. 이것은 미세하지만 효과적입니다. 즉, "선호도" 규칙은 부모가 있는 곳에만 자식이 있도록 합니다. 부모가 갑자기 다른 노드에 대한 장애 조치를 수행해야 하는 경우(또는 부모만 이동시키는 기타 제한된 작업) 리소스 관리자는 자식이 부모와 함께 있지 않다는 것을 알 때까지 어떤 문제가 있다고 생각하지 않습니다. 관계는 즉시 적용되지 않기 때문입니다.

### 분할 지원
마지막으로 선호도에 대해 알아두어야 할 사항으로 선호도 관계는 부모가 분할된 곳에서는 지원되지 않습니다. 이는 나중에 지원하겠지만, 지금은 허용되지 않습니다.

## 다음 단계
- 서비스 구성에 사용할 수 있는 기타 옵션에 대한 자세한 내용은 [서비스 구성에 대해 알아보기](service-fabric-cluster-resource-manager-configure-services.md)에서 다른 클러스터 리소스 관리자 구성에 대한 항목을 확인하세요.

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!---HONumber=AcomDC_0316_2016-->