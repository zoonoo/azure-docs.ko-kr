---
title: 계산 인스턴스 만들기 및 관리
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 작업 영역에서 계산 인스턴스를 만들고 관리 하는 방법에 대해 알아봅니다. 계산 인스턴스를 개발 환경으로 사용 하거나 학습 및 유추 개발/테스트 목적으로 사용 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: ca4ed58de030e372f97ebda87d12340a57a584d5
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207090"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Azure Machine Learning 계산 인스턴스 만들기 및 관리

Azure Machine Learning 작업 영역에서 [계산 인스턴스](concept-compute-instance.md) 를 만들고 관리 하는 방법에 대해 알아봅니다.

클라우드에서 완전 구성 및 관리형 개발 환경으로 컴퓨팅 인스턴스를 사용합니다. 개발 및 테스트를 위해 인스턴스를 [학습 계산 대상](concept-compute-target.md#train) 또는 [유추 대상](concept-compute-target.md#deploy)으로 사용할 수도 있습니다.   계산 인스턴스는 여러 작업을 병렬로 실행할 수 있으며 작업 큐가 있습니다. 개발 환경에서는 계산 인스턴스를 작업 영역에 있는 다른 사용자와 공유할 수 없습니다.

이 문서에서는 다음 방법을 설명합니다.

* 컴퓨팅 인스턴스 만들기 
* 계산 인스턴스 관리 (시작, 중지, 다시 시작, 삭제)
* 터미널 창 액세스 
* R 또는 Python 패키지 설치
* 새 환경 만들기 또는 Jupyter 커널

계산 인스턴스는 회사에서 SSH 포트를 열지 않아도 [가상 네트워크 환경](how-to-secure-training-vnet.md)에서 작업을 안전 하 게 실행할 수 있습니다. 작업은 컨테이너 화 된 환경에서 실행 되며 모델 종속성을 Docker 컨테이너에 패키지 합니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.

* Machine Learning 서비스, [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)또는 [Azure Machine Learning Visual Studio Code 확장](tutorial-setup-vscode-extension.md) [에 대 한 Azure CLI 확장](reference-azure-machine-learning-cli.md)입니다.

## <a name="create"></a>생성

**예상 시간**: 약 5 분.

계산 인스턴스를 만드는 작업은 작업 영역에 대 한 일회성 프로세스입니다. 이 계산을 개발 워크스테이션으로 다시 사용 하거나 학습을 위한 계산 대상으로 재사용할 수 있습니다. 여러 계산 인스턴스를 작업 영역에 연결할 수 있습니다.

VM 제품군 할당량 당 지역별 전용 코어 및 계산 인스턴스 생성에 적용 되는 총 지역 할당량은 통합 되 고 Azure Machine Learning 교육 계산 클러스터 할당량과 공유 됩니다. 계산 인스턴스를 중지 하는 경우에는 계산 인스턴스를 다시 시작할 수 있도록 할당량을 해제 하지 않습니다. 계산 인스턴스를 만든 후에는 가상 컴퓨터 크기를 변경할 수 없습니다.

다음 예에서는 계산 인스턴스를 만드는 방법을 보여 줍니다.

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

이 예제에 사용 된 클래스, 메서드 및 매개 변수에 대 한 자세한 내용은 다음 참조 문서를 참조 하세요.

* [로 서 Einstance 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance?view=azure-ml-py&preserve-view=true)
* [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py&preserve-view=true#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py&preserve-view=true#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

자세한 내용은 [az ml computetarget create caeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext_azure_cli_ml_az_ml_computetarget_create_computeinstance) reference를 참조 하세요.

# <a name="studio"></a>[스튜디오](#tab/azure-studio)

Azure Machine Learning studio의 작업 영역에서, 노트북 중 하나를 실행할 준비가 되 면 **계산** 섹션 또는 **노트북** 섹션에서 새 계산 인스턴스를 만듭니다.

스튜디오에서 계산 인스턴스를 만드는 방법에 대 한 자세한 내용은 [Azure Machine Learning studio에서 계산 대상 만들기](how-to-create-attach-compute-studio.md#compute-instance)를 참조 하세요.

---

[Azure Resource Manager 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)사용 하 여 계산 인스턴스를 만들 수도 있습니다. 

### <a name="create-on-behalf-of-preview"></a>(미리 보기)를 대신 하 여 만들기

관리자는 데이터 과학자를 대신 하 여 계산 인스턴스를 만들고 다음을 사용 하 여 인스턴스를 할당할 수 있습니다.
* [Azure Resource Manager 템플릿입니다](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  이 템플릿에 필요한 TenantID 및 ObjectID를 찾는 방법에 대 한 자세한 내용은 [인증 구성에 대 한 id 개체 Id 찾기](../healthcare-apis/find-identity-object-ids.md)를 참조 하세요.  Azure Active Directory 포털에서 이러한 값을 찾을 수도 있습니다.
* REST API

계산 인스턴스를 만드는 데이터 과학자는 다음과 같은 [Azure 역할 기반 액세스 제어 (RBAC)](../role-based-access-control/overview.md) 권한이 필요 합니다. 
* *MachineLearningServices/작업 영역/계산/시작/작업*
* *MachineLearningServices/작업 영역/계산/중지/작업*
* *MachineLearningServices/작업 영역/계산/다시 시작/작업*
* *MachineLearningServices/작업 영역/계산/applicationaccess/작업*

데이터 과학자는 계산 인스턴스를 시작, 중지 및 다시 시작할 수 있습니다. 다음에 대해 계산 인스턴스를 사용할 수 있습니다.
* Jupyter
* JupyterLab
* RStudio
* 통합 노트북

## <a name="manage"></a>관리

계산 인스턴스를 시작, 중지, 다시 시작 및 삭제 합니다. 계산 인스턴스는 자동으로 축소 되지 않으므로 리소스를 중지 하 여 지속적인 요금을 방지 해야 합니다.

# <a name="python"></a>[Python](#tab/python)

아래 예제에서 계산 인스턴스의 이름은 **instance** 입니다.

* 상태 가져오기

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* 중지

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* 시작

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* 재시작

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* DELETE

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

아래 예제에서 계산 인스턴스의 이름은 **instance** 입니다.

* 중지

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    자세한 내용은 [az ml computetarget stop](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop)를 참조 하세요.

* 시작 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    자세한 내용은 [az ml computetarget start einstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start)를 참조 하세요.

* 재시작 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    자세한 내용은 [az ml computetarget restart 확인 einstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart)를 참조 하세요.

* DELETE

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    자세한 내용은 [az ml computetarget delete einstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-delete)를 참조 하세요.

# <a name="studio"></a>[스튜디오](#tab/azure-studio)

Azure Machine Learning Studio의 작업 영역에서 **컴퓨팅**을 선택한 다음, 맨 위에 있는 **컴퓨팅 인스턴스**를 선택합니다.

![컴퓨팅 인스턴스 관리](./media/concept-compute-instance/manage-compute-instance.png)

다음 작업을 수행할 수 있습니다.

* 새 계산 인스턴스 만들기 
* 계산 인스턴스 탭을 새로 고칩니다.
* 계산 인스턴스를 시작, 중지 및 다시 시작 합니다.  인스턴스가 실행 될 때마다 해당 인스턴스에 대 한 비용을 지불 합니다. 계산 인스턴스를 사용 하 여 비용을 줄일 수 없는 경우 중지 합니다. 계산 인스턴스를 중지 하면 할당을 취소 합니다. 그런 다음, 필요할 때 다시 시작합니다.
* 계산 인스턴스를 삭제 합니다.
* 계산 인스턴스 목록을 필터링 하 여 만든 항목만 표시 합니다.

사용자가 만들었거나 만든 작업 영역의 각 계산 인스턴스에 대해 다음을 수행할 수 있습니다.

* 컴퓨팅 인스턴스에서 Jupyter, JupyterLab, RStudio에 액세스합니다.
* SSH를 컴퓨팅 인스턴스로 실행합니다. SSH 액세스는 기본적으로 사용하지 않도록 설정되어 있지만 컴퓨팅 인스턴스 생성 시 사용하도록 설정할 수 있습니다. SSH 액세스에는 공개/프라이빗 키 메커니즘이 사용됩니다. 탭에 IP 주소, 사용자 이름 및 포트 번호와 같은 SSH 연결에 대한 세부 정보가 제공됩니다.
* 특정 컴퓨팅 인스턴스에 대한 세부 정보(예: IP 주소 및 지역)를 가져옵니다.

---

[RBAC](/azure/role-based-access-control/overview)를 사용하면 작업 영역에서 컴퓨팅 인스턴스를 만들고, 삭제, 시작, 중지 및 다시 시작할 수 있는 사용자를 제어할 수 있습니다. 작업 영역 기여자 및 소유자 역할의 모든 사용자는 작업 영역에서 컴퓨팅 인스턴스를 만들고, 삭제, 시작, 중지 및 다시 시작할 수 있습니다. 그러나 특정 계산 인스턴스의 작성자나 사용자를 대신 하 여 생성 된 사용자만 해당 계산 인스턴스의 Jupyter, JupyterLab 및 RStudio에 액세스할 수 있습니다. 계산 인스턴스는 루트 액세스 권한이 있는 단일 사용자 전용 이며 Jupyter/JupyterLab/RStudio를 통해에서 터미널 할 수 있습니다. 계산 인스턴스는 단일 사용자 로그인을 포함 하 고 모든 작업은 해당 사용자의 id를 사용 하 여 RBAC 및 실험 실행의 특성을 사용 합니다. SSH 액세스는 공개/프라이빗 키 메커니즘을 통해 제어됩니다.

이러한 작업은 RBAC로 제어할 수 있습니다.
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *MachineLearningServices/작업 영역/계산/시작/작업*
* *MachineLearningServices/작업 영역/계산/중지/작업*
* *MachineLearningServices/작업 영역/계산/다시 시작/작업*


## <a name="access-the-terminal-window"></a>터미널 창 액세스

다음 방법 중 하나로 계산 인스턴스의 터미널 창을 엽니다.

* RStudio: 왼쪽 위에서 **터미널** 탭을 선택합니다.
* Jupyter Lab:  시작 관리자 탭의 **기타** 제목 아래에 있는 **터미널** 타일을 선택합니다.
* Jupyter:  파일 탭에서 오른쪽 위에 있는 **새로 만들기 > 터미널**을 선택합니다.
* 컴퓨터에 SSH를 사용 하도록 설정 하는 경우 컴퓨터에 SSH를 사용 합니다.

터미널 창을 사용 하 여 패키지를 설치 하 고 추가 커널을 만들 수 있습니다.

## <a name="install-packages"></a>패키지 설치

Jupyter Notebook 또는 RStudio에 직접 패키지를 설치할 수 있습니다.

* RStudio는 오른쪽 아래에 있는 **패키지** 탭 또는 왼쪽 위의 **콘솔** 탭을 사용합니다.  
* Python: 설치 코드를 추가 하 고 Jupyter Notebook 셀에를 실행 합니다.

또는 터미널 창에서를 설치할 수 있습니다. Python **3.6-AzureML** 환경에 python 패키지를 설치 합니다.  **R** 환경에 R 패키지를 설치합니다.
% pip 및% conda magic 함수는 Jupyter 노트북 세션에서 현재 실행 중인 커널에 패키지를 자동으로 설치 합니다.

## <a name="add-new-kernels"></a>새 커널 추가

> [!WARNING]
>  계산 인스턴스를 사용자 지정 하는 동안 **azureml_py36** conda 환경 또는 **Python 3.6-azureml** 커널을 삭제 하지 않아야 합니다. Jupyter/JupyterLab 기능에 필요 합니다.

새 Jupyter 커널을 계산 인스턴스에 추가 하려면 다음을 수행 합니다.

1. Jupyter, JupyterLab 또는 노트북 창에서 또는 SSH에서 계산 인스턴스로 새 터미널 만들기
2. 터미널 창을 사용하여 새 환경을 만듭니다.  예를 들어 아래 코드는를 만듭니다 `newenv` .

    ```shell
    conda create --name newenv
    ```

3. 환경을 활성화합니다.  예를 들어 `newenv`를 만든 후에는 다음을 수행합니다.

    ```shell
    conda activate newenv
    ```

4. 새 환경에 pip 및 ipykernel 패키지를 설치 하 고 해당 conda env의 커널 만들기

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

[사용할 수 있는 Jupyter 커널](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)을 설치할 수 있습니다.



## <a name="next-steps"></a>다음 단계

* [학습 실행 제출](how-to-set-up-training-targets.md) 
