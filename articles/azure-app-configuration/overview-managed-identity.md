---
title: Azure App Configuration을 사용하여 관리 ID 구성
description: Azure App Configuration에서 관리 ID가 작동하는 방식 및 구성하는 방법에 대해 알아봅니다.
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: 270f8d471a95e67939a31357bc1cc056fe8ea4a1
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072801"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Azure App Configuration에서 관리 ID를 사용하는 방법

이 토픽에서는 Azure App Configuration에 대한 관리 ID를 만드는 방법을 보여 줍니다. AAD(Azure Active Directory)의 관리 ID를 사용하면 Azure App Configuration에서 다른 AAD 보호 리소스(예: Azure Key Vault)에 쉽게 액세스할 수 있습니다. ID는 Azure 플랫폼에서 관리합니다. 비밀을 프로비저닝하거나 회전할 필요가 없습니다. AAD의 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

애플리케이션에 두 가지 형식의 ID를 부여할 수 있습니다.

- **시스템 할당 ID**는 구성 저장소에 연결됩니다. 구성 저장소가 삭제된 경우에는 삭제됩니다. 구성 저장소에는 하나의 시스템 할당 ID만 있을 수 있습니다.
- **사용자 할당 ID**는 구성 저장소에 할당할 수 있는 독립 실행형 Azure 리소스입니다. 구성 저장소에는 여러 사용자 할당 ID가 있을 수 있습니다.

## <a name="adding-a-system-assigned-identity"></a>시스템 할당 ID 추가

시스템 할당 ID를 사용하여 App Configuration 저장소를 만들려면 저장소에서 추가 속성을 설정해야 합니다.

### <a name="using-the-azure-cli"></a>Azure CLI 사용

Azure CLI를 사용하여 관리 ID를 설정하려면 기존 구성 저장소에 대해 [az appconfig identity assign] 명령을 사용합니다. 이 섹션의 예제를 실행하는 옵션은 세 가지가 있습니다.

- Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다.
- 아래 각 코드 블록의 오른쪽 위에 있는 “사용해 보세요” 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
- 로컬 CLI 콘솔을 사용하려면 [최신 버전의 Azure CLI(2.1 이상)를 설치](/cli/azure/install-azure-cli)합니다.

다음 단계는 App Configuration 저장소를 만들고 CLI를 사용하여 ID를 할당하는 과정을 안내합니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login]을 사용하여 먼저 Azure에 로그인합니다. Azure 구독과 연결된 계정을 사용합니다.

    ```azurecli-interactive
    az login
    ```

1. CLI를 사용하여 App Configuration 저장소를 만듭니다. Azure App Configuration과 함께 CLI를 사용하는 방법에 대한 자세한 예제는 [App Configuration CLI 샘플](scripts/cli-create-service.md)을 참조하세요.

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. [az appconfig identity assign] 명령을 실행하여 이 구성 저장소에 대한 시스템 할당 ID를 만듭니다.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>사용자 할당 ID 추가

사용자 할당 ID로 App Configuration 저장소를 만들려면 ID를 만든 다음, 저장소에 해당 리소스 ID를 할당해야 합니다.

### <a name="using-the-azure-cli"></a>Azure CLI 사용

Azure CLI를 사용하여 관리 ID를 설정하려면 기존 구성 저장소에 대해 [az appconfig identity assign] 명령을 사용합니다. 이 섹션의 예제를 실행하는 옵션은 세 가지가 있습니다.

- Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다.
- 아래 각 코드 블록의 오른쪽 위에 있는 “사용해 보세요” 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
- 로컬 CLI 콘솔을 사용하려면 [최신 버전의 Azure CLI(2.0.31 이상)를 설치](/cli/azure/install-azure-cli)합니다.

다음 단계는 사용자 할당 ID와 App Configuration 저장소를 만든 다음, CLI를 사용하여 ID를 저장소에 할당하는 과정을 안내합니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login]을 사용하여 먼저 Azure에 로그인합니다. Azure 구독과 연결된 계정을 사용합니다.

    ```azurecli-interactive
    az login
    ```

1. CLI를 사용하여 App Configuration 저장소를 만듭니다. Azure App Configuration과 함께 CLI를 사용하는 방법에 대한 자세한 예제는 [App Configuration CLI 샘플](scripts/cli-create-service.md)을 참조하세요.

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. CLI를 사용하여 `myUserAssignedIdentity`라는 사용하여 사용자 할당 ID를 만듭니다.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    이 명령의 출력에서 `id` 속성의 값을 적어둡니다.

1. [az appconfig identity assign] 명령을 실행하여 이 구성 저장소에 새로운 사용자 할당 ID를 할당합니다. 이전 단계에서 적어두었던 `id` 속성의 값을 사용합니다.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>ID 제거

시스템 할당 ID는 Azure CLI에서 [az appconfig identity remove](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) 명령을 사용하여 기능을 사용하지 않도록 설정하여 제거할 수 있습니다. 사용자 할당 ID는 개별 제거할 수 있습니다. 이런 방식으로 시스템 할당 ID를 제거하면 AAD에서도 삭제됩니다. 앱 리소스가 삭제될 때 시스템 할당 ID도 AAD에서 자동으로 제거됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure App Configuration을 사용하여 ASP.NET Core 앱 만들기](quickstart-aspnet-core-app.md)

[az appconfig identity assign]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login