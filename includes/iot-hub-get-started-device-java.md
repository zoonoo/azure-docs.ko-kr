## 시뮬레이션된 장치 앱 만들기

이 섹션에서는 IoT Hub로 장치-클라우드 메시지를 전송하는 장치를 시뮬레이션하는 Java 콘솔 앱을 작성합니다.

1. *장치 ID 만들기* 섹션에서 만든 iot-java-get-started 폴더에서 명령 프롬프트에서 다음 명령을 사용하여 **simulated-device**라는 새 Maven 프로젝트를 만듭니다. 이것은 하나의 긴 명령입니다.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 명령 프롬프트에서 새 simulated-device 폴더로 이동합니다.

3. 텍스트 편집기를 사용하여 simulated-device 폴더에서 pom.xml 파일을 열고 다음 종속성을 **종속성** 노드에 추가합니다. 이 옵션을 사용하면 IoT Hub와 통신하고 JSON으로 Java 개체를 직렬화하도록 응용 프로그램에서 iothub-java-client 패키지를 사용할 수 있습니다.

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

7. 다음 클래스 수준의 변수를 **App** 클래스에 추가하여 **{youriothubname}**을 IoT Hub 이름으로, **{yourdeviceid}** 및 **{yourdevicekey}**를 *장치 ID 만들기* 섹션에서 생성된 장치 값으로 대체합니다.

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```

    이 샘플 응용 프로그램은 **DeviceClient** 개체를 인스턴스화할 때 **protocol** 변수를 사용합니다. HTTPS 또는 AMQPS 프로토콜을 사용하여 IoT Hub와 통신할 수 있습니다.

8. **App** 클래스 안에 다음 중첩 **TelemetryDataPoint** 클래스를 추가하여 장치가 IoT Hub에 전송하는 원격 분석 데이터를 지정합니다.

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

9. **App** 클래스 안에 다음 중첩 **EventCallback** 클래스를 추가하여 시뮬레이션된 장치에서 메시지를 처리하는 경우 IoT Hub가 반환하는 승인 상태를 표시합니다. 이 메서드는 또한 메시지가 처리되면 응용 프로그램에서 메인 스레드를 알립니다.

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

10. **App** 클래스 안에 다음 중첩 **MessageSender** 클래스를 추가합니다. 이 클래스에서 **run** 메서드는 IoT Hub로 보내는 샘플 원격 분석 데이터를 생성하고 다음 메시지를 보내기 전에 승인을 기다립니다.

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

11. **main** 메서드를 IoT Hub에 장치-클라우드 메시지를 보내는 스레드를 만드는 다음 코드로 대체합니다.

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

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0323_2016-->