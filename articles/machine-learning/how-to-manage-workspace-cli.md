---
title: Azure CLI를 사용하여 작업 영역 만들기
titleSuffix: Azure Machine Learning
description: Azure CLI를 사용하여 새 Azure Machine Learning 작업 영역을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: larryfr
author: Blackmist
ms.date: 06/25/2020
ms.openlocfilehash: 64963bfc28921d195d9ed0f96b2673a9c9e4aa2b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392712"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Azure CLI를 사용하여 Azure Machine Learning의 작업 영역 만들기
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure CLI를 사용하여 Azure Machine Learning 작업 영역을 만드는 방법을 알아봅니다. Azure CLI는 Azure 리소스를 관리하기 위한 명령을 제공합니다. CLI에 대한 Machine Learning 확장은 Azure Machine Learning 리소스를 사용하기 위한 명령을 제공합니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. 구독이 없는 경우[Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* **로컬 환경**에서 이 문서의 CLI 명령을 사용하려면 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)가 필요합니다.

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)을 사용하는 경우 CLI는 브라우저를 통해 액세스하고 클라우드에 있습니다.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Azure 구독에 CLI 연결

> [!IMPORTANT]
> Azure Cloud Shell을 사용하는 경우 이 섹션을 건너뛸 수 있습니다. Cloud Shell은 Azure 구독에 로그인하는 계정을 사용하여 사용자를 자동으로 인증합니다.

CLI에서 Azure 구독에 인증하는 방법에는 여러 가지가 있습니다. 가장 기본적인 방법은 브라우저를 사용하여 대화형으로 인증하는 것입니다. 대화형으로 인증하려면 명령줄 또는 터미널을 열고 다음 명령을 사용합니다.

```azurecli-interactive
az login
```

CLI가 기본 브라우저를 열 수 있는 경우, 그렇게 하고 로그인 페이지를 로드합니다. CLI가 브라우저를 열 수 없는 경우에는 사용자가 브라우저를 열고 명령줄의 지침을 따릅니다. 지침에 따라 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)으로 이동하고 인증 코드를 입력합니다.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

다른 인증 방법은 [Azure CLI로 로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)을 참조하세요.

## <a name="install-the-machine-learning-extension"></a>기계 학습 확장 설치

기계 학습 확장을 설치하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>작업 영역 만들기

Azure Machine Learning 작업 영역은 다음과 같은 Azure 서비스 또는 엔터티를 사용합니다.

> [!IMPORTANT]
> 기존 Azure 서비스를 지정하지 않으면 작업 영역을 만드는 동안 서비스가 자동으로 생성됩니다. 항상 리소스 그룹을 지정해야 합니다. 사용자 고유의 저장소 계정을 연결 하는 경우 Azure Blob 및 Azure 파일 기능을 모두 사용 하도록 설정 하 고 계층 구조 네임 스페이스 (ADLS Gen 2)를 사용 하지 않도록 설정 해야 합니다. 작업 영역이 datastores로 생성 된 후에는 언제 든 지 사용자 고유의 저장소 계정을 연결할 수 있습니다.

| 서비스 | 기존 인스턴스를 지정하는 매개 변수 |
| ---- | ---- |
| **Azure 리소스 그룹** | `-g <resource-group-name>`
| **Azure Storage 계정** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹 내에 Azure Machine Learning 작업 영역을 만들어야 합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다. __새 리소스 그룹을 만들려면__ 다음 명령을 사용합니다. `<resource-group-name>`을 이 리소스 그룹에 사용할 이름으로 바꿉니다. `<location>`을 이 리소스 그룹에 사용할 Azure 지역으로 바꿉니다.

> [!TIP]
> Azure Machine Learning을 사용할 수 있는 지역을 선택해야 합니다. 자세한 내용은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)을 참조하세요.

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

이 명령의 응답은 다음 JSON과 비슷합니다.

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

리소스 그룹 작업에 대한 자세한 내용은 [az 그룹](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)을 참조하세요.

### <a name="automatically-create-required-resources"></a>자동으로 필수 리소스 만들기

__서비스가 자동으로 생성되는__ 새 작업 영역을 만들려면 다음 명령을 사용합니다.

> [!TIP]
> 이 섹션의 명령은 기본 버전 작업 영역을 만듭니다. 엔터프라이즈 작업 영역을 만들려면 `az ml workspace create` 명령과 함께 `--sku enterprise` 스위치를 사용합니다. Azure Machine Learning 버전에 관한 자세한 내용은 [Azure Machine Learning이란?](overview-what-is-azure-ml.md#sku)을 참조하세요.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> 작업 영역 이름은 대/소문자를 구분하지 않습니다.

이 명령의 출력은 다음 JSON과 비슷합니다.

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="use-existing-resources"></a>기존 리소스 사용

기존 리소스를 사용하는 작업 영역을 만들려면 리소스에 대한 ID를 제공해야 합니다. 다음 명령을 사용하여 서비스에 대한 ID를 가져옵니다.

> [!IMPORTANT]
> 기존 리소스를 모두 지정할 필요는 없습니다. 하나 이상의 리소스를 지정할 수 있습니다. 예를 들어, 기존 스토리지 계정을 지정할 수 있으며 작업 영역에서는 다른 리소스를 만듭니다.

+ **Azure Storage 계정**: `az storage account show --name <storage-account-name> --query "id"`

    이 명령의 응답은 다음 텍스트와 비슷하며 스토리지 계정의 ID입니다.

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure Application Insights**:

    1. Application Insights 확장을 선택합니다.

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Application Insights 서비스의 ID를 가져옵니다.

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        이 명령의 응답은 다음 텍스트와 비슷하며 Application Insights 서비스의 ID입니다.

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: `az keyvault show --name <key-vault-name> --query "ID"`

    이 명령의 응답은 다음 텍스트와 비슷하며 Key Vault의 ID입니다.

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    이 명령의 응답은 다음 텍스트와 비슷하며 컨테이너 레지스트리의 ID입니다.

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Azure Machine Learning 작업 영역에서 사용할 수 있으려면 컨테이너 레지스트리에 [관리자 계정](/azure/container-registry/container-registry-authentication#admin-account)을 사용하도록 설정해야 합니다.

작업 영역에서 사용하려는 리소스에 대한 ID가 있는 경우 기본 `az workspace create -w <workspace-name> -g <resource-group-name>` 명령을 사용하고 기존 리소스에 대한 매개 변수 및 ID를 추가합니다. 예를 들어 다음 명령은 기존 컨테이너 레지스트리를 사용하는 작업 영역을 만듭니다.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

이 명령의 출력은 다음 JSON과 비슷합니다.

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>작업 영역 나열

Azure 구독에 대한 모든 작업 영역을 나열하려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml workspace list
```

이 명령의 출력은 다음 JSON과 비슷합니다.

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

자세한 내용은 [az ml workspace list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) 설명서를 참조하세요.

## <a name="get-workspace-information"></a>작업 영역 정보 가져오기

작업 영역에 대한 정보를 가져오려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

이 명령의 출력은 다음 JSON과 비슷합니다.

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

자세한 내용은 [az ml workspace show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) 설명서를 참조하세요.

## <a name="update-a-workspace"></a>작업 영역 업데이트

작업 영역을 업데이트하려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

이 명령의 출력은 다음 JSON과 비슷합니다.

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

자세한 내용은 [az ml workspace update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) 설명서를 참조하세요.

## <a name="share-a-workspace-with-another-user"></a>다른 사용자와 작업 영역 공유

구독의 작업 영역을 다른 사용자와 공유하려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Azure Machine Learning의 RBAC(역할 기반 액세스 제어)에 대한 자세한 내용은 [사용자 및 역할 관리](how-to-assign-roles.md)를 참조하세요.

자세한 내용은 [az ml workspace share](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) 설명서를 참조하세요.

## <a name="sync-keys-for-dependent-resources"></a>종속 리소스의 키 동기화

작업 영역에서 사용 하는 리소스 중 하나에 대 한 액세스 키를 변경 하는 경우 작업 영역이 새 키로 동기화 되는 데 약 1 시간이 소요 됩니다. 작업 영역에서 새 키를 즉시 동기화 하도록 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

키 변경에 대한 자세한 내용은 [스토리지 액세스 키 다시 생성](how-to-change-storage-access-key.md)을 참조하세요.

자세한 내용은 [az ml workspace sync-keys](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) 설명서를 참조하세요.

## <a name="delete-a-workspace"></a>작업 영역 삭제

더 이상 필요하지 않은 작업 영역을 삭제하려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> 작업 영역을 삭제해도 작업 영역에서 사용하는 애플리케이션 인사이트, 스토리지 계정, Key Vault 또는 컨테이너 레지스트리는 삭제되지 않습니다.

리소스 그룹에서 작업 영역 및 기타 모든 Azure 리소스를 삭제하는 리소스 그룹을 삭제할 수도 있습니다. 리소스 그룹을 삭제하려면 다음 명령을 사용합니다.

```azurecli-interactive
az group delete -g <resource-group-name>
```

자세한 내용은 [az ml workspace delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) 설명서를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="resource-provider-errors"></a>리소스 공급자 오류

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>작업 영역 이동

> [!WARNING]
> Azure Machine Learning 작업 영역을 다른 구독으로 이동하거나 소유하는 구독을 새 테넌트로 이동하는 것은 지원되지 않습니다. 이렇게 하면 오류가 발생할 수 있습니다.

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry 삭제

Azure Machine Learning 작업 영역에서는 일부 작업에 ACR(Azure Container Registry)을 사용합니다. 먼저 필요한 경우 ACR 인스턴스를 자동으로 만듭니다.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>다음 단계

기계 학습용 Azure CLI 확장에 대한 자세한 내용은 [az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) 설명서를 참조하세요.
