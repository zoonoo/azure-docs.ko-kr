---
title: Python 개발 환경 설정
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Service 작업 시 개발 환경을 구성하는 방법을 알아봅니다. 이 문서에서는 Conda 환경을 사용하고, 구성 파일을 만들고, Jupyter Notebooks, Azure Notebooks, Azure Databricks, IDE, 코드 편집기 및 Data Science Virtual Machine을 구성하는 방법을 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 0f53b3cec843ca8016c61a360025b5e731b96f55
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815874"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Azure Machine Learning용 개발 환경 구성

이 문서에서는 Azure Machine Learning Service에서 작동하는 개발 환경을 구성하는 방법을 알아봅니다. Azure Machine Learning Service는 플랫폼에 관계 없이 사용할 수 있습니다. 

개발 환경에 대한 유일한 요구 사항은 Python 3, Conda(격리된 환경의 경우) 및 Azure Machine Learning 작업 영역 정보를 포함하는 구성 파일입니다.

이 문서에서는 다음과 같은 환경 및 도구를 중점적으로 다룹니다.

* [Azure Notebooks](#aznotebooks): Azure 클라우드에 호스트되는 Jupyter Notebook 서비스입니다. Azure Machine Learning SDK가 이미 설치되어 있으므로 시작하기에 가장 쉬운 방법입니다.

* [DSVM(Data Science Virtual Machine)](#dsvm) 데이터 과학 작업용으로 설계되고 CPU 전용 VM 인스턴스 또는 GPU 기반 인스턴스에 배포할 수 있는 Azure 클라우드에 미리 구성된 개발 또는 실험 환경입니다. Python 3, Conda, Jupyter Notebook 및 Azure Machine Learning SDK가 이미 설치되어 있습니다. VM은 기계 학습 솔루션 개발에 자주 사용되는 기계 학습 및 딥 러닝 프레임워크, 도구, 편집기가 함께 제공됩니다. Azure 플랫폼에서 가장 완성도 높은 기계 학습용 개발 환경입니다.

* [Jupyter Notebook](#jupyter): 이미 Jupyter Notebook을 사용 중인 경우 SDK를 통해 몇 가지 요소를 추가로 설치해야 합니다.

* [Visual Studio Code](#vscode): Visual Studio Code를 사용하는 경우 몇 가지 유용한 확장 프로그램을 설치할 수 있습니다.

* [Azure Databricks](#aml-databricks): Apache Spark 기반의 인기 있는 데이터 분석 플랫폼입니다. 모델을 배포할 수 있도록 클러스터로 Azure Machine Learning SDK를 가져오는 방법을 알아봅니다.

이미 Python 3 환경을 갖고 있거나 SDK를 설치하는 데 필요한 기본 단계만 원하는 경우 [로컬 컴퓨터](#local) 섹션을 참조하세요.

## <a name="prerequisites"></a>필수 조건

- Azure Machine Learning 서비스 작업 영역. 작업 영역을 만들려면 [Azure Machine Learning Service 시작](quickstart-get-started.md)을 참조하세요.

- [Continuum Anaconda](https://www.anaconda.com/download/) 또는 [Miniconda](https://conda.io/miniconda.html) 패키지 관리자.

    > [!IMPORTANT]
    > Azure Notebooks를 사용할 때에는 Anaconda 및 Miniconda가 필요 없습니다.

- Linux 또는 Mac OS에서는 bash 셸이 필요합니다.

    > [!TIP]
    > Linux 또는 Mac OS에서 bash가 아닌 셸을 사용하는 경우(예: zsh) 일부 명령을 실행할 때 오류가 발생할 수 있습니다. 이 문제를 해결하려면 `bash` 명령을 사용하여 새 bash 셸을 시작하고 거기서 명령을 실행하세요.

- Windows에서는 명령 프롬프트 또는 Anaconda 프롬프트(Anaconda 및 Miniconda를 통해 설치한)가 필요합니다.

## <a id="aznotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com)(미리 보기)는 Azure 클라우드의 대화형 개발 환경입니다. Azure Machine Learning 개발을 시작하는 가장 쉬운 방법입니다.

* Azure Machine Learning SDK가 이미 설치되어 있습니다.
* Azure Portal에서 Azure Machine Learning Service 작업 영역을 만든 후에는 단추를 클릭하여 작업 영역과 함께 작동하도록 자동으로 Azure Notebook 환경을 구성할 수 있습니다.

Azure Notebooks로 개발을 시작하려면 [Azure Machine Learning Service 시작](quickstart-get-started.md)을 참조하세요.

기본적으로 Azure Notebooks는 메모리 4GB, 데이터 1GB로 제한되는 무료 서비스 계층을 사용합니다. 하지만 Data Science Virtual Machine 인스턴스를 Azure Notebooks 프로젝트에 연결하여 이 제한을 제거할 수 있습니다. 자세한 내용은 [Azure Notebooks 프로젝트 관리 및 구성 - 컴퓨팅 계층](/azure/notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)을 참조하세요.

## <a id="dsvm"></a>Data Science Virtual Machine

DSVM는 사용자 지정된 VM(가상 머신) 이미지입니다. 데이터 과학 작업을 위해 설계되었으며 다음 요소가 미리 구성되어 있습니다.

  - TensorFlow, PyTorch, Scikit-learn, XGBoost 및 Azure Machine Learning SDK 같은 패키지
  - Spark Standalone 및 Drill 같은 인기 있는 데이터 과학 도구
  - Azure CLI, AzCopy 및 Storage 탐색기 같은 Azure 도구
  - Visual Studio Code 및 PyCharm 같은 IDE(통합 개발 환경)
  - Jupyter Notebook 서버

Azure Machine Learning SDK는 DSVM의 Ubuntu 또는 Windows 버전에서 작동합니다. 하지만 컴퓨팅 대상으로 DSVM도 사용할 계획인 경우 Ubuntu만 지원됩니다.

개별 환경으로 DSVM을 사용하려면 다음 단계를 수행합니다.

1. 다음 환경 중 하나에서 DSVM을 만듭니다.

    * Azure Portal:

        * [Ubuntu Data Science Virtual Machine 만들기](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Windows Data Science Virtual Machine 만들기](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI:

        > [!IMPORTANT]
        > * Azure CLI를 사용하는 경우 먼저 `az login` 명령을 사용하여 Azure 구독에 로그인해야 합니다.
        >
        > * 이 단계의 명령을 사용하는 경우 리소스 그룹 이름, VM 이름, 사용자 이름 및 암호를 제공해야 합니다.

        * Ubuntu Data Science Virtual Machine을 만들려면 다음 명령을 사용합니다.

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Windows Data Science Virtual Machine을 만들려면 다음 명령을 사용합니다.

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. Azure Machine Learning SDK가 DSVM에 이미 설치되어 있습니다. SDK가 포함된 Conda 환경을 사용하려면 다음 명령 중 하나를 사용합니다.

    * Ubuntu DSVM:

        ```shell
        conda activate py36
        ```

    * Windows DSVM:

        ```shell
        conda activate AzureML
        ```

1. SDK에 액세스할 수 있는지 확인하고 버전을 검사하려면 다음 Python 코드를 사용합니다.

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Azure Machine Learning Service 작업 영역을 사용하도록 DSVM을 구성하려면 [작업 영역 구성 파일 만들기](#workspace) 섹션을 참조하세요.

자세한 내용은 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)를 참조하세요.

## <a id="local"></a>로컬 컴퓨터

로컬 컴퓨터를 사용하는 경우(원격 가상 머신이 될 수도 있음) 다음 단계에 따라 Conda 환경을 만들고 SDK를 설치합니다.

1. 명령 프롬프트 또는 셸을 엽니다.

1. 다음 명령을 사용하여 Conda 환경을 만듭니다.

    ```shell
    # create a new Conda environment with Python 3.6, NumPy, and Cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the Conda environment
    conda activate myenv

    # On macOS run
    source activate myenv
    ```

    Python 3.6 및 기타 구성 요소를 다운로드해야 하는 경우 환경을 만드는 데 몇 분 정도 걸릴 수 있습니다.

1. 다음 명령을 사용하여 Notebook 추가 기능 및 데이터 준비 SDK가 포함된 Azure Machine Learning SDK를 설치합니다.

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > PyYAML을 제거할 수 없다는 메시지가 표시되면 다음 명령을 대신 사용합니다.
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   SDK를 설치하는 데 몇 분 정도 걸릴 수 있습니다.

1. 기계 학습 실험에 필요한 패키지를 설치합니다. 다음 명령을 사용하고 *\<new package>* 를 설치하려는 패키지로 바꿉니다.

    ```shell
    conda install <new package>
    ```

1. SDK가 설치되었는지 확인하려면 다음 Python 코드를 사용합니다.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook은 [Jupyter 프로젝트](https://jupyter.org/)의 일부입니다. 라이브 코드를 설명 테스트 및 그래픽과 혼합하는 문서를 만드는 대화형 코딩 환경을 제공합니다. Jupyter Notebook은 코드 섹션의 출력을 문서에 저장할 수 있으므로 다른 사람과 결과를 공유하는 좋은 방법이기도 합니다. Jupyter Notebook은 다양한 플랫폼에 설치할 수 있습니다.

[로컬 컴퓨터](#local) 섹션의 단계에서는 Jupyter Notebook의 선택적 구성 요소를 설치합니다. Jupyter Notebook 환경에서 이러한 구성 요소를 사용하려면 다음 단계를 수행합니다.

1. 명령 프롬프트 또는 셸을 엽니다.

1. Conda를 인식하는 Jupyter Notebook 서버를 설치하려면 다음 명령을 사용합니다.

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. 다음 명령을 사용하여 Jupyter Notebook을 엽니다.

    ```shell
    jupyter notebook
    ```

1. Jupyter Notebook에서 SDK를 사용할 수 있는지 확인하려면 새 Notebook을 열고, **myenv**를 커널로 선택하고, Notebook 셀에서 다음 명령을 실행합니다.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Azure Machine Learning Service 작업 영역을 사용하도록 Jupyter Notebook을 구성하려면 [작업 영역 구성 파일 만들기](#workspace) 섹션으로 이동하세요.

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code는 크로스 플랫폼 코드 편집기입니다. 로컬 Python 3와 Python을 지원하기 위해 설치되는 Conda를 사용하지만, AI를 사용하기 위한 추가 도구를 제공합니다. 코드 편집기 내에서 Conda 환경을 선택할 수 있는 지원도 제공합니다.

개발에 Visual Studio Code를 사용하려면 다음을 수행합니다.

1. Python 개발에 Visual Studio Code를 사용하는 방법은 [VSCode에서 Python 시작](https://code.visualstudio.com/docs/python/python-tutorial)을 참조하세요.

1. Conda 환경을 선택하려면 VS Code를 열고, Ctrl-Shift-P(Linux 및 Windows) 또는 Command-Shift-P(Mac)를 선택합니다.  
    __명령 팔레트__가 열립니다. 

1. __Python: 인터프리터 선택__을 입력한 다음, Conda 환경을 선택합니다.

1. SDK를 사용할 수 있는지 확인하려면 다음 코드가 포함된 새 Python 파일(.py)을 만들고 실행합니다.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Visual Studio Code용 Azure Machine Learning 확장을 설치하려면 [AI용 도구](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)를 참조하세요.

    자세한 내용은 [Visual Studio Code용 Azure Machine Learning 사용](how-to-vscode-tools.md)을 참조하세요.

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

엔드투엔드 사용자 지정 기계 학습을 위해 Azure Databricks용 Azure Machine Learning SDK 사용자 지정 버전을 사용할 수 있습니다. 또는 [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code)를 사용하여 Databricks 내에서 모델을 학습하고 배포할 수 있습니다.

Databricks 클러스터를 준비하고 샘플 노트북을 가져오려면:

1. 다음 설정을 사용하여 [Databricks 클러스터](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)를 만듭니다.

    | 설정 | 값 |
    |----|---|
    | 클러스터 이름 | yourclustername |
    | Databricks 런타임 | 모든 비 ML 런타임(비 ML 4.x, 5.x) |
    | Python 버전 | 3 |
    | 작업자 | 2 이상 |

    Databricks에서 자동화된 기계 학습을 사용하는 경우에만 이러한 설정을 사용합니다.
    
    |   설정 | 값 |
    |----|---|
    | 작업자 노드 VM 유형 | 메모리 최적화 VM 기본 설정 |
    | 자동 크기 조정 사용 | 선택 취소 |
    
    Databricks 클러스터의 작업자 노드 수는 자동화된 ML 설정의 최대 동시 반복 수를 결정합니다.  

    클러스터를 만드는 데 몇 분이 걸립니다. 계속 진행하기 전에 클러스터가 실행될 때까지 기다립니다.

1. Azure Machine Learning SDK 패키지를 클러스터에 설치 및 연결합니다.  

    * 다음 설정 중 하나를 사용하여(_이러한 옵션 중 하나만 선택_) [라이브러리를 만듭니다](https://docs.databricks.com/user-guide/libraries.html#create-a-library).
    
        * 자동화된 기계 학습 기능 _없이_ Azure Machine Learning SDK를 설치하려면
            | 설정 | 값 |
            |----|---|
            |원본 | Python 업로드 Egg 또는 PyPI
            |PyPi 이름 | azureml-sdk[databricks]
    
        * 자동화된 기계 학습 기능과 _함께_ Azure Machine Learning SDK를 설치하려면
            | 설정 | 값 |
            |----|---|
            |원본 | Python 업로드 Egg 또는 PyPI
            |PyPi 이름 | azureml-sdk[automl_databricks]
    
    * **모든 클러스터에 자동으로 연결**을 선택하지 않습니다.

    * 클러스터 이름 옆에 있는 **연결**을 선택합니다.

    * 상태가 **연결됨**으로 변경될 때까지 오류가 없는지 확인합니다. 이 작업에 몇 분 정도가 소요됩니다.

    이전 SDK 버전이 있는 경우 클러스터의 설치된 라이브러리에서 선택을 취소하고 휴지통으로 이동합니다. 새 SDK 버전을 설치하고 클러스터를 다시 시작합니다. 그래도 문제가 있으면 클러스트를 분리했다가 다시 연결합니다.

    작업이 완료되면 다음 이미지처럼 라이브러리가 연결됩니다. 이러한 [일반적인 Databricks 문제](resource-known-issues.md#databricks)를 인식합니다.

    * 자동화된 기계 학습 _없이_ Azure Machine Learning SDK를 설치한 경우 ![Databricks에 자동화된 기계 학습이 설치되지 않은 SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

    * 자동화된 기계 학습과 _함께_ Azure Machine Learning SDK를 설치한 경우 ![Databricks에 자동화된 기계 학습이 설치된 SDK](./media/how-to-configure-environment/automlonadb.jpg)

   이 단계가 실패하면 다음을 수행하여 클러스터를 다시 시작합니다.

   a. 왼쪽 창에서 **클러스터**를 선택합니다. 
   
   b. 테이블에서 클러스터 이름을 선택합니다. 

   다. **라이브러리** 탭에서 **다시 시작**을 선택합니다.

1. [Azure Databricks/Azure Machine Learning SDK Notebook 보관 파일](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc)을 다운로드합니다.

   >[!Warning]
   > 여러 샘플 Notebooks는 Azure Machine Learning Service에서 사용할 수 있습니다. 이러한 [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)만 Azure Databricks에서 작동합니다.

1.  Databricks 클러스터로 [보관 파일을 가져오고](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) [Machine Learning Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) 페이지에 설명된 대로 검색을 시작합니다.


## <a id="workspace"></a>작업 영역 구성 파일 만들기

작업 영역 구성 파일은 Azure Machine Learning Service 작업 영역과 통신하는 방법을 SDK에 알려주는 JSON 파일입니다. 파일 이름은 *config.json*이고 형식은 다음과 같습니다.

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

이 JSON 파일은 Python 스크립트 또는 Jupyter Notebook을 포함하는 디렉터리 구조 내에 있어야 합니다. 같은 디렉터리에 있어도 되고, 하위 디렉터리 *aml_config*에 있어도 되고, 부모 디렉터리에 있어도 됩니다.

코드에서 이 파일을 사용하려면 `ws=Workspace.from_config()`를 사용합니다. 이 코드는 파일의 정보를 로드하고 작업 영역에 연결합니다.

다음과 같은 세 가지 방법으로 구성 파일을 만들 수 있습니다.

* **[Azure Machine Learning 빠른 시작](quickstart-get-started.md) 수행**: *config.json* 파일은 Azure Notebooks 라이브러리에 만들어집니다. 이 파일은 작업 영역에 대한 구성 정보를 포함합니다. *config.json* 파일을 다른 개발 환경으로 다운로드 또는 복사할 수 있습니다.

* **수동으로 파일 만들기**: 이 방법을 선택하는 경우 텍스트 편집기를 사용합니다. [Azure Portal](https://portal.azure.com)에서 작업 영역을 방문하여 구성 파일의 값을 찾을 수 있습니다. 작업 영역 이름, 리소스 그룹 및 구독 ID 값을 복사하여 구성 파일에 사용합니다.

     ![Azure portal](./media/how-to-configure-environment/configure.png)

* **프로그래밍 방식으로 파일 만들기**: 다음 코드 조각에서는 구독 ID, 리소스 그룹 및 작업 영역 이름을 입력하여 작업 영역에 연결합니다. 그런 다음, 작업 영역 구성을 파일에 저장합니다.

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

    이 코드는 *aml_config/config.json* 파일에 구성 파일을 씁니다.

## <a name="next-steps"></a>다음 단계

- [ MNIST 데이터 세트로 Azure Machine Learning에서 모델 교육](tutorial-train-models-with-aml.md)
- [Python용 Azure Machine Learning SDK](https://aka.ms/aml-sdk) 참조 보기
- [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)에 대해 알아보기
