---
title: "Azure IoT Edge를 사용하여 Azure 함수 배포 | Microsoft Docs"
description: "Azure 함수를 모듈로 Edge 장치에 배포"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: fb295b37819788ed14f54e4123ae0fe1b52d0210
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Azure 함수를 IoT Edge 모듈로 배포 - 미리 보기
비즈니스 논리를 직접 IoT Edge 장치에 구현하는 코드를 배포하려면 Azure Functions를 사용할 수 있습니다. 이 자습서에서는 “[Windows][lnk-tutorial1-win] 또는 [Linux][lnk-tutorial1-lin]에서 시뮬레이트된 장치에 Azure IoT Edge 배포” 자습서에서 만든 IoT Edge 장치에서 센서 데이터를 필터링하는 Azure 함수를 만들고 배포하는 과정을 안내합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.     

> [!div class="checklist"]
> * Visual Studio Code를 사용하여 Azure 함수 만들기
> * VS Code 및 Docker를 사용하여 Docker 이미지를 만들어 레지스트리에 게시합니다. 
> * 모듈을 IoT Edge 장치에 배포합니다.
> * 생성된 데이터 보기


이 자습서에서 만든 Azure 함수는 장치에서 생성된 온도 데이터를 필터링하고, 온도가 지정된 임계값보다 높은 경우에만 Azure IoT Hub로 메시지 업스트림을 보냅니다. 

## <a name="prerequisites"></a>필수 조건

* 빠른 시작 또는 이전 자습서에서 만든 Azure IoT Edge 장치
* [Visual Studio Code](https://code.visualstudio.com/) 
* [C# for Visual Studio Code(OmniSharp 제공) 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). (Visual Studio Code의 확장 패널에서 확장을 설치할 수 있음)
* [Visual Studio Code용 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) (Visual Studio Code의 확장 패널에서 확장을 설치할 수 있음)
* [Docker](https://docs.docker.com/engine/installation/). 이 자습서에서는 플랫폼용 CE(Community Edition)로 충분합니다. 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 

## <a name="set-up-a-docker-registry"></a>Docker 레지스트리 설정
이 자습서에서는 VS Code용 Azure IoT Edge 확장을 사용하여 Azure 함수를 만들고 이를 사용하여 [Docker 이미지](https://docs.docker.com/glossary/?term=image)를 빌드합니다. 그런 다음 [Docker 레지스트리](https://docs.docker.com/glossary/?term=registry)에 호스트된 [Docker 리포지토리](https://docs.docker.com/glossary/?term=repository)에 이 Docker 이미지를 푸시합니다. 마지막으로, 레지스트리에서 [Docker 컨테이너](https://docs.docker.com/glossary/?term=container)로 패키지된 Docker 이미지를 IoT Edge 장치에 배포합니다.  

이 자습서에서는 Docker 호환 레지스트리를 사용할 수 있습니다. 클라우드에서 사용 가능한 두 개의 인기 있는 Docker 레지스트리 서비스는 **Azure Container Registry** 및 **Docker Hub**입니다.

- [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/)는 [유료 구독](https://azure.microsoft.com/en-us/pricing/details/container-registry/)에서 사용할 수 있습니다. 이 자습서에서는 **기본** 구독으로 충분합니다. 

- Docker ID를 등록할 경우(무료) [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)에서는 하나의 무료 개인 리포지토리를 제공합니다. 
    1. Docker ID를 등록하려면 Docker 사이트에 나와 있는 [Docker ID 등록](https://docs.docker.com/docker-id/#register-for-a-docker-id)의 지침을 따릅니다. 

    2. 개인 Docker 리포지토리를 만들려면 Docker 사이트에 나와 있는 [Docker Hub에 새 리포지토리 만들기](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub)의 지침을 따릅니다.

이 자습서 전체에서 해당하는 경우, Azure Container Registry와 Docker Hub 모두에 대한 명령을 제공합니다.

## <a name="create-a-function-project"></a>함수 프로젝트 만들기
다음 단계는 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 IoT Edge 함수를 만드는 방법을 보여 줍니다.
1. VS Code를 엽니다.
2. **보기 | 통합 터미널** 메뉴 명령을 사용하여 VS Code 통합 터미널을 엽니다.
3. 통합 터미널에서 다음 명령을 입력하여 dotnet에 **AzureIoTEdgeFunction** 템플릿을 설치(또는 업데이트)합니다.

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. 통합 터미널에서 다음 명령을 입력하여 새 모듈의 프로젝트를 만듭니다.

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

    >[!NOTE]
    > 이 명령은 현재 작업 폴더에 프로젝트 폴더 **FilterFunction**을 만듭니다. 다른 위치에 만들려면 명령을 실행하기 전에 디렉터리를 변경하세요.

3. **파일 | 폴더 열기** 메뉴 명령을 사용하여 **FilterFunction** 폴더로 이동한 다음 **폴더 선택**을 클릭하여 VS Code에서 프로젝트를 엽니다.
4. VS Code 탐색기에서 **EdgeHubTrigger-Csharp** 폴더를 클릭한 다음 **run.csx** 파일을 클릭하여 엽니다.
5. `#r "Microsoft.Azure.Devices.Client"` 문 뒤에 다음 문을 추가합니다.

    ```csharp
    #r "Newtonsoft.Json"
    ```

5. 기존 `using` 문 뒤에 나오는 문을 사용하여 다음을 추가합니다.

    ```csharp
    using Newtonsoft.Json;
    ```

1. 다음 클래스를 추가합니다. 이러한 클래스는 수신 메시지 본문의 예상 스키마를 정의합니다.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
    ```

1. **Run** 메서드의 본문을 다음 코드로 바꿉니다. 메시지 본문의 온도 값 및 온도 임계값을 기반으로 메시지를 필터링합니다.

    ```csharp
    const int temperatureThreshold = 25;

    byte[] messageBytes = messageReceived.GetBytes();
    var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

    if (!string.IsNullOrEmpty(messageString))
    {
        // Get the body of the message and deserialize it
        var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);
        
        if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
        {
            // We will send the message to the output as the temperature value is greater than the threashold
            var filteredMessage = new Message(messageBytes);
            // We need to copy the properties of the original message into the new Message object
            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
            {
                filteredMessage.Properties.Add(prop.Key, prop.Value);
            }
            // We are adding a new property to the message to indicate it is an alert
            filteredMessage.Properties.Add("MessageType", "Alert");
            // Send the message        
            await output.AddAsync(filteredMessage);
            log.Info("Received and transferred a message with temperature above the threshold");
        }
    }
    ```

11. 파일을 저장합니다.

## <a name="publish-a-docker-image"></a>Docker 이미지 게시

1. Docker 이미지를 빌드합니다.
    1. VS Code 탐색기에서 **Docker** 폴더를 클릭하여 엽니다. 그런 다음 컨테이너 플랫폼의 폴더를 선택합니다(**linux-x64** 또는 **windows-nano**). 
    2. **Dockerfile** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 모듈 Docker 이미지 빌드**를 클릭합니다. 
    3. **폴더 선택** 상자에서 프로젝트 폴더, **FilterFunction**으로 이동한 후 **EXE_DIR로 폴더 선택**을 클릭합니다. 
    4. VS Code 창의 맨 위에 있는 팝업 텍스트 상자에 이미지 이름을 입력합니다. 예를 들어 `<docker registry address>/filterfunction:latest`에서 *docker registry address*는 Docker Hub를 사용하는 경우 Docker ID이고, Azure Container Registry를 사용하는 경우 `<your registry name>.azurecr.io`와 유사합니다.
 
4. Docker에 로그인합니다. 통합 터미널에서 다음 명령을 입력합니다. 

    - Docker Hub(메시지가 표시되면 자격 증명 입력):
        
        ```csh/sh
        docker login
        ```

    - Azure Container Registry:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        이 명령에서 사용할 사용자 이름, 암호 및 로그인 서버를 찾으려면 [Azure Portal](https://portal.azure.com)로 이동합니다. **모든 리소스**에서 Azure Container Registry에 대한 타일을 클릭하여 속성을 연 다음 **액세스 키**를 클릭합니다. **사용자 이름**, **암호** 및 **로그인 서버** 필드의 값을 복사합니다. 로그인 서버는 `<your registry name>.azurecr.io` 형식이어야 합니다.

3. Docker 리포지토리에 이미지를 푸시합니다. **보기 | 명령 팔레트... | Edge: IoT Edge 모듈 Docker 이미지 푸시** 메뉴 명령을 사용하여 VS Code 창의 맨 위에 있는 팝업 텍스트 상자에 이미지 이름을 입력합니다. 1.c단계에서 사용한 동일한 이미지 이름을 사용하세요.

## <a name="add-registry-credentials-to-your-edge-device"></a>Edge 장치에 레지스트리 자격 증명 추가
Edge 장치를 실행 중인 컴퓨터의 Edge 런타임에 레지스트리의 자격 증명을 추가합니다. 이렇게 하면 컨테이너를 끌어오기 위한 런타임 액세스 권한이 제공됩니다. 

- Windows의 경우 다음 명령을 실행합니다.
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- Linux의 경우 다음 명령을 실행합니다.
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>솔루션 실행

1. **Azure Portal**에서 IoT Hub로 이동합니다.
2. **IoT Edge(미리 보기)**로 이동하여 IoT Edge 장치를 선택합니다.
1. **모듈 설정**을 선택합니다. 
2. **tempSensor** 모듈을 추가합니다. 이 단계는 이전에 **tempSensor** 모듈을 IoT Edge 장치에 배포하지 않은 경우에만 필요합니다.
    1. **IoT Edge 모듈 추가**를 선택합니다.
    2. **이름** 필드에 `tempSensor`를 입력합니다.
    3. **이미지 URI** 필드에 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`를 입력합니다.
    4. 다른 설정은 변경하지 않고 **저장**을 클릭합니다.
1. **filterfunction** 모듈을 추가합니다.
    1. **IoT Edge 모듈 추가**를 다시 선택합니다.
    2. **이름** 필드에 `filterfunction`을 입력합니다.
    3. **이미지** 필드에 이미지 주소(예: `<docker registry address>/filterfunction:latest`)를 입력합니다.
    74. **Save**를 클릭합니다.
2. **다음**을 누릅니다.
3. **경로 지정** 단계에서 다음 JSON을 텍스트 상자에 복사합니다. 모듈은 모든 메시지를 Edge 런타임에 게시합니다. 런타임의 선언적 규칙은 해당 메시지가 어디로 흐르는지를 정의합니다. 이 자습서에서는 두 개의 경로가 필요합니다. 첫 번째 경로는 **FilterMessages** 핸들러로 구성한 엔드포인트인 “input1” 엔드포인트를 통해 온도 센서에서 필터 모듈로 메시지를 전송합니다. 두 번째 경로는 필터 모듈에서 IoT Hub로 메시지를 전송합니다. 이 경로에서 `upstream`은 메시지를 IoT Hub로 보내라고 Edge Hub에 알리는 특수 대상입니다. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
    }
    ```

4. **다음**을 누릅니다.
5. **템플릿 검토** 단계에서 **제출**을 클릭합니다. 
6. IoT Edge 장치 세부 정보 페이지로 돌아가서 **새로 고침**을 클릭합니다. **tempSensor** 모듈 및 **IoT Edge runtime**과 함께 실행되는 새로운 **filterfunction** 모듈이 표시됩니다. 

## <a name="view-generated-data"></a>생성된 데이터 보기

IoT Edge 장치에서 IoT Hub로 보낸 장치-클라우드 메시지를 모니터하려면 다음을 수행하세요.
1. IoT Hub용 연결 문자열로 Azure IoT Toolkit 확장을 구성합니다. 
    1. **보기 | 탐색** 메뉴 명령을 사용하여 VS Code 탐색기를 엽니다. 
    3. 탐색기에서 **IOT HUB 장치**를 클릭하고 **...**을 클릭합니다. **IoT Hub 연결 문자열 설정**을 클릭하고, 팝업 창에서 IoT Edge 장치를 연결할 IoT Hub용 연결 문자열을 입력합니다. 

        연결 문자열을 찾으려면 Azure Portal에서 IoT Hub의 타일을 클릭한 다음 **공유 액세스 정책**을 클릭합니다. **공유 액세스 정책**에서 **iothubowner** 정책을 클릭한 다음 **iothubowner** 창에 있는 IoT Hub 연결 문자열을 복사합니다.   

1. IoT Hub에 도착하는 데이터를 모니터링하려면 **보기 | 명령 팔레트... | IoT: Start monitoring D2C message**(D2C 메시지 모니터링 시작) 메뉴 명령을 사용합니다. 
2. 데이터 모니터링을 중지하려면 **보기 | 명령 팔레트... | IoT: D2C 메시지 모니터링 중지** 메뉴 명령을 사용합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 장치에서 생성된 원시 데이터를 필터링하는 코드가 포함된 Azure 함수를 만들었습니다. Azure IoT Edge을 계속 탐색하려면 IoT Edge 장치를 게이트웨이로 사용하는 방법을 알아보세요. 

> [!div class="nextstepaction"]
> [IoT Edge 게이트웨이 장치 만들기](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
