---
title: "Azure IoT Hub 직접 메서드 사용 (Java) | Microsoft Docs"
description: "Azure IoT Hub 직접 메서드를 사용하는 방법입니다. Java용 Azure IoT 장치 SDK를 사용하여 직접 메서드를 포함한 시뮬레이션된 장치 앱을 구현하며 Java용 Azure IoT service SDK를 사용하여 직접 메서드를 호출하는 서비스 앱을 구현합니다."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 5fa42c4fe7ad04bc74f70b023715bb61f81806ab
ms.contentlocale: ko-kr
ms.lasthandoff: 08/09/2017

---
# <a name="use-direct-methods-java"></a>직접 메서드 사용(Java)

[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

이 자습서에서는 다음 두 개의 Java 콘솔 앱을 만듭니다.

* **invoke-direct-method**: 시뮬레이트된 장치 앱에서 메서드를 호출하고 응답을 표시하는 Java 백엔드 앱입니다.
* **simulated-device**: IoT Hub를 사용자가 만드는 장치 ID에 연결하는 장치를 시뮬레이트하는 Java 앱입니다. 이 앱은 백 엔드의 직접 호출에 응답합니다.

> [!NOTE]
> 장치와 솔루션 백 엔드에서 실행하기 위해 응용 프로그램을 빌드하는 데 사용할 수 있는 SDK에 관한 정보는 [Azure IoT SDK][lnk-hub-sdks]를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

* Java SE 8. <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 Java를 설치하는 방법을 설명합니다.
* Maven 3.  <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 [Maven][lnk-maven]을 설치하는 방법을 설명합니다.
* [Node.js 버전 0.10.0 이상](http://nodejs.org)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기

이 섹션에서는 솔루션 백 엔드에서 호출한 메서드에 응답하는 Java 콘솔 앱을 만듭니다.

1. iot-java-direct-method라는 빈 폴더를 만듭니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 iot-java-direct-method 폴더에 **simulated-device**라는 새 Maven 프로젝트를 만듭니다. 다음 명령은 긴 단일 명령입니다.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 명령 프롬프트에서 simulated-device 폴더로 이동합니다.

1. 텍스트 편집기를 사용하여 simulated-device 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT Hub와 통신하도록 앱에서 iot-device-client 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 검색][lnk-maven-device-search]을 사용하여 **iot-device-client**의 최신 버전을 확인할 수 있습니다.

1. **종속성** 노드 뒤에 다음 **빌드** 노드를 추가합니다. 이 구성에서는 Maven에 Java 1.8을 사용하여 앱을 빌드하도록 지시합니다.

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. pom.xml 파일을 저장하고 닫습니다.

1. 텍스트 편집기를 사용하여 simulated-device\src\main\java\com\mycompany\app\App.java 파일을 엽니다.

1. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. `{youriothubname}`을 IoT 허브 이름으로 바꾸고 `{yourdevicekey}`를 *장치 ID 만들기* 섹션에서 만든 장치 키 값으로 바꿉니다.

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    이 샘플 앱은 **DeviceClient** 개체를 인스턴스화할 때 **프로토콜** 변수를 사용합니다. 현재, 직접 메서드를 사용하려면 MQTT 프로토콜을 사용해야 합니다.

1. 상태 코드를 IoT Hub로 반환하려면 중첩된 다음 클래스를 **App** 클래스에 추가합니다.

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. 솔루션 백 엔드의 직접 메서드 호출을 처리하려면 중첩된 다음 클래스를 **App** 클래스에 추가합니다.

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "writeLine" :
          {
            int status = METHOD_SUCCESS;
            System.out.println(new String((byte[])methodData));
            deviceMethodData = new DeviceMethodData(status, "Executed direct method " + methodName);
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. **DeviceClient**를 만들고 직접 메서드 호출을 수신 대기하려면 **main** 메서드를 **App** 클래스에 추가합니다.

    ```java
    public static void main(String[] args)
      throws IOException, URISyntaxException
    {
      System.out.println("Starting device sample...");

      DeviceClient client = new DeviceClient(connString, protocol);

      try
      {
        client.open();
        client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
        System.out.println("Subscribed to direct methods. Waiting...");
      }
      catch (Exception e)
      {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
        client.close();
        System.out.println("Shutting down...");
      }

      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
      scanner.close();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. simulated-device\src\main\java\com\mycompany\app\App.java 파일을 저장한 후 닫습니다.

1. **simulated-device** 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 simulated-device 폴더로 이동한 후 다음 명령을 실행합니다.

    `mvn clean package -DskipTests`

## <a name="call-a-direct-method-on-a-device"></a>장치에서 직접 메서드 호출

이 섹션에서는 직접 메서드를 호출하고 응답을 표시하는 Java 콘솔 앱을 만듭니다. 이 콘솔 앱은 IoT Hub에 연결하여 직접 메서드를 호출합니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 iot-java-direct-method 폴더에 **invoke-direct-method**라는 새 Maven 프로젝트를 만듭니다. 다음 명령은 긴 단일 명령입니다.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=invoke-direct-method -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 명령 프롬프트에서 invoke-direct-method 폴더로 이동합니다.

1. 텍스트 편집기를 사용하여 invoke-direct-method 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT Hub와 통신하도록 앱에서 iot-service-client 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 검색][lnk-maven-service-search]을 사용하여 **iot-service-client**의 최신 버전을 확인할 수 있습니다.

1. **종속성** 노드 뒤에 다음 **빌드** 노드를 추가합니다. 이 구성에서는 Maven에 Java 1.8을 사용하여 앱을 빌드하도록 지시합니다.

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. pom.xml 파일을 저장하고 닫습니다.

1. 텍스트 편집기를 사용하여 invoke-direct-method\src\main\java\com\mycompany\app\App.java 파일을 엽니다.

1. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. `{youriothubconnectionstring}`은 *IoT Hub 만들기* 섹션에서 기록한 IoT Hub 연결 문자열로 바꿉니다.

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String methodName = "writeLine";
    public static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    public static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    public static final String payload = "a line to be written";
    ```

1. 시뮬레이트된 장치에서 메서드를 호출하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
        System.out.println("Invoke direct method");
        MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

        if(result == null)
        {
            throw new IOException("Direct method invoke returns null");
        }
        System.out.println("Status=" + result.getStatus());
        System.out.println("Payload=" + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }

    System.out.println("Shutting down sample...");
    ```

1. invoke-direct-method\src\main\java\com\mycompany\app\App.java 파일을 저장하고 닫습니다.

1. **invoke-direct-method** 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 invoke-direct-method 폴더로 이동한 후 다음 명령을 실행합니다.

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>앱 실행

이제 콘솔 앱을 실행할 준비가 되었습니다.

1. simulated-device 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub의 메서드 호출에 대한 대기를 시작합니다.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![직접 메서드 호출을 수신 대기할 Java IoT Hub 시뮬레이트된 장치 앱][8]

1. invoke-direct-method 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub의 시뮬레이트된 장치에 대해 메서드를 호출합니다.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![직접 메서드 호출을 수행할 Java IoT Hub 서비스 앱][7]

1. 시뮬레이트된 장치는 직접 메서드 호출에 응답합니다.

    ![Java IoT Hub 시뮬레이트된 장치 앱은 직접 메서드 호출에 응답합니다.][9]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 장치 ID를 만들었습니다. 시뮬레이션된 장치 앱이 클라우드에서 호출한 메서드에 반응할 수 있도록 장치 ID를 사용했습니다. 장치에서 메서드를 호출하고 장치의 응답을 표시하는 앱도 만들었습니다.

다른 IoT 시나리오를 탐색하려면 [여러 장치에서 작업 예약][lnk-devguide-jobs]을 참조하세요.

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

<!-- Images. -->
[7]: ./media/iot-hub-java-java-direct-methods/invoke-method.png
[8]: ./media/iot-hub-java-java-direct-methods/device-listen.png
[9]: ./media/iot-hub-java-java-direct-methods/device-respond.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22

