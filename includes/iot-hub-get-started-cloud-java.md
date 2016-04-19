## 장치 ID 만들기

이 섹션에서는 IoT Hub의 ID 레지스트리에서 새 장치 ID를 만드는 Java 콘솔 앱을 작성합니다. 장치 ID 레지스트리에 항목이 없는 경우 장치를 IoT Hub에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]의 **장치 ID 레지스트리** 섹션을 참조하세요. 이 콘솔 응용 프로그램을 실행하면 장치-클라우드 메시지를 IoT Hub로 보낼 때 장치가 자체적으로 ID를 식별할 수 있는 고유한 장치 ID와 키를 생성합니다.

1. iot-java-get-started라는 새로운 빈 폴더를 만듭니다. iot-java-get-started 폴더에서 명령 프롬프트에서 다음 명령을 사용하여 **create-device-identity**라는 새 Maven 프로젝트를 만듭니다. 이것은 하나의 긴 명령입니다.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 명령 프롬프트에서 새 create-device-identity 폴더로 이동합니다.

3. 텍스트 편집기를 사용하여 create-device-identity 폴더에서 pom.xml 파일을 열고 다음 종속성을 **종속성** 노드에 추가합니다. 응용 프로그램에서 iothub-service-sdk 패키지를 사용할 수 있습니다.

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

7. 다음 클래스 수준 변수를 **App** 클래스에 추가하여 **{yourhubname}** 및 **{yourhubkey}**를 이전에 기록한 값으로 대체합니다.

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. 아래 표시된 예외를 포함하도록 **main** 메서드의 서명을 수정합니다.

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. **main** 메서드의 본문으로 다음 코드를 추가합니다. 존재하지 않는 경우 이 코드는 IoT Hub ID 레지스트리에 *javadevice*라는 장치를 만듭니다. 그런 다음 나중에 필요한 장치 ID와 키를 표시합니다.

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

13. **장치 ID** 및 **장치 키**를 적어 둡니다. 나중에 장치로 IoT Hub에 연결하는 응용 프로그램을 만들 때 필요합니다.

> [AZURE.NOTE] IoT Hub ID 레지스트리는 장치 ID만 저장하여 허브에 보안 액세스를 사용합니다. 보안 자격 증명으로 사용하기 위해 장치 ID 및 키와 개별 장치에 대해 액세스하지 못하도록 설정할 수 있는 사용/사용 안 함 플래그를 저장합니다. 응용 프로그램이 다른 장치별 메타데이터를 저장해야 하는 경우 응용 프로그램별 저장소를 사용해야 합니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]를 참조하세요.

## 장치-클라우드 메시지 받기

이 섹션에서는 IoT Hub에서 장치-클라우드 메시지를 읽는 Java 콘솔 앱을 만듭니다. IoT Hub가 [이벤트 허브][lnk-event-hubs-overview]와 호환되는 끝점을 노출하여 장치-클라우드 메시지를 읽을 수 있습니다. 작업을 단순화하기 위해 이 자습서에서는 처리량이 높은 배포용이 아닌 기본적인 판독기를 만듭니다. [장치-클라우드 메시지 처리][lnk-processd2c-tutorial] 자습서에서는 대규모로 장치-클라우드 메시지를 처리하는 방법을 보여줍니다. [이벤트 허브 시작][lnk-eventhubs-tutorial] 자습서는 이벤트 허브에서 메시지를 처리하는 방법에 대한 추가 정보를 제공하고 IoT Hub 이벤트 허브 호환 끝점에 적용할 수 있습니다.

> [AZURE.NOTE] 장치-클라우드 메시지를 읽는 이벤트 허브와 호환 가능한 끝점은 항상 AMQPS 프로토콜을 사용합니다.

1. *장치 ID 만들기* 섹션에서 만든 iot-java-get-started 폴더에서 명령 프롬프트의 다음 명령을 사용하여 **read-d2c-messages**라는 새 Maven 프로젝트를 만듭니다. 이것은 하나의 긴 명령입니다.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 명령 프롬프트에서 새 read-d2c-messages 폴더로 이동합니다.

3. 텍스트 편집기를 사용하여 read-d2c-messages 폴더에서 pom.xml 파일을 열고 다음 종속성을 **종속성** 노드에 추가합니다. 이 옵션을 사용하면 응용 프로그램에서 eventhub 클라이언트 패키지를 사용하여 이벤트 허브 호환 끝점에서 읽을 수 있습니다.

    ```
    <dependency>
      <groupId>com.microsoft.eventhubs.client</groupId>
      <artifactId>eventhubs-client</artifactId>
      <version>1.0</version>
    </dependency>
    ```

4. pom.xml 파일을 저장하고 닫습니다.

5. 텍스트 편집기를 사용하여 read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java 파일을 엽니다.

6. 파일에 다음 **가져오기** 문을 추가합니다.

    ```
    import java.io.IOException;
    import com.microsoft.eventhubs.client.Constants;
    import com.microsoft.eventhubs.client.EventHubClient;
    import com.microsoft.eventhubs.client.EventHubEnqueueTimeFilter;
    import com.microsoft.eventhubs.client.EventHubException;
    import com.microsoft.eventhubs.client.EventHubMessage;
    import com.microsoft.eventhubs.client.EventHubReceiver;
    import com.microsoft.eventhubs.client.ConnectionStringBuilder;
    ```

7. 다음 클래스 수준 변수를 **앱** 클래스에 추가합니다.

    ```
    private static EventHubClient client;
    private static long now = System.currentTimeMillis();
    ```

8. **앱** 클래스 안에 다음 중첩된 클래스를 추가합니다. 응용 프로그램은 이벤트 허브의 두 개의 파티션에서 메시지를 읽도록 두 개의 스레드를 만들어 **MessageReceiver**를 실행합니다.

    ```
    private static class MessageReceiver implements Runnable
    {
        public volatile boolean stopThread = false;
        private String partitionId;
    }
    ```

9. **MessageReceiver** 클래스에 다음 생성자를 추가합니다.

    ```
    public MessageReceiver(String partitionId) {
        this.partitionId = partitionId;
    }
    ```

10. **MessageReceiver** 클래스에 다음 **실행** 메서드를 추가합니다. 이 메서드는 이벤트 허브 파티션에서 읽도록 **EventHubReceiver** 인스턴스를 만듭니다. **stopThread**가 true일 때까지 지속적으로 반복하고 콘솔에 메시지 세부 정보를 인쇄합니다.

    ```
    public void run() {
      try {
        EventHubReceiver receiver = client.getConsumerGroup(null).createReceiver(partitionId, new EventHubEnqueueTimeFilter(now), Constants.DefaultAmqpCredits);
        System.out.println("** Created receiver on partition " + partitionId);
        while (!stopThread) {
          EventHubMessage message = EventHubMessage.parseAmqpMessage(receiver.receive(5000));
          if(message != null) {
            System.out.println("Received: (" + message.getOffset() + " | "
                + message.getSequence() + " | " + message.getEnqueuedTimestamp()
                + ") => " + message.getDataAsString());
          }
        }
        receiver.close();
      }
      catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
      }
    }
    ```

    > [AZURE.NOTE] 이 메서드는 수신기를 만들 때 필터를 사용하여 수신기는 수신기 실행이 시작된 후 IoT Hub에 전송된 메시지를 읽습니다. 테스트 환경에서 유용하므로 현재 일련의 메시지를 볼 수 있지만 프로덕션 환경에서 코드가 모든 메시지를 처리하고 있는지 확인해야 합니다. 자세한 정보는 [IoT Hub 장치-클라우드 메시지를 처리하는 방법][lnk-processd2c-tutorial] 자습서를 참조하세요.

11. 아래 표시된 예외를 포함하도록 **기본** 메서드의 서명을 수정합니다.

    ```
    public static void main( String[] args ) throws IOException
    ```

12. **앱** 클래스의 **기본** 메서드에 다음 코드를 추가합니다. 이 코드는 **EventHubClient** 인스턴스를 만들어 IoT Hub의 이벤트 허브 호환 끝점에 연결합니다. 그런 다음 두 개의 파티션에서 읽도록 두 개의 스레드를 만듭니다. **{youriothubkey}**, **{youreventhubcompatiblenamespace}** 및 **{youreventhubcompatiblename}**을 이전에 기록한 값으로 대체합니다. **{youreventhubcompatiblenamespace}** 자리 표시자의 값은 **이벤트 허브 호환 끝점**에서 나오며 **xxxxnamespace** 형태를 취합니다(즉, 포털의 이벤트 허브 호환 끝점 값에서 ****sb://** 접두사와 **.servicebus.windows.net** 접미사 제거).

    ```
    String policyName = "iothubowner";
    String policyKey = "{youriothubkey}";
    String namespace = "{youreventhubcompatiblenamespace}";
    String name = "{youreventhubcompatiblename}";
    try {
      ConnectionStringBuilder csb = new ConnectionStringBuilder(policyName, policyKey, namespace);
      client = EventHubClient.create(csb.getConnectionString(), name);
    }
    catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
    }
    
    MessageReceiver mr0 = new MessageReceiver("0");
    MessageReceiver mr1 = new MessageReceiver("1");
    Thread t0 = new Thread(mr0);
    Thread t1 = new Thread(mr1);
    t0.start(); t1.start();

    System.out.println("Press ENTER to exit.");
    System.in.read();
    mr0.stopThread = true;
    mr1.stopThread = true;
    client.close();
    ```

    > [AZURE.NOTE] 이 코드는 F1(무료) 계층에서 IoT Hub를 만들었다고 가정합니다. 무료 IoT Hub에는 "0" 및 "1"이라는 두 개의 파티션이 있습니다. 다른 가격 책정 계층 중 하나를 사용하여 IoT Hub를 만든 경우 각 파티션에 대해 **MessageReceiver**를 만들도록 코드를 조정해야 합니다.

13. App.java 파일을 저장하고 닫습니다.

14. Maven을 사용하여 **read-d2c-messages** 응용 프로그램을 빌드하려면 read-d2c-messages 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```
    mvn clean package -DskipTests
    ```



<!-- Links -->

[lnk-eventhubs-tutorial]: ../articles/event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../articles/event-hubs/event-hubs-overview.md
[lnk-processd2c-tutorial]: ../articles/iot-hub/iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0413_2016-->