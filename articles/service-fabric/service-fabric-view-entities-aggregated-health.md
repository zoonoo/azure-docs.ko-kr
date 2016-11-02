<properties
   pageTitle="Azure 서비스 패브릭 엔터티에서 집계한 상태를 보는 방법 | Microsoft Azure"
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
   ms.date="09/28/2016"
   ms.author="oanapl"/>


# <a name="view-service-fabric-health-reports"></a>서비스 패브릭 상태 보고서 보기
Azure 서비스 패브릭은 시스템 구성 요소와 watchdogs가 모니터링하는 로컬 조건을 보고할 수 있는 상태 엔터티로 구성된 [상태 모델](service-fabric-health-introduction.md) 을 소개합니다. [상태 저장소](service-fabric-health-introduction.md#health-store) 는 모든 상태 데이터를 집계하여 엔터티가 정상인지 여부를 판단합니다.

기본적으로, 클러스터는 시스템 구성 요소에 의해 전송되는 상태 보고서로 채워집니다. 추가 정보는 [시스템 상태 보고서를 사용하여 문제 해결](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)을 참조하세요.

서비스 패브릭은 엔터티의 집계한 상태를 가져올 수 있는 여러 방법을 제공합니다.

- [서비스 패브릭 탐색기](service-fabric-visualizing-your-cluster.md) 또는 기타 시각화 도구

- 상태 쿼리(PowerShell, API 또는 REST를 통해)

- 상태를 속성 중 하나로 가지고 있는 엔터티 목록을 반환하는 일반 쿼리(PowerShell, API 또는 REST를 통해)

이러한 옵션을 설명하기 위해 5개의 노드가 있는 로컬 클러스터를 사용하겠습니다. **패브릭:/시스템** 응용 프로그램(기본적으로 존재) 옆에는 다른 응용 프로그램이 배포되어 있습니다. 응용 프로그램 중 하나는 **fabric:/WordCount**입니다. 이 응용 프로그램에는 7개의 복제본으로 구성된 상태 저장 서비스가 포함되어 있습니다. 5개의 노드만 있으므로 시스템 구성 요소는 파티션이 대상 개수 이하라는 경고를 보여 줍니다.

```xml
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>서비스 패브릭 탐색기 내 상태
서비스 패브릭 탐색기는 클러스터의 시각적 보기를 제공합니다. 아래 이미지에서 다음을 알 수 있습니다.

- 속성 **가용성**에 대해 **MyWatchdog**에서 보고한 오류 이벤트가 있으므로 응용 프로그램 **fabric:/WordCount**가 빨간색(오류 시)입니다.

- 해당 서비스 중 하나인 **패브릭:/WordCount/WordCountService** 가 노란색입니다(경고 시). 서비스가 7개의 복제본으로 구성되었기 때문에 모두 배치될 수 없습니다(5개의 노드만 있으므로). 여기에 표시되지 않았지만 서비스 파티션은 시스템 보고서 때문에 노란색입니다. 노란색 파티션은 노란색 서비스를 트리거합니다.

- 클러스터는 빨간색 응용 프로그램으로 인해 빨간색입니다.

평가는 클러스터 매니페스트 및 응용 프로그램 매니페스트의 기본 정책을 사용합니다. 엄격한 정책이며 오류를 용납하지 않습니다.

서비스 패브릭 탐색기로 클러스터 보기:

![서비스 패브릭 탐색기로 클러스터 보기.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] [서비스 패브릭 탐색기](service-fabric-visualizing-your-cluster.md)에 대해 자세히 알아봅니다.

## <a name="health-queries"></a>상태 쿼리
서비스 패브릭은 각각의 지원되는 [엔터티 유형](service-fabric-health-introduction.md#health-entities-and-hierarchy)에 대해 상태 쿼리를 노출합니다. 이러한 쿼리는 API( **FabricClient.HealthManager**에서 찾을 수 있는 메서드), PowerShell cmdlet 및 REST를 통해 액세스할 수 있습니다. 이러한 쿼리는 집계된 성능 상태, 엔터티 상태 이벤트, 자녀 상태(해당되는 경우) 및 엔터티가 정상이 아닐 때 비정상적 평가 등을 포함한 엔터티에 대한 완전한 상태 정보를 반환합니다.

> [AZURE.NOTE] 상태 엔터티는 Health 스토어에서 완전히 채워지면 사용자에게 반환됩니다. 엔터티는 활성화 상태이고(삭제되지 않음) 시스템 보고서를 가져야 합니다. 또한 계층 구조 체인에서 부모 엔터티는 시스템 보고서를 가져야 합니다. 이러한 조건 중 하나라도 만족되지 않으면 상태 쿼리가 엔터티가 반환되지 않는 이유를 보여 주는 예외를 반환합니다.

상태 쿼리는 엔터티 유형에 따라 달라지는 엔터티 식별자를 전달해야 합니다. 쿼리는 옵션인 상태 정책 매개 변수를 수락합니다. 상태 정책이 지정되지 않으면 클러스터 또는 응용 프로그램 매니페스트의 [상태 정책](service-fabric-health-introduction.md#health-policies) 이 평가에 사용됩니다. 또한 쿼리는 지정된 필터를 유지하는 부분 자녀 또는 이벤트만 반환하기 위한 필터를 수락합니다.

> [AZURE.NOTE] 출력 필터는 서버 쪽에 적용되므로 메시지 회신 크기가 감소합니다. 클라이언트 쪽에서 필터를 적용하는 것보다 반환된 데이터를 제한하는 출력 필터를 사용하는 것이 좋습니다.

엔터티의 상태는 다음을 포함합니다.

- 엔터티의 집계된 성능 상태. 엔터티 상태 보고서, 자녀 상태(해당되는 경우) 및 상태 정책을 기반으로 Health 스토어에 의해 계산됩니다. [엔터티 상태 평가](service-fabric-health-introduction.md#entity-health-evaluation)에 대해 자세히 알아봅니다.  

- 엔터티에 대한 상태 이벤트입니다.

- 자녀를 가질 수 있는 엔터티에 대한 모든 자녀의 성능 상태 컬렉션입니다. 성능 상태에는 엔터티 식별자와 집계된 성능 상태가 포함됩니다. 자녀에 대한 완전한 상태를 얻으려면 자녀 엔터티 유형에 대한 쿼리 상태를 호출하고 자녀 식별자를 전달합니다.

- 엔터티가 비정상일 경우 비정상인 평가는 엔터티의 상태를 트리거한 보고서를 가리킵니다.

## <a name="get-cluster-health"></a>클러스터 상태 가져오기
클러스터 엔터티의 상태를 반환하고 응용 프로그램 및 노드의 상태를 포함합니다(클러스터의 자녀). 입력:

- [옵션] 노드 및 클러스터 이벤트를 평가하는 데 사용되는 클러스터 상태 정책.

- [옵션] 응용 프로그램 매니페스트 정책을 재정의하는 데 사용되는 상태 정책이 있는 응용 프로그램 상태 정책 맵.

- [옵션] 관심 있는 엔터티를 지정하고 결과에 반환되어야 하는 이벤트, 노드 및 응용 프로그램에 대한 필터(예: 오류만 또는 경고 및 오류). 모든 이벤트, 노드 및 응용 프로그램은 필터에 관계 없이 엔터티 집계된 상태 평가에 사용됩니다.

### <a name="api"></a>API
클러스터 상태를 얻으려면 **HealthManager**에서 `FabricClient`를 만들고 [GetClusterHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) 메서드를 호출합니다.

다음 호출은 클러스터 상태를 가져옵니다.

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

다음 코드는 노드 및 응용 프로그램에 대한 사용자 지정 클러스터 상태 정책 및 필터를 사용하여 클러스터 상태를 가져옵니다. 입력 정보를 포함하는 [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx)을 생성합니다.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
클러스터 상태를 가져오려는 cmdlet은 [Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx)입니다. 먼저 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) cmdlet을 사용하여 클러스터에 연결합니다.

클러스터의 상태는 설명된 대로 구성된 5개의 노드 및 시스템 응용 프로그램 및 fabric:/WordCount입니다.

다음 cmdlet은 기본 상태 정책을 사용하여 클러스터 상태를 가져옵니다. 패브릭:/WordCount 응용 프로그램이 경고이기 때문에 집계된 성능 상태는 경고입니다. 비정상 평가가 집계된 상태를 트리거한 조건을 어떻게 자세히 제공하는지 확인합니다.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

다음 PowerShell cmdlet이 사용자 지정 응용 프로그램 정책을 사용하여 클러스터의 상태를 가져옵니다. 오류만 또는 경고 응용 프로그램 및 노드만 가져오도록 결과를 필터링합니다. 결과적으로 모두 정상이므로 반환되는 노드가 없습니다. 패브릭:/WordCount 응용 프로그램만 응용 프로그램 필터를 유지합니다. 사용자 지정 정책이 경고를 패브릭:/WordCount 응용 프로그램에 대해 오류로 고려하도록 지정하므로 응용 프로그램이 오류 시 평가되며 클러스터도 마찬가지입니다.

```powershell
PS c:\> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

### <a name="rest"></a>REST (영문)
본문에 설명된 상태 정책을 포함하는 [GET 요청](https://msdn.microsoft.com/library/azure/dn707669.aspx) 또는 [POST 요청](https://msdn.microsoft.com/library/azure/dn707696.aspx)를 사용하여 클러스터 상태를 가져올 수 있습니다.

## <a name="get-node-health"></a>노드 상태 가져오기
노드 엔터티의 상태를 반환하고 노드에서 보고된 상태 이벤트를 포함합니다. 입력:

- [필수] 노드를 식별하는 노드 이름.

- [옵션] 상태를 평가하는 데 사용되는 클러스터 상태 정책 설정.

- [옵션] 관심 있는 엔터티를 지정하고 결과에 반환되어야 하는 이벤트에 대한 필터(예: 오류만 또는 경고 및 오류). 모든 이벤트는 필터에 관계 없이 엔터티 집계된 상태 평가에 사용됩니다.

### <a name="api"></a>API
API를 통해 노드 상태를 가져오려면 HealthManager에서 `FabricClient` 를 만들고 [GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx) 메서드를 호출합니다.

다음 코드는 지정된 노드 이름에 대한 노드 상태를 가져옵니다.

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

다음 코드는 지정된 노드 이름에 대한 노드 상태를 가져오고 [NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx)을 통해 이벤트 필터와 사용자 지정 정책을 전달합니다.

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
노드 상태를 가져오려는 cmdlet은 [Get-ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx)입니다. 먼저 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) cmdlet을 사용하여 클러스터에 연결합니다.
다음 cmdlet은 기본 상태 정책을 사용하여 노드 상태를 가져옵니다.

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

다음 cmdlet은 클러스터 내에서 모든 노드의 상태를 가져옵니다.

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

### <a name="rest"></a>REST (영문)
본문에 설명된 상태 정책을 포함하는 [GET 요청](https://msdn.microsoft.com/library/azure/dn707650.aspx) 또는 [POST 요청](https://msdn.microsoft.com/library/azure/dn707665.aspx)를 사용하여 노드 상태를 가져올 수 있습니다.

## <a name="get-application-health"></a>응용 프로그램 상태 가져오기
응용프로그램 엔터티의 상태를 반환합니다. 배포된 응용 프로그램 및 서비스 자녀의 성능 상태를 포함합니다. 입력:

- [필수] 응용 프로그램을 식별하는 응용 프로그램 이름(URI).

- [옵션] 응용 프로그램 매니페스트 정책을 재정의하는 데 사용되는 응용 프로그램 상태 정책.

- [옵션] 관심 있는 엔터티를 지정하고 결과에 반환되어야 하는 이벤트, 서비스 및 배포된 응용 프로그램에 대한 필터(예: 오류만 또는 경고 및 오류). 모든 이벤트, 서비스 및 배포된 응용 프로그램은 필터에 관계 없이 엔터티 집계된 상태 평가에 사용됩니다.

### <a name="api"></a>API
응용 프로그램 상태를 가져오려면 HealthManager에서 `FabricClient` 를 만들고 [GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx) 메서드를 호출합니다.

다음 코드는 지정된 응용 프로그램 이름(URI)에 대한 응용 프로그램 상태를 가져옵니다.

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

다음 코드는 [ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx)을 통해 지정된 필터와 사용자 지정 정책으로 지정된 응용 프로그램 이름(URI)에 대한 응용 프로그램 상태를 가져옵니다.

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
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
응용 프로그램 상태를 가져오기 위한 cmdlet은 [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx)입니다. 먼저 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) cmdlet을 사용하여 클러스터에 연결합니다.

다음 cmdlet은 **fabric:/WordCount** 응용 프로그램의 상태를 반환합니다.

```powershell
PS c:\>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

다음 PowerShell cmdlet은 사용자 지정 정책을 전달합니다. 또한 자녀와 이벤트를 필터링합니다.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST (영문)
본문에 설명된 상태 정책을 포함하는 [GET 요청](https://msdn.microsoft.com/library/azure/dn707681.aspx) 또는 [POST 요청](https://msdn.microsoft.com/library/azure/dn707643.aspx)를 사용하여 응용 프로그램 상태를 가져올 수 있습니다.

## <a name="get-service-health"></a>서비스 상태 가져오기
서비스 엔터티의 상태를 반환합니다. 파티션 성능 상태를 포함합니다. 입력:

- [필수] 서비스를 식별하는 서비스 이름(URI)

- [옵션] 응용 프로그램 매니페스트 정책을 재정의하는 데 사용되는 응용 프로그램 상태 정책.

- [옵션] 관심 있는 엔터티를 지정하고 결과에 반환되어야 하는 이벤트 및 파티션에 대한 필터(예: 오류만 또는 경고 및 오류). 모든 이벤트 및 파티션은 필터에 관계 없이 엔터티 집계된 상태 평가에 사용됩니다.

### <a name="api"></a>API
API를 통해 서비스 상태를 가져오려면 HealthManager에서 `FabricClient` 를 만들고 [GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx) 메서드를 호출합니다.

다음 예제는 지정된 서비스 이름(URI)을 가진 서비스 상태를 가져옵니다.

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

다음 코드는 [ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx)을 통해 필터와 사용자 지정 정책을 지정하면서 지정된 서비스 이름(URI)에 대한 서비스 상태를 가져옵니다.

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
서비스 상태를 가져오는 cmdlet은 [Get-ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx)입니다. 먼저 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) cmdlet을 사용하여 클러스터에 연결합니다.

다음 cmdlet은 기본 상태 정책을 사용하여 서비스 상태를 가져옵니다.

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
```

### <a name="rest"></a>REST (영문)
본문에 설명된 상태 정책을 포함하는 [GET 요청](https://msdn.microsoft.com/library/azure/dn707609.aspx) 또는 [POST 요청](https://msdn.microsoft.com/library/azure/dn707646.aspx)를 사용하여 서비스 상태를 가져올 수 있습니다.

## <a name="get-partition-health"></a>파티션 상태 가져오기
파티션 엔터티의 상태를 반환합니다. 복제본 성능 상태를 포함합니다. 입력:

- [필수] 파티션을 식별하는 파티션 ID (GUID).

- [옵션] 응용 프로그램 매니페스트 정책을 재정의하는 데 사용되는 응용 프로그램 상태 정책.

- [옵션] 관심 있는 엔터티를 지정하고 결과에 반환되어야 하는 이벤트 및 복제본에 대한 필터(예: 오류만 또는 경고 및 오류). 모든 이벤트 및 복제본은 필터에 관계 없이 엔터티 집계된 상태 평가에 사용됩니다.

### <a name="api"></a>API
API를 통해 파티션 상태를 가져오려면 HealthManager에서 `FabricClient` 를 만들고 [GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx) 메서드를 호출합니다. 옵션인 매개 변수를 지정하려면 [PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx)을 만듭니다.

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
파티션 상태를 가져오기 위한 cmdlet은 [Get-ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx)입니다. 먼저 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) cmdlet을 사용하여 클러스터에 연결합니다.

다음 cmdlet은 **fabric:/WordCount/WordCountService** 서비스의 모든 파티션에 대한 상태를 가져옵니다.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST (영문)
본문에 설명된 상태 정책을 포함하는 [GET 요청](https://msdn.microsoft.com/library/azure/dn707683.aspx) 또는 [POST 요청](https://msdn.microsoft.com/library/azure/dn707680.aspx)를 사용하여 파티션 상태를 가져올 수 있습니다.

## <a name="get-replica-health"></a>복제본 상태 가져오기
상태 저장 서비스 복제본 또는 상태 비저장 서비스 인스턴스의 상태를 반환합니다. 입력:

- [필수] 복제본을 식별하는 파티션 ID(GUID) 및 복제본 ID.

- [옵션] 응용 프로그램 매니페스트 정책을 재정의하는 데 사용되는 응용 프로그램 상태 정책 매개 변수.

- [옵션] 관심 있는 엔터티를 지정하고 결과에 반환되어야 하는 이벤트에 대한 필터(예: 오류만 또는 경고 및 오류). 모든 이벤트는 필터에 관계 없이 엔터티 집계된 상태 평가에 사용됩니다.

### <a name="api"></a>API
API를 통해 복제본 상태를 가져오려면 HealthManager에서 `FabricClient` 를 만들고 [GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx) 메서드를 호출합니다. 고급 매개 변수를 지정하려면 [ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx)을 사용합니다.

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
복제본 상태를 가져오는 cmdlet은 [Get-ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx)입니다. 먼저 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) cmdlet을 사용하여 클러스터에 연결합니다.

다음 cmdlet은 서비스의 모든 파티션에 대한 주 복제본의 상태를 가져옵니다.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST (영문)
본문에 설명된 상태 정책을 포함하는 [GET 요청](https://msdn.microsoft.com/library/azure/dn707673.aspx) 또는 [POST 요청](https://msdn.microsoft.com/library/azure/dn707641.aspx)를 사용하여 복제본 상태를 가져올 수 있습니다.

## <a name="get-deployed-application-health"></a>배포된 응용 프로그램 상태 가져오기
노드 엔터티에 배포된 응용 프로그램의 상태를 반환합니다. 배포된 서비스 패키지 성능 상태를 포함합니다. 입력:

- [필수] 배포된 응용 프로그램을 식별하는 응용 프로그램 이름(URI) 및 노드 이름(문자열)

- [옵션] 응용 프로그램 매니페스트 정책을 재정의하는 데 사용되는 응용 프로그램 상태 정책.

- [옵션] 관심 있는 엔터티를 지정하고 결과에 반환되어야 하는 이벤트 및 배포된 서비스 패키지에 대한 필터(예: 오류만 또는 경고 및 오류). 모든 이벤트 및 배포된 서비스 패키지는 필터에 관계 없이 엔터티 집계된 상태 평가에 사용됩니다.

### <a name="api"></a>API
API 통해 노드에 배포된 응용 프로그램의 상태를 가져오려면 HealthManager에서 `FabricClient` 를 만들고 [GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx) 메서드를 호출합니다. 옵션인 매개 변수를 지정하려면 [DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx)을 사용합니다.

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
배포된 응용 프로그램 상태를 가져오기 위한 cmdlet은 [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx)입니다. 먼저 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) cmdlet을 사용하여 클러스터에 연결합니다. 응용 프로그램이 배포되는 위치를 확인하려면 [Get ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) 를 실행하고 배포된 응용 프로그램 자녀를 살펴봅니다.

다음 cmdlet은 **_Node_2**에 배포된 **fabric:/WordCount** 응용 프로그램의 상태를 가져옵니다.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST (영문)
본문에 설명된 상태 정책을 포함하는 [GET 요청](https://msdn.microsoft.com/library/azure/dn707644.aspx) 또는 [POST 요청](https://msdn.microsoft.com/library/azure/dn707688.aspx)를 사용하여 배포된 응용 프로그램 상태를 가져올 수 있습니다.

## <a name="get-deployed-service-package-health"></a>배포된 서비스 패키지 상태 가져오기
배포된 서비스 패키지 엔터티의 상태를 반환합니다. 입력:

- [필수] 배포된 서비스 패키지를 식별하는 응용 프로그램 이름(URI), 노드 이름(문자열) 및 서비스 매니페스트 이름(문자열).

- [옵션] 응용 프로그램 매니페스트 정책을 재정의하는 데 사용되는 응용 프로그램 상태 정책.

- [옵션] 관심 있는 엔터티를 지정하고 결과에 반환되어야 하는 이벤트에 대한 필터(예: 오류만 또는 경고 및 오류). 모든 이벤트는 필터에 관계 없이 엔터티 집계된 상태 평가에 사용됩니다.

### <a name="api"></a>API
API 통해 배포된 서비스 패키지 상태를 가져오려면 HealthManager에서 `FabricClient` 를 만들고 [GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx) 메서드를 호출합니다. 옵션인 매개 변수를 지정하려면 [DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx)을 사용합니다.

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
배포된 서비스 패키지 상태를 가져오는 cmdlet은 [Get-ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx)입니다. 먼저 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) cmdlet을 사용하여 클러스터에 연결합니다. 응용 프로그램이 배포되는 위치를 확인하려면 [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) 를 실행하고 배포된 응용 프로그램을 살펴봅니다. 응용 프로그램에 어떤 서비스 패키지가 있는지 보려면 [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx) 출력에 배포된 서비스 패키지 자녀를 살펴봅니다.

다음 cmdlet은 **_Node_2**에 배포된 **fabric:/WordCount** 응용 프로그램의 **WordCountServicePkg** 서비스 패키지 상태를 가져옵니다. 엔터티에 성공적인 서비스 패키지 및 진입점 활성화와 성공적인 서비스 유형 등록을 위한 **System.Hosting** 보고서가 있습니다.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST (영문)
본문에 설명된 상태 정책을 포함하는 [GET 요청](https://msdn.microsoft.com/library/azure/dn707677.aspx) 또는 [POST 요청](https://msdn.microsoft.com/library/azure/dn707689.aspx)를 사용하여 배포된 서비스 패키지 상태를 가져올 수 있습니다.

## <a name="health-chunk-queries"></a>상태 청크 쿼리
상태 청크 쿼리는 입력 필터당 여러 수준의 클러스터 자식(재귀적)을 반환할 수 있습니다. 고유 식별자 또는 다른 그룹 식별자 및/또는 상태에 의해 식별되고 반환될 자식을 표현하도록 많은 융통성을 허용하는 고급 필터를 지원합니다. 항상 첫 번째 수준 자식이 포함되는 상태 명령과는 다르게, 기본적으로 자식이 포함되지 않습니다.

[상태 쿼리](service-fabric-view-entities-aggregated-health.md#health-queries) 는 필요한 필터마다 지정된 엔터티의 첫 번째 수준 자식만 반환합니다. 자식의 자식을 가져오려면 관심이 있는 각 엔터티에 대해 추가 상태 API를 호출해야 합니다. 마찬가지로, 특정 엔터티의 상태를 가져오려면 원하는 각 엔터티에 대해 하나의 상태 API를 호출해야 합니다. 청크 쿼리 고급 필터링을 사용하면 쿼리 하나로 원하는 여러 항목을 요청할 수 있으므로 메시지 크기와 메시지의 수를 최소화할 수 있습니다.

청크 쿼리의 값은 한 번의 호출로 더 많은 클러스터 엔터티(필요한 루트에서 시작하여 잠재적으로 모든 클러스터 엔터티)의 상태 정보를 얻을 수 있습니다. 복잡한 상태 쿼리를 다음과 같이 표현할 수 있습니다.

- 오류 시 응용 프로그램만 반환하고, 이러한 응용 프로그램의 경우 경고|오류 시 모든 서비스 포함합니다. 반환된 서비스의 경우 모든 파티션을 포함합니다.

- 이름으로 지정된 4개 응용 프로그램의 상태만 반환합니다.

- 원하는 유형의 응용 프로그램 상태만 반환합니다.

- 노드에 배포된 모든 엔터티를 반환합니다. 모든 응용 프로그램, 지정된 노드에 배포된 모든 응용 프로그램, 해당 노드에 배포된 모든 서비스 패키지가 반환됩니다.

- 오류 시 모든 복제본을 반환합니다. 오류 시 모든 응용 프로그램, 서비스, 파티션 및 복제본만 반환합니다.

- 모든 응용 프로그램을 반환합니다. 지정된 서비스의 경우 모든 파티션을 포함합니다.

현재, 상태 청크 쿼리는 클러스터 엔터티에 대해서만 노출됩니다. 상태 청크 쿼리는 클러스터 상태 청크를 반환하며, 다음을 포함합니다.

- 클러스터 집계 성능 상태.

- 입력 필터를 준수하는 노드의 상태 청크 목록.

- 입력 필터를 준수하는 응용 프로그램의 상태 청크 목록. 각 응용 프로그램 상태 청크는 입력 필터를 준수하는 모든 서비스가 포함된 청크 목록과 필터를 준수하는 모든 배포된 응용 프로그램이 포함된 청크 목록을 포함하고 있습니다. 서비스 자식 및 배포된 응용 프로그램에도 동일한 내용이 적용됩니다. 이러한 방식으로, 요청이 있을 경우 클러스터의 모든 엔터티를 계층적 방식으로 반환할 수 있습니다.

### <a name="cluster-health-chunk-query"></a>클러스터 상태 청크 쿼리
클러스터 엔터티의 상태를 반환하며 필수 자식의 계층적 상태 청크를 포함합니다. 입력:

- [옵션] 노드 및 클러스터 이벤트를 평가하는 데 사용되는 클러스터 상태 정책.

- [옵션] 응용 프로그램 매니페스트 정책을 재정의하는 데 사용되는 상태 정책이 있는 응용 프로그램 상태 정책 맵.

- [옵션] 관심 있는 엔터티를 지정하고 결과에 반환되어야 하는 노드 및 응용 프로그램에 대한 필터. 필터는 엔터티/엔터티 그룹에 한정적으로 적용하거나 해당 수준의 모든 엔터티에 적용할 수 있습니다. 쿼리에서 반환하는 엔터티를 세분화할 수 있도록 필터 목록에 일반 필터 하나 그리고/또는 특정 식별자에 대한 필터가 포함될 수 있습니다. 필터 목록이 비어 있으면 기본적으로 자식이 반환되지 않습니다.
필터에 대한 자세한 내용은 [NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx) 및 [ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx)를 참조하세요. 응용 프로그램 필터는 자식에 대한 고급 필터를 재귀적으로 지정할 수 있습니다.

청크 결과에는 필터를 준수하는 하위 항목이 포함됩니다.

현재, 청크 쿼리 비정상 평가 또는 엔터티 이벤트를 반환하지 않습니다. 이러한 추가 정보는 기존 클러스터 상태 쿼리를 사용하여 얻을 수 있습니다.

### <a name="api"></a>API
클러스터 상태 청크를 가져오려면 해당 **HealthManager**에서 `FabricClient`를 만들고 [GetClusterHealthChunkAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx) 메서드를 호출합니다. 상태 정책 및 고급 필터를 설명하는 [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx) 을 전달할 수 있습니다.

다음 코드는 고급 필터가 포함된 클러스터 상태 청크를 가져옵니다.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
클러스터 상태를 가져오려는 cmdlet은 [Get-ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx)입니다. 먼저 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) cmdlet을 사용하여 클러스터에 연결합니다.

다음 코드는 항상 반환되어야 하는 특정 노드를 제외하고 노드에 오류가 있을 때에만 해당 노드를 가져옵니다.

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

다음 cmdlet은 응용 프로그램 필터가 포함된 클러스터 청크를 가져옵니다.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters

HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks :
                                   TotalCount            : 1

                                   ServiceName           : fabric:/WordCount/WordCountService
                                   HealthState           : Error
                                   PartitionHealthStateChunks :
                                       TotalCount            : 1

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

다음 cmdlet은 노드에 배포된 모든 엔터티를 반환합니다.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 1

                                       ServiceManifestName   : FAS
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

### <a name="rest"></a>REST (영문)
본문에 설명된 상태 정책 및 고급 필터를 포함하는 [GET 요청](https://msdn.microsoft.com/library/azure/mt656722.aspx) 또는 [POST 요청](https://msdn.microsoft.com/library/azure/mt656721.aspx)를 사용하여 클러스터 상태 청크를 가져올 수 있습니다.

## <a name="general-queries"></a>일반 쿼리
일반 쿼리는 지정된 형식의 서비스 패브릭 엔터티 목록을 반환합니다. 이러한 쿼리는 API( **FabricClient.QueryManager**상의 메서드를 통해), PowerShell cmdlet 및 REST를 통해 노출됩니다. 이러한 쿼리는 여러 구성 요소에서 하위 쿼리를 집계합니다. 둘 중 하나는 [Health 스토어](service-fabric-health-introduction.md#health-store)로 각 쿼리 결과에 대해 집계된 상태를 채웁니다.  

> [AZURE.NOTE] 일반 쿼리는 엔터티의 집계된 성능 상태를 반환 하고 풍부한 상태 데이터를 포함하지 않습니다. 엔터티가 비정상이면 상태 쿼리를 따라서 이벤트, 자녀 성능 상태 및 비정상 평가를 포함하는 모든 상태 정보를 가져올 수 있습니다.

일반 쿼리가 엔터티에 대해 알 수 없는 성능 상태를 반환하는 경우 상태 저장소에 해당 엔터티에 대해 완전한 데이터가 없을 수 있습니다. 또한 상태 저장소에 대한 하위 쿼리에 성공하지 못한 것일 수 있습니다(예: 통신 오류가 있거나 상태 저장소가 정체됨). 엔터티에 대한 상태 쿼리를 따릅니다. 네트워크 문제와 같은 하위 쿼리에 일시적인 오류가 발생한 경우 후속 쿼리는 성공할 수 있습니다. 또한 엔터티가 상태 저장소로부터 노출되지 않은 자세한 이유를 제공할 수도 있습니다.

엔터티에 대한 **HealthState** 가 포함된 쿼리는 다음과 같습니다.

- 노드 목록: 클러스터의 목록 노드(페이징)를 반환합니다.
  - API: [FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  - PowerShell: Get-ServiceFabricNode
- 응용 프로그램 목록: 클러스터의 응용 프로그램 목록(페이징)을 반환합니다.
  - API: [FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricApplication
- 서비스 목록: 응용 프로그램의 서비스 목록(페이징)을 반환합니다.
  - API: [FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  - PowerShell: Get-ServiceFabricService
- 파티션 목록: 서비스의 파티션 목록(페이징)을 반환합니다.
  - API: [FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  - PowerShell: Get-ServiceFabricPartition
- 복제본 목록: 파티션의 복제본 목록(페이징)을 반환합니다.
  - API: [FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  - PowerShell: Get-ServiceFabricReplica
- 배포된 응용 프로그램 목록: 노드에 배포된 응용 프로그램의 목록을 반환합니다.
  - API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication
- 배포된 서비스 패키지 목록: 배포된 응용 프로그램에서 서비스 패키지 목록을 반환합니다.
  - API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication

> [AZURE.NOTE] 일부 쿼리는 페이징된 결과를 반환합니다. 반환되는 이러한 쿼리는 [PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx)에서 파생된 목록입니다. 결과가 메시지와 맞지 않으면 한 페이지만 반환되고 열거형이 중지된 위치를 추적하는 ContinuationToken이 반환됩니다. 다음 결과를 얻으려면 계속해서 동일한 쿼리를 호출하고 이전 쿼리의 연속 토큰을 전달해야 합니다.

### <a name="examples"></a>예

다음 코드는 클러스터에서 비정상 응용 프로그램을 가져옵니다.

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

다음 cmdlet은 패브릭:/WordCount 응용 프로그램에 대한 응용 프로그램 세부 정보를 가져옵니다. 성능 상태는 경고입니다.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

다음 cmdlet은 성능 상태 경고로 서비스를 가져옵니다.

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## <a name="cluster-and-application-upgrades"></a>클러스터 및 응용 프로그램 업그레이드
클러스터 및 응용 프로그램의 모니터링된 업그레이드 중 서비스 패브릭이 상태를 점검하여 모든 것이 정상으로 유지되는지 확인합니다. 엔터티가 구성된 상태 정책을 사용한 평가로 비정상인 경우 업그레이드는 다음 작업을 결정하는 업그레이드 관련 정책을 적용합니다. 업그레이드는 사용자 상호 작용을 허용하도록 일시 중지되거나(예: 오류 조건 해결 또는 정책 변경) 이전 버전으로 자동으로 롤백할 수도 있습니다.

*클러스터* 업그레이드 중 클러스터 업그레이드 상태를 가져올 수 있습니다. 업그레이드 상태는 클러스터에서 비정상인 항목을 가리키는 비정상 평가를 포함합니다. 상태 문제로 인해 업그레이드가 롤백되는 경우 업그레이드 상태가 마지막 비정상 이유를 기억합니다. 이 정보는 업그레이드가 롤백되거나 중지된 후에 무엇이 잘못되었는지를 관리자가 조사하는 데 도움이 될 수 있습니다.

마찬가지로, *응용 프로그램* 업그레이드 중에 응용 프로그램 업그레이드 상태에 비정상 평가가 포함됩니다.

다음은 수정된 패브릭:/WordCount 응용 프로그램에 대한 응용 프로그램 업그레이드 상태를 보여줍니다. watchdog에서 복제본 하나에서 오류를 보고했습니다. 상태 검사를 준수하지 않기 때문에 업그레이드가 롤백됩니다.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

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

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

[Service Fabric 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)에 대해 자세히 알아봅니다.

## <a name="use-health-evaluations-to-troubleshoot"></a>상태 평가를 사용하여 문제 해결
클러스터 또는 응용 프로그램에 문제가 있을 때마다 클러스터나 응용 프로그램 상태를 확인하여 무엇이 잘못인지 식별합니다. 비정상 평가는 현재 비정상 상태를 무엇이 트리거했는지에 대한 세부 정보를 제공합니다. 필요한 경우 비정상 자녀 엔터티로 드릴다운하여 근본 원인을 식별할 수 있습니다.

> [AZURE.NOTE] 비정상 평가는 엔터티가 현재 상태로 평가된 첫 번째 이유를 보여 줍니다. 이 상태를 트리거하는 다른 여러 이벤트가 있을 수 있지만 평가에 반영되지 않습니다. 자세한 내용을 알아보려면 클러스터의 모든 비정상 보고서를 산출하는 상태 엔터티를 드릴다운합니다.

## <a name="next-steps"></a>다음 단계
[시스템 상태 보고서를 사용하여 문제 해결](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[사용자 지정 서비스 패브릭 상태 보고서 추가](service-fabric-report-health.md)

[서비스 상태를 보고 및 확인하는 방법](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[로컬로 서비스 모니터링 및 진단](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)



<!--HONumber=Oct16_HO2-->


