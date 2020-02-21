---
title: Azure 앱 구성을 사용 하 여 관리 되는 id 구성
description: Azure 앱 구성에서 관리 id가 작동 하는 방법 및 관리 id를 구성 하는 방법에 대해 알아봅니다.
author: jpconnock
ms.topic: article
ms.date: 02/18/2020
ms.author: jeconnoc
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: 751b10c0d80412671028e85debb2dcd4422c8459
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493151"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Azure 앱 구성에 관리 id를 사용 하는 방법

이 항목에서는 Azure 앱 구성에 대 한 관리 되는 id를 만드는 방법을 보여 줍니다. AAD (Azure Active Directory에서 관리 되는 id를 사용 하면 Azure 앱 구성에서 Azure Key Vault 같은 다른 AAD로 보호 되는 리소스에 쉽게 액세스할 수 있습니다. Id는 Azure 플랫폼에서 관리 합니다. 암호를 프로 비전 하거나 회전할 필요가 없습니다. AAD의 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

애플리케이션에 두 가지 형식의 ID를 부여할 수 있습니다.

- **시스템 할당 id** 는 구성 저장소에 연결 됩니다. 구성 저장소가 삭제 된 경우 삭제 됩니다. 구성 저장소에는 시스템 할당 id가 하나만 있을 수 있습니다.
- **사용자 할당 id** 는 구성 저장소에 할당할 수 있는 독립 실행형 Azure 리소스입니다. 구성 저장소에는 사용자에 게 할당 된 id가 여러 개 있을 수 있습니다.

## <a name="adding-a-system-assigned-identity"></a>시스템 할당 ID 추가

시스템이 할당 한 id를 사용 하 여 앱 구성 저장소를 만들려면 저장소에 추가 속성을 설정 해야 합니다.

### <a name="using-the-azure-cli"></a>Azure CLI 사용

Azure CLI를 사용 하 여 관리 id를 설정 하려면 기존 구성 저장소에 대해 [az appconfig identity assign] 명령을 사용 합니다. 이 섹션의 예제를 실행하는 옵션은 세 가지가 있습니다.

- Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다.
- 아래 각 코드 블록의 오른쪽 위 모퉁이에 있는 "사용해 보세요." 단추를 통해 포함 된 Azure Cloud Shell를 사용 합니다.
- 로컬 CLI 콘솔을 사용 하려는 경우 [최신 버전의 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.1 이상)을 설치 합니다.

다음 단계는 응용 프로그램 구성 저장소를 만들고 CLI를 사용 하 여 id를 할당 하는 과정을 안내 합니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login]을 사용하여 먼저 Azure에 로그인합니다. Azure 구독과 연결 된 계정을 사용 합니다.

    ```azurecli-interactive
    az login
    ```

1. CLI를 사용 하 여 앱 구성 저장소를 만듭니다. Azure 앱 구성에서 CLI를 사용 하는 방법에 대 한 자세한 예제는 [앱 구성 CLI 샘플](scripts/cli-create-service.md)을 참조 하세요.

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. [Az appconfig identity assign] 명령을 실행 하 여이 구성 저장소에 대 한 시스템 할당 id를 만듭니다.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>사용자 할당 id 추가

사용자 할당 id를 사용 하 여 앱 구성 저장소를 만들려면 id를 만든 다음 해당 리소스 식별자를 저장소에 할당 해야 합니다.

### <a name="using-the-azure-cli"></a>Azure CLI 사용

Azure CLI를 사용 하 여 관리 id를 설정 하려면 기존 구성 저장소에 대해 [az appconfig identity assign] 명령을 사용 합니다. 이 섹션의 예제를 실행하는 옵션은 세 가지가 있습니다.

- Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다.
- 아래 각 코드 블록의 오른쪽 위 모퉁이에 있는 "사용해 보세요." 단추를 통해 포함 된 Azure Cloud Shell를 사용 합니다.
- 로컬 CLI 콘솔을 사용하려면 [최신 버전의 Azure CLI(2.0.31 이상)를 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.

다음 단계는 사용자 할당 id와 앱 구성 저장소를 만든 다음 CLI를 사용 하 여 id를 저장소에 할당 하는 과정을 안내 합니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login]을 사용하여 먼저 Azure에 로그인합니다. Azure 구독과 연결 된 계정을 사용 합니다.

    ```azurecli-interactive
    az login
    ```

1. CLI를 사용 하 여 앱 구성 저장소를 만듭니다. Azure 앱 구성에서 CLI를 사용 하는 방법에 대 한 자세한 예제는 [앱 구성 CLI 샘플](scripts/cli-create-service.md)을 참조 하세요.

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. CLI를 사용 하 여 `myUserAssignedIdentity` 이라는 사용자 할당 id를 만듭니다.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    이 명령의 출력에서 `id` 속성의 값을 확인 합니다.

1. [Az appconfig identity assign] 명령을 실행 하 여이 구성 저장소에 새 사용자 할당 id를 할당 합니다. 이전 단계에서 기록한 `id` 속성의 값을 사용 합니다.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Id 제거

Azure CLI에서 [az appconfig identity remove](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) 명령을 사용 하 여이 기능을 사용 하지 않도록 설정 하 여 시스템에 할당 된 id를 제거할 수 있습니다. 사용자 할당 ID는 개별 제거할 수 있습니다. 이런 방식으로 시스템 할당 ID를 제거하면 AAD에서도 삭제됩니다. 앱 리소스가 삭제될 때 시스템 할당 ID도 AAD에서 자동으로 제거됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 앱 구성을 사용 하 여 ASP.NET Core 앱 만들기](quickstart-aspnet-core-app.md)

[az appconfig identity assign]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
