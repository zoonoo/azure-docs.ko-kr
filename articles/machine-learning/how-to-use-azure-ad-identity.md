---
title: 웹 서비스와 함께 AAD ID 사용
titleSuffix: Azure Machine Learning
description: Azure Kubernetes 서비스에서 웹 서비스와 함께 AAD ID를 사용하여 채점 하는 동안 클라우드 리소스에 액세스합니다.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122845"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Azure Kubernetes 서비스에서 기계 학습 웹 서비스와 함께 Azure AD ID 사용

이 방법에서는 Azure Kubernetes 서비스에서 배포된 기계 학습 모델에 AAD(Azure Active Directory) ID를 할당하는 방법을 알아봅니다. [AAD 포드 ID](https://github.com/Azure/aad-pod-identity) 프로젝트를 사용하면 [애플리케이션이 관리되는 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 및 Kubernetes 프리미티브를 사용하여 AAD를 사용하여 클라우드 리소스에 안전하게 액세스할 수 있습니다. 이렇게 하면 웹 서비스가 스크립트 내에 직접 자격 증명을 포함하거나 토큰을 관리할 `score.py` 필요 없이 Azure 리소스에 안전하게 액세스할 수 있습니다. 이 문서에서는 Azure Kubernetes 서비스 클러스터에 Azure ID를 만들고 설치하고 배포된 웹 서비스에 ID를 할당하는 단계를 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [기계 학습 서비스에 대한 Azure CLI 확장,](reference-azure-machine-learning-cli.md) [파이썬용 Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)또는 [Azure 기계 학습 시각적 스튜디오 코드 확장.](tutorial-setup-vscode-extension.md)

- 명령을 사용하여 AKS 클러스터에 `kubectl` 액세스합니다. 자세한 내용은 [클러스터에 연결](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- AKS 클러스터에 배포된 Azure 기계 학습 웹 서비스입니다.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>AKS 클러스터에 Azure ID 생성 및 설치

1. AKS 클러스터가 RBAC를 사용하도록 설정되어 있는지 확인하려면 다음 명령을 사용합니다.

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    이 명령은 RBAC가 활성화된 경우의 `true` 값을 반환합니다. 이 값은 다음 단계에서 사용할 명령을 결정합니다.

1. AKS 클러스터에 [AAD 포드 ID를](https://github.com/Azure/aad-pod-identity#getting-started) 설치하려면 다음 명령 중 하나를 사용합니다.

    * AKS 클러스터에 **RBAC를 사용하도록 설정한** 경우 다음 명령을 사용합니다.
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * AKS **클러스터에 RBAC를 사용하도록 설정한**경우 다음 명령을 사용합니다.
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        명령의 출력은 다음 텍스트와 유사합니다.

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. AAD 포드 ID 프로젝트 페이지에 표시된 단계에 따라 [Azure ID를 만듭니다.](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity)

1. AAD 포드 ID 프로젝트 페이지에 표시된 단계에 따라 [Azure ID를 설치합니다.](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity)

1. AAD 포드 ID 프로젝트 페이지에 표시된 단계에 따라 [Azure ID 바인딩을 설치합니다.](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)

1. 이전 단계에서 만든 Azure ID가 AKS 클러스터와 동일한 리소스 그룹에 없는 경우 AAD Pod Id 프로젝트 페이지에 표시된 단계에 따라 [MIC에 대한 권한 설정(권한 설정)을](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) 따릅니다.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>기계 학습 웹 서비스에 Azure ID 할당

다음 단계에서는 이전 섹션에서 만든 Azure Id를 사용하고 **선택 레이블을**통해 AKS 웹 서비스에 할당합니다.

먼저 Azure ID를 할당할 AKS 클러스터에서 배포의 이름과 네임스페이스를 식별합니다. 다음 명령을 실행하여 이 정보를 얻을 수 있습니다. 네임스페이스는 Azure Machine Learning 작업 영역 이름이어야 하며 배포 이름은 포털에 표시된 대로 끝점 이름이어야 합니다.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

배포 사양을 편집하여 배포에 Azure Id 선택기 레이블을 추가합니다. 선택기 값은 [Azure ID 바인딩 설치](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)의 5단계에서 정의한 값이어야 합니다.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

배포를 편집하여 Azure ID 선택기 레이블을 추가합니다. 아래의 `/spec/template/metadata/labels`섹션으로 이동합니다. `isazuremlapp: “true”`와 같은 값이 표시됩니다. 아래와 같이 aad-pod-IDENTITY 레이블을 추가합니다.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

레이블이 올바르게 추가되었는지 확인하려면 다음 명령을 실행합니다.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

모든 포드 상태를 보려면 다음 명령을 실행합니다.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

포드가 실행되면 이 배포의 웹 서비스는 이제 코드에 자격 증명을 포함하지 않고도 Azure Id를 통해 Azure 리소스에 액세스할 수 있습니다. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Azure ID에 적절한 역할 할당

다른 Azure 리소스에 액세스하는 [적절한 역할로 Azure 관리 ID를 할당합니다.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) 할당하는 역할에 올바른 **데이터 작업이**있는지 확인합니다. 예를 들어 [저장소 Blob 데이터 리더 역할에는](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) 저장소 Blob에 대한 읽기 권한이 있지만 일반 [판독기 역할은](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) 그렇지 않을 수 있습니다.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>기계 학습 웹 서비스와 함께 Azure ID 사용

AKS 클러스터에 모델을 배포합니다. 스크립트에는 `score.py` Azure Id에 액세스할 수 있는 Azure 리소스를 가리키는 작업이 포함될 수 있습니다. 액세스하려는 리소스에 필요한 클라이언트 라이브러리 종속성을 설치했습니다. 다음은 Azure ID를 사용하여 서비스에서 다른 Azure 리소스에 액세스하는 방법에 대한 몇 가지 예입니다.

### <a name="access-key-vault-from-your-web-service"></a>웹 서비스에서 키 볼트 에 액세스

Azure Id 읽기 액세스 권한을 **키 볼트**내부의 비밀에 부여한 경우 다음 코드를 사용하여 액세스할 `score.py` 수 있습니다.

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

### <a name="access-blob-from-your-web-service"></a>웹 서비스에서 Blob에 액세스

Azure Id읽기 액세스 권한을 저장소 **Blob**내의 데이터에 `score.py` 부여한 경우 다음 코드를 사용하여 데이터에 액세스할 수 있습니다.

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

* Python Azure Id 클라이언트 라이브러리를 사용하는 방법에 대한 자세한 내용은 GitHub의 [리포지토리를](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) 참조하십시오.
* Azure Kubernetes 서비스 클러스터에 모델을 배포하는 방법에 대한 자세한 가이드는 [방법을](how-to-deploy-azure-kubernetes-service.md)참조하십시오.