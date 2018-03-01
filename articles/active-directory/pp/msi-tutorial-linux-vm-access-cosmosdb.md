---
title: "Linux VM에서 사용자 할당 MSI(관리 서비스 ID)를 사용하여 Azure Cosmos DB에 액세스"
description: "Linux VM에서 사용자 할당 MSI(관리 서비스 ID)를 사용하여 Azure Cosmos DB에 액세스하는 프로세스를 안내하는 자습서입니다."
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
ms.date: 02/14/2018
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2c0c3597999e80af86f079385653d94ddfcab245
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-cosmos-db"></a>Linux VM에서 사용자 할당 MSI(관리 서비스 ID)를 사용하여 Azure Cosmos DB에 액세스 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

이 자습서에서는 Linux 가상 머신에서 사용자 할당 MSI(관리 서비스 ID)를 만들고 사용하여 Azure Cosmos DB에 액세스하는 방법을 보여 줍니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 사용자 할당 MSI(관리 서비스 ID) 만들기
> * Linux 가상 머신에 사용자 할당 MSI 할당
> * Azure Cosmos DB 인스턴스에 대한 MSI 액세스 권한 부여
> * 사용자 할당 MSI ID를 사용하여 액세스 토큰을 가져오고, 해당 토큰을 사용하여 Azure Cosmos DB에 액세스

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

이 자습서의 CLI 스크립트 예제는 두 가지 옵션을 통해 실행할 수 있습니다.

- Azure Portal에서 또는 각 코드 블록의 오른쪽 상단 모서리에 있는 **사용해 보세요** 단추를 통해 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)을 사용합니다.
- 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.23 이상)을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>새 리소스 그룹에 Linux 가상 머신 만들기

이 자습서에서는 새 Linux VM을 만듭니다. 기존 VM에서 MSI를 사용하도록 설정할 수도 있습니다.

1. Azure Portal의 왼쪽 위 모서리에 있는 **+ 리소스 만들기**를 클릭합니다.
2. **Compute**를 선택한 다음, **Ubuntu Server 16.04 LTS VM**을 선택합니다.
3. 가상 머신 정보를 입력합니다. **인증 유형**으로 **SSH 공용 키** 또는 **암호**를 선택합니다. 생성된 자격 증명을 사용하면 VM에 로그인할 수 있습니다.

    ![MSI Linux VM](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 드롭다운에서 가상 머신의 **구독**을 선택합니다.
5. **리소스 그룹**에서 **새로 만들기**를 선택하고 이 VM에 대한 리소스 그룹의 이름을 입력합니다. 완료되면 **확인**을 클릭합니다.
6. VM의 크기를 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 지원되는 디스크 형식 필터를 변경합니다. **설정** 블레이드에서 기본값을 그대로 유지하고 **확인**을 클릭합니다.

## <a name="create-a-user-assigned-msi"></a>사용자 할당 MSI 만들기

1. Azure Cloud Shell 세션 대신 CLI 콘솔을 사용하는 경우 먼저 Azure에 로그인합니다. 새 MSI를 만들려는 Azure 구독과 연결된 계정을 사용하세요.

    ```azurecli
    az login
    ```

2. [az identity create](/cli/azure/identity#az_identity_create)를 사용하여 사용자 할당 MSI를 만듭니다. `-g` 매개 변수는 MSI가 만들어지는 리소스 그룹을 지정하고, `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<MSI NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    응답은 다음 예제와 유사한 생성된 사용자 할당 MSI에 대한 세부 정보를 포함합니다(이후 단계에서 사용되는 MSI에 대한 `clientId` 및 `id` 값 참고).

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
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

## <a name="create-a-cosmos-db-account"></a>Cosmos DB 계정 만들기 

Cosmos DB 계정이 아직 없는 경우 지금 만듭니다. 원하는 경우 이 단계를 건너뛰고 기존 Cosmos DB 계정을 사용해도 됩니다. 

1. Azure Portal의 왼쪽 위에 있는 **+/새 서비스 만들기** 단추를 클릭합니다.
2. **데이터베이스**를 클릭한 다음, **Azure Cosmos DB**를 클릭하면 새로운 "새 계정" 패널이 표시됩니다.
3. 나중에 사용하는 Cosmos DB 계정에 대한 **ID**를 입력합니다.  
4. **API**는 "SQL"로 설정되어야 합니다. 이 자습서에서 설명하는 방식은 사용 가능한 다른 API 형식으로 사용될 수 있지만 이 자습서의 단계는 SQL API에 대한 것입니다.
5. **구독** 및 **리소스 그룹**은 이전 단계에서 VM을 만들 때 지정한 것과 일치합니다.  Cosmos DB를 사용할 수 있는 **위치**를 선택합니다.
6. **만들기**를 클릭합니다.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Cosmos DB 계정에서 컬렉션을 만듭니다.

다음으로, 이후 단계에서 쿼리할 수 있는 Cosmos DB 계정에서 데이터 컬렉션을 추가합니다.

1. 새로 만든 Cosmos DB 계정으로 이동합니다.
2. **개요** 탭에서 **+/컬렉션 추가** 단추를 클릭하면 "컬렉션 추가" 패널이 슬라이드됩니다.
3. 컬렉션에 데이터베이스 ID, 컬렉션 ID를 제공하고, 저장소 용량을 선택하고, 파티션 키를 입력하고, 처리량 값을 입력한 다음, **확인**을 클릭합니다.  이 자습서의 경우 데이터베이스 ID 및 컬렉션 ID로 "Test"를 사용하고, 고정된 저장소 용량 및 가장 낮은 처리량(400RU/s)을 선택하는 데 충분합니다.

## <a name="grant-your-user-assigned-msi-access-to-the-cosmos-db-account-access-keys"></a>Cosmos DB 계정 액세스 키에 대한 사용자 할당 MSI 액세스 부여

Cosmos DB는 Azure AD 인증을 기본적으로 지원하지 않습니다.  그러나 MSI를 사용하여 Resource Manager에서 Cosmos DB 액세스 키를 검색한 다음, 해당 키를 사용하여 Cosmos DB에 액세스할 수 있습니다.  이 단계에서 Cosmos DB 계정에 키에 대한 사용자 할당 MSI 액세스를 부여합니다.

먼저 Azure CLI를 사용하여 Azure Resource Manager의 Cosmos DB 계정에 대한 MSI ID 액세스를 부여합니다. `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` 및 `<COSMOS DB ACCOUNT NAME>`의 값을 환경에 대해 업데이트합니다. `<MSI PRINCIPALID>`를 [사용자 할당 MSI 만들기](#create-a-user-assigned-msi)의 `az identity create` 명령에서 반환한 `principalId` 속성으로 바꿉니다.  Cosmos DB는 액세스 키를 사용할 때 두 가지 수준의 세분성: 계정에 대한 읽기/쓰기 액세스 및 계정에 대한 읽기 전용 액세스를 지원합니다.  계정에 대한 읽기/쓰기 키를 가져오려는 경우 `DocumentDB Account Contributor` 역할을 할당하거나 계정에 대한 읽기 전용 키를 가져오려는 경우 `Cosmos DB Account Reader Role` 역할을 할당합니다.

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

응답에는 생성된 역할 할당에 대한 세부 정보가 포함됩니다.

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msi-and-use-it-to-call-azure-resource-manager"></a>사용자 할당 MSI를 사용하여 액세스 토큰을 가져온 다음, Azure Resource Manager를 호출하는 데 사용

자습서의 나머지 부분에서는 이전에 만든 VM에서 작업합니다.

아래의 단계를 완료하려면 SSH 클라이언트가 필요합니다. Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](https://msdn.microsoft.com/commandline/wsl/install_guide)에서 SSH 클라이언트를 사용할 수 있습니다. SSH 클라이언트의 키 구성에 대한 도움이 필요하면 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](../../virtual-machines/linux/ssh-from-windows.md) 또는 [Azure에서 Linux VM용 SSH 공개 및 개인 키 쌍을 만들고 사용하는 방법](../../virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.

1. Azure Portal에서 **Virtual Machines**, Linux 가상 머신으로 이동한 후 **개요** 페이지 위쪽의 **연결**을 클릭합니다. VM에 연결하기 위한 문자열을 복사합니다. 
2. SSH 클라이언트를 사용하여 VM에 연결합니다.  
3. 그리고 나면 **Linux VM**을 만들 때 추가했던 **암호**를 입력하라는 메시지가 표시됩니다. 이제 정상적으로 로그인되어야 합니다.  
4. CURL을 사용하여 Azure Resource Manager에 대한 액세스 토큰을 가져옵니다.  

    액세스 토큰에 대한 CURL 요청 및 응답은 다음과 같습니다.  <CLIENT ID>를 사용자 할당 MSI의 clientId 값으로 바꿉니다.
    
    ```bash
    curl 'http://localhost:50342/oauth2/token?resource=https://management.azure.com/&client_id=<CLIENT ID>' -H "Metadata:true"
    ```
    
    > [!NOTE]
    > 이전 요청에서 "resource" 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. Azure Resource Manager 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다.
    > 다음 응답에서는 간단히 나타내기 위해 access_token 요소를 축약했습니다.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Cosmos DB 호출을 위해 Azure Resource Manager에서 액세스 키 가져오기  

이제 CURL을 사용하여 이전 섹션에서 검색한 액세스 토큰을 사용하여 Resource Manager를 호출하여 Cosmos DB 계정 액세스 키를 검색합니다. 액세스 키가 있으면 Cosmos DB를 쿼리할 수 있습니다. `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` 및 `<COSMOS DB ACCOUNT NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<ACCESS TOKEN>` 값을 앞서 검색한 액세스 토큰으로 바꿉니다.  읽기/쓰기 키를 검색하려는 경우 키 작업 유형 `listKeys`를 사용합니다.  읽기 전용 키를 검색하려는 경우 키 작업 유형 `readonlykeys`를 사용합니다.

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> 이전 URL의 텍스트는 대/소문자를 구분하므로 리소스 그룹을 적절하게 반영하는 대/소문자를 사용하는지 확인하세요. 또한 이 요청은 GET 요청이 아닌 POST 요청임을 기억해야 하며, -d(NULL일 수 있음)를 사용하여 길이 제한을 캡처하는 값을 전달해야 합니다.  

CURL 응답에서는 키 목록을 제공합니다.  예를 들어 읽기 전용 키를 가져오는 경우:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

이제 Cosmos DB 계정에 대한 액세스 키가 있으므로 Cosmos DB SDK에 전달하고 계정에 액세스하는 호출을 만들 수 있습니다.  빠른 예제의 경우 Azure CLI에 액세스 키를 전달할 수 있습니다.  Azure Portal에서 Cosmos DB 계정 블레이드의 **개요** 탭에서 <COSMOS DB CONNECTION URL>을 가져올 수 있습니다.  <ACCESS KEY>를 위에서 얻은 값으로 바꿉니다.

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

이 CLI 명령은 컬렉션에 대한 세부 정보를 반환합니다.

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>다음 단계

- MSI의 개요는 [Azure 리소스용 MSI(관리 서비스 ID)](msi-overview.md)를 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.