<properties
   pageTitle="신뢰할 수 있는 서비스 직렬화 | Microsoft Azure"
   description="서비스 패브릭 신뢰할 수 있는 서비스 직렬화에 대한 개념 설명서"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson,tyadam"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="mcoskun"/>

# 신뢰할 수 있는 서비스의 직렬화
신뢰할 수 있는 상태 관리자는 신뢰할 수 있는 여러 개체를 포함할 수 있습니다. 이러한 신뢰할 수 있는 개체의 일부는 기본적으로 제공되는 신뢰할 수 있는 사전 및 신뢰할 수 있는 큐와 같은 일반 데이터 구조일 수 있습니다. 신뢰할 수 있는 일반 데이터 구조이므로 그 안에 들어 있는 일반 개체는 디스크에 복제하여 유지가 가능하도록 직렬화되어야 합니다.

신뢰할 수 있는 상태 관리자는 세 가지 직렬 변환기를 지원합니다. * 사용자 지정 직렬 변환기, 제한된 유형에만 제공되는 * 기본 제공 직렬 변환기, * DataContractSerilizer

신뢰할 수 있는 개체는 한 개체를 직렬화해야 하는 경우 해당 유형에 맞는 직렬 변환기를 신뢰할 수 있는 상태 관리자에 쿼리합니다. 신뢰할 수 있는 상태 관리자는 먼저 해당 입력 유형에 대해 등록된 사용자 지정 직렬 변환기가 있는지 확인합니다. 없으면 기본 제공 직렬 변환기 중 하나로 해당 유형을 직렬화할 수 있는지 확인합니다. 신뢰할 수 있는 상태 관리자는 guid, bool, byte, sbyte, char, decimal, double, float, int, uint, long, ulong, short, ushort 및 string에 대한 기본 제공 직렬 변환기를 제공합니다. 직렬화할 수 없으면 DataContractSerializer가 반환됩니다.

이 문서에서는 사용자 지정 직렬 변환기를 사용하는 시기와 방법을 집중적으로 다룹니다.

## 사용자 지정 직렬 변환기를 사용하는 시기
사용자 지정 직렬 변환기를 사용하는 두 가지 일반적인 이유가 있습니다. * 성능 * 암호화

기본 유형에 포함되지 않는 유형인 경우 DataContractSerializer 대신 사용자 지정 직렬 변환기를 사용하여 성능을 대폭 향상할 수 있습니다. 한 가지 이유로, 사용자 지정 직렬 변환기는 유형 정보를 직렬화할 필요가 없습니다. 서비스 패브릭은 특정 유형에 대한 직렬 변환기에 해당 유형만 제공하여 직렬화 및 역직렬화하도록 보장합니다.

직렬 변환기에서 생성한 직렬화된 데이터는 디스크에 복제되어 그 상태 그대로 유지됩니다. 기밀 데이터의 경우 네트워크 및 디스크의 비트를 암호화하는 것이 좋습니다.

## 사용자 지정 직렬 변환기를 사용하는 방법
특정 유형에 대한 사용자 지정 직렬 변환기를 사용하려면 * 사용자 지정 상태 직렬 변환기를 구현해야 합니다. * 신뢰할 수 있는 서비스에 사용자 지정 상태 직렬 변환기를 등록해야 합니다.

### 사용자 지정 직렬 변환기를 구현하는 방법
사용자 지정 직렬 변환기에서 IStateSerializer<T> 인터페이스를 구현해야 합니다. 이 인터페이스의 두 가지 핵심 메서드는 다음과 같습니다. * T Read(BinaryReader binaryReader); * void Write(T value, BinaryWriter binaryWriter);

첫 번째 메서드는 ReliableObject가 BinaryReader를 사용하여 스트림에서 직렬화된 개체를 읽을 때 사용됩니다. 두 번째 메서드는 그와 반대로 Binary Writer를 사용하여 개체를 스트림에 쓸 때 사용됩니다.

아래는 그에 대한 사용자 지정 클래스 및 직렬 변환기의 예입니다.

```C#
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }
    public short District { get; set; }
    public int Customer { get; set; }
    public long Order { get; set; }
```

```C#
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
    void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
    {
        writer.Write(value.Warehouse);
        writer.Write(value.District);
        writer.Write(value.Customer);
        writer.Write(value.Order);
    }

    OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
    {
        OrderKey value = new OrderKey();
        value.Warehouse = reader.ReadByte();
        value.District = reader.ReadInt16();
        value.Customer = reader.ReadInt32();
        value.Order = reader.ReadInt64();

        return value;
    }

    void IStateSerializer<OrderKey>.Write(OrderKey currentValue, OrderKey newValue, BinaryWriter writer)
    {
        ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
    }

    OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
    {
        return ((IStateSerializer<OrderKey>)this).Read(reader);
    }
}
```
>[AZURE.NOTE]위의 예에서 구현된 읽기 및 쓰기 오버로드는 단순히 상응하는 오버로드를 호출합니다. 아직 제공되지 않는 기능에 다음 두 메서드가 사용될 예정이기 때문입니다.

### 사용자 지정 직렬 변환기를 등록하는 방법
사용자 지정 직렬 변환기를 등록하려면 먼저 모든 사용자 지정 직렬 변환기를 등록할 수 있는 메서드가 필요합니다. 이 메서드는 인수를 가져오면 안 되고 작업을 반환할 수 있어야 합니다. 이 메서드에서 IReliableStateManager.TryAddStateSerializer<T>는 신뢰할 수 있는 서비스에 대한 모든 사용자 지정 직렬 변환기를 등록하는 데 사용되어야 합니다.

```C#
protected Task InitializeStateSerializers()
{
    this.StateManager.TryAddStateSerializer(new OrderKeySerializer());
    return Task.FromResult(false);
}
```

그 다음으로 모든 사용자 지정 상태 직렬 변환기를 등록해야 할 때 신뢰할 수 있는 상태 관리자가 호출하는 대리자로 위의 메서드를 등록해야 합니다. 이 메서드는 로컬 복구가 시작되기 전에 신뢰할 수 있는 서비스 복제가 시작될 때에만 호출됩니다. 디스크에서 직렬화된 데이터를 읽기 위해 직렬 변환기가 필요할 수도 있기 때문입니다. 직렬 변환기가 등록되면 모든 신뢰할 수 있는 개체의 관련 유형에서는 이 직렬 변환기를 사용하여 해당 개체를 직렬화 및 역직렬화합니다.

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            onInitializeStateSerializersEvent : this.InitializeStateSerializers));
}
```
### 버전 관리
서비스 패브릭은 직렬 변환기가 상위 버전 및 하위 버전과 무한히 호환되는 것으로 예상합니다. 기본 제공 직렬 변환기를 사용하는 유형의 경우 서비스 패브릭이 상위 버전 및 하위 버전 호환성을 보장합니다. DataContractSerializer 또는 사용자 지정 직렬 변환기를 사용하는 유형의 경우 사용자가 절대로 변경하면 안 됩니다. DataContract 버전 관리는 [데이터 계약 버전 관리](https://msdn.microsoft.com/library/ms731138.aspx)를 참조하세요. 변경이 필요한 경우 응용 프로그램 수준에서 이전 데이터 버전이 있는 서비스 인스턴스에서 새로운 데이터 버전이 있는 서비스로 상태를 이동해야 합니다.

### 직렬 변환기가 사용되는 시기
 * 신뢰할 수 있는 개체에 쓰기 작업을 수행하면 개체가 직렬화 및 복제되고 로그에 저장됩니다.
 * 작업이 충분히 기록된 후에는 메모리의 최신 데이터가 직렬화되어 디스크의 검사점이 됩니다.
 * 복제를 다시 빌드할 경우 주 복제본에서 디스크의 검사점 파일과 로그의 최신 데이터가 직접 바이트 단위로(즉, 데이터가 다시 직렬화되지 않음) 전송됩니다. 이러한 바이트는 보조 복제본에서 C# 개체로 역직렬화됩니다.
 * 복구 과정에서 검사점 파일 및 최신 데이터 로그가 역직렬화됩니다.
 * 백업 과정에서 검사점 파일 및 최근 로그 데이터가 바이트 단위로 복사됩니다.
 * 복원 과정에서 이전에 백업된 검사점 파일 및 로그 데이터가 제자리에 복사되어 역직렬화됩니다.

## 다음 단계
 * [신뢰할 수 있는 서비스 프로그래밍 모델 고급 사용법](service-fabric-reliable-services-advanced-usage.md)

<!---HONumber=AcomDC_1203_2015-->