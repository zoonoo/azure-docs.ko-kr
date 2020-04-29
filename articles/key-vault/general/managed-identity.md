---
title: 시스템 할당 관리 id를 사용 하 여 Azure Key Vault에 액세스
description: App Service 응용 프로그램에 대 한 관리 되는 id를 만드는 방법 및이를 사용 하 여에 액세스 하는 방법에 대해 알아봅니다 Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: bb5288d043ab5638bb33c357cea55c64b03fcf1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432126"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>관리 id를 사용 하 여 Key Vault 인증 제공

Azure Active Directory에서 관리 되는 id를 사용 하면 앱에서 다른 Azure AD로 보호 되는 리소스에 쉽게 액세스할 수 있습니다. ID는 Azure 플랫폼에서 관리하며 비밀을 프로비전하거나 회전할 필요가 없습니다. 자세한 내용은 [Azure 리소스에 대한 ID 관리](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요. 

이 문서에서는 App Service 응용 프로그램에 대 한 관리 되는 id를 만들고이를 사용 하 여 Azure Key Vault에 액세스 하는 방법을 보여 줍니다. Azure Vm에 호스트 된 응용 프로그램의 경우 [WINDOWS vm 시스템 할당 관리 id를 사용 하 여 Azure Key Vault에 액세스](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)를 참조 하세요.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>전제 조건 

이 가이드를 완료 하려면 다음 리소스가 있어야 합니다. 

- 키 자격 증명 모음. 다음 빠른 시작 중 하나의 단계에 따라 기존 키 자격 증명 모음을 사용하거나 새로 만들 수 있습니다.
   - [Azure CLI를 사용하여 키 자격 증명 모음 만들기](../secrets/quick-create-cli.md)
   - [Azure PowerShell을 사용하여 키 자격 증명 모음 만들기](../secrets/quick-create-powershell.md)
   - [Azure Portal을 사용하여 키 자격 증명 모음 만들기](../secrets/quick-create-portal.md)
- Key vault 액세스 권한을 부여 하는 기존 App Service 응용 프로그램입니다. [App Service 설명서](../../app-service/overview.md)의 단계에 따라 신속 하 게 만들 수 있습니다.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 또는 [Azure PowerShell](/powershell/azure/overview). 또는 [Azure Portal](https://portal.azure.com)을 사용할 수 있습니다.


## <a name="adding-a-system-assigned-identity"></a>시스템 할당 ID 추가 

먼저 시스템에 할당 된 id를 응용 프로그램에 추가 해야 합니다. 
 
### <a name="azure-portal"></a>Azure portal 

포털에서 관리 ID를 설정하려면 먼저 정상적으로 애플리케이션을 만든 다음, 기능을 사용하도록 설정합니다. 

1. 함수 앱을 사용하는 경우 **플랫폼 기능**으로 이동합니다. 다른 유형의 앱을 사용하는 경우 왼쪽 탐색 창에서 **설정** 그룹이 나올 때까지 아래로 스크롤합니다. 

1. **관리 ID**를 선택합니다. 

1. **시스템 할당** 탭에서 **상태**를 **켜기**로 바꿉니다. **저장**을 클릭합니다. 

    ![](../media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

이 빠른 시작에는 Azure CLI 버전 2.0.4 이상을 이상이 필요 합니다. `az --version`을 실행하여 현재 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요. 

Azure CLI를 사용 하 여 로그인 하려면 [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login) 명령을 사용 합니다.

```azurecli-interactive
az login
```

Azure CLI 로그인 옵션에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)을 참조 하세요. 

이 응용 프로그램에 대 한 id를 만들려면 Azure CLI [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) 명령 또는 [az functionapp identity assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) 명령을 사용 합니다.


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

다음 섹션에서 필요한 `PrincipalId`를 적어 둡니다.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>앱에 대 한 액세스 권한 부여 Key Vault 

### <a name="azure-portal"></a>Azure portal

1.  Key Vault 리소스로 이동 합니다. 

1.  **액세스** 정책을 선택 하 고 **액세스 정책 추가**를 클릭 합니다. 

1.  **비밀 권한**에서 **가져오기, 목록**을 차례로 선택 합니다. 

1.  **보안 주체 선택**을 선택 하 고 검색 필드에 앱의 이름을 입력 합니다.  결과 목록에서 앱을 선택 하 고 **선택**을 클릭 합니다. 

1.  **추가** 를 클릭 하 여 새 액세스 정책 추가를 마칩니다.

    ![](../media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

응용 프로그램에 키 자격 증명 모음에 대 한 액세스 권한을 부여 하려면 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 명령을 사용 하 여 위에서 기록한 **principalid** 를 사용 하 여 **ObjectId** 매개 변수를 제공 합니다.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 보안: ID 및 액세스 관리](overview-security.md#identity-and-access-management)
- [액세스 제어 정책을 사용하여 Key Vault 인증 제공](group-permissions-for-apps.md)
- [키 자격 증명 모음 보안](secure-your-key-vault.md)).
- [Azure Key Vault 개발자 가이드](developers-guide.md)
- [Azure Key Vault 모범 사례](best-practices.md) 검토