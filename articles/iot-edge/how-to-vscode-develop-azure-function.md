---
title: Visual Studio Code를 사용하여 Azure Functions 개발 및 Azure IoT Edge에 배포 | Microsoft Docs
description: 컨텍스트 전환 없이 VS Code에서 C# Azure Functions를 개발하고 Azure IoT Edge에 배포
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 47d420b4b283b390f67719233c4bea59495a589a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2018
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>Visual Studio Code를 사용하여 Azure Functions 개발 및 Azure IoT Edge에 배포

이 문서에서는 [Visual Studio Code](https://code.visualstudio.com/)를 주 개발 도구로 사용하여 IoT Edge에서 Azure Functions를 개발하고 배포하기 위한 자세한 지침을 제공합니다. 

## <a name="prerequisites"></a>필수 조건
이 아티클에서는 사용자가 Windows 또는 Linux를 실행하는 컴퓨터 또는 가상 머신을 개발 컴퓨터로 사용한다고 가정합니다. IoT Edge 장치가 다른 물리적 장치가 될 수도 있고, 개발 컴퓨터에서 IoT Edge 장치를 시뮬레이트할 수도 있습니다.

이 설명서를 시작하기 전에 다음 자습서를 완료했는지 확인합니다.
- [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) 또는 [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)에서 시뮬레이트된 장치에 Azure IoT Edge 배포
- [Azure Functions 배포](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

다음은 이전 자습서를 완료한 경우 표시되어야 하는 항목을 보여 주는 검사 목록입니다.

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Visual Studio Code용 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [C# for Visual Studio Code(OmniSharp 제공) 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT Edge 제어 스크립트](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeFunction 템플릿(`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- 하나 이상의 IoT Edge 장치가 있는 활성 IoT Hub.

모듈 이미지와 컨테이너를 보다 잘 관리하기 위해 [VS Code에 대한 Docker 지원](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)을 설치하는 것도 좋은 방법입니다.

> [!NOTE]
> 현재, IoT Edge의 Azure Functions는 C#만 지원합니다.

## <a name="deploy-azure-iot-functions-in-vs-code"></a>VS Code의 Azure IoT Functions 배포
[Azure Functions 배포](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function) 자습서에서는 VS Code에서 함수 모듈 이미지를 업데이트, 빌드 및 게시한 다음, Azure Portal을 방문하여 Azure Functions를 배포합니다. 이 섹션에서는 VS Code를 사용하여 Azure Functions를 배포하고 모니터링하는 방법을 소개합니다.

### <a name="start-a-local-docker-registry"></a>로컬 Docker 레지스트리 시작
이 아티클에서 Docker 호환 레지스트리를 사용할 수 있습니다. 클라우드에서 사용 가능한 두 개의 인기 있는 Docker 레지스트리 서비스는 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 및 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)입니다. 이 섹션에서는 초기 개발 중에 테스트용으로 제공되는 좀 더 용이한 [로컬 Docker 레지스트리](https://docs.docker.com/registry/deploying/)를 사용합니다.
VS Code의 **통합 터미널**(Ctrl + ')에서 다음 명령을 실행하여 로컬 레지스트리를 시작합니다.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> 위 예제에서는 테스트에만 적합한 레지스트리 구성을 보여 줍니다. 프로덕션에 사용 가능한 레지스트리는 TLS로 보호되어야 하며, 액세스 제어 메커니즘을 사용하는 것이 좋습니다. 프로덕션에 사용 가능한 IoT Edge 모듈을 배포하려면 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 또는 [Docker 허브](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)를 사용하는 것이 좋습니다.

### <a name="create-a-function-project"></a>함수 프로젝트 만들기
다음 단계는 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 .NET Core 2.0을 기반으로 IoT Edge 모듈을 만드는 방법을 보여 줍니다. 이전 자습서에서 이 섹션을 완료했으면 건너뛰어도 됩니다.

1. Visual Studio Code에서 **보기** > **통합 터미널**을 선택하여 VS Code 통합 터미널을 엽니다.
2. dotnet에서 **AzureIoTEdgeFunction** 템플릿을 설치(또는 업데이트)하려면 통합 터미널에서 다음 명령을 실행합니다.

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. 새 모듈에 대한 프로젝트를 만듭니다. 다음 명령은 현재 작업 폴더에 프로젝트 폴더 **FilterFunction**을 만듭니다.

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. **파일 > 폴더 열기**를 선택한 후 **FilterFunction** 폴더로 이동하고 VS Code에서 프로젝트를 엽니다.
5. **FilterFunction** 폴더로 이동한 다음 **폴더 선택**을 클릭하여 VS Code에서 프로젝트를 엽니다.
6. VS Code 탐색기에서 **EdgeHubTrigger-Csharp** 폴더를 확장한 다음 **run.csx** 파일을 엽니다.
7. 파일 내용을 다음 코드로 바꿉니다.

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
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

8. 파일을 저장합니다.

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker 이미지를 만들어 레지스트리에 게시

1. VS Code 탐색기에서 **Docker** 폴더를 확장합니다. 그런 다음 컨테이너 플랫폼에 대한 폴더(**linux-x64** 또는 **windows-nano**)를 확장합니다.
2. **Dockerfile** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 모듈 Docker 이미지 빌드**를 클릭합니다. 

    ![Docker 이미지 빌드](./media/how-to-vscode-develop-csharp-function/build-docker-image.png)

3. **FilterFunction** 프로젝트 폴더로 이동하고 **EXE_DIR로 폴더 선택**을 클릭합니다. 
4. VS Code 창의 맨 위에 있는 팝업 텍스트 상자에 이미지 이름을 입력합니다. 예: `<your container registry address>/filterfunction:latest` 로컬 레지스트리를 배포하는 경우 `localhost:5000/filterfunction:latest`를 입력해야 합니다.
5. Docker 리포지토리에 이미지를 푸시합니다. **Edge: IoT Edge 모듈 Docker 이미지 푸시** 명령을 사용하여 VS Code 창의 맨 위에 있는 팝업 텍스트 상자에 이미지 URL을 입력합니다. 위 단계에서 사용한 동일한 이미지 URL을 사용하세요.
    ![Docker 이미지 푸시](./media/how-to-vscode-develop-csharp-function/push-image.png)

### <a name="deploy-your-function-to-iot-edge"></a>IoT Edge에 함수 배포

1. `deployment.json` 파일을 열고 **modules** 섹션을 아래 콘텐츠로 바꿉니다.
   ```json
   "tempSensor": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
         "createOptions": ""
      }
   },
   "filterfunction": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "localhost:5000/filterfunction:latest",
         "createOptions": ""
      }
   }
   ```

2. **routes** 섹션을 아래 콘텐츠로 바꿉니다.
   ```json
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   ```
   > [!NOTE]
   > 런타임의 선언적 규칙은 해당 메시지가 어디로 흐르는지를 정의합니다. 이 아티클에서는 두 개의 경로가 필요합니다. 첫 번째 경로는 FilterMessages 핸들러로 구성한 끝점인 “input1” 끝점을 통해 온도 센서에서 필터 함수로 메시지를 전송합니다. 두 번째 경로는 필터 함수에서 IoT Hub로 메시지를 전송합니다. 이 경로에서 upstream은 메시지를 IoT Hub로 보내라고 Edge Hub에 알리는 특수 대상입니다.

3. 이 파일을 저장합니다.
4. 명령 팔레트에서 **Edge: Edge 장치에 대한 배포 만들기**를 선택합니다. 그런 다음 배포를 만들 IoT Edge 장치 ID를 선택합니다. 또는 장치 목록에서 장치 ID를 마우스 오른쪽 단추로 클릭하고 **Edge 장치에 대한 배포 만들기**를 선택합니다.

    ![배포 만들기](./media/how-to-vscode-develop-csharp-function/create-deployment.png)

5. 업데이트한 `deployment.json`을 선택합니다. 출력 창에서 배포에 해당하는 출력을 볼 수 있습니다.
6. 명령 팔레트에서 Edge 런타임을 시작합니다. **Edge: Edge 시작**
7. Docker 탐색기에서 시뮬레이트된 센서 및 필터 함수를 사용하여 IoT Edge 런타임이 실행되는 것을 볼 수 있습니다.

    ![실행 중인 솔루션](./media/how-to-vscode-develop-csharp-function/solution-running.png)

8. Edge 장치 ID를 마우스 오른쪽 단추로 클릭하고 VS Code의 D2C 메시지를 모니터링할 수 있습니다.

    ![메시지 모니터링](./media/how-to-vscode-develop-csharp-function/monitor-d2c-messages.png)


## <a name="next-steps"></a>다음 단계

[VS Code에서 Azure Functions 디버그](how-to-vscode-debug-azure-function.md)
