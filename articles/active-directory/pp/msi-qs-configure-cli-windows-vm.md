---
title: "Azure CLI를 사용하여 Azure VM에 사용자 할당 MSI를 구성하는 방법"
description: "Azure CLI를 사용하여 Azure VM에 사용자 할당 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침을 제공합니다."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 495ed6daf0d73d89a4bc572f6bccf294cee7decb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>Azure CLI를 사용하여 VM에 사용자 할당 MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

관리 서비스 ID는 Azure Active Directory에서 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure CLI를 사용하여 Azure VM에 대해 사용자 할당 MSI를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

이 자습서의 CLI 스크립트 예제는 두 가지 옵션을 통해 실행할 수 있습니다.

- Azure Portal에서 또는 각 코드 블록의 오른쪽 상단 모서리에 있는 "사용해 보세요" 단추를 통해 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)을 사용합니다.
- 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.23 이상)을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다. 그런 다음 [az login](/cli/azure/#az_login)을 사용하여 Azure에 로그인합니다. 사용자 할당 MSI 및 VM을 배포하려는 Azure 구독과 연결된 계정을 사용하세요.

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Azure VM 생성 중에 MSI를 사용하도록 설정

이 섹션에서는 VM을 만들고 VM에 사용자 할당 MSI를 할당하는 방법을 설명합니다. 사용하려는 VM이 이미 있는 경우 이 섹션을 건너뛰고 다음으로 진행합니다.

1. 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다. [az group create](/cli/azure/group/#az_group_create)를 사용하여 MSI를 포함하고 배포하는 데 사용할 [리소스 그룹](~/articles/azure-resource-manager/resource-group-overview.md#terminology)을 만듭니다. `<RESOURCE GROUP>` 및 `<LOCATION>` 매개 변수 값을 원하는 값으로 바꾸세요. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. [az identity create](/cli/azure/identity#az_identity_create)를 사용하여 사용자 할당 MSI를 만듭니다.  `-g` 매개 변수는 MSI가 만들어지는 리소스 그룹을 지정하고, `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<MSI NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
응답에는 다음과 유사하게 생성된 사용자 할당 MSI에 대한 세부 정보가 포함됩니다. MSI에 할당된 리소스 `id` 값은 다음 단계에서 사용됩니다.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. [az vm create](/cli/azure/vm/#az_vm_create)를 사용하여 VM을 만듭니다. 다음 예제에서는 `--assign-identity` 매개 변수에서 지정한 대로 새 사용자 할당 MSI와 연결된 VM을 만듭니다. 이전 단계에서 만든 `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` 및 `<`MSI ID >` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource `id' 속성을 바꿔야 합니다. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>기존 Azure VM에서 MSI를 사용하도록 설정

1. [az identity create](/cli/azure/identity#az_identity_create)를 사용하여 사용자 할당 MSI를 만듭니다.  `-g` 매개 변수는 MSI가 만들어지는 리소스 그룹을 지정하고, `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<MSI NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
응답에는 다음과 유사하게 생성된 사용자 할당 MSI에 대한 세부 정보가 포함됩니다. MSI에 할당된 리소스 `id` 값은 다음 단계에서 사용됩니다.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity)를 사용하여 VM에 사용자 할당 MSI를 할당합니다. `<RESOURCE GROUP>` 및 `<VM NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<MSI ID>`은 이전 단계에서 만든 대로 사용자 할당 MSI의 리소스 `id` 속성입니다.

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM에서 MSI 제거

1. [az vm remove-identity](/cli/azure/vm#az_vm_remove_identity)를 사용하여 VM에서 사용자 할당 MSI를 제거합니다. `<RESOURCE GROUP>` 및 `<VM NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<MSI NAME>`은 `az identity create` 명령 중에 지정된 대로 사용자 할당 MSI의 `name` 속성입니다(이전 섹션의 예제 참조).

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>다음 단계

- [관리 서비스 ID 개요](msi-overview.md)
- 전체 Azure VM 만들기 퀵 스타트는 다음 참조: 

  - [CLI를 사용하여 Windows 가상 머신 만들기](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [CLI를 사용하여 Linux 가상 머신 만들기](~/articles/virtual-machines/linux/quick-create-cli.md) 

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.
















