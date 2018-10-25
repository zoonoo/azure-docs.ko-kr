---
title: Azure Machine Learning 서비스에서 IoT Edge 장치로 모델 배포 | Microsoft Docs
description: Azure Machine Learning 서비스에서 Azure IoT Edge 장치로 모델을 배포하는 방법을 알아봅니다. Edge 장치로 모델을 배포하면 클라우드로 보내고 응답을 기다리는 대신 장치에서 데이터 점수를 매길 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shipatel
author: shivanipatel
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 7d706cf71761496fd740c729224ee4331eeb2911
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091626"
---
# <a name="prepare-to-deploy-models-on-iot-edge"></a>IoT Edge에서 모델 배포 준비

이 문서에서는 Azure Machine Learning 서비스를 사용하여 Azure IoT Edge 장치에 학습된 모델을 배포하기 위해 준비하는 방법을 알아봅니다.

Azure IoT Edge 장치는 Azure IoT Edge 런타임을 실행하는 Linux 또는 Windows 기반 장치입니다. 이러한 장치에 Machine Learning 모델을 IoT Edge 모듈로 배포할 수 있습니다. IoT Edge 장치에 모델을 배포하면 처리를 위해 클라우드로 데이터를 전송하는 대신 장치에서 직접 모델을 사용할 수 있습니다. 응답 시간은 더 빨라지고 데이터 전송은 더 적어집니다.

모델을 Edge 장치에 배포하기 전에 이 문서의 단계에 따라 모델 및 장치를 준비합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

* Azure Machine Learning 서비스 작업 영역. 이를 만들려면 [Azure Machine Learning 서비스 시작](quickstart-get-started.md) 문서에 나와 있는 단계를 따릅니다.

* 개발 환경. 자세한 내용은 [개발 환경을 구성하는 방법](how-to-configure-environment.md) 문서를 참조하세요.

* Azure 구독의 [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md). 

* 학습된 모델. 모델 학습에 관한 예제는 [Azure Machine Learning을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 문서를 참조하세요. 미리 학습된 모델은 [Azure IoT Edge GitHub 리포지토리에 대한 AI 도구 키트](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)에서 사용할 수 있습니다.

## <a name="prepare-the-iot-device"></a>IoT 장치 준비

장치를 등록하고 IoT 런타임을 설치하는 방법을 알아보려면 [빠른 시작: Linux x64 장치에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart-linux.md) 문서에 나온 단계를 수행합니다.

## <a name="register-the-model"></a>모델 등록

Azure IoT Edge 모듈은 컨테이너 이미지를 기반으로 합니다. 모델을 IoT Edge 장치에 배포하려면 Azure Machine Learning 서비스 작업 영역에서 모델을 등록하고 Docker 이미지를 만드는 다음 단계를 사용합니다. 

1. 작업 영역을 초기화하고 config.json 파일을 로드합니다.

    ```python
    from azureml.core  import Workspace

    #Load existing workspace from the config file info.
    ws  = Workspace.from_config()
    ```    

1. 작업 영역에 모델을 등록합니다. 기본 텍스트를 모델 경로, 이름, 태그 및 설명으로 바꿉니다.

    > [!IMPORTANT]
    > Azure Machine Learning을 사용하여 모델을 학습한 경우 모델이 작업 영역에 이미 등록되어 있을 수 있습니다. 이러한 경우 이 단계를 건너뜁니다. 이 작업 영역에 등록된 모델의 목록을 보려면 `Model.list(ws)`를 사용합니다.

    ```python
    from azureml.core.model import Model
    model = Model.register(model_path = "model.pkl", # this path points to the local file
                        model_name = "best_model", # the model gets registered as this name
                        tags = {'attribute': "myattribute", 'classification': "myclassification"},
                        description = "My awesome model",
                        workspace = ws)
    ```    

1. 등록된 모델을 검색합니다. 

    ```python
    from azureml.core.model import Model

    model_name = "best_model"
    model = Model(ws, model_name)                     
    ```    

## <a name="create-a-docker-image"></a>Docker 이미지 만들기

1. `score.py`라는 **점수 매기기 스크립트**를 만듭니다. 이 파일은 이미지 내에서 모델을 실행하는 데 사용됩니다. 다음 함수를 포함해야 합니다.

    * `init()` 함수는 일반적으로 모델을 전역 개체에 로드합니다. 이 함수는 Docker 컨테이너를 시작할 때 한 번만 실행됩니다. 

    * `run(input_data)` 함수는 모델을 사용하여 입력 데이터를 기반으로 값을 예측합니다. 실행에 대한 입력 및 출력은 일반적으로 serialization 및 deserialization용으로 JSON을 사용하지만 다른 형식도 지원됩니다.

    예제는 [이미지 분류 자습서](tutorial-deploy-models-with-aml.md#make-script)를 참조하세요.

1. `myenv.yml`이라는 **환경 파일**을 만듭니다. 이 파일은 Conda 환경 사양이며 스크립트 및 모델에 필요한 종속성 모두 나열합니다. 예제는 [이미지 분류 자습서](tutorial-deploy-models-with-aml.md#make-myenv)를 참조하세요.

1. `score.py` 및 `myenv.yml` 파일을 사용하여 Docker 이미지를 구성합니다.
    
    ```python
    from azureml.core.image import Image, ContainerImage
    
    #Image configuration
    image_config = ContainerImage.image_configuration( runtime = "python", 
                           execution_script = "score.py",
                           conda_file = "myenv.yml", 
                           tags = {"attributes", "calssification"},
                           description = "Image that contains my model",
                           
                        )
    ```    

1. 모델 및 이미지 구성을 사용하여 이미지를 만듭니다.

    ```python
    image = ContainerImage.create (name = "myimage", 
                           models = [model], #this is the model object
                           image_config = image_config,
                           workspace = ws
                        )
    ```     

    이미지를 만드는 데는 약 5분이 걸립니다.

## <a name="get-the-container-registry-credentials"></a>컨테이너 레지스트리 자격 증명 가져오기

Azure IoT에는 Azure Machine Learning 서비스가 Docker 이미지를 저장하는 컨테이너 레지스트리에 대한 자격 증명이 필요합니다. 다음 단계를 사용하여 자격 증명을 가져옵니다.

1. [Azure Portal](https://portal.azure.com/signin/index)에 로그인합니다.

1. Azure Machine Learning 서비스 작업 영역으로 이동하고 __개요__를 선택합니다. 컨테이너 레지스트리 설정으로 이동하려면 __레지스트리__ 링크를 선택합니다.

    ![컨테이너 레지스트리 항목의 이미지](./media/how-to-deploy-to-iot/findregisteredcontainer.png)

1. 컨테이너 레지스트리에서 **액세스 키**를 선택한 다음, 관리 사용자를 활성화합니다.

    ![액세스 키 화면의 이미지](./media/how-to-deploy-to-iot/findaccesskey.png)

1. 로그인 서버, 사용자 이름 및 암호의 값을 저장합니다. 

   이러한 자격 증명은 개인 컨테이너 레지스트리에 있는 이미지에 대한 IoT Edge 장치 액세스를 제공하는 데 필요합니다.

## <a name="next-steps"></a>다음 단계

배포에 대한 준비를 완료했습니다. 이제 [Azure Portal에서 Azure IoT Edge 모듈 배포](../../iot-edge/how-to-deploy-modules-portal.md)의 단계를 사용하여 Edge 장치에 배포합니다. 장치에 대해 __레지스트리 설정__을 구성하는 경우 이전에 구성한 자격 증명을 사용합니다.
