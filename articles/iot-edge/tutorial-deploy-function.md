---
title: 자습서 - 디바이스에 Azure 함수 배포 Azure IoT Edge | Microsoft Docs
description: 이 자습서에서는 Azure 함수를 IoT Edge 모듈로 개발한 다음, 에지 디바이스에 배포합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: d8912b67656cb9862f31b03eb9a899dfc650f377
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053368"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>자습서: IoT Edge 모듈로 Azure 함수 배포

비즈니스 논리를 직접 Azure IoT Edge 디바이스에 구현하는 코드를 배포하려면 Azure Functions를 사용할 수 있습니다. 이 자습서에서는 시뮬레이션된 IoT Edge 디바이스에서 센서 데이터를 필터링하는 Azure 함수를 만들고 배포하는 과정을 안내합니다. 여기서는 [Windows](quickstart.md) 또는 [Linux](quickstart-linux.md) 빠른 시작의 시뮬레이션된 디바이스에 Azure IoT Edge 배포에서 만든 시뮬레이션된 IoT Edge 디바이스를 사용합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.     

> [!div class="checklist"]
> * Visual Studio Code를 사용하여 Azure 함수를 만듭니다.
> * VS Code 및 Docker를 사용하여 Docker 이미지를 만들어 컨테이너 레지스트리에 게시합니다.
> * IoT Edge 디바이스에 컨테이너 레지스트리의 모듈을 배포합니다.
> * 필터링된 데이터를 봅니다.

<center>
![다이어그램 - 함수 모듈 아키텍처, 스테이징 및 배포 자습서](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Azure IoT Edge의 Azure 함수 모듈은 공개 [미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 상태입니다. 

이 자습서에서 만드는 Azure 함수는 디바이스에서 생성한 온도 데이터를 필터링합니다. 함수는 온도가 지정된 임계값을 초과하는 경우에만 Azure IoT Hub에 메시지 업스트림을 전송합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 디바이스:

* [Linux](quickstart-linux.md) 또는 [Windows 디바이스](quickstart.md)의 빠른 시작에 설명된 단계에 따라 개발 머신 또는 가상 머신을 Edge 디바이스로 설정할 수 있습니다.

클라우드 리소스:

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md). 

개발 리소스:

* [Visual Studio Code](https://code.visualstudio.com/) 
* [C# for Visual Studio Code(OmniSharp 제공) 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Visual Studio Code용 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

이 자습서에서는 Visual Studio Code용 Azure IoT Tools를 사용하여 모듈을 빌드하고 파일에서 **컨테이너 이미지**를 만듭니다. 그런 후 이미지를 저장하고 관리하는 **레지스트리**에 이 이미지를 푸시합니다. 마지막으로 IoT Edge 장치에서 실행되도록 레지스트리의 이미지를 배포합니다.  

임의 Docker 호환 레지스트리를 사용하여 컨테이너 이미지를 유지할 수 있습니다. 두 개의 인기 있는 Docker 레지스트리 서비스는 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 및 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)입니다. 이 자습서에서는 Azure Container Registry를 사용합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **컨테이너** > **Container Registry**를 선택합니다.

    ![Azure Portal에서 컨테이너 레지스트리 만들기](./media/tutorial-deploy-function/create-container-registry.png)

2. 다음 값을 입력하여 컨테이너 레지스트리를 만듭니다.

   | 필드 | 값 | 
   | ----- | ----- |
   | 레지스트리 이름 | 고유한 이름을 입력합니다. |
   | 구독 | 드롭다운 목록에서 구독을 선택합니다. |
   | 리소스 그룹 | IoT Edge 빠른 시작 및 자습서에서 만드는 모든 테스트 리소스에 동일한 리소스 그룹을 사용하는 것이 좋습니다. 예를 들어 **IoTEdgeResources**를 사용합니다. |
   | 위치 | 가까운 위치를 선택합니다. |
   | 관리 사용자 | **사용**으로 설정합니다. |
   | SKU | **기본**을 선택합니다. | 

5. **만들기**를 선택합니다.

6. 컨테이너 레지스트리를 만든 후에는 해당 레지스트리를 찾은 다음, **액세스 키**를 선택합니다. 

7. **로그인 서버**, **사용자 이름** 및 **암호**의 값을 복사합니다. 나중에 자습서의 뒷부분에서 이러한 값을 사용하여 컨테이너 레지스트리에 대한 액세스를 제공합니다. 

## <a name="create-a-function-project"></a>함수 프로젝트 만들기

필수 조건에서 설치한 Visual Studio Code용 Azure IoT Tools는 관리 기능뿐만 아니라 일부 코드 템플릿도 제공합니다. 이 섹션에서는 Visual Studio Code를 사용하여 Azure 함수를 포함하는 IoT Edge 솔루션을 만듭니다. 

1. 개발 머신에서 Visual Studio Code를 엽니다.

2. **보기** > **명령 팔레트**를 차례로 선택하여 VS Code 명령 팔레트를 엽니다.

3. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 명령 팔레트의 프롬프트에 따라 솔루션을 만듭니다.

   | 필드 | 값 |
   | ----- | ----- |
   | 폴더 선택 | VS Code에 대한 개발 머신에서 위치를 선택하여 솔루션 파일을 만듭니다. |
   | 솔루션 이름 제공 | **FunctionSolution**과 같은 솔루션에 대한 설명이 포함된 이름을 입력하거나 기본값을 적용합니다. |
   | 모듈 템플릿 선택 | **Azure Functions - C#** 을 선택합니다. |
   | 모듈 이름 제공 | 모듈의 이름을 **CSharpFunction**으로 지정합니다. |
   | 모듈의 Docker 이미지 리포지토리 제공 | 이미지 리포지토리는 컨테이너 레지스트리의 이름 및 컨테이너 이미지의 이름을 포함합니다. 컨테이너 이미지는 마지막 단계에서 미리 채워져 있습니다. **localhost:5000**을 Azure 컨테이너 레지스트리의 로그인 서버 값으로 바꿉니다. Azure Portal에서 컨테이너 레지스트리의 개요 페이지에서 로그인 서버를 검색할 수 있습니다. 마지막 문자열은 \<레지스트리 이름\>.azurecr.io/CSharpFunction과 같습니다. |

   ![Docker 이미지 리포지토리 제공](./media/tutorial-deploy-function/repository.png)

4. VS Code 창은 \.vscode 폴더, 모듈 폴더, 배포 매니페스트 템플릿 파일 등 IoT Edge 솔루션 작업 영역을 로드합니다. \.env 파일입니다. VS Code 탐색기에서 **모듈** > **CSharpFunction** > **CSharpFunction.cs**를 엽니다.

5. **CSharpFunction.cs** 파일 내용을 다음 코드로 바꿉니다. 이 코드는 주위 온도 및 기계 온도에 대한 원격 분석을 수신한 후, 기계 온도가 정의된 임계값을 초과하는 경우 IoT Hub에 메시지를 전달합니다.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

6. 파일을 저장합니다.

## <a name="build-your-iot-edge-solution"></a>IoT Edge 솔루션 빌드

이전 섹션에서는 IoT Edge 솔루션을 만들고 **CSharpFunction**에 코드를 추가하여 보고된 컴퓨터 온도가 허용 가능한 임계값 이하인 메시지를 필터링했습니다. 이제 솔루션을 컨테이너 이미지로 빌드하고 컨테이너 레지스트리로 푸시해야 합니다.

이 섹션에서는 컨테이너 레지스트리의 자격 증명을 두 번 입력합니다. 첫 번째는 Visual Studio Code가 레지스트리에 이미지를 푸시할 수 있도록 개발 머신에서 로컬로 로그인할 때입니다. 두 번째는 레지스트리에서 이미지를 풀하는 권한을 IoT Edge 디바이스에 제공하는 IoT Edge 솔루션의 **.env** 파일입니다. 

1. **보기** > **터미널**을 차례로 선택하여 VS Code 통합 터미널을 엽니다. 

2. 통합 터미널에 다음 명령을 입력하여 컨테이너 레지스트리에 로그인합니다. 앞에서 복사한 Azure 컨테이너 레지스트리의 사용자 이름 및 로그인 서버를 사용합니다.
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    암호를 묻는 경우 컨테이너 레지스트리의 암호를 붙여넣고 **Enter** 키를 누릅니다.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. VS Code 탐색기에서 IoT Edge 솔루션 작업 영역에 있는 **deployment.template.json** 파일을 엽니다. 이 파일은 IoT Edge 런타임에 디바이스에 배포할 모듈을 알려줍니다. **CSharpFunction** 함수 모듈은 테스트 데이터를 제공하는 **tempSensor** 모듈과 함께 나열됩니다. 배포 매니페스트에 대한 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.

   ![배포 매니페스트의 모듈 보기](./media/tutorial-deploy-function/deployment-template.png)

3. IoT Edge 솔루션 작업 영역에서 **.env** 파일을 엽니다. 배포 매니페스트 템플릿에 컨테이너 레지스트리 자격 증명을 필요가 없도록 이 git 무시 파일은 컨테이너 레지스트리 자격 증명을 저장합니다. 컨테이너 레지스트리의 **사용자 이름** 및 **암호**를 입력합니다. 

5. 이 파일을 저장합니다.

6. VS Code 탐색기에서 deployment.template.json 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드 및 푸시**를 선택합니다. 

솔루션을 빌드하도록 Visual Studio Code에 지시하면 먼저 배포 템플릿의 정보를 가져와서 **config**라는 새 폴더에 deployment.json 파일을 생성합니다. 그런 다음, 통합 터미널에서 두 개의 명령, 즉 `docker build`과 `docker push`를 실행합니다. 이 두 명령은 코드를 빌드하고, 함수를 컨테이너화한 다음, 솔루션을 초기화할 때 지정한 컨테이너 레지스트리로 코드를 푸시합니다. 

## <a name="view-your-container-image"></a>컨테이너 이미지 보기

컨테이너 이미지가 컨테이너 레지스트리에 푸시될 때 Visual Studio Code는 성공 메시지를 출력합니다. 직접 성공적인 작업을 확인하려면 레지스트리에서 이미지를 볼 수 있습니다. 

1. Azure Portal에서 Azure 컨테이너 레지스트리를 찾습니다. 
2. **리포지토리**를 선택합니다.
3. 목록에 **csharpfunction** 리포지토리가 표시됩니다. 자세한 내용을 보려면 이 리포지토리를 선택합니다.
4. **태그** 섹션에 **0.0.1-amd64** 태그가 표시됩니다. 이 태그는 빌드하는 이미지의 버전 및 플랫폼을 나타냅니다. 이러한 값은 CSharpFunction 폴더의 module.json 파일에서 설정됩니다. 

## <a name="deploy-and-run-the-solution"></a>솔루션 배포 및 실행

Azure Portal을 사용하여 빠른 시작에서 수행한 것처럼 IoT Edge 디바이스에 함수 모듈을 배포할 수 있습니다. 또한 Visual Studio Code 내에서 모듈을 배포하고 모니터링할 수 있습니다. 다음 섹션에서는 필수 조건에 나와 있는 VS Code용 Azure IoT Tools를 사용합니다. 아직 설치하지 않은 경우 확장을 설치합니다. 

1. **보기** > **명령 팔레트**를 차례로 선택하여 VS Code 명령 팔레트를 엽니다.

2. **Azure: 로그인** 명령을 검색하여 실행합니다. 지침에 따라 Azure 계정에 로그인합니다. 

3. 명령 팔레트에서 **Azure IoT Hub: IoT Hub 선택** 명령을 검색하여 실행합니다. 

4. IoT Hub가 있는 구독을 선택한 다음, 액세스하려는 IoT 허브를 선택합니다.

5. VS Code 탐색기에서 **Azure IoT Hub 디바이스** 섹션을 펼칩니다. 

6. IoT Edge 디바이스의 이름을 마우스 오른쪽 단추로 클릭한 다음, **단일 디바이스용 배포 만들기**를 선택합니다. 

7. **CSharpFunction**이 포함된 솔루션 폴더를 찾습니다. config 폴더를 열고 **deployment.json** 파일을 선택한 다음, **Edge 배포 매니페스트 선택**을 클릭합니다.

8. **Azure IoT Hub 디바이스** 섹션을 새로 고칩니다. **TempSensor** 모듈과 **$edgeAgent** 및 **$edgeHub**와 함께 실행되는 새 **CSharpFunction**이 표시됩니다. 모듈을 시작하는 데 몇 분 정도 걸릴 수 있습니다. IoT Edge 디바이스는 IoT Hub에서 해당 새 배포 정보를 검색하고, 새 컨테이너를 시작하고, 상태를 IoT Hub에 다시 보고해야 합니다. 

   ![VS Code에서 배포된 모듈 보기](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>생성된 데이터 보기

명령 팔레트에서 **Azure IoT Hub: D2C 메시지 모니터링 시작**을 실행하여 IoT Hub에 도착한 모든 메시지를 볼 수 있습니다.

특정 디바이스에서 IoT Hub에 도달한 모든 메시지를 보려면 보기를 필터링할 수도 있습니다. **Azure IoT Hub 디바이스** 섹션에서 디바이스를 마우스 오른쪽 단추로 클릭하고 **D2C 메시지 모니터링 시작**을 선택합니다.

메시지 모니터링을 중지하려면 명령 팔레트에서 **Azure IoT Hub: D2C 메시지 모니터링 중지** 명령을 실행합니다. 


## <a name="clean-up-resources"></a>리소스 정리

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 장치와 동일한 IoT Edge 장치를 계속 사용해도 됩니다. 

그렇지 않은 경우 요금 청구를 방지하도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 디바이스에서 생성한 원시 데이터를 필터링하는 코드가 포함된 Azure 함수 모듈을 만들었습니다. 고유한 모듈을 빌드할 준비가 되면 [Visual Studio Code에 대한 Azure IoT Edge를 사용하여 Azure 함수를 개발](how-to-develop-csharp-function.md)하는 방법에 대해 자세히 알아볼 수 있습니다. 

Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [Azure Stream Analytics에서 부동 창을 사용하여 평균 찾기](tutorial-deploy-stream-analytics.md)

