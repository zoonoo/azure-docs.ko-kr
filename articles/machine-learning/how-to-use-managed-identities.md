---
title: 액세스 제어에 관리 되는 id 사용 (미리 보기)
titleSuffix: Azure Machine Learning
description: 관리 id를 사용 하 여 Azure Machine Learning 작업 영역에서 Azure 리소스에 대 한 액세스를 제어 하는 방법을 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 6bcc4ac5561a8bdb721018aa05bf2376579b627b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079679"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Azure Machine Learning에서 관리 되는 id 사용 (미리 보기)

[관리 id](/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하 여 *리소스에 액세스 하는 데 필요한 최소 권한*으로 작업 영역을 구성할 수 있습니다. 

Azure Machine Learning 작업 영역을 신뢰할 수 있는 방식으로 구성 하는 경우 작업 영역과 연결 된 여러 서비스에 올바른 액세스 수준이 있는지 확인 하는 것이 중요 합니다. 예를 들어 기계 학습 워크플로 중 작업 영역에서 Docker 이미지에 대 한 Azure Container Registry (ACR)에 액세스 하 고 학습 데이터에 대 한 저장소 계정을 사용 해야 합니다. 

또한 관리 되는 id를 사용 하면 사용 권한에 대 한 세분화 된 제어를 사용할 수 있습니다. 예를 들어 특정 ACR에 대 한 특정 계산 리소스의 액세스 권한을 부여 하거나 취소할 수 있습니다.

이 문서에서는 관리 되는 id를 사용 하 여 다음을 수행 하는 방법을 알아봅니다.

 * ACR에 대 한 관리 사용자 액세스를 사용 하도록 설정 하지 않고도 Azure Machine Learning 작업 영역에 대 한 ACR을 구성 하 고 사용 합니다.
 * 작업 영역 외부의 개인 ACR에 액세스 하 여 학습 또는 유추를 위한 기본 이미지를 끌어옵니다.
 * 저장소 액세스 키 대신 관리 되는 id를 사용 하 여 학습을 위한 데이터 집합에 액세스 합니다.

> [!IMPORTANT]
> 관리 id를 사용 하 여 Azure Machine Learning 있는 리소스에 대 한 액세스를 제어 하는 기능은 현재 미리 보기로 제공 됩니다. 미리 보기 기능은 지원 또는 서비스 수준 계약의 보증 없이 "있는 그대로" 제공 됩니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대 한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.
 
## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.
- [Machine Learning 서비스에 대 한 Azure CLI 확장](reference-azure-machine-learning-cli.md)
- [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)입니다.
- 역할을 할당 하려면 Azure 구독에 대 한 로그인에 [관리 Id 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 역할 또는 필요한 작업 (예: __소유자__)을 부여 하는 다른 역할이 있어야 합니다.
- [관리 id](/azure/active-directory/managed-identities-azure-resources/overview)를 만들고 사용 하는 방법에 대해 잘 알고 있어야 합니다.

## <a name="configure-managed-identities"></a>관리되는 ID 구성

일부 경우에는 Azure Container Registry에 대 한 관리 사용자 액세스를 허용 하지 않아야 합니다. 예를 들어 ACR을 공유 하 고 다른 사용자의 관리자 액세스를 허용 해야 하는 경우가 있습니다. 또는 관리 사용자가 사용 하도록 설정 된 ACR 만들기는 구독 수준 정책에 의해 허용 되지 않습니다.

> [!IMPORTANT]
> ACI (Azure Container Instance)에서 유추에 Azure Machine Learning를 사용 하는 경우 ACR에 대 한 관리자 액세스 권한이 __필요__합니다. 모델을 유추할 수 있도록 ACI에 배포 하려는 경우에는 사용 하지 않도록 설정 합니다.

관리자 사용자 액세스를 사용 하도록 설정 하지 않고 ACR을 만드는 경우 관리 되는 id를 사용 하 여 Docker 이미지를 빌드하고 가져오도록 ACR에 액세스 합니다.

작업 영역을 만들 때 관리 사용자를 사용 하지 않도록 설정 된 사용자 고유의 ACR를 가져올 수 있습니다. 또는 작업 영역 ACR을 만들고 나중에 관리자 사용자를 사용 하지 않도록 설정 Azure Machine Learning 수 있습니다.

### <a name="bring-your-own-acr"></a>사용자 고유의 ACR 가져오기

ACR admin 사용자가 구독 정책에 의해 허용 되지 않는 경우 먼저 관리자 사용자 없이 ACR를 만든 다음 작업 영역에 연결 해야 합니다. 또한 관리자 사용자를 사용 하지 않도록 설정 된 기존 ACR이 있는 경우이를 작업 영역에 연결할 수 있습니다.

인수를 설정 하지 않고 [Azure CLI에서](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli) ```--admin-enabled``` 또는 관리 사용자를 사용 하도록 설정 하지 않고 Azure Portal에서 ACR을 만듭니다. 그런 다음 Azure Machine Learning 작업 영역을 만들 때 ACR의 Azure 리소스 ID를 지정 합니다. 다음 예제에서는 기존 ACR을 사용 하는 새 Azure ML 작업 영역을 만드는 방법을 보여 줍니다.

> [!TIP]
> 매개 변수에 대 한 값을 가져오려면 `--container-registry` [az acr show](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az_acr_show) 명령을 사용 하 여 acr에 대 한 정보를 표시 합니다. 이 `id` 필드에는 ACR의 리소스 ID가 포함 되어 있습니다.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Azure Machine Learning 서비스에서 작업 영역 ACR 만들기

사용자 고유의 ACR을 가져오지 않으면 서비스를 요구 하는 작업을 수행 하는 경우 Azure Machine Learning 서비스에서 사용자에 게 하나를 만듭니다. 예를 들어 Machine Learning 컴퓨팅 교육 실행을 제출 하 여 환경을 빌드하거나 웹 서비스 끝점을 배포 합니다. 작업 영역에서 만든 ACR은 관리 사용자를 사용할 수 있으며 관리 사용자를 수동으로 사용 하지 않도록 설정 해야 합니다.

1. 새 작업 영역 만들기

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. ACR이 필요한 작업을 수행 합니다. 예를 들어 [모델을 학습](tutorial-train-models-with-aml.md)하는 방법에 대 한 자습서가 있습니다.

1. 클러스터에서 만든 ACR 이름을 가져옵니다.

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    이 명령은 다음 텍스트와 비슷한 값을 반환 합니다. ACR 인스턴스 이름인 텍스트의 마지막 부분만 필요 합니다.

    ```text
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. ACR을 업데이트 하 여 관리 사용자를 사용 하지 않도록 설정 합니다.

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>관리 id를 사용 하 여 계산을 만들어 교육용 Docker 이미지 액세스

작업 영역 ACR에 액세스 하려면 시스템 할당 관리 id를 사용 하도록 설정 하 여 machine learning 계산 클러스터를 만듭니다. 계산을 만들 때 또는 Azure CLI를 사용 하 여 Azure Portal 또는 스튜디오에서 id를 사용 하도록 설정할 수 있습니다.

# <a name="python"></a>[Python](#tab/python)

[AmlComputeProvisioningConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration?view=azure-ml-py)를 사용 하 여 계산 클러스터를 만들 때 `identity_type` 매개 변수를 사용 하 여 관리 되는 id 유형을 설정 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[포털](#tab/azure-portal)

Studio에서 계산 클러스터를 만들 때 관리 id를 구성 하는 방법에 대 한 자세한 내용은 [관리 Id 설정](how-to-create-attach-compute-studio.md#managed-identity)을 참조 하세요.

---

관리 id는 작업 영역 ACR에서 자동으로 ACRPull 역할을 부여 하 여 교육용 Docker 이미지를 끌어올 수 있도록 합니다.

> [!NOTE]
> Compute를 먼저 만든 경우 작업 영역 ACR을 만들기 전에 ACRPull 역할을 수동으로 할당 해야 합니다.

## <a name="access-base-images-from-private-acr"></a>개인 ACR에서 기본 이미지 액세스

기본적으로 Azure Machine Learning는 Microsoft에서 관리 하는 공용 리포지토리에서 제공 되는 Docker 기본 이미지를 사용 합니다. 그런 다음 해당 이미지에 대 한 학습 또는 유추 환경을 구축 합니다. 자세한 내용은 [ML 환경 이란?](concept-environments.md)을 참조 하세요.

회사 내부의 사용자 지정 기본 이미지를 사용 하려면 관리 되는 id를 사용 하 여 개인 ACR에 액세스할 수 있습니다. 두 가지 사용 사례가 있습니다.

 * 기본 이미지를 학습에 그대로 사용 합니다.
 * 사용자 지정 이미지를 기반으로 하는 Azure Machine Learning 관리 이미지를 빌드합니다.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>있는 그대로 교육용 Docker 기본 이미지를 machine learning 계산 클러스터로 끌어옵니다.

앞에서 설명한 대로 시스템 할당 관리 id를 사용 하도록 설정 하 여 machine learning 계산 클러스터를 만듭니다. 그런 다음 관리 되는 id의 보안 주체 ID를 확인 합니다.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

필요에 따라 사용자 할당 관리 id를 할당 하도록 계산 클러스터를 업데이트할 수 있습니다.

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

계산 클러스터가 기본 이미지를 가져올 수 있도록 하려면 개인 ACR에서 관리 되는 서비스 id ACRPull 역할을 부여 합니다.

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

마지막으로 학습 실행을 제출할 때 [환경 정의](how-to-use-environments.md#use-existing-environments)에서 기본 이미지 위치를 지정 합니다.

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> 기본 이미지를 계산 리소스로 직접 끌어올 수 있도록 하려면를 설정 하 `user_managed_dependencies = True` 고 Dockerfile을 지정 하지 않습니다. 그렇지 않으면 계산 클러스터만 ACR에서 기본 이미지를 끌어올 수 있는 액세스 권한이 있기 때문에 Azure Machine Learning 서비스에서 새 Docker 이미지를 빌드하려고 시도 하 고 실패 합니다.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>학습 또는 유추를 위해 개인 ACR의 기본 이미지에 Azure Machine Learning 관리 되는 환경 빌드

이 시나리오에서 Azure Machine Learning 서비스는 개인 ACR에서 제공 하는 기본 이미지 위에 학습 또는 유추 환경을 구축 합니다. 이미지 빌드 작업은 ACR 작업을 사용 하 여 작업 영역 ACR에서 발생 하기 때문에 액세스를 허용 하기 위해 추가 단계를 수행 해야 합니다.

1. __사용자 할당 관리 id__ 를 만들고 __개인 ACR__에 대 한 id acrpull 액세스 권한을 부여 합니다.  
1. 이전 단계에서 __사용자 할당 관리 id__ 에 대 한 관리 id 운영자 역할을 작업 영역 __시스템 할당 관리__ id에 부여 합니다. 이 역할을 통해 작업 영역에서 관리 되는 환경을 빌드하기 위한 ACR 작업에 사용자 할당 관리 id를 할당할 수 있습니다. 

    1. 작업 영역 시스템 할당 관리 id의 보안 주체 ID를 가져옵니다.

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. 관리 Id 운영자 역할을 부여 합니다.

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        UAI 리소스 ID는 형식으로 사용자 할당 id의 Azure 리소스 ID입니다 `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` .

1. [Workspace.set_connection 메서드](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-)를 사용 하 여 작업 영역 연결에서 __사용자 할당 관리 ID__ 의 외부 ACR 및 클라이언트 ID를 지정 합니다.

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

구성이 완료 되 면 학습 또는 유추 환경을 구축할 때 개인 ACR의 기본 이미지를 사용할 수 있습니다. 다음 코드 조각에서는 환경 정의에서 기본 이미지 ACR 및 이미지 이름을 지정 하는 방법을 보여 줍니다.

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

필요에 따라 [RegistryIdentity](https://docs.microsoft.com/python/api/azureml-core/azureml.core.container_registry.registryidentity?view=azure-ml-py)를 사용 하 여 환경 정의 자체에서 관리 되는 ID 리소스 URL 및 클라이언트 ID를 지정할 수 있습니다. 명시적으로 레지스트리 id를 사용 하는 경우 이전에 지정 된 작업 영역 연결을 재정의 합니다.

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="access-training-data"></a>학습 데이터 액세스

앞서 설명한 대로 관리 id를 사용 하 여 machine learning 계산 클러스터를 만든 후에는 해당 id를 사용 하 여 저장소 계정 키 없이 학습 데이터에 액세스할 수 있습니다. 이 시나리오에는 시스템 또는 사용자 할당 관리 id 중 하나를 사용할 수 있습니다.

### <a name="grant-compute-managed-identity-access-to-storage-account"></a>저장소 계정에 계산 관리 id 액세스 권한 부여

학습 데이터를 저장 하는 저장소 계정에 대 [한 읽기 권한자 역할에 관리 되는 id를 부여](https://docs.microsoft.com/azure/storage/common/storage-auth-aad#assign-azure-roles-for-access-rights) 합니다.

### <a name="register-data-store-with-workspace"></a>작업 영역에 데이터 저장소 등록

관리 id를 할당 한 후에는 저장소 자격 증명을 지정 하지 않고 데이터 저장소를 만들 수 있습니다.

```python
from azureml.core import Datastore

blob_dstore = Datastore.register_azure_blob_container(workspace=workspace,
                                                      datastore_name='my-datastore',
                                                      container_name='my-container',
                                                      account_name='my-storage-account')
```

### <a name="submit-training-run"></a>교육 실행 제출

데이터 저장소를 사용 하 여 학습 실행을 제출 하면 machine learning 계산에서 관리 되는 id를 사용 하 여 데이터에 액세스 합니다.

## <a name="use-docker-images-for-inference"></a>Docker 이미지를 사용 하 여 유추

앞서 설명한 대로 관리 사용자 없이 ACR을 구성 하면 Azure Kubernetes 서비스 (AKS)에서 관리 키 없이 유추를 위해 Docker 이미지에 액세스할 수 있습니다. AKS를 만들거나 작업 영역에 연결 하면 클러스터의 서비스 주체가 작업 영역 ACR에 대 한 ACRPull 액세스 권한을 자동으로 할당 합니다.

> [!NOTE]
> 사용자 고유의 AKS 클러스터를 가져오는 경우 클러스터에서 관리 되는 id 대신 서비스 주체를 사용 하도록 설정 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning의 엔터프라이즈 보안](concept-enterprise-security.md)에 대해 자세히 알아보세요.
