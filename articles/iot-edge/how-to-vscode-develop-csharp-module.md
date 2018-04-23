---
title: Visual Studio Code를 사용하여 Azure IoT Edge에서 C# 모듈 개발 | Microsoft Docs
description: 컨텍스트 전환 없이 Visual Studio Code에서 C# 모듈을 개발하고 Azure IoT Edge에 배포
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 48c6cacebdeb7505c8dc2bcaed099c33862589ac
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2018
---
# <a name="use-visual-studio-code-to-develop-a-c-module-with-azure-iot-edge"></a>Visual Studio Code를 사용하여 Azure IoT Edge에서 C# 모듈 개발
이 문서에서는 [Visual Studio Code](https://code.visualstudio.com/)를 주 개발 도구로 사용하여 Azure IoT Edge 모듈을 개발 및 디버그하기 위한 자세한 지침을 제공합니다. 

## <a name="prerequisites"></a>필수 조건
이 아티클에서는 사용자가 Windows 또는 Linux를 실행하는 컴퓨터 또는 가상 머신을 개발 컴퓨터로 사용한다고 가정합니다. IoT Edge 장치가 다른 물리적 장치가 될 수도 있고, 개발 컴퓨터에서 IoT Edge 장치를 시뮬레이트할 수도 있습니다.

이 설명서를 시작하기 전에 다음과 같은 자습서를 완료하세요.
- [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) 또는 [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)에서 시뮬레이트된 장치에 Azure IoT Edge 배포
- [C# IoT Edge 모듈을 개발하여 시뮬레이트된 장치에 배포](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

다음은 이전 자습서를 완료한 경우 표시되어야 하는 항목을 보여 주는 검사 목록입니다.

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Visual Studio Code에 대한 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [C# for Visual Studio Code(OmniSharp 제공) 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT Edge 제어 스크립트](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeModule 템플릿(`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- 하나 이상의 IoT Edge 장치가 있는 활성 IoT Hub.

모듈 이미지와 컨테이너를 보다 잘 관리하기 위해 [VS Code에 대한 Docker 지원](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)을 설치하는 것도 유용한 방법입니다.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>VS Code에서 Azure IoT Edge 모듈 배표

### <a name="list-your-iot-hub-devices"></a>IoT 허브 장치 나열
VS Code에 IoT 허브 장치를 나열하는 두 가지 방법이 있습니다. 둘 중 하나를 선택하여 계속할 수 있습니다.

#### <a name="sign-in-to-your-azure-account-in-vs-code-and-choose-your-iot-hub"></a>VSCode에서 Azure 계정에 로그인하고 IoT 허브를 선택합니다.
1. 명령 팔레트(F1 또는 Ctrl + Shift + P)에서 **Azure: 로그인**을 입력하고 선택합니다. 그런 다음 **복사 및 열기**를 선택합니다. 브라우저에 코드를 붙여넣고(Ctrl + V) **계속**을 선택합니다. 그런 후 Azure 계정으로 로그인합니다. VS Code 상태 표시줄에서 계정 정보를 볼 수 있습니다.
2. 명령 팔레트에 **IoT: IoT Hub 선택**을 입력하고 선택합니다. 먼저 이전 자습서에서 IoT 허브를 만든 구독을 선택합니다. 그런 다음 IoT Edge 장치를 포함하는 IoT 허브를 선택합니다.

    ![장치 목록 스크린샷](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 설정
명령 팔레트에서 **IoT: IoT Hub 연결 문자열 설정**을 입력하고 선택합니다. **iothubowner** 정책 아래에 연결 문자열을 붙여 넣습니다. (Azure Portal에서 IoT Hub의 공유 액세스 정책에서 찾을 수 있습니다.)
 
왼쪽 세로 막대에서 IoT Hub Devices Explorer의 장치 목록을 볼 수 있습니다.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>IoT Edge 런타임 시작 및 모듈 배포
장치에 Azure IoT Edge 런타임을 설치하고 시작합니다. Azure IoT Hub로 원격 분석 데이터를 보낼 시뮬레이션된 센서 모듈을 배포합니다.
1. 명령 팔레트에서 **Edge: Edge 설정**을 선택하고 IoT Edge 장치 ID를 선택합니다. 또는 **장치 목록**에서 IoT Edge 장치 ID를 마우스 오른쪽 단추로 클릭하고 **Edge 설정**을 선택합니다.

    ![Edge 런타임 설정 스크린샷](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. 명령 팔레트에서 **Edge: Edge 시작**을 선택하여 IoT Edge 런타임을 시작합니다. 통합된 터미널에 해당 출력을 볼 수 있습니다.

    ![Edge 런타임 시작 스크린샷](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. Docker 탐색기에서 IoT Edge 런타임 상태를 확인합니다. 녹색은 IoT Edge 런타임이 제대로 시작되어 실행되고 있음을 나타냅니다. 컴퓨터는 이제 IoT Edge 장치를 시뮬레이트합니다.

    ![Edge 런타임 상태 스크린샷](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. IoT Edge 장치에 메시지를 계속 전송하는 센서를 시뮬레이트합니다. 명령 팔레트에서 **Edge: Edge 구성 파일 생성**을 입력하고 선택합니다. 이 파일을 만들 폴더를 선택합니다. 생성된 deployment.json 파일에서 콘텐츠 `<registry>/<image>:<tag>`를 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`로 바꾼 후 파일을 저장합니다.

    ![센서 모듈 스크린샷](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. **Edge: Edge 장치에 대한 배포 만들기**를 선택하고 새 배포를 만들 IoT Edge 장치 ID를 선택합니다. 또는 장치 목록에서 IoT Edge 장치 ID를 마우스 오른쪽 단추로 클릭하고 **Edge 장치에 대한 배포 만들기**를 선택할 수 있습니다. 

6. Docker 탐색기에서 시뮬레이트된 센서를 사용하여 IoT Edge가 실행을 시작하는 것을 볼 수 있습니다. Docker 탐색기에서 컨테이너를 마우스 오른쪽 단추로 클릭합니다. 각 모듈에 대한 docker 로그를 볼 수 있습니다. 또한, 장치 목록에 있는 모듈 목록을 볼 수 있습니다.

    ![모듈 목록 스크린샷](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. IoT Edge 장치 ID를 마우스 오른쪽 단추로 클릭하고 VS Code의 D2C 메시지를 모니터링할 수 있습니다.
8. IoT Edge 런타임 및 센서 모듈을 중지하려면 명령 팔레트에서 **Edge: Edge 중지**를 입력하고 선택합니다.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>VS Code에서 C# 모듈 개발 및 배포
[C# 모듈 개발](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module) 자습서에서 VS Code의 모듈 이미지를 업데이트, 빌드 및 게시합니다. 그런 다음 Azure Portal로 이동하여 C# 모듈을 배포합니다. 이 섹션에서는 VS Code를 사용하여 C# 모듈을 배포하고 모니터링하는 방법을 소개합니다.

### <a name="start-a-local-docker-registry"></a>로컬 Docker 레지스트리 시작
이 아티클에서 Docker 호환 레지스트리를 사용할 수 있습니다. 클라우드에서 사용 가능한 두 개의 인기 있는 Docker 레지스트리 서비스는 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 및 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)입니다. 이 섹션에서는 초기 개발 중에 테스트용으로 제공되는 좀 더 용이한 [로컬 Docker 레지스트리](https://docs.docker.com/registry/deploying/)를 사용합니다.
VS Code의 **통합 터미널**(Ctrl + ')에서 다음 명령을 실행하여 로컬 레지스트리를 시작합니다.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> 이 예제에서는 테스트에만 적합한 레지스트리 구성을 보여 줍니다. 프로덕션에 사용 가능한 레지스트리는 TLS로 보호되어야 하며, 액세스 제어 메커니즘을 사용하는 것이 좋습니다. 프로덕션에 사용 가능한 IoT Edge 모듈을 배포하려면 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 또는 [Docker 허브](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)를 사용하는 것이 좋습니다.

### <a name="create-an-iot-edge-module-project"></a>IoT Edge 모듈 프로젝트 만들기
다음 단계는 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 .NET Core 2.0을 기반으로 IoT Edge 모듈을 만드는 방법을 보여 줍니다. 이전 자습서에서 이 섹션을 완료했으면 건너뛰어도 됩니다.
1. Visual Studio Code에서 **보기** > **통합 터미널**을 선택하여 VS Code 통합 터미널을 엽니다.
3. 통합 터미널에서 다음 명령을 입력하여 dotnet에 **AzureIoTEdgeModule** 템플릿을 설치(또는 갱신)합니다.

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. 새 모듈에 대한 프로젝트를 만듭니다. 다음 명령은 현재 작업 폴더에 프로젝트 폴더 **FilterModule**을 만듭니다.

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. **파일** > **폴더 열기**를 선택합니다.
4. **FilterModule** 폴더로 이동한 다음 **폴더 선택**을 클릭하여 VS Code에서 프로젝트를 엽니다.
5. VS Code 탐색기에서 **Program.cs**를 선택하여 엽니다. **program.cs**의 맨 위에 다음 네임스페이스를 포함합니다.
   ```csharp
   using Microsoft.Azure.Devices.Shared;
   using System.Collections.Generic;  
   using Newtonsoft.Json;
   ```

6. `temperatureThreshold` 변수를 **Program** 클래스에 추가합니다. 이 변수는 데이터가 IoT Hub로 전송되기 위해 측정된 온도가 초과해야 하는 값을 설정합니다. 

   ```csharp
   static int temperatureThreshold { get; set; } = 25;
   ```

7. `MessageBody`, `Machine` 및 `Ambient` 클래스를 **Program** 클래스에 추가합니다. 이러한 클래스는 수신 메시지 본문의 예상 스키마를 정의합니다.

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

8. **Init** 메서드에서 코드는 **DeviceClient** 개체를 만들고 구성합니다. 이 개체를 사용하면 메시지를 주고받기 위해 로컬 IoT Edge 런타임에 모듈을 연결할 수 있습니다. IoT Edge 런타임은 **Init** 메서드에 사용되는 연결 문자열을 이 모듈에 제공합니다. **DeviceClient** 개체를 만든 후 코드는 **input1** 끝점을 통해 IoT Edge 허브로부터 메시지를 수신하기 위한 콜백을 등록합니다. `SetInputMessageHandlerAsync` 메서드를 새 메서드로 바꾸로 원하는 속성 업데이트에 대한 `SetDesiredPropertyUpdateCallbackAsync` 메서드를 추가합니다. 이 변경을 수행하려면 **Init** 메서드의 마지막 줄을 다음 코드로 바꾸세요.

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. **Program** 클래스에 `onDesiredPropertiesUpdate` 메서드를 추가합니다. 이 메서드는 모듈 쌍에서 원하는 속성에 대한 업데이트를 수신하고, 일치하도록 **temperatureThreshold** 변수를 업데이트합니다. 모든 모듈에는 자체 모듈 쌍이 있어서 클라우드에서 직접 모듈 내에서 실행되는 코드를 구성할 수 있습니다.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

10. `PipeMessage` 메서드를 `FilterMessages` 메서드로 바꿉니다. 이 메서드는 모듈이 IoT Edge Hub에서 메시지를 수신할 때마다 호출됩니다. 모듈 쌍을 통해 설정된 온도 임계값 아래의 온도를 보고하는 메시지를 필터링합니다. 또한 값이 **Alert**로 설정된 **MessageType** 속성을 메시지에 추가합니다. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. 프로젝트를 빌드하려면 탐색기에서 **FilterModule.csproj** 파일을 마우스 오른쪽 단추로 클릭하고 **Build IoT Edge 모듈 빌드**를 선택합니다. 이 프로세스는 모듈을 컴파일하고 이진 및 해당 종속성을 Docker 이미지 생성에 사용되는 폴더로 내보냅니다. 

    ![VS Code Explorer 스크린샷](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker 이미지를 만들어 레지스트리에 게시

1. VS Code 탐색기에서 **Docker** 폴더를 확장합니다. 그런 다음 컨테이너 플랫폼에 대한 폴더(**linux-x64** 또는 **windows-nano**)를 확장합니다.
2. **Dockerfile** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 모듈 Docker 이미지 빌드**를 선택합니다. 

    ![VS Code Explorer 스크린샷](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. **폴더 선택** 창에서 `./bin/Debug/netcoreapp2.0/publish`로 이동하거나 이 항목을 입력합니다. **Select Folder as EXE_DIR**(EXE_DIR로 폴더 선택)을 선택합니다.
4. VS Code 창의 맨 위에 있는 팝업 텍스트 상자에 이미지 이름을 입력합니다. 예: `<your container registry address>/filtermodule:latest` 로컬 레지스트리를 배포하는 경우 `localhost:5000/filtermodule:latest`를 입력해야 합니다.
5. Docker 리포지토리에 이미지를 푸시합니다. **Edge: IoT Edge 모듈 Docker 이미지 푸시** 명령을 사용하여 VS Code 창의 맨 위에 있는 팝업 텍스트 상자에 이미지 URL을 입력합니다. 이전 단계에서 사용한 동일한 이미지 URL을 사용하세요. 콘솔 로그를 확인하여 이미지가 푸시되었는지 검토합니다.

    ![Docker 이미지 푸시 스크린샷](./media/how-to-vscode-develop-csharp-module/push-image.png) ![콘솔 로그 스크린샷](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>IoT Edge 모듈 배포

1. `deployment.json` 파일을 열고 **modules** 섹션을 다음으로 바꿉니다.
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
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. **routes** 섹션을 다음으로 바꿉니다.
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > 런타임의 선언적 규칙은 해당 메시지가 어디로 흐르는지를 정의합니다. 이 아티클에서는 두 개의 경로가 필요합니다. 첫 번째 경로는 “input1” 끝점을 통해 온도 센서에서 필터 모듈로 메시지를 전송합니다. FilterMessages 처리기로 구성한 끝점입니다. 두 번째 경로는 필터 모듈에서 IoT Hub로 메시지를 전송합니다. 이 경로에서 upstream은 메시지를 IoT Hub로 보내라고 IoT Edge Hub에 알리는 특수 대상입니다.

3. 이 파일을 저장합니다.
4. 명령 팔레트에서 **Edge: Edge 장치에 대한 배포 만들기**를 선택합니다. 그런 다음 배포를 만들 IoT Edge 장치 ID를 선택합니다. 또는 장치 목록에서 장치 ID를 마우스 오른쪽 단추로 클릭하고 **Edge 장치에 대한 배포 만들기**를 선택합니다.

    ![배포 만들기 옵션 스크린샷](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. 업데이트한 `deployment.json`을 선택합니다. 출력 창에서 배포에 해당하는 출력을 볼 수 있습니다.

    ![출력 창의 스크린샷](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. 명령 팔레트에서 **Edge: Edge 시작**을 선택하여 IoT Edge 런타임을 시작합니다.
7. Docker 탐색기에서 시뮬레이트된 센서 및 필터 모듈을 사용하여 IoT Edge 런타임이 실행되는 것을 볼 수 있습니다.

    ![Docker 탐색기 스크린샷](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. IoT Edge 장치 ID를 마우스 오른쪽 단추로 클릭하고 VS Code의 D2C 메시지를 모니터링할 수 있습니다.


## <a name="next-steps"></a>다음 단계

[VS Code에서 C# 모듈 디버그](how-to-vscode-debug-csharp-module.md)
