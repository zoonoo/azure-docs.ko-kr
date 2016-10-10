<properties
	pageTitle="IoT Hub 장치-클라우드 메시지 처리(Java) | Microsoft Azure"
	description="이 Java 자습서를 수행하여 IoT Hub 장치-클라우드 메시지를 처리하는 데 유용한 패턴을 알아봅니다."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/01/2016"
     ms.author="dobett"/>

# 자습서: Java를 사용하여 IoT Hub 장치-클라우드 메시지를 처리하는 방법

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## 소개

Azure IoT Hub는 수백만의 IoT 장치와 응용 프로그램 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. 기타 자습서([IoT Hub로 시작] 및 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기][lnk-c2d])는 IoT Hub의 기본 장치-클라우드 및 클라우드-장치 메시징 기능을 사용하는 방법을 보여 줍니다.

이 자습서는 [IoT Hub 시작] 자습서에 나와있는 코드에 기반하고 장치-클라우드 메시지를 처리하는 데 사용할 수 있는 확장성 있는 두 개의 패턴을 보여 줍니다.

- [Azure Blob 저장소]에서 장치-클라우드 메시지의 신뢰할 수 있는 저장소입니다. 일반적인 시나리오는 분석 프로세스에 대한 입력으로 사용할 원격 분석 데이터를 blob에 저장하는 *콜드 경로* 분석입니다. 이러한 프로세스는 [Azure 데이터 팩터리] 또는 [HDInsight(Hadoop)] 스택과 같은 도구를 통해 진행됩니다.

- *대화형* 장치-클라우드 메시지의 신뢰할 수 있는 처리입니다. 장치-클라우드 메시지는 응용 프로그램 백 엔드에서 일련의 작업에 대해 즉각적인 트리거인 경우 대화형입니다. 예를 들어 장치는 CRM 시스템으로의 티켓 삽입을 트리거하는 경보 메시지를 보낼 수 있습니다. 이와 반대로 *데이터 요소* 메시지는 단순히 분석 엔진에 공급됩니다. 예를 들어 나중에 분석을 위해 저장해야 하는 장치의 온도 원격 분석이 데이터 요소 메시지에 해당합니다.

IoT Hub가 [이벤트 허브][lnk-event-hubs] 호환 끝점을 노출하여 장치-클라우드 메시지를 받기 때문에 이 자습서에서는 [EventProcessorHost] 인스턴스를 사용합니다. 이 인스턴스는 다음을 수행합니다.

* *데이터 요소* 메시지를 Azure Blob 저장소에 안정적으로 저장합니다.
* 즉시 처리를 위해 *대화형* 장치-클라우드 메시지를 [Azure 서비스 버스 큐]에 전달합니다.

서비스 버스는 메시지당 검사점 및 시간 기반 중복 제거 기능을 제공하므로 신뢰할 수 있게 대화형 메시지를 처리할 수 있도록 도와줍니다.

> [AZURE.NOTE] **EventProcessorHost** 인스턴스는 대화형 메시지를 처리하는 유일한 방법입니다. 기타 옵션으로는 [Azure 서비스 패브릭][lnk-service-fabric] 및 [Azure 스트림 분석][lnk-stream-analytics]이 있습니다.

이 자습서의 끝 부분에서는 다음의 세 가지 Java 콘솔 앱을 실행합니다.

* **simulated-device**, [IoT Hub 시작] 자습서에서 만든 수정된 버전의 앱이며, 매초 데이터 요소 장치-클라우드 메시지를 보내고 10초마다 대화형 장치-클라우드 메시지를 보냅니다. 이 앱에서는 IoT Hub와 통신하는 데 AMQPS 프로토콜을 사용합니다.
* **process-d2c-messages**에서는 [EventProcessorHost] 클래스를 사용하여 이벤트 허브 호환 끝점에서 메시지를 검색합니다. 그런 다음 Azure Blob 저장소에 데이터 요소 메시지를 안정적으로 저장하고 대화형 메시지를 서비스 버스 큐에 전달합니다.
* **process-interactive-messages**는 서비스 버스 큐에서 대화형 메시지를 제거합니다.

> [AZURE.NOTE] IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 JavaScript 포함)에 SDK를 지원합니다. 물리적 장치를 사용하여 이 자습서의 시뮬레이션된 장치를 바꾸는 방법 및 장치를 IoT Hub에 연결하는 방법에 대한 지침은 [Azure IoT 개발자 센터]를 참조하세요.

이 자습서는 [HDInsight(Hadoop)] 프로젝트와 같이 이벤트 허브와 호환되는 메시지를 사용하는 다른 방법에 직접 적용할 수 있습니다. 자세한 내용은 [Azure IoT Hub 개발자 가이드 - 장치-클라우드]를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

+ [IoT Hub 시작] 자습서의 전체 작업 버전

+ Java SE 8. <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 Java를 설치하는 방법을 설명합니다.

+ Maven 3. <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 Maven을 설치하는 방법을 설명합니다.

+ 활성 Azure 계정. <br/>Azure 구독이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

[Azure 저장소] 및 [Azure 서비스 버스]의 기본 지식이 있어야 합니다.


## 시뮬레이트된 장치에서 대화형 메시지 보내기

이 섹션에서는 [IoT Hub 시작]에서 만든 시뮬레이트된 장치 응용 프로그램을 수정하여 대화형 장치-클라우드 메시지를 IoT Hub로 보냅니다.

1. 텍스트 편집기를 사용하여 simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java 파일을 엽니다. 이 파일에는 [IoT Hub 시작] 자습서에서 만든 **simulated-device** 앱이 포함되어 있습니다.

2. 다음 중첩 클래스를 **App** 클래스에 추가합니다.

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    이 클래스는 **simulated-device** 프로젝트의 **MessageSender** 클래스와 비슷합니다. 유일한 차이점은 이제 **MessageId** 시스템 속성 및 **messageType**이라는 사용자 지정 속성을 설정한다는 것입니다. 이 코드는 UUID(Universally Unique Identifier)를 **MessageId** 속성에 할당합니다. 서비스 버스는 이 식별자를 사용하여 받은 메시지를 중복 제거할 수 있습니다. 샘플은 **messageType** 속성을 사용하여 데이터 요소 메시지에서 대화형 메시지를 구분합니다. 응용 프로그램은 정보를 메시지 본문 대신 메시지 속성에 전달하므로 이벤트 프로세서가 메시지 라우팅을 수행하기 위해 전체 메시지를 역직렬화할 필요가 없습니다.

    > [AZURE.NOTE] 장치 코드의 대화형 메시지를 중복 제거하는 데 사용되는 **MessageId**를 만드는 것이 중요합니다. 간헐적인 네트워크 통신 또는 기타 오류로 인해 해당 장치에서 동일한 메시지가 여러 번 재전송될 수 있습니다. 또한 UUID 대신 의미 체계 메시지 ID(예: 관련 메시지 데이터 필드의 해시)를 사용할 수 있습니다.

3. 다음 코드 조각에 나와 있는 것처럼 대화형 메시지와 데이터 요소 메시지를 둘 다 보내도록 **main** 메서드를 수정합니다.

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java 파일을 저장한 후 닫습니다.

    > [AZURE.NOTE] 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.

5. Maven을 사용하여 **simulated-device** 응용 프로그램을 빌드하려면 simulated-device 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```
    mvn clean package -DskipTests
    ```

## 장치-클라우드 메시지 처리

이 섹션에서는 IoT Hub에서 장치-클라우드 메시지를 처리하는 Java 콘솔 앱을 만듭니다. IoT Hub가 [이벤트 허브]와 호환되는 끝점을 노출하여 응용 프로그램이 장치-클라우드 메시지를 읽을 수 있습니다. 이 자습서에서는 [EventProcessorHost] 클래스를 사용하여 콘솔 응용 프로그램에서 이러한 메시지를 처리합니다. 이벤트 허브에서 메시지를 처리하는 방법에 대한 자세한 내용은 [이벤트 허브 시작] 자습서를 참조하세요.

데이터 요소 메시지 또는 대화형 메시지 전달의 신뢰할 수 있는 저장소를 구현하는 경우 주요 해결 과제는 이벤트 처리가 해당 진행 상태에 대한 검사점을 제공하기 위해 메시지 소비자에 의존한다는 점입니다. 또한 높은 처리량을 달성하기 위해 이벤트 허브에서 읽은 경우 큰 배치에서 검사점을 제공해야 합니다. 이러한 접근 방식을 사용할 경우, 오류가 발생하여 이전 검사점으로 되돌리려고 하면 많은 수의 메시지가 중복 처리될 가능성이 있습니다. 이 자습서에서는 **EventProcessorHost** 검사점을 사용하여 Azure Storage 쓰기와 서비스 버스 중복 제거 창을 동기화하는 방법이 표시됩니다.

Azure Storage에 메시지를 안정적으로 기록하려면 샘플은 [블록 Blob][Azure Block Blobs]의 개별 블록 커밋 기능을 사용합니다. 이벤트 프로세서는 검사점을 제공할 시간이 될 때까지 메모리에 메시지를 누적합니다. 예를 들어 메시지의 누적된 버퍼가 4MB의 최대 블록 크기에 도달하거나, 서비스 버스 중복 제거 기간이 결과한 이후가 여기에 해당합니다. 그런 다음 검사점을 설정하기 전에 코드는 새 블록은 Blob에 커밋합니다.

이벤트 프로세서는 블록 ID로 이벤트 허브 메시지 오프셋을 사용합니다. 이 메커니즘을 따르면 이벤트 프로세서가 저장소에 새 블록을 커밋하기 전에 중복 제거 확인을 수행할 수 있으므로 블록 커밋과 검사점 간의 가능한 충돌이 방지됩니다.

> [AZURE.NOTE] 이 자습서는 단일 저장소 계정을 사용하여 IoT Hub에서 검색된 모든 메시지를 작성합니다. 솔루션에 여러 Azure 저장소 계정을 사용해야 하는 경우 [Azure 저장소 확장성 지침]을 참조하여 결정하세요.

응용 프로그램은 서비스 버스 중복 제거 기능을 사용하여 대화형 메시지를 처리할 때 중복을 방지합니다. 시뮬레이션된 장치는 고유한 **MessageId**를 사용하여 각 대화형 메시지를 스탬프 처리합니다. 이를 통해 서비스 버스는 지정된 중복 제거 기간에 동일한 **MessageId**를 갖는 어느 두 개의 메시지도 수신자에게 전달되지 않도록 할 수 있습니다. 해당 중복 제거는 서비스 버스 큐에서 제공된 메시지당 완료 의미 체계와 함께 대화형 메시지를 안정적으로 처리하도록 구현할 수 있게 합니다.

메시지가 중복 제거 창 외부에서 다시 전송되지 않도록 하려면 코드가 **EventProcessorHost** 검사점 메커니즘을 서비스 버스 큐 중복 제거 창과 동기화합니다. 중복 제거 창이 경과(이 자습서에서 1시간)될 때마다 검사점을 최소 한 번 강제 적용하여 이러한 동기화가 수행됩니다.

> [AZURE.NOTE] 이 자습서에서는 단일 분할된 서비스 버스 큐를 사용하여 IoT Hub에서 검색된 모든 대화형 메시지를 처리합니다. 서비스 버스 큐를 사용하여 솔루션의 확장성 요구를 충족하는 방법에 대한 자세한 내용은 [Azure 서비스 버스] 설명서를 참조하세요.

### Azure 저장소 계정 및 서비스 버스 큐 프로비전

[EventProcessorHost] 클래스를 사용하기 위해서는 **EventProcessorHost** 검사점 정보를 기록하도록 하는 Azure Storage 계정이 있어야 합니다. 기존 저장소 계정을 사용하거나 [Azure 저장소 정보]의 지침에 따라 새 계정을 만들 수 있습니다. 저장소 계정 연결 문자열을 기록해 둡니다.

> [AZURE.NOTE] 저장소 계정 연결 문자열을 복사하여 붙여 넣는 경우 공백이 없는지 확인합니다.

대화형 메시지의 신뢰할 수 있는 처리를 활성화하려면 서비스 버스 큐가 필요합니다. [서비스 버스 큐를 사용하는 방법][Service Bus queue]에서 설명한 것처럼 한 시간 동안 중복 제거 창을 사용하여 프로그래밍 방식으로 큐를 만들거나, 또는 다음 단계에 따라 [Azure 클래식 포털][lnk-classic-portal]을 사용할 수 있습니다.

1. 왼쪽 아래 구석에 있는 **새로 만들기**를 클릭합니다. **앱 서비스** > **서비스 버스** > **큐** > **사용자 지정 만들기**를 클릭합니다. 이름 **d2ctutorial**을 입력하고 영역을 선택한 후 기존 네임스페이스를 사용하거나 새로 만듭니다. 이 자습서의 뒷부분에서 필요하므로 네임스페이스 이름을 적어둡니다. 다음 페이지에서 **중복 검색 사용**을 선택하고 **중복 검색 기록 기간**을 1시간으로 설정합니다. 그런 후 오른쪽 아래 모서리에 있는 확인 표시를 클릭하여 큐 구성을 저장합니다.

    ![Azure 포털에서 큐 만들기][30]

2. 서비스 버스 큐의 목록에서 **d2ctutorial**을 클릭한 다음 **구성**을 클릭합니다. **보내기** 권한으로 **보내기**, **수신** 권한으로 **수신**이라는 두 개의 공유 액세스 정책을 만듭니다. 이 자습서의 뒷부분에서 필요하므로 두 정책에 대한 **기본 키** 값을 적어둡니다. 완료되면 아래쪽의 **저장**을 클릭합니다.

    ![Azure 포털에서 큐 구성][31]

### 이벤트 프로세서 만들기

이 섹션에서는 이벤트 허브 호환 끝점의 메시지를 처리하는 Java 응용 프로그램을 만듭니다.

첫 번째 작업은 IoT Hub 이벤트 허브 호환 끝점에서 장치-클라우드 메시지를 받아 다른 백 엔드 서비스로 보내는 **process-d2c-messages**라는 Maven 프로젝트를 추가하는 것입니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 [IoT Hub 시작] 자습서에서 만든 iot-java-get-started 폴더에 **read-d2c-messages**라는 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 명령 프롬프트에서 새 process-d2c-messages 폴더로 이동합니다.

3. 텍스트 편집기를 사용하여 process-d2c-messages 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 응용 프로그램에서 azure-eventhubs, azure-eventhubs-eph 및 azure-servicebus 패키지를 사용하여 IoT hub 및 서비스 버스 큐와 상호 작용할 수 있습니다.

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. pom.xml 파일을 저장하고 닫습니다.

다음 작업은 **ErrorNotificationHandler** 클래스를 프로젝트에 추가하는 것입니다.

1. 텍스트 편집기를 사용하여 process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\ErrorNotificationHandler.java 파일을 만듭니다. 파일에 다음 코드를 추가하여 **EventProcesssorHost** 인스턴스의 오류 메시지를 표시합니다.

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. ErrorNotificationHandler.java 파일을 저장한 후 닫습니다.

이제 **IEventProcessor** 인터페이스를 구현하는 클래스를 추가할 수 있습니다. **EventProcessorHost** 클래스는 이 클래스를 호출하여 IoT Hub에서 수신하는 장치-클라우드 메시지를 처리합니다. 이 클래스의 코드는 Blob 컨테이너에 메시지를 안정적으로 전달하고 서비스 버스 큐에 대화형 메시지 전달하는 논리를 구현합니다.

**onEvents** 메서드는 이 이벤트 처리기가 읽은 최신 메시지의 오프셋 및 시퀀스 번호를 추적하는 **latestEventData** 변수를 설정합니다. 각 프로세서가 단일 파티션에 대한 책임을 집니다. **onEvents** 메서드는 IoT Hub에서 배치 메시지를 수신하고 다음과 같이 처리합니다. 서비스 버스 큐에 대화형 메시지를 전송하고 **toAppend** 메모리 버퍼에 데이터 요소 메시지를 추가합니다. 메모리 버퍼가 4Mb 블록 제한에 도달하거나 중복 제거 기간이 경과하면(이 자습서의 마지막 검사점부터 1시간 이후) 이 메서드는 검사점을 트리거합니다.

**AppendAndCheckPoint** 메서드는 먼저 blob에 추가될 블록에 대한 **blockId**를 생성합니다. Azure Storage는 모든 블록 ID가 동일한 길이를 갖도록 하므로 메서드는 선행 0으로 오프셋을 채웁니다. 그런 다음 해당 ID의 블록이 이미 Blob에 있는 경우 메서드는 현재 블록 내용으로 덮어씁니다.

> [AZURE.NOTE] 코드를 단순화하기 위해 이 자습서는 파티션당 단일 Blob 파일을 사용하여 메시지를 저장합니다. 실제 솔루션은 특정 기간이 결과한 후에 또는 특정 크기에 도달할 때 추가 파일을 만들어 파일 롤링을 구현합니다. Azure 블록 Blob는 최대 195GB의 데이터를 포함할 수 있습니다.

다음 작업은 **IEventProcessor** 인터페이스를 구현하는 것입니다.

1. 텍스트 편집기를 사용하여 process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\EventProcessor.java 파일을 만듭니다.

2. EventProcessor.java 파일에 다음 가져오기 및 클래스 정의를 추가합니다. **EventProcessor** 클래스는 이벤트 허브 클라이언트의 동작을 정의하는 **IEventProcessor** 인터페이스를 구현합니다.

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. **EventProcessor** 클래스에 다음 메서드를 추가하여 **IEventProcessor** 인터페이스를 구현합니다.

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. 다음 클래스 수준 변수를 **EventProcessor** 클래스에 추가합니다.

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. 다음 서명이 있는 **AppendAndCheckPoint** 메서드를 **EventProcessor** 클래스에 추가합니다.

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. 다음 코드를 **AppendAndCheckPoint** 메서드에 추가하여 파티션의 현재 메시지 오프셋 및 시퀀스 번호를 검색합니다.

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. **AppendAndCheckPoint** 메서드에서 현재 오프셋 값을 사용하여 blob에 저장할 다음 블록에 대한 **BlockEntry** 인스턴스를 만듭니다.

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. **AppendAndCheckPoint** 메서드에서 블록 blob에는 최신 메시지 집합을 업로드하고 블록의 현재 목록을 검색합니다.

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. **AppendAndCheckPoint** 메서드에서 새 blob에 초기 블록을 만들거나 기존 blob에 블록을 추가합니다.

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. 마지막으로 **AppendAndCheckPoint** 메서드에서 파티션에 검사점을 만들고 메시지의 다음 블록을 저장할 준비를 수행합니다.

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. **onEvents** 메서드에서 다음 코드를 추가하여 IoT Hub 끝점에서 메시지를 수신하고 대화형 메시지를 서비스 버스 큐에 전달합니다. 그런 후 블록이 가득 차거나 제한 시간이 만료될 때 **AppendAndCheckPoint** 메서드를 호출합니다.

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. 마지막으로 **onEvents** 메서드에서, IoT Hub에서 들어오는 메시지가 없는 동안 제한 시간이 만료되는 경우 **AppendAndCheckPoint**를 호출하도록 'else if' 절을 추가합니다.

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. EventProcessor.java 파일에 대한 변경 내용을 저장합니다.

**process-d2c-messages** 프로젝트의 마지막 작업은 **EventProcessorHost** 인스턴스를 인스턴스화하는 코드를 **main** 메서드에 추가하는 것입니다.

1. 텍스트 편집기를 사용하여 process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java 파일을 엽니다.

2. 파일에 다음 **import** 문을 추가합니다.

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. **{yourstorageaccountconnectionstring}**을(를) 이전에 [Azure Storage 계정 및 서비스 버스 큐 프로비전](#provision-an-azure-storage-account-and-a-service-bus-queue) 섹션에서 적어둔 Azure Storage 계정 연결 문자열로 바꿉니다.

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. 다음 클래스 수준 변수를 **App** 클래스에 추가하고 **{yourservicebusnamespace}**을(를) 서비스 버스 네임스페이스로 바꾸고 **{yourservicebussendkey}**을(를) 큐의 **send** 키와 바꿉니다. 이전에 [Azure Storage 계정 및 서비스 버스 큐 프 비전](#provision-an-azure-storage-account-and-a-service-bus-queue) 섹션에서 다음과 같은 네임스페이스 및 **listen** 키 값을 적어두었을 것입니다.

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. **{youreventhubcompatibleendpoint}**을(를) 이벤트 허브 호환 끝점 이름으로 바꿉니다. 끝점 이름은 **ihs....namespace**와 같이 보이므로 **sb://** 접두사 및 **.servicebus.windows.net/** 접미사를 제거해야 합니다. **{youreventhubcompatiblename}**을(를) 이벤트 허브 호환 이름으로 바꿉니다. **{youriothubkey}**을(를) **iothubowner** 키와 바꿉니다. *Java용 Azure IoT Hub 시작* 자습서의 [IoT Hub 만들기][lnk-create-an-iot-hub] 섹션에서 다음 값을 적어두었을 것입니다.

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. **main** 메서드의 서명을 다음과 같이 수정합니다.

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. 다음 코드를 **main** 메서드에 추가하여 메시지를 저장하는 blob 컨테이너에 대한 참조를 가져옵니다.

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. 다음 코드를 **main** 메서드에 추가하여 서비스 버스 서비스에 대한 참조를 가져옵니다.

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. **main** 메서드에서 **EventProcessorHost** 인스턴스를 구성하고 만듭니다. **setInvokeProcessorAfterReceiveTimeout** 옵션을 사용하면 처리할 메시지가 없어도 **EventProcessorHost** 인스턴스가 **IEventProcessor** 인터페이스에서 **onEvents** 메서드를 호출합니다. **onEvents** 메서드는 제한 시간이 만료되면 **AppendAndCheckPoint** 메서드를 항상 호출합니다.

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. **main** 메서드에서 **EventProcessorHost** 인스턴스에 **IEventProcessor** 구현을 등록합니다.

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. 마지막으로 **main** 메서드에 **EventProcessorHost** 인스턴스를 종료하는 논리를 추가합니다.

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java 파일을 저장한 후 닫습니다.

13. Maven을 사용하여 **process-d2c-messages** 응용 프로그램을 빌드하려면 process-d2c-messages 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```
    mvn clean package -DskipTests
    ```

## 대화형 메시지 수신

이 섹션에서는 서비스 버스 큐에서 대화형 메시지를 수신하는 Java 콘솔 응용 프로그램을 작성합니다.

첫 번째 작업은 **EventProcessor** 인스턴스에서 서비스 버스 큐에 전송된 메시지를 수신하는 **process-interactive-messages**라는 Maven 프로젝트를 추가하는 것입니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 [IoT Hub 시작] 자습서에서 만든 iot-java-get-started 폴더에 **process-interactive-messages**라는 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 명령 프롬프트에서 새 process-interactive-messages 폴더로 이동합니다.

3. 텍스트 편집기를 사용하여 process-interactive-messages 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이 종속성을 사용하면 응용 프로그램에서 azure-servicebus 패키지를 사용하여 서비스 버스 큐와 상호 작용할 수 있습니다.

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. pom.xml 파일을 저장하고 닫습니다.

다음 작업은 서비스 버스 큐에서 메시지를 검색하는 코드를 추가하는 것입니다.

1. 텍스트 편집기를 사용하여 process-interactive-messages\\src\\main\\java\\com\\mycompany\\app\\App.java 파일을 엽니다.

2. 다음 `import` 문을 파일에 추가합니다.

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. 다음 클래스 수준 변수를 **App** 클래스에 추가하고 **{yourservicebusnamespace}**을(를) 서비스 버스 네임스페이스로 바꾸고 **{yourservicebuslistenkey}**을(를) 큐의 **listen** 키와 바꿉니다. 이전에 [Azure Storage 계정 및 서비스 버스 큐 프 비전](#provision-an-azure-storage-account-and-a-service-bus-queue) 섹션에서 다음과 같은 네임스페이스 및 **listen** 키 값을 적어두었을 것입니다.

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. 중첩된 다음 클래스를 **App** 클래스에 추가하여 큐에서 메시지를 받습니다.

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. **main** 메서드의 서명을 다음과 같이 수정합니다.

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. **main** 메서드에서 다음 코드를 추가하여 새 메시지의 수신을 시작합니다.

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. process-interactive-messages\\src\\main\\java\\com\\mycompany\\app\\App.java 파일을 저장한 후 닫습니다.

8. Maven을 사용하여 **process-interactive-messages** 응용 프로그램을 빌드하려면 process-interactive-messages 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```
    mvn clean package -DskipTests
    ```

## 응용 프로그램 실행

이제 세 개의 응용 프로그램을 실행할 준비가 되었습니다.

1.	**process-interactive-messages** 응용 프로그램을 실행하려면 명령 프롬프트 또는 셸에서 process-interactive-messages 폴더로 이동한 후 다음 명령을 실행합니다.

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![process-interactive-messages 실행][processinteractive]

2.	**process-d2c-messages** 응용 프로그램을 실행하려면 명령 프롬프트 또는 셸에서 process-d2c-messages 폴더로 이동한 후 다음 명령을 실행합니다.

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![process-d2c-messages 실행][processd2c]

3.	**simulated-device** 응용 프로그램을 실행하려면 명령 프롬프트 또는 셸에서 simulated-device 폴더로 이동한 후 다음 명령을 실행합니다.

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![simulated-device 실행][simulateddevice]

> [AZURE.NOTE] Blob 파일의 업데이트를 보려면 **StoreEventProcessor** 클래스의 **MAX\_BLOCK\_SIZE** 상수를 **1024**와 같은 더 작은 값으로 줄여야 합니다. 시뮬레이션된 장치에서 보낸 데이터로 블록 크기 제한에 도달하는데 시간이 걸리기 때문에 이렇게 변경하는 것이 좋습니다. 블록 크기가 작을수록 Blob가 만들어지고 업데이트되는 과정을 오래 기다리지 않습니다. 그러나 더 큰 블록 크기를 사용하면 응용 프로그램을 더 확장할 수 있습니다.

## 다음 단계

이 자습서에서 [EventProcessorHost]를 사용하여 안정적으로 데이터 요소 및 대화형 장치-클라우드 메시지를 처리하는 방법을 알아보았습니다.

[IoT Hub를 사용하여 클라우드-장치 메시지를 보내는 방법][lnk-c2d]에서는 백 엔드에서 사용자 장치에 메시지를 전송하는 방법을 보여 줍니다.

IoT Hub를 사용하는 전체 종단 간 솔루션의 예를 보려면 [Azure IoT Suite][lnk-suite]를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드]를 참조하세요.

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[Azure Blob 저장소]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure 데이터 팩터리]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight(Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[Azure 서비스 버스 큐]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Azure IoT Hub 개발자 가이드 - 장치-클라우드]: iot-hub-devguide.md#d2c

[Azure 저장소]: https://azure.microsoft.com/documentation/services/storage/
[Azure 서비스 버스]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[IoT Hub 시작]: iot-hub-java-java-getstarted.md
[IoT Hub로 시작]: iot-hub-java-java-getstarted.md
[Azure IoT 개발자 센터]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Azure 저장소 정보]: ../storage/storage-create-storage-account.md#create-a-storage-account
[이벤트 허브 시작]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Azure 저장소 확장성 지침]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[이벤트 허브]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Event Hubs Programming Guide]: https://github.com/Azure/azure-event-hubs/blob/master/java/readme.md
[일시적인 오류 처리]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub

<!---HONumber=AcomDC_0928_2016-->