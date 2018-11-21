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
ms.openlocfilehash: 2fd2d35bde95a3e268f46b398f2163f9d40ab1ee
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613956"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Azure Machine Learning용 개발 환경 구성

이 문서에서는 Azure Machine Learning 서비스에서 작동하는 개발 환경을 구성하는 방법을 알아봅니다. Azure Machine Learning 서비스는 플랫폼에 관계 없이 사용할 수 있습니다. 개발 환경에 대한 유일한 요구 사항은 __Python 3__, __Conda__(격리된 환경의 경우) 및 Azure Machine Learning 작업 영역 정보를 포함하는 구성 파일입니다.

이 문서에서는 다음과 같은 환경 및 도구를 중점적으로 다룹니다.

* [Azure Notebooks](#aznotebooks): Azure 클라우드에 호스트되는 Jupyter Notebook 서비스입니다. Azure Machine Learning SDK가 이미 설치되어 있으므로 시작하기에 __가장 쉬운__ 방법입니다.
* [Data Science Virtual Machine](#dsvm): __데이터 과학 작업용으로 디자인된__ Azure 클라우드의 가상 머신입니다. Python 3, Conda, Jupyter Notebook 및 Azure Machine Learning SDK가 이미 설치되어 있습니다. VM과 함께 인기 있는 ML 솔루션 개발용 ML 프레임워크, 도구 및 편집기가 제공됩니다. Azure 플랫폼에서 __가장 완성도 높은__ ML용 개발 환경일 것입니다.
* [Jupyter Notebook](#jupyter): 이미 Jupyter Notebook을 사용 중인 경우 SDK를 통해 몇 가지 요소를 추가로 설치해야 합니다.
* [Visual Studio Code](#vscode): Visual Studio Code를 사용하는 경우 몇 가지 유용한 확장 프로그램을 설치할 수 있습니다.

이미 Python 3 환경을 갖고 있거나 SDK를 설치하는 데 필요한 기본 단계만 원하는 경우 [로컬 컴퓨터](#local) 섹션을 참조하세요.

## <a name="prerequisites"></a>필수 조건

- Azure Machine Learning 서비스 작업 영역. [Azure Machine Learning 서비스 시작](quickstart-get-started.md)의 단계에 따라 작업 영역을 만듭니다.

- [Continuum Anaconda](https://www.anaconda.com/download/) 또는 [Miniconda](https://conda.io/miniconda.html) 패키지 관리자.

    > [!IMPORTANT]
    > Azure Notebooks를 사용할 때에는 Anaconda 및 Miniconda가 필요 없습니다.

- Linux 또는 Mac OS에서는 bash 셸이 필요합니다.

    > [!TIP]
    > Linux 또는 Mac OS에서 bash가 아닌 셸을 사용하는 경우(예: zsh) 일부 명령을 실행할 때 오류가 발생할 수 있습니다. 이 문제를 해결하려면 `bash` 명령을 사용하여 새 bash 셸을 시작하고 거기서 명령을 실행하세요.

- Windows에서는 명령 프롬프트 또는 Anaconda 프롬프트(Anaconda 및 Miniconda를 통해 설치한)가 필요합니다.

## <a id="anotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com)(미리 보기)는 Azure 클라우드의 대화형 개발 환경입니다. Azure Machine Learning 개발을 시작하는 __가장 쉬운__ 방법입니다.

* Azure Machine Learning SDK가 __이미 설치되어 있습니다__.
* Azure Portal에서 Azure Machine Learning 서비스 작업 영역을 만든 후에는 단추를 클릭하여 작업 영역과 함께 작동하도록 자동으로 Azure Notebook 환경을 구성할 수 있습니다.

Azure Notebooks로 개발을 시작하려면 [Azure Machine Learning 서비스 시작](quickstart-get-started.md) 문서를 따릅니다.

## <a id="dsvm"></a>Data Science Virtual Machine

DSVM(Data Science Virtual Machine)은 **데이터 과학 작업용으로 디자인된** 사용자 지정 VM(가상 머신) 이미지입니다.  다음을 포함합니다.

  - 인기 있는 데이터 과학 도구
  - RStudio, PyCharm 같은 IDE(통합 개발 환경)
  - Jupyter Notebook, Tensorflow 같은 패키지

Azure Machine Learning SDK는 DSVM의 Ubuntu 또는 Windows 버전에서 작동합니다. 개별 환경으로 DSVM을 사용하려면 다음 단계를 따릅니다.

1. Data Science Virtual Machine을 만들려면 다음 문서 중 하나의 단계를 수행합니다.

    * [Ubuntu Data Science Virtual Machine 만들기](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)
    * [Windows Data Science Virtual Machine 만들기](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

1. Azure Machine Learning SDK가 DSVM에 **이미 설치되어 있습니다**. SDK가 포함된 Conda 환경을 사용하려면 다음 명령 중 하나를 사용합니다.

    * __Ubuntu__ DSVM에서는 다음 명령을 사용합니다.

        ```shell
        conda activate py36
        ```

    * __Windows__ DSVM에서는 다음 명령을 사용합니다.

        ```shell
        conda activate AzureML
        ```

1. SDK에 액세스할 수 있는지 확인하고 버전을 검사하려면 다음 Python 코드를 사용합니다.

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Azure Machine Learning 서비스 작업 영역을 사용하도록 DSVM을 구성하려면 [작업 영역 구성](#workspace) 섹션을 참조하세요.

Data Science Virtual Machine에 대한 자세한 내용은 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)을 참조하세요.

## <a id="local"></a>로컬 컴퓨터

로컬 컴퓨터를 사용할 때에는(원격 가상 머신이 될 수도 있음) 다음 단계에 따라 conda 환경을 만들고 SDK를 설치합니다.

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

   > [!NOTE]
   > `PyYAML`을 제거할 수 없다는 메시지를 받는 경우 다음 명령을 대신 사용합니다.
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   SDK를 설치하는 데 몇 분 정도 걸릴 수 있습니다.

1. 기계 학습 실험에 필요한 패키지를 설치합니다. 다음 명령을 사용하고 `<new package>`를 설치하려는 패키지로 바꿉니다.

    ```shell
    conda install <new package>
    ```

1. SDK가 설치되었는지 확인하려면 다음 Python 코드를 실행합니다.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook은 [Jupyter 프로젝트](https://jupyter.org/)의 일부입니다. 라이브 코드를 설명 테스트 및 그래픽과 혼합하는 문서를 만드는 대화형 코딩 환경을 제공합니다. Jupyter Notebook은 코드 섹션의 출력을 문서에 저장할 수 있으므로 다른 사람과 결과를 공유하는 좋은 방법이기도 합니다. Jupyter Notebook은 다양한 플랫폼에 설치할 수 있습니다.

[로컬 컴퓨터](#local) 섹션의 단계에서는 Jupyter Notebook을 사용하기 위한 선택적 구성 요소를 설치합니다. Jupyter Notebook 환경에서 이러한 구성 요소를 사용하려면 다음 단계를 수행합니다.

1. 명령 프롬프트 또는 셸을 엽니다.

1. Conda를 인식하는 Jupyter Notebook 서버를 설치하고 실험 위젯을 사용하려면 다음 명령을 사용합니다.

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

1. Jupyter Notebook에서 SDK를 사용할 수 있는지 확인하려면 새 Notebook을 열고 "myenv"를 커널로 선택합니다. Notebook 셀에서 다음 명령을 실행합니다.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Azure Machine Learning 서비스 작업 영역을 사용하도록 Jupyter Notebook을 구성하려면 [작업 영역 구성](#workspace) 섹션을 참조하세요.

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code는 크로스 플랫폼 코드 편집기입니다. 로컬 Python 3와 Python을 지원하기 위해 설치되는 Conda를 사용하지만, AI를 사용하기 위한 추가 도구를 제공합니다. 코드 편집기 내에서 Conda 환경을 선택할 수 있는 지원도 제공합니다.

개발에 Visual Studio Code를 사용하려면 다음 단계를 수행합니다.

1. Python 개발에 Visual Studio Code를 사용하는 방법은 [VSCode에서 Python 시작](https://code.visualstudio.com/docs/python/python-tutorial) 문서를 참조하세요.

1. Conda 환경을 선택하려면 VS Code를 열고 __Ctrl-Shift-P__(Linux 및 Windows) 또는 __Command-Shift-P__(Mac)를 사용하여 __명령 팔레트__를 가져옵니다. __Python: 인터프리터 선택__을 입력하고 conda 환경을 선택합니다.

1. SDK를 사용할 수 있는지 확인하려면 다음 코드가 포함된 새 Python 파일(.py)을 만듭니다. 그런 다음, 파일을 실행합니다.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Visual Studio Code Tools for AI 확장을 설치하려면 [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) 페이지를 참조하세요.

    자세한 내용은 [Azure Machine Learning에서 VS Code Tools for AI 사용](how-to-vscode-tools.md) 문서를 참조하세요.

## <a id="workspace"></a>작업 영역 구성 파일 만들기

작업 영역 구성 파일은 Azure Machine Learning 서비스 작업 영역과 통신하는 방법을 SDK에 알려주는 JSON 문서입니다. 파일 이름은 `config.json`이고 형식은 다음과 같습니다.

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

이 파일은 Python 스크립트 또는 Jupyter Notebook을 포함하는 디렉터리 구조 내에 있어야 합니다. 같은 디렉터리에 있어도 되고, 하위 디렉터리 `aml_config`에 있어도 되고, 부모 디렉터리에 있어도 됩니다.

코드에서 이 파일을 사용하려면 `ws=Workspace.from_config()`를 사용합니다. 이 코드는 파일의 정보를 로드하고 작업 영역에 연결합니다.

구성 파일을 만드는 세 가지 방법이 있습니다.

* [Azure Machine Learning 빠른 시작](quickstart-get-started.md)을 따르는 경우 `config.json` 파일은 Azure Notebooks 라이브러리에 생성됩니다. 이 파일은 작업 영역에 대한 구성 정보를 포함합니다. 이 `config.json` 파일을 다른 개발 환경으로 다운로드 또는 복사할 수 있습니다.

* 텍스트 편집기를 사용하여 **수동으로 파일을 만들 수 있습니다**. [Azure Portal](https://portal.azure.com)에서 작업 영역을 방문하여 해당 구성 파일의 값을 찾을 수 있습니다. __작업 영역 이름__, __리소스 그룹__ 및 __구독 ID__ 값을 복사하여 구성 파일에 사용합니다.
        ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **파일을 프로그래밍 방식으로 만들 수 있습니다**. 다음 코드 조각은 구독 ID, 리소스 그룹 및 작업 영역 이름을 입력하여 작업 영역에 연결하는 방법을 보여줍니다. 그런 다음, 작업 영역 구성을 파일에 저장합니다.

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    이 코드는 `aml_config/config.json` 파일에 구성 파일을 씁니다.

## <a name="next-steps"></a>다음 단계

- [ MNIST 데이터 집합으로 Azure Machine Learning에서 모델 교육](tutorial-train-models-with-aml.md)
- [Python용 Azure Machine Learning SDK](https://aka.ms/aml-sdk)
- [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)