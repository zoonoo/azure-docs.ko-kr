---
title: "Azure IoT Hub 장치 쌍 속성(Java) 사용 | Microsoft Docs"
description: "Azure IoT Hub 장치 쌍을 사용하여 장치를 구성하는 방법입니다. Java용 Azure IoT 장치 SDK를 사용하여 시뮬레이션된 장치 앱을 구현하고 Java용 Azure IoT 서비스 SDK를 사용하여 장치 쌍을 통해 장치 구성을 수정하는 서비스 앱을 구현합니다."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: dobett
ms.openlocfilehash: 3881e3791c97baf2922722f01f9e6e6ea55ed2e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>desired 속성을 사용하여 장치 구성

[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

이 자습서를 마치면 다음 두 가지 Java 콘솔 앱이 만들어집니다.

* **simulated-device** - 원하는 구성 업데이트를 기다리고 시뮬레이션된 구성 업데이트 프로세스의 상태를 보고하는 시뮬레이션된 장치 앱입니다.
* **set-configuration** - 장치에 원하는 구성을 설정하고 구성 업데이트 프로세스를 쿼리하는 백 엔드 앱입니다.

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 문서는 장치 및 백 엔드 앱을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 대한 정보를 제공합니다.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* 최신 [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[장치 쌍 시작][lnk-twin-tutorial] 자습서를 수행했으면 이미 IoT Hub 및 **myDeviceId**라는 장치 ID가 있습니다. 이 경우 [시뮬레이션된 장치 앱 만들기][lnk-how-to-configure-createapp] 섹션으로 건너뛸 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 **myDeviceId**로 허브에 연결하는 Java 콘솔 앱을 만들고, 원하는 구성 업데이트를 기다린 다음, 시뮬레이션된 구성 업데이트 프로세스의 업데이트를 보고합니다.

1. dt-get-started라는 빈 폴더를 만듭니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 dt-get-started 폴더에 **simulated-device**라는 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 명령 프롬프트에서 simulated-device 폴더로 이동합니다.

1. 텍스트 편집기를 사용하여 simulated-device 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT Hub와 통신하도록 앱에서 iot-service-client 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
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

1. 텍스트 편집기를 사용하여 simulated-device\src\main\java\com\mycompany\app\App.java 원본 파일을 엽니다.

1. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. **{yourdeviceconnectionstring}**을 *장치 ID 만들기* 섹션에서 기록해 둔 장치 연결 문자열로 바꿉니다.

    ```java
    private static String deviceId = "myDeviceId";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    private static TelemetryConfig telemetryConfig;
    ```

1. 장치 쌍 상태 이벤트에 대한 콜백 처리기를 구현하려면(디버깅 프로세스를 위해) **App** 클래스에 다음 중첩 클래스를 추가합니다.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback 
    {
        public void execute(IotHubStatusCode status, Object context) 
        {
            //System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. 다음 중첩 클래스를 **App** 클래스에 추가합니다.

    ```java
    private static class TelemetryConfig extends Device 
    {
        private String configId = "0000";
        private String sendFrequency = "45m";

        private Boolean initialRun = true;
        
        private Property telemetryConfig = new Property("telemetryConfig", "{configId=" + configId + ", sendFrequency=" + sendFrequency + "}");

        public void InitTelemetry() throws IOException 
        {
            System.out.println("Report initial telemetry config:");
            System.out.println(this.telemetryConfig);

            this.setReportedProp(this.telemetryConfig);

            client.sendReportedProperties(this.getReportedProp());
        }

        private void UpdateReportedConfiguration() 
        {
            try {
                System.out.println("Initiating config change");
                    
                this.setReportedProp(this.telemetryConfig);
                client.sendReportedProperties(this.getReportedProp());
                    
                System.out.println("Simulating device reset");
                
                Thread.sleep(10000);
                
                System.out.println("Completing config change");
                System.out.println("Config change complete");
            } catch (Exception e) {
                System.out.println("Exception \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
            }
        }

        @Override
        public void PropertyCall(String propertyKey, Object propertyValue, Object context) 
        {
            if (propertyKey.equals("telemetryConfig")) {
                if (!(initialRun)) {
                    System.out.println("Desired property change:");
                    System.out.println(propertyKey + ":" + propertyValue);

                    telemetryConfig.setValue(propertyValue);

                    UpdateReportedConfiguration();
                } else {
                    initialRun = false;
                }
            } 
        }
    }
    ```

    > [!NOTE]
    > TelemetryConfig 클래스는 원하는 속성 콜백에 대한 액세스 권한을 얻기 위해 [Device class][lnk-java-device-class]를 확장합니다.

1. 다음 예외를 throw하도록 **main** 메서드의 서명을 수정합니다.

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. **DeviceClient** 및 **TelemetryConfig**를 인스턴스화하도록 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    client = new DeviceClient(connString, protocol);

    telemetryConfig = new TelemetryConfig();
    ```

1. 장치 쌍 서비스를 시작하려면 다음 코드를 **main** 메서드에 추가합니다.

    ```java
    try 
    {
        System.out.println("Connecting to hub");
        client.open();
        client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, telemetryConfig, null);

        telemetryConfig.InitTelemetry();

        System.out.println("Wait for desired telemetry...");
        client.subscribeToDesiredProperties(telemetryConfig.getDesiredProp());
    }
    catch (Exception e) 
    {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
        telemetryConfig.clean();
        client.close();
        System.out.println("Shutting down...");
    }
    ```

1. 필요한 경우 장치 시뮬레이터를 종료하도록 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    System.out.println("Press enter to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    telemetryConfig.clean();
    client.close();
    ```

1. simulated-device\src\main\java\com\mycompany\app\App.java 파일을 저장한 후 닫습니다.

1. **simulated-device** 백 엔드 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 simulated-device 폴더로 이동한 후 다음 명령을 실행합니다.

    `mvn clean package -DskipTests`

   > [!NOTE]
   > 이 자습서는 동시 구성 업데이트에 대해 어떤 동작도 시뮬레이션하지 않습니다. 어떤 구성 업데이트 프로세스는 업데이트가 실행되는 중에 대상 구성의 변경을 수용할 수 있는 반면에 어떤 프로세스는 대기해야 하거나 오류 조건을 사용해 거부할 수 있습니다. 특정 구성 프로세스에 대해 원하는 동작을 고려하여 구성 변경을 시작하기 전에 적절한 논리를 추가해야 합니다.
   > 
   > 

## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기
이 섹션에서는 새로운 원격 분석 구성 개체를 사용하여 **myDeviceId**와 연결된 장치 쌍에서 *desired 속성*을 업데이트하는 Java 콘솔 앱을 만듭니다. 그런 다음 IoT Hub에 저장된 장치 쌍을 쿼리하고 장치의 desired 구성과 reported 구성 사이의 차이점을 표시합니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 dt-get-started 폴더에 **set-configuration**이라는 Maven 프로젝트를 만듭니다. 긴 단일 명령입니다.

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=set-configuration -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 명령 프롬프트에서 set-configuration 폴더로 이동합니다.

1. 텍스트 편집기를 사용하여 trigger-reboot 폴더에서 pom.xml 파일을 열고 **종속성** 노드에 다음 종속성을 추가합니다. 이러한 종속성을 통해 IoT Hub와 통신하도록 앱에서 iot-service-client 패키지를 사용할 수 있습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
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

1. 텍스트 편집기를 사용하여 set-configuration\src\main\java\com\mycompany\app\App.java 원본 파일을 엽니다.

1. 파일에 다음 **import** 문을 추가합니다.

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. 다음 클래스 수준 변수를 **App** 클래스에 추가합니다. **{youriothubconnectionstring}**을 *IoT Hub 만들기* 섹션에서 기록한 IoT Hub 연결 문자열로 대체합니다.

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";
    ```

1. 시뮬레이트된 장치에서 장치 쌍을 쿼리하고 업데이트하려면 **main** 메서드에 다음 코드를 추가합니다.

    ```java
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);

    String sendFrequency= "12h";

    try {
        twinClient.getTwin(device);

        // make sure a differing value exists before calling the updateTwin() method, else a null device exception will be thrown
        // this allows the program to be run multiple times for demonstration purposes
        String desiredProperties = device.desiredPropertiesToString();
        if (desiredProperties.contains("sendFrequency=" + sendFrequency))
        {
            sendFrequency = "8h";
        }
            
        Set<Pair> tags = new HashSet<Pair>();
        tags.add(new Pair("telemetryConfig", "{configId=0001, sendFrequency=" + sendFrequency + "}"));

        twinClient.getTwin(device);
        device.setDesiredProperties(tags);

        System.out.println("Config report for: " + deviceId);   
        System.out.println(device);

        twinClient.updateTwin(device);

        String reportedProperties = device.reportedPropertiesToString();
        Boolean waitFlag = true;

        while (waitFlag) {
            if (!reportedProperties.contains("sendFrequency=" + sendFrequency)) {
                Thread.sleep(10000);
            }
            else 
            {
                waitFlag = false;
            }

            twinClient.getTwin(device);
            reportedProperties = device.reportedPropertiesToString();
        }
            
        SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "properties.reported.telemetryConfig='{configId=0001, sendFrequency=" + sendFrequency + "}'", null);

        Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
        while (twinClient.hasNextDeviceTwin(twinQuery)) {
            DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
            System.out.println(d.getDeviceId() + " found with changed telemetryConfig");
        }

        System.out.println("Config report for: " + deviceId);
        twinClient.getTwin(device);
        System.out.println(device);

        } catch (IotHubException e) {
            System.out.println(e.getMessage());
        } catch (IOException e) {
            System.out.println(e.getMessage());
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }
    ```

1. set-desired-configuration\src\main\java\com\mycompany\app\App.java 파일을 저장하고 닫습니다.

1. **set-configuration** 백 엔드 앱을 빌드하고 오류를 수정합니다. 명령 프롬프트에서 set-configuration 폴더로 이동한 후 다음 명령을 실행합니다.

    `mvn clean package -DskipTests`
   
    이 코드는 **myDeviceId**에 대한 장치 쌍을 검색한 다음 새로운 원격 분석 구성 개체를 사용하여 desired 속성을 업데이트합니다.
    그런 다음 10초마다 IoT Hub에 저장된 장치 쌍을 쿼리하고, 원하는 보고된 원격 분석 구성을 출력합니다. 모든 장치에서 다양한 보고서를 생성하는 방법을 알아보려면 [IoT Hub 쿼리 언어][lnk-query]를 참조하세요.
   
   > [!IMPORTANT]
   > 이 응용 프로그램은 장치가 업데이트될 때까지 설명 목적으로 10초 마다 IoT Hub를 쿼리합니다. 변경을 감지하기 위해서가 아니라 여러 장치에서 사용자용 보고서를 생성하기 위해 쿼리를 사용합니다. 솔루션에 장치 이벤트의 실시간 알림이 필요한 경우 [쌍 알림][lnk-twin-notifications]을 사용합니다.
   > 

   > [!IMPORTANT]
   > 장치 보고서 작업 및 쿼리 결과 사이에 최대 1분간 지연됩니다. 이는 쿼리 인프라를 매우 높은 규모에서 작업하도록 하기 위해서입니다.  단일 장치 쌍의 일관된 보기를 검색하려면 **DeviceTwin** 클래스의 **getDeviceTwin** 메서드를 사용합니다.
   > 
   > 

## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 준비가 되었습니다.

1. simulated-device 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub의 장치 쌍 호출에 대한 대기를 시작합니다.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. set-configuration 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub의 시뮬레이트된 장치에서 장치 쌍을 쿼리하고 업데이트합니다.

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. 시뮬레이트된 장치는 재부팅 직접 메서드 호출에 응답합니다.

    ![Java IoT Hub 시뮬레이트된 장치 앱은 장치 쌍 호출에 응답합니다.][img-deviceconfigured]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 솔루션 백 엔드에서 원하는 구성을 *desired 속성*으로 설정하고, 해당 변경 사항을 감지하고 reported 속성을 통해 상태를 보고하는 다단계 업데이트 프로세스를 시뮬레이션하는 장치 앱을 작성했습니다.

아래와 같이 실행할 방법을 알아보려면 다음 리소스를 참조하세요.

* [IoT Hub 시작][lnk-iothub-getstarted] 자습서를 참조하여 장치에서 원격 분석을 보냅니다.
* [jobs 예약 및 브로드캐스트][lnk-schedule-jobs] 자습서를 참조하여 대규모 장비 집합에 대한 작업을 예약하거나 수행합니다.
* [직접 메서드 사용][lnk-methods-tutorial] 자습서를 참조하여 대화형으로(예: 사용자 제어 앱에서 팬 작동) 장치를 제어합니다.

<!-- images -->
[img-deviceconfigured]: media/iot-hub-java-java-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-java-device-class]: https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_twin._device

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
