---
title: "Service Fabric 클러스터 리소스 관리자 - 응용 프로그램 그룹 | Microsoft Docs"
description: "서비스 패브릭 클러스터 리소스 관리자에서 응용 프로그램 그룹 기능 개요"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: eef9cbca0479801cbac53ae1ae5713d4dac6a4bf
ms.openlocfilehash: 57525cd41d518b12f3f465d5dea7247659e3c8b6


---
# <a name="introduction-to-application-groups"></a>응용 프로그램 그룹 소개
Service Fabric의 Cluster Resource Manager는 일반적으로 부하([메트릭](service-fabric-cluster-resource-manager-metrics.md)을 통해 표시됨)를 클러스터 전체에 균등하게 분산하여 클러스터 리소스를 관리합니다. 또한 Service Fabric은 클러스터에서 노드의 용량과 [용량](service-fabric-cluster-resource-manager-cluster-description.md)의 개념을 통해 전체적으로 클러스터를 관리합니다. 메트릭과 용량은 다양한 워크로드에 잘 적용되지만 서로 다른 Service Fabric 응용 프로그램 인스턴스를 과도하게 사용하는 패턴은 때때로 추가 요구 사항을 가져옵니다. 몇 가지 추가 요구 사항은 일반적으로 다음과 같습니다.

* 클러스터에서 응용 프로그램 인스턴스의 서비스에 대한 용량을 예약하는 기능
* 응용 프로그램 내에서 서비스가 실행되는 노드의 총수를 제한하는 기능
* 내부 서비스의 수 또는 전체 리소스 소비량을 제한하기 위해 응용 프로그램 인스턴스 자체에서 용량 정의

이러한 요구 사항을 충족하기 위해 Service Fabric Cluster Resource Manager는 응용 프로그램 그룹을 지원합니다.

## <a name="managing-application-capacity"></a>응용 프로그램 용량 관리
응용 프로그램에 의해 확장되는 노드 수를 제한하는 데 응용 프로그램 용량을 사용할 수 있습니다. 응용 프로그램 서비스의 메트릭 부하를 개별 노드 및 총합에서 제한할 수도 있습니다. 응용 프로그램에 대한 클러스터의 리소스를 예약하는 데도 사용할 수 있습니다.

생성될 때 새 응용 프로그램에 대해 응용 프로그램 용량을 설정하거나 기존 응용 프로그램에 대해 업데이트할 수도 있습니다.

### <a name="limiting-the-maximum-number-of-nodes"></a>노드의 최대 수 제한
응용 프로그램 용량에 대한 가장 간단한 사용 사례는 응용 프로그램 인스턴스화를 특정 최대 노드 수로 제한해야 하는 경우입니다. 응용 프로그램 용량이 지정된 경우 Service Fabric Cluster Resource Manager는 일반 규칙(균형 조정 또는 조각 모음)에 따라 서비스를 만들고 배치합니다.

다음 이미지에서는 최대 노드 수가 정의되거나 정의되지 않은 응용 프로그램 인스턴스를 보여 줍니다. 특정 서비스의 복제본 또는 인스턴스가 함께 배치되거나 특정 노드가 사용된다는 보장이 없습니다.

<center>
![최대 노드 수를 정의하는 응용 프로그램 인스턴스][Image1]
</center>

왼쪽 예에서 응용 프로그램에는 설정된 응용 프로그램 용량이 없으며 세 가지 서비스가 있습니다. Cluster Resource Manager는 클러스터에서 최상의 균형을 달성하기 위해&6;개의 사용 가능한 노드에 모든 복제본을 분산했습니다. 오른쪽 예제에서는 동일한 응용 프로그램이&3;개의 노드로 제한된 것을 확인할 수 있습니다.

이 동작을 제어하는 매개 변수를 MaximumNodes라고 합니다. 이 매개 변수는 응용 프로그램을 만드는 동안 설정되거나 이미 실행 중인 응용 프로그램 인스턴스에 대해 업데이트될 수 있습니다.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>응용 프로그램 메트릭, 부하 및 용량
응용 프로그램 그룹을 사용하면 지정된 응용 프로그램 인스턴스와 연결된 메트릭 및 이러한 메트릭에 대한 응용 프로그램의 용량을 정의할 수 있습니다. 그러면 해당 응용 프로그램 인스턴스 내에서 서비스의 리소스 사용을 추적하고 예약하며 제한할 수 있습니다.

각 응용 프로그램 메트릭에서 두 개의 값을 다음과 같이 설정할 수 있습니다.

* **총 응용 프로그램 용량** - 이 설정은 특정 메트릭에 대한 응용 프로그램의 총 용량을 나타냅니다. Cluster Resource Manager를 사용하면 이 값을 초과하는 총 부하를 발생시키는 이 응용 프로그램 인스턴스 내에서 새 서비스를 만들 수 없습니다. 예를 들어, 응용 프로그램 인스턴스에 10이라는 용량이 있었고 이미 5라는 부하가 있다고 가정해보겠습니다. 이 경우에 10이라는 기본 총 부하를 가진 서비스를 만들도록 허용하지 않습니다.
* **노드 최대 용량** – 이 설정은 단일 노드의 응용 프로그램 내에서 서비스의 복제본에 대한 최대 총 부하를 지정합니다. 노드의 총 부하가 이 용량을 초과하면 Cluster Resource Manager는 용량 제한이 적용되도록 복제본을 다른 노드로 이동하려고 합니다.

## <a name="reserving-capacity"></a>용량 예약
응용 프로그램 그룹의 또 다른 일반적인 용도는 클러스터 내의 리소스가 지정된 응용 프로그램 인스턴스에 대해 예약되었는지 확인하는 것입니다. 응용 프로그램 인스턴스에 아직 서비스가 없거나 응용 프로그램 인스턴스가 리소스를 아직 사용하지 않은 경우에도 발생할 수 있습니다. 작동하는 방법을 살펴보겠습니다.

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>노드 및 리소스 예약의 최소 수 지정
응용 프로그램 인스턴스에 대해 리소스를 예약하려면 추가 매개 변수 *MinimumNodes* 및 *NodeReservationCapacity*를 지정해야 합니다.

* **MinimumNodes** - 응용 프로그램 내의 서비스에서 실행할 수 있는 노드 최대 수를 지정하는 것처럼 최소 수를 지정할 수도 있습니다. 이 설정은 응용 프로그램 인스턴스 만들기의 일부로 클러스터 내에서 용량을 보장하는 리소스가 예약되어야 하는 노드의 수를 정의합니다.
* **NodeReservationCapacity** - 응용 프로그램 내의 각 메트릭에 대해 NodeReservationCapacity를 정의할 수 있습니다. 이 설정은 서비스의 복제본 또는 인스턴스가 배치되는 모든 노드에서 응용 프로그램에 대해 예약된 메트릭 부하의 양을 정의합니다.

용량 예약의 예를 살펴보겠습니다.

<center>
![예약된 용량을 정의하는 응용 프로그램 인스턴스][Image2]
</center>

왼쪽 예에서 응용 프로그램은 정의된 응용 프로그램 용량이 없습니다. Cluster Resource Manager는 기본 규칙에 따라 모든 항목의 균형을 유지합니다.

오른쪽의 예제에서는 응용 프로그램을 다음 설정으로 만들었다고 가정하겠습니다.

* MinimumNodes&2;로 설정
* MaximumNodes&3;으로 설정
* 다음으로 정의된 응용 프로그램 메트릭
  * 20인 NodeReservationCapacity
  * 50인 MaximumNodeCapacity
  * 100인 TotalApplicationCapacity

Service Fabric은 블루 응용 프로그램에 대한 두 노드에서 용량을 예약하고 클러스터의 다른 응용 프로그램 인스턴스에서 서비스가 해당 용량을 소비하도록 허용하지 않습니다. 이 예약된 응용 프로그램 용량은 해당 노드 및 클러스터 내에서 남은 용량으로 소비되고 계산될 것으로 간주됩니다.

응용 프로그램이 예약을 사용하여 만들어진 경우 Cluster Resource Manager 용량은 MinimumNodes * NodeReservationCapacity(각 메트릭의 경우)와 동일합니다. 그러나 복제본이 하나 이상 배치되는 경우에만 용량이 특정 노드에서 예약됩니다. 이후 예약을 사용하면 리소스가 노드에서 필요할 때만 예약되므로 유연성과 리소스 사용률을 개선할 수 있습니다.

## <a name="obtaining-the-application-load-information"></a>응용 프로그램 부하 정보 얻기
응용 프로그램 용량이 정의된 각 응용 프로그램의 경우 해당 서비스의 복제본에서 보고된 집계 부하에 대한 정보를 얻을 수 있습니다.

예를 들어 다음 PowerShell cmdlet을 사용하여 부하를 검색할 수 있습니다.

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

ApplicationLoad 쿼리는 응용 프로그램에 대해 지정된 응용 프로그램 성능에 대한 기본 정보를 반환합니다. 이 정보에는 최소 노드와 최대 노드 정보 및 응용 프로그램이 현재 사용하고 있는 노드 수가 포함됩니다. 다음을 비롯한 각 응용 프로그램 부하 메트릭에 대한 정보도 포함합니다.

* 메트릭 이름: 메트릭의 이름.
* 예약 용량: 이 응용 프로그램에 대한 클러스터에 예약된 클러스터 용량.
* 응용 프로그램 부하: 이 응용 프로그램 자식 복제본의 총 부하.
* 응용 프로그램 용량: 응용 프로그램 부하의 허용되는 최대값.

## <a name="removing-application-capacity"></a>응용 프로그램 용량 삭제
응용 프로그램 용량 매개 변수가 응용 프로그램에 대해 설정되면 업데이트 응용 프로그램 API 또는 PowerShell cmdlet을 사용하여 제거할 수 있습니다. 예:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

이 명령은 응용 프로그램에서 모든 응용 프로그램 용량 매개 변수를 제거합니다. 명령은 즉시 적용됩니다. 이 명령이 완료되면 Cluster Resource Manager는 응용 프로그램을 관리하는 기본 동작을 사용하도록 되돌립니다. Update-ServiceFabricApplication을 통해 응용 프로그램 용량 매개 변수를 다시 지정할 수 있습니다.

### <a name="restrictions-on-application-capacity"></a>응용 프로그램 용량에 대한 제한 사항
고려해야 할 응용 프로그램 용량 매개 변수에 대한 몇 가지 제한 사항이 있습니다. 유효성 검사 오류가 발생하면 응용 프로그램의 만들기 또는 업데이트는 오류와 함께 거부되고 변경되지 않습니다.

* 모든 정수 매개 변수는 음수가 아닌 숫자여야 합니다.
* MinimumNodes는 MaximumNodes보다 클 수 없습니다.
* 부하 메트릭의 용량을 정의한 경우 다음 규칙을 따라야 합니다.
  * 노드 예약 용량은 최대 노드 용량보다 크지 않아야 합니다. 예를 들어, 노드의 메트릭 "CPU"의 용량을&2;단위로 제한하고 각 노드에&3;단위를 예약하려고 시도할 수 없습니다.
  * MaximumNodes를 지정하는 경우 MaximumNodes의 제품 및 최대 노드 용량은 총 응용 프로그램 용량보다 크지 않아야 합니다. 예를 들어, 부하 메트릭 "CPU"의 최대 노드 용량을&8;로 설정했다고 가정하겠습니다. 또한 최대 노드를&10;으로 설정했다고 가정하겠습니다. 이 경우에 이 부하 메트릭에 대해 응용 프로그램 총용량은 80보다 커야 합니다.

제한 사항은 응용 프로그램 생성 중(클라이언트 쪽) 및 응용 프로그램 업데이트 중(서버 쪽) 모두에 적용됩니다.

## <a name="how-not-to-use-application-capacity"></a>응용 프로그램 용량을 사용하지 않는 방법
* 응용 프로그램 그룹 기능을 사용하여 응용 프로그램을 노드의 _특정_ 하위 집합으로 제한하지 않습니다. 즉, 응용 프로그램을 최대 다섯 개의 노드에서 실행하도록 지정할 수 있지만 클러스터에서 어떤 특정 다섯 개의 노드인지 지정할 수 없습니다. 서비스에 대한 배치 제약 조건을 사용하여 응용 프로그램 특정 노드로 제한할 수 있습니다.
* 동일한 응용 프로그램의 두 서비스가 서로 나란히 배치될 수 있도록 응용 프로그램 용량을 사용하려고 하지 않습니다. 특정 요구 사항에 따라 선호도 또는 배치 제약 조건을 사용하여 서비스가 동일한 노드에서 실행되도록 할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 서비스 구성에 사용할 수 있는 기타 옵션에 대한 자세한 내용은 [서비스 구성에 대해 알아보기](service-fabric-cluster-resource-manager-configure-services.md)에서 사용할 수 있는 기타 Cluster Resource Manager 구성에 대한 항목을 확인하세요.
* 클러스터 Resource Manager가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)
* 처음부터 시작 및 [서비스 패브릭 클러스터 리소스 관리자 소개](service-fabric-cluster-resource-manager-introduction.md)
* 메트릭이 일반적으로 작동하는 방식에 대한 자세한 내용은 [서비스 패브릭 부하 메트릭](service-fabric-cluster-resource-manager-metrics.md)
* Cluster Resource Manager에는 클러스터를 설명하기 위한 많은 옵션이 있습니다. 이에 대해 자세히 알아보려면 [Service Fabric 클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md)에 대한 문서를 확인하세요.

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png



<!--HONumber=Jan17_HO1-->


