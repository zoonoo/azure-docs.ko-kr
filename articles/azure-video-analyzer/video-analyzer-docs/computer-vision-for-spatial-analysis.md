---
title: 공간 분석을 위해 Computer Vision을 사용하여 라이브 비디오 분석 - Azure
description: 이 자습서에서는 Azure Cognitive Services의 Computer Vision 공간 분석 AI 기능과 함께 Azure Video Analyzer를 사용하여 (시뮬레이션된) IP 카메라의 라이브 비디오 피드를 분석하는 방법을 보여줍니다.
author: Juliako
ms.author: juliako
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 04/01/2021
ms.openlocfilehash: 82edf5b282f7b68a7d4d1d7909cfe653a65c175b
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746569"
---
# <a name="tutorial-live-video-with-computer-vision-for-spatial-analysis-preview"></a>자습서: Computer Vision과 함께 라이브 비디오를 사용하여 공간 분석(미리 보기)

이 자습서에서는 [Azure Cognitive Services의 공간 분석 AI 서비스를 위한 Computer Vision](../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md)과 함께 Azure Video Analyzer를 사용하여 (시뮬레이션된) IP 카메라의 라이브 비디오 피드를 분석하는 방법을 보여줍니다. 이 유추 서버를 통해 스트리밍 비디오를 분석하여 물리적 공간에서 사람과 움직임 간의 공간 관계를 이해하는 방법을 확인할 수 있습니다. 비디오 피드의 프레임은 이 추론 서버에 전송되고, 결과는 IoT Edge 허브로 전송되고, 일부 조건이 충족되면 비디오 클립이 녹화되고 Video Analyzer 계정에 비디오로 저장됩니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
>
> - 리소스 설정
> - 코드를 검사합니다.
> - 샘플 코드 실행
> - 이벤트 모니터링

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>추천 참고 자료

시작하기 전에 다음 문서를 참조하세요.

- [Video Analyzer 개요](overview.md)
- [Video Analyzer 용어](terminology.md)
- [파이프라인 개념](pipeline.md)
- [이벤트 기반 비디오 녹화](record-event-based-live-video.md)
- [자습서: IoT Edge 모듈 개발](../../iot-edge/tutorial-develop-for-linux.md)

## <a name="prerequisites"></a>필수 구성 요소

다음은 공간 분석 모듈을 Azure Video Analyzer 모듈에 연결하기 위한 필수 조건입니다.

- 개발 컴퓨터의 [Visual Studio Code](https://code.visualstudio.com/). [Azure IoT Tools 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)이 있는지 확인합니다.
  - 개발 컴퓨터가 연결된 네트워크에서 5671 포트를 통해 고급 메시지 큐 프로토콜을 허용하는지 확인합니다. 이렇게 설정하면 Azure IoT Tools에서 Azure IoT Hub와 통신할 수 있습니다.
- 공간 분석을 위한 [Azure Cognitive Service Computer Vision 컨테이너](../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md).
  이 컨테이너를 사용하려면 연결된 **API 키** 및 **엔드포인트 URI** 를 가져오기 위한 Computer Vision 리소스가 있어야 합니다. API 키는 Azure Portal의 Computer Vision 개요 및 키 페이지에서 확인할 수 있습니다. 컨테이너를 시작하려면 키와 엔드포인트가 필요합니다.

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

1. 공간 분석 컨테이너를 실행하려면 [NVIDIA Tesla T4 GPU](https://www.nvidia.com/data-center/tesla-t4/)가 있는 컴퓨팅 디바이스가 필요합니다. GPU 가속과 함께 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)를 사용하는 것이 좋습니다. 그러나 컨테이너는 호스트 컴퓨터에 [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/)가 설치된 다른 데스크톱 머신에서 실행됩니다.

   #### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 디바이스](#tab/azure-stack-edge)

   Azure Stack Edge는 HaaS(Hardware-as-a-Service) 솔루션이며 네트워크 데이터 전송 기능이 있는 AI 지원 에지 컴퓨팅 디바이스입니다. 자세한 준비 및 설정 지침은 [Azure Stack Edge 설명서](../../databox-online/azure-stack-edge-deploy-prep.md)를 참조하세요.

   #### <a name="desktop-machine"></a>[데스크톱 컴퓨터](#tab/desktop-machine)

   #### <a name="minimum-hardware-requirements"></a>최소 하드웨어 요구 사항

   - 4GB 시스템 RAM
   - 4GB GPU RAM
   - 8코어 CPU
   - NVIDIA Tesla T4 GPU 1개
   - 20GB의 HDD 공간

   #### <a name="recommended-hardware"></a>권장 하드웨어

   - 32GB 시스템 RAM
   - 16GB GPU RAM
   - 8코어 CPU
   - NVIDIA Tesla T4 GPU 2개
   - 50GB의 SSD 공간

   이 문서에서는 다음 소프트웨어 패키지를 다운로드하고 설치합니다. 호스트 컴퓨터에서 다음을 실행할 수 있어야 합니다(지침은 아래 참조).

   - [NVIDIA 그래프 드라이버](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) 및 [NVIDIA CUDA Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
   - [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)(다중 프로세스 서비스)에 대한 구성
   - [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) 및 [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker)
   - [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) 런타임

   #### <a name="azure-vm-with-gpu"></a>[GPU가 있는 Azure VM](#tab/virtual-machine)

   K80 GPU가 하나 있는 [NC 시리즈 VM](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 활용할 수 있습니다.

   1. VM에 연결하고 터미널에서 다음 명령을 입력합니다.

        `bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`
    
        Azure Video Analyzer 모듈은 권한이 없는 로컬 사용자 계정을 사용하여 에지 디바이스에서 실행됩니다. 또한 애플리케이션 구성 데이터를 저장하기 위해 특정 로컬 폴더가 필요합니다. 마지막으로, 이 방법 가이드에서는 분석을 위해 실시간으로 비디오 피드를 AVA 모듈에 릴레이하는 [RTSP 시뮬레이터](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)를 활용하고 있습니다. 이 시뮬레이터는 입력 디렉터리에서 입력으로 사용합니다. 
    
        위에서 사용된 준비 디바이스 스크립트는 이러한 작업을 자동화하므로 하나의 명령을 실행하고, 관련 입력과 구성 폴더, 비디오 입력 파일 및 권한을 가진 사용자 계정을 모두 원활하게 만들 수 있습니다. 명령이 성공적으로 완료되면 에지 디바이스에 생성된 다음 폴더가 표시됩니다. 
    
        * `/home/localedgeuser/samples`
        * `/home/localedgeuser/samples/input`
        * `/var/lib/videoanalyzer`
        * `/var/media`
    
        분석할 입력 파일 역할을 하는 /home/localedgeuser/samples/input 폴더에 있는 비디오 파일(*.mkv)을 확인합니다. 

1. [에지 디바이스 설정](../../cognitive-services/computer-vision/spatial-analysis-container.md#set-up-the-host-computer)

1. 다음으로, 다른 Azure 리소스를 배포합니다.

   [![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

   > [!NOTE]
   > 위의 단추는 NVIDIA GPU가 없는 기본 Virtual Machine을 만들고 사용합니다. ARM(Azure Resource Manager) 템플릿에서 기존 디바이스를 사용하라는 메시지가 표시되면 "기존 에지 디바이스 사용" 옵션을 사용하고 위 단계의 IoT Hub 및 디바이스 정보를 사용합니다.
   > :::image type="content" source="./media/spatial-analysis/use-existing-device.png" alt-text="기존 디바이스 사용":::

   [!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>개요

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/overview.png" alt-text="공간 분석 개요":::

이 다이어그램에서는 이 자습서의 신호 흐름을 보여 줍니다. [에지 모듈](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스팅하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](pipeline.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 `CognitiveServicesVisionProcessor` 노드로 보냅니다.

`CognitiveServicesVisionProcessor` 노드는 프록시 역할을 수행합니다. 비디오 프레임을 지정된 이미지 형식으로 변환합니다. 그런 다음 **공유 메모리** 를 통해 이미지를 gRPC 엔드포인트 뒤에서 AI 작업을 실행하는 다른 에지 모듈에 릴레이합니다. 이 예에서 에지 모듈은 공간 분석 모듈입니다. `CognitiveServicesVisionProcessor` 노드는 다음과 같은 두 가지 작업을 수행합니다.

- 결과를 수집하고 [IoT Hub 싱크](pipeline.md#iot-hub-message-sink) 노드에 이벤트를 게시합니다. 그런 다음, 노드에서 이러한 이벤트를 [IoT Edge Hub](../../iot-fundamentals/iot-glossary.md#iot-edge-hub)에 보냅니다.
- 또한 [신호 게이트 프로세서](pipeline.md#signal-gate-processor)를 사용하여 RTSP 원본에서 30초 비디오 클립을 캡처하여 비디오 파일로 저장합니다.

## <a name="create-the-computer-vision-resource"></a>Computer Vision 리소스 만들기

[Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) 또는 Azure CLI를 통해 Computer Vision 유형의 Azure 리소스를 만들어야 합니다. 컨테이너에 대한 액세스 요청을 승인하고 Azure 구독 ID를 등록한 후에 리소스를 만들 수 있습니다.  https://aka.ms/csgate 로 이동하여 사용 사례와 Azure 구독 ID를 제출합니다. 액세스 양식 요청에서 제공된 것과 동일한 Azure 구독을 사용하여 Azure 리소스를 만들어야 합니다.

### <a name="gathering-required-parameters"></a>필수 매개 변수 수집

공간 분석 컨테이너를 포함하여 필요한 모든 Cognitive Services의 컨테이너에 대한 세 가지 기본 매개 변수가 있습니다. EULA(최종 사용자 사용권 계약)는 수락 값과 함께 제공되어야 합니다. 또한 엔드포인트 URI와 API 키가 모두 필요합니다.

### <a name="keys-and-endpoint-uri"></a>키 및 엔드포인트 URI

키는 공간 분석 컨테이너를 시작하는 데 사용되며 해당하는 Cognitive Service 리소스의 Azure Portal `Keys and Endpoint` 페이지에서 확인할 수 있습니다. 이 페이지로 이동하여 키와 엔드포인트 URI를 찾습니다.  

spatialanalysis 컨테이너를 배포하려면 배포 매니페스트 파일에 이 키와 엔드포인트 URI가 필요합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/keys-endpoint.png" alt-text="엔드포인트 URI":::

## <a name="set-up-your-development-environment"></a>개발 환경 설정

1. [https://github.com/Azure-Samples/azure-video-analyzer-iot-edge-csharp](https://github.com/Azure-Samples/azure-video-analyzer-iot-edge-csharp)에서 리포지토리를 복제합니다.
1. Visual Studio Code에서 리포지토리를 다운로드한 폴더를 엽니다.
1. Visual Studio Code에서 src/cloud-to-device-console-app으로 이동합니다. 여기에서 파일을 만들고 이름을 *appsettings.json* 으로 지정합니다. 이 파일에는 프로그램을 실행하는 데 필요한 설정이 포함됩니다.
1. Azure Portal에서 appsettings.json 파일의 내용을 복사합니다. 텍스트는 다음 코드와 비슷합니다.
   ```json
   {
     "IoThubConnectionString": "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
     "deviceId": "<your Edge Device name>",
     "moduleId": "avaedge"
   }
   ```

1. src/edge 폴더로 이동하고, .env라는 파일을 만듭니다.
1. Azure Portal에서 env.txt 파일의 내용을 복사합니다. 텍스트는 다음 코드와 비슷합니다.

   ```env
   SUBSCRIPTION_ID="<Subscription ID>"
   RESOURCE_GROUP="<Resource Group>"
   AVA_PROVISIONING_TOKEN="<The provisioning token>"
   VIDEO_INPUT_FOLDER_ON_DEVICE="/home/localedgeuser/samples/input"
   VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
   APPDATA_FOLDER_ON_DEVICE="/var/lib/videoanalyzer"
   CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
   CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"
   ```

## <a name="set-up-deployment-template"></a>배포 템플릿 설정

/src/edge/deployment.spatialAnalysis.template.json에서 배포 파일을 찾습니다. 템플릿에는 avaedge 모듈, rtspsim 모듈 및 spatialanalysis 모듈이 있습니다.

배포 템플릿 파일에서 주의해야 할 몇 가지 사항이 있습니다.

1. `spatialanalysis` 모듈에서 포트 바인딩을 설정합니다.

   ```
   "PortBindings": {
       "50051/tcp": [
           {
               "HostPort": "50051"
           }
       ]
   }
   ```

1. `avaedge`의 `IpcMode`와 `spatialanalysis` 모듈 createOptions는 동일해야 하며 **host** 로 설정되어야 합니다.
1. RTSP 시뮬레이터가 작동하려면 Azure Stack Edge 디바이스를 사용할 때 볼륨 범위를 설정해야 합니다.

   1. [SMB 공유에 연결](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share)하고 [샘플 stairwell 비디오 파일](https://lvamedia.blob.core.windows.net/public/2018-03-05.10-27-03.10-30-01.admin.G329.mkv)을 로컬 공유에 복사합니다.

      > [!VIDEO https://www.microsoft.com/videoplayer/embed/RWDRJd]

   1. rtspsim 모듈이 다음과 같이 구성되었는지 확인합니다.
      ```
      "createOptions": {
                          "HostConfig": {
                            "Mounts": [
                              {
                                "Target": "/live/mediaServer/media",
                                "Source": "<your Local Docker Volume Mount name>",
                                "Type": "volume"
                              }
                            ],
                            "PortBindings": {
                              "554/tcp": [
                                {
                                  "HostPort": "554"
                                }
                              ]
                            }
                          }
                        }
      ```
## <a name="generate-and-deploy-the-deployment-manifest"></a>배포 매니페스트 생성 및 배포

배포 매니페스트는 에지 디바이스에 배포되는 모듈을 정의합니다. 또한 해당 모듈에 대한 구성 설정을 정의합니다.

다음 단계에 따라 템플릿 파일에서 매니페스트를 생성한 다음, 에지 디바이스에 배포합니다.

1. Visual Studio Code를 엽니다.
1. `AZURE IOT HUB` 창 옆에 있는 [추가 작업] 아이콘을 선택하여 IoT Hub 연결 문자열을 설정합니다. `src/cloud-to-device-console-app/appsettings.json` 파일에서 문자열을 복사할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/set-connection-string.png" alt-text="공간 분석: 연결 문자열":::

1. `src/edge/deployment.spatialAnalysis.template.json`을 마우스 오른쪽 단추로 클릭하고 [IoT Edge 배포 매니페스트 생성]을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/generate-deployment-manifest.png" alt-text="공간 분석: 배포 amd64 json":::

   이렇게 하면 src/edge/config 폴더에 `deployment.spatialAnalysis.amd64.json`이라는 매니페스트 파일이 생성됩니다.

1. 마우스 오른쪽 단추로 `src/edge/config/deployment.spatialAnalysis.amd64.json` 파일을 클릭하고 **단일 디바이스용 배포 만들기** 를 선택한 다음, 에지 디바이스의 이름을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/deployment-single-device.png" alt-text="공간 분석: 단일 디바이스에 배포":::

1. 페이지 맨 위에 IoT Hub 디바이스를 선택하라는 메시지가 표시됩니다. 드롭다운 메뉴에서 에지 디바이스 이름을 선택합니다.
1. 약 30초 후 창의 왼쪽 아래 모서리에서 **AZURE IOT HUB** 창을 새로 고칩니다. 이제 에지 디바이스에 다음과 같은 배포된 모듈이 표시됩니다.

   - Azure Video Analyzer(모듈 이름 **avaedge**)
   - RTSP(Real-Time Streaming Protocol) 시뮬레이터(모듈 이름 **rtspsim**)
   - 공간 분석(모듈 이름 **spatialanalysis**)

배포가 성공하면 다음과 같은 메시지가 출력 창에 표시됩니다.

```
[Edge] Start deployment to device [<edge device name>]
[Edge] Deployment succeeded.
```

그런 다음, 디바이스/모듈에서 `avaedge`, `spatialanalysis` 및 `rtspsim` 모듈을 찾을 수 있으며 모듈의 상태는 "**실행 중**"입니다.

## <a name="prepare-to-monitor-events"></a>이벤트 모니터링 준비

이러한 이벤트를 보려면 다음 단계를 수행합니다.

1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누름) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정** 을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text="확장 설정":::

1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text="자세한 정보 메시지 표시":::

1. 탐색기 창을 열고 왼쪽 아래 모서리에서 **AZURE IOT HUB** 를 찾아 마우스 오른쪽 단추로 클릭한 다음, [기본 제공 이벤트 엔드포인트 모니터링 시작]을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/start-monitoring.png" alt-text="공간 분석: 모니터링 시작":::

## <a name="run-the-program"></a>프로그램 실행

`src/cloud-to-device-console-app/operations.json` 파일에는 직접 메서드를 호출하는 program.cs가 있습니다. operations.json을 설정하고 파이프라인 사용에 대한 pipelineTopology를 제공해야 합니다.

operations.json:

- 다음과 같이 pipelineTopology를 설정합니다.

  ```json
  {
      "opName": "pipelineTopologySet",
      "opParams": {
          "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json"
      }
  },
  ```

- 다음과 같이 livePipeline을 만들고 pipelineTopology에서 매개 변수를 설정합니다.

  ```json
  {
      "opName": "livePipelineSet",
      "opParams": {
          "name": "Sample-Pipeline-1",
          "properties": {
              "topologyName": "InferencingWithCVExtension",
              "description": "Sample pipeline description",
              "parameters": [
                  {
                      "name": "rtspUrl",
                      "value": " rtsp://rtspsim:554/media/2018-03-05.10-27-03.10-30-01.admin.G329.mkv"
                  },
                  {
                      "name": "rtspUserName",
                      "value": "testuser"
                  },
                  {
                      "name": "rtspPassword",
                      "value": "testpassword"
                  }
              ]
          }
      }
  },
  ```

  > [!Note]
  > `CognitiveServicesVisionExtension`을 사용하여 공간 분석 모듈에 연결하는지 확인합니다. ${grpcUrl}을 **tcp://spatialAnalysis:<PORT_NUMBER>** (예: tcp://spatialAnalysis:50051)로 설정합니다.

  ```json
  {
          "@type": "#Microsoft.VideoAnalyzer.CognitiveServicesVisionProcessor",
          "name": "computerVisionExtension",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${grpcUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${spatialanalysisusername}",
              "password": "${spatialanalysispassword}"
            }
          },
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value": "video"
                }
              ]
            }
          ],
          "operation": {
            "@type": "#Microsoft.VideoAnalyzer.SpatialAnalysisPersonCountOperation",
            "zones": [
              {
                "zone": {
                  "@type": "#Microsoft.VideoAnalyzer.NamedPolygonString",
                  "polygon": "[[0.37,0.43],[0.48,0.42],[0.53,0.56],[0.34,0.57],[0.34,0.46]]",
                  "name": "stairlanding"
                },
                "events": [
                  {
                    "trigger": "event",
                    "outputFrequency": "1",
                    "threshold": "16",
                    "focus": "bottomCenter"
                  }
                ]
              }
            ]
          }
        }
      ],
  ```

F5를 선택하여 디버그 세션을 실행하고 **TERMINAL** 지침에 따라 pipelineTopology를 설정하고, livePipeline을 설정하고, livePipeline을 활성화하고, 마지막으로 리소스를 삭제합니다.

## <a name="interpret-results"></a>결과 해석

pipelineTopology가 인스턴스화되면 "MediaSessionEstablished" 이벤트(여기서는 [샘플 MediaSessionEstablished 이벤트](detect-motion-emit-events-quickstart.md#mediasessionestablished-event))가 표시됩니다.

또한 공간 분석 모듈은 AI 인사이트 이벤트를 Azure Video Analyzer에 전송한 후 IoT Hub에 전송하며, **OUTPUT** 에도 표시됩니다. ENTITY는 검색 개체이고 EVENT는 spaceanalytics 이벤트입니다. 이 출력은 Azure Video Analyzer로 전달됩니다.

(`SpatialAnalysisPersonZoneCrossingOperation` 작업의) personZoneEvent에 대한 샘플 출력

```
{
  "timestamp": 145666725597833,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "8724dff43d3c4716936aa6c9a808ee2e",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9980469
        },
        "box": {
          "l": 0.3848941,
          "t": 0.28569314,
          "w": 0.092775516,
          "h": 0.3819766
        }
      },
      "extensions": {
        "footprintX": "inf",
        "centerGroundPointY": "0.0",
        "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
        "footprintY": "inf",
        "centerGroundPointX": "0.0"
      }
    },
    {
      "type": "event",
      "inferenceId": "0b0de3d228b640488fc0624950a6c9e8",
      "relatedInferences": [
        "8724dff43d3c4716936aa6c9a808ee2e"
      ],
      "event": {
        "name": "personZoneEnterExitEvent",
        "properties": {
          "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
          "status": "Enter",
          "zone": "door"
        }
      }
    }
  ]
}
```

## <a name="operations"></a>작업:

### <a name="person-zone-crossing"></a>사람 영역 교차

#### <a name="parameters"></a>매개 변수

| 이름                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 영역                     | list    | 영역 목록입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| name                      | 문자열  | 이 영역에 대한 식별 이름입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| polygon                   | 문자열  | 각 값 쌍은 다각형의 꼭짓점에 대한 x,y를 나타냅니다. 다각형은 사람이 추적되거나 계산되는 영역을 나타냅니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다. threshold float 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. 기본값은 type이 zonecrossing인 경우 48이고, time이 DwellTime인 경우 16입니다. 이러한 값은 최대 정확도를 얻기 위해 권장되는 값입니다. |
| eventType                 | 문자열  | cognitiveservices.vision.spatialanalysis-personcrossingpolygon의 경우 zonecrossing 또는 zonedwelltime이어야 합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 트리거                   | 문자열  | 이벤트를 보내는 트리거의 형식입니다. 지원되는 값: "event": 누군가가 영역에 들어오거나 나갈 때 발생합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| focus                     | 문자열  | 이벤트를 계산하는 데 사용되는 사람의 경계 상자 내의 지점 위치입니다. focus의 값은 footprint(사람의 공간), bottom_center(사람의 경계 상자의 아래쪽 가운데) 또는 center(사람의 경계 상자의 중심)입니다. 기본값은 footprint입니다.                                                                                                                                                                                                                                                                                               |
| enableFaceMaskClassifier  | boolean | 비디오 스트림에서 얼굴 마스크를 착용한 사람을 감지하려면 true, 감지하지 않으려면 false로 설정합니다. 기본적으로 사용하지 않도록 설정됩니다. 얼굴 마스크 감지를 사용하려면 입력 비디오 너비 매개 변수가 1920 "INPUT_VIDEO_WIDTH": 1920이어야 합니다. 얼굴 마스크 특성은 반환되지 않습니다.                                                                                                                                                                                                                                                                                                        |
| detectorNodeConfiguration | 문자열  | 모든 공간 분석 작업에 대한 DETECTOR_NODE_CONFIG 매개 변수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

#### <a name="output"></a>출력:

```json
{
  "timestamp": 145666725597833,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "8724dff43d3c4716936aa6c9a808ee2e",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9980469
        },
        "box": {
          "l": 0.3848941,
          "t": 0.28569314,
          "w": 0.092775516,
          "h": 0.3819766
        }
      },
      "extensions": {
        "footprintX": "inf",
        "centerGroundPointY": "0.0",
        "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
        "footprintY": "inf",
        "centerGroundPointX": "0.0"
      }
    },
    {
      "type": "event",
      "inferenceId": "0b0de3d228b640488fc0624950a6c9e8",
      "relatedInferences": ["8724dff43d3c4716936aa6c9a808ee2e"],
      "event": {
        "name": "personZoneEnterExitEvent",
        "properties": {
          "trackingId": "80eeb5bbb6f542b89b5b16a1e37c53c4",
          "status": "Enter",
          "zone": "door"
        }
      }
    }
  ]
}
```

### <a name="more-operations"></a>추가 작업:

<details>
  <summary>클릭하여 확장</summary>

### <a name="person-line-crossing"></a>사람 줄 교차

#### <a name="parameters"></a>매개 변수

| 이름                      | Type    | Description                                                                                                                                                                                                                                                                   |
| ------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| lines                     | list    | 선 목록입니다.                                                                                                                                                                                                                                                                |
| name                      | 문자열  | 이 선에 대한 식별 이름입니다.                                                                                                                                                                                                                                                  |
| line                      | 문자열  | 각 값 쌍은 선의 시작 지점과 끝 지점을 나타냅니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다.                            |
| outputFrequency           | int     | 이벤트가 송신되는 속도입니다. output_frequency = X이면 모든 X 이벤트가 송신됩니다. 예를 들어 output_frequency = 2는 다른 모든 이벤트가 출력됨을 의미합니다. output_frequency는 이벤트와 간격 모두에 적용됩니다.                                                       |
| focus                     | 문자열  | 이벤트를 계산하는 데 사용되는 사람의 경계 상자 내의 지점 위치입니다. focus의 값은 footprint(사람의 공간), bottom_center(사람의 경계 상자의 아래쪽 가운데) 또는 center(사람의 경계 상자의 중심)입니다. 기본값은 footprint입니다. |
| threshold                 | float   | 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. 기본값은 16입니다. 이는 최대 정확도를 얻기 위해 권장되는 값입니다.                                                                                                |
| enableFaceMaskClassifier  | boolean | 비디오 스트림에서 얼굴 마스크를 착용한 사람을 감지하려면 true, 감지하지 않으려면 false로 설정합니다. 기본적으로 사용하지 않도록 설정됩니다. 얼굴 마스크 감지를 사용하려면 입력 비디오 너비 매개 변수가 1920 "INPUT_VIDEO_WIDTH": 1920이어야 합니다. 얼굴 마스크 특성은 반환되지 않습니다.          |
| detectorNodeConfiguration | 문자열  | 모든 공간 분석 작업에 대한 DETECTOR_NODE_CONFIG 매개 변수입니다.                                                                                                                                                                                                      |

#### <a name="output"></a>출력:

```json
{
  "timestamp": 145666620394490,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "2d3c7c7d6c0f4af7916eb50944523bdf",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.38330078
        },
        "box": {
          "l": 0.5316645,
          "t": 0.28169397,
          "w": 0.045862257,
          "h": 0.1594377
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "trackingId": "ac4a79a29a67402ba447b7da95907453",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "2206088c80eb4990801f62c7050d142f",
      "relatedInferences": ["2d3c7c7d6c0f4af7916eb50944523bdf"],
      "event": {
        "name": "personLineEvent",
        "properties": {
          "trackingId": "ac4a79a29a67402ba447b7da95907453",
          "status": "CrossLeft",
          "zone": "door"
        }
      }
    }
  ]
}
```

### <a name="person-distance"></a>사람 거리

#### <a name="parameters"></a>매개 변수

| 이름                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 영역                     | list    | 영역 목록입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| name                      | 문자열  | 이 영역에 대한 식별 이름입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| polygon                   | 문자열  | 각 값 쌍은 다각형의 꼭짓점에 대한 x,y를 나타냅니다. 다각형은 사람이 추적되거나 계산되는 영역을 나타냅니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다. threshold float 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. 기본값은 type이 zonecrossing인 경우 48이고, time이 DwellTime인 경우 16입니다. 이러한 값은 최대 정확도를 얻기 위해 권장되는 값입니다. |
| outputFrequency           | int     | 이벤트가 송신되는 속도입니다. output_frequency = X이면 모든 X 이벤트가 송신됩니다. 예를 들어 output_frequency = 2는 다른 모든 이벤트가 출력됨을 의미합니다. output_frequency는 이벤트와 간격 모두에 적용됩니다.                                                                                                                                                                                                                                                                                                                                                     |
| focus                     | 문자열  | 이벤트를 계산하는 데 사용되는 사람의 경계 상자 내의 지점 위치입니다. focus의 값은 footprint(사람의 공간), bottom_center(사람의 경계 상자의 아래쪽 가운데) 또는 center(사람의 경계 상자의 중심)입니다. 기본값은 footprint입니다.                                                                                                                                                                                                                                                                                               |
| threshold                 | float   | 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| outputFrequency           | int     | 이벤트가 송신되는 속도입니다. output_frequency = X이면 모든 X 이벤트가 송신됩니다. 예를 들어 output_frequency = 2는 다른 모든 이벤트가 출력됨을 의미합니다. output_frequency는 이벤트와 간격 모두에 적용됩니다.                                                                                                                                                                                                                                                                                                                                                     |
| minimumDistanceThreshold  | float   | 사람이 이 거리보다 덜 떨어져 있을 때 "TooClose" 이벤트를 트리거하는 거리(피트)입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| maximumDistanceThreshold  | float   | 사람이 이 거리보다 더 떨어져 있을 때 "TooFar"이벤트를 트리거하는 거리(피트)입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| aggregationMethod         | 문자열  | 집계된 persondistance 결과에 대한 메서드입니다. aggregationMethod는 모드와 평균에 모두 적용됩니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| enableFaceMaskClassifier  | boolean | 비디오 스트림에서 얼굴 마스크를 착용한 사람을 감지하려면 true, 감지하지 않으려면 false로 설정합니다. 기본적으로 사용하지 않도록 설정됩니다. 얼굴 마스크 감지를 사용하려면 입력 비디오 너비 매개 변수가 1920 "INPUT_VIDEO_WIDTH": 1920이어야 합니다. 얼굴 마스크 특성은 반환되지 않습니다.                                                                                                                                                                                                                                                                                                        |
| detectorNodeConfiguration | 문자열  | 모든 공간 분석 작업에 대한 DETECTOR_NODE_CONFIG 매개 변수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

#### <a name="output"></a>출력:

```json
{
  "timestamp": 145666613610297,
  "inferences": [
    {
      "type": "event",
      "inferenceId": "85a5fc4936294a3bac90b9c43876741a",
      "event": {
        "name": "personDistanceEvent",
        "properties": {
          "maximumDistanceThreshold": "14.5",
          "personCount": "0.0",
          "eventName": "Unknown",
          "zone": "door",
          "averageDistance": "0.0",
          "minimumDistanceThreshold": "1.5",
          "distanceViolationPersonCount": "0.0"
        }
      }
    }
  ]
}
```

### <a name="person-count"></a>사람 수

#### <a name="parameters"></a>매개 변수

| 이름                      | Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 영역                     | list    | 영역 목록입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| name                      | 문자열  | 이 영역에 대한 식별 이름입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| polygon                   | 문자열  | 각 값 쌍은 다각형의 꼭짓점에 대한 x,y를 나타냅니다. 다각형은 사람이 추적되거나 계산되는 영역을 나타냅니다. 부동 소수점 값은 왼쪽 위 모서리를 기준으로 하는 꼭짓점의 위치를 나타냅니다. 절대 x, y 값을 계산하려면 이러한 값과 프레임 크기를 곱해야 합니다. threshold float 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다. 기본값은 type이 zonecrossing인 경우 48이고, time이 DwellTime인 경우 16입니다. 이러한 값은 최대 정확도를 얻기 위해 권장되는 값입니다. |
| outputFrequency           | int     | 이벤트가 송신되는 속도입니다. output_frequency = X이면 모든 X 이벤트가 송신됩니다. 예를 들어 output_frequency = 2는 다른 모든 이벤트가 출력됨을 의미합니다. output_frequency는 이벤트와 간격 모두에 적용됩니다.                                                                                                                                                                                                                                                                                                                                                     |
| 트리거                   | 문자열  | 이벤트를 보내는 트리거의 형식입니다. 지원되는 값은 개수가 변경되면 이벤트를 보내는 event 또는 개수가 변경되었는지 여부에 관계없이 정기적으로 이벤트를 보내는 interval입니다.                                                                                                                                                                                                                                                                                                                                                           |
| focus                     | 문자열  | 이벤트를 계산하는 데 사용되는 사람의 경계 상자 내의 지점 위치입니다. focus의 값은 footprint(사람의 공간), bottom_center(사람의 경계 상자의 아래쪽 가운데) 또는 center(사람의 경계 상자의 중심)입니다. 기본값은 footprint입니다.                                                                                                                                                                                                                                                                                               |
| threshold                 | float   | 이벤트는 사람이 영역 내의 이 픽셀 수보다 클 때 송신됩니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| enableFaceMaskClassifier  | boolean | 비디오 스트림에서 얼굴 마스크를 착용한 사람을 감지하려면 true, 감지하지 않으려면 false로 설정합니다. 기본적으로 사용하지 않도록 설정됩니다. 얼굴 마스크 감지를 사용하려면 입력 비디오 너비 매개 변수가 1920 "INPUT_VIDEO_WIDTH": 1920이어야 합니다. 얼굴 마스크 특성은 반환되지 않습니다.                                                                                                                                                                                                                                                                                                        |
| detectorNodeConfiguration | 문자열  | 모든 공간 분석 작업에 대한 DETECTOR_NODE_CONFIG 매개 변수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

#### <a name="output"></a>출력:

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

### <a name="custom"></a>사용자 지정

#### <a name="parameters"></a>매개 변수

| 이름                   | Type   | Description                           |
| ---------------------- | ------ | ------------------------------------- |
| extensionConfiguration | 문자열 | 작업의 JSON 표현입니다. |

#### <a name="output"></a>출력:

```json
{
  "timestamp": 145666599533564,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "5b8076753b8c47bba8c72a7e0f7c5cc0",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.9458008
        },
        "box": {
          "l": 0.474487,
          "t": 0.26522297,
          "w": 0.066929355,
          "h": 0.2828749
        }
      },
      "extensions": {
        "centerGroundPointX": "0.0",
        "centerGroundPointY": "0.0",
        "footprintX": "inf",
        "footprintY": "inf"
      }
    },
    {
      "type": "event",
      "inferenceId": "fb309c9285f94f268378540b5fbbf5ad",
      "relatedInferences": ["5b8076753b8c47bba8c72a7e0f7c5cc0"],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone": "demo"
        }
      }
    }
  ]
}
```

</details>

## <a name="playing-back-the-recording"></a>녹화본 재생

Azure Portal에 로그인하여 비디오를 살펴보면 라이브 파이프라인에서 만든 Video Analyzer 비디오 리소스를 검사할 수 있습니다.

1. 웹 브라우저를 열고 [Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.
1. 구독에 있는 리소스 중에서 Video Analyzer 계정을 찾아 계정 창을 엽니다.
1. **비디오 분석기** 목록에서 **비디오** 를 선택합니다.
1. 이름이 `personcount`인 비디오가 나열됩니다. 이 이름은 파이프라인 토폴로지 파일에서 선택한 이름입니다.
1. 비디오를 선택합니다.
1. 비디오 세부 정보 페이지에서 **재생** 아이콘을 클릭합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback.png" alt-text="비디오 재생 스크린샷":::
   
1. 추론 메타데이터를 비디오에서 경계 상자로 보려면 **경계 상자** 아이콘을 클릭합니다.
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/bounding-box.png" alt-text="경계 상자 아이콘":::

> [!NOTE]
> 비디오 원본은 카메라 피드를 시뮬레이션하는 컨테이너이므로 비디오의 타임스탬프는 라이브 파이프라인을 활성화한 시간 및 비활성화한 시간과 관련이 있습니다.

## <a name="troubleshooting"></a>문제 해결

spatialanalysis는 큰 컨테이너이며 시작하는 데 최대 30초가 걸릴 수 있습니다. 가동이 시작된 spatialanalysis 컨테이너는 추론 이벤트를 보내기 시작합니다.

```JSON
[IoTHubMonitor] [3:37:28 PM] Message received from [ase03-edge/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620671848135494 1 IN IP4 172.27.86.122\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafeteria.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.066\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/cafeteria.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCHoQAAAMABAAAAwDwPGDGWA==,aOvssiw=\r\na=control:track1\r\n"
}
[IoTHubMonitor] [3:37:30 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/customoperation-05102021",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}
[IoTHubMonitor] [3:37:40 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:37:50 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:38:18 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/customoperation-05102021",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}
[IoTHubMonitor] [3:38:42 PM] Message received from [ase03-edge/avaedge]:
{
  "timestamp": 145860472980260,
  "inferences": [
    {
      "type": "entity",
      "inferenceId": "647aacf9d8bc47078a1ed31d1c459c24",
      "entity": {
        "tag": {
          "value": "person",
          "confidence": 0.7583008
        },
        "box": {
          "l": 0.48213565,
          "t": 0.21217245,
          "w": 0.056364775,
          "h": 0.29961595
        }
      },
      "extensions": {
        "centerGroundPointY": "0.0",
        "centerGroundPointX": "0.0",
        "footprintX": "0.5087100982666015",
        "footprintY&quot;: &quot;0.49634415356080924"
      }
    },
    {
      "type": "event",
      "inferenceId": "dae6c2b742634196b615c128654845dc",
      "relatedInferences": [
        "647aacf9d8bc47078a1ed31d1c459c24"
      ],
      "event": {
        "name": "personCountEvent",
        "properties": {
          "personCount": "1.0",
          "zone&quot;: &quot;stairlanding"
        }
      }
    }
  ]
}

```

> [!NOTE]
> **"초기화 중"** 메시지가 표시될 수 있습니다. 이 메시지는 spatialAnalysis 모듈이 시작되는 동안 표시되며 실행 중 상태로 전환될 때까지 최대 60초가 걸릴 수 있습니다. 잠시 기다리면 추론 이벤트 흐름이 표시됩니다.

## <a name="next-steps"></a>다음 단계

`spatialAnalysis` 모듈에서 제공하는 다른 작업을 시도해 봅니다. 다음 pipelineTopologies를 참조하세요.

- [personCount](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json)
- [personDistance](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-pperation-topology.json)
- [personCrossingLine](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json)
- [personZoneCrossing](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json)
- [customOperation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json)

> [!Tip]
> 프레임에 두 명 이상이 있는 **[샘플 비디오 파일](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv)** 을 사용합니다.
