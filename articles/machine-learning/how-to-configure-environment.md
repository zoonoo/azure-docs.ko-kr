---
title: Python 개발 환경 설정
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에 대한 개발 환경을 구성하는 방법을 알아봅니다. Conda 환경을 사용하고 구성 파일을 만들고 자체 클라우드 기반 노트북 서버, Jupyter 노트북, Azure Databricks, IID, 코드 편집기 및 데이터 과학 가상 컴퓨터를 구성합니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: a5f46f5af723e1245afbc6bca90d25ae9036d646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472429"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Azure Machine Learning용 개발 환경 구성
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습을 사용할 수 있도록 개발 환경을 구성하는 방법을 배웁니다. Azure 기계 학습은 플랫폼에 구애받지 않습니다. 개발 환경에 대한 유일한 하드 요구 사항은 파이썬 3입니다. 아나콘다 나 버추얼 렌프와 같은 고립 된 환경도 권장됩니다.

다음 표에서는 이 문서에서 다루는 각 개발 환경을 장단점과 함께 보여 줍니다.

| Environment | 장점 | 단점 |
| --- | --- | --- |
| [클라우드 기반 Azure 기계 학습 계산 인스턴스(미리 보기)](#compute-instance) | 시작하는 가장 쉬운 방법. 전체 SDK가 이미 작업 영역 VM에 설치되어 있으며 노트북 자습서를 미리 복제하여 실행할 준비가 되었습니다. | 개발 환경 및 종속성을 제어할 수 없습니다. Linux VM에 대해 발생하는 추가 비용(요금을 피하기 위해 사용하지 않을 때 VM을 중지할 수 있음). [가격 세부 정보를](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)참조하십시오. |
| [로컬 환경](#local) | 개발 환경 및 종속성을 완전히 제어합니다. 선택한 빌드 도구, 환경 또는 IDE로 실행합니다. | 시작하는 데 시간이 더 오래 걸립니다. 필요한 SDK 패키지를 설치해야 하며 아직 패키지가 없는 환경도 설치해야 합니다. |
| [Azure 데이터 브릭](#aml-databricks) | 확장 가능한 Apache Spark 플랫폼에서 대규모의 집약적 기계 학습 워크플로를 실행하는 데 적합합니다. | 실험용 기계 학습 또는 소규모 실험 및 워크플로우를 위한 과잉 조정. Azure 데이터 브릭에 대해 발생하는 추가 비용입니다. [가격 세부 정보를](https://azure.microsoft.com/pricing/details/databricks/)참조하십시오. |
| [데이터 과학 가상 머신(DSVM)](#dsvm) | 클라우드 기반 컴퓨팅 인스턴스(Python 및 SDK가 사전 설치됨)와 유사하지만 인기 있는 데이터 과학 및 기계 학습 도구가 사전 설치되어 있습니다. 다른 사용자 지정 도구 및 워크플로와 쉽게 확장하고 결합할 수 있습니다. | 클라우드 기반 컴퓨팅 인스턴스에 비해 시작 환경이 느려집니다. |

이 문서에서는 다음 도구에 대한 추가 사용 팁도 제공합니다.

* [Jupyter 노트북](#jupyter): 이미 Jupyter 노트북을 사용하고 있다면 SDK에는 설치해야 할 몇 가지 추가 파일이 있습니다.

* [Visual Studio 코드](#vscode): Visual Studio 코드를 사용하는 경우 [Azure 기계 학습 확장에는](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) Python에 대한 광범위한 언어 지원뿐만 아니라 Azure 기계 학습을 훨씬 더 편리하고 생산적인 작업으로 만드는 기능이 포함됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure 기계 학습 작업 영역 만들기를](how-to-manage-workspace.md)참조하십시오. 작업 영역은 자체 클라우드 기반 노트북 [서버,](#compute-instance) [DSVM](#dsvm)또는 [Azure Databricks를](#aml-databricks)시작하기만 하면 됩니다.

[로컬 컴퓨터,](#local) [Jupyter 노트북 서버](#jupyter) 또는 Visual Studio [코드에](#vscode) 대한 SDK 환경을 설치하려면 다음이 필요합니다.

- [아나콘다](https://www.anaconda.com/download/) 또는 [미니콘다](https://conda.io/miniconda.html) 패키지 매니저.

- Linux 또는 Mac OS에서는 bash 셸이 필요합니다.

    > [!TIP]
    > Linux 또는 Mac OS에서 bash가 아닌 셸을 사용하는 경우(예: zsh) 일부 명령을 실행할 때 오류가 발생할 수 있습니다. 이 문제를 해결하려면 `bash` 명령을 사용하여 새 bash 셸을 시작하고 거기서 명령을 실행하세요.

- Windows에서는 명령 프롬프트 또는 Anaconda 프롬프트(Anaconda 및 Miniconda를 통해 설치한)가 필요합니다.

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>자체 클라우드 기반 컴퓨팅 인스턴스

Azure 기계 학습 [계산 인스턴스(미리 보기)는](concept-compute-instance.md) 데이터 과학자에게 Jupyter 노트북 서버, JupyterLab 및 완전히 준비된 ML 환경을 제공하는 안전한 클라우드 기반 Azure 워크스테이션입니다.

계산 인스턴스에 대해 설치하거나 구성할 필요가 없습니다.  Azure 기계 학습 작업 영역 내에서 언제든지 하나를 만듭니다. 이름만 제공하고 Azure VM 유형을 지정합니다. 이 [자습서: 설치 환경 및 작업 영역을](tutorial-1st-experiment-sdk-setup.md)사용 해 보십시오.

[인스턴스 계산에](concept-compute-instance.md)대해 자세히 알아봅니다.

계산 요금 발생을 중지하려면 [계산 인스턴스를 중지합니다.](tutorial-1st-experiment-sdk-train.md#clean-up-resources)

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>데이터 과학 가상 머신

DSVM는 사용자 지정된 VM(가상 머신) 이미지입니다. 데이터 과학 작업을 위해 설계되었으며 다음 요소가 미리 구성되어 있습니다.

  - TensorFlow, PyTorch, Scikit-learn, XGBoost 및 Azure Machine Learning SDK 같은 패키지
  - Spark Standalone 및 Drill 같은 인기 있는 데이터 과학 도구
  - Azure CLI, AzCopy 및 Storage Explorer 같은 Azure 도구
  - Visual Studio Code 및 PyCharm 같은 IDE(통합 개발 환경)
  - Jupyter Notebook 서버

Azure Machine Learning SDK는 DSVM의 Ubuntu 또는 Windows 버전에서 작동합니다. 하지만 컴퓨팅 대상으로 DSVM도 사용할 계획인 경우 Ubuntu만 지원됩니다.

DSVM을 개발 환경으로 사용하려면 다음을 수행하십시오.

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

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Windows Data Science Virtual Machine을 만들려면 다음 명령을 사용합니다.

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Azure Machine Learning SDK가 DSVM에 이미 설치되어 있습니다. SDK가 포함된 Conda 환경을 사용하려면 다음 명령 중 하나를 사용합니다.

    * Ubuntu DSVM:

        ```bash
        conda activate py36
        ```

    * Windows DSVM:

        ```bash
        conda activate AzureML
        ```

1. SDK에 액세스할 수 있는지 확인하고 버전을 검사하려면 다음 Python 코드를 사용합니다.

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Azure 기계 학습 작업 영역을 사용하도록 DSVM을 구성하려면 [작업 영역 구성 파일 만들기](#workspace) 섹션을 참조하세요.

자세한 내용은 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)를 참조하세요.

## <a name="local-computer"></a><a id="local"></a>로컬 컴퓨터

원격 가상 컴퓨터일 수도 있는 로컬 컴퓨터를 사용하는 경우 Anaconda 환경을 만들고 SDK를 설치합니다. 예를 들면 다음과 같습니다.

1. 다운로드 하고 [아나콘다](https://www.anaconda.com/distribution/#download-section) (파이썬 3.7 버전) 당신이 이미없는 경우 를 설치합니다.

1. Anaconda 프롬프트를 열고 다음 명령을 사용하여 환경을 만듭니다.

    다음 명령을 실행하여 환경을 만듭니다.

    ```bash
    conda create -n myenv python=3.6.5
    ```

    그런 다음 환경을 활성화합니다.

    ```bash
    conda activate myenv
    ```

    이 예제에서는 파이썬 3.6.5를 사용하여 환경을 만들지만 특정 하위 버전을 선택할 수 있습니다. SDK 호환성은 특정 주요 버전(3.5+ 권장)과 함께 보장되지 않을 수 있으며 오류가 발생할 경우 Anaconda 환경에서 다른 버전/전복을 시도하는 것이 좋습니다. 구성 요소 및 패키지를 다운로드하는 동안에 환경을 만드는 데 몇 분 정도 걸립니다.

1. 새 환경에서 다음 명령을 실행하여 환경별 IPython 커널을 활성화합니다. 이렇게 하면 Anaconda 환경 내에서 Jupyter 노트북으로 작업할 때 예상되는 커널 및 패키지 가져오기 동작이 보장됩니다.

    ```bash
    conda install notebook ipykernel
    ```

    그런 다음 다음 명령을 실행하여 커널을 만듭니다.

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. 다음 명령을 사용하여 패키지를 설치합니다.

    이 명령은 노트북 및 `automl` 추가 기능을 갖춘 기본 Azure 기계 학습 SDK를 설치합니다. `automl` 추가 설치는 대규모 설치이며 자동화된 기계 학습 실험을 실행하지 않으려는 경우 대괄호에서 제거할 수 있습니다. 추가 `automl` 에는 기본적으로 Azure 기계 학습 데이터 준비 SDK가 종속성으로 포함되어 있습니다.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * PyYAML을 제거할 수 없다는 메시지가 표시되면 다음 명령을 대신 사용합니다.
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * macOS Catalina부터 zsh(Z 셸)는 기본 로그인 셸 및 대화형 셸입니다. zsh에서 "\\"(백슬래시)로 대괄호를 이스케이프하는 다음 명령을 사용합니다.
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   SDK를 설치하는 데 몇 분 정도 걸릴 것입니다. 설치 옵션에 대한 자세한 내용은 [설치 가이드를](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)참조하십시오.

1. 기계 학습 실험을 위해 다른 패키지를 설치합니다.

    다음 명령 중 하나를 사용하고 * \<새 패키지>* 설치하려는 패키지로 바꿉습니다. 패키지를 통해 `conda install` 패키지를 설치하려면 패키지가 현재 채널의 일부여야 합니다(Anaconda Cloud에 새 채널을 추가할 수 있음).

    ```bash
    conda install <new package>
    ```

    또는 을 통해 `pip`패키지를 설치할 수 있습니다.

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter 노트북

Jupyter Notebook은 [Jupyter 프로젝트](https://jupyter.org/)의 일부입니다. 라이브 코드를 설명 테스트 및 그래픽과 혼합하는 문서를 만드는 대화형 코딩 환경을 제공합니다. Jupyter Notebook은 코드 섹션의 출력을 문서에 저장할 수 있으므로 다른 사람과 결과를 공유하는 좋은 방법이기도 합니다. Jupyter Notebook은 다양한 플랫폼에 설치할 수 있습니다.

[로컬 컴퓨터](#local) 섹션의 절차는 아나콘다 환경에서 Jupyter 노트북을 실행하는 데 필요한 구성 요소를 설치합니다.

Jupyter 노트북 환경에서 이러한 구성 요소를 사용하려면 다음을 수행하십시오.

1. Anaconda 프롬프트를 열고 환경을 활성화합니다.

    ```bash
    conda activate myenv
    ```

1. 샘플 전자 필기장 집합에 대 한 [GitHub 리포지토리를](https://aka.ms/aml-notebooks) 복제 합니다.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Jupyter 노트북 서버를 다음 명령으로 실행합니다.

    ```bash
    jupyter notebook
    ```

1. Jupyter 노트북이 SDK를 사용할 수 있는지 확인하려면 **새** 전자 필기장을 만들고 **파이썬 3을** 커널로 선택한 다음 노트북 셀에서 다음 명령을 실행합니다.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 모듈을 `ModuleNotFoundError`가져오는 데 문제가 발생하여 다음 코드를 전자 필기장 셀에서 실행하여 Jupyter 커널이 환경에 대한 올바른 경로에 연결되어 있는지 확인합니다.

    ```python
    import sys
    sys.path
    ```

1. Azure 기계 학습 작업 영역을 사용하도록 Jupyter 노트북을 구성하려면 [작업 영역 구성 파일 만들기 섹션으로](#workspace) 이동합니다.

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio 코드는 [Visual Studio 마켓플레이스에서](https://marketplace.visualstudio.com/vscode)사용할 수 있는 확장을 통해 광범위한 프로그래밍 언어 및 도구 집합을 지원하는 매우 인기 있는 크로스 플랫폼 코드 편집기입니다. [Azure 기계 학습 확장은](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) 모든 유형의 파이썬 환경(가상, 아나콘다 등)에서 코딩할 수 있는 [파이썬 확장을](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 설치합니다. 또한 Azure 기계 학습 리소스로 작업하고 Visual Studio 코드를 떠나지 않고 Azure 기계 학습 실험을 실행하기 위한 편리한 기능을 제공합니다.

개발에 Visual Studio 코드를 사용하려면 다음을 수행하십시오.

1. 시각적 스튜디오 코드에 대한 Azure 기계 학습 확장을 설치하면 [Azure 기계 학습](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)을 참조하십시오.

    자세한 내용은 [Visual Studio Code용 Azure Machine Learning 사용](tutorial-setup-vscode-extension.md)을 참조하세요.

1. 모든 유형의 파이썬 개발에 Visual Studio 코드를 사용하는 방법에 대해 알아보려면 [VSCode에서 파이썬으로 시작하기](https://code.visualstudio.com/docs/python/python-tutorial)를 참조하십시오.

    - SDK를 포함하는 SDK 파이썬 환경을 선택하려면 VS 코드를 연 다음 Ctrl +Shift +P (리눅스와 윈도우) 또는 명령 + 시프트 + P (Mac)를 선택합니다.
        - __명령 팔레트가__ 열립니다.

    - __파이썬 입력 : 인터프리터를 선택한__다음 적절한 환경을 선택하십시오.

1. SDK를 사용할 수 있는지 확인하려면 다음 코드를 포함하는 새 Python 파일(.py)을 만듭니다.

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    "셀 실행" CodeLens를 클릭하거나 시프트 입력을 누르기만 하면 이 코드를 실행합니다.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks는 Azure 클라우드의 아파치 스파크 기반 환경입니다. CPU 또는 GPU 기반 컴퓨팅 클러스터를 통해 협업 노트북 기반 환경을 제공합니다.

Azure 데이터 브릭이 Azure 기계 학습과 함께 작동하는 방법:
+ Spark MLlib를 사용하여 모델을 학습하고 Azure Databricks 내에서 ACI/AKS에 모델을 배포할 수 있습니다.
+ Azure Databricks가 있는 특수 Azure ML SDK에서 [자동화된 기계 학습](concept-automated-ml.md) 기능을 사용할 수도 있습니다.
+ Azure Databricks를 [Azure 기계 학습 파이프라인의](concept-ml-pipelines.md)계산 대상으로 사용할 수 있습니다.

### <a name="set-up-your-databricks-cluster"></a>데이터브릭스 클러스터 설정

데이터 [벽돌 클러스터를](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)만듭니다. 일부 설정은 Databricks에서 자동화된 기계 학습을 위해 SDK를 설치하는 경우에만 적용됩니다.
**클러스터를 만드는 데 몇 분이 걸립니다.**

다음 설정을 사용합니다.

| 설정 |적용 대상| 값 |
|----|---|---|
| 클러스터 이름 |always| yourclustername |
| Databricks Runtime |always|비 ML 런타임 6.0 (스칼라 2.11, 스파크 2.4.3) |
| Python 버전 |always| 3 |
| 작업자 |always| 2 이상 |
| 작업자 노드 VM 유형 <br>(동시 반복의 최대 # 결정) |자동화된 ML<br>전용| 메모리 최적화 VM 기본 설정 |
| 자동 크기 조정 사용 |자동화된 ML<br>전용| 선택 취소 |

계속 진행하기 전에 클러스터가 실행될 때까지 기다립니다.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>올바른 SDK를 데이터브릭 라이브러리에 설치
클러스터가 실행되면 적절한 Azure 기계 학습 SDK 패키지를 클러스터에 연결하는 [라이브러리를 만듭니다.](https://docs.databricks.com/user-guide/libraries.html#create-a-library)

1. 라이브러리를 저장할 현재 작업 영역 폴더를 마우스 오른쪽 단추로 클릭합니다. 라이브러리 **만들기를** > **선택합니다.**

1. **하나의 옵션만** 선택(다른 SDK 설치는 지원되지 않음)

   |SDK&nbsp;&nbsp;패키지 엑스트라|원본|파이피&nbsp;이름&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |데이터 브릭의 경우| Python 업로드 Egg 또는 PyPI | azureml-sdk[databricks]|
   |데이터 브릭의 경우 -<br> 자동화된 ML 기능| Python 업로드 Egg 또는 PyPI | azureml-sdk[오토ml]|

   > [!Warning]
   > 다른 SDK 엑스트라를 설치할 수 없습니다. 위의 옵션 [데이터 벽돌] 또는 [automl] 중 하나만 선택합니다.

   * **모든 클러스터에 자동으로 연결**하지 마십시오.
   * 클러스터 이름 옆에 **연결옵션을** 선택합니다.

1. 몇 분 정도 걸릴 수 있는 **상태 변경**될 때까지 오류를 모니터링 합니다.  이 단계가 실패하면 다음을 수행합니다.

   다음을 수행하여 클러스터를 다시 시작해 보십시오.
   1. 왼쪽 창에서 **클러스터**를 선택합니다.
   1. 테이블에서 클러스터 이름을 선택합니다.
   1. **라이브러리** 탭에서 **다시 시작**을 선택합니다.

   또한 다음을 고려하십시오.
   + AutoML 구성에서 Azure Databricks를 사용하는 경우 다음 매개 변수를 추가합니다.
       1. ```max_concurrent_iterations```은 클러스터의 작업자 노드 수를 기반으로 합니다.
        2. ```spark_context=sc```은 기본 스파크 컨텍스트를 기반으로 합니다.
   + 또는 이전 SDK 버전이 있는 경우 클러스터의 설치된 libs에서 선택을 취소하고 휴지통으로 이동합니다. 새 SDK 버전을 설치하고 클러스터를 다시 시작합니다. 다시 시작한 후 문제가 발생하면 클러스터를 분리하고 다시 연결합니다.

설치에 성공하면 가져온 라이브러리는 다음 중 하나처럼 보일 것입니다.

데이터 브릭을 위한 **_without_** 자동화된 ![기계 학습 Azure 기계 학습 SDK 없이 데이터 브릭을 위한 SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Databricks에 설치된 **WITH** 자동화된 기계 ![학습을 갖춘 자동화된 기계 학습 SDK를 갖춘 데이터브릭용 SDK](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>다양한 콘텐츠 살펴보기

사용해보기:
+ 많은 샘플 전자 필기장을 사용할 수 있지만 ** [이러한 샘플 전자 필기장만](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) Azure Databricks에서 작동합니다.**

+ 작업 영역에서 직접 이러한 샘플을 가져옵니다. 아래 참조: ![](./media/how-to-configure-environment/azure-db-screenshot.png)
![가져오기 가져오기 패널 선택](./media/how-to-configure-environment/azure-db-import.png)

+ [교육 계산으로 Databricks를 사용하여 파이프라인을 만드는](how-to-create-your-first-pipeline.md)방법에 대해 알아봅니다.

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>작업 영역 구성 파일 만들기

작업 영역 구성 파일은 SDK에 Azure 기계 학습 작업 영역과 통신하는 방법을 알려주는 JSON 파일입니다. 파일 이름은 *config.json*이고 형식은 다음과 같습니다.

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

이 JSON 파일은 Python 스크립트 또는 Jupyter Notebook을 포함하는 디렉터리 구조 내에 있어야 합니다. 동일한 디렉터리, *.azureml*이라는 하위 디렉터리 또는 부모 디렉터리에 있을 수 있습니다.

코드에서 이 파일을 사용하려면 `ws=Workspace.from_config()`를 사용합니다. 이 코드는 파일의 정보를 로드하고 작업 영역에 연결합니다.

다음과 같은 세 가지 방법으로 구성 파일을 만들 수 있습니다.

* ** [ws.write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py):** *config.json* 파일을 작성합니다. 이 파일은 작업 영역에 대한 구성 정보를 포함합니다. *config.json* 파일을 다른 개발 환경으로 다운로드 또는 복사할 수 있습니다.

* **파일 다운로드**: [Azure 포털에서](https://ms.portal.azure.com)작업 영역의 **개요** 섹션에서 **config.json 다운로드를** 선택합니다.

     ![Azure portal](./media/how-to-configure-environment/configure.png)

* **프로그래밍 방식으로 파일 만들기**: 다음 코드 조각에서 구독 ID, 리소스 그룹 및 작업 영역 이름을 제공하여 작업 영역에 연결합니다. 그런 다음, 작업 영역 구성을 파일에 저장합니다.

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

    이 코드는 *.azureml/config.json* 파일에 구성 파일을 씁니다.

## <a name="next-steps"></a>다음 단계

- MNIST 데이터 [집합을](tutorial-train-models-with-aml.md) 사용하여 Azure 기계 학습에 대한 모델 학습
- [Python용 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 참조 보기
