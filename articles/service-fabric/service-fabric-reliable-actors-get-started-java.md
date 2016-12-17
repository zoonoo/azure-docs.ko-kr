---
title: "Service Fabric Reliable Actors 시작 | Microsoft Docs"
description: "이 자습서에서는 서비스 패브릭 Reliable Actors를 사용하여 간단한 행위자 기반 서비스를 생성, 디버깅 및 배포하는 과정을 단계별로 안내합니다."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d31dc8ab-9760-4619-a641-facb8324c759
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3e349d0c76078889b6e41340ee8bb2f599819ba3


---
# <a name="getting-started-with-reliable-actors"></a>Reliable Actors 시작
> [!div class="op_single_selector"]
> * [Windows에서 C#](service-fabric-reliable-actors-get-started.md)
> * [Linux에서 Java](service-fabric-reliable-actors-get-started-java.md)
> 
> 

이 문서는 Azure Service Fabric Reliable Actors의 기본 개념을 설명하고 Java에서 간단한 Reliable Actor 응용 프로그램을 생성 및 배포하는 과정을 안내합니다.

## <a name="installation-and-setup"></a>설치 및 설정
시작하기 전에 컴퓨터에 Service Fabric 개발 환경이 설정되어 있는지 확인합니다.
설정해야 하는 경우 [Mac에서 시작](service-fabric-get-started-mac.md) 또는 [Linux에서 시작](service-fabric-get-started-linux.md)으로 이동합니다.

## <a name="basic-concepts"></a>기본 개념
Reliable Actors를 시작하려면 몇 가지 기본 개념만 이해하면 됩니다.

* **행위자 서비스**. Reliable Actors는 서비스 패브릭 인프라에 배포될 수 있는 Reliable Services에 패키징됩니다. 행위자 인스턴스는 명명된 서비스 인스턴스에서 활성화됩니다.
* **행위자 등록**. Reliable Services와 마찬가지로 Reliable Actor 서비스를 Service Fabric 런타임에 등록해야 합니다. 또한 행위자 형식을 행위자 런타임에 등록해야 합니다.
* **행위자 인터페이스**. 행위자 인터페이스는 행위자에 대한 강력한 형식의 공용 인터페이스를 정의하는 데 사용됩니다. Reliable Actor 모델 용어에서 행위자 인터페이스는 행위자가 이해하고 처리할 수 있는 메시지의 유형을 정의합니다. 행위자 인터페이스는 다른 행위자 또는 클라이언트 응용 프로그램에서 메시지를 행위자에게 "보내는"(비동기) 데 사용됩니다. Reliable Actors는 여러 인터페이스를 구현할 수 있습니다.
* **ActorProxy 클래스**. ActorProxy 클래스는 클라이언트 응용 프로그램에서 행위자 인터페이스를 통해 노출되는 메서드를 호출하는 데 사용됩니다. ActorProxy 클래스는 다음 두 가지 중요한 기능을 제공합니다.
  
  * 이름 확인: 클러스터에서 행위자를 찾을 수 있습니다(호스트되는 클러스터의 노드 찾기).
  * 오류 처리: 메서드 호출을 다시 시도하고 행위자를 클러스터의 다른 노드로 재배치해야 하는 경우 등의 오류가 발생한 후 행위자의 위치를 다시 파악할 수 있습니다.

행위자 인터페이스와 관련된 다음 규칙을 확인하면 도움이 됩니다.

* 행위자 인터페이스 메서드는 오버로드할 수 없습니다.
* 행위자 인터페이스 메서드에는 out, ref 또는 선택적 매개 변수가 없어야 합니다.
* 제네릭 인터페이스는 지원되지 않습니다.

## <a name="create-an-actor-service"></a>행위자 서비스 만들기
새 Service Fabric 응용 프로그램을 만들어 시작합니다. Linux용 Service Fabric SDK는 Service Fabric 응용 프로그램용 스캐폴딩에 상태 비저장 서비스를 제공하기 위해 Yeoman 생성기를 포함합니다. 다음 Yeoman 명령을 실행하여 시작합니다.

```bash
$ yo azuresfjava
```

지침에 따라 **신뢰할 수 있는 행위자 서비스**를 만듭니다. 이 자습서에서는 응용 프로그램을 "HelloWorldActorApplication", 행위자를 "HelloWorldActor"라고 이름을 지정합니다. 다음 스캐폴딩이 만들어집니다.

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>신뢰할 수 있는 행위자 기본 구성 요소
앞에서 설명한 기본 개념은 신뢰할 수 있는 행위자 서비스의 기본 구성 요소로 변환합니다.

### <a name="actor-interface"></a>행위자 인터페이스
여기에는 행위자에 대한 인터페이스 정의가 포함됩니다. 이 인터페이스는 행위자 구현과 행위자를 호출하는 클라이언트에 의해 공유되는 행위자 계약을 정의하므로 일반적으로 행위자 구현과는 별개이고 다른 여러 서비스 또는 클라이언트 응용 프로그램이 공유할 수 있는 장소에서 정의하는 것이 좋습니다.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>행위자 서비스
여기에는 행위자 구현 및 행위자 등록 코드가 포함됩니다. 행위자 클래스는 행위자 인터페이스를 구현합니다. 사용자 행위자가 작업을 수행하는 위치입니다.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>행위자 등록
행위자 서비스는 서비스 패브릭 런타임에서 서비스 유형에 등록되어야 합니다. 행위자 서비스에서 행위자 인스턴스를 실행하려면 행위자 서비스에 행위자 유형이 등록되어야 합니다. `ActorRuntime` 등록 메서드가 행위자에 대한 이 작업을 수행합니다.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

    public static void main(String[] args) throws Exception {

        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);

        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>테스트 클라이언트
이는 행위자 서비스를 테스트하기 위해 Service Fabric 응용 프로그램과 별개로 실행할 수 있는 간단한 테스트 클라이언트 응용 프로그램입니다. 이는 행위자 인스턴스를 활성화하고 통신하기 위해 ActorProxy를 사용할 수 있는 위치의 예입니다. 사용자의 서비스와 함께 배포되지 않습니다.

### <a name="the-application"></a>응용 프로그램
마지막으로, 응용 프로그램은 배포를 위해 나중에 추가할 수 있는 행위자 서비스와 기타 모든 서비스를 패키징합니다. 여기에는 *ApplicationManifest.xml*와 행위자 서비스 패키지를 위한 자리 표시자가 포함됩니다.

## <a name="run-the-application"></a>응용 프로그램 실행
Yeoman 스캐폴딩은 응용 프로그램을 빌드하는 Gradle 스크립트와 응용 프로그램을 배포하고 배포 취소하는 Bash 스크립트를 포함합니다. 응용 프로그램을 실행하려면 먼저 다음과 같은 Gradle을 통해 응용 프로그램을 빌드합니다.

```bash
$ gradle
```

이는 Service Fabric Azure CLI를 사용하여 배포할 수 있는 Service Fabric 응용 프로그램 패키지를 생성합니다. install.sh 스크립트는 응용 프로그램 패키지를 배포하는 데 필요한 Azure CLI 명령을 포함합니다. 다음과 같이 배포할 install.sh 스크립트를 실행하면 됩니다.

```bask
$ ./install.sh
```



<!--HONumber=Nov16_HO3-->


