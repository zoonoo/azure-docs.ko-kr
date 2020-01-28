---
title: '자습서: Azure Machine Learning Visual Studio Code 확장 설정'
titleSuffix: Azure Machine Learning
description: Visual Studio Code Azure Machine Learning 확장을 설정하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 4000fcc80d507d3b1e871d7f3288fc3b77693c76
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157464"
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
- 간단한 플랫폼 간 코드 편집기인 [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview)를 설치합니다. 

## <a name="install-the-extension"></a>확장 설치

1. Visual Studio Code를 엽니다.
1. **작업 막대**에서 **확장** 아이콘을 선택하여 [확장] 보기를 엽니다.
1. [확장] 보기에서 "Azure Machine Learning"을 검색합니다.
1. **설치**를 선택합니다.

> [!NOTE]
> 또는 [설치 관리자를 직접 다운로드](https://aka.ms/vscodetoolsforai)하여 Visual Studio Marketplace를 통해 Azure Machine Learning 확장을 설치할 수 있습니다. 

이 자습서의 나머지 단계는 **0.6.8 버전**의 확장을 사용하여 테스트되었습니다.

## <a name="sign-in-to-your-azure-account"></a>Azure 계정에 로그인

Azure에서 리소스를 프로비저닝하고 워크로드를 실행하려면 Azure 계정 자격 증명을 사용하여 로그인해야 합니다. 계정 관리를 지원하기 위해 Azure Machine Learning에서 Azure 계정 확장을 자동으로 설치합니다. 다음 사이트를 방문하여 [Azure 계정 확장에 대해 자세히 알아보세요](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. 메뉴 모음에서 **보기 > 명령 팔레트**를 차례로 선택하여 명령 팔레트를 엽니다. 
1. 텍스트 상자에서 "Azure: 로그인" 명령을 입력하여 로그인 프로세스를 시작합니다.

## <a name="run-a-script-in-azure"></a>Azure에서 스크립트 실행

이제 계정 자격 증명을 사용하여 Azure에 로그인했으므로 이 섹션의 단계를 통해 확장을 사용하여 기계 학습 모델을 학습시키는 방법을 알아봅니다.

1. 컴퓨터의 아무 곳에 [VS Code Tools for AI 리포지토리](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip)를 다운로드하여 압축을 풉니다.
1. Visual Studio Code에서 `mnist-vscode-docs-sample` 디렉터리를 엽니다.
1. 작업 막대에서 **Azure** 아이콘을 선택합니다.
1. Azure Machine Learning 보기의 위쪽에서 **실험 실행** 아이콘을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![실험 실행](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. 명령 팔레트가 확장되면 프롬프트의 지시를 따릅니다.

    1. Azure 구독을 선택합니다.
    1. **새 Azure ML 작업 영역 만들기**를 선택합니다.
    1. **TensorFlow 단일 노드 학습** 작업 형식을 선택합니다.
    1. 학습할 스크립트로 `train.py`를 입력합니다. 이 스크립트는 필기 숫자의 이미지를 분류하는 기계 학습 모델에 대한 코드를 포함한 파일입니다.
    1. 실행하기 위한 요구 사항으로 다음 패키지를 지정합니다.

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. 이 시점에서 아래와 비슷한 구성 파일이 텍스트 편집기에 표시됩니다. 구성에는 모델을 학습시키는 코드와 이전 단계에서 지정한 Python 종속성이 포함된 파일과 같이 학습 작업을 실행하는 데 필요한 정보가 포함되어 있습니다.

    ```json
    {
        "workspace": "WS12191742",
        "resourceGroup": "WS12191742-rg2",
        "location": "South Central US",
        "experiment": "WS12191742-exp2",
        "compute": {
            "name": "WS12191742-com2",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS12191742-com2-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. Azure에서 실험을 실행하려면 **실험 제출**을 선택합니다. 그러면 `train.py` 및 구성 파일이 Azure Machine Learning 작업 영역으로 보내집니다. 그런 다음, Azure의 컴퓨팅 리소스에서 학습 작업이 시작됩니다.
1. 몇 분 후에 학습된 TensorFlow 모델이 포함된 `output`이라는 디렉터리가 로컬로 만들어집니다.

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Machine Learning Visual Studio Code 확장을 사용하여 이미지 분류 TensorFlow 모델 학습 및 배포](tutorial-train-deploy-image-classification-model-vscode.md)
