---
title: '자습서: Visual Studio Code 확장 설정'
titleSuffix: Azure Machine Learning
description: Visual Studio Code Azure Machine Learning 확장을 설정하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 04/13/2020
ms.openlocfilehash: 731ab18346ac9f100862174312c2c9950026f1eb
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272923"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Azure Machine Learning Visual Studio Code 확장 설정

Azure Machine Learning Visual Studio Code 확장을 사용하여 스크립트를 설치하고 실행하는 방법에 대해 알아봅니다.

이 자습서에서는 다음 작업에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Machine Learning Visual Studio Code 확장 설치
> * Visual Studio Code에서 Azure 계정에 로그인
> * Azure Machine Learning 확장을 사용하여 샘플 스크립트 실행

## <a name="prerequisites"></a>사전 요구 사항

- 동작합니다. 구독이 없는 경우 [Azure Machine Learning의 평가판 또는 유료 버전](https://aka.ms/AMLFree)에 가입하여 사용해 보세요.
- Visual Studio Code. 없는 경우 [설치](https://code.visualstudio.com/docs/setup/setup-overview)합니다.
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>확장 설치

1. Visual Studio Code를 엽니다.
1. **작업 막대**에서 **확장** 아이콘을 선택하여 [확장] 보기를 엽니다.
1. [확장] 보기에서 "Azure Machine Learning"을 검색합니다.
1. **설치**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning VS Code 확장 설치](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> 또는 [설치 관리자를 직접 다운로드](https://aka.ms/vscodetoolsforai)하여 Visual Studio Marketplace를 통해 Azure Machine Learning 확장을 설치할 수 있습니다. 

이 자습서의 나머지 단계는 **0.6.8 버전**의 확장을 사용하여 테스트되었습니다.

## <a name="sign-in-to-your-azure-account"></a>Azure 계정에 로그인

Azure에서 리소스를 프로비저닝하고 워크로드를 실행하려면 Azure 계정 자격 증명을 사용하여 로그인해야 합니다. 계정 관리를 지원하기 위해 Azure Machine Learning에서 Azure 계정 확장을 자동으로 설치합니다. 다음 사이트를 방문하여 [Azure 계정 확장에 대해 자세히 알아보세요](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. 메뉴 모음에서 **보기 > 명령 팔레트**를 차례로 선택하여 명령 팔레트를 엽니다. 
1. 텍스트 상자에서 "Azure: 로그인" 명령을 입력하여 로그인 프로세스를 시작합니다.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Azure에서 기계 학습 모델 학습 스크립트 실행

이제 계정 자격 증명을 사용하여 Azure에 로그인했으므로 이 섹션의 단계를 통해 확장을 사용하여 기계 학습 모델을 학습시키는 방법을 알아봅니다.

1. 컴퓨터의 아무 곳에 [VS Code Tools for AI 리포지토리](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip)를 다운로드하여 압축을 풉니다.
1. Visual Studio Code에서 `mnist-vscode-docs-sample` 디렉터리를 엽니다.
1. 작업 막대에서 **Azure** 아이콘을 선택합니다.
1. Azure Machine Learning 보기의 위쪽에서 **실험 실행** 아이콘을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![실험 실행](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. 명령 팔레트가 확장되면 프롬프트의 지시를 따릅니다.

    1. Azure 구독을 선택합니다.
    1. 환경 목록에서 **Conda 종속성 파일**을 선택합니다.
    1. **Enter**를 눌러 Conda 종속성 파일을 찾아봅니다. 이 파일에는 스크립트를 실행하는 데 필요한 종속성이 포함되어 있습니다. 이 경우 종속성 파일은 `mnist-vscode-docs-sample` 디렉터리 내의 `env.yml` 파일입니다.
    1. **Enter**를 눌러 학습 스크립트 파일을 찾아봅니다. 이 스크립트는 필기 숫자의 이미지를 분류하는 기계 학습 모델에 대한 코드를 포함한 파일입니다. 이 경우 모델을 학습시키는 스크립트는 `mnist-vscode-docs-sample` 디렉터리 내의 `train.py` 파일입니다.

1. 이 시점에서 아래와 비슷한 구성 파일이 텍스트 편집기에 표시됩니다. 구성에는 모델을 학습시키는 코드와 이전 단계에서 지정한 Python 종속성이 포함된 파일과 같이 학습 작업을 실행하는 데 필요한 정보가 포함되어 있습니다.

    ```json
    {
        "workspace": "WS04131142",
        "resourceGroup": "WS04131142-rg1",
        "location": "South Central US",
        "experiment": "WS04131142-exp1",
        "compute": {
            "name": "WS04131142-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS04131142-com1-rc1",
            "environment": {
                "name": "WS04131142-env1",
                "conda_dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip"
                ],
                "pip_dependencies": [
                    "azureml-defaults"
                ],
                "environment_variables": {}
            }
        }
    }
    ```

1. 구성에 만족하면 명령 팔레트를 열고 다음 명령을 입력하여 실험을 제출합니다.

    ```text
    Azure ML: Submit Experiment
    ```

    그러면 `train.py` 및 구성 파일이 Azure Machine Learning 작업 영역으로 보내집니다. 그런 다음, Azure의 컴퓨팅 리소스에서 학습 작업이 시작됩니다.

### <a name="track-the-progress-of-the-training-script"></a>학습 스크립트의 진행률 추적

스크립트를 실행하는 데 몇 분 정도 걸릴 수 있습니다. 진행률을 추적하려면 다음을 수행합니다.

1. 작업 막대에서 **Azure** 아이콘을 선택합니다.
1. 구독 노드를 펼칩니다.
1. 현재 실행 중인 실험의 노드를 펼칩니다. 이는 작업 영역 및 실험의 값이 구성 파일에 정의된 속성과 동일한 `{workspace}/Experiments/{experiment}` 노드 내에 있습니다.
1. 실험에 대한 모든 실행과 해당 상태가 나열됩니다. 최신 상태를 가져오려면 Azure Machine Learning 보기의 위쪽에 있는 새로 고침 아이콘을 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![실험 진행률 추적](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>학습된 모델 다운로드

실험 실행이 완료되면 출력은 학습된 모델입니다. 출력을 로컬로 다운로드하려면 다음을 수행합니다.

1. 마우스 오른쪽 단추로 가장 최근의 실행을 클릭하고, **출력 다운로드**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![학습된 모델 다운로드](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. 출력을 저장할 위치를 선택합니다.
1. 실행 이름이 포함된 폴더가 로컬로 다운로드됩니다. 해당 페이지로 이동합니다.
1. 모델 파일은 `outputs/outputs/model` 디렉터리 내에 있습니다.

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Machine Learning Visual Studio Code 확장을 사용하여 이미지 분류 TensorFlow 모델 학습 및 배포](tutorial-train-deploy-image-classification-model-vscode.md)
