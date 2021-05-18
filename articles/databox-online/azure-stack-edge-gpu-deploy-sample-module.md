---
title: Azure Stack Edge Pro GPU 디바이스에 GPU 모듈 배포 | Microsoft Docs
description: 컴퓨팅을 사용하도록 설정하고 로컬 UI를 통해 Azure Stack Edge Pro 디바이스를 컴퓨팅 준비 상태로 만드는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: af44912edf3ce98ceb71bd34388543f7652c2181
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568463"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에 GPU 지원 IoT 모듈 배포

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 디바이스에 GPU 지원 IoT Edge 모듈을 배포하는 방법을 설명합니다. 

이 문서에서는 다음 방법을 설명합니다.
  - GPU 모듈을 실행하도록 Azure Stack Edge Pro를 준비합니다.
  - Git 리포지토리에서 샘플 코드를 다운로드하고 설치합니다.
  - 솔루션을 빌드하고 배포 매니페스트를 생성합니다.
  - Azure Stack Edge Pro 디바이스에 솔루션을 배포합니다.
  - 모듈 출력 모니터링


## <a name="about-sample-module"></a>샘플 모듈 정보

이 기사의 GPU 샘플 모듈에는 GPU에 대한 CPU용 PyTorch 및 TensorFlow 벤치마킹 샘플 코드가 포함되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음을 확인합니다.

- GPU 지원 1-노드 Azure Stack Edge Pro 디바이스에 액세스할 수 있습니다. 이 디바이스는 Azure의 리소스를 사용하여 활성화됩니다. [디바이스 활성화](azure-stack-edge-gpu-deploy-activate.md)를 참조하세요.
- 이 디바이스에서 컴퓨팅을 구성했습니다. [자습서: Azure Stack Edge Pro 디바이스에서 컴퓨팅 구성](azure-stack-edge-gpu-deploy-configure-compute.md)의 단계를 따릅니다.
- ACR(Azure Container Registry). **액세스 키** 블레이드로 이동하여 ACR 로그인 서버, 사용자 이름 및 암호를 기록해 둡니다. 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 프라이빗 컨테이너 레지스트리 만들기](../container-registry/container-registry-get-started-portal.md#create-a-container-registry)로 이동합니다.
- Windows 클라이언트의 다음 개발 리소스:
    - [Azure CLI 2.0 이상](https://aka.ms/installazurecliwindows)
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). 소프트웨어를 다운로드 및 설치할 계정을 만들어야 할 수 있습니다.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Visual Studio Code용 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)    
    - [Visual Studio Code용 Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3](https://www.python.org/)    
    - Python 패키지 설치를 위한 Pip(일반적으로 Python 설치와 함께 포함됨)

## <a name="get-the-sample-code"></a>샘플 코드 가져오기

1. [Azure의 Azure 인텔리전트 에지 패턴 샘플](https://github.com/azure-samples/azure-intelligent-edge-patterns)로 이동합니다. 코드용 zip 파일을 복제하거나 다운로드합니다. 

    ![Zip 파일 다운로드](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    zip에서 파일을 추출합니다. 샘플을 복제할 수도 있습니다.

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>모듈 빌드 및 배포

1. Visual Studio Code에서 **GpuReferenceModules** 폴더를 엽니다.

    ![VS Code에서 GPUReferenceModules 열기](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. *deployment.template.json* 을 열고 컨테이너 레지스트리에 대해 참조하는 매개 변수를 식별합니다. 다음 파일에서는 CONTAINER_REGISTRY_USERNAME, CONTAINER_REGISTRY_PASSWORD 및 CONTAINER_REGISTRY_NAME이 사용됩니다.

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. 새 파일을 만듭니다. 다음과 같이 컨테이너 레지스트리 매개 변수의 값을 입력합니다(이전 단계에서 식별된 매개 변수 사용). 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    샘플 *.env* 파일은 다음과 같습니다.
    
    ![.env 파일 만들기 및 저장](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. 파일을 **SampleSolution** 폴더에 *.env* 로 저장합니다.

5. Docker에 로그인하려면 Visual Studio Code 통합 터미널에 다음 명령을 입력합니다. 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    Azure Portal에서 컨테이너 레지스트리의 **액세스 키** 섹션으로 이동합니다. 레지스트리 이름, 암호 및 로그인 서버를 복사하여 사용합니다.

    ![컨테이너 레지스트리의 액세스 키](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    자격 증명이 제공되면 로그인이 성공합니다.

    ![로그인 성공](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. Azure Container Registry로 이미지를 푸시합니다. VS Code 탐색기에서 **deployment.template.json** 파일을 선택하여 마우스 오른쪽 단추로 클릭한 다음 **IoT Edge 솔루션 빌드 및 푸시** 를 선택합니다. 

    ![IoT Edge 솔루션 빌드 및 푸시](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    Python 및 Python 확장이 설치되지 않은 경우 솔루션을 빌드하고 푸시할 때 설치됩니다. 그러나 이렇게 하면 빌드 시간이 길어집니다. 

    이 단계가 완료되면 컨테이너 레지스트리에 모듈이 표시됩니다.

    ![컨테이너 레지스트리의 모듈](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. 배포 매니페스트를 만들려면 **deployment.template.json** 을 마우스 오른쪽 단추로 클릭한 다음 **IoT Edge 배포 매니페스트 생성** 을 선택합니다. 

    ![IoT Edge 배포 매니페스트 생성](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    알림은 배포 매니페스트가 생성된 경로를 알려줍니다. 매니페스트는 **config** 폴더에 생성된 `deployment.amd64.json` 파일입니다. 

8. **config** 폴더에서 **deployment.amd64.json** 파일을 선택한 다음 **단일 디바이스용 배포 만들기** 를 선택합니다. **deployment.template.json** 파일을 사용하지 마세요. 

    ![단일 디바이스용 배포 만들기](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    **출력** 창에 배포가 성공했다는 메시지가 표시되어야 합니다.

    ![출력에서 배포 성공](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>모듈 모니터링  

1. VS Code 명령 팔레트에서 **Azure IoT Hub: IoT Hub 선택** 을 실행합니다.

2. 구성하려는 IoT Edge 디바이스가 포함된 구독 및 IoT Hub를 선택합니다. 이 경우 Azure Stack Edge Pro 디바이스를 배포하는 데 사용되는 구독을 선택하고 Azure Stack Edge Pro 디바이스용으로 만든 IoT Edge 디바이스를 선택합니다. 이는 이전 단계에서 Azure Portal을 통해 컴퓨팅을 구성할 때 발생합니다.

3. VS Code 탐색기에서 Azure IoT Hub 섹션을 확장합니다. **디바이스** 아래에 Azure Stack Edge Pro 디바이스에 해당하는 IoT Edge 디바이스가 표시되어야 합니다. 

    1. 해당 디바이스를 선택하고 마우스 오른쪽 단추를 클릭한 다음 **내장 이벤트 엔드포인트 모니터링 시작** 을 선택합니다.
  
        ![모니터링 시작](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. **디바이스 > 모듈** 로 이동하면 **GPU 모듈** 이 실행 중인 것을 확인할 수 있습니다.

        ![IoT Hub의 모듈](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. VS Code 터미널은 또한 IoT Hub 이벤트를 Azure Stack Edge Pro 디바이스에 대한 모니터링 출력으로 표시해야 합니다.

        ![모니터링 출력](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        GPU에서 동일한 작업 세트(모양 변환 5,000회 반복)를 실행하는 데 걸리는 시간이 CPU보다 훨씬 적다는 것을 알 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [모듈을 사용하도록 GPU를 구성](./azure-stack-edge-gpu-configure-gpu-modules.md)하는 방법에 대해 자세히 알아봅니다.