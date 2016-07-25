<properties
   pageTitle="Reliable Actors 프레임워크의 다형성 | Microsoft Azure"
   description="Reliable Actors 프레임워크에서 .NET 인터페이스 및 형식의 계층 구조를 구축하여 기능 및 API 정의를 다시 사용합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/07/2016"
   ms.author="seanmck"/>

# Reliable Actors 프레임워크의 다형성

Reliable Actors 프레임워크를 사용하면 개체 지향 디자인에 사용하는 동일한 기술을 대부분 사용하여 행위자를 빌드할 수 있습니다. 이러한 다형성 기술 중 하나는 보다 일반화된 부모로부터 형식과 인터페이스를 상속하도록 허용합니다. Reliable Actors 프레임워크의 상속은 일반적으로 몇 가지 추가적인 제약 조건과 함께 .NET 모델을 따릅니다.

## 인터페이스

Reliable Actors 프레임워크에서는 행위자 형식으로 구현될 인터페이스를 하나 이상 정의해야 합니다. 이 인터페이스는 클라이언트가 행위자와의 통신에 사용할 수 있는 프록시 클래스를 생성하는 데 사용됩니다. 모든 인터페이스가 행위자 형식에 의해 구현되고, 인터페이스의 모든 부모가 궁극적으로 IActor로부터 파생되기만 한다면 인터페이스는 다른 인터페이스에서 상속할 수 있습니다. IActor는 행위자에 대한 플랫폼 정의 기본 인터페이스입니다. 도형을 사용한 전형적인 다형성의 예는 다음과 유사한 모양입니다.

![shape 행위자에 대한 인터페이스 계층 구조][shapes-interface-hierarchy]


## 형식

플랫폼에 제공되는 기본 Actor 클래스에서 파생되는 행위자 형식 계층 구조를 만들 수 있습니다. 도형의 경우 상태 기본 `Shape` 형식일 수 있습니다.

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```

`Shape`의 하위 유형은 기본부터 메서드를 재정의할 수 있습니다.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```

행위자 형식의 `ActorService` 특성에 유의하세요. 이 특성은 Reliable Actor 프레임워크가 이런 형식의 행위자를 호스팅하기 위해 서비스를 자동으로 생성하도록 지시합니다. 경우에 따라서 하위 형식과 기능을 공유하는 것만을 목적으로 기본 형식을 만들려고 하지만 구체적인 행위자를 인스턴스화하는 데 전혀 사용되지 못합니다. 이런 경우에는 `abstract` 키워드를 사용하여 그런 형식을 기반으로 행위자를 만들지 않겠다는 것을 나타냅니다.


## 다음 단계

- 안정성, 확장성, 일관적인 상태를 제공하려면 [Reliable Actors 프레임워크에서 서비스 패브릭 플랫폼을 활용하는 방법](service-fabric-reliable-actors-platform.md)을 참조하세요.
- [행위자 수명 주기](service-fabric-reliable-actors-lifecycle.md)에 대해 알아봅니다.

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

<!---HONumber=AcomDC_0713_2016-->