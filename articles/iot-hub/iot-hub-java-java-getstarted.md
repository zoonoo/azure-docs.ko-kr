<properties
	pageTitle="Java용 Azure IoT Hub 시작 | Microsoft Azure"
	description="Java용 Azure IoT Hub 시작 자습서입니다. Microsoft Azure IoT SDK를 포함한 Azure IoT Hub 및 Java를 사용하여 사물 인터넷의 솔루션을 구현합니다."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/06/2016"
     ms.author="dobett"/>

# Java용 Azure IoT Hub 시작

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## 소개

Azure IoT Hub는 수백만의 IoT(사물 인터넷) 장치와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. IoT 프로젝트가 직면하는 큰 과제 중 하나는 장치를 안정적이고 안전하게 솔루션 백 엔드에 연결하는 방법입니다. 이러한 문제를 해결하기 위해 IoT Hub는 다음을 수행합니다.

- 신뢰할 수 있는 장치-클라우드 및 클라우드-장치 하이퍼 스케일 메시징을 제공합니다.
- 장치 단위 보안 자격 증명 및 액세스 제어를 사용하여 통신을 보호합니다.
- 가장 인기 있는 언어 및 플랫폼에 대한 장치 라이브러리를 포함합니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

- Azure 포털을 사용하여 IoT Hub를 만듭니다.
- IoT Hub에서 장치 ID를 만듭니다.
- 클라우드 백 엔드에 원격 분석을 전송하는 시뮬레이션된 장치를 만듭니다.

이 자습서의 끝 부분에서 다음의 세 가지 Java 콘솔 응용 프로그램이 만들어집니다.

* **create-device-identity**는 장치 ID 및 시뮬레이션된 보안 키를 만들어 시뮬레이션된 장치에 연결합니다.
* **read-d2c-messages**는 시뮬레이션된 장치에서 보낸 원격 분석을 표시합니다.
* **simulated-device**는 앞에서 만든 장치 ID로 IoT Hub에 연결하고 AMQPS 프로토콜을 사용하여 매초마다 원격 분석 메시지를 보냅니다.

> [AZURE.NOTE] [IoT Hub Sdk][lnk-hub-sdks] 문서는 장치와 솔루션 백 엔드에서 실행하기 위해 두 응용 프로그램을 빌드하는 데 사용할 수 있는 다양한 SDK에 관한 정보를 제공합니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ Java SE 8. <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 Java를 설치하는 방법을 설명합니다.

+ Maven 3. <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 Maven을 설치하는 방법을 설명합니다.

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk-free-trial]을 참조하세요.

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

마지막 단계로 IoT Hub 블레이드에서 **설정**을 클릭한 다음 **설정** 블레이드에서 **메시징**을 클릭합니다. **메시징** 블레이드에서 **이벤트 허브 호환 이름** 및 **이벤트 허브 호환 끝점**을 기록해 둡니다. **read-d2c-messages** 응용 프로그램을 만들 때 이러한 값이 필요합니다.

![][6]

이제 IoT Hub를 만들었고 이 자습서의 나머지 부분을 완료해야 할 IoT Hub 호스트 이름, IoT Hub 연결 문자열, 이벤트 허브 호환 이름 및 이벤트 허브 호환 끝점이 있습니다.

## 장치 ID 만들기

이 섹션에서는 IoT Hub의 ID 레지스트리에서 새 장치 ID를 만드는 Java 콘솔 앱을 작성합니다. 장치 ID 레지스트리에 항목이 없는 경우 장치를 IoT Hub에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]의 **장치 ID 레지스트리** 섹션을 참조하세요. 이 콘솔 응용 프로그램을 실행하면 장치-클라우드 메시지를 IoT Hub로 보낼 때 장치가 자체적으로 ID를 식별할 수 있는 고유한 장치 ID와 키를 생성합니다.

1. iot-java-get-started라는 새로운 빈 폴더를 만듭니다. 명령 프롬프트에서 다음 명령을 사용하여 iot-java-get-started 폴더에 **create-device-identity**라는 새 Maven 프로젝트를 만듭니다. 이것은 하나의 긴 명령입니다.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 명령 프롬프트에서 새 create-device-identity 폴더로 이동합니다.

3. 텍스트 편집기를 사용하여 create-device-identity 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 응용 프로그램에서 iothub-service-sdk 패키지를 사용할 수 있습니다.

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.2</version>
    </dependency>
    ```
    
4. pom.xml 파일을 저장하고 닫습니다.

5. 텍스트 편집기를 사용하여 create-device-identity\\src\\main\\java\\com\\mycompany\\app\\App.java 파일을 엽니다.

6. 파일에 다음 **import** 문을 추가합니다.

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. **App** 클래스에 다음 클래스 수준 변수를 추가하고 **{yourhostname}** 및 **{yourhubkey}**를 앞에서 기록해둔 값으로 바꿉니다.

    ```
    private static final String connectionString = "HostName={yourhostname};SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. **main** 메서드의 서명을 수정하여 아래의 예외를 포함합니다.

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. **main** 메서드의 본문으로 다음 코드를 추가합니다. 이 코드는 IoT Hub ID 레지스트리에 *javadevice*라는 장치를 만듭니다(없는 경우). 그런 다음 나중에 필요한 장치 ID와 키를 표시합니다.

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
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. App.java 파일을 저장하고 닫습니다.

11. Maven을 사용하여 **create-device-identity** 응용 프로그램을 빌드하려면 create-device-identity 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```
    mvn clean package -DskipTests
    ```

12. Maven을 사용하여 **create-device-identity** 응용 프로그램을 실행하려면 create-device-identity 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. **장치 ID**와 **장치 키**를 기록해 둡니다. 나중에 장치로 IoT Hub에 연결하는 응용 프로그램을 만들 때 필요합니다.

> [AZURE.NOTE] IoT Hub ID 레지스트리는 장치 ID만 저장하여 허브에 보안 액세스를 사용합니다. 보안 자격 증명으로 사용하기 위해 장치 ID 및 키와 개별 장치에 대해 액세스하지 못하도록 설정할 수 있는 사용/사용 안 함 플래그를 저장합니다. 응용 프로그램이 다른 장치별 메타데이터를 저장해야 할 경우 응용 프로그램별 저장소를 사용해야 합니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]를 참조하세요.

## 장치-클라우드 메시지 받기

이 섹션에서는 IoT Hub에서 장치-클라우드 메시지를 읽는 Java 콘솔 앱을 만듭니다. IoT Hub가 [이벤트 허브][lnk-event-hubs-overview]와 호환되는 끝점을 노출하여 장치-클라우드 메시지를 읽을 수 있습니다. 작업을 단순화하기 위해 이 자습서에서는 처리량이 높은 배포용이 아닌 기본적인 판독기를 만듭니다. [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial] 자습서는 대량의 장치-클라우드 메시지를 처리하는 방법을 보여줍니다. [이벤트 허브 시작][lnk-eventhubs-tutorial] 자습서는 이벤트 허브의 메시지를 처리하는 방법에 대해 추가 정보를 제공하며 IoT Hub 이벤트 허브 호환 끝점에 적용됩니다.

> [AZURE.NOTE] 장치-클라우드 메시지를 읽는 이벤트 허브와 호환 가능한 끝점은 항상 AMQPS 프로토콜을 사용합니다.

1. *장치 ID 만들기* 섹션에서 만든 iot-java-get-started 폴더의 명령 프롬프트에서 다음 명령을 사용하여 **read-d2c-messages**라는 새 Maven 프로젝트를 만듭니다. 이것은 하나의 긴 명령입니다.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 명령 프롬프트에서 새 read-d2c-messages 폴더로 이동합니다.

3. 텍스트 편집기를 사용하여 read-d2c-messages 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이 옵션을 사용하면 응용 프로그램에서 eventhub 클라이언트 패키지를 사용하여 이벤트 허브 호환 끝점에서 읽을 수 있습니다.

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.7.1</version> 
    </dependency>
    ```

4. pom.xml 파일을 저장하고 닫습니다.

5. 텍스트 편집기를 사용하여 read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java 파일을 엽니다.

6. 파일에 다음 **import** 문을 추가합니다.

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. **{youriothubkey}**, **{youreventhubcompatibleendpoint}**, **{youreventhubcompatiblename}**을 앞에서 기록해둔 값으로 바꿉니다.

    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. 다음 **receiveMessages** 메서드를 **App** 클래스에 추가합니다. 이 메서드는 **EventHubClient** 인스턴스를 만들어 이벤트 허브 호환 끝점에 연결하고 **PartitionReceiver** 인스턴스를 비동기식으로 만들어 이벤트 허브 파티션에서 읽습니다. 계속해서 반복하고 응용 프로그램이 종료될 때까지 메시지 세부 정보를 출력합니다.

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
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
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

    > [AZURE.NOTE] 이 메서드는 수신기를 만들 때 필터를 사용하여 수신기는 수신기 실행이 시작된 후 IoT Hub에 전송된 메시지를 읽습니다. 테스트 환경에서는 현재 메시지 집합을 볼 수 있어 유용하지만 프로덕션 환경에서는 코드가 모든 메시지를 처리하는지 확인해야 합니다. 자세한 내용은 [IoT Hub 장치-클라우드 메시지 처리 방법][lnk-process-d2c-tutorial] 자습서를 참조하세요.

9. **main** 메서드의 서명을 수정하여 아래의 예외를 포함합니다.

    ```
    public static void main( String[] args ) throws IOException
    ```

10. **App** 클래스의 **main** 메서드에 다음 코드를 추가합니다. 이 코드에서는 두 **EventHubClient** 및 **PartitionReceiver** 인스턴스를 만들고 메시지 처리를 완료하면 응용 프로그램을 닫을 수 있습니다.

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

    > [AZURE.NOTE] 이 코드는 F1(무료) 계층에서 IoT Hub를 만들었다고 가정합니다. 무료 IoT Hub에는 "0" 및 "1"이라는 두 개의 파티션이 있습니다.

11. App.java 파일을 저장하고 닫습니다.

12. Maven을 사용하여 **read-d2c-messages** 응용 프로그램을 빌드하려면 read-d2c-messages 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```
    mvn clean package -DskipTests
    ```

## 시뮬레이션된 장치 앱 만들기

이 섹션에서는 IoT Hub로 장치-클라우드 메시지를 전송하는 장치를 시뮬레이션하는 Java 콘솔 앱을 작성합니다.

1. *장치 ID 만들기* 섹션에서 만든 iot-java-get-started 폴더의 명령 프롬프트에서 다음 명령을 사용하여 **simulated-device**라는 새 Maven 프로젝트를 만듭니다. 이것은 하나의 긴 명령입니다.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 명령 프롬프트에서 새 simulated-device 폴더로 이동합니다.

3. 텍스트 편집기를 사용하여 simulated-device 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이 옵션을 사용하면 IoT Hub와 통신하고 JSON으로 Java 개체를 직렬화하도록 응용 프로그램에서 iothub-java-client 패키지를 사용할 수 있습니다.

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.2</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. pom.xml 파일을 저장하고 닫습니다.

5. 텍스트 편집기를 사용하여 simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java 파일을 엽니다.

6. 파일에 다음 **import** 문을 추가합니다.

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Random;
    import javax.naming.SizeLimitExceededException;
    import com.google.gson.Gson;
    ```

7. 다음의 클래스 수준 변수를 **App** 클래스에 추가하고 **{youriothubname}**을 IoT Hub 이름, **{yourdeviceid}** 및 **{yourdevicekey}**를 *장치 ID 만들기* 섹션에서 만든 장치 값으로 바꿉니다.

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```

    이 응용 프로그램 예제는 **DeviceClient** 개체를 인스턴스화할 때 **프로토콜** 변수를 사용합니다. HTTPS 또는 AMQPS 프로토콜을 사용하여 IoT Hub와 통신할 수 있습니다.

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

9. 다음의 중첩된 **EventCallback** 클래스를 **App** 클래스 안에 추가하여 시뮬레이션된 장치의 메시지를 처리할 때 IoT Hub가 반환하는 확인 상태를 표시합니다. 이 메서드는 또한 메시지가 처리되면 응용 프로그램에서 메인 스레드를 알립니다.

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status " + status.name());
      
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
      public volatile boolean stopThread = false;

      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          DeviceClient client;
          client = new DeviceClient(connString, protocol);
          client.open();
        
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = "myFirstDevice";
            telemetryDataPoint.windSpeed = currentWindSpeed;
      
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println(msgStr);
        
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
          client.close();
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
    ```

    이 메서드는 IoT Hub에서 이전 메시지를 확인하고 1초 후 새 장치-클라우드 메시지를 보냅니다. 메시지에는 deviceId가 있는 JSON 직렬화된 개체와 풍속 센서를 시뮬레이션하기 위해 임의로 생성된 숫자가 있습니다.

11. **main** 메서드를 장치-클라우드 메시지를 사용자의 IoT Hub로 전송하는 스레드를 만드는 다음 코드로 바꿉니다.

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
    
      MessageSender ms0 = new MessageSender();
      Thread t0 = new Thread(ms0);
      t0.start(); 
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      ms0.stopThread = true;
    }
    ```

12. App.java 파일을 저장하고 닫습니다.

13. Maven을 사용하여 **simulated-device** 응용 프로그램을 빌드하려면 simulated-device 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1. read-d2c 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub에서 첫 번째 파티션의 모니터링을 시작합니다.

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"  -Dexec.args="0"
    ```

    ![][7]

1. read-d2c 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub에서 두 번째 파티션의 모니터링을 시작합니다.

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"  -Dexec.args="1"
    ```

    ![][7]

2. simulated-device 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub에 원격 분석 데이터 전송을 시작합니다.

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][8]

3. [Azure 포털][lnk-portal]의 **사용량** 타일에 허브로 전송된 메시지 수가 표시됩니다.

    ![][43]

## 다음 단계

이 자습서에서 포털에서 새 IoT Hub를 구성한 다음, 허브의 ID 레지스트리에서 장치 ID를 만들었습니다. 장치-클라우드 메시지를 허브로 보내기 위해 시뮬레이션된 장치 앱을 사용하는 이 장치 ID를 사용하고 허브에서 받은 메시지를 표시하는 앱을 만들었습니다. IoT Hub 기능 및 다른 IoT 시나리오는 다음의 자습서에서 계속해서 탐색할 수 있습니다.

- [IoT Hub를 사용하여 클라우드-장치 메시지 보내기][lnk-c2d-tutorial]는 장치에 메시지를 보내고 IoT Hub에서 생성된 전달 피드백을 처리하는 방법을 보여줍니다.
- [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial]는 장치에서 들어오는 대화형 메시지 및 원격 분석을 안정적으로 처리하는 방법을 보여 줍니다.
- [장치에서 파일 업로드][lnk-upload-tutorial]는 장치에서 파일을 쉽게 업로드하기 위해 클라우드-장치 메시지를 사용하는 패턴을 설명합니다.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0615_2016-->