---
title: "Reliable Services 시작 |Microsoft Docs"
description: "상태 비저장 및 상태 저장 서비스를 사용하여 Microsoft Azure 서비스 패브릭 응용 프로그램 만들기 소개"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2016
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 224538560a50f9ab24b8c2746e99de1ab87f084b


---
# <a name="get-started-with-reliable-services"></a>Reliable Services로 시작하기
> [!div class="op_single_selector"]
> * [Windows에서 C#](service-fabric-reliable-services-quick-start.md)
> * [Linux에서 Java](service-fabric-reliable-services-quick-start-java.md)
> 
> 

이 문서는 Azure Service Fabric Reliable Services의 기본 개념을 설명하고 Java로 작성된 Reliable Services 응용 프로그램을 생성 및 배포하는 과정을 안내합니다.

## <a name="installation-and-setup"></a>설치 및 설정
시작하기 전에 컴퓨터에 Service Fabric 개발 환경이 설정되어 있는지 확인합니다.
설정해야 하는 경우 [Mac에서 시작](service-fabric-get-started-mac.md) 또는 [Linux에서 시작](service-fabric-get-started-linux.md)으로 이동합니다.

## <a name="basic-concepts"></a>기본 개념
Reliable Services를 시작하려면 몇 가지 기본 개념만 이해하면 됩니다.

* **서비스 유형**: 서비스 구현입니다. 이름 및 버전 번호와 함께 `StatelessService` 및 기타 코드 또는 여기에 사용된 종속성을 확장하도록 사용자가 작성한 클래스에 의해 정의됩니다.
* **명명된 서비스 인스턴스**: 서비스를 실행하려면 클래스 유형의 개체 인스턴스를 만드는 것처럼 서비스 유형의 명명된 인스턴스를 만듭니다. 서비스 인스턴스는 실제로 사용자가 작성한 서비스 클래스의 개체 인스턴스화입니다. 
* **서비스 호스트**: 호스트 내에서 실행해야 하는 사용자가 만든 명명된 서비스 인스턴스입니다. 서비스 호스트는 서비스의 인스턴스에서 실행할 수 있는 프로세스일 뿐입니다.
* **서비스 등록**: 등록은 모든 항목을 함께 모읍니다. Service Fabric에서 실행할 인스턴스를 만들 수 있도록 서비스 호스트의 Service Fabric 런타임에 서비스 유형을 등록해야 합니다.  

## <a name="create-a-stateless-service"></a>상태 비저장 서비스 만들기
새 Service Fabric 응용 프로그램을 만들어 시작합니다. Linux용 Service Fabric SDK는 Service Fabric 응용 프로그램용 스캐폴딩에 상태 비저장 서비스를 제공하기 위해 Yeoman 생성기를 포함합니다. 다음 Yeoman 명령을 실행하여 시작합니다.

```bash
$ yo azuresfjava
```

지침에 따라 **신뢰할 수 있는 상태 비저장 서비스**를 만듭니다. 이 자습서에서는 응용 프로그램을 "HelloWorldApplication", 행위자를 "HelloWorld"라고 이름을 지정합니다. 결과에는 `HelloWorldApplication` 및 `HelloWorld`에 대한 디렉터리가 포함됩니다.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>서비스 구현
**HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**를 엽니다. 이 클래스는 서비스 유형을 정의하고 모든 코드를 실행할 수 있습니다. 서비스 API는 코드에 대한 두 진입점을 제공합니다.

* 장기 실행 계산 워크로드 등 모든 워크로드의 실행을 시작할 수 있는 `runAsync()`라는 개방형 진입점 메서드.

```java
@Override
protected CompletableFuture<?> runAsync() {
    ...
}
```

* 원하는 통신 스택을 연결할 수 있는 통신 진입점. 사용자 및 다른 서비스에서 요청을 수신하도록 시작할 수 있습니다.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

이 자습서에서는 `runAsync()` 진입점 메서드에 집중합니다. 바로 코드를 실행하기 시작할 수 있습니다.

### <a name="runasync"></a>RunAsync
서비스의 인스턴스가 배치되어 실행할 준비가 되면 플랫폼이 이 메서드를 호출합니다. 서비스 인스턴스의 열림-닫힘 주기는 서비스의 수명 동안 전체적으로 여러 번 발생할 수 있습니다. 다음을 포함하여 여러 가지 이유로 발생할 수 있습니다.

* 시스템은 리소스 분산을 위해 서비스 인스턴스를 이동시킵니다.
* 오류는 코드에서 발생합니다.
* 응용 프로그램 또는 시스템 업그레이드됩니다.
* 기본 하드웨어가 중단됩니다.

이러한 오케스트레이션은 서비스의 가용성을 높게 유지하고 제대로 균형을 유지하기 위해 Service Fabric에 의해 관리됩니다.

#### <a name="cancellation"></a>취소
Service Fabric에서 알림을 받을 때 `runAsync()`의 코드가 실행을 중지할 수 있습니다. Service Fabric이 서비스 실행을 중지해야 하는 경우 `runAsync()`에서 반환된 `CompletableFuture`는 취소됩니다. 다음 예제에서는 취소 이벤트를 처리하는 방법을 보여줍니다. 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);

        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });

        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });

        return completableFuture;
   }
``` 

### <a name="service-registration"></a>서비스 등록
서비스 유형은 Service Fabric 런타임에 등록되어야 합니다. 서비스 유형은 `ServiceManifest.xml`에서 정의되고 `StatelessService`를 구현하는 서비스 클래스입니다. 서비스 등록은 프로세스 주 진입점에서 수행됩니다. 이 예제에서는 프로세스 주 진입점은 `HelloWorldServiceHost.java`입니다.

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    } 
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

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


