---
title: 컴퓨팅 인스턴스 만들기 및 관리
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 컴퓨팅 인스턴스 만들고 관리하는 방법을 알아봅니다. 개발 환경으로 사용하거나 개발/테스트를 위해 컴퓨팅 대상으로 사용합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli, references_regions
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: db6414ecf4b1b5fcbdf52d59c0c79b72998e610a
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110375219"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Azure Machine Learning 컴퓨팅 인스턴스 만들고 관리

Azure Machine Learning 작업 영역에서 [컴퓨팅 인스턴스](concept-compute-instance.md)를 만들고 관리하는 방법에 대해 알아봅니다.

클라우드에서 완전 구성 및 관리형 개발 환경으로 컴퓨팅 인스턴스를 사용합니다. 개발 및 테스트를 위해 인스턴스를 [학습 컴퓨팅 대상](concept-compute-target.md#train) 또는 [유추 대상](concept-compute-target.md#deploy)으로 사용할 수도 있습니다.   컴퓨팅 인스턴스는 여러 작업을 병렬로 실행할 수 있으며 1개의 작업 큐가 있습니다. 개발 환경에서는 계산 인스턴스를 작업 영역에 있는 다른 사용자와 공유할 수 없습니다.

이 문서에서는 다음 방법을 설명합니다.

* 컴퓨팅 인스턴스 생성
* 컴퓨팅 인스턴스 관리(시작, 중지, 다시 시작, 삭제)
* 터미널 창 액세스
* R 또는 Python 패키지 설치
* 새 환경 만들기 또는 Jupyter 커널

컴퓨팅 인스턴스를 사용하면 기업에서 SSH 포트를 열지 않아도 [가상 네트워크 환경](how-to-secure-training-vnet.md)에서 작업을 안전하게 실행할 수 있습니다. 작업은 컨테이너화된 환경에서 실행되며 모델 종속성을 Docker 컨테이너로 패키지합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* [Machine Learning 서비스를 위한 Azure CLI 확장](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) 또는 [Azure Machine Learning Visual Studio Code 확장](how-to-setup-vs-code.md).

## <a name="create"></a>생성

> [!IMPORTANT]
> 아래에 표시된 항목(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

**예상 시간**: 약 5분이 소요됩니다.

컴퓨팅 인스턴스 만들기는 작업 영역에 대한 일회성 프로세스입니다. 컴퓨팅을 개발 워크스테이션으로 다시 사용하거나 학습을 위한 계산 대상으로 재사용할 수 있습니다. 여러 컴퓨팅 인스턴스를 작업 영역에 연결할 수 있습니다.

컴퓨팅 인스턴스 만들기에 적용되는 VM 제품군 할당량 및 총 지역 할당량당 지역별 전용 코어는 Azure Machine Learning 학습 컴퓨팅 클러스터 할당량과 통합 및 공유됩니다. 컴퓨팅 인스턴스를 중지해도 컴퓨팅 인스턴스를 다시 시작할 수 있도록 할당량이 해제되지 않습니다. 컴퓨팅 인스턴스를 만든 후에는 가상 머신 크기를 변경할 수 없습니다.

다음 예에서는 컴퓨팅 인스턴스를 만드는 방법을 보여줍니다.

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

이 예제에 사용된 클래스, 메서드 및 매개 변수에 대한 자세한 내용은 다음 참조 문서를 확인하세요.

* [ComputeInstance 클래스](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

자세한 내용은 [az ml computetarget create computeinstance](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_computeinstance) 참조를 확인하세요.

# <a name="studio"></a>[스튜디오](#tab/azure-studio)

Azure Machine Learning 스튜디오의 작업 영역에서 노트북 중 하나를 실행할 준비가 되면 **계산** 섹션이나 **노트북** 섹션에서 새 계산 인스턴스를 만듭니다.

스튜디오에서 컴퓨팅 인스턴스를 만드는 방법에 대한 자세한 내용은 [Azure Machine Learning 스튜디오에서 컴퓨팅 대상 만들기](how-to-create-attach-compute-studio.md#compute-instance)를 참조하세요.

---

[Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)을 사용하여 계산 인스턴스를 만들 수도 있습니다.



## <a name="create-on-behalf-of-preview"></a><a name="on-behalf"></a> 대신 만들기(미리 보기)

관리자는 데이터 과학자를 대신하여 컴퓨팅 인스턴스를 만들고 다음을 통해 해당 인스턴스를 할당할 수 있습니다.

* [Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance).  이 템플릿에 필요한 TenantID 및 ObjectID를 찾는 방법에 대한 자세한 내용은 [인증 구성에 대한 ID 개체 ID 찾기](../healthcare-apis/fhir/find-identity-object-ids.md)를 참조하세요.  Azure Active Directory 포털에서 이러한 값을 찾을 수도 있습니다.

* REST API

컴퓨팅 인스턴스를 만드는 데이터 과학자에게는 다음과 같은 [Azure RBAC(Azure Role-based Access Control)](../role-based-access-control/overview.md) 사용 권한이 필요합니다.
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*

데이터 과학자는 컴퓨팅 인스턴스를 시작, 중지, 다시 시작할 수 있습니다. 다음에 대해 컴퓨팅 인스턴스를 사용할 수 있습니다.
* Jupyter
* JupyterLab
* RStudio
* 통합 Notebook

## <a name="customize-the-compute-instance-with-a-script-preview"></a><a name="setup-script"></a> 스크립트를 사용하여 컴퓨팅 인스턴스 사용자 지정(미리 보기)

자동화된 방법으로 설치 스크립트를 사용하여 프로비전 시 컴퓨팅 인스턴스를 사용자 지정하고 구성합니다. 관리자는 요구 사항에 따라 작업 영역에서 모든 컴퓨팅 인스턴스를 프로비전하는 데 사용할 사용자 지정 스크립트를 작성할 수 있습니다.

설치 스크립트에서 수행할 수 있는 작업에 대한 몇 가지 예제는 다음과 같습니다.

* 패키지, 도구 및 소프트웨어 설치
* 데이터 탑재
* 사용자 지정 conda 환경 및 Jupyter 커널 만들기
* git 리포지토리 복제 및 git 구성 설정
* 네트워크 프록시 설정
* 환경 변수 설정
* JupyterLab 확장 설치

### <a name="create-the-setup-script"></a>설치 스크립트 만들기

설치 스크립트는 *rootuser* 로 실행되는 셸 스크립트입니다.  **Notebooks** 파일에 스크립트를 만들거나 업로드합니다.

1. [스튜디오](https://ml.azure.com)에 로그인하고 작업 영역을 선택합니다.
2. 왼쪽에서 **Notebooks** 를 선택합니다.
3. **파일 추가** 도구를 사용하여 설치 셸 스크립트를 만들거나 업로드합니다.  스크립트 파일 이름이 ".sh"로 끝나는지 확인합니다.  새 파일을 만들 때 **파일 형식** 을 *bash(.sh)* 로 변경합니다.

:::image type="content" source="media/how-to-create-manage-compute-instance/create-or-upload-file.png" alt-text="스튜디오에서 Notebooks 파일에 설치 스크립트 만들기 또는 업로드":::

스크립트가 실행될 때 스크립트의 현재 작업 디렉터리는 파일이 업로드된 디렉터리입니다. 예를 들어 **사용자>관리자** 에 스크립트를 업로드하는 경우 스크립트가 실행될 때 컴퓨팅 인스턴스 및 현재 작업 디렉터리에 대한 스크립트의 위치는 */home/azureuser/cloudfiles/code/Users/admin* 입니다. 이렇게 하면 스크립트에서 상대 경로를 사용할 수 있습니다.

스크립트 인수를 스크립트에서 $1, $2 등으로 참조할 수 있습니다.

스크립트가 conda 환경 또는 jupyter 커널을 설치하는 등 azureuser와 관련된 작업을 수행하는 경우 다음과 같이 *sudo -u azureuser* 블록 내에 배치해야 합니다.

```shell
sudo -u azureuser -i <<'EOF'

EOF
```
*sudo -u azureuser* 는 현재 작업 디렉터리를 */home/azureuser* 로 변경합니다. 또한 이 블록의 스크립트 인수에 액세스할 수 없습니다.

스크립트에서 다음 환경 변수를 사용할 수도 있습니다.

1. CI_RESOURCE_GROUP
2. CI_WORKSPACE
3. CI_NAME
4. CI_LOCAL_UBUNTU_USER. azureuser를 가리킵니다.

### <a name="use-the-script-in-the-studio"></a>스튜디오에서 스크립트 사용

스크립트를 저장한 후 컴퓨팅 인스턴스를 만드는 동안 지정합니다.

1. [스튜디오](https://ml.azure.com/)에 로그인하고 작업 영역을 선택합니다.
1. 왼쪽에서 **Compute** 를 선택합니다.
1. 새 컴퓨팅 인스턴스를 만들려면 **+ 새로 만들기** 를 선택합니다.
1. [양식을 작성합니다](how-to-create-attach-compute-studio.md#compute-instance).
1. 양식의 두 번째 페이지에서 **고급 설정 표시** 를 엽니다.
1. **설치 스크립트를 사용하여 프로비전** 켜기
1. 저장한 셸 스크립트를 찾습니다.  또는 컴퓨터에서 스크립트를 업로드합니다.
1. 필요에 따라 명령 인수를 추가합니다.

:::image type="content" source="media/how-to-create-manage-compute-instance/setup-script.png" alt-text="Studio에서 설치 스크립트를 사용하여 컴퓨팅 인스턴스를 프로비전합니다.":::

작업 영역 스토리지가 가상 네트워크에 연결된 경우 가상 네트워크 내에서 Studio에 액세스하지 않는 한 설치 스크립트 파일에 액세스할 수 없습니다.

### <a name="use-script-in-a-resource-manager-template"></a>Resource Manager 템플릿에서 스크립트를 사용

Resource Manager [템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)에서 `setupScripts`를 추가하여 컴퓨팅 인스턴스가 프로비전될 때 설치 스크립트를 호출합니다. 예를 들면 다음과 같습니다.

```json
"setupScripts":{
    "scripts":{
        "creationScript":{
        "scriptSource":"workspaceStorage",
        "scriptData":"[parameters('creationScript.location')]",
        "scriptArguments":"[parameters('creationScript.cmdArguments')]"
        }
    }
}
```

대신 Resource Manager 템플릿에 대한 스크립트 인라인을 제공할 수 있습니다.  셸 명령은 Notebooks 파일 공유에 업로드된 종속성을 참조할 수 있습니다.  인라인 문자열을 사용하는 경우 스크립트의 작업 디렉터리는 */mnt/batch/tasks/shared/LS_root/mounts/clusters/**ciname**/code/Users* 입니다.

예를 들어 `scriptData`에 대한 base64 인코딩 명령 문자열을 지정합니다.

```json
"setupScripts":{
    "scripts":{
        "creationScript":{
        "scriptSource":"inline",
        "scriptData":"[base64(parameters('inlineCommand'))]",
        "scriptArguments":"[parameters('creationScript.cmdArguments')]"
        }
    }
}
```

### <a name="setup-script-logs"></a>스크립트 로그 설정

설치 스크립트 실행의 로그는 컴퓨팅 인스턴스 세부 정보 페이지의 로그 폴더에 나타납니다. 로그는 Logs\<compute instance name> 폴더 아래의 Notebooks 파일 공유에 다시 저장됩니다. 특정 컴퓨팅 인스턴스에 대한 스크립트 파일 및 명령 인수가 세부 정보 페이지에 표시됩니다.

## <a name="manage"></a>관리

컴퓨팅 인스턴스를 시작, 중지 다시 시작, 삭제합니다. 컴퓨팅 인스턴스는 자동으로 축소되지 않으므로 리소스를 중지하여 지속적으로 요금이 청구되지 않도록 방지해야 합니다. 컴퓨팅 인스턴스를 중지하면 할당을 취소합니다. 그런 다음, 필요할 때 다시 시작합니다. 컴퓨팅 인스턴스를 중지하면 컴퓨팅 시간에 대한 청구는 중지되지만 디스크, 공용 IP, 표준 부하 분산 장치에 대한 비용은 여전히 청구됩니다.

> [!TIP]
> 컴퓨팅 인스턴스에 120GB OS 디스크가 있습니다. 디스크 공간이 부족한 경우에는 [터미널을 사용](how-to-access-terminal.md)하여 최소 1~2GB를 지운 후 컴퓨팅 인스턴스를 중지하거나 다시 시작해야 합니다.

# <a name="python"></a>[Python](#tab/python)

아래의 예제에서 컴퓨팅 인스턴스의 이름은 **instance** 입니다.

* 상태 가져오기

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Stop

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

아래의 예제에서 컴퓨팅 인스턴스의 이름은 **instance** 입니다.

* Stop

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    자세한 내용은 [az ml computetarget stop computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop)를 참조하세요.

* 시작

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    자세한 내용은 [az ml computetarget start computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_start)를 참조하세요.

* 재시작

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    자세한 내용은 [az ml computetarget restart computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart)를 참조하세요.

* DELETE

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    자세한 내용은 [az ml computetarget delete computeinstance](/cli/azure/ml/computetarget#az_ml_computetarget_delete)를 참조하세요.

# <a name="studio"></a>[스튜디오](#tab/azure-studio)

Azure Machine Learning Studio의 작업 영역에서 **컴퓨팅** 을 선택한 다음, 맨 위에 있는 **컴퓨팅 인스턴스** 를 선택합니다.

![컴퓨팅 인스턴스 관리](./media/concept-compute-instance/manage-compute-instance.png)

다음 작업을 수행할 수 있습니다.

* 새 컴퓨팅 인스턴스 만들기
* 컴퓨팅 인스턴스 탭을 새로 고침합니다.
* 컴퓨팅 인스턴스를 시작, 중지, 다시 시작합니다.  인스턴스가 실행될 때마다 비용을 지불해야 합니다. 비용을 줄이기 위해 컴퓨팅 인스턴스를 사용하지 않을 때는 이를 중지합니다. 컴퓨팅 인스턴스를 중지하면 할당을 취소합니다. 그런 다음, 필요할 때 다시 시작합니다.
* 컴퓨팅 인스턴스를 삭제합니다.
* 생성한 컴퓨팅 인스턴스만 표시하도록 컴퓨팅 인스턴스 목록을 필터링합니다.

사용자가 만들었거나 사용자를 위해 만들어진 작업 영역의 각 계산 인스턴스에 대해 다음을 수행할 수 있습니다.

* 컴퓨팅 인스턴스에서 Jupyter, JupyterLab, RStudio에 액세스합니다.
* SSH를 컴퓨팅 인스턴스로 실행합니다. SSH 액세스는 기본적으로 사용하지 않도록 설정되어 있지만 컴퓨팅 인스턴스 생성 시 사용하도록 설정할 수 있습니다. SSH 액세스에는 공개/프라이빗 키 메커니즘이 사용됩니다. 탭에 IP 주소, 사용자 이름 및 포트 번호와 같은 SSH 연결에 대한 세부 정보가 제공됩니다.
* 특정 컴퓨팅 인스턴스에 대한 세부 정보(예: IP 주소 및 지역)를 가져옵니다.

---

[Azure RBAC](../role-based-access-control/overview.md)를 사용하면 작업 영역에서 컴퓨팅 인스턴스를 생성, 삭제, 시작, 중지, 다시 시작할 수 있는 사용자를 제어할 수 있습니다. 작업 영역 기여자 및 소유자 역할의 모든 사용자는 작업 영역에서 컴퓨팅 인스턴스를 만들고, 삭제, 시작, 중지 및 다시 시작할 수 있습니다. 그러나 특정 계산인스턴스의 작성자나 사용자를 대신하여 생성된 사용자만 해당 계산인스턴스의 Jupyter, JupyterLab 및 RStudio에 액세스할 수 있습니다. 컴퓨팅 인스턴스는 루트 액세스 권한이 있는 단일 사용자 전용이며 Jupyter/JupyterLab/RStudio를 통해 터미널에 들어갈 수 있습니다. 컴퓨팅 인스턴스는 단일 사용자 로그인을 사용하며 모든 작업에는 Azure RBAC에 대한 사용자의 ID 및 실험 실행의 특성이 사용됩니다. SSH 액세스는 공개/프라이빗 키 메커니즘을 통해 제어됩니다.

Azure RBAC는 다음 작업을 제어할 수 있습니다.
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

컴퓨팅 인스턴스를 만들려면 다음 작업에 대한 사용 권한이 있어야 합니다.
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action*


## <a name="next-steps"></a>다음 단계

* [컴퓨팅 인스턴스 터미널에 액세스](how-to-access-terminal.md)
* [파일 만들기 및 관리](how-to-manage-files.md)
* [학습 실행 제출](how-to-set-up-training-targets.md)
