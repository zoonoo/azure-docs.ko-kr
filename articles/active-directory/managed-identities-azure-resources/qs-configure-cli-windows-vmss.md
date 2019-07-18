---
title: Azure CLI를 사용 하 여 Azure 가상 머신 확장에 시스템 및 사용자 할당 관리 되는 id를 구성 하는 방법 설정
description: 단계는 Azure 가상 머신 확장에서 시스템 및 사용자 할당 관리 되는 id를 구성 하기 위한 지침 집합 Azure CLI를 사용 하 여.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: MarkusVi
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04a3c9eba1f6498796a7e617b400649963c996d1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60290929"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Azure CLI를 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID 구성

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure CLI를 사용 하는 Azure 가상 머신 확장 집합에 대 한 Azure 리소스 작업에 대 한 id 관리를 수행 하는 방법을 알아봅니다.
- Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하거나 사용하지 않도록 설정
- Azure 가상 머신 확장 집합에서 사용자 할당 관리 ID 추가 및 제거


## <a name="prerequisites"></a>필수 조건

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 이 문서의 관리 작업을 수행하려면 계정에 다음과 같은 Azure 역할 기반 액세스 제어가 할당되어야 합니다.

    > [!NOTE]
    > 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

    - [가상 머신 참가자](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor): 가상 머신 확장 집합을 만들고, 가상 머신 확장 집합에서 시스템 및/또는 사용자가 할당한 관리 ID를 사용하도록 설정하고 제거합니다.
    - [관리 ID 참가자](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) 역할: 사용자 할당 관리 ID를 만듭니다.
    - [관리 ID 운영자](/azure/role-based-access-control/built-in-roles#managed-identity-operator) 역할: 가상 머신 확장 집합에 사용자 할당 관리 ID를 할당하거나 이 집합에서 사용자 할당 관리 ID를 제거합니다.
- CLI 스크립트 예제는 다음의 세 가지 옵션 중 하나로 실행할 수 있습니다.
    - Azure Portal에서 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다(다음 섹션 참조).
    - 각 코드 블록의 오른쪽 위에 있는 "사용해 보세요." 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
    - 로컬 CLI 콘솔을 사용하려는 경우 [Azure CLI의 최신 버전을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다(2.0.13 이상). 
      
      > [!NOTE]
      > [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)의 최신 릴리스를 반영하도록 명령이 업데이트되었습니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

이 섹션에서는 Azure CLI를 사용 하 여 설정 하는 Azure 가상 머신 확장에 대 한 관리 되는 id 시스템 할당을 사용 하지 않도록 설정 하는 방법을 알아봅니다.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합을 만드는 동안 시스템 할당 관리 ID를 사용하도록 설정

시스템 할당 관리 ID를 사용하도록 설정된 가상 머신 확장 집합을 만들려면:

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/reference-index#az-login)을 사용하여 먼저 Azure에 로그인합니다. 가상 머신 확장 집합을 배포하려는 Azure 구독과 연결된 계정을 사용합니다.

   ```azurecli-interactive
   az login
   ```

2. [az group create](/cli/azure/group/#az-group-create)를 사용하여 가상 머신 확장 집합 및 관련 리소스를 포함하고 배포하기 위한 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md#terminology)을 만듭니다. 대신 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다.

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. [az vmss create](/cli/azure/vmss/#az-vmss-create)를 사용하여 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 `--assign-identity` 매개 변수에서 요청한 대로 시스템 할당 관리 ID를 사용하여 *myVMSS*라는 가상 머신 확장 집합을 만듭니다. `--admin-username` 및 `--admin-password` 매개 변수는 가상 머신 로그인을 위한 관리자 이름 및 암호 계정을 지정합니다. 이러한 값은 사용자 환경에 적절하게 업데이트합니다. 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>기존 Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하도록 설정

기존 Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하도록 설정해야 하는 경우:

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/reference-index#az-login)을 사용하여 먼저 Azure에 로그인합니다. 가상 머신 확장 집합이 포함된 Azure 구독과 연결된 계정을 사용합니다.

   ```azurecli-interactive
   az login
   ```

2. [az vm identity assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) 명령을 사용하여 기존 VM에 시스템 할당 관리 ID를 사용하도록 설정합니다.

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하지 않도록 설정

시스템 할당 관리 ID는 더 이상 필요하지 않고 사용자 할당 관리 ID는 여전히 필요한 가상 머신 확장 집합이 있는 경우 다음 명령을 사용합니다.

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

시스템 할당 관리 ID는 더 이상 필요하지 않고 사용자 할당 관리 ID가 없는 가상 머신이 있는 경우 다음 명령을 사용합니다.

> [!NOTE]
> `none` 값은 대/소문자를 구분합니다. 소문자여야 합니다. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

> [!NOTE]
> 사용 하 여 제거 해야 하는 경우 (사용 되지 않음)에 VM 확장을 Azure 리소스에 대 한 관리 되는 id를 프로 비전 [az vmss 확장 삭제](https://docs.microsoft.com/cli/azure/vm/)합니다. 자세한 내용은 [VM 확장에서 인증에 대 한 Azure IMDS로 마이그레이션](howto-migrate-vm-extension.md)합니다.

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

이 섹션에서는 Azure CLI를 사용하여 사용자 할당 관리 ID를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>가상 머신 확장 집합을 만드는 동안 사용자 할당 관리 ID를 할당합니다.

이 섹션에서는 사용자 할당 관리 id 할당 및 가상 머신 확장 집합 만들기를 안내 가상 머신 확장 집합입니다. 사용 하려는 가상 머신 확장 집합에 이미 있는 경우이 섹션을 건너뛰고 진행 합니다.

1. 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다. [az group create](/cli/azure/group/#az-group-create)를 사용하여 사용자 할당 관리 ID를 포함하고 배포하는 데 사용할 [리소스 그룹](~/articles/azure-resource-manager/resource-group-overview.md#terminology)을 만듭니다. `<RESOURCE GROUP>` 및 `<LOCATION>` 매개 변수 값을 원하는 값으로 바꾸세요. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. [az identity create](/cli/azure/identity#az-identity-create)를 사용하여 사용자 할당 관리 ID를 만듭니다.  `-g` 매개 변수는 사용자 할당 관리 ID가 만들어진 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   응답에는 다음과 같이 생성된 사용자가 할당한 관리 ID에 대한 세부 정보가 포함됩니다. 사용자 할당 관리 ID에 할당된 리소스 `id` 값은 다음 단계에서 사용됩니다.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. 사용 하 여 설정 하는 가상 머신 확장을 만듭니다 [az vmss 만들기](/cli/azure/vmss/#az-vmss-create)합니다. 다음 예제에서는 새 사용자 할당 관리 서비스 id에 지정 된 대로 연결 된 가상 머신 확장 집합을 만듭니다는 `--assign-identity` 매개 변수입니다. `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` 및 `<USER ASSIGNED IDENTITY>` 매개 변수 값을 원하는 값으로 바꾸세요. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>기존 가상 머신 확장 집합에 사용자 할당 관리 ID 할당

1. [az identity create](/cli/azure/identity#az-identity-create)를 사용하여 사용자 할당 관리 ID를 만듭니다.  `-g` 매개 변수는 사용자 할당 관리 ID가 만들어진 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   응답에는 다음과 같이 생성된 사용자가 할당한 관리 ID에 대한 세부 정보가 포함됩니다.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. 가상 머신 확장에 사용자 할당 관리 되는 id를 사용 하 여 설정 하는 할당 [az vmss identity 할당](/cli/azure/vmss/identity)합니다. `<RESOURCE GROUP>` 및 `<VIRTUAL MACHINE SCALE SET NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<USER ASSIGNED IDENTITY>`는 이전 단계에서 만든 대로 사용자 할당 ID의 리소스 `name` 속성입니다.

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 사용자가 할당한 관리 ID 제거

가상 머신 확장 집합에서 사용자 할당 관리 ID를 제거하려면 [az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove)를 사용합니다. 가상 머신 확장 집합에 할당된 유일한 사용자 할당 관리 ID인 경우 `UserAssigned`는 ID 유형 값에서 제거됩니다.  `<RESOURCE GROUP>` 및 `<VIRTUAL MACHINE SCALE SET NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<USER ASSIGNED IDENTITY>`는 사용자 할당 관리 ID의 `name` 속성이며 `az vmss identity show`를 사용하여 가상 머신 확장 집합의 ID 섹션에서 찾을 수 있습니다.

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

가상 머신 확장 집합에 시스템 할당 관리 ID가 없고 모든 사용자 할당 관리 ID를 제거하려는 경우 다음 명령을 사용합니다.

> [!NOTE]
> `none` 값은 대/소문자를 구분합니다. 소문자여야 합니다.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

가상 머신 확장 집합에 시스템 할당 및 사용자 할당 관리 ID가 모두 있는 경우 시스템 할당 관리 ID만 사용하도록 전환하여 모든 사용자 할당 관리 ID를 제거할 수 있습니다. 다음 명령을 사용합니다.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID 개요](overview.md)
- Azure 가상 머신 확장 집합 만들기에 대한 빠른 시작은 다음을 참조하세요. 

  - [CLI를 사용하여 가상 머신 확장 집합 만들기](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















