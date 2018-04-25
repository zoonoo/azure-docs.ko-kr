---
title: Linux VM 사용자 할당 MSI를 사용하여 Azure Resource Manager에 액세스
description: Linux VM에서 사용자 할당 MSI(관리 서비스 ID)를 사용하여 Azure Resource Manager에 액세스하는 프로세스를 설명하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c2b6d70e441dc3d300f49adff1c02d7cc65788d2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>Linux VM에서 사용자 할당 MSI(관리 서비스 ID)를 사용하여 Azure Resource Manager에 액세스

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

이 자습서에서는 사용자 할당 MSI(관리 서비스 ID)를 만들고, Linux VM(Virtual Machine)에 할당하고, 해당 ID를 사용하여 Azure Resource Manager API에 액세스하는 방법을 설명합니다. 

관리 서비스 ID는 Azure에서 자동으로 관리됩니다. 그러면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다.

다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 사용자 할당 MSI 만들기
> * Linux VM에 MSI 할당 
> * MSI에 Azure Resource Manager의 리소스 그룹 액세스 권한 부여 
> * MSI를 사용하여 액세스 토큰을 가져온 다음 Azure Resource Manager를 호출하는 데 사용 

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

이 자습서의 CLI 스크립트 예제는 두 가지 옵션을 통해 실행할 수 있습니다.

- Azure Portal에서 또는 각 코드 블록의 오른쪽 상단 모서리에 있는 "사용해 보세요" 단추를 통해 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)을 사용합니다.
- 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.23 이상)을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>새 리소스 그룹에 Linux Virtual Machine 만들기

이 자습서에서는 먼저 새 Linux VM을 만듭니다. 기존 VM을 사용할 수도 있습니다.

1. Azure Portal의 왼쪽 위에서 **리소스 만들기**를 클릭합니다.
2. **Compute**를 선택한 후 **Ubuntu Server 16.04 LTS**를 선택합니다.
3. 가상 머신 정보를 입력합니다. **인증 유형**으로 **SSH 공용 키** 또는 **암호**를 선택합니다. 생성된 자격 증명을 사용하면 VM에 로그인할 수 있습니다.

    ![Linux VM 만들기](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 드롭다운에서 가상 머신의 **구독**을 선택합니다.
5. 가상 머신을 만들 새 **리소스 그룹**을 선택하려면 **새로 만들기**를 선택합니다. 완료되면 **확인**을 클릭합니다.
6. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 지원되는 디스크 형식 필터를 변경합니다. 설정 블레이드에서 기본값을 그대로 유지하고 **확인**을 클릭합니다.

## <a name="create-a-user-assigned-msi"></a>사용자 할당 MSI 만들기

1. Azure Cloud Shell 세션 대신 CLI 콘솔을 사용하는 경우 먼저 Azure에 로그인합니다. 새 MSI를 만들려는 Azure 구독과 연결된 계정을 사용하세요.

    ```azurecli
    az login
    ```

2. [az identity create](/cli/azure/identity#az_identity_create)를 사용하여 사용자 할당 MSI를 만듭니다. `-g` 매개 변수는 MSI가 만들어지는 리소스 그룹을 지정하고, `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<MSI NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    응답에는 다음 예제와 같이 생성된 사용자 할당 MSI에 대한 세부 정보가 포함됩니다. 다음 단계에서 사용되므로 MSI의 `id` 값을 적어 둡니다.

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Linux VM에 사용자 할당 MSI 할당

시스템 할당 MSI와는 달리, 사용자 할당 MSI는 클라이언트가 여러 Azure 리소스에 사용할 수 있습니다. 이 자습서에서는 단일 VM에 할당하겠습니다. 여러 VM에 할당해도 됩니다.

[az vm assign-identity](/cli/azure/vm#az_vm_assign_identity)를 사용하여 Linux VM에 사용자 할당 MSI를 할당합니다. `<RESOURCE GROUP>` 및 `<VM NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. 이전 단계에서 반환된 `id` 속성을 `--identities` 매개 변수 값에 사용합니다.

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>사용자 할당 MSI에 Azure Resource Manager의 리소스 그룹 액세스 권한 부여 

MSI에서는 Azure AD 인증을 지원하는 리소스 API에 인증하기 위해 코드에 액세스 토큰을 제공합니다. 이 자습서에서 코드는 Azure Resource Manager API에 액세스합니다. 

하지만 코드가 API에 액세스할 수 있기 전에 Azure Resource Manager의 리소스에 MSI의 ID 액세스 권한을 부여해야 합니다. 이 경우에는 VM이 포함된 리소스 그룹입니다. `<SUBSCRIPTION ID>` 및 `<RESOURCE GROUP>`의 값을 환경에 적합하게 업데이트합니다. 또한 `<MSI PRINCIPALID>`를 [사용자 할당 MSI 만들기](#create-a-user-assigned-msi)의 `az identity create` 명령에서 반환한 `principalId` 속성으로 바꿉니다.

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

응답에는 다음 예제와 같이 만든 역할 할당에 대한 세부 정보가 포함됩니다.

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>VM의 ID를 사용하여 액세스 토큰을 가져온 다음 Resource Manager를 호출하는 데 사용 

자습서의 나머지 부분에서는 이전에 만든 VM에서 작업합니다.

아래의 단계를 완료하려면 SSH 클라이언트가 필요합니다. Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](https://msdn.microsoft.com/commandline/wsl/about)에서 SSH 클라이언트를 사용할 수 있습니다. SSH 클라이언트의 키 구성에 대한 도움이 필요하면 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](~/articles/virtual-machines/linux/ssh-from-windows.md) 또는 [Azure에서 Linux VM용 SSH 공개 및 개인 키 쌍을 만들고 사용하는 방법](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.

1. Azure Portal에서 **Virtual Machines**, Linux 가상 머신으로 이동한 후 **개요** 페이지 위쪽의 **연결**을 클릭합니다. VM에 연결하기 위한 문자열을 복사합니다.
2. 선택한 SSH 클라이언트를 사용하여 VM에 **연결**합니다.  
3. 터미널 창에서 CURL을 사용하여 로컬 MSI 끝점에 대한 요청을 수행해 Azure Resource Manager용 액세스 토큰을 가져옵니다.  

   다음 예제에는 액세스 토큰을 획득하는 CURL 요청이 나와 있습니다. `<CLIENT ID>`를 [사용자 할당 MSI 만들기](#create-a-user-assigned-msi)의 `az identity create` 명령에서 반환한 `clientId` 속성으로 바꿉니다. 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > `resource` 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. 리소스 관리자 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다. 
    
    응답에는 Azure Resource Manager에 액세스하는 데 필요한 액세스 토큰이 포함되어 있습니다. 
    
    응답 예제:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

4. 이제 액세스 토큰을 사용하여 Azure Resource Manager에 액세스하고, 이전에 사용자 할당 MSI 액세스 권한을 부여했던 리소스 그룹의 속성을 읽습니다. `<SUBSCRIPTION ID>` 및 `<RESOURCE GROUP>`을 앞에서 지정한 값으로 바꾸고, `<ACCESS TOKEN>`을 이전 단계에서 반환된 토큰으로 바꿔야 합니다.

    > [!NOTE]
    > URL은 대/소문자를 구분하므로 앞서 리소스 그룹의 이름을 지정할 때 사용했던 것과 정확히 동일한 대/소문자 및 `resourceGroups`의 대문자 "G"를 사용해야 합니다.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    응답은 다음 예제와 비슷하게 특정 리소스 그룹 정보를 포함합니다. 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>다음 단계

- MSI의 개요는 [관리 서비스 ID 개요](msi-overview.md)를 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.
