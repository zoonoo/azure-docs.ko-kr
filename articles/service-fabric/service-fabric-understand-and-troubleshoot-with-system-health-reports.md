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
   ms.date="01/26/2016"
   ms.author="oanapl"/>

# 시스템 상태 보고서를 사용하여 문제 해결

Azure 서비스 패브릭 구성 요소가 클러스터 내의 모든 엔터티에 대해 즉각적으로 보고합니다. [Health 스토어](service-fabric-health-introduction.md#health-store)는 시스템 보고서를 기반으로 엔터티를 만들고 삭제합니다. 또한 엔터티 상호 작용을 캡처하는 계층 구조에서 보고서를 구성합니다.

> [AZURE.NOTE] 상태 관련 개념을 이해하려면 [서비스 패브릭 상태 모델](service-fabric-health-introduction.md)을 자세히 읽어봅니다.

시스템 상태 보고서는 상태를 통해 클러스터 및 응용 프로그램의 기능 및 플래그 문제에 대한 가시성을 제공합니다. 응용 프로그램 및 서비스의 경우, 시스템 상태 보고서는 서비스 패브릭 관점에서 엔터티가 올바르게 구현되고 동작하는지 확인합니다. 보고서는 응답이 없는 프로세스의 감지 또는 서비스의 비즈니스 논리의 상태 모니터링은 제공하지 않습니다. 사용자 서비스는 논리에 고유한 정보로 건강 데이터를 보강할 수 있습니다.

> [AZURE.NOTE] Watchdogs 상태 보고서는 시스템 구성 요소가 엔터티를 만든 *후*에만 표시됩니다. 엔터티가 삭제되면 Health 스토어가 연결된 모든 상태 보고서를 자동으로 삭제합니다. 엔터티의 새 인스턴스를 만들 때에도 동일합니다.(예를 들어 새로운 서비스 복제 인스턴스가 생성됩니다) 이전 인스턴스와 연결된 모든 보고서는 삭제되고 저장소에서 정리됩니다.

시스템 구성 요소 보고서는 "**System.**" 접두사로 시작되는 원본에 의해 식별됩니다. 잘못된 매개 변수가 있는 보고서가 거부되므로 Watchdogs는 소스에 대해 동일한 접두사를 사용할 수 없습니다. 일부 시스템 보고서를 검토하여 이들이 어떻게 트리거되고 이들이 나타내는 발생 가능한 문제를 수정하는 방법을 파악합니다.

> [AZURE.NOTE] 서비스 패브릭이 클러스터 및 응용 프로그램에서 일어나는 상황에 대한 가시성을 향상시켜주는 관심 조건에 대한 보고서를 계속해서 추가합니다.

## 클러스터 시스템 상태 보고서
클러스터 상태 엔터티가 Health 스토어에서 자동으로 생성되므로 모든 것이 적절하게 작동한다면 시스템 보고서는 없습니다.

### 환경 손실
**System.Federation**은 환경 손실을 감지하면 오류를 보고합니다. 보고서는 개별 노드에서 나오며 노드 ID는 속성 이름에 포함됩니다. 전체 서비스 패브릭 링에서 하나의 환경이 손실되면 일반적으로 두 개의 이벤트를 예상할 수 있습니다.(간격의 양측에서 보고함) 더 많은 환경이 손실된 경우 더 많은 이벤트가 있습니다.

보고서는 전역 임대 시간 제한을 TTL(Time to live)로 지정합니다. 조건이 활성인 한 보고서는 TTL 기간의 절반마다 다시 보내집니다. 이벤트는 만료되면 자동으로 제거되므로 보고 노드가 다운되는 경우에도 Health 스토어에서 올바르게 정리됩니다.

- **SourceId**: System.Federation
- **속성**: **환경**으로 시작되고 노드 정보를 포함합니다.
- **다음 단계**: 환경이 손실된 이유를 조사합니다.(예를 들어 클러스터 노드 간 통신을 확인합니다)

## 노드 시스템 상태 보고서
장애 조치(Failover) 관리자 서비스를 나타내는 **System.FM**은 클러스터 노드에 대한 정보를 관리하는 기관입니다. 모든 노드는 상태를 보여주는 System.FM로부터 하나의 보고서를 가져야 합니다. 노드가 비활성화되면 노드 엔터티가 제거됩니다.

### 노드 위/아래
System.FM은 노드가 링에 조인하는 경우 확인으로 보고합니다.(실행 중) 노드가 링에서 분리하는 경우 오류를 보고합니다.(업그레이드이든 단순히 실패한 것이든 중단되었습니다) Health 스토어에서 작성한 상태 계층이 System.FM 노드 보고서와의 상관관계에 따라, 배포된 엔터티에 대해 조치를 취합니다. 배포된 모든 엔터티의 가상 부모를 노드로 간주합니다. 노드가 다운되거나 보고되지 않거나 노드에 해당 엔터티와 관련된 인스턴스와 다른 인스턴스가 있는 경우 해당 노드에서 배포된 엔터티는 쿼리를 통해 노출되지 않습니다. System.FM에서 노드가 다운되었거나 다시 시작한 것을 보고하면(새로운 인스턴스) Health 스토어가 다운 노드 또는 이전 노드 인스턴스에서만 존재할 수 있는 배포된 엔터티를 자동으로 정리합니다.

- **SourceId**: System.FM
- **속성**: 상태
- **다음 단계**: 업그레이드를 위해 노드가 중지된 경우 업그레이드되면 돌아와야 합니다. 이 경우에 상태를 확인으로 다시 전환해야 합니다. 노드가 다시 돌아오지 않거나 실패하면 문제는 자세한 조사가 필요합니다.

다음은 노드 실행을 위해 상태가 확인인 System.FM 이벤트를 표시합니다.

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
**System.FabricNode**는 노드에 의해 사용되는 인증서가 만료가 가까워질 경우 경고를 보고합니다. 노드당 다음 3가지 인증서가 있습니다. **Certificate\_cluster**, **Certificate\_server** 및 **Certificate\_default\_client**. 적어도 2주 후에 만료될 때 보고서 상태는 확인입니다. 2주 이내에 만료되면 보고서 형식은 경고입니다. 이러한 이벤트의 TTL은 무한대이며 노드가 클러스터를 벗어나면 제거됩니다

- **SourceId**: System.FabricNode
- **속성**: **인증서**로 시작하고 인증서 종류에 대한 자세한 정보를 포함합니다.
- **다음 단계**: 만료가 가까운 경우 인증서를 업데이트합니다.

### 부하 용량 위반
노드 용량 위반이 발견되면 서비스 패브릭 부하 분산 장치에서 경고를 보고합니다.

 - **SourceId**: System.PLB
 - **속성**: **용량**으로 시작합니다.
 - **다음 단계**: 제공된 메트릭을 확인하고 노드에서 현재 용량을 검토합니다.

## 응용 프로그램 시스템 상태 보고서
클러스터 관리자 서비스를 나타내는 **System.CM**은 응용 프로그램에 대한 정보를 관리하는 기관입니다.

### 시스템 상태
응용 프로그램을 만들거나 업데이트할 때 System.CM은 확인을 보고합니다. 저장소에서 제거될 수 있도록 응용 프로그램이 언제 삭제되었는지를 Health 스토어에 알려줍니다.

- **SourceId**: System.CM
- **속성**: 상태
- **다음 단계**: 응용 프로그램이 만들어진 경우 클러스터 관리자 상태 보고서를 포함해야 합니다. 그렇지 않은 경우 쿼리를 발행하여 응용 프로그램의 상태를 확인합니다.(예: PowerShell cmdlet **Get-ServiceFabricApplication -ApplicationName *applicationName***)

다음은 **fabric:/WordCount** 응용 프로그램에 대한 상태 이벤트를 보여줍니다.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None) -DeployedApplicationsHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

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
장애 조치(Failover) 관리자 서비스를 나타내는 **System.FM**은 서비스에 대한 정보를 관리하는 기관입니다.

### 시스템 상태
System.FM은 서비스가 만들어질 때 확인을 보고합니다. 서비스가 삭제될 때 Health 스토어에서 엔터티를 삭제합니다.

- **SourceId**: System.FM
- **속성**: 상태

다음에서 서비스 **fabric:/WordCount/WordCountService**에 상태 이벤트를 보여줍니다.

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
- **속성**: 상태
- **다음 단계**: 서비스 제약 조건 및 배치의 현재 상태를 확인합니다.

## 파티션 시스템 상태 보고서
장애 조치(Failover) 관리자 서비스를 나타내는 **System.FM**은 서비스 파티션에 대한 정보를 관리하는 기관입니다.

### 시스템 상태
System.FM은 파티션이 생성되고 정상적이면 확인을 보고합니다. 파티션이 삭제될 때 Health 스토어에서 엔터티를 삭제합니다.

파티션이 최소 복제본 수 이하인 경우 오류를 보고합니다. 파티션이 최소 복제본 개수 이하는 아니지만 대상 복제본 개수 이하라면 경고를 보고합니다. 파티션이 쿼럼 손실인 경우 System.FM이 오류를 보고합니다.

기타 중요한 이벤트는 재구성이 예상보다 오래 걸리며 빌드가 예상보다 오래 걸리는 경우 경고를 포함합니다. 예상되는 빌드 및 재구성 시간을 서비스 시나리오에 따라 구성할 수 있습니다. 예를 들어 서비스가 SQL 데이터베이스와 같은 테라바이트 상태인 경우 작은 양의 상태를 사용하여 서비스에 걸리는 것보다 긴 시간이 걸립니다.

- **SourceId**: System.FM
- **속성**: 상태
- **다음 단계**: 상태가 확인이 아니라면 일부 복제본이 주 또는 보조에 올바르게 생성되거나 열리고 승격되지 않았을 수 있습니다. 대부분의 경우 근본 원인은 열기 또는 변경 역할 구현에서 서비스 버그입니다.

다음은 정상 파티션을 보여줍니다.

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

다음은 대상 복제본 개수 이하인 파티션의 상태를 보여줍니다. 다음 단계는 어떻게 구성되었는지를 보여주는 파티션 설명을 가져오며 이는 **MinReplicaSetSize**가 두 개이고 **TargetReplicaSetSize**가 일곱 개입니다. 그런 다음 클러스터에 있는 노드 수를 가져옵니다.5 따라서 이 경우 두 개의 복제본을 배치할 수 없습니다.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

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
- **속성**: **ReplicaConstraintViolation**으로 시작

## 복제본 시스템 상태 보고서
재구성 에이전트 구성 요소를 나타내는 **System.RA**는 복제본 상태의 기관입니다.

### 시스템 상태
복제본을 만들 때 **System.RA**는 확인을 보고합니다.

- **SourceId**: System.RA
- **속성**: 상태

다음은 정상 복제본을 보여줍니다.

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

복제본 열기가 구성된 기간(기본: 30분)보다 오래 걸릴 경우 **System.RA**는 경고를 보고합니다. API가 서비스 가용성에 영향을 주는 경우 보고서가 훨씬 더 빠르게 발행됩니다.(구성 가능한 간격, 기본 30초) 복제기 열기 및 서비스 열기에 걸린 시간이 포함됩니다. 열기가 완료되면 속성이 확인으로 변경됩니다.

- **SourceId**: System.RA
- **속성**: **ReplicaOpenStatus**
- **다음 단계**: 성능 상태가 확인이 아닌 경우 복제본의 열기가 예상보다 오래 걸린 이유를 확인합니다.

### 느린 서비스 API 호출
**System.RAP** 및 **System.Replicator**는 사용자 서비스 코드에 대한 호출이 구성된 시간보다 오래 걸리는 경우 경고를 보고합니다. 이 경고는 호출이 완료되면 지워집니다.

- **SourceId**: System.RAP 또는 System.Replicator
- **속성**: 느린 API의 이름입니다. 이 설명은 API가 보류된 시간에 대한 자세한 정보를 제공합니다.
- **다음 단계**: 호출이 예상보다 오래 걸리는 이유를 조사합니다.

다음 예제에서는 그 이유를 파악하기 위해 실행된 조사 단계 뿐만 아니라 쿼럼 손실에서 파티션을 보여줍니다. 복제본 하나는 경고 성능 상태를 가지고 있으므로 상태를 얻을 수 있습니다. 이것은 서비스 작업이 예상보다 오래 걸림을 보여줍니다. System.RAP에서 보고된 이벤트입니다. 이 정보를 받은 후에 다음 단계는 서비스 코드를 살펴보고 조사하는 것입니다. 이 경우, 상태 저장 서비스의 **RunAsync** 구현이 처리되지 않는 예외를 throw합니다. 복제본은 재활용되므로 경고 상태에서 어떤 복제본도 표시되지 않을 수도 있습니다. 상태 가져오기를 다시 시도하고 복제본 ID에서 차이를 찾을 수 있습니다. 경우에 따라 단서를 제공할 수 있습니다.

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
- **속성**: 복제본의 역할에 따라 **PrimaryReplicationQueueStatus** 또는 **SecondaryReplicationQueueStatus**

## DeployedApplication 시스템 상태 보고서
**System.Hosting**은 배포된 엔터티 상의 기관입니다.

### 활성화
System.Hosting은 응용 프로그램이 노드에서 성공적으로 활성화되면 확인을 보고합니다. 그렇지 않으면 오류를 보고합니다.

- **SourceId**: System.Hosting
- **속성**: 롤아웃 버전을 비롯한 활성화
- **다음 단계**: 응용 프로그램이 비정상인 경우 활성화에 실패한 이유를 조사합니다.

다음은 성공적인 활성화를 표시해줍니다.

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
- **속성**: **다운로드:*RolloutVersion***
- **다음 단계**: 노드에서 다운로드에 실패한 이유를 조사합니다.

## DeployedServicePackage 시스템 상태 보고서
**System.Hosting**은 배포된 엔터티 상의 기관입니다.

### 서비스 패키지 활성화
System.Hosting은 노드에서 서비스 패키지 활성화가 성공하면 확인을 보고합니다. 그렇지 않으면 오류를 보고합니다.

- **SourceId**: System.Hosting
- **속성**: 활성화
- **다음 단계**: 활성화가 실패한 이유를 조사합니다.

### 코드 패키지 활성화
**System.Hosting**은 활성화가 성공한 경우 각 코드 패키지에 대해 확인을 보고합니다. 활성화에 실패하는 경우 구성된 대로 경고를 보고합니다. **CodePackage**가 활성화에 실패하거나 구성된 **CodePackageHealthErrorThreshold** 보다 큰 오류와 함께 종료되면 호스팅이 오류를 보고합니다. 서비스 패키지에 여러 코드 패키지가 있다면 각 패키지에 대해 생성된 활성화 보고서가 있습니다.

- **SourceId**: System.Hosting
- **속성**: 접두사 **CodePackageActivation**를 사용하고 코드 패키지 및 진입점의 이름을 **CodePackageActivation:*CodePackageName*:*SetupEntryPoint/EntryPoint***으로 포함합니다.(예를 들어 **CodePackageActivation:Code:SetupEntryPoint**)

### 서비스 유형 등록
서비스 유형을 성공적으로 등록하면 **System.Hosting**이 확인을 보고합니다. 등록이 제시간에 완료되지 않으면 오류를 보고합니다.(**ServiceTypeRegistrationTimeout**을 사용하여 구성된 대로) 서비스 유형이 노드로부터 등록되지 않았다면 런타임이 닫혔기 때문입니다. 호스팅이 경고를 보고합니다.

- **SourceId**: System.Hosting
- **속성**: **ServiceTypeRegistration** 접두사를 사용하고 서비스 유형 이름을 포함합니다.(예: **ServiceTypeRegistration:FileStoreServiceType**)

다음은 정상으로 배포된 서비스 패키지를 보여줍니다.

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
서비스 패키지 다운로드에 실패하면 **System.Hosting**가 오류를 보고합니다.

- **SourceId**: System.Hosting
- **속성**: **다운로드:*RolloutVersion***
- **다음 단계**: 노드에서 다운로드에 실패한 이유를 조사합니다.

### 유효성 검사 업그레이드
**System.Hosting**은 업그레이드 중에 유효성 검사에 실패하거나 노드에서 업그레이드에 실패하면 오류를 보고합니다.

- **SourceId**: System.Hosting
- **속성**: **FabricUpgradeValidation** 접두사를 포함하고 업그레이드 버전을 포함합니다.
- **설명**: 발생한 오류를 가리킵니다.

## 다음 단계
[서비스 패브릭 상태 보고서 보기](service-fabric-view-entities-aggregated-health.md)

[로컬로 서비스 모니터링 및 진단](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0128_2016-->