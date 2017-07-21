---
title: "Java를 사용하여 Azure IoT Edge 모듈 만들기 | Microsoft Docs"
description: "이 자습서에서는 최신 Azure IoT Edge Maven 패키지를 사용하여 BLE 데이터 변환기 모듈을 작성하는 방법을 소개합니다."
services: iot-hub
author: junyi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 06/28/2017
ms.author: junyi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 682feb4d889ecd881abe1a70d36e0a5a4df3d910
ms.contentlocale: ko-kr
ms.lasthandoff: 07/04/2017

---
# <a name="create-an-azure-iot-edge-module-with-java"></a>Java를 사용하여 Azure IoT Edge 모듈 만들기

이 자습서는 Java에서 Azure IoT Edge에 대한 모듈을 빌드하는 방법을 소개합니다.

이 자습서에서는 최신 Azure IoT Edge Maven 패키지를 사용하여 환경 설정 및 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 데이터 변환기 모듈을 작성하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 섹션에서는 IoT Edge 모듈 개발을 위한 환경을 설정하게 됩니다. *64비트 Windows* 및 *64비트 Linux(Ubuntu/Debian 8)* 운영 체제 모두에 적용됩니다.

다음과 같은 소프트웨어가 필요합니다.

* [Git 클라이언트](https://git-scm.com/downloads).
* [**x64** JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* [Maven](https://maven.apache.org/install.html)

명령줄 터미널 창을 열고 다음 리포지토리를 복제합니다.

1. `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
2. `cd iot-edge-samples/java/simulated_ble`

## <a name="overall-architecture"></a>전체 아키텍처

Azure IoT Edge 플랫폼은 [Von Neumann 아키텍처](https://en.wikipedia.org/wiki/Von_Neumann_architecture)를 중요하게 채택합니다. 전체 Azure IoT Edge 아키텍처는 입력을 처리하고 출력을 생성하는 시스템이며 각 개별 모듈은 아주 작은 입력-출력 하위 시스템이기도 합니다. 이 자습서에서는 다음 두 개의 모듈을 소개합니다.

1. 시뮬레이션된 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 신호를 수신하여 형식이 지정된 [JSON](https://en.wikipedia.org/wiki/JSON) 메시지로 변환하는 모듈입니다.
2. 수신한 [JSON](https://en.wikipedia.org/wiki/JSON) 메시지를 인쇄하는 모듈입니다.

다음 이미지는 이 프로젝트에 대한 일반적인 종단 간 데이터 흐름을 표시합니다.

![3개의 모듈 간의 데이터 흐름](media/iot-hub-iot-edge-create-module/dataflow.png "입력: 시뮬레이션된 BLE 모듈, 프로세서: 변환기 모듈, 출력: 프린터 모듈")

## <a name="understanding-the-code"></a>코드 이해

### <a name="maven-project-structure"></a>Maven 프로젝트 구조

Azure IoT Edge 패키지는 Maven을 기반으로 하므로 `pom.xml` 파일을 포함하는 일반적인 Maven 프로젝트 구조를 만들어야 합니다.

POM은 `com.microsoft.azure.gateway.gateway-module-base` 패키지에서 상속합니다. 이는 런타임 이진 파일, 게이트웨이 구성 파일 경로 및 실행 동작을 포함하는 모듈 프로젝트에서 필요한 모든 종속성을 선언합니다. 이를 통해 많은 시간을 절약할 수 있고, 수백 개의 코드 줄을 반복하여 쓰고 다시 쓸 필요가 없어집니다.

다음 코드 조각에 나와 있는 것처럼 모듈에서 사용할 구성 파일의 필수 종속성/플러그 인 및 이름을 선언하여 pom.xml 파일을 업데이트해야 합니다.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- Inherit from parent -->
  <parent>
    <groupId>com.microsoft.azure.gateway</groupId>
    <artifactId>gateway-module-base</artifactId>
    <version>1.0.1</version>
  </parent>
  
  <groupId>com.microsoft.azure.gateway</groupId>
  <artifactId>ble-converter</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <!-- Set the filename of the Azure IoT Edge configuration located
       under ./src/main/resources/gateway/ which is used in parent -->
  <properties>
    <gw.config.fileName>gw-config.json</gw.config.fileName>
  </properties>

  <!-- Re-declare dependencies used in parent -->
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.gateway</groupId>
      <artifactId>gateway-java-binding</artifactId>
    </dependency>
    <dependency>
      <groupId>${dependency.runtime.group}</groupId>
      <artifactId>${dependency.runtime.name}</artifactId>
    </dependency>
  </dependencies>

  <!-- Re-declare plugins used in parent -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### <a name="basic-understanding-of-an-azure-iot-edge-module"></a>Azure IoT Edge 모듈에 대한 기본적인 이해

Azure IoT Edge 모듈을 입력을 수신하고, 처리하며, 출력을 생성하는 작업을 수행하는 데이터 프로세서로 간주할 수 있습니다.

입력은 하드웨어(예: 동작 탐지기)의 데이터, 다른 모듈의 메시지 또는 그밖에 무엇(예: 타이머에 의해 정기적으로 생성된 난수)이 될 수 있습니다.

출력은 입력과 유사하게, 하드웨어 동작(예: 깜박이는 LED), 다른 모듈로 보내는 메시지 또는 그밖에 무엇(예: 콘솔에 인쇄)을 트리거할 수 있습니다.

모듈은 `com.microsoft.azure.gateway.messaging.Message` 클래스를 사용하여 서로 통신합니다. `Message`의 **콘텐츠**는 사용자가 선호하는 모든 종류의 데이터를 표시할 수 있는 바이트 배열입니다. **속성**은 `Message`에서 사용할 수 있으며 단순히 문자열-문자열 매핑입니다. **속성**을 HTTP 요청의 헤더 또는 파일의 메타데이터로 생각할 수도 있습니다.

Java에서 Azure IoT Edge 모듈을 개발하려면 `com.microsoft.azure.gateway.core.GatewayModule`에서 상속하는 새 모듈 클래스를 만들고, 필수 추상 메서드 `receive()` 및 `destroy()`를 구현해야 합니다. 이 시점에서 선택적 `start()` 또는 `create()` 메서드를 구현하도록 선택할 수도 있습니다. 다음 코드 조각은 Azure IoT Edge 모듈을 작성하는 방법을 보여 줍니다.

```java
import com.microsoft.azure.gateway.core.Broker;
import com.microsoft.azure.gateway.core.GatewayModule;
import com.microsoft.azure.gateway.messaging.Message;

public class MyEdgeModule extends GatewayModule {
  public MyEdgeModule(long address, Broker broker, String configuration) {
    /* Let the GatewayModule do the dirty work of initialization. It's also
       a good time to parse your own configuration defined in Azure IoT Edge
       configuration file (typically ./src/main/resources/gateway/gw-config.json) */
    super(address, broker, configuration);
  }

  @Override
  public void start() {
    /* Acquire the resources you need. If you don't
       need any resources, you may omit this method. */
  }

  @Override
  public void destroy() {
    /* It's time to release all resources. This method is required. */
  }

  @Override
  public void receive(Message message) {
    /* Logic to process the input message. This method is required. */
    // ...
    /* Use publish() method to do the output. You are
       allowed to publish your new Message instance. */
    this.publish(message);
  }
}
```

### <a name="converter-module"></a>변환기 모듈

| 입력                    | 프로세서                              | 출력                 | 원본 파일            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| 온도 데이터 메시지 | 새 JSON 메시지의 구문 분석 및 생성 | JSON 메시지 구조 | `ConverterModule.java` |

이 모듈은 일반적인 Azure IoT Edge 모듈입니다. 다른 모듈에서 온도 메시지를 수락하고(하드웨어 모듈 또는 이 경우 시뮬레이션된 BLE 모듈), 구조적 JSON 메시지로 온도 메시지를 정규화합니다(메시지 ID 첨부, 온도 경고를 트리거해야 하는지 여부의 속성 설정 등 포함).

```java
@Override
public void receive(Message message) {
  try {
    JSONObject messageFromBle = new JSONObject(new String(message.getContent()));
    double temperature = messageFromBle.getDouble("temperature");
    Map<String, String> inputProperties = message.getProperties();

    HashMap<String, String> properties = new HashMap<>();
    properties.put("source", inputProperties.get("source"));
    properties.put("macAddress", inputProperties.get("macAddress"));
    properties.put("temperatureAlert", temperature > 30 ? "true" : "false");

    String content = String.format(
        "{ \"deviceId\": \"Intel NUC Gateway\", \"messageId\": %d, \"temperature\": %f }",
        ++this.messageCount, temperature);

    this.publish(new Message(content.getBytes(), properties));
  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
```

### <a name="printer-module"></a>프린터 모듈

| 입력                          | 프로세서 | 출력                     | 원본 파일          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| 다른 모듈의 모든 메시지 | 해당 없음       | 콘솔에 메시지 로그 | `PrinterModule.java` |

이는 간단하고 설명이 따로 필요 없으며, 수신한 메시지를 터미널 창으로 출력하는 모듈입니다.

```java
@Override
public void receive(Message message) {
  System.out.println(message.toString());
}
```

### <a name="azure-iot-edge-configuration"></a>Azure IoT Edge 구성

모듈을 실행하기 전 마지막 단계는 Azure IoT Edge를 구성하고 모듈 간 연결을 설정하는 것입니다.

첫 번째로, 나중에 섹션에서 자체 `name`에 의해 참조될 수 있는 Java 로더를 선언해야 합니다(Azure IoT Edge가 다른 언어의 로더를 지원하기 때문).

```json
"loaders": [{
  "type": "java",
  "name": "java",
  "configuration": {
    "jvm.options": {
      "library.path": "./"
    }
  }
}]
```

로더를 선언하고 나면, 모듈도 선언해야 합니다. 로더 선언과 마찬가지로 모듈도 `name` 특성에 의해 참조될 수 있습니다. 모듈을 선언할 때 각 모듈에 대해 사용해야 하는 로더(앞서 정의한 것 중 하나여야 함) 및 진입점(모듈의 정규화된 클래스 이름이어야 함)을 지정해야 합니다. `simulated_device` 모듈은 Azure IoT Edge 코어 런타임 패키지에 포함되는 네이티브 모듈입니다. `args`를 JSON 파일에 `null`인 경우라도 포함해야 합니다.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/ConverterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  },
  {
    "name": "print",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/PrinterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  }
]
```

구성 마지막에 연결을 설정합니다. 각 연결은 `source` 및 `sink`로 표현됩니다. 미리 정의된 모듈을 모두 참조해야 합니다. `source` 모듈의 출력 메시지는 `sink` 모듈의 입력으로 전달됩니다.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "print"
  }
]
```

## <a name="running-the-modules"></a>모듈 실행

`mvn package`를 사용하여 모든 항목을 `target/` 폴더에 작성합니다. `mvn clean package`도 클린 빌드에 좋습니다.

`mvn exec:exec`를 사용하여 Azure IoT Edge를 실행하고, 온도 데이터와 모든 속성이 고정된 속도로 콘솔에 인쇄되는 것을 확인해야 합니다.

응용 프로그램을 종료하려면 `<Enter>` 키를 누릅니다.

> [!IMPORTANT]
> Ctrl + C를 사용하여 IoT Edge 게이트웨이 응용 프로그램을 종료하는 것은 권장되지 않습니다. 이는 프로세스를 비정상적으로 종료할 수 있기 때문입니다.


