<properties
	pageTitle="IoT Hub Java 클라이언트 만들기 | Microsoft Azure"
	description="이 자습서에 따라서 Azure IoT Hub와의 통신을 위해서 Java*용 Microsoft Azure IoT 장치 SDK를 사용하는 IoT Hub Java 클라이언트를 빌드합니다."
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
     ms.date="11/26/2015"
     ms.author="dobett"/>
     
# Java를 사용하여 Azure IoT Hub 클라이언트 응용 프로그램 만들기

이 문서는 Azure IoT Hub와의 통신을 위해서 [Java용 Microsoft Azure IoT 장치 SDK][lnk-java-sdk]를 사용하는 클라이언트 응용 프로그램을 만드는 방법을 설명합니다. 이 자습서는 [Maven][apache-maven] 도구를 사용하여 프로젝트를 만들고 빌드하는 방법을 보여줍니다. 이 지침은 Windows 또는 Linux 컴퓨터에서 따라 할 수 있습니다.

Java API 문서는 [여기][lnk-java-api-docs]에서 찾을 수 있습니다.

## 설치

Windows 또는 Linux의 개발 환경 설정 및 필수 구성 요소에 대한 정보는 [개발 환경 준비][devbox-setup]를 참조하세요.

> [AZURE.NOTE]로컬 Maven 리포지토리에 필요한 JAR 파일을 추가하고 필수 구성 요소를 설치하기 위하여, 이 자습서를 시작하기 전에 [개발 환경 준비][devbox-setup]에 포함된 단계를 완료하는 것이 중요합니다.

## 프로젝트 만들기

1. 명령줄 도구에서 다음 명령을 실행하여 개발 컴퓨터의 적절한 위치에 빈 Maven 프로젝트를 새로 만듭니다.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=iot-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [AZURE.NOTE]이것은 하나의 긴 명령입니다. 명령줄 도구에 붙여 넣으려면 명령 전체를 복사해야 합니다.

    이 명령은 표준 Maven 프로젝트 구조를 포함하는 *iot-device*라는 이름의 프로젝트 폴더를 만듭니다. 자세한 내용은 Apache 웹 사이트의 [Maven in 5 Minutes][maven-five-minutes]를 참조하세요.

2. 텍스트 편집기에서 iot-device 폴더의 **pom.xml** 파일을 엽니다.

3. 다음과 같은 새 **dependency** 섹션을 기존 섹션 다음에 추가하여 필요한 클라이언트 라이브러리를 포함시킵니다.

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-client</artifactId>
      <version>1.0.0-preview.4</version>
    </dependency>
    ```

4. **dependencies** 섹션 뒤에 다음과 같은 **build** 섹션을 추가하여 JAR 파일이 dependencies를 포함하고 manifest가 **main** 클래스를 식별하도록 합니다.

    ```
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-shade-plugin</artifactId>
          <executions>
            <execution>
              <phase>package</phase>
              <goals>
                <goal>shade</goal>
              </goals>
            </execution>
          </executions>
          <configuration>
            <finalName>${artifactId}-${version}-with-deps</finalName>
          </configuration>
        </plugin>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-jar-plugin</artifactId>
          <version>2.6</version>
          <configuration>
            <archive>
              <manifest>
                <addClasspath>true</addClasspath>
                <mainClass>com.mycompany.app.App</mainClass>
              </manifest>
            </archive>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. **pom.xml** 파일을 저장합니다.

## 응용 프로그램 만들기

1. 텍스트 편집기에서 iot-device/src/main/java/com/mycompany/app 폴더의 **App.java** 파일을 엽니다.

2. **package** 문 다음에 IoT 장치 라이브러리를 포함하는 다음과 같은 **import** 문을 추가합니다.

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
    import java.util.Scanner;
    
    import javax.naming.SizeLimitExceededException;
    ```

3. **App** 클래스 안에 중첩 클래스로 다음과 같은 **EventCallback** 클래스를 추가합니다. **EventCallback** 클래스의 **execute** 메서드는 장치가 장치-클라우드 메시지에 대한 응답으로 IoT Hub로부터 승인을 수신하면 호출됩니다.

    ```
    protected static class EventCallback
        implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        Integer i = (Integer) context;
        System.out.println("IoT Hub responded to message " + i.toString()
            + " with status " + status.name());
      }
    }
    ```

4. **App** 클래스 안에 중첩 클래스로 다음과 같은 **MessageCallback** 클래스를 추가합니다. **MessageCallback** 클래스의 **execute** 메서드는 장치가 수신한 클라우드-장치 메시지에 대한 응답으로 IoT Hub에 피드백 메시지를 보낼 수 있도록 합니다.

    ```
    protected static class MessageCallback
        implements com.microsoft.azure.iothub.MessageCallback
    {
      public IotHubMessageResult execute(Message msg,
          Object context)
      {
        System.out.println(
            "Received message with content: " + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
          return IotHubMessageResult.COMPLETE;
      }
    }
    ```

5. 기존 **main** 메서드를 다음을 수행하는 코드로 바꿉니다.

  - **DeviceClient** 인스턴스를 만듭니다.
  - 클라우드-장치 메시지에 대한 메시지 콜백을 초기화합니다.
  - 장치-클라우드 메시지를 보내고 클라우드-장치 메시지를 수신할 수 있도록 **DeviceClient**를 엽니다.
  - IoT Hub에 10개의 샘플 메시지를 보냅니다.

    `<your device connection string>`을 유효한 장치 연결 문자열로 바꿉니다. [DeviceExplorer][lnk-device-explorer] 도구 또는 [IoT Hub Explorer][lnk-iothub-explorer] 도구를 사용하여 장치를 프로비전하고 장치의 연결 문자열을 가져올 수 있습니다.

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException
    {
      String connString = "<your device connection string>";
      IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    
      DeviceClient client = new DeviceClient(connString, protocol);
    
      MessageCallback messageCallback = new MessageCallback();
      client.setMessageCallback(messageCallback, null);
    
      client.open();
    
      for (int i = 0; i < 10; ++i)
      {
        String msgStr = "Event Message " + Integer.toString(i);
        try
        {
          Message msg = new Message(msgStr);
          msg.setProperty("messageCount", Integer.toString(i));
          System.out.println(msgStr);
    
          EventCallback eventCallback = new EventCallback();
          client.sendEventAsync(msg, eventCallback, i);
        }
        catch (Exception e)
        {
        }
      }
    
      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
    
      client.close();
    }
    ```

6. 코드를 컴파일하고 JAR 파일 패키지로 만들려면 **iot-device** 프로젝트 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```
    mvn package
    ```

7. 응용 프로그램을 실행하려면 **iot-device** 프로젝트 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```
    java -jar target/iot-device-1.0-SNAPSHOT-with-deps.jar
    ```

8. [DeviceExplorer][lnk-device-explorer] 도구를 사용하여 IoT Hub가 수신하는 장치-클라우드 메시지를 모니터링하고 클라우드-장치 메시지를 IoT Hub에서 장치에 보낼 수 있습니다.

## 로깅 세분성 변경

로깅 세분성을 변경하려면 다음 줄을 `config.properties` 파일에 포함시킵니다.

```
.level = {LOGGING_LEVEL}
```

> [AZURE.NOTE]다양한 로깅 수준에 대한 설명은 [여기](http://docs.oracle.com/javase/7/docs/api/java/util/logging/Level.html)를 참조하세요.

그 후, JVM 속성 `java.util.logging.config.file={Path to your config.properties file}`을 설정합니다.

AMQP 프레임을 로깅하려면 명령 환경에서 환경 변수를 `PN_TRACE_FRM=1`로 설정합니다.


[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-java-api-docs]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[apache-maven]: https://maven.apache.org/index.html
[maven-five-minutes]: https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html
[devbox-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
[lnk-iothub-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/doc/provision_device.md

<!---HONumber=AcomDC_1203_2015-->