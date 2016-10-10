<properties
   pageTitle="시스템 상태 보고서 문제 해결 | Microsoft Azure"
   description="Azure 서비스 패브릭 구성 요소에서 보낸 상태 보고서와 클러스터 또는 응용 프로그램 문제 해결에 대한 사용을 설명합니다."
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

# 시스템 상태 보고서를 사용하여 문제 해결

Azure 서비스 패브릭 구성 요소가 클러스터 내의 모든 엔터티에 대해 즉각적으로 보고합니다. [Health 스토어](service-fabric-health-introduction.md#health-store)는 시스템 보고서를 기반으로 엔터티를 만들고 삭제합니다. 또한 엔터티 상호 작용을 캡처하는 계층 구조에서 보고서를 구성합니다.

> [AZURE.NOTE] 상태 관련 개념을 이해하려면 [Service Fabric 상태 모델](service-fabric-health-introduction.md)을 자세히 읽어봅니다.

시스템 상태 보고서는 상태를 통해 클러스터 및 응용 프로그램의 기능 및 플래그 문제에 대한 가시성을 제공합니다. 응용 프로그램 및 서비스의 경우, 시스템 상태 보고서는 서비스 패브릭 관점에서 엔터티가 올바르게 구현되고 동작하는지 확인합니다. 보고서는 응답이 없는 프로세스의 감지 또는 서비스의 비즈니스 논리의 상태 모니터링은 제공하지 않습니다. 사용자 서비스는 논리에 고유한 정보로 건강 데이터를 보강할 수 있습니다.

> [AZURE.NOTE] Watchdogs 상태 보고서는 시스템 구성 요소가 엔터티를 만든 *후*에만 표시됩니다. 엔터티가 삭제되면 Health 스토어가 연결된 모든 상태 보고서를 자동으로 삭제합니다. 엔터티의 새 인스턴스를 만들 때에도 동일합니다.(예를 들어 새로운 서비스 복제 인스턴스가 생성됩니다) 이전 인스턴스와 연결된 모든 보고서는 삭제되고 저장소에서 정리됩니다.

시스템 구성 요소 보고서는 "**System.**" 접두사로 시작되는 원본에 의해 식별됩니다. 잘못된 매개 변수가 있는 보고서가 거부되므로 Watchdogs는 소스에 대해 동일한 접두사를 사용할 수 없습니다. 일부 시스템 보고서를 검토하여 이들이 어떻게 트리거되고 이들이 나타내는 발생 가능한 문제를 수정하는 방법을 파악합니다.

> [AZURE.NOTE] 서비스 패브릭이 클러스터 및 응용 프로그램에서 일어나는 상황에 대한 가시성을 향상시켜주는 관심 조건에 대한 보고서를 계속해서 추가합니다.

## 클러스터 시스템 상태 보고서
클러스터 상태 엔터티는 Health 스토어에서 자동으로 만들어집니다. 모든 것이 제대로 작동하면 시스템 보고서는 없습니다.

### 환경 손실
**System.Federation**은 환경 손실을 감지하면 오류를 보고합니다. 보고서는 개별 노드에서 나오며 노드 ID는 속성 이름에 포함됩니다. 전체 Service Fabric 링에서 하나의 환경이 손실되면 일반적으로 두 개의 이벤트를 예상할 수 있습니다(간격의 양측에서 보고함). 더 많은 환경이 손실된 경우 더 많은 이벤트가 있습니다.

보고서는 전역 임대 시간 제한을 TTL(Time to live)로 지정합니다. 조건이 활성인 한 보고서는 TTL 기간의 절반마다 다시 보내집니다. 이벤트는 만료되면 자동으로 제거됩니다. 보고 노드가 다운되었더라도 보고서가 Health 스토어에서 제대로 정리된 것을 만료된 동작이 보장하면 제거합니다.

- **SourceId**: System.Federation
- **속성**: **환경**으로 시작되고 노드 정보를 포함합니다.
- **다음 단계**: 환경이 손실된 이유를 조사합니다.(예를 들어 클러스터 노드 간 통신을 확인합니다)

## 노드 시스템 상태 보고서
장애 조치(Failover) 관리자 서비스를 나타내는 **System.FM**은 클러스터 노드에 대한 정보를 관리하는 기관입니다. 모든 노드는 상태를 보여주는 System.FM로부터 하나의 보고서를 가져야 합니다. 노드 상태가 제거되면 노드 엔터티도 제거됩니다([RemoveNodeStateAsync](https://msdn.microsoft.com/library/azure/mt161348.aspx) 참조).

### 노드 위/아래
System.FM은 노드가 링에 조인하는 경우 확인으로 보고합니다.(실행 중) 노드가 링에서 분리하는 경우 오류를 보고합니다.(업그레이드이든 단순히 실패한 것이든 중단되었습니다) Health 스토어에서 작성한 상태 계층이 System.FM 노드 보고서와의 상관관계에 따라, 배포된 엔터티에 대해 조치를 취합니다. 배포된 모든 엔터티의 가상 부모를 노드로 간주합니다. 엔터티와 연결된 인스턴스와 동일한 인스턴스와 함께 노드가 가동 중인 것으로 System.FM에 의해 보고되면 해당 노드에 배포된 엔터티가 쿼리를 통해 노출됩니다. System.FM에서 노드가 다운되었거나 다시 시작한 것을 보고하면(새로운 인스턴스) Health 스토어가 다운 노드 또는 이전 노드 인스턴스에서만 존재할 수 있는 배포된 엔터티를 자동으로 정리합니다.

- **SourceId**: System.FM
- **Property**: State
- **다음 단계**: 업그레이드를 위해 노드가 중지된 경우 업그레이드되면 돌아와야 합니다. 이 경우에 상태를 확인으로 다시 전환해야 합니다. 노드가 다시 돌아오지 않거나 실패하면 문제는 자세한 조사가 필요합니다.

다음 예제는 노드 실행을 위해 상태가 정상인 System.FM 이벤트를 표시합니다.

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### 인증서가 만료
**System.FabricNode**는 노드에 의해 사용되는 인증서가 만료가 가까워질 경우 경고를 보고합니다. 노드당 **Certificate\_cluster**, **Certificate\_server** 및 **Certificate\_default\_client**의 3가지 인증서가 있습니다. 적어도 2주 후에 만료될 때 보고서 상태는 확인입니다. 2주 이내에 만료되면 보고서 형식은 경고입니다. 이러한 이벤트의 TTL은 무한대이며 노드가 클러스터를 벗어나면 제거됩니다

- **SourceId**: System.FabricNode
- **Property**: **Certificate**로 시작하고 인증서 유형에 대한 추가 정보를 포함합니다.
- **다음 단계**: 만료가 가까운 경우 인증서를 업데이트합니다.

### 부하 용량 위반
노드 용량 위반이 발견되면 서비스 패브릭 부하 분산 장치에서 경고를 보고합니다.

 - **SourceId**: System.PLB
 - **Property**: **Capacity**로 시작합니다.
 - **다음 단계**: 제공된 메트릭을 확인하고 노드에서 현재 용량을 검토합니다.

## 응용 프로그램 시스템 상태 보고서
클러스터 관리자 서비스를 나타내는 **System.CM**은 응용 프로그램에 대한 정보를 관리하는 기관입니다.

### 시스템 상태
응용 프로그램을 만들거나 업데이트할 때 System.CM은 확인을 보고합니다. 저장소에서 제거될 수 있도록 응용 프로그램이 언제 삭제되었는지를 Health 스토어에 알려줍니다.

- **SourceId**: System.CM
- **Property**: State
- **다음 단계**: 응용 프로그램이 만들어진 경우 클러스터 관리자 상태 보고서를 포함해야 합니다. 그렇지 않은 경우 쿼리를 발행하여 응용 프로그램의 상태를 확인합니다(예: PowerShell cmdlet **Get-ServiceFabricApplication -ApplicationName *applicationName***).

다음 예제는 **fabric:/WordCount** 응용 프로그램에 대한 상태 이벤트를 보여 줍니다.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## 서비스 시스템 상태 보고서
장애 조치(failover) 관리자 서비스를 나타내는 **System.FM**은 서비스에 대한 정보를 관리하는 기관입니다.

### 시스템 상태
System.FM은 서비스가 만들어질 때 확인을 보고합니다. 서비스가 삭제될 때 Health 스토어에서 엔터티를 삭제합니다.

- **SourceId**: System.FM
- **Property**: State

다음 예제는 서비스 **fabric:/WordCount/WordCountService**에 상태 이벤트를 보여 줍니다.

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### 위치가 지정되지 않은 복제본 위반
**System.PLB**는 하나 이상의 서비스 복제본에 대한 배치를 찾을 수 없는 경우 경고를 보고합니다. 보고서가 만료되면 제거됩니다.

- **SourceId**: System.FM
- **Property**: State
- **다음 단계**: 서비스 제약 조건 및 배치의 현재 상태를 확인합니다.

다음 예제는 5개의 노드가 있는 클러스터에서 7개의 대상 복제본으로 구성된 서비스에 대한 위반을 보여 줍니다.

```xml
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService


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
                        SequenceNumber        : 131032232425505477
                        SentAt                : 3/23/2016 4:14:02 PM
                        ReceivedAt            : 3/23/2016 4:14:03 PM
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
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## 파티션 시스템 상태 보고서
장애 조치(failover) 관리자 서비스를 나타내는 **System.FM**은 서비스 파티션에 대한 정보를 관리하는 기관입니다.

### 시스템 상태
System.FM은 파티션이 생성되고 정상적이면 확인을 보고합니다. 파티션이 삭제될 때 Health 스토어에서 엔터티를 삭제합니다.

파티션이 최소 복제본 수 이하인 경우 오류를 보고합니다. 파티션이 최소 복제본 개수 이하는 아니지만 대상 복제본 개수 이하라면 경고를 보고합니다. 파티션이 쿼럼 손실인 경우 System.FM이 오류를 보고합니다.

기타 중요한 이벤트는 재구성이 예상보다 오래 걸리며 빌드가 예상보다 오래 걸리는 경우 경고를 포함합니다. 예상되는 빌드 및 재구성 시간을 서비스 시나리오에 따라 구성할 수 있습니다. 예를 들어 서비스에 SQL Database와 같은 테라바이트 상태가 있는 경우 작은 양의 상태가 있는 서비스보다 빌드가 오래 걸립니다.

- **SourceId**: System.FM
- **Property**: State
- **다음 단계**: 상태가 정상이 아니라면 일부 복제본이 주 또는 보조에 올바르게 생성되거나 열리고 승격되지 않았을 수 있습니다. 대부분의 경우 근본 원인은 열기 또는 변경 역할 구현에서 서비스 버그입니다.

다음 예제는 정상 파티션을 보여줍니다.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

다음 예제는 대상 복제본 개수 이하인 파티션의 상태를 보여줍니다. 다음 단계는 파티션이 구성된 방식을 보여 주는 파티션 설명을 가져옵니다. 여기서 **MinReplicaSetSize**는 2이고 **TargetReplicaSetSize**는 7입니다. 그런 다음 클러스터에 있는 노드 수를 가져옵니다.5 따라서 이 경우 두 개의 복제본을 배치할 수 없습니다.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### 복제본 제약 조건 위반
**System.PLB**는 복제본 제약 조건 위반을 감지하고 파티션의 복제본을 배치할 수 없는 경우 경고를 보고합니다.

- **SourceId**: System.PLB
- **Property**: **ReplicaConstraintViolation**으로 시작합니다.

## 복제본 시스템 상태 보고서
재구성 에이전트 구성 요소를 나타내는 **System.RA**는 복제본 상태의 기관입니다.

### 시스템 상태
복제본을 만들 때 **System.RA**는 정상으로 보고합니다.

- **SourceId**: System.RA
- **Property**: State

다음 예제는 정상 복제본을 보여줍니다.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### 복제본 열린 상태
이 상태 보고서에 대한 설명에는 API 호출이 호출된 경우 시작 시간(협정 세계시)이 포함됩니다.

복제본 열기가 구성된 기간(기본: 30분)보다 오래 걸릴 경우 **System.RA**는 경고를 보고합니다. API가 서비스 가용성에 영향을 주는 경우 보고서가 훨씬 더 빠르게 발행됩니다.(구성 가능한 간격, 기본 30초) 측정된 시간에는 복제기 열기 및 서비스 열기에 소요된 시간이 포함됩니다. 열기가 완료되면 속성이 확인으로 변경됩니다.

- **SourceId**: System.RA
- **Property**: **ReplicaOpenStatus**
- **다음 단계**: 성능 상태가 정상이 아닌 경우 복제본의 열기가 예상보다 오래 걸린 이유를 확인합니다.

### 느린 서비스 API 호출
**System.RAP** 및 **System.Replicator**는 사용자 서비스 코드에 대한 호출이 구성된 시간보다 오래 걸리는 경우 경고를 보고합니다. 이 경고는 호출이 완료되면 지워집니다.

- **SourceId**: System.RAP 또는 System.Replicator
- **Property**: 느린 API의 이름입니다. 이 설명은 API가 보류된 시간에 대한 자세한 정보를 제공합니다.
- **다음 단계**: 호출이 예상보다 오래 걸리는 이유를 조사합니다.

다음 예제에서는 쿼럼 손실 내 파티션을 보여주고 이유를 파악하기 위해 실행된 조사 단계를 보여줍니다. 복제본 하나는 경고 성능 상태를 가지고 있으므로 상태를 얻을 수 있습니다. 이것은 서비스 작업이 예상보다 오래 걸림을 보여줍니다. System.RAP에서 보고된 이벤트입니다. 이 정보를 받은 후에 다음 단계는 서비스 코드를 살펴보고 조사하는 것입니다. 이 경우, 상태 저장 서비스의 **RunAsync** 구현이 처리되지 않는 예외를 throw합니다. 복제본은 재활용되므로 경고 상태에서 어떤 복제본도 표시되지 않을 수도 있습니다. 상태 가져오기를 다시 시도하고 복제본 ID에서 차이를 찾을 수 있습니다. 경우에 따라 재시도가 단서를 줄 수 있습니다.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

디버거 하에서 결함이 있는 응용 프로그램을 시작하는 경우 진단 이벤트 창에서 RunAsync로부터 thow된 예외를 표시합니다.

![Visual Studio 2015 진단 이벤트: fabric:/HelloWorldStatefulApplication에서 RunAsync 실패][1]

Visual Studio 2015 진단 이벤트: **fabric:/HelloWorldStatefulApplication**에서 RunAsync 실패

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### 복제 큐 전체
복제 큐가 가득 찬 경우 **System.Replicator**는 경고를 보고합니다. 기본 데이터베이스에서는 하나 이상의 보조 복제본이 작업을 승인하는 속도가 느리기 때문에 일반적으로 발생합니다. 보조 데이터베이스에서는 서비스가 작업에 적용하는 속도가 느릴 때 일반적으로 발생합니다. 큐가 더 이상 가득 차지 않으면 경고가 지워집니다.

- **SourceId**: System.Replicator
- **Property**: 복제본의 역할에 따라 **PrimaryReplicationQueueStatus** 또는 **SecondaryReplicationQueueStatus**

### 느린 이름 지정 작업

**System.NamingService**는 이름 지정 작업이 허용 가능한 시간보다 오래 걸리는 경우 주 복제본에 해당 상태를 보고합니다. 이름 지정 작업의 예는 [CreateServiceAsync](https://msdn.microsoft.com/library/azure/mt124028.aspx) 또는 [DeleteServiceAsync](https://msdn.microsoft.com/library/azure/mt124029.aspx)입니다. FabricClient 아래에서 더 많은 메서드를 찾을 수 있습니다. 예를 들어 [서비스 관리 메서드](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.aspx) 또는 [속성 관리 메서드](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.propertymanagementclient.aspx) 아래입니다.

> [AZURE.NOTE] 이름 지정 서비스는 클러스터의 위치에 서비스 이름을 확인하고 사용자가 서비스 이름 및 속성을 관리할 수 있도록 합니다. 서비스 패브릭 분할된 지속형 서비스입니다. 파티션 중 하나는 모든 Service Fabric 이름 및 서비스에 대한 메타데이터를 포함하는 기관 소유자를 나타냅니다. 서비스 패브릭 이름은 이름 소유자 파티션이라는 다른 파티션에 매핑되므로 서비스는 확장 가능합니다. [이름 지정 서비스](service-fabric-architecture.md)에 대해 자세히 알아봅니다.

이름 지정 작업이 예상보다 오래 걸리는 경우 작업은 *작업을 사용하는 이름 지정 서비스 파티션의 주 복제본*의 경고 보고서로 플래그 지정됩니다. 작업이 성공적으로 완료되면 경고는 해제됩니다. 오류와 함께 작업이 완료되면 상태 보고서는 오류에 대한 세부 정보를 포함합니다.

- **SourceId**: System.NamingService
- **속성**: 접두사 **Duration\_**으로 시작하고 느린 작업 및 작업이 적용되는 서비스 패브릭 이름을 식별합니다. 예를 들어 이름 fabric:/MyApp/MyService에서 서비스 만들기가 너무 오래 걸리는 경우 속성은 Duration\_AOCreateService.fabric:/MyApp/MyService입니다. AO는 이 이름 및 작업에 대한 이름 지정 파티션의 역할을 가리킵니다.
- **다음 단계**: 명명 작업이 실패하는 이유를 확인합니다. 각 작업에는 다른 원인이 있을 수 있습니다. 예를 들어 응용 프로그램 호스트가 서비스 코드의 사용자 버그로 인해 노드에 충돌이 발생하기 때문에 서비스 삭제는 노드에서 중지될 수 있습니다.

다음 예제는 서비스 만들기 작업을 보여 줍니다. 작업이 구성된 기간보다 오래 걸렸습니다. AO는 다시 시도하고 NO로 작업을 보냅니다. NO에서 시간 제한이 있는 마지막 작업을 완료했습니다. 이 경우 동일한 복제본은 AO 및 NO 역할에 대해 주 복제본입니다.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## DeployedApplication 시스템 상태 보고서
**System.Hosting**은 배포된 엔터티에 대한 권한입니다.

### 활성화
System.Hosting은 응용 프로그램이 노드에서 성공적으로 활성화되면 확인을 보고합니다. 그렇지 않으면 오류를 보고합니다.

- **SourceId**: System.Hosting
- **Property**: Activation(롤아웃 버전 포함)
- **다음 단계**: 응용 프로그램이 비정상인 경우 활성화에 실패한 이유를 조사합니다.

다음 예제는 성공적인 활성화를 보여줍니다.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### 다운로드
**System.Hosting**은 응용 프로그램 패키지 다운로드에 실패하면 오류를 보고합니다.

- **SourceId**: System.Hosting
- **Property**: **Download:*RolloutVersion***
- **다음 단계**: 노드에서 다운로드에 실패한 이유를 조사합니다.

## DeployedServicePackage 시스템 상태 보고서
**System.Hosting**은 배포된 엔터티에 대한 권한입니다.

### 서비스 패키지 활성화
System.Hosting은 노드에서 서비스 패키지 활성화가 성공하면 확인을 보고합니다. 그렇지 않으면 오류를 보고합니다.

- **SourceId**: System.Hosting
- **Property**: Activation
- **다음 단계**: 활성화가 실패한 이유를 조사합니다.

### 코드 패키지 활성화
**System.Hosting**은 활성화가 성공한 경우 각 코드 패키지에 대해 확인을 보고합니다. 활성화에 실패하는 경우 구성된 대로 경고를 보고합니다. **CodePackage**가 활성화에 실패하거나 구성된 **CodePackageHealthErrorThreshold**보다 큰 오류와 함께 종료되면 호스팅이 오류를 보고합니다. 서비스 패키지에 여러 코드 패키지가 있다면 각 패키지에 대해 생성된 활성화 보고서가 있습니다.

- **SourceId**: System.Hosting
- **Property**: 접두사 **CodePackageActivation**을 사용하고 코드 패키지 및 진입점의 이름을 **CodePackageActivation:*CodePackageName*:*SetupEntryPoint/EntryPoint***로 포함합니다(예: **CodePackageActivation:Code:SetupEntryPoint**).

### 서비스 유형 등록
서비스 유형을 정상적으로 등록하면 **System.Hosting**이 정상 상태로 보고합니다. 등록이 제시간에 완료되지 않으면 오류를 보고합니다(**ServiceTypeRegistrationTimeout**을 사용하여 구성된 대로). 서비스 유형이 노드로부터 등록되지 않았다면 런타임이 닫혔기 때문입니다. 호스팅이 경고를 보고합니다.

- **SourceId**: System.Hosting
- **Property**: **ServiceTypeRegistration** 접두사를 사용하고 서비스 유형 이름을 포함합니다(예: **ServiceTypeRegistration:FileStoreServiceType**).

다음 예제는 정상으로 배포된 서비스 패키지를 보여줍니다.

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### 다운로드
서비스 패키지 다운로드에 실패하면 **System.Hosting**이 오류를 보고합니다.

- **SourceId**: System.Hosting
- **Property**: **Download:*RolloutVersion***
- **다음 단계**: 노드에서 다운로드에 실패한 이유를 조사합니다.

### 유효성 검사 업그레이드
**System.Hosting**은 업그레이드 중에 유효성 검사에 실패하거나 노드에서 업그레이드에 실패하면 오류를 보고합니다.

- **SourceId**: System.Hosting
- **Property**: **FabricUpgradeValidation** 접두사를 포함하고 업그레이드 버전을 포함합니다.
- **Description**: 발생한 오류를 가리킵니다.

## 다음 단계
[서비스 패브릭 상태 보고서 보기](service-fabric-view-entities-aggregated-health.md)

[서비스 상태를 보고 및 확인하는 방법](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[로컬로 서비스 모니터링 및 진단](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0928_2016-->