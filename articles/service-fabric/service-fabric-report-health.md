<properties
   pageTitle="사용자 지정 서비스 패브릭 상태 보고서 추가"
   description="Azure 서비스 패브릭 상태 엔터티에 사용자 지정 상태 보고서를 보내는 방법을 설명합니다. 고품질 상태 보고서 설계 및 구현에 대한 권장 사항을 제공합니다."
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

# 사용자 지정 서비스 패브릭 상태 보고서 추가
서비스 패브릭은 특정 엔터티의 비정상 클러스터 또는 응용 프로그램 상태에 플래그를 적용하도록 설계된 [상태 모델](service-fabric-health-introduction.md)을 소개합니다. 이것은 **상태 보고자**(시스템 구성 요소 및 Watchdog)를 사용하여 수행됩니다. 쉽고 빠른 진단을 목표로 합니다. 서비스 작성자는 상태를 미리 고려해야 합니다. 상태에 영향을 줄 수 있는 모든 조건은 보고되어야 하며, 특히 루트에 가깝게 문제를 표시하는데 도움이 되는 경우에 대해서는 보고가 이루어져야 합니다. 서비스가 클라우드 규모로 시작되어 실행되기만 하면 이것은 많은 디버깅과 조사를 덜어줄 수 있습니다.

서비스 패브릭 보고자는 식별된 관심 조건을 모니터링합니다. 보고자는 각자 로컬 보기를 기반으로 이러한 조건에 대한 정보를 보고합니다. [상태 저장소](service-fabric-health-introduction.md#Health-Store)는 엔터티가 전체적으로 정상인지를 판단하기 위하여 모든 보고자가 보낸 상태 데이터를 수집합니다. 모델은 다양하고 유연하며 사용이 쉽도록 계획됩니다. 상태 보고서의 품질은 클러스터 상태 보기의 정확성을 결정합니다. 비정상 이슈를 잘못 나타내는 잘못된 긍정은 업그레이드 또는 복구 서비스 또는 경고 메커니즘과 같이 상태 데이터를 사용하는 다른 서비스에 부정적인 영향을 미칠 수 있습니다. 따라서 최선의 방식으로 관심 조건을 포착하는 보고서를 제공하기 위해서는 몇 가지를 고려해야 합니다.

상태 보고를 설계하고 구현하기 위하여 Watchdog 및 시스템 구성 요소는 다음을 수행해야 합니다.

- 관심 있는 조건, 해당 조건을 모니터링하는 방식, 클러스터/응용 프로그램 기능에 미치는 영향을 정의합니다. 이것은 상태 보고서 속성 및 성능 상태를 정의합니다.

- 보고서가 적용되는 [엔터티](service-fabric-health-introduction.md#health-entities-and-hierarchy)를 정의합니다.

- 보고를 서비스 내에서 수행할지, 내부 또는 외부 Watchdog에서 수행할지 위치를 정의합니다.

- 보고자를 식별하는데 사용할 소스를 정의합니다.

- 주기적 또는 전환기 중에서 보고 전략을 선택합니다. 주기적을 선택하면 코드가 더 간단하기 때문에 오류가 발생할 가능성이 낮아지므로 주기적 방식을 권장합니다.

- 비정상 조건에 대한 보고서를 상태 저장소에 보관할 기간과 보고서를 지우는 방식을 결정합니다. 이것은 만료 동작을 기반으로 존속 및 제거를 위한 보고 시간을 정의합니다.

위에 언급된 바와 같이, 보고가 가능한 위치는 다음과 같습니다.

- 모니터링되는 서비스 패브릭 서비스 복제본.

- 서비스 패브릭 서비스로 배포되는 내부 Watchdog. 예: 조건 및 문제 보고서를 모니터링하는 서비스 패브릭 상태 비저장 서비스. Watchdog는 모든 노드에 배포되거나 모니터링되는 서비스로 규합될 수 있습니다.

- 서비스 패브릭 노드에서 실행되지만 서비스 패브릭 서비스로 구현되지 **않는** 내부 Watchdog.

- 서비스 패브릭 클러스터 **외부**의 리소스를 조사하는 외부 Watchdog. 예: 모니터링 서비스 같은 Gomez.

> [AZURE.NOTE]기본적으로, 클러스터는 시스템 구성 요소에 의해 전송되는 상태 보고서로 채워집니다. 추가 정보는 [시스템 상태 보고서를 사용하여 문제 해결](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)을 참조하십시오. 사용자 보고서는 시스템에 의해 이미 생성된 [상태 엔터티](service-fabric-health-introduction.md#health-entities-and-hierarchy)로 보내야 합니다.

상태 보고 디자인이 명확하면 상태 보고서 보내기가 쉽습니다. Powershell 또는 REST를 통해 FabricClient.HealthManager.ReportHealth를 사용하여 API를 통해 수행할 수 있습니다. 내부적으로 모든 메서드는 패브릭 클라이언트 내에 포함된 상태 클라이언트를 사용합니다. 향상된 성능에 대한 배치 보고서를 위한 구성 노브가 있습니다.

> [AZURE.NOTE]상태 보고는 동기화이며 클라이언트 쪽의 유효성 검사 작업만을 나타냅니다. 보고서가 상태 클라이언트에 의해 수용된다고 해도, 저장소에 적용된다는 의미는 아닙니다. 보고서는 비동기적으로 전송될 것이며, 다른 보고서와 함께 일괄 처리될 가능성이 있고, 서버에서의 처리가 실패(예: 부실 시퀀스 번호 즉, 보고서가 적용되어야 하는 엔터티가 삭제되는 등)할 수 있습니다.

## 상태 클라이언트
상태 보고서는 패브릭 클라이언트 내에 있는 상태 클라이언트를 사용하여 상태 저장소로 전송됩니다. 상태 클라이언트는 다음과 같이 구성될 수 있습니다.

- HealthReportSendInterval. 보고서가 클라이언트에 추가되는 시간과 보고서가 상태 저장소로 전송되는 시간 사이의 지연 간격입니다. 이것은 성능 향상을 위하여, 각각의 보고서 별로 메시지를 전송하는 대신 보고서를 하나의 메시지로 일괄 처리하는데 사용됩니다. 기본값: 30초.

- HealthReportRetrySendInterval. 상태 클라이언트가 축적된 상태 보고서를 상태 저장소에 재전송하는 간격입니다. 기본값: 30초.

- HealthOperationTimeout. 상태 저장소로 전송되는 보고 메시지에 대한 시간 제한입니다. 메시지 시간이 초과되면 상태 클라이언트는 상태 저장소에서 보고서 처리를 확인할 때까지 재시도합니다. 기본값: 2분.

> [AZURE.NOTE]보고서가 일괄 처리되는 경우, 패브릭 클라이언트는 HealthReportSendInterval에서 전송된 것을 확인하는 동안 유지되어야 합니다. 일시적인 오류로 인하여 메시지가 손실되거나 상태 저장소가 메시지를 적용할 수 없는 경우, 패브릭 클라이언트는 다시 시도할 기회를 부여하기 위하여 더 오래 유지되어야 합니다.

클라이언트에서의 버퍼링은 보고서의 고유성을 고려합니다. 예를 들어 특정한 악성 보고자가 동일한 엔터티의 동일한 속성에 대해 초 당 100개의 보고서를 보낸다면, 해당 보고서는 최신 버전으로 교체됩니다. 그러한 보고서는 많아야 한 개가 클라이언트 큐에 존재합니다. 일괄 작업이 구성되면 상태 저장소에 전송되는 보고서의 개수는 전송 간격 당 하나뿐이며, 엔터티의 최신 상태를 반영하는 맨 마지막으로 추가된 보고서입니다. 모든 구성 매개 변수는 상태 관련 엔터티에 대해 원하는 값으로 FabricClientSettings를 전달하는 방식으로 FabricClient를 생성할 때 지정될 수 있습니다.

다음 코드는 패브릭 클라이언트를 생성하고 보고서가 추가되자마자 전송되도록 지정합니다. 재시도가 가능한 오류가 발생하거나 시간이 초과되는 경우, 40초마다 재시도가 이뤄집니다.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Powershell을 통해 클러스터에 대한 연결을 생성하는 경우, 동일한 매개 변수가 지정될 수 있습니다. 다음 코드는 로컬 클러스터에 대한 연결을 시작합니다.

```powershell
PS C:> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

> [AZURE.NOTE]권한이 없는 서비스가 클러스터 내의 엔터티에 대한 상태를 보고할 수 없도록 하려면, 보안이 확인된 클라이언트에서 보내는 요청만을 받아들이도록 서버를 구성합니다. FabricClient를 통해 보고가 이뤄지기 때문에, 인증서 인증 또는 Kerberos 같은 클러스터와 통신을 할 수 있으려면 FabricClient에서 보안을 사용해야 합니다.

## 상태 보고 설계
고품질 보고서를 생성하는 첫 단계는 서비스 상태에 영향을 미칠 수 있는 조건을 식별하는 것입니다. 문제가 발생하기 전에, 서비스 또는 클러스터 내에서 문제 발생을 경고하는데 도움이 되는 모든 조건은 잠재적으로 수십억 달러를 절약할 수 있습니다. 중지 시간을 줄이고, 문제 조사와 회복에 소요되는 야간 시간을 줄이고, 고객 만족을 높여줍니다.

조건이 확인되면, Watchdog 작성자는 오버헤드와 유효성 사이에서 최고의 균형을 잡기 위한 최선의 모니터링 방법을 알아 내야 합니다. 예를 들어, 공유 위치에서 임시 파일을 사용하여 복잡한 계산을 하는 서비스가 있습니다. Watchdog는 공유 위치의 공간을 충분히 확보하기 위하여 공유 위치를 모니터링할 수 있습니다. 파일/디렉토리 변경에 대한 알림에 주의를 기울일 수 있습니다. 중요한 임계값에 도달하면 경고를, 공유 공간이 꽉 차면 오류를 보고할 수 있습니다. 경고가 보고되면, 복구 시스템이 공유 공간에서 오래된 파일 정리를 시작할 수 있습니다. 오류가 보고되면, 복구 시스템은 서비스 복제본을 다른 노드로 이동할 수 있습니다. 상태에 대한 조건을 묘사하는 방식, 정상 또는 비정상(경고 또는 오류)으로 간주될 수 있는 조건의 상태에 유의해야 합니다.

모니터링 세부 정보가 설정되면, Watchdog 작성자는 Watchdog 구현 방법을 생각해 내야 합니다. 서비스 내에서 조건을 결정할 수 있으면 Watchdog는 모니터링되는 서비스의 일부가 될 수 있습니다. 예를 들어, 서비스 코드는 로컬 패브릭 클라이언트가 파일 작성을 시도할 때마다 로컬 패브릭 클라이언트를 사용하여 공유 사용 및 보고서를 확인할 수 있습니다. 이러한 접근의 이점은 보고가 간단하다는 것입니다. Watchdog 버그가 서비스 기능에 영향을 미치지 않도록 하기 위하여 주의를 기울여야 합니다.

모니터링되는 서비스 내에서의 보고를 선택할 수 없는 경우도 있습니다. 서비스에 포함된 Watchdog는 조건을 감지하지 못할 수 있습니다. 결정을 내리기 위한 논리(로직)나 데이터가 없거나, 모니터링 조건의 오버헤드가 높거나, 조건이 서비스에 대해 구체적이지 않거나, 서비스 간의 상호 작용에 영향을 미칠 수 있습니다. 클러스터 내의 Watchdog를 별개의 프로세스로 둘 수 있습니다. Watchdog는 주 서비스에 전혀 영향을 미치지 않고 단지 조건과 보고서 모니터링만 합니다. 예를 들어, 이러한 Watchdog는 동일한 응용 프로그램 내에서 상태 비저장 서비스로 구현되어 모든 노드에 배포되거나 서비스와 같은 노드에 배포될 수 있습니다.

때때로, 클러스터 내에서 실행되는 Watchdog를 사용할 수 없는 경우도 있습니다. 사용자가 서비스를 보는 동안 서비스의 가용성이나 기능을 모니터링하는 조건인 경우에는 Watchdog를 사용자 클라이언트와 동일한 위치에 두고, 사용자가 서비스를 호출하는 것과 같은 방식으로 작업을 테스트하는 것이 가장 바람직합니다. 예를 들어, Watchdog를 외부에 두고 서비스에 대한 요청을 생성하면서, 지연 시간과 결과의 정확성(계산기 서비스의 경우 2+2가 합당한 시간 내에 4를 반환하는지)을 확인하도록 할 수 있습니다.

Watchdog 세부 정보가 마무리되면, Watchdog를 고유하게 식별할 소스 ID를 결정합니다. 클러스터 내에 동일한 유형의 Watchdog가 여럿인 경우에는, 서로 다른 엔터티에 보고하거나, 동일한 엔터티에 보고한다면 (보고서가 공존할 수 있도록) 소스 ID나 속성이 다르도록 해야 합니다. 상태 보고서 속성은 모니터링되는 조건을 포착해야 합니다. 예를 들어, 위쪽 사례의 경우 속성은 ShareSize가 될 수 있습니다. 동일한 조건에 다수의 데이터가 적용되는 경우, 보고서가 공존할 수 있도록 하기 위하여 속성에 동적 정보가 포함되어야 합니다. 예를 들어, 여러 개의 공유를 모니터링해야 하는 경우 속성 이름으로 ShareSize-sharename을 사용할 수 있습니다.

> [AZURE.NOTE]상태 저장소는 상태 정보를 유지하는데 사용되지 **말아야** 합니다. 상태와 관련된 정보만 보고되어야 합니다. 엔터티 상태 평가에 영향을 미치는 정보만 보고되어야 합니다. 상태 저장소는 범용 저장소로 설계되지 않았습니다. 상태 저장소는 상태 평가 논리를 사용하여 모든 데이터를 성능 상태로 수집합니다. 상태와 무관한 정보(예, 성능 상태의 보고 상태가 정상임)를 전송해도 수집된 성능 상태에는 영향을 미치지 않지만 상태 저장소의 성능에 부정적인 영향을 미칠 수 있습니다.

다음으로 결정할 내용은 보고할 엔터티를 결정하는 것입니다. 대부분의 경우 조건을 기반으로 명확한 결정이 가능합니다. 최대한 세분화하여 엔터티를 선택합니다. 조건이 파티션 내의 모든 복제본에 영향을 미치는 경우에는 서비스가 아닌 파티션에 대해 보고합니다. 세심한 주의가 필요한 사각 지대가 있습니다. 조건이 하나의 엔터티(예, 복제본)에 영향을 미치지만, 복제본 수명 기간이 지난 후에도 조건에 플래그를 달고자 한다면, 파티션에 보고를 해야 합니다. 그렇게 하지 않으면, 복제본이 삭제된 후에는 그와 관련된 모든 보고서가 저장소에서 삭제됩니다. Watchdog 작성자는 엔터티 및 보고서의 수명도 고려해야 합니다. 보고서가 저장소에서 삭제되어야 하는 시점(예, 엔터티에 보고된 오류가 더 이상 적용되는 않는 시점)도 명확해야 합니다.

이와 같은 요점을 모아놓은 사례를 살펴보겠습니다. 모든 노드에 배포된 마스터 상태 저장 지속 서비스 및 슬레이브 상태 비저장 서비스로 구성된 서비스 패브릭 응용 프로그램이 있습니다. 마스터에는 슬레이브에 의해 실행되는 명령을 포함하는 처리 큐가 있습니다. 슬레이브는 들어오는 요청을 수행하고 ACK를 반환합니다. 모니터링이 가능한 한 가지 조건은 마스터 처리 큐 길이입니다. 마스터 큐 길이가 임계값에 도달하면, 슬레이브가 부하를 처리할 수 없다는 경고를 보고합니다. 큐가 최대 길이에 도달하고 명령이 삭제되면, 서비스를 복구할 수 없다는 오류를 보고합니다. 보고서는 "QueueStatus" 속성에 대한 보고서입니다. Watchdog는 서비스 내에 있으며 마스터 주 복제본에 주기적으로 전송됩니다. TTL이 2분이며 30초마다 주기적으로 전송됩니다. 주 복제본이 다운되면, 보고서는 저장소에서 자동으로 삭제됩니다. 서비스 복제본이 실행 중이지만 교착 상태에 있거나 다른 문제가 있으면 보고서는 상태 저장소에서 만료되고 엔터티는 오류 상황에서 평가됩니다.

모니터링할 수 있는 다른 조건은 작업 수행 시간입니다. 마스터는 작업 유형을 기반으로 슬레이브에 작업을 분배합니다. 설계 내용에 따라서 마스터는 작업 상태를 슬레이브에 폴링하거나, 작업 완료 후 슬레이브가 ACK를 반환할 때까지 기다릴 수 있습니다. 후자의 경우, 슬레이브가 죽거나 메시지가 손실되는 상황을 감지하기 위하여 반드시 주의를 기울여야 합니다. 한 가지 방법은 마스터가 동일한 슬레이브에 상태를 반환하는 PING 요청을 보내는 것입니다. 상태가 수신되지 않으면, 실패를 고려하여 작업 일정을 다시 세웁니다. 이것은 작업을 멱등 상태로 가정합니다. 작업이 특정 시간 t1(예: 10분) 내에 완료되지 않으면 모니터링된 조건을 경고로 해석하고, t2(예: 20분) 내에 완료되지 않으면 오류로 해석할 수 있습니다. 이러한 보고는 여러 가지 방법으로 수행될 수 있습니다.

- 마스터 주 복제본은 주기적으로 자체 보고합니다. 큐 내의 모든 보류 작업에 대해 하나의 속성을 둘 수 있습니다. 하나 이상의 작업에 시간이 많이 걸리면, "PendingTasks" 속성에 대해 경고 또는 오류 중 합당한 항목을 보고합니다. 보류 작업이 없거나 모든 작업이 막 시작된 경우에는 OK를 보고합니다. 작업이 지속되기 때문에 주 복제본이 다운되면 새로 승격된 주 복제본이 계속 알맞게 보고할 수 있습니다.

- (클라우드 또는 외부에 존재하는) 다른 Watchdog 프로세스는 작업 완료 여부를 보기 위하여 (원하는 작업 결과를 기반으로 외부에서) 작업을 확인합니다. 임계값을 준수하지 않으면, 마스터 서비스를 보고합니다. 각 작업을 보고하고 작업 식별자(예: PendingTask+taskid)를 포함합니다. 비정상 상태만 보고합니다. TTL을 몇 분 정도로 설정하고 만료되면 보고서가 제거되도록 표시해 두어, 확실히 제거되도록 합니다.

- 작업을 수행하는 슬레이브는 작업 수행에 예상보다 긴 시간이 소요되면 보고합니다. "PendingTasks" 속성의 서비스 인스턴스를 보고합니다. 이것은 문제가 있는 서비스 인스턴스를 정확히 나타내지만 인스턴스가 사라지는 상황을 포착하지는 않습니다. 보고서는 그 시점에서 삭제됩니다. 슬레이브가 작업을 완료하고 슬레이브 인스턴스가 저장소에서 보고서를 삭제하면 슬레이브 서비스를 보고할 수 있습니다. 이것은 ACK 메시지가 손실되는 상황을 포착하지 않으며 마스터의 관점에서 작업은 마무리되지 않습니다.

하지만 위에 명시된 상황에서 보고가 수행되며 이것은 상태가 평가될 때 응용 프로그램 상태에서 포착됩니다.

## 주기적 보고 대 전환기 보고
상태 보고 모델을 사용하면 Watchdog에서 주기적으로 또는 전환기에 보고서를 보낼 수 있습니다. 코드가 훨씬 간단하고 오류 가능성이 적은 주기적 보고를 권장합니다. 오류가 있는 보고서를 트리거할 수 있는 버그를 방지하기 위하여, Watchdog는 최대한 간단하게 만들어야 합니다. 잘못된 비정상 보고서는 상태 평가에 영향을 미치고 업그레이드와 같은 상태를 기반으로 하는 시나리오에 영향을 미칩니다. 잘못된 정상 보고서는 클러스터의 문제를 숨기는데 이것은 바람직하지 않습니다.

주기적인 보고를 위해 Watchdog를 타이머와 함께 구현할 수 있습니다. 타이머 콜백이 발생하면, Watchdog는 상태를 확인하고 현재 상태를 기반으로 보고서를 보냅니다. 이전에 어떤 보고서를 전송했는지 볼 필요가 없고, 메시지 전송과 관련한 최적화도 필요하지 않습니다. 상태 클라이언트에는 이것을 돕기 위한 일괄 처리 논리가 있습니다. 상태 클라이언트가 유지되는 한 상태 저장소에 의해 보고서가 ACK될 때까지 또는 Watchdog가 동일한 엔터티, 속성 및 소스에 대한 새 보고서를 생성할 때가지 내부적으로 재시도합니다.

전환기 보고는 꼼꼼한 상태 처리가 필요합니다. Watchdog는 조건을 모니터링하면서 조건이 변경된 경우에만 보고합니다. 장점은 필요한 보고서 개수가 적다는 것입니다. 단점은 Watchdog에 대한 논리가 복잡하다는 것입니다. 조건 또는 보고서를 검사하여 상태 변화를 판단할 수 있도록 조건 또는 보고서가 유지되어야 합니다. 장애 조치 시 이전에 전송되지 않은(큐에 추가된 적은 있지만 상태 저장소에는 아직 보낸 적인 없는) 보고서에 주의를 기울여서 보내야 합니다. 시퀀스 번호는 항상 증가되어야 하며, 그렇지 않은 경우 부실을 이유로 보고서가 거부됩니다. 드물지만 데이터 손실이 발생하는 경우에는 보고자의 상태와 상태 저장소의 상태 사이에 동기화가 필요할 수 있습니다.

## 상태 보고 구현
엔터티와 보고서 세부 사항이 명확해지면, API, Powershell 또는 REST를 통해 상태 보고서 보내기를 수행할 수 있습니다.

### API
API를 통해 보고하려면, 사용자는 보고하려는 엔터티에 맞는 상태 보고서를 생성하여 상태 클라이언트에 제공해야 합니다.

다음은 클러스터 내의 Watchdog에서 보내는 주기적 보고서의 사례입니다. 감시자는 노드 내에서 외부 리소스를 액세스할 수 있는지 확인합니다. 리소스는 응용 프로그램 내의 서비스 매니페스트에 필요합니다. 리소스를 사용할 수 없는 경우, 응용 프로그램 내 다른 서비스가 제대로 작동할 수 있습니다. 따라서, 보고서는 배포된 서비스 패키지 엔터티에 주기적으로 30초마다 전송됩니다.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snipet brevity.
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### Powershell
사용자는 Send-ServiceFabric*EntityType*HealthReport로 상태 보고서를 보낼 수 있습니다.

다음은 노드의 CPU 값에 대한 주기적인 보고의 사례입니다. 보고서는 30초마다 전송되어야 하며, TTL 2분이 여기에 포함됩니다. 보고서가 만료되면 보고자에 문제가 있다는 의미이며 따라서 노드가 오류로 평가됩니다. CPU가 임계값을 초과하면 보고서의 성능 상태는 경고가 되고, CPU가 임계값이 구성된 시간보다 길게 초과하면 오류로 보고됩니다. 그렇지 않으면, 보고자는 OK를 보냅니다.

```powershell
PS C:> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

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

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

다음 예는 복제본에 대한 일시적인 경고를 보고합니다. 처음에는 관련 서비스에 대한 파티션 ID와 복제본 ID를 갖고, PowershellWatcher에서 ResourceDependency 속성에 대한 보고서를 보냅니다. 보고서는 2분간만 관여한 후 저장소에서 자동으로 삭제됩니다.

```powershell
PS C:> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

## 다음 단계

상태 데이터를 기반으로 서비스 작성자 및 클러스터/응용 프로그램 관리자는 정보를 소비하는 방식에 대해 생각할 수 있습니다. 예를 들어, 서비스 작성자는 중단이 유발되기 전에 심각한 문제를 포착하기 위하여 성능 상태를 기반으로 경고를 설정할 수 있습니다. 자동으로 문제를 해결하기 위한 자동 복구 시스템을 설정할 수 있습니다.

[서비스 패브릭 상태 모니터링 소개](service-fabric-health-introduction.md)

[서비스 패브릭 상태 보고 보는 방법](service-fabric-view-entities-aggregated-health.md)

[문제 해결을 위한 시스템 상태 보고서 사용](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[로컬에서 서비스를 모니터링 및 진단하는 방법](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)
 

<!---HONumber=July15_HO2-->