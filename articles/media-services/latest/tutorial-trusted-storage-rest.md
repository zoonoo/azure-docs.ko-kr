---
title: Azure Media Services 신뢰할 수 있는 스토리지
description: 이 자습서에서는 Azure Media Services에 대해 신뢰할 수 있는 스토리지를 사용하도록 설정하고, 신뢰할 수 있는 스토리지용 관리 ID를 사용하고, 방화벽이나 VPN을 사용할 때 Azure 서비스에 스토리지 계정에 대한 액세스 권한을 부여하는 방법을 알아봅니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: ee57a743d28dcb80eeff5e2060ec9fff436e888c
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963311"
---
# <a name="tutorial-media-services-trusted-storage"></a>자습서: Media Services 신뢰할 수 있는 스토리지

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> - Azure Media Services에 대해 신뢰할 수 있는 스토리지를 사용하도록 설정하는 방법
> - 신뢰할 수 있는 스토리지에 관리 ID를 사용하는 방법
> - 방화벽 또는 VPN과 같은 네트워크 액세스 제어를 사용할 때 Azure 서비스에 스토리지 계정에 대한 액세스 권한을 부여하는 방법

2020-05-01 API를 사용하면 관리 ID를 Media Services 계정과 연결하여 신뢰할 수 있는 스토리지를 사용하도록 설정할 수 있습니다.

>[!NOTE]
>신뢰할 수 있는 스토리지는 API에서만 사용할 수 있으며 현재 Azure Portal에서는 사용할 수 없습니다.

Media Services는 시스템 인증을 사용하여 스토리지 계정에 자동으로 액세스할 수 있습니다. Media Services는 Media Services 계정과 스토리지 계정이 동일한 구독에 있는지 확인합니다. 또한 연결을 추가하는 사용자가 Azure Resource Manager RBAC를 사용하여 스토리지 계정에 액세스했는지 확인합니다.

그러나 네트워크 액세스 제어를 사용하여 스토리지 계정을 보호하고 신뢰할 수 있는 스토리지를 사용하도록 설정하려면 [관리 ID](concept-managed-identities.md) 인증이 필요합니다. 이를 통해 Media Services는 신뢰할 수 있는 스토리지 액세스를 통해 방화벽 또는 VNet 제한으로 구성된 스토리지 계정에 액세스할 수 있습니다.

## <a name="overview"></a>개요

> [!IMPORTANT]
> Media Services에 대한 모든 요청에 2020-05-01 API를 사용합니다.

다음은 Media Services에 대해 신뢰할 수 있는 스토리지를 생성하기 위한 일반적인 단계입니다.

1. 리소스 그룹을 만듭니다.
1. 스토리지 계정을 만듭니다.
1. 스토리지 계정이 준비될 때까지 폴링합니다. 스토리지 계정이 준비되면 요청에서 서비스 주체 ID를 반환합니다.
1. *Storage Blob 데이터 기여자* 역할의 ID를 찾습니다.
1. 권한 부여 공급자를 호출하고 역할 할당을 추가합니다.
1. 관리 ID를 사용하여 스토리지 계정에 인증하도록 미디어 서비스 계정을 업데이트합니다.
1. 리소스를 더 이상 사용하지 않고 요금이 청구되지 않도록 하려면 리소스를 삭제합니다.

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 Azure 구독이 필요합니다.  Azure 구독이 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free/)을 만듭니다.

### <a name="get-your-tenant-id-and-subscription-id"></a>테넌트 ID 및 구독 ID 가져오기

테넌트 ID 및 구독 ID를 가져오는 방법을 모르는 경우 [테넌트 ID 찾는 방법](how-to-set-azure-tenant.md) 및 [테넌트 ID 찾기](how-to-set-azure-tenant.md)를 참조하세요.

### <a name="create-a-service-principal-and-secret"></a>서비스 주체 및 비밀 만들기

서비스 주체 및 비밀을 만드는 방법을 모르는 경우 [Media Services API에 액세스하기 위한 자격 증명 가져오기](access-api-howto.md)를 참조하세요.

## <a name="use-a-rest-client"></a>REST 클라이언트 사용

이 스크립트는 Visual Studio 코드 확장에서 사용할 수 있는 것과 같은 REST 클라이언트와 함께 사용하기 위한 것입니다.  개발 환경에 맞게 조정합니다.

## <a name="set-initial-variables"></a>초기 변수 설정

이 스크립트 부분은 REST 클라이언트에서 사용됩니다. 개발 환경에서 변수를 다르게 사용할 수 있습니다.

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Azure Resource Manager에 대한 토큰 가져오기

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Graph API에 대한 토큰 가져오기

이 스크립트 부분은 REST 클라이언트에서 사용됩니다. 개발 환경에서 변수를 다르게 사용할 수 있습니다.

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>서비스 주체 세부 정보 가져오기

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>서비스 주체 ID 저장

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>스토리지 계정 만들기

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>스토리지 계정 상태 가져오기

스토리지 계정이 준비되는 데 시간이 좀 걸리므로 이 요청은 상태를 폴링합니다.  스토리지 계정이 준비될 때까지 이 요청을 반복합니다.

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>스토리지 계정 세부 정보 가져오기

스토리지 계정이 준비되면 스토리지 계정의 속성을 가져옵니다.

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>ARM에 대한 토큰 가져오기

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>시스템 할당 관리 ID를 사용하여 Media Services 계정 만들기

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>스토리지 Storage Blob 데이터 역할 정의 가져오기

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>스토리지 역할 할당 설정

역할 할당은 Media Services 계정에 대한 서비스 주체에게 *Storage Blob 데이터 기여자* 스토리지 역할 있음을 의미합니다.  이 작업은 시간이 걸릴 수 있으며 기다려야 합니다. 그렇지 않으면 Media Services 계정이 올바르게 설정되지 않습니다.

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>스토리지 계정에 관리 ID 바이패스 액세스 권한 부여

이 작업은 시스템 관리 ID에서 관리 ID로의 액세스를 변경합니다. 이러한 방식으로 Azure 서비스가 IP 액세스 규칙(ACL)에 관계없이 스토리지 계정에 액세스할 수 있으므로 스토리지 계정은 방화벽을 통해 스토리지 계정에 액세스할 수 있습니다.

다시 말하지만 스토리지 계정에 역할이 할당될 때까지 기다리세요. 그렇지 않으면 Media Services 계정이 잘못 설정됩니다.

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>관리 ID를 사용하도록 Media Services 계정 업데이트

스토리지 역할 할당이 전파되는 데 몇 분 정도 걸릴 수 있으므로 이 요청을 몇 번 재시도해야 할 수 있습니다.

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>액세스 테스트

스토리지 계정에서 자산을 만들어 액세스를 테스트합니다.

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>리소스 삭제

만든 리소스를 더 이상 유지하지 않고 요금이 계속 청구되지 않도록 하려면 해당 리소스를 삭제합니다.

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>다음 단계

[자산을 만드는 방법](asset-create-asset-how-to.md)
