---
title: 웹 서비스에서 Azure AD id 사용
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service의 웹 서비스에서 Azure AD id를 사용 하 여 점수 매기기 중에 클라우드 리소스에 액세스 합니다.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: bcf5f75cf5cabe42f530a6a179c2cafd43b5520d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952542"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Azure Kubernetes Service에서 Machine Learning 웹 서비스와 함께 Azure AD ID 사용

이 방법에서는 Azure Kubernetes Service에서 배포 된 machine learning 모델에 Azure Active Directory (Azure AD) id를 할당 하는 방법에 대해 알아봅니다. [AZURE Ad Pod id](https://github.com/Azure/aad-pod-identity) 프로젝트를 사용 하면 응용 프로그램에서 [관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md) 및 Kubernetes 기본 형식을 사용 하 여 azure ad와 안전 하 게 클라우드 리소스에 액세스할 수 있습니다. 이렇게 하면 웹 서비스가 스크립트 내에서 직접 자격 증명을 포함 하거나 토큰을 관리할 필요 없이 Azure 리소스에 안전 하 게 액세스할 수 있습니다 `score.py` . 이 문서에서는 azure Kubernetes Service 클러스터에서 Azure Id를 만들고 설치 하 고 배포 된 웹 서비스에 id를 할당 하는 단계를 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Machine Learning 서비스에 대 한 Azure CLI 확장](reference-azure-machine-learning-cli.md), [PYTHON 용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)또는 [Azure Machine Learning Visual Studio Code 확장](tutorial-setup-vscode-extension.md)입니다.

- 명령을 사용 하 여 AKS 클러스터에 액세스 `kubectl` 합니다. 자세한 내용은 [클러스터에 연결](../aks/kubernetes-walkthrough.md#connect-to-the-cluster) 을 참조 하세요.

- AKS 클러스터에 배포 된 Azure Machine Learning 웹 서비스입니다.

## <a name="create-and-install-an-azure-identity"></a>Azure Id 만들기 및 설치

1. AKS 클러스터가 Kubernetes RBAC를 사용 하는지 확인 하려면 다음 명령을 사용 합니다.

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    `true`KUBERNETES RBAC를 사용 하는 경우이 명령은 값을 반환 합니다. 이 값은 다음 단계에서 사용할 명령을 결정 합니다.

1. AKS 클러스터에 [AZURE AD Pod id](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/) 를 설치 합니다.

1. Azure AD Pod Identity project 페이지에 표시 된 단계에 따라 [azure에서 id를 만듭니다](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#2-create-an-identity-on-azure) .

1. Azure AD Pod Identity project 페이지에 표시 된 단계에 따라 [AzureIdentity를 배포](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#3-deploy-azureidentity) 합니다.

1. Azure AD Pod Identity project 페이지에 표시 된 단계에 따라 [AzureIdentityBinding를 배포](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding) 합니다.

1. 이전 단계에서 만든 Azure Id가 AKS 클러스터에 대 한 동일한 노드 리소스 그룹에 없는 경우 Azure AD Pod Id 프로젝트 페이지에 표시 된 [역할 할당](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/#user-assigned-identities-that-are-not-within-the-node-resource-group) 단계를 따릅니다.

## <a name="assign-azure-identity-to-web-service"></a>웹 서비스에 Azure Id 할당

다음 단계에서는 이전 섹션에서 만든 Azure Id를 사용 하 고 **선택기 레이블을** 통해 AKS 웹 서비스에 할당 합니다.

먼저 AKS 클러스터에서 Azure Id를 할당 하려는 배포의 이름 및 네임 스페이스를 식별 합니다. 다음 명령을 실행 하 여이 정보를 가져올 수 있습니다. 네임 스페이스는 Azure Machine Learning 작업 영역 이름 이어야 하 고 배포 이름은 포털에 표시 된 것 처럼 끝점 이름 이어야 합니다.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

배포 사양을 편집 하 여 배포에 Azure Id 선택기 레이블을 추가 합니다. Selector 값은 [AzureIdentityBinding 배포](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding)의 5 단계에서 정의한 값 이어야 합니다.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

배포를 편집 하 여 Azure Id 선택기 레이블을 추가 합니다. 에서 다음 섹션으로 이동 `/spec/template/metadata/labels` 합니다. 와 같은 값이 표시 되어야 합니다 `isazuremlapp: “true”` . 아래와 같이 aad-pod id 레이블을 추가 합니다.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

레이블이 올바르게 추가 되었는지 확인 하려면 다음 명령을 실행 합니다. 또한 새로 만든 pod의 상태를 확인 해야 합니다.

```azurecli-interactive
   kubectl get pod -n azureml-<name of workspace> --show-labels
```

Pod 실행 되 면이 배포에 대 한 웹 서비스에서 코드에 자격 증명을 포함 하지 않고도 Azure Id를 통해 Azure 리소스에 액세스할 수 있게 됩니다.

## <a name="assign-roles-to-your-azure-identity"></a>Azure Id에 역할 할당

[Azure 관리 되는 id를 적절 한 역할에 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 하 여 다른 azure 리소스에 액세스 합니다. 할당 하는 역할에 올바른 **데이터 작업이** 있는지 확인 합니다. 예를 들어 [저장소 Blob 데이터 읽기 권한자 역할](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 에는 저장소 blob에 대 한 읽기 권한이 부여 되지만 일반 [판독기 역할](../role-based-access-control/built-in-roles.md#reader) 은 그렇지 않을 수도 있습니다.

## <a name="use-azure-identity-with-your-web-service"></a>웹 서비스에서 Azure Id 사용

AKS 클러스터에 모델을 배포 합니다. 스크립트에는 `score.py` Azure id가 액세스할 수 있는 azure 리소스를 가리키는 작업이 포함 될 수 있습니다. 액세스 하려는 리소스에 대 한 필수 클라이언트 라이브러리 종속성을 설치 했는지 확인 합니다. 다음은 Azure Id를 사용 하 여 서비스에서 다양 한 Azure 리소스에 액세스 하는 방법에 대 한 몇 가지 예입니다.

### <a name="access-key-vault-from-your-web-service"></a>웹 서비스에서 Key Vault 액세스

**Key Vault** 내에서 암호에 대 한 읽기 액세스 권한을 Azure id에 제공 하면 `score.py` 다음 코드를 사용 하 여 액세스할 수 있습니다.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name)
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> 이 예에서는 DefaultAzureCredential를 사용 합니다. 특정 액세스 정책을 사용 하 여 id 액세스 권한을 부여 하려면 [Azure CLI를 사용 하 여 Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-cli.md)을 참조 하세요.

### <a name="access-blob-from-your-web-service"></a>웹 서비스에서 Blob 액세스

**저장소 Blob** 내의 데이터에 대 한 Azure id 읽기 액세스 권한을 제공 하는 경우 `score.py` 다음 코드를 사용 하 여 액세스할 수 있습니다.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>다음 단계

* Python Azure Id 클라이언트 라이브러리를 사용 하는 방법에 대 한 자세한 내용은 GitHub의 [리포지토리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) 를 참조 하세요.
* Azure Kubernetes Service 클러스터에 모델을 배포 하는 방법에 대 한 자세한 지침은 [방법](how-to-deploy-azure-kubernetes-service.md)을 참조 하세요.