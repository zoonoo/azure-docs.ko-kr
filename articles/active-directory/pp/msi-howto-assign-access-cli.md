---
title: "Azure CLI를 사용하여 사용자 할당 MSI에 Azure 리소스 액세스 권한을 할당하는 방법"
description: "Azure CLI를 사용하여 특정 리소스에 사용자 할당 MSI를 할당하고 다른 리소스에 액세스 권한을 할당하기 위한 단계별 지침을 제공합니다."
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
ms.openlocfilehash: 8cdfd80f436a9ef39426da9d3f2f76ae59677847
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Azure CLI를 사용하여 사용자 할당 MSI(관리 서비스 ID)에 리소스 액세스 권한 할당

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

사용자 할당 MSI를 만든 후에는 모든 보안 주체와 마찬가지로 해당 MSI에 다른 리소스 액세스 권한을 제공할 수 있습니다. 이 예제에서는 Azure CLI를 사용하여 사용자 할당 MSI에 Azure Storage 계정 액세스 권한을 제공하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

이 자습서의 CLI 스크립트 예제는 두 가지 옵션을 통해 실행할 수 있습니다.

- Azure Portal에서 또는 각 코드 블록의 오른쪽 상단 모서리에 있는 "사용해 보세요" 단추를 통해 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)을 사용합니다.
- 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.23 이상)을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다. 그런 다음 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure에 로그인합니다. 사용자 할당 MSI 및 VM을 배포하려는 Azure 구독과 연결된 계정을 사용하세요.

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>RBAC를 사용하여 MSI에 다른 리소스 액세스 권한 할당

로그인 또는 리소스 액세스용으로 VM 등의 호스트 리소스에 MSI를 사용하려면 먼저 역할 할당을 통해 MSI에 리소스 액세스 권한을 부여해야 합니다. MSI를 호스트에 연결하기 전이나 연결한 후에도 이 작업을 수행할 수 있습니다. MSI를 만들고 VM과 연결하는 전체 단계는 [Azure CLI를 사용하여 VM용 사용자 할당 MSI 구성](msi-qs-configure-cli-windows-vm.md)을 참조하세요.

다음 예제에서는 사용자 할당 MSI에 저장소 계정 액세스 권한을 제공합니다.  

1. MSI에 액세스 권한을 제공하려면 MSI 서비스 주체의 클라이언트 ID(앱 ID라고도 함)가 필요합니다. 클라이언트 ID는 MSI 및 해당 서비스 주체(여기에 참조용으로 나와 있음)를 프로비전하면 [az identity create](/cli/azure/identity#az_identity_create)를 통해 제공됩니다.

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   올바른 응답의 `clientId` 속성에는 사용자 할당 MSI 서비스 주체의 클라이언트 ID가 포함되어 있습니다.

   ```json
   {
        "clientId": "9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp/credentials?tid=733a8f0p-ec41-4e69-8adz-971fc4b533bl&oid=z4baa4fc-fce4-4202-8250-5fb359abblll&aid=9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp",
        "location": "westcentralus",
        "name": "msiServiceApp",
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "resourceGroup": "rgPrivate",
        "tags": {},
        "tenantId": "733a8f0p-ec41-4e69-8adz-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
   }
   ```

2. 클라이언트 ID가 확인되면 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)를 사용하여 다른 리소스 액세스 권한을 MSI에 할당합니다. `--assignee` 매개 변수에 이 클라이언트 ID를 사용해야 하며 이 ID와 일치하는 구독 ID 및 리소스 그룹 이름(`az identity create` 실행 시 반환됨)을 사용해야 합니다. 여기서는 "myStorageAcct" 저장소 계정에 대한 "독자" 권한을 MSI에 할당합니다.

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   올바른 응답은 다음 출력과 비슷합니다.

   ```json
   {
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct/providers/Microsoft.Authorization/roleAssignments/f4766864-9493-43c6-91d7-abd131c3c017",
        "name": "f4766864-9493-43c6-91d7-abd131c3c017",
        "properties": {
            "additionalProperties": {
            "createdBy": null,
            "createdOn": "2017-12-21T20:49:37.5590544Z",
            "updatedBy": "pd78b21f-17a4-41az-b7db-9aadec3376bl",
            "updatedOn": "2017-12-21T20:49:37.5590544Z"
            },
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "roleDefinitionId": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "scope": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct"
        },
        "resourceGroup": "rgPrivate",
        "type": "Microsoft.Authorization/roleAssignments"
   }
   ```

## <a name="next-steps"></a>다음 단계

- MSI의 개요는 [관리 서비스 ID 개요](msi-overview.md)를 참조하세요.
- Azure VM에서 사용자 할당 MSI를 사용하도록 설정하려면 [Azure CLI를 사용하여 VM용 사용자 할당 MSI(관리 서비스 ID) 구성](msi-qs-configure-cli-windows-vm.md)을 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

