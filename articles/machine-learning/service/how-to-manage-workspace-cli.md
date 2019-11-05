---
title: Azure CLI를 사용 하 여 작업 영역 만들기
titleSuffix: Azure Machine Learning
description: Azure CLI를 사용 하 여 새 Azure Machine Learning 작업 영역을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 08/30/2019
ms.openlocfilehash: 8606ac2578c45062182517b5e67d669a09b8e5c0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489720"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Azure CLI를 사용 하 여 Azure Machine Learning에 대 한 작업 영역 만들기
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure CLI를 사용 하 여 Azure Machine Learning 작업 영역을 만드는 방법에 대해 알아봅니다. Azure CLI는 Azure 리소스를 관리 하기 위한 명령을 제공 합니다. CLI에 대 한 machine learning 확장은 Azure Machine Learning 리소스를 사용 하기 위한 명령을 제공 합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. 없는 경우 [무료 또는 유료 버전의 Azure Machine Learning](https://aka.ms/AMLFree)을 사용해 보세요.

* **로컬 환경**에서이 문서의 CLI 명령을 사용 하려면 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)필요 합니다.

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)사용 하는 경우 CLI는 브라우저를 통해 액세스할 수 있으며 클라우드에 있습니다.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Azure 구독에 CLI 연결

> [!IMPORTANT]
> Azure Cloud Shell 사용 하는 경우이 섹션을 건너뛸 수 있습니다. Cloud shell은 Azure 구독에 로그인 하는 계정을 사용 하 여 자동으로 인증 합니다.

CLI에서 Azure 구독에 인증 하는 방법에는 여러 가지가 있습니다. 가장 기본적인 방법은 브라우저를 사용 하 여 대화형으로 인증 하는 것입니다. 대화형으로 인증 하려면 명령줄 또는 터미널을 열고 다음 명령을 사용 합니다.

```azurecli
az login
```

CLI가 기본 브라우저를 열 수 있는 경우, 그렇게 하고 로그인 페이지를 로드합니다. 그렇지 않으면 브라우저를 열고 명령줄의 지침을 따르세요. 지침에는 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 를 찾고 인증 코드를 입력 하는 작업이 포함 됩니다.

다른 인증 방법에 대 한 자세한 내용은 [Azure CLI 사용 하 여 로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)을 참조 하세요.

## <a name="install-the-machine-learning-extension"></a>Machine learning 확장 설치

Machine learning 확장을 설치 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>작업 영역 만들기

Azure Machine Learning 작업 영역에서는 다음과 같은 Azure 서비스 또는 엔터티를 사용 합니다.

> [!IMPORTANT]
> 기존 Azure 서비스를 지정 하지 않으면 작업 영역을 만드는 동안 자동으로 하나 생성 됩니다. 항상 리소스 그룹을 지정 해야 합니다.

| 부여 | 기존 인스턴스를 지정 하는 매개 변수 |
| ---- | ---- |
| **Azure 리소스 그룹** | `-g <resource-group-name>`
| **Azure Storage 계정** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹 내에 Azure Machine Learning 작업 영역을 만들어야 합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다. __새 리소스 그룹을 만들려면__다음 명령을 사용 합니다. `<resource-group-name>`을이 리소스 그룹에 사용할 이름으로 바꿉니다. `<location>`을이 리소스 그룹에 사용할 Azure 지역으로 바꿉니다.

> [!TIP]
> Azure Machine Learning를 사용할 수 있는 지역을 선택 해야 합니다. 자세한 내용은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)을 참조 하세요.

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

이 명령의 응답은 다음 JSON과 유사 합니다.

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

리소스 그룹 작업에 대 한 자세한 내용은 [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)을 참조 하세요.

### <a name="automatically-create-required-resources"></a>필요한 리소스를 자동으로 만듭니다.

__서비스가 자동으로 생성 되__는 새 작업 영역을 만들려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

이 명령의 출력은 다음 JSON과 유사 합니다.

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

기존 리소스를 사용 하는 작업 영역을 만들려면 리소스에 대 한 ID를 제공 해야 합니다. 다음 명령을 사용 하 여 서비스에 대 한 ID를 가져옵니다.

> [!IMPORTANT]
> 기존 리소스를 모두 지정할 필요는 없습니다. 하나 이상의을 지정할 수 있습니다. 예를 들어 기존 저장소 계정을 지정 하 고 작업 영역에서 다른 리소스를 만들 수 있습니다.

+ **Azure Storage 계정**: `az storage account show --name <storage-account-name> --query "id"`

    이 명령의 응답은 다음 텍스트와 비슷하며 저장소 계정의 ID입니다.

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure Application Insights**:

    1. Application insights 확장을 설치 합니다.

        ```bash
        az extension add -n application-insights
        ```

    2. Application insights 서비스의 ID를 가져옵니다.

        ```bash
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        이 명령의 응답은 다음 텍스트와 비슷하며 application insights 서비스의 ID입니다.

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: ' az keyvault show--name < Key-name >--QUERY "ID"

    이 명령의 응답은 다음 텍스트와 비슷하며 키 자격 증명 모음에 대 한 ID입니다.

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    이 명령의 응답은 다음 텍스트와 비슷하며 컨테이너 레지스트리의 ID입니다.

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > 컨테이너 레지스트리가 Azure Machine Learning 작업 영역에서 사용할 수 있도록 [관리자 계정을](/azure/container-registry/container-registry-authentication#admin-account) 사용 하도록 설정 되어 있어야 합니다.

작업 영역에서 사용 하려는 리소스에 대 한 Id가 있는 경우 기본 `az workspace create -w <workspace-name> -g <resource-group-name>` 명령을 사용 하 고 기존 리소스에 대 한 매개 변수 및 ID를 추가 합니다. 예를 들어 다음 명령은 기존 컨테이너 레지스트리를 사용 하는 작업 영역을 만듭니다.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

이 명령의 출력은 다음 JSON과 유사 합니다.

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

Azure 구독에 대 한 모든 작업 영역을 나열 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml workspace list
```

이 명령의 출력은 다음 JSON과 유사 합니다.

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

자세한 내용은 [az ml workspace list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) 설명서를 참조 하세요.

## <a name="get-workspace-information"></a>작업 영역 정보 가져오기

작업 영역에 대 한 정보를 가져오려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

이 명령의 출력은 다음 JSON과 유사 합니다.

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

자세한 내용은 [az ml workspace show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) 설명서를 참조 하세요.

## <a name="update-a-workspace"></a>작업 영역 업데이트

작업 영역을 업데이트 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

이 명령의 출력은 다음 JSON과 유사 합니다.

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

자세한 내용은 [az ml workspace update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) 설명서를 참조 하세요.

## <a name="share-a-workspace-with-another-user"></a>다른 사용자와 작업 영역 공유

구독에서 작업 영역을 다른 사용자와 공유 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Azure Machine Learning의 RBAC (역할 기반 액세스 제어)에 대 한 자세한 내용은 [사용자 및 역할 관리](how-to-assign-roles.md)를 참조 하세요.

자세한 내용은 [az ml workspace share](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) 설명서를 참조 하세요.

## <a name="sync-keys-for-dependent-resources"></a>종속 리소스의 키 동기화

작업 영역에서 사용 하는 리소스 중 하나에 대 한 액세스 키를 변경 하는 경우 다음 명령을 사용 하 여 새 키와 작업 영역을 동기화 합니다.

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

키를 변경 하는 방법에 대 한 자세한 내용은 [저장소 액세스 키 다시 생성](how-to-change-storage-access-key.md)을 참조 하세요.

자세한 내용은 [az ml workspace sync-keys](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) 설명서를 참조 하세요.

## <a name="delete-a-workspace"></a>작업 영역 삭제

더 이상 필요 하지 않은 작업 영역을 삭제 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> 작업 영역을 삭제 해도 작업 영역에서 사용 하는 응용 프로그램 정보, 저장소 계정, 키 자격 증명 모음 또는 컨테이너 레지스트리는 삭제 되지 않습니다.

리소스 그룹에서 작업 영역 및 기타 모든 Azure 리소스를 삭제 하는 리소스 그룹을 삭제할 수도 있습니다. 리소스 그룹을 삭제 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az group delete -g <resource-group-name>
```

자세한 내용은 [az ml workspace delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

기계 학습을 위한 Azure CLI 확장에 대 한 자세한 내용은 [az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) 설명서를 참조 하세요.
