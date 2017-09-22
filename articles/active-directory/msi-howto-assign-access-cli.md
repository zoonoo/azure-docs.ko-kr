---
title: "Azure CLI를 사용하여 MSI에 Azure 리소스 액세스 권한을 할당하는 방법"
description: "Azure CLI를 사용하여 특정 리소스에 MSI를 할당하고 다른 리소스에 대한 액세스 권한을 할당하기 위한 단계별 지침을 제공합니다."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: e6eede1c093145894f4330a0c4385969cd4dd7da
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---

# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Azure CLI를 사용하여 MSI(관리 서비스 ID)에 리소스 액세스 권한 할당

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

MSI를 사용하여 Azure 리소스를 구성한 후에는 모든 보안 주체와 마찬가지로 MSI에 다른 리소스 액세스 권한을 제공할 수 있습니다. 이 예제에서는 Azure CLI를 사용하여 Azure 가상 컴퓨터의 MSI에 Azure Storage 계정 액세스 권한을 제공하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

CLI 스크립트 예제는 다음의 세 가지 옵션 중 하나로 실행할 수 있습니다.

- Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다(다음 섹션 참조).
- 각 코드 블록의 오른쪽 위에 있는 "사용해 보세요." 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
- 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.13 이상)을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>RBAC를 사용하여 MSI에 다른 리소스 액세스 권한 할당

[Azure VM](msi-qs-configure-cli-windows-vm.md)과 같은 Azure 리소스에서 MSI를 사용하도록 설정한 후에 다음을 수행합니다. 

1. Azure Portal에서 Azure Cloud Shell을 사용하고 있지 않다면 먼저 [az login](/cli/azure/#login)을 사용하여 Azure에 로그인합니다. VM을 배포하려는 Azure 구독과 연결된 계정을 사용하세요.

   ```azurecli-interactive
   az login
   ```

2. 이 예제에서는 Azure VM에 저장소 계정 액세스 권한을 제공합니다. 먼저 [az resource list](/cli/azure/resource/#list)를 사용하여 VM에서 MSI를 사용하도록 설정할 때 만든 VM "myVM"의 서비스 주체를 가져옵니다.

   ```azurecli-interactive
   $spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```

3. 서비스 주체 ID를 가져온 후에는 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)를 사용하여 저장소 계정 "myStorageAcct"에 대한 "Reader" 액세스 권한을 VM에 제공합니다.

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>문제 해결

리소스의 MSI가 사용 가능한 ID 목록에 표시되지 않으면 MSI가 올바르게 사용하도록 설정되었는지 확인하세요. 이 예제에서는 [Azure Portal](https://portal.azure.com)에서 Azure VM으로 돌아가서 다음을 수행할 수 있습니다.

- "구성" 페이지에서 MSI enabled = "Yes"인지 확인합니다.
- "확장" 페이지에서 MSI 확장이 올바르게 배포되었는지 확인합니다.

이 두 항목 중 하나가 올바르지 않으면 리소스에서 MSI를 재배포하거나 배포 오류 관련 문제를 해결해야 할 수 있습니다.

## <a name="related-content"></a>관련 콘텐츠

- MSI의 개요는 [관리 서비스 ID 개요](msi-overview.md)를 참조하세요.
- Azure VM에서 MSI를 사용하도록 설정하려면 [Azure CLI를 사용하여 Azure VM MSI(관리 서비스 ID) 구성](msi-qs-configure-cli-windows-vm.md)을 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.


