---
title: Python 개발 환경 설정
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에 대 한 Python 개발 환경을 설정 하는 방법을 알아봅니다. Conda 환경을 사용 하 여 구성 파일을 만들고 고유한 클라우드 기반 노트북 서버, Jupyter 노트북, Azure Databricks, Ide, 코드 편집기 및 Data Science Virtual Machine를 구성 합니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1, devx-track-azurecli
ms.openlocfilehash: 451ad33a9d041635c3f51e323539b423378d02d1
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422906"
---
# <a name="set-up-a-python-development-environment-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 Python 개발 환경 설정

Azure Machine Learning에 대 한 Python 개발 환경을 구성 하는 방법을 알아봅니다.

다음 표에서는 이 문서에서 다루는 각 개발 환경을 장단점과 함께 보여 줍니다.

| Environment | 장점 | 단점 |
| --- | --- | --- |
| [로컬 환경](#local) | 개발 환경 및 종속성에 대 한 모든 권한 원하는 빌드 도구, 환경 또는 IDE를 사용 하 여를 실행 합니다. | 시작 하는 데 시간이 더 오래 걸립니다. 필요한 SDK 패키지를 설치 해야 하 고, 아직 설치 되어 있지 않은 경우에도 환경을 설치 해야 합니다. |
| [Data Science Virtual Machine (DSVM)](#dsvm) | 클라우드 기반 계산 인스턴스 (Python 및 SDK는 사전 설치 됨)와 비슷하지만 널리 사용 되는 추가 데이터 과학 및 기계 학습 도구가 미리 설치 되어 있습니다. 쉽게 확장 하 고 다른 사용자 지정 도구 및 워크플로와 결합할 수 있습니다. | 클라우드 기반 계산 인스턴스와 비교 하 여 더 느린 시작 환경을 제공 합니다. |
| [Azure Machine Learning 컴퓨팅 인스턴스](#compute-instance) | 시작 하는 가장 쉬운 방법입니다. 전체 SDK는 작업 영역 VM에 이미 설치 되어 있으며, 노트북 자습서는 미리 복제 되어 실행할 준비가 되었습니다. | 개발 환경 및 종속성에 대 한 제어가 부족 합니다. Linux VM에 대해 발생 하는 추가 비용입니다 (요금을 방지 하기 위해 사용 하지 않을 때 VM을 중지할 수 있음). [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)를 참조하세요. |
| [Azure Databricks](how-to-configure-databricks-automl-environment.md) | 확장 가능한 Apache Spark 플랫폼에서 대규모의 집약적 기계 학습 워크플로를 실행하는 데 적합합니다. | 실험적 기계 학습 또는 작은 규모의 실험 및 워크플로를 위한 과잉. Azure Databricks에 대해 발생 하는 추가 비용입니다. [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/databricks/)를 참조하세요. |

또한이 문서에서는 다음 도구에 대 한 추가 사용 팁을 제공 합니다.

* Jupyter Notebook: 이미 Jupyter Notebook을 사용 중인 경우 SDK를 통해 몇 가지 요소를 추가로 설치해야 합니다.

* Visual Studio Code: Visual Studio Code를 사용 하는 경우 [Azure Machine Learning 확장](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) 에는 Python에 대 한 광범위 한 언어 지원 뿐만 아니라 Azure Machine Learning 훨씬 더 편리 하 고 생산적으로 작업할 수 있는 기능이 포함 되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure Machine Learning 작업 영역입니다. 없는 경우 [Azure Portal](how-to-manage-workspace.md), [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)및 [Azure Resource Manager 템플릿을](how-to-create-workspace-template.md)통해 Azure Machine Learning 작업 영역을 만들 수 있습니다.

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> 로컬 및 DSVM만 해당: 작업 영역 구성 파일 만들기

작업 영역 구성 파일은 SDK에서 Azure Machine Learning 작업 영역과 통신 하는 방법을 알려 주는 JSON 파일입니다. 파일 이름은 *config.json* 이고 형식은 다음과 같습니다.

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

이 JSON 파일은 Python 스크립트 또는 Jupyter Notebook을 포함하는 디렉터리 구조 내에 있어야 합니다. 동일한 디렉터리, *.azureml* 이라는 하위 디렉터리 또는 부모 디렉터리에 있을 수 있습니다.

코드에서이 파일을 사용 하려면 메서드를 사용 [`Workspace.from_config`](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#from-config-path-none--auth-none---logger-none---file-name-none-&preserve-view=true) 합니다. 이 코드는 파일의 정보를 로드하고 작업 영역에 연결합니다.

다음 방법 중 하나를 수행 하 여 작업 영역 구성 파일을 만듭니다.

* Azure portal

    **파일 다운로드** : [Azure Portal](https://ms.portal.azure.com)의 작업 영역 **개요** 섹션에서 **config.js켜기** 를 선택 합니다.

    ![Azure portal](./media/how-to-configure-environment/configure.png)

* Azure Machine Learning Python SDK

    Azure Machine Learning 작업 영역에 연결 하는 스크립트를 만들고 메서드를 사용 하 여 [`write_config`](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#write-config-path-none--file-name-none-&preserve-view=true) 파일을 생성 하 고를 *azureml/config.js에* 저장 합니다. , 및를 사용자 `subscription_id` 고유의으로 바꾸어야 `resource_group` `workspace_name` 합니다.

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

## <a name="local-computer-or-remote-vm-environment"></a><a id="local"></a>로컬 컴퓨터 또는 원격 VM 환경

로컬 컴퓨터 또는 원격 가상 컴퓨터 (예: Azure Machine Learning 계산 인스턴스 또는 Data Science VM)에서 환경을 설정할 수 있습니다. 

로컬 개발 환경 또는 원격 VM을 구성 하려면:

1. Python 가상 환경 (virtualenv, conda)을 만듭니다.

    > [!NOTE]
    > 필수는 아니지만 [Anaconda](https://www.anaconda.com/download/) 또는 [miniconda](https://www.anaconda.com/download/) 를 사용 하 여 Python 가상 환경을 관리 하 고 패키지를 설치 하는 것이 좋습니다.

    > [!IMPORTANT]
    > Linux 또는 Mac OS에서 bash가 아닌 셸을 사용하는 경우(예: zsh) 일부 명령을 실행할 때 오류가 발생할 수 있습니다. 이 문제를 해결하려면 `bash` 명령을 사용하여 새 bash 셸을 시작하고 거기서 명령을 실행하세요.

1. 새로 만든 Python 가상 환경을 활성화 합니다.
1. [Azure Machine Learning PYTHON SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)를 설치 합니다.
1. Azure Machine Learning 작업 영역을 사용 하도록 로컬 환경을 구성 하려면 [작업 영역 구성 파일을 만들거나 기존 작업](#workspace) 영역을 사용 합니다.

로컬 환경이 설정 되었으므로 이제 Azure Machine Learning 작업을 시작할 준비가 되었습니다. 시작 하려면 [Python 시작 가이드 Azure Machine Learning](tutorial-1st-experiment-sdk-setup-local.md) 를 참조 하세요.

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebook

로컬 Jupyter Notebook 서버를 실행 하는 경우 Python 가상 환경에 대 한 IPython 커널을 만드는 것이 좋습니다. 이는 예상 되는 커널 및 패키지 가져오기 동작을 보장 하는 데 도움이 됩니다.

1. 환경 관련 IPython 커널 사용

    ```bash
    conda install notebook ipykernel
    ```

1. Python 가상 환경에 대 한 커널을 만듭니다. 을 `<myenv>` Python 가상 환경의 이름으로 바꾸어야 합니다.

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. Jupyter Notebook 서버를 시작 합니다.

Azure Machine Learning 및 Jupyter 노트북을 시작 하려면 [Azure Machine Learning 노트북 리포지토리](https://github.com/Azure/MachineLearningNotebooks) 를 참조 하세요.

> [!NOTE]
> 커뮤니티 기반 예제 리포지토리는 https://github.com/Azure/azureml-examples 에서 찾을 수 있습니다.

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

개발에 Visual Studio Code를 사용 하려면 다음을 수행 합니다.

1. [Visual Studio Code](https://code.visualstudio.com/Download)를 설치합니다.
1. [Azure Machine Learning Visual Studio Code 확장](tutorial-setup-vscode-extension.md) (미리 보기)을 설치 합니다.

Visual Studio Code 확장을 설치한 후에는 [Azure Machine Learning 리소스](how-to-manage-resources-vscode.md)를 관리 하 고, [실험을 실행 및 디버그](how-to-debug-visual-studio-code.md)하 고, [학습 된 모델을 배포할](tutorial-train-deploy-image-classification-model-vscode.md)수 있습니다.

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Azure Machine Learning 계산 인스턴스

Azure Machine Learning [계산 인스턴스](concept-compute-instance.md) 는 데이터 과학자에 Jupyter Notebook 서버, JupyterLab 및 완전히 관리 되는 Machine Learning 환경을 제공 하는 클라우드 기반 보안 워크스테이션입니다.

계산 인스턴스를 설치 하거나 구성할 수 없습니다.  

Azure Machine Learning 작업 영역 내에서 언제 든 지 하나를 만듭니다. 이름을 입력 하 고 Azure VM 유형을 지정 합니다. [설정 환경 및 작업 영역](tutorial-1st-experiment-sdk-setup.md)을 사용 하 여 지금 사용해 보세요.

패키지를 설치 하는 방법을 비롯 하 여 계산 인스턴스에 대해 자세히 알아보려면 [Azure Machine Learning 계산 인스턴스 만들기 및 관리](how-to-create-manage-compute-instance.md)를 참조 하세요.

> [!TIP]
> 사용 하지 않는 계산 인스턴스에 대 한 요금 발생을 방지 하려면 [계산 인스턴스를 중지](how-to-create-manage-compute-instance.md#manage)합니다.

Jupyter Notebook 서버 및 JupyterLab 외에도 [Azure Machine Learning studio 내의 통합 된 노트북 기능](how-to-run-jupyter-notebooks.md)에서 계산 인스턴스를 사용할 수 있습니다.

Azure Machine Learning Visual Studio Code 확장을 사용 하 여 [Azure Machine Learning 계산 인스턴스를 원격 Jupyter Notebook 서버로 구성할](how-to-set-up-vs-code-remote.md#configure-compute-instance-as-remote-notebook-server)수도 있습니다.

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Data Science Virtual Machine

Data Science VM은 개발 환경으로 사용할 수 있는 사용자 지정 VM (가상 머신) 이미지입니다. 이는 다음과 같은 미리 구성 된 도구 및 소프트웨어용 데이터 과학 작업을 위해 설계 되었습니다.

  - TensorFlow, PyTorch, Scikit-learn, XGBoost 및 Azure Machine Learning SDK 같은 패키지
  - Spark Standalone 및 Drill 같은 인기 있는 데이터 과학 도구
  - Azure CLI, AzCopy 및 Storage Explorer 같은 Azure 도구
  - Visual Studio Code 및 PyCharm 같은 IDE(통합 개발 환경)
  - Jupyter Notebook 서버

보다 포괄적인 도구 목록은 [Data Science VM 도구 가이드](data-science-virtual-machine/tools-included.md)를 참조 하세요.

> [!IMPORTANT]
> Data Science VM를 학습 또는 추론 작업에 대 한 [계산 대상](concept-compute-target.md) 으로 사용할 계획인 경우 Ubuntu만 지원 됩니다.

Data Science VM를 개발 환경으로 사용 하려면 다음을 수행 합니다.

1. 다음 방법 중 하나를 사용 하 여 Data Science VM를 만듭니다.

    * Azure Portal를 사용 하 여 [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) 또는 [Windows](data-science-virtual-machine/provision-vm.md) dsvm을 만듭니다.
    * [ARM 템플릿을 사용 하 여 Data Science VM를 만듭니다](data-science-virtual-machine/dsvm-tutorial-resource-manager.md).
    * Azure CLI 사용

        Ubuntu Data Science VM를 만들려면 다음 명령을 사용 합니다.

        ```azurecli-interactive
        # create a Ubuntu Data Science VM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Windows DSVM을 만들려면 다음 명령을 사용 합니다.

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Azure Machine Learning SDK를 포함 하는 conda 환경을 활성화 합니다.

    * Ubuntu Data Science VM:

        ```bash
        conda activate py36
        ```

    * Windows Data Science VM:

        ```bash
        conda activate AzureML
        ```

1. Azure Machine Learning 작업 영역을 사용 하도록 Data Science VM를 구성 하려면 [작업 영역 구성 파일을 만들거나 기존 작업 영역](#workspace) 을 사용 합니다.

로컬 환경과 마찬가지로 Visual Studio Code와 [Azure Machine Learning Visual Studio Code 확장](#vscode) 을 사용 하 여 Azure Machine Learning와 상호 작용할 수 있습니다.

자세한 내용은 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- MNIST 데이터 집합을 사용 하 여 Azure Machine Learning에 대 한 [모델을 학습](tutorial-train-models-with-aml.md) 합니다.
- [AZURE MACHINE LEARNING SDK For Python 참조](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)를 참조 하세요. 