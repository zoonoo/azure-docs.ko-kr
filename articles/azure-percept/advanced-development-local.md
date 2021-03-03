---
title: 로컬에서 Azure Percept 고급 개발 시작
description: AzureML에서 VS Code + Jupyter Notebook을 사용하여 로컬 기계 학습 개발 시작
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 88e71ac21177a13d30176212e97442c63272eca6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658973"
---
# <a name="getting-started-with-machine-learning-development-using-vs-code--jupyter-notebooks-on-azureml"></a>AzureML에서 VS Code + Jupyter Notebook을 사용하여 기계 학습 개발 시작

이 문서에서는 Azure Machine Learning 작업 영역을 설정하고, 컴퓨팅 인스턴스를 만들고, 로컬 컴퓨터에 Visual Studio Code 개발 환경을 설정하고, VS Code 내에서 미리 구성된 샘플 Jupyter Notebook의 셀을 실행하는 과정을 안내합니다.

[Notebook](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb)은 bowl을 검색하기 위한 사용자 지정 데이터 세트를 사용하여 Python 환경의 AzureML에 미리 학습된 TensorFlow 모델(MobileNetSSDV2Lite)을 사용하여 전이 학습을 수행합니다.

이 Notebook에서는 [COCO 데이터 세트](https://cocodataset.org/#home)에서 시작하여 관심 있는 클래스(bowl)로만 필터링한 다음, 이를 적절한 형식으로 변환하는 방법을 보여줍니다. 또는 오픈 소스 [VoTT 2](https://github.com/microsoft/VoTT) 레이블 도구를 사용하여 PASCAL VOC 형식으로 경계 상자를 만들고 레이블을 지정할 수 있습니다.

사용자 지정 데이터 세트로 모델을 다시 학습시킨 후 모듈 쌍 업데이트 메서드를 사용하여 모델을 Azure Percept DK에 배포할 수 있습니다. 그런 다음, Azure Percept Vision SoM에서 라이브 RTSP 스트림을 확인하여 모델 추론을 확인할 수 있습니다. 모델 재학습 및 배포는 모두 원격 컴퓨팅 인스턴스를 통해 Notebook 내에서 수행됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure Machine Learning 구독](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept Vision SoM이 연결된 Azure Percept DK](./overview-azure-percept-dk.md)
- [Azure Percept DK 온보딩 경험](./quickstart-percept-dk-set-up.md) 완료

## <a name="download-azure-percept-github-repository"></a>Azure Percept GitHub 리포지토리 다운로드

1. [Azure Percept DK GitHub 리포지토리](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview)로 이동합니다.
1. 리포지토리를 복제하거나 ZIP 파일을 다운로드합니다. 화면 상단에서 **코드** -> **ZIP 다운로드** 를 클릭합니다.

    :::image type="content" source="./media/advanced-development-local/download-zip.png" alt-text="GitHub 다운로드 화면":::

## <a name="create-an-azure-machine-learning-workspace-and-remote-compute-instance"></a>Azure Machine Learning 작업 영역 및 원격 컴퓨팅 인스턴스 만들기

1. [Azure Machine Learning 포털](https://ml.azure.com)로 이동합니다.
1. 드롭다운 메뉴에서 디렉터리, Azure 구독 및 Machine Learning 작업 영역을 선택하고 **시작** 을 클릭합니다.

    :::image type="content" source="./media/advanced-development-local/machine-learning-portal-get-started.png" alt-text="Azure ML 시작 화면":::

    Azure Machine Learning 작업 영역이 아직 없는 경우 **새 작업 영역 만들기** 를 클릭합니다. 새 브라우저 탭에서 다음을 수행합니다.

    1. Azure 구독을 선택합니다.
    1. 리소스 그룹을 선택합니다.
    1. 작업 영역의 이름을 입력합니다.
    1. 지역을 선택합니다.
    1. 작업 영역 버전을 선택합니다.
    1. **검토 + 만들기** 를 클릭합니다.
    1. 다음 페이지에서 선택 내용을 검토하고 **만들기** 를 클릭합니다.

        :::image type="content" source="./media/advanced-development-local/workspace-review-and-create.png" alt-text="Azure ML의 작업 영역 만들기 화면":::

    작업 영역을 만드는 데 몇 분 정도 걸릴 수 있습니다. 작업 영역 만들기가 완료되면 리소스 옆에 녹색 확인 표시가 나타나고 Machine Learning Services 개요 페이지 맨 위에 **배포가 완료됨** 이 표시됩니다.

    :::image type="content" source="./media/advanced-development-local/workspace-creation-complete-inline.png" alt-text="작업 영역 만들기 확인" lightbox= "./media/advanced-development-local/workspace-creation-complete.png":::

    작업 영역 만들기가 완료되면 Machine Learning 포털 탭으로 돌아가서 **시작** 을 클릭합니다.

1. Machine Learning 작업 영역 홈페이지의 왼쪽 창에서 **컴퓨팅** 을 클릭합니다.

1. 기존 컴퓨팅 인스턴스가 없는 경우 **새로 만들기** 를 클릭하여 새로운 CPU 또는 GPU 컴퓨팅을 만듭니다. **새 컴퓨팅 인스턴스** 창에서 **컴퓨팅 이름** 을 입력하고, **가상 머신 유형** 을 선택하고, **가상 머신 크기** 를 선택합니다. **만들기** 를 클릭합니다.

    :::image type="content" source="./media/advanced-development-local/new-compute-instance.png" alt-text="새 컴퓨팅 인스턴스 만들기 화면":::

    **만들기** 를 클릭하면 컴퓨팅 인스턴스를 만드는 데 몇 분이 걸립니다. 컴퓨팅 만들기가 완료되면 **컴퓨팅** 상태에 녹색 원 및 **\<your compute name> - 실행 중** 이 표시됩니다. 이 컴퓨팅 인스턴스는 Jupyter 서버를 실행하며 이 자습서에서 활용됩니다.

## <a name="visual-studio-code-development-environment-setup"></a>Visual Studio Code 개발 환경 설정

1. 필요한 도구를 설치합니다.

    1. 옵션 1:

        [개발 도구 팩 설치 관리자](./dev-tools-installer.md)를 사용하여 다음 패키지를 설치합니다.

        - Visual Studio Code
        - Python 3.5, 3.6 또는 3.7
        - Miniconda3
        - Intel OpenVino Toolkit 2020.2

        참고: Intel OpenVino Toolkit은 개발 도구 팩 설치 관리자에서 선택적 패키지로 표시되지만 Azure Percept DK 개발 키트의 Azure Percept Vision SoM을 사용하는 데 필요합니다.

    1. 옵션 2:

        개발 도구 팩 설치 관리자를 사용하지 않으려면 아래 링크를 클릭하고 지정된 다운로드 및 설치 지침에 따라 다음 패키지를 수동으로 설치합니다.

        - [Visual Studio Code](https://code.visualstudio.com/)
        - [Python 3.5, 3.6 또는 3.7](https://www.python.org/)
        - [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
        - [Intel OpenVino Toolkit 2020.2](https://docs.openvinotoolkit.org/)

    참고: 전체 Anaconda 배포가 이미 설치되어 있으면 Miniconda를 설치할 필요가 없습니다. 또는, Anaconda 또는 Miniconda를 사용하지 않으려면 Python 가상 환경을 만들고 pip을 사용하여 AI 모델 개발을 실행하는 데 필요한 패키지를 설치합니다.

1. Visual Studio Code를 시작합니다.
1. 데이터 과학 환경을 설정합니다.

    - 옵션 1 - 이미 큐레이팅된 ML 패키지가 있는 기존의 또는 새로운 기계 학습 원격 컴퓨팅 인스턴스에 연결합니다. **이 자습서에서는 이 옵션을 사용합니다**.

    - 옵션 2 - 로컬 컴퓨터에서 conda 환경을 설정합니다.
        1. Anaconda 또는 Miniconda 명령 프롬프트를 열고 다음 명령을 실행하여 지정된 패키지를 사용해서 **myenv** 라는 환경을 만듭니다.

            `conda create -n myenv python=3.7 pandas jupyter seaborn scikit-learn keras tensorflow=1.15`

            Anaconda 환경을 만들고 관리하는 방법에 대한 자세한 내용은 [Anaconda 설명서](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)를 참조하세요.

1. VS Code 작업 영역을 만듭니다.

    1. 편리한 위치에 Visual Studio Code 작업 영역으로 사용할 폴더를 만듭니다. 이름을 **myworkspace** 로 지정합니다.
    1. **파일** > **폴더 열기** > **폴더 선택** 을 클릭하여 Visual Studio Code에서 **myworkspace** 를 엽니다.
    1. 파일 탐색기에서 Azure Percept DK GitHub 리포지토리의 로컬 복사본에 포함된 [Transferlearningusing_SSDLiteV2 Model.ipynbb 파일](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb)로 이동하여 선택합니다. 이 Notebook 파일을 myworkspace 폴더에 복사합니다.

## <a name="azure-integration-with-visual-studio-code"></a>Visual Studio Code와 Azure 통합

1. [Azure Machine Learning의 무료 버전 또는 유료 버전](https://aka.ms/AMLFree)에 아직 등록하지 않았으면 등록하여 사용해 보세요.

1. 다음과 같은 VS Code용 Azure 확장을 설치합니다.
    - [Azure Machine Learning 확장](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)
    - Python 참가자 확장. VS Code에서 **보기** -> **명령 팔레트** 로 이동합니다. 명령 팔레트에서 **Python: Switch to Insiders Daily Channel(Python: 참가자 일일 채널로 전환)** 을 입력하고 선택합니다.
    - [Azure 계정 확장.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) 메시지가 표시되면 VS Code에서 창을 다시 로드합니다.
    - [Azure IoT Hub Toolkit 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)
    - [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)

1. Visual Studio Code 내에서 Azure 계정에 로그인하여 리소스를 프로비저닝하고 Azure에서 워크로드를 실행합니다.
    1. Visual Studio Code의 메뉴 모음에서 **보기** > **명령 팔레트** 를 차례로 선택하여 명령 팔레트를 엽니다.
    1. 명령 팔레트에 **Azure: Sign In(Azure: 로그인)** 을 입력하여 로그인 프로세스를 시작합니다.

        :::image type="content" source="./media/advanced-development-local/transfer-learning-azure-sign-in-inline.png" alt-text="Azure 로그인 화면" lightbox= "./media/advanced-development-local/transfer-learning-azure-sign-in.png":::

    1. VS Code의 왼쪽에 있는 Azure 아이콘을 클릭하여 Python 확장 및 Azure 계정을 활성화합니다.

        :::image type="content" source="./media/advanced-development-local/azure-icon.png" alt-text="VS Code의 Azure 아이콘":::

    1. **myworkspace** 폴더에서 Transferlearningusing_SSDLiteV2 Model_VSCode.ipynb Notebook을 엽니다.
    1. 명령 팔레트를 엽니다. **Python: specify local or remote Jupyter server for connections(Python: 연결할 로컬 또는 원격 Jupyter 서버 지정)** 를 입력하고 선택합니다.
    1. 연결을 선택할 수 있는 목록이 표시됩니다. **Azure ML 컴퓨팅 인스턴스** 를 선택합니다.

        :::image type="content" source="./media/advanced-development-local/azure-machine-learning-compute-instances.png" alt-text="VS Code의 Azure ML 컴퓨팅 인스턴스 옵션":::

    1. 단계별 프롬프트에 따라 구독, 작업 영역 및 원격 컴퓨팅 인스턴스를 선택합니다. 이 자습서의 앞부분에서 만든 작업 영역 및 원격 컴퓨팅 인스턴스를 선택합니다. 새 컴퓨팅 인스턴스를 만들 수도 있습니다.
    1. 컴퓨팅 인스턴스를 선택하면 VS Code 창을 다시 로드하라는 메시지가 표시됩니다. 다시 로드한 후 **Python 3.6 - AzureML** 커널을 선택합니다. 이제 Notebook의 모든 셀을 실행할 수 있습니다. Notebook 셀을 실행하면 Notebook과 컴퓨팅 인스턴스 간의 연결이 인스턴스화됩니다. Notebook 도구 모음은 작업 중인 컴퓨팅 인스턴스를 반영하도록 업데이트됩니다.

        :::image type="content" source="./media/advanced-development-local/kernel-inline.png" alt-text="VS Code의 커널 선택" lightbox= "./media/advanced-development-local/kernel.png":::

## <a name="working-with-the-notebook"></a>Notebook 작업

이제 VS Code에서 Notebook을 실행하여 사용자 지정 bowl 감지기를 학습시키고 개발 키트에 배포할 준비가 되었습니다. Notebook의 일부 셀은 스크립트를 실행하기 전에 입력 매개 변수가 필요하기 때문에 각 셀을 개별적으로 실행해야 합니다. 입력 매개 변수가 필요한 셀은 Notebook에서 직접 편집할 수 있습니다. 셀을 실행하려면 셀 왼쪽에 있는 재생 아이콘을 클릭합니다.

:::image type="content" source="./media/advanced-development-local/run-cell-1.png" alt-text="셀 아이콘이 강조 표시된 Notebook":::

## <a name="next-steps"></a>다음 단계

추가 Azure Machine Learning Service 예제 Notebook을 보려면 이 [리포지토리](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)를 참조하세요.
