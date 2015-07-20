<properties
   pageTitle="신뢰할 수 있는 행위자로 시작하기 | Microsoft Azure"
   description="이 자습서는 서비스 패브릭의 신뢰할 수 있는 서비스를 사용하여 표준 HelloWorld 서비스를 생성, 디버깅 및 배포하는 과정을 단계별로 안내합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/17/2015"
   ms.author="claudioc"/>

# 신뢰할 수 있는 행위자: 표준 HelloWorld 연습 시나리오
이 문서는 서비스 패브릭의 신뢰할 수 있는 행위자에 대한 기본 개념을 설명하고 Visual Studio에서 간단한 HelloWorld 응용 프로그램을 생성, 디버깅 및 배포하는 과정을 안내합니다.

## 설치 및 설정
시작하기 전에 컴퓨터에 서비스 패브릭 개발 환경이 설정되어 있는지 확인합니다. 개발 환경을 설정하는 방법에 대한 자세한 지침은 [여기](service-fabric-get-started.md)를 참조하세요.

## 기본 개념
신뢰할 수 있는 행위자를 시작하기 위해서는 4가지 기본 개념을 이해해야 합니다.

* **행위자 서비스**. 신뢰할 수 있는 행위자는 서비스 패브릭 인프라에 배포할 수 있는 서비스에 패키지되어 있습니다. 서비스는 하나 이상의 행위자를 호스트할 수 있습니다. 나중에 서비스당 여러 행위자일 때와 하나일 때의 장단점에 대해 좀더 자세히 알아보도록 하겠습니다. 지금은 행위자를 하나만 구현해야 한다고 가정해 보겠습니다.
* **행위자 인터페이스**. 행위자 인터페이스는 행위자의 공용 인터페이스를 정의하는 데 사용됩니다. 행위자 모델 관련 용어에서 행위자가 프로세스를 이해할 수 있는 메시지의 유형을 정의합니다. 행위자 인터페이스는 다른 행위자 또는 클라이언트 응용 프로그램에서 메시지를 행위자에게 '보내는'(비동기) 데 사용됩니다. 알 수 있듯이, HelloWorld 행위자가 IHelloWorld 인터페이스를 구현할 수 있지만 다양한 메시지/기능을 정의하는 ILogging 인터페이스도 구현할 수 있으므로, 신뢰할 수 있는 행위자는 여러 인터페이스를 구현할 수 있습니다.
* **행위자 등록**. 행위자 서비스에서 서비스 패브릭이 새로운 형식을 인식하고 이를 사용하여 새로운 행위자를 만들 수 있도록 행위자 유형을 등록해야 합니다.
* **ActorProxy 클래스**. ActorProxy 클래스는 행위자에 바인딩하는 데 사용되며 해당 인터페이스를 통해 노출되는 메서드를 호출하는 데 사용됩니다. ActorProxy 클래스는 다음 두 가지 중요한 기능을 제공합니다.
	* 이름 확인: 클러스터에서 행위자를 찾을 수 있습니다(호스트되는 클러스터의 노드 찾기).
	* 오류 처리: 메서드 호출을 다시 시도하고 행위자를 클러스터의 다른 노드로 재배치해야 하는 경우 등의 오류가 발생한 후 행위자의 위치를 다시 파악할 수 있습니다.

## Visual Studio에서 새 프로젝트 만들기
Visual Studio용 서비스 패브릭 도구를 설치한 후 새 프로젝트 형식을 만들 수 있습니다. 새 프로젝트 형식은 새 프로젝트 대화 상자의 '클라우드' 범주 아래에 표시됩니다.


![][1]


HelloWorld 프로젝트의 경우, 서비스 패브릭 행위자 서비스를 사용하도록 합니다.

솔루션을 만들고 나면 다음과 같은 구조로 표시됩니다.

![][2]

## 신뢰할 수 있는 행위자 기본 구성 요소

일반적으로 신뢰할 수 있는 행위자 솔루션은 다음 3개 프로젝트로 구성됩니다.

* 응용 프로그램 프로젝트(HelloWorldApplication). 배포를 위해 모든 서비스를 함께 패키지하는 프로젝트입니다. 응용 프로그램을 관리하기 위한 ApplicationManifest.xml 및 PowerShell 스크립트가 포함되어 있습니다.

* 인터페이스 프로젝트(HelloWorld.Interfaces). 행위자에 대한 인터페이스 정의가 포함된 프로젝트입니다. 인터페이스 프로젝트에서 솔루션의 행위자에 의해 사용될 인터페이스를 정의할 수 있습니다.

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld.Interfaces
{
    public interface IHelloWorld : IActor
    {
        Task<string> SayHello(string greeting);
    }
}

```

* 서비스 프로젝트(HelloWorld). 행위자를 호스트할 서비스 패브릭 서비스를 정의하는 데 사용되는 프로젝트입니다. 대부분의 경우(ServiceHost.cs)와 행위자의 구현에서 편집해야 할 필요가 없는 일부 상용구 코드가 포함되어 있습니다. 행위자의 구현에는 기본 형식(행위자)에서 파생되는 클래스의 구현이 포함되며 .Interfaces 프로젝트에 정의된 인터페이스를 구현합니다.

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using HelloWorld.Interfaces;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld
{
    public class HelloWorld : Actor, IHelloWorld
    {
        public Task<string> SayHello(string greeting)
        {
            return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
        }
    }
}

```

행위자 서비스 프로젝트에는 서비스 패브릭 서비스를 만드는 코드가 포함되어 있으며, 새로운 행위자를 인스턴스화하는 데 사용할 수 있도록 서비스 정의에 행위자 형식이 등록됩니다.

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(HelloWorld));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
}  

```

Visual Studio에서 새 프로젝트부터 시작하고 하나의 행위자 정의만 있는 경우, 등록은 기본적으로 Visual Studio에서 생성하는 코드에 포함됩니다. 서비스에서 다른 행위자를 정의하는 경우에는 다음을 사용하여 행위자 등록을 추가해야 합니다.

```csharp

fabricRuntime.RegisterActor(typeof(MyNewActor));


```

## 디버그

Visual Studio용 서비스 패브릭 도구는 로컬 컴퓨터에서 디버깅을 지원합니다. F5를 누르면 디버깅 세션을 시작할 수 있습니다. Visual Studio는 로컬 서비스 패브릭 클러스터에서 응용 프로그램을 빌드(필요한 경우), 패키지 및 배포하고 디버거를 연결합니다. ASP.NET 응용 프로그램을 디버깅하는 것과 비슷합니다. 배포 프로세스가 진행되는 동안 출력 창에서 진행률을 볼 수 있습니다.

![][3]

## 응용 프로그램 배포
Visual Studio에서 디버거를 시작하지 않고도 서비스 패브릭 응용 프로그램 프로젝트를 선택한 후 마우스 오른쪽 단추로 클릭하여 로컬 클러스터에서 응용 프로그램을 패키지하고 배포할 수 있습니다.

![][4]

* **배포**: 앱을 패키지하고 배포 프로세스를 시작합니다.
* **배포 제거**: 로컬 클러스터에서 응용 프로그램을 제거하는 데 사용할 수 있습니다.
* **패키지**: 응용 프로그램을 패키지합니다. 이 작업은 Azure 등에서 다른 클러스터에 배포할 응용 프로그램을 준비하는 데 유용할 수 있습니다.

## 다음 단계

[서비스 패브릭 행위자 소개](service-fabric-reliable-actors-introduction.md)



<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
 

<!---HONumber=July15_HO2-->