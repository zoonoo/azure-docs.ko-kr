---
title: Azure Live Video Analytics를 사용하여 사용자 지정 Azure Resource Manager 역할을 만들고 서비스 주체에 할당
description: 이 문서에서는 Azure CLI를 사용하여 사용자 지정 Azure Resource Manager 역할을 만들고 IoT Edge의 Azure Live Video Analytics에 대한 서비스 주체에 할당하는 방법에 대한 지침을 제공합니다.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 4cf819fdcdb0c046070e90dd9c2aa1a42a1d8a1b
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110373757"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal-with-live-video-analytics"></a>Live Video Analytics를 사용하여 사용자 지정 Azure Resource Manager 역할을 만들고 서비스 주체에 할당

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

IoT Edge 모듈 인스턴스의 Live Video Analytics가 제대로 작동하려면 활성 Azure Media Services 계정이 필요합니다. IoT Edge 모듈의 Live Video Analytics와 Azure Media Service 계정의 관계는 모듈 쌍 속성 집합을 통해 설정됩니다. 이러한 쌍 속성 중 하나는 모듈 인스턴스가 Media Services 계정과 통신하고 필요한 작업을 트리거할 수 있도록 하는 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)입니다. 에지 디바이스에서 오용 및/또는 실수로 인한 잠재적인 데이터 노출을 최소화하려면 이 서비스 주체에게 최소한의 권한이 있어야 합니다.

이 문서에서는 서비스 주체를 만드는 데 사용되는 Azure Cloud Shell을 사용하여 사용자 지정 Azure Resource Manager 역할을 만드는 단계를 보여 줍니다.

## <a name="prerequisites"></a>전제 조건  

이 문서의 필수 구성 요소는 다음과 같습니다.

* 소유자 구독이 있는 Azure 구독
* 앱을 만들고 역할에 서비스 주체를 할당할 수 있는 권한이 있는 Azure Active Directory

계정에 적절한 사용 권한이 있는지를 확인하는 가장 쉬운 방법은 포털을 통하는 것입니다. [필요한 사용 권한 확인](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)을 참조하세요.

## <a name="overview"></a>개요  

다음 순서로 사용자 지정 역할을 만들고 서비스 주체와 연결하는 단계를 살펴보겠습니다.

1. Media Service 계정이 없는 경우 만듭니다.
1. 서비스 주체를 생성합니다.
1. 제한된 권한으로 사용자 지정 Azure Resource Manager 역할을 만듭니다.
1. 만든 사용자 지정 역할을 사용하여 서비스 주체 권한을 "제한"합니다.
1. 간단한 테스트를 실행하여 서비스 주체를 성공적으로 제한할 수 있는지 확인합니다.
1. IoT Edge 배포 매니페스트에 사용할 매개 변수를 캡처합니다.

### <a name="create-a-media-services-account"></a>Media Services 계정 만들기  

Media Service 계정이 없는 경우 다음 단계를 사용하여 계정을 만듭니다.

1. [Cloud Shell](https://shell.azure.com/)로 이동합니다.
1. 셸 창의 왼쪽에 있는 드롭다운 목록에서 "Bash"를 환경으로 선택합니다.

    ![화면 캡처는 셸 창에서 선택된 Bash를 보여 줍니다.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. 다음 명령 템플릿을 사용하여 Azure 구독을 기본 계정으로 설정합니다.
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. [리소스 그룹](/cli/azure/group#az_group_create) 및 [스토리지 계정](/cli/azure/storage/account#az_storage_account_create)을 만듭니다.
1. 이제 Cloud Shell에서 다음 명령 템플릿을 사용하여 Azure Media Service 계정을 만듭니다.

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>서비스 주체 만들기  

이제 새 서비스 주체를 만들고 Media Service 계정에 연결합니다.

인증 매개 변수가 없으면 서비스 주체의 임의 암호를 통한 암호 기반 인증이 사용됩니다. Cloud Shell에서 다음 명령 템플릿을 사용합니다.

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

이 명령은 다음과 비슷한 응답을 생성합니다.

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. 암호 인증을 사용하는 서비스 주체의 출력에는 암호 키(이 경우 "AadSecret" 매개 변수)가 포함됩니다. 

    이 값은 검색할 수 없으므로 복사해야 합니다. 암호를 잊어버린 경우 [서비스 주체 자격 증명을 다시 설정](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials)하세요.
1. appId 및 테넌트 키는 각각 "AadClientId" 및 "AadTenantId"로 출력에 표시됩니다. 서비스 주체 인증에 사용됩니다. 해당 값은 기록하지만 언제든지 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)를 사용하여 검색할 수 있습니다.

### <a name="create-a-custom-role-definition"></a>사용자 지정 역할 정의 만들기  

사용자 지정 역할을 만들려면 다음 단계를 수행해야 합니다.

1. 로컬 시스템에 역할 정의 JSON 파일을 만들고 파일에 다음 텍스트를 저장합니다. 
    1. <yourSubscriptionId>를 Azure 구독 ID로 바꿉니다.
    1. 이 역할에 허용되는 유일한 작업은 다음과 같습니다.
        * listContainerSas – 자산 콘텐츠를 업로드하고 다운로드하기 위해 SAS(공유 액세스 서명)를 사용하여 모듈 목록 컨테이너 URL을 지원합니다.
        * 자산 쓰기 – 모듈이 자산을 만들거나 업데이트하는 데 도움이 됩니다.
        * listEdgePolicies – 에지 디바이스에 적용되는 정책을 나열합니다.  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. 만든 후에는 다음 명령 템플릿을 실행하여 구독에 새 역할 정의를 만듭니다.
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    명령이 성공적으로 실행되면 다음 출력이 표시됩니다.
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>역할 할당 만들기  

역할 할당을 추가하려면 방금 만든 사용자 지정 역할을 할당하려는 서비스 주체의 objectId가 필요합니다.

Cloud Shell에서 다음 명령을 사용하여 objectId를 가져옵니다.

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> [서비스 주체 만들기](#create-service-principal) 단계의 출력에서 `<appId>`를 검색할 수 있습니다.

위의 명령은 서비스 주체의 objectId를 출력합니다. 

```
“objectId” : “<yourObjectId>”,
```

[az role assignment create 명령](/cli/azure/role/assignment#az_role_assignment_create) 템플릿을 사용하여 사용자 지정 역할을 서비스 주체와 연결합니다.

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

매개 변수

|매개 변수|Description| 
|---|---|
|--role |사용자 지정 역할 이름 또는 ID입니다. 이 경우 "LVAEdge User"입니다.|
|--assignee-object-id|사용할 서비스 주체의 개체 ID입니다.|

결과는 다음과 같습니다.

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>역할 할당이 발생했는지 확인

이제 서비스 주체가 방금 만든 사용자 지정 역할에 연결되어 있는지 확인하려면 다음 명령을 실행합니다.

```
az role assignment list  --assignee < objectId>
```

결과는 다음과 같습니다.

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
"roleDefinitionName"을 찾아 해당 값이 "LVAEdge User"로 설정되어 있는지 확인합니다. 

설정되어 있으면 사용자 지정 사용자 역할이 애플리케이션에 사용되는 서비스 주체에 연결된 것입니다.

### <a name="test-the-service-principal-access-control"></a>서비스 주체 액세스 제어 테스트

1. 서비스 주체를 사용하여 로그인합니다. 이를 위해 [서비스 주체 만들기](#create-service-principal) 단계의 출력에서 얻을 수 있는 적절한 액세스 토큰을 부여하는 데 필요한 Azure Active Directory에 대한 다음 3가지 정보가 필요합니다.
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. 이제 아래 명령 템플릿을 사용하여 로그인을 시도합니다.
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  이제 로그인이 실패하도록 하는 리소스 그룹을 만들어 "LVAEdge 사용자" 역할을 가진 서비스 주체로 제한되는지 살펴보겠습니다. Cloud Shell에서 다음 명령을 실행합니다.

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    이 명령은 실패하고 다음과 같이 표시됩니다.
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>다음 단계  

이 문서의 다음 값을 기록합니다. 이러한 값은 IoT Edge 모듈에서 Live Video Analytics의 쌍 속성을 구성하는 데 필요합니다. [모듈 쌍 JSON 스키마](module-twin-configuration-schema.md)를 참조하세요.

| 이 문서의 변수|IoT Edge의 Live Video Analytics에 대한 쌍 속성 이름|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
