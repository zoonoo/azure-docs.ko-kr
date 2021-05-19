---
title: Azure CLI를 사용하여 작업 영역 만들기
titleSuffix: Azure Machine Learning
description: 기계 학습용 Azure CLI를 사용하여 새 Azure Machine Learning 작업 영역을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2021
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: d86350bf96e4c91fb7ea0a635387d4da95cc8581
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888983"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Azure CLI를 사용하여 Azure Machine Learning의 작업 영역 만들기


이 문서에서는 Azure CLI를 사용하여 Azure Machine Learning 작업 영역을 만드는 방법을 알아봅니다. Azure CLI는 Azure 리소스를 관리하기 위한 명령을 제공합니다. CLI에 대한 Machine Learning 확장은 Azure Machine Learning 리소스를 사용하기 위한 명령을 제공합니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. 구독이 없는 경우[Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* **로컬 환경** 에서 이 문서의 CLI 명령을 사용하려면 [Azure CLI](/cli/azure/install-azure-cli)가 필요합니다.

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)을 사용하는 경우 CLI는 브라우저를 통해 액세스하고 클라우드에 있습니다.

## <a name="limitations"></a>제한 사항

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Azure 구독에 CLI 연결

> [!IMPORTANT]
> Azure Cloud Shell을 사용하는 경우 이 섹션을 건너뛸 수 있습니다. Cloud Shell은 Azure 구독에 로그인하는 계정을 사용하여 사용자를 자동으로 인증합니다.

CLI에서 Azure 구독에 인증하는 방법에는 여러 가지가 있습니다. 가장 간단한 방법은 브라우저를 사용하여 대화형으로 인증하는 것입니다. 대화형으로 인증하려면 명령줄 또는 터미널을 열고 다음 명령을 사용합니다.

```azurecli-interactive
az login
```

CLI가 기본 브라우저를 열 수 있는 경우, 그렇게 하고 로그인 페이지를 로드합니다. CLI가 브라우저를 열 수 없는 경우에는 사용자가 브라우저를 열고 명령줄의 지침을 따릅니다. 지침에 따라 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)으로 이동하고 인증 코드를 입력합니다.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

다른 인증 방법은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

## <a name="create-a-workspace"></a>작업 영역 만들기

Azure Machine Learning 작업 영역은 다음과 같은 Azure 서비스 또는 엔터티를 사용합니다.

> [!IMPORTANT]
> 기존 Azure 서비스를 지정하지 않으면 작업 영역을 만드는 동안 서비스가 자동으로 생성됩니다. 항상 리소스 그룹을 지정해야 합니다. 사용자 고유의 스토리지 계정을 연결하는 경우 다음 조건을 충족하는지 확인합니다.
>
> * 스토리지 계정이 프리미엄 계정((Premium_LRS 및 Premium_GRS)이 _아님_
> * Azure Blob 및 Azure File 기능 모두 사용
> * 계층 구조 네임스페이스(ADLS Gen 2) 사용 안 함
>
> 이러한 요구 사항은 작업 영역에서 사용하는 _기본_ 스토리지 계정에만 적용됩니다.

| 서비스 | 기존 인스턴스를 지정하는 매개 변수 |
| ---- | ---- |
| **Azure 리소스 그룹** | `-g <resource-group-name>`
| **Azure Storage 계정** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

ACR(Azure Container Registry)은 현재 리소스 그룹 이름에서 유니코드 문자를 지원하지 않습니다. 이 문제를 완화하려면 이러한 문자를 포함하지 않는 리소스 그룹을 사용합니다.

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

리소스 그룹 작업에 대한 자세한 내용은 [az 그룹](/cli/azure/group)을 참조하세요.

### <a name="automatically-create-required-resources"></a>자동으로 필수 리소스 만들기

__서비스가 자동으로 생성되는__ 새 작업 영역을 만들려면 다음 명령을 사용합니다.

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

### <a name="virtual-network-and-private-endpoint"></a>가상 네트워크 및 프라이빗 엔드포인트

> [!IMPORTANT]
> Azure Government 지역에서는 프라이빗 링크로 Azure Machine Learning 작업 영역을 사용할 수 없습니다.

작업 영역에 대한 액세스를 가상 네트워크로 제한하려는 경우 다음 매개 변수를 사용할 수 있습니다.

* `--pe-name`: 만들어진 프라이빗 엔드포인트의 이름입니다.
* `--pe-auto-approval`: 작업 영역에 대한 프라이빗 엔드포인트 연결을 자동으로 승인해야 하는지 여부입니다.
* `--pe-resource-group`: 프라이빗 엔드포인트를 만들 리소스 그룹입니다. 가상 네트워크를 포함하는 그룹과 같아야 합니다.
* `--pe-vnet-name`: 프라이빗 엔드포인트를 만들 기존 가상 네트워크입니다.
* `--pe-subnet-name`: 프라이빗 엔드포인트를 만들 서브넷의 이름입니다. 기본값은 `default`입니다.

작업 영역에서 프라이빗 엔드포인트 및 가상 네트워크를 사용하는 방법에 대한 자세한 내용은 [가상 네트워크 격리 및 개인 정보 보호 개요](how-to-network-security-overview.md)를 참조하세요.

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>고객 관리형 키 및 높은 비즈니스 영향 작업 영역

기본적으로 작업 영역에 대한 메타데이터는 Microsoft에서 유지 관리하는 Azure Cosmos DB 인스턴스에 저장됩니다. 이 데이터는 Microsoft 관리형 키를 사용하여 암호화됩니다.

> [!NOTE]
> Azure Cosmos DB는 모델 성능, 실험에서 기록한 정보 또는 모델 배포에서 기록된 정보 등의 정보를 저장하는 데 사용되지 __않습니다__. 이러한 항목을 모니터링하는 방법에 대한 자세한 내용은 아키텍처 및 개념 문서의 [모니터링 및 로깅](concept-azure-machine-learning-architecture.md) 섹션을 참조하세요.

Microsoft 관리형 키를 사용하는 대신 사용자 고유 키를 사용할 수 있습니다. 이렇게 하면 Azure 구독에 메타데이터를 저장하는 Azure Cosmos DB 인스턴스가 만들어집니다. `--cmk-keyvault` 매개 변수를 사용하여 키가 포함된 Azure Key Vault를 지정하고 `--resource-cmk-uri`를 사용하여 자격 증명 모음 내 키의 URL을 지정합니다.

`--cmk-keyvault` 및 `--resource-cmk-uri` 매개 변수를 사용하기 전에 먼저 다음 작업을 수행해야 합니다.

1. 구독에 대한 기여자 권한으로 __Machine Learning 앱__(ID 및 액세스 관리에서)에 권한을 부여합니다.
1. [고객 관리형 키 구성](../cosmos-db/how-to-setup-cmk.md)의 단계에 따라 다음을 수행합니다.
    * Azure Cosmos DB 공급자 등록
    * Azure Key Vault 만들기 및 구성
    * 키 생성

Azure Cosmos DB 인스턴스는 수동으로 만들 필요가 없으며 작업 영역을 만드는 동안 생성됩니다. 이 Azure Cosmos DB 인스턴스는 `<your-resource-group-name>_<GUID>` 패턴을 기반으로 하는 이름을 사용하여 별도의 리소스 그룹에 만들어집니다.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Microsoft가 작업 영역에서 수집하는 데이터를 제한하려면 `--hbi-workspace` 매개 변수를 사용합니다. 

> [!IMPORTANT]
> 높은 비즈니스 영향을 선택하는 작업은 작업 영역을 만들 때만 수행할 수 있습니다. 작업 영역을 만든 후에는 이 설정을 변경할 수 없습니다.

고객 관리형 키 및 높은 비즈니스 영향 작업 영역에 대한 자세한 내용은 [Azure Machine Learning에 대한 엔터프라이즈 보안](concept-data-encryption.md#encryption-at-rest)을 참조하세요.

### <a name="use-existing-resources"></a>기존 리소스 사용

기존 리소스를 사용하는 작업 영역을 만들려면 리소스에 대한 ID를 제공해야 합니다. 다음 명령을 사용하여 서비스에 대한 ID를 가져옵니다.

> [!IMPORTANT]
> 기존 리소스를 모두 지정할 필요는 없습니다. 하나 이상의 리소스를 지정할 수 있습니다. 예를 들어, 기존 스토리지 계정을 지정할 수 있으며 작업 영역에서는 다른 리소스를 만듭니다.

+ **Azure Storage 계정**: `az storage account show --name <storage-account-name> --query "id"`

    이 명령의 응답은 다음 텍스트와 비슷하며 스토리지 계정의 ID입니다.

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > 기존 Azure Storage 계정을 사용하려는 경우 프리미엄 계정(Premium_LRS 및 Premium_GRS)으로 사용할 수 없습니다. 또한 계층 구조 네임스페이스(Azure Data Lake Storage Gen2와 함께 사용)를 가질 수 없습니다. Premium Storage 또는 계층 구조 네임스페이스는 작업 영역의 _기본_ 스토리지 계정에서 지원되지 않습니다. _기본이 아닌_ 스토리지 계정으로 Premium Storage 또는 계층 구조 네임스페이스를 사용할 수 있습니다.

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
    > Azure Machine Learning 작업 영역에서 사용할 수 있으려면 컨테이너 레지스트리에 [관리자 계정](../container-registry/container-registry-authentication.md#admin-account)을 사용하도록 설정해야 합니다.

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

자세한 내용은 [az ml workspace list](/cli/azure/ml/workspace#az_ml_workspace_list) 설명서를 참조하세요.

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

자세한 내용은 [az ml workspace show](/cli/azure/ml/workspace#az_ml_workspace_show) 설명서를 참조하세요.

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

자세한 내용은 [az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update) 설명서를 참조하세요.

## <a name="share-a-workspace-with-another-user"></a>다른 사용자와 작업 영역 공유

구독의 작업 영역을 다른 사용자와 공유하려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Azure Machine Learning의 Azure RBAC(Azure 역할 기반 액세스 제어)에 대한 자세한 내용은 [사용자 및 역할 관리](how-to-assign-roles.md)를 참조하세요.

자세한 내용은 [az ml workspace share](/cli/azure/ml/workspace#az_ml_workspace_share) 설명서를 참조하세요.

## <a name="sync-keys-for-dependent-resources"></a>종속 리소스의 키 동기화

작업 영역에서 사용하는 리소스 중 하나에 대한 액세스 키를 변경하는 경우 작업 영역이 새 키로 동기화되는 데 약 1시간이 소요됩니다. 작업 영역에서 새 키를 즉시 동기화하도록 하려면 다음 명령을 사용합니다.

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

키 변경에 대한 자세한 내용은 [스토리지 액세스 키 다시 생성](how-to-change-storage-access-key.md)을 참조하세요.

자세한 내용은 [az ml workspace sync-keys](/cli/azure/ml/workspace#az_ml_workspace_sync-keys) 설명서를 참조하세요.

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

자세한 내용은 [az ml workspace delete](/cli/azure/ml/workspace#az_ml_workspace_delete) 설명서를 참조하세요.

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

기계 학습용 Azure CLI 확장에 대한 자세한 내용은 [az ml](/cli/azure/ml) 설명서를 참조하세요.