<properties
   pageTitle="서비스 패브릭 클러스터 리소스 관리자 - 응용 프로그램 그룹 | Microsoft Azure"
   description="서비스 패브릭 클러스터 리소스 관리자에서 응용 프로그램 그룹 기능 개요"
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

# 응용 프로그램 그룹 소개
서비스 패브릭의 클러스터 리소스 관리자는 일반적으로 부하(메트릭을 통해 표시됨)를 클러스터 전체에 균등하게 분산하여 클러스터 리소스를 관리합니다. 또한 서비스 패브릭은 클러스터에서 노드의 용량과 용량의 개념을 통해 전체적으로 클러스터를 관리합니다. 이는 다양한 유형의 작업에 잘 적용되지만 서로 다른 서비스 패브릭 응용 프로그램 인스턴스를 과도하게 사용하는 패턴은 때때로 추가 요구 사항을 가져옵니다. 몇 가지 추가 요구 사항은 일반적으로 다음과 같습니다.

- 일부 노드 수의 응용 프로그램 인스턴스 서비스에 대한 용량을 예약하는 기능
- 응용 프로그램 내에서 서비스의 지정된 집합이 실행되도록 허용되는 노드의 총 수를 제한하는 기능
- 내부 서비스의 수 또는 전체 리소스 소비량을 제한하기 위해 응용 프로그램 인스턴스 자체에서 용량 정의

이러한 요구 사항을 충족하기 위해 이른바 응용 프로그램 그룹에 대한 지원을 개발했습니다.

## 응용 프로그램 용량 관리
응용 프로그램에 의해 확장되는 노드의 수 및 개별 노드의 응용 프로그램 인스턴스의 총 메트릭 부하를 제한하는 데 응용 프로그램 용량을 사용할 수 있습니다. 응용 프로그램에 대한 클러스터의 리소스를 예약하는 데도 사용할 수 있습니다.

생성될 때 새 응용 프로그램에 대해 응용 프로그램 용량을 설정할 수 있습니다. 응용 프로그램 용량을 지정하지 않고 생성된 기존 응용 프로그램에 대해 업데이트할 수도 있습니다.

### 노드의 최대 수 제한
응용 프로그램 용량에 대한 가장 간단한 사용 사례는 응용 프로그램 인스턴스화를 특정 최대 노드 수로 제한해야 하는 경우입니다. 응용 프로그램 용량이 지정되지 않은 경우 서비스 패브릭 클러스터 리소스 관리자는 일반 규칙(분산 또는 조각 모음)에 따라 복제본을 인스턴스화합니다. 즉, 일반적으로 해당 서비스는 클러스터에서 사용할 수 있는 모든 노드에 분산되거나 조각 모음이 설정된 경우 일부는 임의이지만 더 작은 수의 노드에서 분산됩니다.

다음 이미지는 정의된 노드의 최대 수 없이 응용 프로그램 인스턴스의 잠재적 배치를 표시한 다음 설정된 최대 노드 수로 동일한 응용 프로그램을 표시합니다. 서비스의 복제본 또는 인스턴스가 함께 배치된다는 보장이 없습니다.

![최대 노드 수를 정의하는 응용 프로그램 인스턴스][Image1]

왼쪽 예에서 응용 프로그램에는 설정된 응용 프로그램 용량이 없으며 세 가지 서비스가 있습니다. CRM은 클러스터에서 최상의 균형을 달성하기 위해 6개의 사용 가능한 노드에 모든 복제본을 분산하도록 논리적 결정을 했습니다. 오른쪽 예제에서는 세 개의 노드에서 제한되는 동일한 응용 프로그램이 표시되고 여기에서 서비스 패브릭 CRM은 응용 프로그램 서비스의 복제본에 대한 최상의 균형을 달성했습니다.

이 동작을 제어하는 매개 변수를 MaximumNodes라고 합니다. 이 매개 변수는 응용 프로그램을 만드는 동안 설정되거나 이미 실행 중인 응용 프로그램 인스턴스에 대해 업데이트될 수 있습니다. 이 경우 서비스 패브릭 CRM은 응용 프로그램 서비스의 복제본을 정의된 최대 노드 수로 제한합니다.

Powershell

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

## 응용 프로그램 메트릭, 부하 및 용량
응용 프로그램 그룹을 사용하여 지정된 응용 프로그램 인스턴스와 연결된 메트릭 뿐만 아니라 이러한 메트릭과 관련하여 응용 프로그램의 용량도 정의할 수 있습니다. 예를 들어 원하는 만큼의 서비스를 만들 수 있도록 지정할 수 있습니다.

각 메트릭의 경우 해당 응용 프로그램 인스턴스에 대한 용량을 설명하기 위해 설정할 수 있는 두 가지 값이 있습니다.

-	총 응용 프로그램 용량 - 특정 메트릭에 대한 응용 프로그램의 총 용량을 나타냅니다. 서비스 패브릭 CRM은 지정된 값으로 이 응용 프로그램 서비스의 메트릭 부하의 합계를 제한하려고 합니다. 또한 응용 프로그램의 서비스가 이미 이 제한까지 부하를 소비한 경우 서비스 패브릭 클러스터 리소스 관리자는 총 부하가 이 제한을 초과하게 되는 모든 새로운 서비스 또는 파티션의 생성을 허용하지 않습니다.
-	최대 노드 용량 – 단일 노드에서 응용 프로그램 서비스의 복제본에 대한 최대 총 부하를 지정합니다. 노드의 총 부하가 이 용량을 초과하면 서비스 패브릭 CRM은 용량 제한이 적용되도록 복제본을 다른 노드로 이동하려고 합니다.

## 용량 예약
응용 프로그램 그룹에 대한 또 다른 일반적인 용도는 응용 프로그램 인스턴스에 서비스가 아직 없는 경우 또는 리소스를 아직 사용하지 않은 경우에도 클러스터 내의 리소스가 지정된 응용 프로그램 인스턴스에 대해 예약되어 있음을 확인하는 것입니다. 작동하는 방법을 살펴보겠습니다.

### 노드 및 리소스 예약의 최소 수 지정
응용 프로그램 인스턴스에 대해 리소스를 예약하려면 추가 매개 변수 *MinimumNodes* 및 *NodeReservationCapacity*를 지정해야 합니다.

- MinimumNodes - 응용 프로그램 내의 서비스에서 실행할 수 있는 대상 최대 노드 수를 지정하는 것처럼 응용 프로그램에서 실행해야 할 최소 노드 수를 지정할 수도 있습니다. 이 설정은 응용 프로그램 인스턴스 만들기의 일부로 클러스터 내에서 용량을 보장하는 리소스가 최소한으로 예약되어야 하는 노드의 수를 효과적으로 정의합니다.
- NodeReservationCapacity - 응용 프로그램 내의 각 메트릭에 대해 NodeReservationCapacity를 정의할 수 있습니다. 서비스의 복제본 또는 인스턴스가 배치되는 모든 노드의 응용 프로그램에 대해 예약된 메트릭 부하의 양을 정의합니다.

용량 예약의 예를 살펴보겠습니다.

![예약된 용량을 정의하는 응용 프로그램 인스턴스][Image2]

왼쪽 예에서 응용 프로그램은 정의된 응용 프로그램 용량이 없습니다. 서비스 패브릭 클러스터 리소스 관리자는 다른 서비스(응용 프로그램 외부)의 복제본 및 인스턴스와 함께 응용 프로그램의 자식 서비스 복제본과 인스턴스의 균형을 유지하여 클러스터의 균형을 유지합니다.

오른쪽 예에서 응용 프로그램이 2로 설정된 MinimumNodes와 3으로 설정된 MaximumNodes 및 20의 예약으로 정의된 응용 프로그램 메트릭, 50 노드의 최대 용량, 100의 총 응용 프로그램 용량으로 만들어졌다고 가정하면 서비스 패브릭은 파란색 응용 프로그램에 대한 두 개의 노드에 용량을 예약하고 클러스터의 다른 복제본이 해당 용량을 소비하도록 허용하지 않습니다. 이 예약된 응용 프로그램 용량은 소비되고 남은 클러스터 용량으로 계산될 것으로 간주됩니다.

응용 프로그램이 예약으로 만들어질 때 클러스터 리소스 관리자는 클러스터의 MinimumNodes * NodeReservationCapacity와 같은 용량을 예약하지만 응용 프로그램 서비스의 복제본이 생성되고 배치될 때까지 특정 노드에 대해 용량을 예약하지 않습니다. 노드는 만들어질 때 새 복제본에 대해 선택되므로 유연성을 허용합니다. 복제본이 하나 이상 배치되면 용량이 특정 노드에서 예약됩니다.

## 응용 프로그램 부하 정보 얻기
응용 프로그램 용량이 정의된 각 응용 프로그램의 경우 해당 서비스의 복제본에서 보고된 집계 부하에 대한 정보를 얻을 수 있습니다. 서비스 패브릭은 이 목적을 위해 PowerShell 및 관리되는 API 쿼리를 제공합니다.

예를 들어 다음 PowerShell cmdlet을 사용하여 부하를 검색할 수 있습니다.

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

이 쿼리의 출력에는 최소 노드 및 최대 노드와 같은 응용 프로그램에 대해 지정된 응용 프로그램 용량에 대한 기본 정보가 포함됩니다. 응용 프로그램이 현재 사용하는 노드의 수에 대한 정보도 있습니다. 따라서 각 부하 메트릭의 경우 다음에 대한 정보가 있습니다.
- 메트릭 이름: 메트릭의 이름.
-	예약 용량: 이 응용 프로그램에 대한 클러스터에 예약된 클러스터 용량.
-	응용 프로그램 부하: 이 응용 프로그램 자식 복제본의 총 부하.
-	응용 프로그램 용량: 응용 프로그램 부하의 허용되는 최대값.

## 응용 프로그램 용량 삭제
응용 프로그램 용량 매개 변수가 응용 프로그램에 대해 설정되면 업데이트 응용 프로그램 API 또는 PowerShell cmdlet을 사용하여 제거할 수 있습니다. 예:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

이 명령은 응용 프로그램에서 모든 응용 프로그램 용량 매개 변수를 제거하고 서비스 패브릭 클러스터 리소스 관리자는 이 응용 프로그램을 이러한 매개 변수가 정의되지 않은 클러스터의 다른 응용 프로그램으로 처리하기 시작합니다. 명령의 효과는 즉시 이루어지며 클러스터 리소스 관리자는 이 응용 프로그램에 대한 모든 응용 프로그램 용량 매개 변수를 삭제합니다. 다시 지정하려면 적절한 매개 변수로 호출될 업데이트 응용 프로그램 API가 필요합니다.

## 응용 프로그램 용량에 대한 제한 사항
고려해야 할 응용 프로그램 용량 매개 변수에 대한 몇 가지 제한 사항이 있습니다. 유효성 검사 오류가 발생할 경우 응용 프로그램의 만들기 또는 업데이트는 오류와 함께 거부됩니다. 모든 정수 매개 변수는 음수가 아닌 숫자여야 합니다. 또한 개별 매개 변수의 경우 제한 사항은 다음과 같습니다.

-	MinimumNodes는 MaximumNodes보다 클 수 없습니다.
-	부하 메트릭의 용량을 정의한 경우 다음 규칙을 따라야 합니다.
  - 노드 예약 용량은 최대 노드 용량보다 크지 않아야 합니다. 예를 들어 노드의 메트릭 "CPU"에 대한 용량을 2단위로 제한하고 각 노드에 3단위를 예약하려고 시도할 수 없습니다.
  - MaximumNodes를 지정하는 경우 MaximumNodes의 제품 및 최대 노드 용량은 총 응용 프로그램 용량보다 크지 않아야 합니다. 예를 들어 부하 메트릭 "CPU"에 대한 최대 노드 용량을 8로 설정하고 최대 노드를 10으로 설정하는 경우 총 응용 프로그램 용량은 이 부하 메트릭에 대한 80보다 커야 합니다.

제한 사항은 응용 프로그램 생성 중(클라이언트 쪽) 및 응용 프로그램 업데이트 중(서버 쪽) 모두에 적용됩니다. 생성 중 MaximumNodes < MinimumNodes이므로 이는 요구 사항의 명백한 위반의 예이며 명령은 요청이 서비스 패브릭 클러스터에 전송되기 전에 클라이언트에서 실패합니다.

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

잘못된 업데이트의 예는 다음과 같습니다. 기존 응용 프로그램을 가져와서 최대 노드를 특정 값으로 업데이트하는 경우 업데이트는 전달됩니다.

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

그런 다음 최소 노드 업데이트를 시도할 수 있습니다.

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

클라이언트에 응용 프로그램에 대한 충분한 컨텍스트가 없으므로 업데이트가 서비스 패브릭 클러스터로 전달되도록 허용합니다. 그러나 클러스터에서 서비스 패브릭은 기존 매개 변수와 함께 새 매개 변수의 유효성을 검사하고 최소 노드에 대한 값이 최대 노드에 대한 값보다 크기 때문에 업데이트 작업이 실패합니다. 이 경우 응용 프로그램 용량 매개 변수는 그대로 유지됩니다.

이러한 제한 사항은 클러스터 리소스 관리자가 응용 프로그램 서비스의 복제본에 대한 최상의 배치를 제공할 수 있도록 배치됩니다.

## 응용 프로그램 용량을 사용하지 않는 방법

-	노드의 특정 하위 집합에 대한 응용 프로그램을 제한하는 데 응용 프로그램 용량을 사용하지 마십시오. 서비스 패브릭은 최대 노드가 응용 프로그램 용량이 지정된 각 응용 프로그램에 대해 적용되도록 하지만 사용자는 인스턴스화될 노드를 결정할 수 없습니다. 이는 서비스에 대한 배치 제약 조건을 사용하여 수행할 수 있습니다.
-	동일한 응용 프로그램의 두 서비스가 항상 나란히 배치될 수 있도록 응용 프로그램 용량을 사용하지 마십시오. 이는 서비스 간 선호도 관계를 사용하여 수행할 수 있으며 선호도는 실제로 함께 배치되어야 하는 서비스로만 제한될 수 있습니다.

## 다음 단계
- 서비스 구성에 사용할 수 있는 기타 옵션에 대한 자세한 내용은 [서비스 구성에 대해 알아보기](service-fabric-cluster-resource-manager-configure-services.md)에서 다른 클러스터 Resource Manager 구성에 대한 항목을 확인하세요.
- 클러스터 리소스 관리자가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)에 대한 문서를 확인하세요.
- 처음부터 시작 및 [서비스 패브릭 클러스터 리소스 관리자 소개](service-fabric-cluster-resource-manager-introduction.md)
- 메트릭이 일반적으로 작동하는 방식에 대한 자세한 내용은 [서비스 패브릭 부하 메트릭](service-fabric-cluster-resource-manager-metrics.md)을 읽어 보세요.
- 클러스터 리소스 관리자에는 클러스터를 설명하기 위한 많은 옵션이 있습니다. 이에 대해 자세히 알아보려면 [서비스 패브릭 클러스터를 설명](service-fabric-cluster-resource-manager-cluster-description.md)하는 이 문서를 확인하세요.


[Image1]: ./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

<!---HONumber=AcomDC_0824_2016-->