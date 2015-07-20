<properties
   pageTitle="Azure 서비스 패브릭 엔터티에서 집계한 상태를 보는 방법"
   description="상태 쿼리 및 일반 쿼리를 통해 Azure 서비스 패브릭 엔터티에서 집계한 상태를 쿼리, 확인 및 평가하는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/16/2015"
   ms.author="oanapl"/>

# 서비스 패브릭 상태 보고서를 보는 방법
서비스 패브릭은 시스템 구성 요소와 watchdogs가 모니터링하는 로컬 조건을 보고할 수 있는 상태 엔터티로 구성된 [상태 모델](service-fabric-health-introduction.md)을 소개합니다. [상태 저장소](service-fabric-health-introduction.md#health-store)는 모든 상태 데이터를 집계하여 엔터티가 정상인지 여부를 판단합니다.

기본적으로, 클러스터는 시스템 구성 요소에 의해 전송되는 상태 보고서로 채워집니다. 추가 정보는 [시스템 상태 보고서를 사용하여 문제 해결](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)을 참조하십시오.

서비스 패브릭은 엔터티에서 집계한 상태를 가져올 수 있는 여러 방법을 제공합니다.

- [서비스 패브릭 탐색기](service-fabric-visualizing-your-cluster.md) 또는 기타 시각화 도구

- 상태 쿼리(Powerhsell/API/REST를 통해)

- 상태를 속성 중 하나로 가지고 있는 엔터티 목록을 반환하는 일반 쿼리(Powershell/API/REST를 통해)

이러한 옵션을 설명하기 위해 **5개의 노드**가 있는 로컬 클러스터를 사용하겠습니다. **패브릭:/시스템** 응용프로그램(기본적으로 존재) 옆에는 다른 응용프로그램이 배포되어 있는데 그 중 하나는 **패브릭:/WordCount**입니다. 이 응용프로그램에는 7개의 복제본으로 구성된 상태 저장 서비스가 포함되어 있습니다. 5개의 노드만 있으므로 시스템 구성 요소는 파티션이 대상 개수 이하임을 경고와 함께 플래그합니다.

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## 서비스 패브릭 탐색기 내 상태
서비스 패브릭 탐색기는 클러스터의 시각적 보기를 제공합니다. 아래 그림에서 다음을 알 수 있습니다.

- 속성 가용성에 대해 MyWatchdog에서 보고한 오류 이벤트가 있으므로 응용프로그램 **패브릭:/WordCount**가 "빨간색”(오류 시)입니다.

- 해당 서비스 중 하나인 **패브릭:/WordCount/WordCount.Service**가 "노란색"입니다(경고 시). 위에서 설명한 대로, 이 서비스는 7개의 복제본으로 구성되며 모두 배치할 수 없습니다(5개의 노드만 있으므로). 여기에 표시되지 않았지만 서비스 파티션은 시스템 보고서 때문에 "노란색"입니다. "노란색" 파티션은 "노란색" 서비스를 트리거합니다.

- **클러스터**는 “빨간색" 응용프로그램으로 인해 "빨간색"입니다.

평가는 엄격한 정책으로 오류를 용납하지 않는 클러스터 매니페스트 및 응용프로그램 매니페스트의 기본 정책을 사용합니다.

![ServiceFabricExplorer를 통한 클러스터 보기입니다.][1]

ServiceFabricExplorer를 통한 클러스터 보기입니다.

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE][서비스 패브릭 탐색기](service-fabric-visualizing-your-cluster.md)에 대해 자세하 알아봅니다.

## 상태 쿼리
서비스 패브릭은 각각의 지원되는 [엔터티 유형](service-fabric-health-introduction.md#health-entities-and-hierarchy)에 대해 상태 쿼리를 노출합니다. 이러한 쿼리는 API(FabricClient.HealthManager 상의 메서드), Powershell cmdlets 및 REST를 통해 액세스할 수 있습니다. 이러한 쿼리는 집계된 성능 상태, 엔터티에 대해 보고된 상태 이벤트, 자식 성능 상태(해당되는 경우) 및 엔터티가 정상이 아닐 때 비정상적인 평가 등을 포함한 엔터티에 대한 완벽한 상태 정보를 반환합니다.

> [AZURE.NOTE]상태 엔터티가 상태 저장소에서 완벽하게 채워지면되면 상태 엔터티가 사용자에게 반환됩니다. 엔터티에 시스템 보고서가 있으며 활성화 상태이고(삭제되지 않음) 계층 구조 체인에서 부모 엔터티가 시스템 보고서를 가지게 됩니다. 이러한 조건 중 하나라도 만족되지 않으면 상태 쿼리가 엔터티가 반환되지 않는 이유를 보여주는 예외를 반환합니다.

상태 쿼리는 엔터티 유형에 따라 달라지는 엔터티 식별자를 전달해야 합니다. 옵션인 상태 정책 매개 변수를 수락합니다. 지정하지 않으면 클러스터 또는 응용프로그램 매니페스트의 [상태 정책](service-fabric-health-introduction.md#health-policies)이 평가에 사용됩니다. 또한 지정된 필터를 유지하는 부분 자녀 또는 이벤트만 반환하기 위한 필터를 수락합니다.

> [AZURE.NOTE]출력 필터는 서버 쪽에 적용되므로 메시지 회신 크기가 감소합니다. 클라이언트 쪽에서 필터를 적용하는 것보다 반환된 데이터를 제한하는 필터를 사용하는 것이 좋습니다.

엔터티 상태에는 다음 정보가 포함되어 있습니다.

- 엔터티의 집계된 성능 상태. 이것은 엔터티 상태 보고서, 자녀 성능 상태(해당되는 경우) 및 성능 정책을 기준으로 상태 저장소에 의해 컴퓨팅됩니다. [엔터티 상태 평가](service-fabric-health-introduction.md#entity-health-evaluation)에 대해 자세히 알아봅니다.  

- 엔터티에 대한 상태 이벤트입니다.

- 자녀를 가질 수 있는 엔터티의 경우에는 모든 자녀를 위한 성능 상태의 컬렉션입니다. 성능 상태에는 엔터티 식별자와 집계된 성능 상태가 포함됩니다. 자녀에 대한 완전한 상태를 얻으려면 자녀 식별자를 전달하면서 자녀 엔터티 유형에 대한 쿼리 상태를 호출하십시오.

- 엔터티가 비정상일 경우 비정상인 평가는 엔터티의 상태를 트리거한 보고서를 가리킵니다.

## 클러스터 상태 가져오기
클러스터 엔터티의 상태를 반환합니다. 응용프로그램 및 노드의 성능 상태를 포함합니다(클러스터의 자녀). 입력:

- [옵션] 응용 프로그램 매니페스트 정책을 재정의하는 데 사용되는 상태 정책이 있는 응용 프로그램 상태 정책 맵

- [옵션] 특정 성능 상태(예: 오류만 반환 또는 경고나 오류 반환)로 이벤트, 노드, 응용 프로그램만 반환하는 필터.

### API
클러스터 상태를 얻으려면 HealthManager에서 FabricClient를 만들고 GetClusterHealthAsync 메서드를 호출합니다.

다음은 클러스터 상태를 가져옵니다.

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

다음은 노드 및 응용프로그램에 대해 사용자 지정 클러스터 상태 정책 및 필터를 사용하여 클러스터 상태를 가져옵니다. 모든 입력 데이터가 포함된 System.Fabric.Description.ClusterHealthQueryDescription을 생성합니다.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilter = (long)(HealthStateFilter.Error | HealthStateFilter.Warning)
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilter = (long)HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync(queryDescription).Result;
```

### Powershell
클러스터 상태를 가져오려는 cmdlet은 Get-ServiceFabricClusterHealth입니다. 먼저 Connect-ServiceFabricCluster cmdlet으로 클러스터에 연결합니다. 클러스터의 상태: 상기한 것처럼 구성된 5개의 노드, 시스템 응용프로그램 및 패브릭:/WordCount

다음 cmdlet은 기본 상태 정책을 사용하여 클러스터 상태를 가져옵니다. 패브릭:/WordCount 응용프로그램이 경고이기 때문에 집계된 성능 상태는 경고입니다. 비정상 평가가 집계된 상태를 트리거한 조건을 어떻게 자세히 표시하는지 확인합니다.

```xml
PS C:> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Warning'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Warning'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

NodeHealthStates        :
                          NodeName              : Node.4
                          AggregatedHealthState : Ok

                          NodeName              : Node.2
                          AggregatedHealthState : Ok

                          NodeName              : Node.1
                          AggregatedHealthState : Ok

                          NodeName              : Node.5
                          AggregatedHealthState : Ok

                          NodeName              : Node.3
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/CalculatorActor
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

다음 Powershell cmdlet이 사용자 지정 응용프로그램 정책으로 클러스터의 상태를 가져옵니다. 오류 또는 경고 응용프로그램 및 노드만 가져오도록 결과를 필터링합니다. 결과적으로 모두 정상이므로 반환되는 노드가 없습니다. 패브릭:/WordCount 응용프로그램만 응용프로그램 필터를 유지합니다. 사용자 지정 정책이 경고를 패브릭:/WordCount 응용프로그램에 대해 오류로 고려하도록 지정하므로 응용프로그램이 오류 시 평가되며 클러스터도 마찬가지입니다.

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
$warningAndErrorFilter = [System.Fabric.Health.HealthStateFilter]::Warning.value__  + [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsHealthStateFilter $warningAndErrorFilter -NodesHealthStateFilter $warningAndErrorFilter

AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Error'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## 노드 상태 가져오기
노드 엔터티의 상태를 반환합니다. 노드에서 보고된 상태 이벤트를 포함합니다. 입력:

- [필요] 노드를 식별하는 노드 이름.

- [옵션] 상태를 평가하는 데 사용되는 클러스터 상태 정책 설정.

- [옵션] 특정 성능 상태인 이벤트만 반환하는 필터(예: 오류만 반환 또는 경고나 오류 반환 등).

### API
API를 통해 노드 상태를 가져오려면 HealthManager에서 FabricClient를 만들고 GetNodeHealthAsync 메서드를 호출하십시오.

다음은 지정된 노드 이름에 대한 노드 상태를 가져옵니다.

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

다음은 System.Fabric.Description.NodeHealthQueryDescription을 통해 이벤트 필터와 사용자 지정 정책을 전달하면서 지정된 노드 이름에 대한 노드 상태를 가져옵니다.

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### Powershell
노드 상태를 가져오려는 cmdlet은 Get-ServiceFabricNodeHealth입니다. 먼저 Connect-ServiceFabricCluster cmdlet으로 클러스터에 연결합니다. 다음 cmdlet은 기본 상태 정책을 사용하여 노드 상태를 가져옵니다.

```powershell
PS C:> Get-ServiceFabricNodeHealth -NodeName Node.1

NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM
```

다음 cmdlet은 클러스터 내에서 모든 노드의 상태를 가져옵니다.

```powershell
PS C:> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
Node.4                      Ok
Node.2                      Ok
Node.1                      Ok
Node.5                      Ok
Node.3                      Ok
```

## 응용프로그램 상태 가져오기
응용프로그램 엔터티의 상태를 반환합니다. 배포된 응용 프로그램 및 서비스 자녀의 성능 상태를 포함합니다. 입력:

- [필요] 응용 프로그램을 식별하는 응용 프로그램 이름(Uri)

- [옵션] 응용 프로그램 매니페이스 정책을 재정의하는 데 사용되는 응용 프로그램 상태 정책.

- [옵션] 특정 성능 상태인 이벤트, 서비스, 배포된 응용 프로그램만 반환하는 필터(예: 오류만 반환 또는 경고나 오류 반환 등).

### API
응용 프로그램 상태를 가져오려면 HealthManager에서 FabricClient를 만들고 GetApplicationHealthAsync 메서드를 호출합니다.

다음은 지정된 응용 프로그램 Uri에 대한 응용 프로그램 상태를 가져옵니다.

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

다음은 System.Fabric.Description.ApplicationHealthQueryDescription을 통해 필터와 사용자 지정 정책을 지정하면서 지정된 응용 프로그램 이름 Uri에 대한 응용 프로그램 상태를 가져옵니다.

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
};

ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription).Result;
```

### Powershell
응용 프로그램 상태를 가져오기 위한 cmdlet은 Get-ServiceFabricApplicationHealth입니다. 먼저 Connect-ServiceFabricCluster cmdlet으로 클러스터에 연결합니다.

다음 cmdlet은 패브릭:/WordCount 응용 프로그램의 상태를 반환합니다.

```powershell
PS c:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service',
                                  MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='325da69f-16d4-4418-9c30-1feaa40a072c',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 2456
                                  SentAt                : 4/20/2015 9:57:06 PM
                                  ReceivedAt            : 4/20/2015 9:57:06 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/20/2015 9:57:06 PM
```

다음 Powershell은 사용자 지정 정책을 전달하며 자녀와 이벤트를 필터링합니다.

```powershell
PS C:> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesHealthStateFilter $errorFilter -EventsHealthStateFilter $errorFilter -DeployedApplicationsHealthStateFilter $errorFilter

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## 서비스 상태 가져오기
서비스 엔터티의 상태를 반환합니다. 파티션 성능 상태를 포함합니다. 입력:

- [필요] 서비스를 식별하는 서비스 이름(Uri)
- [옵션] 응용 프로그램 매니페스트 정책을 재정의하는 데 사용되는 응용 프로그램 상태 정책.
- [옵션] 특정 성능 상태의 이벤트 및 파티션만 반환하는 필터(예: 오류만 반환 또는 경고나 오류 반환 등).

### API
API를 통해 서비스 상태를 가져오려면 HealthManager에서 FabricClient를 만들고 GetServiceHealthAsync 메서드를 호출하십시오.

다음 예제는 지정된 서비스 이름(Uri)을 가진 서비스 상태를 가져옵니다.

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

다음은 System.Fabric.Description.ServiceHealthQueryDescription을 통해 필터와 사용자 지정 정책을 지정하면서 지정된 서비스 이름 Uri에 대한 서비스 상태를 가져옵니다.

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### Powershell
서비스 상태를 가져오는 cmdlet은 Get-ServiceFabricServiceHealth입니다. 먼저 Connect-ServiceFabricCluster cmdlet으로 클러스터에 연결합니다.

다음 cmdlet은 기본 상태 정책을 사용하여 서비스 상태를 가져옵니다.

```powershell
PS C:> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCount.Service


ServiceName           : fabric:/WordCount/WordCount.Service
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Warning'.

                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/20/2015 10:12:29 PM
                        ReceivedAt            : 4/20/2015 10:12:33 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:33 PM
```

## 파티션 상태 가져오기
파티션 엔터티의 상태를 반환합니다. 복제본 성능 상태를 포함합니다. 입력:

- [필요] 파티션을 식별하는 파티션 ID (Guid)

- [옵션] 응용 프로그램 매니페스트 정책을 재정의하는 데 사용되는 응용 프로그램 상태 정책.

- [옵션] 특정 성능 상태인 이벤트, 복제본만 반환하는 필터(예: 오류만 반환 또는 경고나 오류 반환 등).

### API
API를 통해 파티션 상태를 가져오려면 HealthManager에서 FabricClient를 만들고 GetServiceHealthAsync 메서드를 호출하십시오. 옵션인 매개 변수를 지정하려면 System.Fabric.Description.PartitionHealthQueryDescription을 만드십시오.

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### Powershell
파티션 상태를 가져오기 위한 cmdlet은 Get-ServiceFabricPartitionHealth입니다. 먼저 Connect-ServiceFabricCluster cmdlet으로 클러스터에 연결합니다.

다음 cmdlet은 단어 개수 서비스의 모든 파티션에 대한 상태를 가져옵니다.

```powershell
PS C:> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricPartitionHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130740415594605870
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415502123433
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605867
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605869
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605868
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 39
                        SentAt                : 4/20/2015 10:12:59 PM
                        ReceivedAt            : 4/20/2015 10:13:03 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/20/2015 10:13:03 PM
```

## 복제본 상태 가져오기
replica.Input의 상태를 반환합니다.

- [필요] 파티션 id (Guid) 및 복제본을 식별하는 복제본 id

- [옵션] 응용 프로그램 매니페시트 정책을 재정의하는 데 사용되는 응용 프로그램 상태 정책 매개 변수.

- [옵션] 특정 성능 상태인 이벤트만 반환하는 필터(예: 오류만 반환 또는 경고나 오류 반환 등).

### API
API를 통해 복제본 상태를 가져오려면 HealthManager에서 FabricClient를 만들고 GetReplicaHealthAsync 메서드를 호출하십시오. System.Fabric.Description.ReplicaHealthQueryDescription으로 고급 매개 변수를 지정합니다.

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### Powershell
복제본 상태를 가져오는 cmdlet은 Get-ServiceFabricReplicaHealth입니다. 먼저 Connect-ServiceFabricCluster cmdlet으로 클러스터에 연결합니다.

다음 cmdlet은 서비스의 모든 파티션에 대한 주 복제본의 상태를 가져옵니다.

```powershell
PS C:> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415502123433
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740415502802942
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## 배포된 응용 프로그램 상태 가져오기
노드 엔터티에 배포된 응용 프로그램의 상태를 반환합니다. 배포된 서비스 패키지 성능 상태를 포함합니다. 입력:

- [필요] 배포된 응용 프로그램을 식별하는 응용 프로그램 이름(Uri) 및 노드 이름(string)

- [옵션] 응용 프로그램 매니페이스 정책을 재정의하는 데 사용되는 응용 프로그램 상태 정책.

- [옵션] 특정 성능 상태인 이벤트, 배포된 서비스 패키지만 반환하는 필터(예: 오류만 반환 또는 경고나 오류 반환 등).

### API
API 통해 노드에 배포된 응용 프로그램에서 상태를 가져오려면 FabricClient를 만들고 해당 HealthManager에서 GetDeployedApplicationHealthAsync 메서드를 호출합니다. 옵션인 매개 변수를 지정하려면 System.Fabric.Description.DeployedApplicationHealthQueryDescription을 사용하십시오.

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### Powershell
배포된 응용 프로그램 상태를 가져오기 위한 cmdlet은 Get-ServiceFabricApplicationHealth입니다. 먼저 Connect-ServiceFabricCluster cmdlet으로 클러스터에 연결합니다. 응용 프로그램이 배포되는 위치를 확인하려면 Get ServiceFabricApplicationHealth를 실행하고 배포된 응용 프로그램 자녀를 살펴봅니다.

다음 cmdlter는 Node.1 노드에 배포된 패브릭:/WordCount 응용 프로그램의 상태를 가져옵니다.

```powershell
PS C:> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName Node.1
ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCount.WebService
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCount.Service
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130740415502842941
                                     SentAt                : 4/20/2015 10:12:30 PM
                                     ReceivedAt            : 4/20/2015 10:12:34 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## 배포된 서비스 패키지 상태 가져오기
배포된 서비스 패키지 엔터티의 상태를 반환합니다. 입력:

- [필요] 배포된 서비스 패키지를 식별하는 응용 프로그램 이름(Uri), 노드 이름(문자열) 및 서비스 매니페스트 이름(문자열)

- [옵션] 응용 프로그램 매니페스트 정책을 재정의하는 데 사용되는 응용 프로그램 상태 정책.

- [옵션] 특정 성능 상태인 이벤트만 반환하는 필터(예: 오류만 반환 또는 경고나 오류 반환 등).

### API
API 통해 배포된 서비스 패키지 상태를 가져오려면 FabricClient를 만들고 해당 HealthManager에서 GetDeployedServicePackageHealthAsync 메서드를 호출합니다.

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### Powershell
배포된 서비스 패키지 상태를 가져오는 cmdlet은 Get-ServiceFabricDeployedServicePackageHealth입니다. 먼저 Connect-ServiceFabricCluster cmdlet으로 클러스터에 연결합니다. 응용 프로그램이 배포되는 위치를 확인하려면 Get-ServiceFabricApplicationHealth를 실행하고 배포된 응용 프로그램들을 살펴봅니다. 응용 프로그램에 어떤 서비스 패키지가 있는지 보려면 Get-ServiceFabricDeployedApplicationHealth 출력에 배포된 서비스 패키지 자녀를 살펴봅니다.

다음 cmdlter는 Node.1 노드에 배포된 패브릭:/WordCount 응용 프로그램의 WordCount.Service 서비스 패키지 상태를 가져옵니다. 엔터티에 성공적인 서비스 패키지 및 진입점 활성화와 성공적인 서비스 유형 등록을 위한 System.Hosting 보고서가 있습니다.

```powershell
PS C:> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName Node.1 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCount.Service

ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCount.Service
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130740415506383060
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130740415506543054
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCount.Service
                        HealthState           : Ok
                        SequenceNumber        : 130740415520193499
                        SentAt                : 4/20/2015 10:12:32 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## 일반 쿼리
일반 쿼리는 지정된 형식의 서비스 패브릭 엔터티 목록을 반환합니다. 이러한 쿼리는 API(FabricClient.QueryManager 상의 메서드), Powershell cmdlets 및 REST를 통해 노출됩니다. 이러한 쿼리는 여러 구성 요소에서 하위 쿼리를 집계합니다. 둘 중 하나는 [상태 저장소](service-fabric-health-introduction.md#health-store)로 각 쿼리 결과에 대해 집계된 성능 상태를 채웁니다.

> [AZURE.NOTE]일반 쿼리는 엔터티의 집계된 성능 상태를 반환 하고 풍부한 상태 데이터를 포함하지 않습니다. 엔터티가 비정상이면 상태 쿼리를 따라서 이벤트, 자녀 성능 상태 및 비정상 평가와 같은 모든 상태 정보를 가져올 수 있습니다.

일반 쿼리가 엔터티에 대해 알 수 없는 성능 상태를 반환하는 경우 상태 저장소에 해당 엔터티에 대해 완전한 데이터가 없거나 상태 저장소에 대한 하위 쿼리에 성공하지 못한 것일 수 있습니다(예: 통신 오류, 상태 저장소가 정체됨). 엔터티에 대한 상태 쿼리를 따릅니다. 이것은 하위 쿼리에 일시적인 오류(예: 네트워크 문제)가 발생한 경우 성공할 수 있으며, 또는 엔터티가 상태 저장소로부터 노출되지 않은 자세한 이유를 제공합니다.

엔터티에 대한 HealthState가 포함된 쿼리는 다음과 같습니다.

- 노드 목록입니다. 클러스터에 목록 노드를 반환합니다.
  - Api: FabricClient.QueryManager.GetNodeListAsync.
  - Powershell: Get-ServiceFabricNode.
- 응용 프로그램 목록. 클러스터에 응용 프로그램 목록을 반환합니다.
  - Api: FabricClient.QueryManager.GetApplicationListAsync.
  - Powershell: Get-ServiceFabricApplication.
- 서비스 목록입니다. 응용 프로그램에서 서비스의 목록을 반환합니다.
  - Api: FabricClient.QueryManager.GetServiceListAsync.
  - Powershell: Get-ServiceFabricService.
- 파티션 목록입니다. 서비스에서 파티션의 목록을 반환합니다.
  - Api: FabricClient.QueryManager.GetPartitionListAsync.
  - Powershell: Get-ServiceFabricPartition.
- 복제본 목록입니다. 파티션에 복제본의 목록을 반환합니다.
  - Api: FabricClient.QueryManager.GetReplicaListAsync.
  - Powershell: Get-ServiceFabricReplica.
- 배포된 응용 프로그램 목록입니다. 노드에 배포된 응용 프로그램의 목록을 반환합니다.
  - Api: FabricClient.QueryManager.GetDeployedApplicationListAsync.
  - Powershell: Get-ServiceFabricDeployedApplication.
- 배포된 서비스 패키지 목록입니다. 배포된 응용 프로그램에서 서비스 패키지 목록을 반환합니다.
  - Api: FabricClient.QueryManager.GetDeployedServicePackageListAsync.
  - Powershell: Get-ServiceFabricDeployedApplication.

### 예제

다음은 클러스터에서 비정상 응용 프로그램을 가져옵니다.

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

다음 cmdlet은 패브릭:/WordCount 응용 프로그램에 대해 응용 프로그램 세부 정보를 가져옵니다. 성능 상태는 경고입니다.

```powershell
PS C:> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

다음 cmdlet은 성능 상태 경고로 서비스를 가져옵니다.

```powershell
PS C:> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}

ServiceName            : fabric:/WordCount/WordCount.Service
ServiceKind            : Stateful
ServiceTypeName        : WordCount.Service
IsServiceGroup         : False
ServiceManifestVersion : 1.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## 클러스터와 응용 프로그램 업그레이드
클러스터 및 응용 프로그램에서 모니터링하는 업그레이드 중에 서비스 패브릭이 상태를 점검하여 모든 것이 정상으로 유지되는지 확인합니다. 구성된 정책별로 비정상인 항목이 있는 경우 업그레이드가 일시 중지되어 사용자의 상호 작용을 허용하거나 자동으로 롤백됩니다.

**클러스터** 업그레이드 중에 클러스터 내에서 비정상인 항목을 가리키는 비정상 평가가 포함된 클러스터 업그레이드 상태를 가져올 수 있습니다. 상태 문제로 인해 업그레이드가 롤백되면, 업그레이드 상태가 마지막 비정상 이유를 유지하므로 관리자가 무엇이 문제인지 조사할 수 있습니다. 마찬가지로, **응용 프로그램** 업그레이드 중에 응용 프로그램 업그레이드 상태에 비정상 평가가 포함됩니다.

다음은 수정된 패브릭:/WordCount 응용 프로그램에 대한 응용 프로그램 업그레이드 상태를 보여줍니다. watchdog에서 복제본 하나에서 오류를 보고했습니다. 상태 검사를 준수하지 않기 때문에 업그레이드가 롤백됩니다.

```powershell
PS C:> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : Node1
                                UpgradePhase        : Upgrading

                                NodeName            : Node2
                                UpgradePhase        : Upgrading

                                NodeName            : Node3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', AggregatedHealthState='Error'.

                                Unhealthy replicas: 16% (1/6), MaxPercentUnhealthyReplicasPerPartition=0%.

                                Unhealthy replica: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', ReplicaOrInstanceId='130741105362491906', AggregatedHealthState='Error'.

                                Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

[서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)에 대해 자세히 알아봅니다.

## 상태 문제 해결
클러스터 또는 응용 프로그램에 문제가 있을 때마다 클러스터나 응용 프로그램 상태를 확인하여 무엇이 잘못인지 식별합니다. 비정상 평가가 현재 비정상 상태를 무엇이 트리거했는지에 대한 세부 정보를 보여줍니다. 필요한 경우 비정상 자녀 엔터티로 드릴다운하여 문제를 파악합니다.

## 다음 단계
[시스템 상태 보고서를 사용하여 문제 해결](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[사용자 지정 서비스 패브릭 상태 보고서 추가하기](service-fabric-report-health.md)

[로컬로 서비스를 모니터링 및 진단하는 방법](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[서비스 패브릭 응용프로그램 업그레이드](service-fabric-application-upgrade.md)
 

<!---HONumber=July15_HO2-->