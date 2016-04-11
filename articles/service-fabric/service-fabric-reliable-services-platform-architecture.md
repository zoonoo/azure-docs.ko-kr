<properties
   pageTitle="신뢰할 수 있는 서비스 아키텍처 | Microsoft Azure"
   description="상태 저장 및 비저장 서비스를 위한 Reliable Services 아키텍처 개요"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/30/2016"
   ms.author="alanwar"/>

# 상태 비저장 신뢰할 수 있는 서비스를 위한 아키텍처

Azure 서비스 패브릭 Reliable Services는 상태 저장 또는 상태 비저장일 수 있습니다. 각 서비스 유형은 특정 아키텍처 내에서 실행됩니다. 이러한 아키텍처는 이 문서에 설명되어 있습니다. 상태 저장 서비스와 상태 비저장 서비스 간의 차이점에 대 한 자세한 내용은 [신뢰할 수 있는 서비스 개요](service-fabric-reliable-services-introduction.md)를 참조하세요.

## 상태 저장 신뢰할 수 있는 서비스

### 상태 저장 서비스의 아키텍처
![상태 저장 서비스의 아키텍처 다이어그램](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### 상태 저장 신뢰할 수 있는 서비스

상태 저장 신뢰할 수 있는 서비스는 StatefulService 또는 StatefulServiceBase 클래스에서 파생될 수 있습니다. 이러한 기본 클래스는 모두 서비스 패브릭에서 제공하며, 상태 저장 서비스가 서비스 패브릭과 상호 작용하고, 서비스 패브릭 클러스터 내에 서비스로 참여하도록 다양한 지원 및 추상화 수준을 제공합니다.

StatefulService는 StatefulServiceBase에서 파생됩니다. StatefulServiceBase는 서비스에 보다 유연함을 제공하지만 서비스 패브릭의 내부 구조에 대한 자세한 이해가 필요합니다. StatefulService 및 StatefulServiceBase 클래스를 사용하여 서비스를 작성하는 방법에 대한 자세한 내용은 [Reliable Services 개요](service-fabric-reliable-services-introduction.md) 및 [Reliable Services 고급 사용법](service-fabric-reliable-services-advanced-usage.md)을 참조하세요.

두 기본 클래스는 서비스 구현의 수명 및 역할을 관리합니다. 서비스 구현이 서비스 구현 수명 주기에서 이 시점에 작업을 수행하는 경우 또는 통신 수신기 개체를 만들려고 하는 경우 서비스 구현이 기본 클래스의 가상 메서드를 재정의할 수 있습니다. 위의 다이어그램에서 서비스 구현은 ICommunicationListener를 노출하는 자체 통신 수신기 개체를 구현할 수 있지만 서비스 패브릭에서 통신 수신기를 구현합니다. 해당 서비스 구현이 서비스 패브릭에서 구현하는 통신 수신기를 사용하기 때문입니다.

상태 저장 Reliable Services는 신뢰할 수 있는 상태 관리자를 사용하여 신뢰할 수 있는 컬렉션을 활용합니다. 신뢰할 수 있는 컬렉션은 서비스에서 대한 가용성이 높은, 즉 서비스 장애 조치에 관계없이 항상 사용 가능한 로컬 데이터 구조체입니다. 신뢰할 수 있는 컬렉션의 각 유형은 신뢰할 수 있는 상태 제공자에 의해 구현됩니다. 신뢰할 수 있는 컬렉션에 대한 자세한 내용은 [신뢰할 수 있는 컬렉션 개요](service-fabric-reliable-services-reliable-collections.md)를 참조하세요.

### 신뢰할 수 있는 상태 관리자와 제공자

신뢰할 수 있는 상태 관리자는 신뢰할 수 있는 상태 제공자를 관리하는 개체입니다. 신뢰할 수 있는 상태 제공자를 만들고, 삭제하고, 열거하고, 유지하고, 가용성을 보장하는 기능을 갖추고 있습니다. 신뢰할 수 있는 상태 제공자 인스턴스는 사전이나 큐와 같은 지속적이고 항상 사용 가능한 데이터 구조체의 인스턴스를 나타냅니다.

각 신뢰할 수 있는 상태 제공자는 상태 저장 서비스에서 신뢰할 수 있는 상태 제공자와 상호 작용하는 데 사용되는 인터페이스를 노출합니다. 예를 들어 IReliableDictionary를 사용하여 신뢰할 수 있는 사전과 상호 작용하고, IReliableQueue를 사용하여 신뢰할 수 있는 큐와 상호 작용합니다. 모든 신뢰할 수 있는 상태 제공자는 IReliableState 인터페이스를 구현합니다.

신뢰할 수 있는 상태 관리자에는 상태 저장 서비스로부터의 액세스를 허용하는 IReliableStateManager라는 인터페이스가 있습니다. 신뢰할 수 있는 상태 제공자에 대한 인터페이스는 IReliableStateManager를 통해 반환됩니다.

신뢰할 수 있는 상태 관리자는 새로운 종류의 신뢰할 수 있는 컬렉션이 동적으로 연결될 수 있도록 플러그인 아키텍처를 사용합니다.

신뢰할 수 있는 사전 및 신뢰할 수 있는 큐는 고성능 버전의 차등 저장소 구현을 바탕으로 작성됩니다.

### 트랜잭션 복제자

트랜잭션 복제자 구성 요소는 서비스의 상태, 즉 신뢰할 수 있는 상태 관리자 및 신뢰할 수 있는 컬렉션 내의 상태가 서비스를 실행하는 모든 복제본에서 일치하도록 보장하는 역할을 합니다. 또한 로그에서도 상태가 유지되도록 보장합니다. 신뢰할 수 있는 상태 관리자는 개인 메커니즘을 통해 트랜잭션 복제자와 상호 작용합니다.

트랜잭션 복제자는 모든 복제본이 최신 상태 정보를 가질 수 있도록 네트워크 프로토콜을 사용하여 서비스 인스턴스의 다른 복제본과 상태를 통신합니다.

트랜잭션 복제자는 프로세스 또는 노드 충돌 시에도 상태 정보가 유지되도록 로그를 사용하여 상태 정보를 유지합니다. 로그에 대한 인터페이스는 개인 메커니즘을 통해 이루어집니다.

### 로그

로그 구성 요소는 회전 디스크 또는 솔리드 스테이트 디스크에 쓰도록 최적화할 수 있는 고성능 영구 저장소를 제공합니다. 로그는 상태 저장 서비스를 실행하는 노드에 로컬인 영구 저장소(즉, 하드 디스크)용으로 설계되었습니다. 따라서 노드에 대해 로컬이 아닌 원격 영구 저장소보다 대기 시간이 짧고 처리량이 높습니다.

로그 구성 요소는 여러 로그 파일을 사용합니다. 상태 데이터 저장을 위해 가장 짧은 대기 시간과 가장 높은 처리량을 제공할 수 있으므로 모든 복제본이 사용하는 노드 수준의 공유 로그 파일이 있습니다. 기본적으로 공유 로그는 서비스 패브릭 노드 작업 디렉터리에 배치되지만 이상적으로 공유 로그에 대해 예약된 디스크의 다른 위치에 배치되도록 구성할 수도 있습니다. 또한 서비스의 각 복제본은 전용 로그 파일을 가지고 있고 전용 로그는 서비스의 작업 디렉터리 내에 배치됩니다. 다른 위치에 배치될 전용 로그를 구성할 메커니즘은 없습니다.

공유 로그는 복제본의 상태 정보의 전환 영역이고, 전용 로그 파일은 상태 정보가 유지되는 최종 대상입니다. 이 설계에서 상태 정보는 먼저 공유 로그 파일에 기록된 후 백그라운드에서 전용 로그 파일에 지연 이동됩니다. 따라서 공유 로그에 대한 쓰기는 대기 시간이 가장 적고 처리량이 가장 높아 서비스를 더 빠르게 진행할 수 있습니다.

공유 로그에 읽고 쓰기는 공유 로그 파일에 대한 디스크에 미리 할당된 공간에 대한 직접 IO를 통해 수행됩니다. 전용 로그가 있는 디스크 공간을 최적으로 사용할 수 있도록 하기 위해 전용 로그 파일이 NTFS 스파스 파일로 생성됩니다. 이렇게 하면 디스크 공간이 과도하게 프로비전될 수 있으며 OS에서 실제 사용된 공간보다 훨씬 많은 디스크 공간을 사용하는 전용 로그 파일이 표시됩니다.

로그에 대한 최소한의 사용자 모드 인터페이스 외에도 로그가 커널 모드 드라이버로 기록됩니다. 커널 모드 드라이버도 실행하면 로그가 사용하는 모든 서비스에 가장 높은 성능을 제공할 수 있습니다.

로그를 구성하는 방법에 대한 자세한 내용은 [상태 저장 Reliable Services 구성](service-fabric-reliable-services-configuration.md)을 참조하세요.

## 상태 비저장 신뢰할 수 있는 서비스

### 상태 저장 서비스의 아키텍처
![상태 비저장 서비스의 아키텍처 다이어그램](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### 상태 비저장 신뢰할 수 있는 서비스

상태 비저장 서비스 구현은 StatelessService 또는 StatelessServiceBase 클래스에서 파생됩니다. StatelessServiceBase 클래스가 StatelessService 클래스보다 유연합니다. 두 기본 클래스는 서비스의 수명 및 역할을 관리합니다.

서비스가 서비스 수명 주기에서 이 시점에 작업을 수행하는 경우 또는 통신 수신기 개체를 만들려고 하는 경우 서비스 구현이 기본 클래스의 가상 메서드를 재정의할 수 있습니다. 위의 다이어그램에서 서비스는 ICommunicationListener를 노출하는 자체 통신 수신기 개체를 구현할 수 있지만 서비스 패브릭에서 통신 수신기를 구현합니다. 해당 서비스 구현이 서비스 패브릭에서 구현하는 통신 수신기를 사용하기 때문입니다.

StatelessService 및 StatelessServiceBase 클래스를 사용하여 서비스를 작성하는 방법에 대한 자세한 내용은 [Reliable Services 개요](service-fabric-reliable-services-introduction.md) 및 [Reliable Services 고급 사용법](service-fabric-reliable-services-advanced-usage.md)을 참조하세요.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

서비스 패브릭에 대한 자세한 내용은 다음을 참조하세요.

[Reliable Services 개요](service-fabric-reliable-services-introduction.md)

[빠른 시작](service-fabric-reliable-services-quick-start.md)

[신뢰할 수 있는 컬렉션 개요](service-fabric-reliable-services-reliable-collections.md)

[Reliable Services 고급 사용법](service-fabric-reliable-services-advanced-usage.md)

[Reliable Services 구성](service-fabric-reliable-services-configuration.md)

<!---HONumber=AcomDC_0330_2016-->