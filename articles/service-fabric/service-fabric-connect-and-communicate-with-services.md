<properties
   pageTitle="Azure 서비스 패브릭과 통신 및 연결 | Microsoft Azure"
   description="서비스 패브릭 응용 프로그램과 연결하고 통신하는 방법에 대해 알아보세요."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/21/2015"
   ms.author="kunalds"/>


# 서비스를 사용하여 통신
마이크로 서비스 세계에서는 각각 전문적인 작업을 수행하는 여러 서비스가 모여서 전체 솔루션을 구성합니다. 이러한 마이크로 서비스는 서로 통신하여 종단간 워크플로를 가능하게 합니다. 서비스에 연결되어 통신하는 클라이언트 응용 프로그램도 있습니다. 이 문서에서는 Azure 서비스 패브릭을 사용하면 서비스 패브릭으로 작성하는 서비스와의 통신을 얼마나 간단하게 설정할 수 있는지에 대해 설명합니다.

## 주요 개념
서비스에 대한 통신을 설정하는 데 있어서 주의해야 하는 주요 개념 중 일부를 설명합니다.
### 통신은 클라이언트-서버로 표시됩니다.
서비스 패브릭 통신 API는 같은 클러스터에서 실행되는 두 서비스 간에 상호 작용이 이루어지는 경우에도 상호 작용의 클라이언트-서버 유형을 나타냅니다. 클라이언트나 다른 서비스에서 연결될 대상 서비스는 서버 역할을 하여 들어오는 요청을 수신합니다. 클러스터의 다른 서비스가 될 수 있는 클라이언트는 서버에 연결하여 호출을 보냅니다.
### 서비스의 이동
분산 시스템에서 사용자가 실행하는 서비스 인스턴스는 시간이 지나면 한 컴퓨터에서 다른 컴퓨터로 이동합니다. 리소스 부하 분산, 업그레이드, 장애 조치(Failover), 규모 확장을 위해 부하 메트릭을 구성한 경우 부하 분산을 비롯한 여러 가지 원인 때문에 이 현상이 발생할 수 있습니다. 이 현상의 영향으로 서비스 인스턴스의 끝점 주소가 변경될 수 있습니다. 서비스 인스턴스와의 통신을 설정하려면 다음 루프를 실행해야 합니다. 서비스 패브릭에 의해 제공되는 통신 API를 사용하는 경우의 자세한 내용은 추상화됩니다.

* **확인**: 서비스 패브릭의 모든 서비스 인스턴스가 고유한 URI를 포함해야 합니다. 예를 들어 "fabric:/MyApplication/MyService"와 그 URI는 시간이 지나도 바뀌지 않습니다. 또한 각 서비스 인스턴스는 서비스 인스턴스가 이동할 때 변경할 수 있는 끝점을 노출합니다. 이는 일정한 URL을 갖는 웹 사이트와 유사하지만 IP 주소는 변경될 수 있습니다. 웹사이트 URL을 IP 주소로 확인하는 웹의 DNS와 유사한 서비스 패브릭 플랫폼은 URI를 끝점으로 확인하는 시스템 서비스도 갖추고 있습니다. 이 단계에서는 서비스 인스턴스의 URI를 끝점으로 확인하는 것과 연관됩니다.

* **연결**: 일단 서비스 URI가 끝점 주소로 확인되면 다음 단계는 해당 서비스에 연결을 시도하는 것입니다. 이 연결은 끝점 주소가 예를 들어 컴퓨터 오류나 부하 분산에 의한 서비스 이동에 따라 변경된 경우에는 실패할 수 있습니다.

* **다시 시도**: 앞서 확인된 연결 시도가 실패하는 경우, 앞선 확인 및 연결 단계를 다시 시도하고 연결에 성공할 때까지 이 주기를 반복해야 합니다.

## 통신 API 옵션
서비스 패브릭의 일부로서 통신 API에 대한 몇 가지 옵션이 제공되며, 그 중에서 가장 적합한 옵션은 프로그래밍 모델, 통신 프레임워크 및 서비스가 작성되는 프로그래밍 언어로 무엇을 선택하는지에 따라 달라집니다.
### 신뢰할 수 있는 행위자에 대한 통신
Reliable Actors API를 사용하여 작성되는 서비스의 경우 모든 통신 세부 사항이 추상화됩니다. 통신이 ActorProxy에서 메서드 호출로 발생하기 때문에 여기서 읽기를 중지할 수 있습니다.

### 신뢰할 수 있는 서비스에 대한 통신 옵션
Reliable Services API를 사용하여 서비스가 작성되는 경우 몇 가지 옵션이 있습니다. 선택하는 통신 프로토콜에 따라 어떤 서비스 패브릭 통신 API를 사용해야 하는지 알려줍니다.

* **특정 프로토콜이 없는 경우:** 특정 커뮤니케이션 프레임워크를 선택하지는 않지만 항목을 신속하게 실행하고 싶을 때 가정 적합한 옵션은 행위자 통신 모델과 유사한 모델을 허용하는 [기본 스택](service-fabric-reliable-services-communication-remoting.md)입니다. 이는 서비스 통신을 시작하기에 가장 쉽고 빠른 방법입니다. 대부분의 통신 상세를 추상화하여 로컬 개체 인스턴스에서 메서드를 호출하는 것처럼 보이는 코드의 원격 서비스에서 메서드를 호출하는 강력한 형식의 RPC 통신을 제공합니다. 이 클래스는 통신 채널을 설정하는 동안 확인, 연결, 다시 시도 및 오류 처리를 추상화하고 통신 모델에 기반하여 메서드를 호출하게 합니다. 그러므로 통신의 서버 쪽에는 `ServiceCommunicationListener` 클래스, 클라이언트 쪽에는 `ServiceProxy` 클래스를 사용합니다.

* **HTTP**: HTTP 기반 통신이 제공하는 유연성을 활용하려면 확인, 연결 및 다시 시도 논리는 여전히 개발자 쪽에서 추상화되면서도 통신 메커니즘을 정의할 수 있는 서비스 패브릭 통신 API를 사용하세요. 예를 들어 웹 API를 사용하여 통신 메커니즘을 지정하고 [`ICommunicationClient` 및 `ServicePartitionClient` 클래스](service-fabric-reliable-services-communication.md)를 활용하여 통신을 설정할 수 있습니다.
* **WCF**: 통신 프레임워크로 WCF를 사용하는 기존 코드가 있는 경우, 서버 측에 대해 WcfCommunicationListener를 사용하고 클라이언트 측에 WcfCommunicationClient 및 ServicePartitionClient 클래스를 사용할 수 있습니다. 자세한 내용은 이 문서에서 [WCF 기반 통신 스택 구현](service-fabric-reliable-services-communication-wcf.md)에 대한 부분을 참조하세요.

* **사용자 지정 프로토콜이 포함된 기타 통신 프레임워크**: 사용자 지정 통신 프로토콜이 포함된 다른 통신 프레임워크를 사용할 계획이라면 모든 검색 및 연결 작업이 개발자 쪽에서 추상화되면서도 개발자가 통신 스택을 플러그인할 수 있는 서비스 패브릭 통신 API를 사용하세요. 자세한 내용은 이 문서에서 [Reliable Services 통신 모델](service-fabric-reliable-services-communication.md)에 대한 부분을 참조하세요.

### 서로 다른 프로그래밍 언어로 쓰인 서비스간의 통신
모든 서비스 패브릭 통신 API는 현재 C#에서만 사용할 수 있습니다. 따라서 Java나 Node.JS와 같은 다른 프로그래밍 언어로 작성된 서비스가 있는 경우 독자적인 통신 메커니즘을 작성해야 합니다.

## 다음 단계
* [신뢰할 수 있는 서비스 프레임워크에 의해 제공되는 기본 통신 스택](service-fabric-reliable-services-communication-remoting.md)
* [신뢰할 수 있는 서비스 통신 모델](service-fabric-reliable-services-communication.md)
* [OWIN 자체 호스팅으로 Microsoft Azure 서비스 패브릭 웹 API 서비스 시작](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services에 대한 WCF 기반 통신 스택](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_1223_2015-->