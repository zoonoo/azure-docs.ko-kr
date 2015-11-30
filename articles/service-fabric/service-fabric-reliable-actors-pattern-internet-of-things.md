
<properties
   pageTitle="사물 인터넷을 위한 신뢰할 수 있는 Azure 솔루션 | Microsoft Azure"
   description="서비스 패브릭 신뢰할 수 있는 행위자는 HTTPS, MQTT 또는 AMQP 등의 다중 전송을 지원하는 메시징 시스템 프런트 엔드를 결합하는 시스템에서 사용하는 주요 구성 요소입니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# 신뢰할 수 있는 행위자 디자인 패턴: 사물 인터넷
장치 및 클라우드 서비스에서의 기술적 향상과 함께 IoT(사물 인터넷)가 새로운 추세가 되면서 개발자는 시스템 개발에 필요한 핵심 구성 요소를 검토하기 시작했습니다. 다음 다이어그램에서는 서비스 패브릭 신뢰할 수 있는 행위자를 사용한 주요 시나리오를 보여 줍니다.

![][1]

서비스 패브릭 신뢰할 수 있는 행위자는 HTTPS, MQTT 또는 AMQP 등의 다중 전송을 지원하는 메시징 시스템 프런트 엔드를 결합한 후 개별 장치를 나타내는 행위자와 통신하는 시스템에서 사용하는 주요 구성 요소(중간 계층)입니다. 행위자가 상태를 유지 관리할 수 있으므로 스트림 모델링(특히 상태 저장 스트림 처리) 및 장치별 집계가 간단합니다. 데이터를 유지해야 하는 경우 빠른 쿼리를 위해 다른 변수에 데이터의 마지막 N 비트를 간편하게 계속 유지하면서 필요에 따라 또는 타이머에 따라 쉽게 플러시할 수 있습니다. 샘플에서는 행위자 모델에 집중할 수 있도록 행위자가 장치와 통신하도록 허용하는 이벤트/메시징 계층의 세부 정보를 의도적으로 생략했습니다. 일반적으로 함께 구성되는 두 가지 기본적인 시나리오가 있습니다.

* *단일 또는 장치 세트에서 원격 분석 및 상태 데이터를 수집하고 해당 상태를 유지 관리*. 수만 개의 쥐덫(그렇습니다. 실제 고객 시나리오입니다)에서 쥐가 잡혔는지 여부에 상관 없이 데이터를 보낸다고 생각해 보겠습니다. 지역별로 데이터를 집계하고 한 지역에서 쥐가 충분히 잡히면 엔지니어를 보내 장치를 청소합니다. 쥐덫이 행위자 역할을 합니까? 그렇습니다. 지역별 그룹 행위자는 집계 역할을 합니까? 물론입니다.

* *단일 장치 또는 장치 세트에 장치의 동작 및 구성 푸시*. 공급 업체가 소비 패턴과 계절성에 따라 여러 구성을 푸시하는 가정용 태양열 발전 장치를 생각해 보겠습니다.

## 원격 분석 데이터 및 장치 그룹화

우선 그룹화된 장치(수만 대를 생각해 보세요)가 모두 원격 분석 데이터를 해당 연결된 그룹에 보내는 경우를 보겠습니다. 다음 예제에서 고객은 각 지역에 장치를 배포했습니다. 장치가 켜지면 우선 하는 일이 위치, 버전 등과 같은 관련 정보와 함께 ActivateMe 메시지를 보내는 것입니다. 그 다음으로, 장치와 연결된 행위자(장치 ID를 통해)가 로컬로 상태 저장(지속 가능할 수도 있음) 및 그룹 행위자 등록 등 장치에 대한 초기 상태를 설정합니다. 이 경우 시뮬레이션을 위해 임의의 그룹이 할당됩니다.

초기화 프로세스의 일부로, 그룹 행위자 또는 일부 다른 에이전트에서 구성 데이터를 가져와서 장치를 구성할 수도 있습니다. 이 방식을 사용하면 초기에는 장치의 처리 능력이 떨어질 수 있지만 초기화를 통해 "스마트"한 성능을 발휘할 수 있습니다. 이 작업이 완료되고 나면 장치 및 행위자는 원격 분석 데이터를 보내고 처리할 준비가 됩니다.

모든 장치는 정기적으로 행위자에게 해당 원격 분석 정보를 보냅니다. 행위자가 이미 활성화되어 있는 경우는 동일한 행위자가 사용됩니다. 그렇지 않으면 활성화됩니다. 이 시점에서 필요한 경우, 안정적인 저장소에서 상태를 복구할 수 있습니다. 행위자가 원격 분석 정보를 받으면 로컬 변수에 저장됩니다. 이 작업을 통해 샘플이 간소화됩니다. 실제 구현에서는 운영 팀에서 장치 상태 및 성능을 모니터링하고 진단할 수 있도록 외부 저장소에 저장할 가능성이 큽니다. 마지막으로, 장치 행위자가 논리적으로 속해 있는 그룹 행위자에 원격 분석 데이터를 푸시합니다.

## IoT 코드 샘플 – 원격 분석

```csharp
public interface IThing : IActor
{
    Task ActivateMe(string region, int version);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingState
{
	[DataMember]
	public List<ThingTelemetry> _telemetry;
	[DataMember]
	public ThingInfo _deviceInfo;
	[DataMember]
	long _deviceGroupId;
}

public class Thing : StatefulActor<ThingState>, IThing
{

    protected override Task OnActivateAsync()
    {
        State._telemetry = new List<ThingTelemetry>();
        State._deviceGroupId = -1; // not activated
        return base.OnActivateAsync();
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        State._telemetry.Add(telemetry); // saving data at the device level
        if (State._deviceGroupId != -1)
        {
            var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
            return deviceGroup.SendTelemetryAsync(telemetry); // sending telemetry data for aggregation
        }
        return Task.FromResult(true);
    }

    public Task ActivateMe(string region, int version)
    {
        State._deviceInfo = new ThingInfo()
        {
            DeviceId = this.GetPrimaryKeyLong(),
            Region = region,
            Version = version
        };

        // based on the info, assign a group... for demonstration we are assigning a random group
        State._deviceGroupId = new Random().Next(10, 12);

        var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
        return deviceGroup.RegisterDevice(State._deviceInfo);
    }
}
```

그룹 수준에서 샘플을 보면, 그룹 내 장치를 모니터링하고 원격 분석 데이터에 집계하여 엔지니어를 위한 경고를 생성하는 것이 목표입니다. 이 경우, 고객은 일정 수의 장치에서 결함이 발생한 특정 지역에 엔지니어를 보냅니다. 물론 고객은 이동하면서 낭비되는 엔지니어링 시간을 최소화하여 비용을 절감하고자 합니다. 이런 이유 때문에 각 그룹 행위자는 지역별로 결함이 있는 장치의 집계된 상태를 유지 관리합니다. 이 숫자가 임계값에 도달하면 고객은 해당 지역으로 엔지니어를 보내 이들 장치를 교체하거나 수리합니다. 이 작업이 어떻게 수행되는지 보도록 하겠습니다.

## IoT 코드 샘플 – 그룹화 및 집계

```csharp
public interface IThingGroup : IActor
{
    Task RegisterDevice(ThingInfo deviceInfo);
    Task UnregisterDevice(ThingInfo deviceInfo);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingGroupState
{
    [DataMember]
    public List<ThingInfo> _devices;
    [DataMember]
    public Dictionary<string, int> _faultsPerRegion;
    [DataMember]
    public List<ThingInfo> _faultyDevices;
}

public class ThingGroup : StatefulActor<ThingGroupState>, IThingGroup
{

    protected override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return Task.FromResult(true);
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return Task.FromResult(true);
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        if (telemetry.DevelopedFault)
        {
            if (false == _faultsPerRegion.ContainsKey(telemetry.Region))
            {
                State._faultsPerRegion[telemetry.Region] = 0;
            }
            State._faultsPerRegion[telemetry.Region]++;
            State._faultyDevices.Add(_devices.Where(d => d.DeviceId == telemetry.DeviceId).FirstOrDefault());

            if (State._faultsPerRegion[telemetry.Region] > _devices.Count(d => d.Region == telemetry.Region) / 3)
            {
                Console.WriteLine("Sending an engineer to repair/replace devices in {0}", telemetry.Region);
                foreach(var device in State._faultyDevices.Where(d => d.Region == telemetry.Region).ToList())
                {
                    Console.WriteLine("\t{0}", device);
                }
            }
        }

        return Task.FromResult(true);
    }
}
```

보다시피 상당히 단순합니다. 간단한 테스트를 실행한 후 출력은 다음과 같습니다.

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

한편, 장치를 지역별로 그룹화했더라면 좋았을 것이라고 생각할 수 있습니다. 물론, 지역적 위치, 장치 유형, 버전, 테넌트 등과 같이 장치를 그룹화/분할하는 방법은 전적으로 사용자에게 달려 있습니다. 여기서 주의할 점은 장치 상태 대 보고/분석에 있습니다. 이 때문에 명시적인 패턴 일러스트레이션을 만드는 것입니다. 보고 행위자를 빌드하려면 행위자에 대한 쿼리 팬아웃을 피해야 합니다. 불필요한 인스턴스화 및 성능을 단점의 예로 들 수 있습니다. 보고의 경우 두 가지 접근 방식을 사용하는 것이 좋습니다.

* 집계와 같은 그룹 수준 행위자를 사용하여 해당 그룹에 대한 뷰를 유지 관리합니다. 각 행위자가 이 행위자로 적극적으로 관련 데이터만 푸시하도록 합니다. 그런 다음, 이 그룹 수준 행위자를 사용하여 그룹 내 장치의 상태를 볼 수 있습니다.

* 보고를 위해 디자인된 명시적 저장소를 유지 관리합니다. 집계에서는 데이터를 버퍼링하여 정기적으로 보고 저장소에 푸시하여 추가 쿼리 및 분석을 수행할 수 있습니다.

## 장치 작업
지금까지는 잘되고 있지만 이러한 장치에 대한 작업은 어떨까요? 장치의 경우처럼 행위자는 관리자가 장치에 대한 작업을 수행할 수 있도록 운영 관련 인터페이스를 노출할 수 있습니다. 예를 들어, 관리자가 계절/지역 변화에 따라 가정용 태양열 발전 장치 그룹에 새 구성을 푸시하려고 합니다(그렇습니다. 또 다른 실제 시나리오입니다).

여기서 핵심 아이디어는 장치에서 들어오는 원격 분석 등의 데이터를 집계하든 또는 구성 등의 데이터를 여러 장치에 보내든 상관 없이, "Thing" 행위자를 사용하여 각 장치를 세부적으로 제어할 뿐만 아니라 "ThingGroup" 행위자를 사용하여 그룹 작업을 수행한다는 것입니다. 플랫폼에서는 장치 행위자의 배포 및 행위자 간의 메시징을 관리하며, 이것은 개발자에게 완전히 투명합니다.

컴퓨터간(M2M) 통신의 경우, 분산 네트워크 및 그래프 섹션에서 설명한 허브 및 스포크 패턴 또는 행위자-행위자간 직접 상호 작용이 모두 제대로 실행됩니다. M2M 시나리오의 경우, 아래 그림과 같이 서로 메시지를 검색하고 보낼 수 있도록 장치 그룹에 대해 "디렉터리/인덱스" 행위자를 모델링할 수 있습니다.

![][2]

또한 Azure 서비스 패브릭 행위자는 해당 행위자의 수명을 관리합니다. 이런 식으로 생각해 보세요. 장치를 항상 감시하는 방법과 장치에 가끔 연결하는 방법이 있습니다. 행위자가 메모리에서 14시간 마다 연결하여 장치를 관리하도록 하는 이유가 무엇일까요? Azure 서비스 패브릭을 사용하면 원하는 시기 및 원하는 위치의 장치 상태를 저장 및 복구할 수 있습니다.

점점 더 많은 고객이 Azure 서비스 패브릭 행위자를 IoT 구현을 위한 하나의 핵심 구성 요소로 보고 있습니다.

## 다음 단계
[패턴: 스마트 캐시](service-fabric-reliable-actors-pattern-smart-cache.md)

[패턴: 분산 네트워크 및 그래프](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[패턴: 리소스 관리](service-fabric-reliable-actors-pattern-resource-governance.md)

[패턴: 상태 저장 서비스 컴퍼지션](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[패턴: 분산 계산](service-fabric-reliable-actors-pattern-distributed-computation.md)

[일부 패턴 방지](service-fabric-reliable-actors-anti-patterns.md)

[서비스 패브릭 행위자 소개](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=Nov15_HO4-->