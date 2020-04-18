---
title: Azure CLI를 사용하여 작업 영역 만들기
titleSuffix: Azure Machine Learning
description: Azure CLI를 사용하여 새 Azure 기계 학습 작업 영역을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.openlocfilehash: 9a7d0b75140c50df61ff63f350e5b312a6a684c7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617784"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Azure CLI를 사용하여 Azure 기계 학습을 위한 작업 영역 만들기
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure CLI를 사용하여 Azure 기계 학습 작업 영역을 만드는 방법을 배웁니다. Azure CLI는 Azure 리소스를 관리하기 위한 명령을 제공합니다. CLI에 대한 기계 학습 확장은 Azure 기계 학습 리소스로 작업하기 위한 명령을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. 없는 경우 [Azure 기계 학습의 무료 또는 유료 버전을](https://aka.ms/AMLFree)사용해 보십시오.

* **로컬 환경에서**이 문서에서 CLI 명령을 사용하려면 Azure [CLI가](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)필요합니다.

    Azure 클라우드 [셸을](https://azure.microsoft.com//features/cloud-shell/)사용하는 경우 CLI는 브라우저를 통해 액세스되고 클라우드에 있습니다.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Azure 구독에 CLI 연결

> [!IMPORTANT]
> Azure 클라우드 셸을 사용하는 경우 이 섹션을 건너뛸 수 있습니다. 클라우드 셸은 Azure 구독에 로그인한 계정을 사용하여 자동으로 인증합니다.

CLI에서 Azure 구독을 인증할 수 있는 방법에는 여러 가지가 있습니다. 가장 기본적인 방법은 브라우저를 사용하여 대화형으로 인증하는 것입니다. 대화식으로 인증하려면 명령줄 또는 터미널을 열고 다음 명령을 사용합니다.

```azurecli-interactive
az login
```

CLI가 기본 브라우저를 열 수 있는 경우, 그렇게 하고 로그인 페이지를 로드합니다. 그렇지 않으면 브라우저를 열고 명령줄의 지침을 따라야 합니다. 지침에는 권한 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 부여 코드를 검색하고 입력하는 것이 포함됩니다.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

다른 인증 방법에 대한 경우 [Azure CLI를 사용 하 여 로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)을 참조 하십시오.

## <a name="install-the-machine-learning-extension"></a>기계 학습 확장 설치

기계 학습 확장을 설치하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>작업 영역 만들기

Azure 기계 학습 작업 영역은 다음 Azure 서비스 또는 엔터티를 기반으로 합니다.

> [!IMPORTANT]
> 기존 Azure 서비스를 지정하지 않으면 작업 영역을 만드는 동안 자동으로 만들어집니다. 항상 리소스 그룹을 지정해야 합니다.

| 서비스 | 기존 인스턴스를 지정하는 매개 변수 |
| ---- | ---- |
| **Azure 리소스 그룹** | `-g <resource-group-name>`
| **Azure Storage 계정** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 기계 학습 작업 영역은 리소스 그룹 내에서 만들어야 합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다. __새 리소스 그룹을 만들려면__다음 명령을 사용합니다. 이 `<resource-group-name>` 리소스 그룹에 사용할 이름으로 바꿉니다. 이 `<location>` 리소스 그룹에 사용할 Azure 지역으로 바꿉습니다.

> [!TIP]
> Azure 기계 학습을 사용할 수 있는 지역을 선택해야 합니다. 자세한 내용은 [지역별로 사용할 수 있는 제품을](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)참조하십시오.

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

이 명령의 응답은 다음 JSON과 유사합니다.

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

리소스 그룹 작업에 대한 자세한 내용은 [az 그룹을](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)참조하십시오.

### <a name="automatically-create-required-resources"></a>필요한 리소스를 자동으로 생성합니다.

__서비스가 자동으로 생성되는__새 작업 영역을 만들려면 다음 명령을 사용합니다.

> [!TIP]
> 이 섹션의 명령은 기본 에디션 작업 영역을 만듭니다. 엔터프라이즈 작업 영역을 만들려면 `--sku enterprise` 명령과 `az ml workspace create` 함께 스위치를 사용합니다. Azure 기계 학습 버전에 대한 자세한 내용은 [Azure 기계 학습이란 무엇입니까](overview-what-is-azure-ml.md#sku)를 참조하십시오.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> 작업 영역 이름은 대/소문자를 구분하지 않습니다.

이 명령의 출력은 다음 JSON과 유사합니다.

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
> 기존 리소스를 모두 지정할 필요는 없습니다. 하나 이상을 지정할 수 있습니다. 예를 들어 기존 저장소 계정을 지정할 수 있으며 작업 영역은 다른 리소스를 만듭니다.

+ **Azure 저장소 계정**:`az storage account show --name <storage-account-name> --query "id"`

    이 명령의 응답은 다음 텍스트와 유사하며 저장소 계정의 ID입니다.

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure 응용 프로그램 인사이트**:

    1. 응용 프로그램 인사이트 확장을 설치합니다.

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. 응용 프로그램 인사이트 서비스의 ID를 가져옵니다.

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        이 명령의 응답은 다음 텍스트와 유사하며 응용 프로그램 인사이트 서비스의 ID입니다.

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure 키 볼트**:`az keyvault show --name <key-vault-name> --query "ID"`

    이 명령의 응답은 다음 텍스트와 유사하며 키 자격 증명 모음의 ID입니다.

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure 컨테이너 레지스트리**:`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    이 명령의 응답은 다음 텍스트와 유사하며 컨테이너 레지스트리의 ID입니다.

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > 컨테이너 레지스트리는 Azure 기계 학습 작업 영역에서 사용하려면 [먼저 관리 계정을](/azure/container-registry/container-registry-authentication#admin-account) 사용하도록 설정해야 합니다.

작업 영역과 함께 사용할 리소스에 대한 ID가 있으면 기본 `az workspace create -w <workspace-name> -g <resource-group-name>` 명령을 사용하고 기존 리소스에 대한 매개 변수 및 ID를 추가합니다. 예를 들어 다음 명령은 기존 컨테이너 레지스트리를 사용하는 작업 영역을 만듭니다.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

이 명령의 출력은 다음 JSON과 유사합니다.

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

## <a name="list-workspaces"></a>작업 영역 목록

Azure 구독의 모든 작업 영역을 나열하려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml workspace list
```

이 명령의 출력은 다음 JSON과 유사합니다.

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

자세한 내용은 az [ml 작업 영역 목록](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) 설명서를 참조하십시오.

## <a name="get-workspace-information"></a>작업 영역 정보 얻기

작업 영역에 대한 정보를 얻으려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

이 명령의 출력은 다음 JSON과 유사합니다.

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

자세한 내용은 az [ml 작업 영역 표시](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) 설명서를 참조하십시오.

## <a name="update-a-workspace"></a>작업 영역 업데이트

작업 영역을 업데이트하려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

이 명령의 출력은 다음 JSON과 유사합니다.

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

자세한 내용은 az [ml 작업 영역 업데이트](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) 설명서를 참조하십시오.

## <a name="share-a-workspace-with-another-user"></a>다른 사용자와 작업 영역 공유

구독에서 다른 사용자와 작업 영역을 공유하려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Azure 기계 학습을 사용하는 역할 기반 액세스 제어(RBAC)에 대한 자세한 내용은 [사용자 및 역할 관리를](how-to-assign-roles.md)참조하십시오.

자세한 내용은 az [ml 작업 영역 공유](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) 설명서를 참조하십시오.

## <a name="sync-keys-for-dependent-resources"></a>종속 리소스에 대한 동기화 키

작업 영역에서 사용되는 리소스 중 하나에 대한 액세스 키를 변경하는 경우 다음 명령을 사용하여 새 키를 작업 영역과 동기화합니다.

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

키 변경에 대한 자세한 내용은 [저장소 액세스 키 재생을](how-to-change-storage-access-key.md)참조하십시오.

자세한 내용은 az [ml 작업 영역 동기화 키](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) 설명서를 참조하십시오.

## <a name="delete-a-workspace"></a>작업 영역 삭제

더 이상 필요하지 않은 작업 영역을 삭제하려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> 작업 영역을 삭제해도 작업 영역에서 사용되는 응용 프로그램 인사이트, 저장소 계정, 키 자격 증명 모음 또는 컨테이너 레지스트리가 삭제되지는 않습니다.

리소스 그룹의 작업 영역 및 기타 모든 Azure 리소스를 삭제하는 리소스 그룹을 삭제할 수도 있습니다. 리소스 그룹을 삭제하려면 다음 명령을 사용합니다.

```azurecli-interactive
az group delete -g <resource-group-name>
```

자세한 내용은 az [ml 작업 영역 삭제](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) 설명서를 참조하십시오.

## <a name="troubleshooting"></a>문제 해결

### <a name="resource-provider-errors"></a>리소스 공급자 오류

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>작업 영역 이동

> [!WARNING]
> Azure Machine Learning 작업 영역을 다른 구독으로 이동하거나 소유 구독을 새 테넌트로 이동하는 것은 지원되지 않습니다. 이렇게 하면 오류가 발생할 수 있습니다.

### <a name="deleting-the-azure-container-registry"></a>Azure 컨테이너 레지스트리 삭제

Azure 기계 학습 작업 영역은 일부 작업에 Azure 컨테이너 레지스트리(ACR)를 사용합니다. ACR 인스턴스가 처음 필요할 때 자동으로 ACR 인스턴스가 생성됩니다.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>다음 단계

기계 학습을 위한 Azure CLI 확장에 대한 자세한 내용은 [az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) 설명서를 참조하십시오.
