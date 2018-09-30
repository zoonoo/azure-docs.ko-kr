---
title: Azure Machine Learning용 개발 환경 구성 | Microsoft Docs
description: Azure Machine Learning 서비스 작업 시 개발 환경을 구성하는 방법을 알아보세요. 이 문서에서는 Conda 환경을 사용하고, 구성 파일을 만들고, Jupyter Notebooks, Azure Notebooks, IDE, 코드 편집기 및 Data Science Virtual Machine을 구성하는 방법을 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 8/6/2018
ms.openlocfilehash: 7796accffb7041e567c5e18857d09e105b5268ce
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961572"
---
# <a name="how-to-configure-a-development-environment-for-the-azure-machine-learning-service"></a>Azure Machine Learning 서비스용 개발 환경을 구성하는 방법

Azure Machine Learning 서비스 작업 시 개발 환경을 구성하는 방법을 알아봅니다. Azure Machine Learning 작업 영역과 개발자 환경을 연결하는 구성 파일을 만드는 방법을 알아봅니다. 또한 다음 개발 환경을 구성하는 방법도 알아봅니다.

* 자신의 컴퓨터에 Jupyter Notebook 구성
* Visual Studio Code
* 선택한 코드 편집기

패키지 간의 종속성 충돌을 피하기 위해 Continuum Anaconda [Conda 가상 환경](https://conda.io/docs/user-guide/tasks/manage-environments.html)을 사용하여 작업 환경을 분리하는 것이 좋습니다. 이 문서에서는 Conda 환경을 설정하고 이를 Azure Machine Learning에 사용하는 단계를 보여줍니다.


## <a name="prerequisites"></a>필수 조건

* Azure Machine Learning 서비스 작업 영역. 이를 만들려면 [Azure Machine Learning 서비스 시작](quickstart-get-started.md) 문서에 나와 있는 단계를 따릅니다.

* [Continuum Anaconda](https://www.anaconda.com/download/) 또는 [Miniconda](https://conda.io/miniconda.html) 패키지 관리자.

 * Visual Studio Code 환경용 [Python 확장 프로그램](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="create-workspace-configuration-file"></a>작업 영역 구성 파일 만들기

작업 영역 구성 파일은 SDK에서 Azure Machine Learning 서비스 작업 영역과 통신하는 데 사용됩니다.  이 파일을 얻는 방법은 두 가지가 있습니다.

* [빠른 시작](quickstart-get-started.md)을 완료하면 Azure Notebooks에 `config.json` 파일이 자동으로 생성됩니다.  이 파일은 작업 영역에 대한 구성 정보를 포함합니다.  이 파일을 참조하는 스크립트나 노트북과 동일한 디렉터리에 다운로드하세요.

* 다음 단계에 따라 구성 파일을 직접 만드세요.

    1. [Azure Portal](https://portal.azure.com)에서 작업 영역을 엽니다. __작업 영역 이름__, __리소스 그룹__ 및 __구독 ID__를 복사합니다. 이 값은 구성 파일을 만드는 데 사용됩니다.

       Portal의 작업 영역 대시보드는 Edge, Chrome 및 Firefox 브라우저에서만 지원됩니다.
    
        ![Azure portal](./media/how-to-configure-environment/configure.png) 
    
    3. 텍스트 편집기에서 **config.json**이라는 파일을 만듭니다.  해당 파일에 다음 내용을 추가하여 포털의 값을 삽입합니다.
    
        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
    
        >[!NOTE] 
        >코드 뒷부분에 `ws = Workspace.from_config()`를 사용하여 이 파일을 읽을 수 있습니다.
    
    4. **config.json**은 반드시 이 파일을 참조하는 스크립트나 노트북과 동일한 디렉터리에 다운로드하세요.
    
## <a name="azure-notebooks-and-data-science-virtual-machine"></a>Azure Notebooks 및 Data Science Virtual Machine

Azure Notebooks 및 Azure Data Science Virtual Machine(DSVM)은 Azure Machine Learning 서비스와 함께 작동하도록 미리 구성됩니다. Azure Machine Learning SDK 같은 필수 구성 요소는 이러한 환경에 미리 설치되어 있습니다.

Azure Notebooks는 Azure 클라우드의 Jupyter Notebook 서비스입니다. Data Science Virtual Machine은 데이터 과학 작업을 위해 미리 구성된 VM 이미지입니다. VM에는 주요 도구, IDE 및 Jupyter Notebooks, PyCharm, Tensorflow 같은 패키지가 포함됩니다.

이러한 환경을 사용할 때도 작업 영역 구성 파일이 필요합니다.

Data Science Virtual Machine에 대한 자세한 내용은 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) 문서를 참조하세요.

Azure Machine Learning 서비스와 Azure Notebooks를 함께 사용하는 방법에 대한 예는 [Azure Machine Learning 서비스 시작](quickstart-get-started.md) 문서를 참조하세요.

## <a name="configure-jupyter-notebooks-on-your-own-computer"></a>자신의 컴퓨터에 Jupyter Notebook 구성

1. 명령 프롬프트 또는 셸을 엽니다.

2. Conda 환경을 만들려면 다음 명령을 사용합니다.

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

    Python 3.6 및 기타 구성 요소를 다운로드해야 할 수 있으므로 환경을 만드는 데 몇 분 정도 걸릴 수 있습니다.

3. 노트북 추가 요소와 함께 Azure Machine Learning SDK를 설치하려면 다음 명령을 사용합니다.

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl,contrib]
    ```

    SDK를 설치하는 데 몇 분 정도 걸릴 수 있습니다.

4. 기계 학습 실험을 위한 패키지를 설치하려면 다음 명령을 사용하고 `<new package>`를 설치하려는 패키지로 바꾸세요.

    ```shell
    conda install <new package>
    ```

5. Conda를 인식하는 Jupyter Notebook 서버를 설치하고 실험 위젯(실행 정보 조회용)을 활성화하려면 다음 명령을 사용하세요.

    ```shell
    # install Jupyter 
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

6. Jupyter Notebook을 시작하려면 다음 명령을 사용합니다.

    ```shell
    jupyter notebook
    ```

7. 새 노트북을 열고 "myenv"를 커널로 선택합니다. 그런 다음, 노트북 셀에서 다음 명령을 실행하여 Azure Machine Learning SDK가 설치되어 있는지 확인합니다.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Visual Studio Code 구성

1. 명령 프롬프트 또는 셸을 엽니다.

2. Conda 환경을 만들려면 다음 명령을 사용합니다.

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

2. Azure Machine Learning SDK를 설치하려면 다음 명령을 사용합니다.
 
    ```shell
    pip install --upgrade azureml-sdk[automl,contrib]
    ```

4. AI용 Visual Studio Code Tools를 설치하려면 [AI용 도구](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)에 대한 Visual Studio 마켓플레이스 항목을 참조하세요. 

5. 기계 학습 실험을 위한 패키지를 설치하려면 다음 명령을 사용하고 `<new package>`를 설치하려는 패키지로 바꾸세요.

    ```shell
    conda install <new package>
    ```

6. Visual Studio Code를 시작한 다음, __CTRL-SHIFT-P__를 사용하여 __명령 팔레트__를 가져옵니다. *Python: 인터프리터 선택*을 입력하고, 자신이 만든 Conda 환경을 선택합니다.

    > [!NOTE]
    > Visual Studio Code는 개발자 컴퓨터에 설치된 Conda 환경을 자동으로 인식합니다. 자세한 내용은 [Visual Studio 코드 문서](https://code.visualstudio.com/docs/python/environments#_conda-environments)를 참조하세요.

7. 구성의 유효성을 검사하려면 Visual Studio Code에서 다음 코드로 새로운 Python 스크립트 파일을 만든 후 실행하세요.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-code-editor-of-your-choice"></a>선택한 코드 편집기 구성

Azure Machine Learning SDK에서 사용자 지정 코드 편집기를 사용하려면 먼저 위에서 설명한 것처럼 Conda 환경을 만듭니다. 그런 다음, 각 편집기에 대한 지침을 따라 Conda 환경을 사용합니다. 예를 들어 PyCharm에 대한 지침은 [https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html)에 있습니다.
 
## <a name="next-steps"></a>다음 단계

* [ MNIST 데이터 집합으로 Azure Machine Learning에서 모델 교육](tutorial-train-models-with-aml.md)
