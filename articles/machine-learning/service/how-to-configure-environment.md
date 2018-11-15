---
title: Azure Machine Learning용 개발 환경 구성 | Microsoft Docs
description: Azure Machine Learning 서비스 작업 시 개발 환경을 구성하는 방법을 알아보세요. 이 문서에서는 Conda 환경을 사용하고, 구성 파일을 만들고, Jupyter Notebooks, Azure Notebooks, IDE, 코드 편집기 및 Data Science Virtual Machine을 구성하는 방법을 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 11/6/2018
ms.openlocfilehash: 8ce411e424d538a4a1f94300bfe5510658017f56
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238327"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Azure Machine Learning용 개발 환경 구성

이 문서에서는 다음을 포함하여 Azure Machine Learning 서비스에 사용할 개발 환경을 구성하는 방법을 알아봅니다.

- 개발 환경을 Azure Machine Learning 서비스 작업 영역과 연결하는 구성 파일을 만드는 방법.
- 다음 개발 환경을 구성하는 방법:
  - 컴퓨터의 Jupyter Notebook
  - Visual Studio Code
  - 사용자 지정 코드 편집기
- [conda 가상 환경](https://conda.io/docs/user-guide/tasks/manage-environments.html)을 설정하고 Azure Machine Learning에 사용하는 방법입니다. 패키지 간의 종속성 충돌을 방지하도록 Continuum Anaconda를 사용하여 작업 환경을 분리하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건

- Azure Machine Learning 서비스 작업 영역을 설정합니다. [Azure Machine Learning 서비스 시작](quickstart-get-started.md)의 단계를 따릅니다.
- [Continuum Anaconda](https://www.anaconda.com/download/) 또는 [Miniconda](https://conda.io/miniconda.html) 패키지 관리자를 설치합니다.
- Visual Studio Code를 사용하는 경우 [Python 확장 프로그램](https://code.visualstudio.com/docs/python/python-tutorial)을 받습니다.

> [!NOTE]
> bash를 사용하여(Linux 및 Mac OS인 경우) 또는 명령 프롬프트를 사용하여(Windows인 경우) 이 문서에 표시된 셸 명령을 테스트할 수 있습니다.

## <a name="create-a-workspace-configuration-file"></a>작업 영역 구성 파일 만들기

Azure Machine Learning SDK는 작업 영역 구성 파일을 사용하여 Azure Machine Learning 서비스 작업 영역과 통신합니다.

- 구성 파일을 만들려면 [Azure Machine Learning 빠른 시작](quickstart-get-started.md)을 완료하세요.
  - 빠른 시작 과정은 Azure Notebooks에 `config.json` 파일을 만듭니다. 이 파일은 작업 영역에 대한 구성 정보를 포함합니다.
  - `config.json` 파일을 참조하는 스크립트 또는 Notebook과 동일한 디렉터리에 이 파일을 다운로드하거나 복사합니다.

- 또는 다음 단계에 따라 이 파일을 수동으로 만들 수 있습니다.

    1. [Azure Portal](https://portal.azure.com)에서 작업 영역을 엽니다. __작업 영역 이름__, __리소스 그룹__ 및 __구독 ID__를 복사합니다. 이 값은 구성 파일을 만드는 데 사용됩니다.
        ![Azure Portal](./media/how-to-configure-environment/configure.png)

    1. 다음 Python 코드를 사용하여 파일을 만들고 작업 영역을 참조하는 스크립트 또는 Notebook과 동일한 디렉터리에서 코드를 실행해야 합니다.

        ```python
        from azureml.core import Workspace

        subscription_id ='<subscription-id>'
        resource_group ='<resource-group>'
        workspace_name = '<workspace-name>'

        try:
           ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
           ws.write_config()
           print('Library configuration succeeded')
        except:
           print('Workspace not found')
        ```
        이 코드는 다음 `aml_config/config.json` 파일을 작성합니다.

        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        `aml_config` 디렉터리 또는 `config.json` 파일만 작업 영역을 참조하는 다른 디렉터리에 복사할 수 있습니다.

       > [!NOTE]
       > 동일한 디렉터리 또는 아래에 있는 기타 스크립트 또는 Notebook은 `ws=Workspace.from_config()`를 사용하여 작업 영역을 로드하게 됩니다.

## <a name="azure-notebooks-and-data-science-virtual-machines"></a>Azure Notebooks 및 Data Science Virtual Machines

Azure Notebooks 및 Azure DSVM(Data Science Virtual Machine)은 Azure Machine Learning 서비스와 함께 작동하도록 구성된 상태로 제공됩니다. 이러한 환경에는 Azure Machine Learning SDK 같은 필수 구성 요소가 포함되어 있습니다.

### <a name="azure-notebooks"></a>Azure Notebooks

- Azure Notebooks는 Azure 클라우드의 Jupyter Notebook 서비스입니다.
- 이러한 환경을 사용할 때도 작업 영역 구성 파일이 필요합니다.

Azure Machine Learning 서비스와 Azure Notebooks를 함께 사용하는 방법에 대한 예는 [Azure Machine Learning 서비스 시작](quickstart-get-started.md)을 참조하세요.

### <a name="data-science-virtual-machines"></a>Data Science Virtual Machine

- Data Science Virtual Machine은 데이터 과학 작업용으로 디자인된 사용자 지정 VM(가상 머신) 이미지입니다. 다음을 포함합니다.
  - 인기 있는 데이터 과학 도구
  - RStudio, PyCharm 같은 IDE(통합 개발 환경)
  - Jupyter Notebook, Tensorflow 같은 패키지

DSVM은 여러 Anaconda 환경이 설치된 상태로 제공됩니다. 패키지를 설치하지 않고 Azure Machine Learning Python SDK를 사용하려면 명령 프롬프트/셸을 열고 다음 명령 중 하나를 사용하여 환경을 활성화합니다.

* __Ubuntu__ DSVM에서 다음 명령을 사용 합니다.

    ```shell
    conda activate py36
    ```

* __Windows__ DSVM에서 다음 명령을 사용 합니다.

    ```shell
    conda activate AzureML
    ```

이 환경으로 전환한 후에는 패키지를 설치하지 않고 원하는 빌드 도구에서 즉시 Azure Machine Learning SDK를 가져올 수 있습니다.

```python
import azureml.core
print(azureml.core.VERSION)
```

Data Science Virtual Machine에 대한 자세한 내용은 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)을 참조하세요.

## <a name="configure-jupyter-notebooks-on-your-computer"></a>컴퓨터에 Jupyter Notebook 구성

1. 명령 프롬프트 또는 셸을 엽니다.

1. 다음 명령을 사용하여 Conda 환경을 만듭니다.

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    Python 3.6 및 기타 구성 요소를 다운로드해야 하는 경우 환경을 만드는 데 몇 분 정도 걸릴 수 있습니다.

1. 다음 명령을 사용하여 Notebook 추가 기능 및 데이터 준비 SDK가 포함된 Azure Machine Learning SDK를 설치합니다.

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   다음 SDK에서 클래스 및 메서드에 대한 Python 참조 문서를 확인할 수 있습니다.
   + [Python용 Azure Machine Learning SDK](https://aka.ms/aml-sdk)
   + [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

   > [!NOTE]
   > `PyYAML`을 제거할 수 없다는 메시지를 받는 경우 다음 명령을 대신 사용합니다.
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   SDK를 설치하는 데 몇 분 정도 걸릴 수 있습니다.

1. 기계 학습 실험에 필요한 패키지를 설치합니다. 다음 명령을 사용하고 `<new package>`를 설치하려는 패키지로 바꿉니다.

    ```shell
    conda install <new package>
    ```

1. conda를 인식하는 Jupyter Notebook 서버를 설치하고 실험 위젯(실행 정보 조회용)을 활성화합니다. 다음 명령을 사용합니다.

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

1. 다음 명령을 사용하여 Jupyter Notebook을 엽니다.

    ```shell
    jupyter notebook
    ```

1. 새 Notebook을 열고, "myenv"를 커널로 선택하고, Azure Machine Learning SDK가 설치되었는지 확인합니다. Notebook 셀에서 다음 명령을 실행합니다.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Visual Studio Code 구성

1. 명령 프롬프트 또는 셸을 엽니다.

1. 다음 명령을 사용하여 Conda 환경을 만듭니다.

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

1. 다음 명령을 사용하여 Azure Machine Learning SDK 및 Data Preparation SDK를 설치합니다.

    ```shell
    pip install --upgrade azureml-sdk[automl] azureml-dataprep
    ```

1. Visual Studio Code Tools for AI 확장을 설치합니다. [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)를 살펴봅니다.

1. 기계 학습 실험에 필요한 패키지를 설치합니다. 다음 명령을 사용하고 `<new package>`를 설치하려는 패키지로 바꿉니다.

    ```shell
    conda install <new package>
    ```

1. Visual Studio Code를 열고 Windows의 경우 **CTRL-SHIFT-P**를, Mac OS의 경우 **COMMAND-SHIFT-P**를 사용하여 **명령 팔레트**를 가져옵니다. _Python: 인터프리터 선택_을 입력하고, 이전에 만든 conda 환경을 선택합니다.

   > [!NOTE]
   > Visual Studio Code는 개발자 컴퓨터에 설치된 Conda 환경을 자동으로 인식합니다. 자세한 내용은 [Visual Studio 코드 문서](https://code.visualstudio.com/docs/python/environments#_conda-environments)를 참조하세요.

1. Visual Studio Code를 사용하여 다음 코드로 새 Python 스크립트 파일을 만든 후 실행하여 구성의 유효성을 검사합니다.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-a-custom-code-editor"></a>사용자 지정 코드 편집기 구성

원하는 코드 편집기를 Azure Machine Learning SDK에 사용할 수 있습니다.

1. 위에서 설명한 [Visual Studio Code 구성](#configure-visual-studio-code)의 2단계에 따라 conda 환경을 만듭니다
1. 각 편집기에 대한 지침에 따라 conda 환경을 사용합니다. 예를 들어 [PyCharm 지침](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html)을 따릅니다.

## <a name="next-steps"></a>다음 단계

- [ MNIST 데이터 집합으로 Azure Machine Learning에서 모델 교육](tutorial-train-models-with-aml.md)
