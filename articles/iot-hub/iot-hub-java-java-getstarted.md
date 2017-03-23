---
title: "Azure IoT Hub 시작(Java) | Microsoft Docs"
description: "Java용 Azure IoT SDK를 사용하여 장치에서 Azure IoT Hub로 장치-클라우드 메시지를 보내는 방법입니다. 메시지를 보내는 시뮬레이션된 장치 앱, ID 레지스트리에서 장치를 등록할 서비스 앱 및 IoT Hub에서 장치-클라우드로 메시지를 읽는 서비스 앱을 만듭니다."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 70dae4a8-0e98-4c53-b5a5-9d6963abb245
ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b7dbfff716806e8b91488d3eb5eafab582e173ba
ms.lasthandoff: 03/15/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-java"></a>Java를 사용하여 IoT Hub에 시뮬레이션된 장치 연결
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

이 자습서의 끝 부분에서 다음의 세 가지 Java 콘솔 앱이 만들어집니다.

* **create-device-identity**는 장치 ID 및 시뮬레이션된 보안 키를 만들어 시뮬레이션된 장치 앱에 연결합니다.
* **read-d2c-messages**는 시뮬레이션된 장치 앱에서 보낸 원격 분석을 표시합니다.
* **simulated-device**는 앞에서 만든 장치 ID로 IoT Hub에 연결하고 MQTT 프로토콜을 사용하여 매초마다 원격 분석 메시지를 보냅니다.

> [!NOTE]
> [Azure IoT SDKs][lnk-hub-sdks] 문서는 장치와 솔루션 백 엔드에서 실행하기 위해 두 앱을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보를 제공합니다.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* Java SE 8. <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 Java를 설치하는 방법을 설명합니다.
* Maven 3.  <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 [Maven][lnk-maven]을 설치하는 방법을 설명합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

마지막 단계로 **기본 키** 값을 적어둡니다. 그런 다음 **끝점** 및 **이벤트** 기본 제공 끝점을 클릭합니다. **속성** 블레이드에서 **Event Hub 호환 이름** 및 **Event Hub 호환 끝점** 주소를 기록해 둡니다. **read-d2c-messages** 앱을 만들 때 이러한 값이 필요합니다.

![Azure Portal IoT Hub 메시징 블레이드][6]

IoT Hub를 만들었습니다. 이 자습서를 완료하는 데 필요한 IoT Hub 호스트 이름, IoT Hub 연결 문자열, IoT Hub 기본 키, Event Hub 호환 이름 및 Event Hub 호환 끝점이 있습니다.

## <a name="create-a-device-identity"></a>장치 ID 만들기
이 섹션에서는 IoT Hub의 ID 레지스트리에서 장치 ID를 만드는 Java 콘솔 앱을 작성합니다. ID 레지스트리에 항목이 없는 경우 장치를 IoT Hub에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]의 **ID 레지스트리** 섹션을 참조하세요. 이 콘솔 앱을 실행하면 장치-클라우드 메시지를 IoT Hub로 보낼 때 장치가 자체적으로 ID를 식별하는 데 사용할 수 있는 고유한 장치 ID와 키를 생성합니다.

1. iot-java-get-started라는 빈 폴더를 만듭니다. 명령 프롬프트에서 다음 명령을 사용하여 iot-java-get-started 폴더에 **create-device-identity**라는 새 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. 명령 프롬프트에서 create-device-identity 폴더로 이동합니다.
3. 텍스트 편집기를 사용하여 create-device-identity 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성으로 인해 앱에서 iot-service-client 패키지를 사용할 수 있습니다.
   
    ```
    </dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.1.15</version>
    </dependency>
    ```
    
    > [!NOTE]
    > [Maven 검색][lnk-maven-service-search]을 사용하여 **iot-service-client**의 최신 버전을 확인할 수 있습니다.

4. pom.xml 파일을 저장하고 닫습니다.
5. 텍스트 편집기를 사용하여 create-device-identity\src\main\java\com\mycompany\app\App.java 파일을 엽니다.
6. 파일에 다음 **import** 문을 추가합니다.
   
    ```
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.Device;
    import com.microsoft.azure.sdk.iot.service.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. **App** 클래스에 다음 클래스 수준 변수를 추가하고 **{yourhubconnectionstring}**를 앞에서 기록해둔 값으로 바꿉니다.
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
   
    ```
8. **main** 메서드의 서명을 수정하여 다음과 같은 예외를 포함합니다.
   
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
9. **main** 메서드의 본문으로 다음 코드를 추가합니다. 이 코드는 IoT Hub ID 레지스트리에 *javadevice* 라는 장치를 만듭니다(없는 경우). 그런 다음 나중에 필요한 장치 ID와 키를 표시합니다.
   
    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);
   
    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device ID: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```
10. App.java 파일을 저장하고 닫습니다.
11. Maven을 사용하여 **create-device-identity** 앱을 빌드하려면 create-device-identity 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.
    
    ```
    mvn clean package -DskipTests
    ```
12. Maven을 사용하여 **create-device-identity** 앱을 실행하려면 create-device-identity 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.
    
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
13. **장치 ID**와 **장치 키**를 기록해 둡니다. 나중에 장치로 IoT Hub에 연결하는 앱을 만들 때 이러한 값이 필요합니다.

> [!NOTE]
> IoT Hub ID 레지스트리는 장치 ID만 저장하여 IoT Hub에 보안 액세스를 사용합니다. 보안 자격 증명으로 사용하기 위해 장치 ID 및 키와 개별 장치에 대해 액세스하지 못하도록 설정할 수 있는 사용/사용 안 함 플래그를 저장합니다. 앱이 다른 장치별 메타데이터를 저장해야 할 경우 앱별 저장소를 사용해야 합니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]를 참조하세요.
> 
> 

## <a name="receive-device-to-cloud-messages"></a>장치-클라우드 메시지 받기
이 섹션에서는 IoT Hub에서 장치-클라우드 메시지를 읽는 Java 콘솔 앱을 만듭니다. IoT Hub가 [Event Hub][lnk-event-hubs-overview] 호환 끝점을 노출하여 장치-클라우드 메시지를 읽을 수 있습니다. 작업을 단순화하기 위해 이 자습서에서는 처리량이 높은 배포용이 아닌 기본적인 판독기를 만듭니다. [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial] 자습서는 대량의 장치-클라우드 메시지를 처리하는 방법을 보여 줍니다. [Event Hubs 시작][lnk-eventhubs-tutorial] 자습서는 Event Hubs의 메시지를 처리하는 방법에 대해 추가 정보를 제공하며 IoT Hub Event Hub 호환 끝점에 적용됩니다.

> [!NOTE]
> 장치-클라우드 메시지를 읽는 Event Hub와 호환 가능한 끝점은 항상 AMQP 프로토콜을 사용합니다.
> 
> 

1. *장치 ID 만들기* 섹션에서 만든 iot-java-get-started 폴더의 명령 프롬프트에서 다음 명령을 사용하여 **read-d2c-messages**라는 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. 명령 프롬프트에서 read-d2c-messages 폴더로 이동합니다.
3. 텍스트 편집기를 사용하여 read-d2c-messages 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 앱에서 eventhubs-client 패키지를 사용하여 Event Hub 호환 끝점에서 읽을 수 있습니다.
   
    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.13.0</version> 
    </dependency>
    ```

    > [!NOTE]
    > [Maven 검색][lnk-maven-eventhubs-search]을 사용하여 **azure-eventhubs**의 최신 버전을 확인할 수 있습니다.

4. pom.xml 파일을 저장하고 닫습니다.
5. 텍스트 편집기를 사용하여 read-d2c-messages\src\main\java\com\mycompany\app\App.java 파일을 엽니다.
6. 파일에 다음 **import** 문을 추가합니다.
   
    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;

    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.function.*;
    ```
7. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. **{youriothubkey}**, **{youreventhubcompatibleendpoint}** 및 **{youreventhubcompatiblename}**을 앞에서 기록해둔 값으로 바꿉니다.
   
    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```
8. 다음 **receiveMessages** 메서드를 **App** 클래스에 추가합니다. 이 메서드는 **EventHubClient** 인스턴스를 만들어 Event Hub 호환 끝점에 연결하고 **PartitionReceiver** 인스턴스를 비동기식으로 만들어 Event Hub 파티션에서 읽습니다. 계속해서 반복하고 앱이 종료될 때까지 메시지 세부 정보를 출력합니다.
   
    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getSystemProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```
   
   > [!NOTE]
   > 이 메서드는 수신기를 만들 때 필터를 사용하여 수신기는 수신기 실행이 시작된 후 IoT Hub에 전송된 메시지를 읽습니다. 이 방법은 테스트 환경에서 현재 메시지 집합을 볼 수 있어 유용합니다. 프로덕션 환경에서는 코드가 모든 메시지를 처리하는지 확인해야 합니다. 자세한 내용은 [IoT Hub 장치-클라우드 메시지 처리 방법][lnk-process-d2c-tutorial] 자습서를 참조하세요.
   > 
   > 
9. **main** 메서드의 서명을 수정하여 다음과 같은 예외를 포함합니다.
   
    ```
    public static void main( String[] args ) throws IOException
    ```
10. **App** 클래스의 **main** 메서드에 다음 코드를 추가합니다. 이 코드에서는 **EventHubClient** 및 **PartitionReceiver**라는 두 개의 인스턴스를 만들고 메시지 처리를 완료할 때 앱을 닫을 수 있습니다.
    
    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```
    
    > [!NOTE]
    > 이 코드는 F1(무료) 계층에서 IoT Hub를 만들었다고 가정합니다. 무료 IoT Hub에는 "0" 및 "1"이라는 두 개의 파티션이 있습니다.
    > 
    > 
11. App.java 파일을 저장하고 닫습니다.
12. Maven을 사용하여 **read-d2c-messages** 앱을 빌드하려면 read-d2c-messages 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.
    
    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 IoT Hub로 장치-클라우드 메시지를 전송하는 장치를 시뮬레이션하는 Java 콘솔 앱을 작성합니다.

1. *장치 ID 만들기* 섹션에서 만든 iot-java-get-started 폴더의 명령 프롬프트에서 다음 명령을 사용하여 **simulated-device**라는 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. 명령 프롬프트에서 simulated-device 폴더로 이동합니다.
3. 텍스트 편집기를 사용하여 simulated-device 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT Hub와 통신하고 JSON으로 Java 개체를 직렬화하도록 앱에서 iothub-java-client 패키지를 사용할 수 있습니다.
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.0.21</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 검색][lnk-maven-device-search]을 사용하여 **iot-device-client**의 최신 버전을 확인할 수 있습니다.

4. pom.xml 파일을 저장하고 닫습니다.
5. 텍스트 편집기를 사용하여 simulated-device\src\main\java\com\mycompany\app\App.java 파일을 엽니다.
6. 파일에 다음 **import** 문을 추가합니다.
   
    ```
    import com.microsoft.azure.sdk.iot.device.DeviceClient;
    import com.microsoft.azure.sdk.iot.device.IotHubClientProtocol;
    import com.microsoft.azure.sdk.iot.device.Message;
    import com.microsoft.azure.sdk.iot.device.IotHubStatusCode;
    import com.microsoft.azure.sdk.iot.device.IotHubEventCallback;
    import com.microsoft.azure.sdk.iot.device.MessageCallback;
    import com.microsoft.azure.sdk.iot.device.IotHubMessageResult;
    import com.google.gson.Gson;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```
7. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. **{youriothubname}**을 IoT Hub 이름으로 바꾸고 **{yourdevicekey}**를 *장치 ID 만들기* 섹션에서 만든 장치 키 값으로 바꿉니다.
   
    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    이 샘플 앱은 **DeviceClient** 개체를 인스턴스화할 때 **프로토콜** 변수를 사용합니다. MQTT, AMQP 또는 HTTP 프로토콜을 사용하여 IoT Hub와 통신할 수 있습니다.
8. 다음의 중첩된 **TelemetryDataPoint** 클래스를 **App** 클래스 안에 추가하여 장치가 IoT Hub에 전송한 원격 분석 데이터를 지정합니다.
   
    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. 다음의 중첩된 **EventCallback** 클래스를 **App** 클래스 안에 추가하여 시뮬레이션된 장치 앱의 메시지를 처리할 때 IoT Hub가 반환하는 확인 상태를 표시합니다. 이 메서드는 또한 메시지가 처리되면 앱에서 메인 스레드를 알립니다.
   
    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
   
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```
10. 다음의 중첩된 **MessageSender** 클래스를 **App** 클래스 안에 추가합니다. 이 클래스의 **run** 메서드는 IoT Hub에 전송할 샘플 원격 분석 데이터를 생성하고 다음 메시지를 전송하기 전에 확인을 기다립니다.
    
    ```
    private static class MessageSender implements Runnable {
    
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
    
          while (true) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
    
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```
    
    이 메서드는 IoT Hub에서 이전 메시지를 확인하고&1;초 후 새 장치-클라우드 메시지를 보냅니다. 메시지에는 deviceId가 있는 JSON 직렬화된 개체와 풍속 센서를 시뮬레이션하기 위해 임의로 생성된 숫자가 있습니다.
11. **main** 메서드를 장치-클라우드 메시지를 사용자의 IoT Hub로 전송하는 스레드를 만드는 다음 코드로 바꿉니다.
    
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```
12. App.java 파일을 저장하고 닫습니다.
13. Maven을 사용하여 **simulated-device** 앱을 빌드하려면 simulated-device 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.
    
    ```
    mvn clean package -DskipTests
    ```

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.
> 
> 

## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. read-d2c 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub에서 첫 번째 파티션의 모니터링을 시작합니다.
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![장치-클라우드 메시지를 모니터링하는 Java IoT Hub 서비스 앱][7]
2. simulated-device 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub에 원격 분석 데이터 전송을 시작합니다.
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![장치-클라우드 메시지를 전송하는 Java IoT Hub 장치 앱][8]
3. [Azure Portal][lnk-portal]의 **사용량** 타일에 IoT Hub로 전송된 메시지 수가 표시됩니다.
   
    ![IoT Hub에 전송된 메시지의 수를 보여주는 Azure Portal 사용량 타일][43]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 장치 ID를 만들었습니다. 장치-클라우드 메시지를 IoT Hub로 보내기 위해 시뮬레이션된 장치 앱을 사용하는 이 장치 ID를 사용했습니다. IoT Hub에서 받은 메시지를 표시하는 앱도 만들었습니다. 

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [장치 연결][lnk-connect-device]
* [장치 관리 시작][lnk-device-management]
* [IoT Gateway SDK 시작][lnk-gateway-SDK]

IoT 솔루션을 확장하고 대량의 장치-클라우드 메시지를 처리하는 방법을 알아보려면 [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial] 자습서를 참조하세요.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-eventhubs-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22
