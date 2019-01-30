---
title: 자습서 - 사용자 지정 Java 모듈 만들기 - Azure IoT Edge | Microsoft Docs
description: 이 자습서에서는 Java 코드를 사용하여 IoT Edge 모듈을 만들고, 에지 디바이스에 배포하는 방법을 보여 줍니다.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 1b2692df51afb50822ec542fbda423f598bcb8e4
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054744"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>자습서: Java IoT Edge 모듈 개발 및 시뮬레이션된 디바이스에 배포

비즈니스 논리를 직접 Azure IoT Edge 디바이스에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 센서 데이터를 필터링하는 IoT Edge 모듈을 만들고 배포하는 과정을 안내합니다. 여기서는 [Windows](quickstart.md) 또는 [Linux](quickstart-linux.md) 빠른 시작의 '시뮬레이션된 디바이스에 Azure IoT Edge 배포'에서 만든 시뮬레이션된 IoT Edge 디바이스를 사용합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.    

> [!div class="checklist"]
> * Visual Studio Code를 사용하여 Azure IoT Edge maven 템플릿 패키지와 Azure IoT Java 디바이스 SDK를 기반으로 하는 IoT Edge Java 모듈을 만듭니다.
> * Visual Studio Code 및 Docker를 사용하여 docker 이미지를 만들어 레지스트리에 게시합니다.
> * 모듈을 IoT Edge 디바이스에 배포합니다.
> * 생성된 데이터를 봅니다.


이 자습서에서 만드는 IoT Edge 모듈은 디바이스에서 생성한 온도 데이터를 필터링합니다. 온도가 지정된 임계값을 초과하는 경우에만 메시지 업스트림을 전송합니다. Edge에서 이 유형의 분석은 클라우드로 전송되고 저장되는 데이터 양을 줄이는 데 유용합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 디바이스:

* [Linux](quickstart-linux.md) 또는 [Windows](quickstart.md)에 대한 빠른 시작의 단계를 수행하여 IoT Edge 디바이스를 설정할 수 있습니다.
* Windows 디바이스에서 IoT Edge의 경우 버전 1.0.5가 Java 모듈을 지원하지 않습니다. 자세한 내용은 [1.0.5 릴리스 정보](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5)를 참조하세요. 특정 버전을 설치하는 방법에 대한 단계는 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.

클라우드 리소스:

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md). 

개발 리소스:

* [Visual Studio Code](https://code.visualstudio.com/) 
* Visual Studio Code용 [Java 확장 팩](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
* Visual Studio Code용 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Java SE Development Kit 10](https://aka.ms/azure-jdks)(JDK 설치를 가리키도록 [`JAVA_HOME` 환경 변수](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) 설정)
* [Maven](https://maven.apache.org/)
* [Docker CE](https://docs.docker.com/install/)
   * Windows 디바이스에서 개발하는 경우 Docker가 [Linux 컨테이너를 사용하도록 구성](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)되어 있는지 확인합니다. 


## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

이 자습서에서는 Visual Studio Code용 Azure IoT Tools를 사용하여 모듈을 빌드하고 파일에서 **컨테이너 이미지**를 만듭니다. 그런 후 이미지를 저장하고 관리하는 **레지스트리**에 이 이미지를 푸시합니다. 마지막으로 IoT Edge 장치에서 실행되도록 레지스트리의 이미지를 배포합니다.  

임의 Docker 호환 레지스트리를 사용하여 컨테이너 이미지를 유지할 수 있습니다. 두 개의 인기 있는 Docker 레지스트리 서비스는 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 및 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)입니다. 이 자습서에서는 Azure Container Registry를 사용합니다. 

컨테이너 레지스트리가 아직 없는 경우 다음 단계를 따라 Azure에서 새로 만드세요.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **컨테이너** > **Container Registry**를 선택합니다.

2. 다음 값을 입력하여 컨테이너 레지스트리를 만듭니다.

   | 필드 | 값 | 
   | ----- | ----- |
   | 레지스트리 이름 | 고유한 이름을 입력합니다. |
   | 구독 | 드롭다운 목록에서 구독을 선택합니다. |
   | 리소스 그룹 | 더 간편한 관리를 위해 IoT Edge 빠른 시작 및 자습서에서 만드는 모든 테스트 리소스에 동일한 리소스 그룹을 사용합니다. 예를 들어 **IoTEdgeResources**를 사용합니다. |
   | 위치 | 가까운 위치를 선택합니다. |
   | 관리 사용자 | **사용**으로 설정합니다. |
   | SKU | **기본**을 선택합니다. | 

5. **만들기**를 선택합니다.

6. 컨테이너 레지스트리를 만든 후에는 해당 레지스트리를 찾은 다음, **액세스 키**를 선택합니다. 

7. **로그인 서버**, **사용자 이름** 및 **암호**의 값을 복사합니다. 나중에 자습서의 뒷부분에서 이러한 값을 사용하여 컨테이너 레지스트리에 대한 액세스를 제공합니다. 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge 모듈 프로젝트 만들기
다음 단계에서는 Azure IoT Edge Maven 템플릿 패키지 및 Azure IoT Java 디바이스 SDK를 기반으로 하는 IoT Edge 모듈 프로젝트를 만듭니다. Visual Studio Code 및 Azure IoT Tools를 사용하여 프로젝트를 만듭니다.

### <a name="create-a-new-solution"></a>새 솔루션 만들기

사용자 고유의 코드를 사용하여 사용자 지정할 수 있는 Java 솔루션 템플릿을 만듭니다. 

1. Visual Studio Code에서 **보기** > **명령 팔레트**를 차례로 선택하여 VS Code 명령 팔레트를 엽니다. 

2. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 명령 팔레트의 프롬프트에 따라 솔루션을 만듭니다.

   | 필드 | 값 |
   | ----- | ----- |
   | 폴더 선택 | VS Code에 대한 개발 머신에서 위치를 선택하여 솔루션 파일을 만듭니다. |
   | 솔루션 이름 제공 | 솔루션에 대한 설명이 포함된 이름을 입력하거나 기본값 **EdgeSolution**을 적용합니다. |
   | 모듈 템플릿 선택 | **Java 모듈**을 선택합니다. |
   | groupId에 값 지정 | 그룹 ID 값을 입력하거나 기본값인 **com.edgemodule**을 그대로 적용합니다. |
   | 모듈 이름 제공 | 모듈 이름을 **JavaModule**로 지정합니다. |
   | 모듈의 Docker 이미지 리포지토리 제공 | 이미지 리포지토리는 컨테이너 레지스트리의 이름 및 컨테이너 이미지의 이름을 포함합니다. 컨테이너 이미지는 마지막 단계에서 미리 채워져 있습니다. **localhost:5000**을 Azure 컨테이너 레지스트리의 로그인 서버 값으로 바꿉니다. Azure Portal에서 컨테이너 레지스트리의 개요 페이지에서 로그인 서버를 검색할 수 있습니다. 마지막 문자열은 \<레지스트리 이름\>.azurecr.io/javamodule과 같습니다. |
 
   ![Docker 이미지 리포지토리 제공](./media/tutorial-java-module/repository.png)
   
Java 모듈을 처음 만드는 경우 maven 패키지를 다운로드하는 데 몇 분이 걸릴 수 있습니다. 그런 다음, VS Code 창에서 IoT Edge 솔루션 작업 영역을 로드합니다. 솔루션 작업 영역에는 최상위 구성 요소 5개가 포함됩니다. **modules** 폴더에는 모듈에 대한 Java 코드와 모듈을 컨테이너 이미지로 빌드하기 위한 Docker 파일이 포함되어 있습니다. **\.env** 파일은 컨테이너 레지스트리 자격 증명을 저장합니다. **deployment.template.json** 파일에는 IoT Edge 런타임에서 디바이스에 모듈을 배포하는 데 사용하는 정보가 포함되어 있습니다. 그리고 **deployment.debug.template.json** 파일에는 모듈의 디버그 버전이 포함되어 있습니다. 이 자습서에서는 **\.vscode** 폴더 또는 **\.gitignore** 파일을 편집하지 않습니다. 

솔루션을 만들 때 컨테이너 레지스트리를 지정하지 않았지만 기본값인 localhost:5000을 수락한 경우에는 \.env 파일이 없습니다. 

### <a name="add-your-registry-credentials"></a>레지스트리 자격 증명 추가

환경 파일은 컨테이너 레지스트리의 자격 증명을 저장하고 IoT Edge 런타임과 공유합니다. 이러한 자격 증명은 런타임에서 개인 이미지를 IoT Edge 디바이스로 가져오기 위해 필요합니다. 

1. VS Code 탐색기에서 .env 파일을 엽니다. 
2. 필드를 Azure 컨테이너 레지스트리에서 복사한 **사용자 이름** 및 **암호** 값으로 업데이트합니다. 
3. 이 파일을 저장합니다. 

### <a name="update-the-module-with-custom-code"></a>사용자 지정 코드를 사용하여 모듈 업데이트

1. VS Code 탐색기에서 **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**를 차례로 엽니다.

5. 파일의 맨 위에 다음 코드를 추가하여 참조된 새 클래스를 가져옵니다.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

5. 다음 정의를 **App** 클래스에 추가합니다. 이 변수는 온도 임계값을 설정합니다. 측정된 머신 온도가 이 값을 초과하기 전까지는 IoT Hub에 보고되지 않습니다. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. **MessageCallbackMqtt**의 execute 메서드를 다음 코드로 바꿉니다. 이 메서드는 모듈에서 IoT Edge 허브의 MQTT 메시지를 받을 때마다 호출됩니다. 모듈 쌍을 통해 설정된 온도 임계값 아래의 온도를 보고하는 메시지를 필터링합니다.

    ```java
        private int counter = 0;
       @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    ```

8. 다음 두 정적 내부 클래스를 **App** 클래스에 추가합니다. 이러한 클래스는 모듈 쌍의 원하는 속성이 변경될 때 tempThreshold 변수를 업데이트합니다. 모든 모듈에는 자체 모듈 쌍이 있어서 클라우드에서 직접 모듈 내에서 실행되는 코드를 구성할 수 있습니다.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

9. 모듈 쌍 업데이트를 등록하려면 **client.open()** 뒤의 **main** 메서드에 다음 줄을 추가합니다.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

11. App.java 파일을 저장합니다.

12. VS Code 탐색기에서 IoT Edge 솔루션 작업 영역에 있는 **deployment.template.json** 파일을 엽니다. 이 파일은 배포할 모듈(이 경우 **tempSensor** 및 **JavaModule**)을 IoT Edge 에이전트에 알려주고, 메시지를 라우팅하는 방법을 IoT Edge 허브에 알려줍니다. Visual Studio Code 확장은 배포 템플릿에 필요한 대부분의 정보를 자동으로 채우지만 솔루션에 대한 모든 정보가 정확한지 확인합니다. 

   1. VS Code 상태 표시줄에서 IoT Edge의 기본 플랫폼은 **amd64**로 설정되므로 **JavaModule**은 Linux amd64 버전의 이미지로 설정됩니다. IoT Edge 디바이스의 아키텍처가 이와 다를 경우 상태 표시줄의 기본 플랫폼을 **amd64**에서 **arm32v7** 또는 **windows-amd64**로 변경하세요. 

      ![모듈 이미지 플랫폼 업데이트](./media/tutorial-java-module/image-platform.png)

   2. IoT Edge 솔루션을 만들 때 변경한 기본 **SampleModule** 이름이 아닌 올바른 모듈 이름이 템플릿에 있는지 확인합니다.

   3. **registryCredentials** 섹션은 IoT Edge 에이전트에서 모듈 이미지를 끌어올 수 있도록 Docker 레지스트리 자격 증명을 저장합니다. 실제 사용자 이름 및 암호 쌍은 Git에서 무시하는 .env 파일에 저장됩니다. 아직 없는 경우 .env 파일에 자격 증명을 추가합니다.  

   4. 배포 매니페스트에 대한 자세한 내용은 [IoT Edge에서 모듈을 배포하고 경로를 설정하는 방법 알아보기](module-composition.md)를 참조하세요.

13. **JavaModule** 모듈 쌍을 배포 매니페스트에 추가합니다. **$edgeHub** 모듈 쌍 뒤에 있는 **moduleContent** 섹션의 아래쪽에 다음 JSON 내용을 삽입합니다. 

   ```json
       "JavaModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![배포 템플릿에 모듈 쌍 추가](./media/tutorial-java-module/module-twin.png)

14. deployment.template.json 파일을 저장합니다.

## <a name="build-your-iot-edge-solution"></a>IoT Edge 솔루션 빌드

이전 섹션에서 IoT Edge 솔루션을 만들고, **JavaModule**에 코드를 추가하여 보고된 머신 온도가 허용 가능한 임계값 미만인 메시지를 필터링했습니다. 이제 솔루션을 컨테이너 이미지로 빌드하고 컨테이너 레지스트리로 푸시합니다. 

1. Visual Studio Code 터미널에 다음 명령을 입력하여 Docker에 로그인합니다. 그런 다음, Azure 컨테이너 레지스트리에 모듈 이미지를 푸시할 수 있습니다.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   첫 번째 섹션에서 복사한 Azure 컨테이너 레지스트리의 사용자 이름, 암호 및 로그인 서버를 사용합니다. 또는 Azure Portal에서 레지스트리의 **액세스 키** 섹션에서 이러한 값을 다시 검색할 수 있습니다.

2. VS Code 탐색기에서 deployment.template.json 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드 및 푸시**를 선택합니다. 

솔루션을 빌드하도록 Visual Studio Code에 지시하면 먼저 배포 템플릿의 정보를 가져와서 **config**라는 새 폴더에 deployment.json 파일을 생성합니다. 그런 다음, 통합 터미널에서 `docker build` 및 `docker push`, 두 개의 명령을 실행합니다. 이 두 명령은 코드를 빌드하고,, Java 앱을 컨테이너화한 다음, 솔루션을 초기화할 때 지정한 컨테이너 레지스트리로 코드를 푸시합니다. 

VS Code 통합 터미널에 태그와 함께 전체 컨테이너 이미지 주소를 볼 수 있습니다. 이미지 주소는 \<리포지토리\>:\<버전\>-\<플랫폼\> 형식으로 module.json 파일에 있는 정보에서 빌드됩니다. 이 자습서에서 주소는 registryname.azurecr.io/javamodule:0.0.1-amd64와 같습니다.

## <a name="deploy-and-run-the-solution"></a>솔루션 배포 및 실행

IoT Edge 디바이스를 설정할 때 사용한 빠른 시작 문서에서는 Azure Portal을 사용하여 모듈을 배포했습니다. Visual Studio Code용 Azure IoT Hub Toolkit 확장(이전의 Azure IoT Toolkit 확장)을 사용하여 모듈을 배포할 수도 있습니다. 여러분의 시나리오를 위한 배포 매니페스트인 **deployment.json** 파일이 이미 준비되어 있습니다. 이제 배포를 받을 디바이스를 선택하기만 하면 됩니다.

1. VS Code 명령 팔레트에서 **Azure: 로그인** 명령을 입력하고 실행한 다음, 지침에 따라 Azure 계정에 로그인합니다. 이미 로그인한 경우 이 단계를 건너뛸 수 있습니다.

2. VS Code 명령 팔레트에서 **Azure IoT Hub: IoT Hub 선택**을 실행합니다. 

3. 구성하려는 IoT Edge 디바이스가 포함된 구독 및 IoT Hub를 선택합니다. 

4. VS Code 탐색기에서 **Azure IoT Hub 디바이스** 섹션을 펼칩니다. 

5. IoT Edge 디바이스의 이름을 마우스 오른쪽 단추로 클릭한 다음, **단일 디바이스용 배포 만들기**를 선택합니다. 

   ![단일 디바이스용 배포 만들기](./media/tutorial-java-module/create-deployment.png)

6. **config** 폴더에서 **deployment.json** 파일을 선택한 다음, **에지 배포 매니페스트 선택**을 클릭합니다. deployment.template.json 파일을 사용하지 마세요. 

7. 새로고침 단추를 클릭합니다. **TempSensor** 모듈과 **$edgeAgent** 및 **$edgeHub**가 함께 실행되는 새 **JavaModule**이 표시됩니다.  

## <a name="view-generated-data"></a>생성된 데이터 보기

IoT Edge 디바이스에 배포 매니페스트를 적용한 후에는 디바이스의 IoT Edge 런타임이 새 배포 정보를 수집하고 그에 따라 실행하기 시작합니다. 배포 매니페스트에 포함되지 않는 디바이스에서 실행되는 모든 모듈은 중지됩니다. 디바이스에서 누락된 모든 모듈이 시작됩니다. 

Visual Studio Code Explorer의 **Azure IoT Hub 디바이스** 섹션을 통해 IoT Edge 디바이스 상태를 확인할 수 있습니다. 배포되어 실행 중인 모듈의 목록을 보려면 디바이스 상세 정보를 확장합니다. 

IoT Edge 디바이스에서 `iotedge list` 명령을 사용하여 배포 모듈 상태를 확인할 수 있습니다. 두 IoT Edge 런타임 모듈과 tempSensor, 이 자습서에서 만든 사용자 지정 모듈 등, 4개 모듈이 표시됩니다. 모든 모듈이 시작되려면 몇 분 정도 걸릴 수 있으므로 처음에 일부가 표시되지 않는다면 명령을 다시 실행합니다. 

모든 모듈에서 생성되는 메시지를 보려면 `iotedge logs <module name>` 명령을 사용합니다. 

메시지가 IoT 허브에 도착하면 Visual Studio Code를 사용하여 메시지를 볼 수 있습니다. 

1. IoT 허브에 도착하는 데이터를 모니터링하려면 줄임표(**...**)를 선택한 다음, **D2C 메시지 모니터링 시작**을 선택합니다.
2. 특정 디바이스에 대한 D2C 메시지를 모니터링하려면 목록에서 해당 디바이스를 마우스 오른쪽 단추로 클릭하고 **D2C 메시지 모니터링 시작**을 선택합니다.
3. 데이터 모니터링을 중지하려면 명령 팔레트에서 **Azure IoT Hub: D2C 메시지 모니터링 중지** 명령을 실행합니다. 
4. 모듈 쌍을 보거나 업데이트하려면 목록에서 해당 모듈을 마우스 오른쪽 단추로 클릭하고 **모듈 쌍 편집**을 선택합니다. 모듈 쌍을 업데이트하려면 쌍 JSON 파일을 저장하고, 편집기 영역을 마우스 오른쪽 단추로 클릭하고, **모듈 쌍** 업데이트를 선택합니다.
5. Docker 로그를 보려면 VS Code에 대한 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)를 설치합니다. Docker 탐색기에서 로컬로 실행 중인 모듈을 찾을 수 있습니다. 통합 터미널에서 보려면 상황에 맞는 메뉴에서 **로그 표시**를 선택합니다.
 
## <a name="clean-up-resources"></a>리소스 정리 

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 장치와 동일한 IoT Edge 장치를 계속 사용해도 됩니다. 

그렇지 않은 경우 요금 청구를 방지하도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 디바이스에서 생성한 원시 데이터를 필터링하는 코드가 포함된 IoT Edge 모듈을 만들었습니다. Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다음 자습서를 진행하면 됩니다.

> [!div class="nextstepaction"]
> [SQL Server 데이터베이스로 에지에 데이터 저장](tutorial-store-data-sql-server.md)

