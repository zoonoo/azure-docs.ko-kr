---
title: Azure 앱 구성으로 관리되는 ID 구성
description: Azure 앱 구성에서 관리되는 ID가 작동하는 방법과 관리되는 ID를 구성하는 방법에 대해 알아봅니다.
author: jpconnock
ms.topic: article
ms.date: 02/25/2020
ms.author: jeconnoc
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: fe66466395a100221e6a3cdebdef870bdf195afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623036"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Azure 앱 구성에 관리되는 ID를 사용하는 방법

이 항목에서는 Azure 앱 구성에 대해 관리되는 ID를 만드는 방법을 보여 주며 있습니다. AAD(Azure Active Directory)에서 관리되는 ID를 사용하면 Azure 앱 구성에서 Azure 키 자격 증명 모음과 같은 다른 AAD 보호 리소스에 쉽게 액세스할 수 있습니다. ID는 Azure 플랫폼에서 관리합니다. 기밀을 프로비전하거나 회전할 필요는 없습니다. AAD의 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

애플리케이션에 두 가지 형식의 ID를 부여할 수 있습니다.

- **시스템 할당 ID는** 구성 저장소에 연결됩니다. 구성 저장소가 삭제되면 삭제됩니다. 구성 저장소에는 하나의 시스템 할당 ID만 있을 수 있습니다.
- **사용자 할당된 ID는** 구성 저장소에 할당할 수 있는 독립 실행형 Azure 리소스입니다. 구성 저장소에는 여러 명의 사용자 할당된 ID가 있을 수 있습니다.

## <a name="adding-a-system-assigned-identity"></a>시스템 할당 ID 추가

시스템 할당된 ID를 사용하여 앱 구성 저장소를 만들려면 저장소에 추가 속성을 설정해야 합니다.

### <a name="using-the-azure-cli"></a>Azure CLI 사용

Azure CLI를 사용하여 관리되는 ID를 설정하려면 [az appconfig id] 가 기존 구성 저장소에 대해 명령을 할당하는 명령을 사용합니다. 이 섹션의 예제를 실행하는 옵션은 세 가지가 있습니다.

- Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다.
- 아래 각 코드 블록의 오른쪽 상단 모서리에 있는 "Try It" 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
- 로컬 [CLI 콘솔을](https://docs.microsoft.com/cli/azure/install-azure-cli) 사용하려는 경우 Azure CLI(2.1 이상)의 최신 버전을 설치합니다.

다음 단계에서는 앱 구성 저장소를 만들고 CLI를 사용하여 ID를 할당하는 단계를 안내합니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login]을 사용하여 먼저 Azure에 로그인합니다. Azure 구독과 연결된 계정을 사용합니다.

    ```azurecli-interactive
    az login
    ```

1. CLI를 사용하여 앱 구성 저장소를 만듭니다. Azure 앱 구성에서 CLI를 사용하는 방법에 대한 자세한 예는 [앱 구성 CLI 샘플을](scripts/cli-create-service.md)참조하십시오.

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. az [appconfig ID 할당] 명령을 실행하여 이 구성 저장소에 대해 시스템 할당된 ID를 만듭니다.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>사용자 할당된 ID 추가

사용자 할당된 ID를 사용하여 App Configuration 저장소를 만들려면 ID를 만든 다음 해당 리소스 식별자를 저장소에 할당해야 합니다.

### <a name="using-the-azure-cli"></a>Azure CLI 사용

Azure CLI를 사용하여 관리되는 ID를 설정하려면 [az appconfig id] 가 기존 구성 저장소에 대해 명령을 할당하는 명령을 사용합니다. 이 섹션의 예제를 실행하는 옵션은 세 가지가 있습니다.

- Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다.
- 아래 각 코드 블록의 오른쪽 상단 모서리에 있는 "Try It" 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
- 로컬 CLI 콘솔을 사용하려면 [최신 버전의 Azure CLI(2.0.31 이상)를 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.

다음 단계에서는 사용자 할당ID 및 앱 구성 저장소를 만든 다음 CLI를 사용하여 저장소에 ID를 할당하는 단계를 안내합니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login]을 사용하여 먼저 Azure에 로그인합니다. Azure 구독과 연결된 계정을 사용합니다.

    ```azurecli-interactive
    az login
    ```

1. CLI를 사용하여 앱 구성 저장소를 만듭니다. Azure 앱 구성에서 CLI를 사용하는 방법에 대한 자세한 예는 [앱 구성 CLI 샘플을](scripts/cli-create-service.md)참조하십시오.

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. CLI를 사용하여 호출된 `myUserAssignedIdentity` 사용자 할당ID를 만듭니다.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    이 명령의 출력에서 속성의 값을 `id` 기록합니다.

1. az [appconfig ID 할당] 명령을 실행하여 이 구성 저장소에 새 사용자 할당 ID를 할당합니다. 이전 단계에서 언급한 `id` 속성값을 사용합니다.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>ID 제거

Azure CLI에서 [az appconfig ID 제거](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) 명령을 사용하여 기능을 비활성화하여 시스템 할당 ID를 제거할 수 있습니다. 사용자 할당 ID는 개별 제거할 수 있습니다. 이런 방식으로 시스템 할당 ID를 제거하면 AAD에서도 삭제됩니다. 앱 리소스가 삭제될 때 시스템 할당 ID도 AAD에서 자동으로 제거됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [App Configuration을 사용하여 ASP.NET Core 앱 만들기](quickstart-aspnet-core-app.md)

[az appconfig ID 할당]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[아즈 로그인]: /cli/azure/reference-index#az-login
