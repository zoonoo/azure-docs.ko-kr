---
title: REST API를 사용 하 고 Azure 파일 공유 백업
description: REST API를 사용하여 복구 서비스 볼트에서 Azure 파일 공유를 백업하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 2cf385830ec1be17cb62432e6ef9cba7d82a9db1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248100"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>나머지 API를 통해 Azure 백업을 사용하는 백업 Azure 파일 공유

이 문서에서는 REST API를 통해 Azure 백업을 사용하여 Azure 파일 공유를 백업하는 방법을 설명합니다.

이 문서에서는 파일 공유에 대한 백업을 구성하기 위한 복구 서비스 자격 증명 모음 및 정책을 이미 만들었다고 가정합니다. 그렇지 않은 경우 새 볼트 및 정책을 만들기 위한 [정책](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy) REST API 자습서를 [만들고 자격 증명 모음 만들기를](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatevault) 참조하세요.

이 문서에서는 다음 리소스를 사용합니다.

- **복구 서비스 볼트**: *azurefilesvault*

- **정책:** *일정1*

- **리소스 그룹**: *azurefiles*

- **스토리지 계정**: *testvault2*

- **파일 공유**: *테스트 쉐어*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>REST API를 사용하여 보호되지 않은 Azure 파일 공유에 대한 백업 구성

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>보호되지 않은 Azure 파일 공유로 저장소 계정 검색

볼트는 복구 서비스 자격 증명 모음에 백업할 수 있는 파일 공유를 사용하여 구독의 모든 Azure 저장소 계정을 검색해야 합니다. [새로 고침 작업](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh)을 사용하여 이 자격 증명 모음을 트리거합니다. 볼트가 현재 구독에서 보호되지 않은 모든 Azure File 공유의 최신 목록을 얻고 이를 '캐시'하도록 하는 비동기 *POST* 작업입니다. 파일 공유가 '캐시'되면 복구 서비스는 파일 공유에 액세스하여 보호할 수 있습니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

POST URI에는 `{subscriptionId}` `{vaultName}`. `{vaultresourceGroupName}`및 `{fabricName}` 매개 변수가 있습니다. 이 예제에서 다른 매개 변수의 값은 다음과 같습니다.

- `{fabricName}`*는 Azure입니다.*

- `{vaultName}`*은 azurefiles볼트입니다.*

- `{vaultresourceGroupName}`*은 azurefiles입니다.*

- $filter=백업관리유형 eq 'AzureStorage'

필요한 모든 매개 변수는 URI에 제공되므로 별도의 요청 본문에 대한 필요가 없습니다.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses"></a>응답

'새로 고침' 작업은 [비동기 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

다른 작업이 생성될 때 202(허용됨)와 해당 작업이 완료되면 200(확인)의 두 개의 응답을 반환합니다.

##### <a name="example-responses"></a>예제 응답

*POST* 요청을 제출하면 202(수락됨) 응답이 반환됩니다.

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

간단한 *GET* 명령으로 “위치” 헤더를 사용하여 결과 작업 추적

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

모든 Azure Storage 계정이 검색되면 GET 명령은 200(콘텐츠 없음) 응답을 반환합니다. 이제 자격 증명 모음은 구독 내에서 백업할 수 있는 파일 공유가 있는 모든 저장소 계정을 검색할 수 있습니다.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-that-can-be-protected-with-recovery-services-vault"></a>복구 서비스 자격 증명 모음으로 보호할 수 있는 저장소 계정 목록 가져옵니다.

"캐싱"이 수행되어 있는지 확인하려면 구독 아래에 보호 가능한 모든 저장소 계정을 나열합니다. 그런 다음 응답에서 원하는 저장소 계정을 찾습니다. 이 작업은 [GET 보호 컨테이너](https://docs.microsoft.com/rest/api/backup/protectablecontainers/list) 작업을 사용하여 수행됩니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

*GET* URI에는 필요한 모든 매개 변수가 있습니다. 추가 요청 본문이 필요없습니다.

응답 본문의 예:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

친숙한 이름으로 응답 본문에 *testvault2* 저장소 계정을 찾을 수 있으므로 위에서 수행한 새로 고침 작업이 성공했습니다. 복구 서비스 자격 증명 모음은 이제 동일한 구독에서 보호되지 않은 파일이 공유된 저장소 계정을 성공적으로 검색할 수 있습니다.

### <a name="register-storage-account-with-recovery-services-vault"></a>복구 서비스 자격 증명 모음으로 저장소 계정 등록

이 단계는 저장소 계정을 이전에 저장소에 등록하지 않은 경우에만 필요합니다. [보호컨테이너 레지스터 작업을](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register)통해 볼트를 등록할 수 있습니다.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

URI의 변수를 다음과 같이 설정합니다.

- {리소스 그룹 이름} - *azurefiles*
- {패브릭 이름} - *Azure*
- {볼트이름} - *azurefilesvault*
- {컨테이너Name} - GET ProtectableContainers 작업의 응답 본문에 있는 이름 특성입니다.
   이 예제에서는 저장소 *컨테이너입니다. 스토리지; AzureFiles;테스트 볼트2*

>[!NOTE]
> 항상 응답의 이름 특성을 가지고 이 요청에 채웁니다. 하드 코딩하거나 컨테이너 이름 형식을 만들지 마십시오. 만들거나 하드 코딩하는 경우 나중에 컨테이너 이름 형식이 변경되면 API 호출이 실패합니다.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

만들기 요청 본문은 다음과 같습니다.

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

요청 본문 및 기타 세부 정보의 전체 정의 목록은 [보호 컨테이너 등록을](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register#azurestoragecontainer)참조하십시오.

이 작업은 비동기 작업이며 작업이 수락될 때 "202 Accepted"와 작업이 완료되면 "200 확인"이라는 두 개의 응답을 반환합니다.  작업 상태를 추적하려면 위치 헤더를 사용하여 작업의 최신 상태를 가져옵니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

작업이 완료되면 응답 본문의 예:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

응답 본문의 등록 상태 매개 변수 값에서 *등록이* 성공했는지 확인할 수 있습니다. 이 경우 *testvault2에*등록된 상태를 표시하므로 등록 작업이 성공했습니다.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>저장소 계정으로 보호되지 않은 모든 파일 공유 문의

[보호 컨테이너-문의](https://docs.microsoft.com/rest/api/backup/protectioncontainers/inquire) 작업을 사용하여 저장소 계정에서 보호 가능한 항목에 대해 문의할 수 있습니다. 비동기 작업이며 위치 헤더를 사용하여 결과를 추적해야 합니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

위의 URI에 대한 변수를 다음과 같이 설정합니다.

- {볼트이름} - *azurefilesvault*
- {패브릭 이름} - *Azure*
- {컨테이너Name}- GET ProtectableContainers 작업의 응답 본문에 있는 이름 특성을 참조합니다. 이 예제에서는 저장소 *컨테이너입니다. 스토리지; AzureFiles;테스트 볼트2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

요청이 성공하면 상태 코드 "확인"을 반환합니다.

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>백업할 파일 공유 선택

구독 아래에 모든 보호 가능한 항목을 나열하고 [GET backupprotectableItems](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) 작업을 사용하여 백업할 원하는 파일 공유를 찾을 수 있습니다.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

다음과 같이 URI를 생성합니다.

- {볼트이름} - *azurefilesvault*
- {$filter} - *백업관리유형 eq 'AzureStorage'*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

샘플 응답:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

응답에는 보호되지 않은 모든 파일 공유 목록이 포함되며 Azure Recovery Service에서 백업을 구성하는 데 필요한 모든 정보가 포함되어 있습니다.

### <a name="enable-backup-for-the-file-share"></a>파일 공유에 대한 백업 사용

관련 파일 공유가 친숙한 이름으로 "식별"된 후 보호할 정책을 선택합니다. 볼트의 기존 정책에 대해 자세히 알아보려면 [정책 API 목록을](https://docs.microsoft.com/rest/api/backup/backuppolicies/list)참조하십시오. 그런 다음, 정책 이름을 참조하여 [관련 정책](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get)을 선택합니다. 정책을 만들려면 [정책 자습서 만들기](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy)를 참조하세요.

보호 활성화는 "보호된 항목"을 만드는 비동기 *PUT* 작업입니다.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

GET backupprotectableitems 작업의 응답 본문에 ID 특성을 사용 하 여 컨테이너 이름 및 **보호 된 itemname** 변수를 설정 합니다. **containername**

이 예제에서 보호하려는 파일 공유의 ID는 다음과 같은 것입니다.

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {컨테이너 이름} - *저장소 컨테이너;저장소;azurefiles;testvault2*
- {보호된항목이름} - *azurefileshare;테스트 쉐어*

또는 보호 컨테이너의 **이름** 특성 및 보호 가능한 항목 응답을 참조할 수 있습니다.

>[!NOTE]
>항상 응답의 이름 특성을 가지고 이 요청에 채웁니다. 하드 코딩하거나 컨테이너 이름 형식 또는 보호된 항목 이름 형식을 만들지 마십시오. 만들거나 하드 코딩하는 경우 나중에 컨테이너 이름 형식 또는 보호된 항목 이름 형식이 변경되면 API 호출이 실패합니다.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

요청 본문 만들기:

다음 요청 본문은 보호된 항목을 만드는 데 필요한 속성을 정의합니다.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**소스ResourceId는** GET 백업보호 항목의 응답으로 **부모 컨테이너패브릭 ID입니다.**

샘플 응답

보호된 항목을 만드는 것은 추적해야 하는 다른 작업을 만드는 비동기 작업입니다. 다른 작업이 생성될 때 202(허용됨)와 해당 작업이 완료되면 200(확인)의 두 개의 응답을 반환합니다.

보호된 항목 만들기 또는 업데이트에 대한 *PUT* 요청을 제출하면 초기 응답은 위치 헤더가 있는 202(수락됨)입니다.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

그런 다음 *GET* 명령을 사용하여 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

작업이 완료되면 응답 본문에서 보호된 항목 콘텐츠를 사용하여 200(정상)을 반환합니다.

샘플 응답 본문:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

이렇게 하면 파일 공유에 대한 보호가 활성화되고 첫 번째 백업이 정책 일정에 따라 트리거됩니다.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>파일 공유를 위한 온디맨드 백업 트리거

백업을 위해 Azure 파일 공유가 구성되면 정책 일정에 따라 백업이 실행됩니다. 첫 번째 예약 백업을 대기하거나 언제든 주문형 백업을 트리거할 수 있습니다.

주문형 백업의 트리거는 POST 작업입니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{컨테이너Name} 및 {protectedItemName}은 백업을 활성화하는 동안 위에서 생성됩니다. 예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>요청 본문 만들기

주문형 백업을 트리거하려면 요청 본문의 구성 요소는 다음과 같습니다.

| 이름       | Type                       | Description                       |
| ---------- | -------------------------- | --------------------------------- |
| 속성 | Azurefile공유백업레크 | 백업요청리소스 속성 |

요청 본문 및 기타 세부 정보에 대한 전체 정의 목록은 [보호된 항목 REST API 문서의 백업 트리거](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body)를 참조하세요.

요청 본문 예제

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses"></a>응답

주문형 백업의 트리거는 [비동기 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

다른 작업이 생성될 때 202(허용됨)와 해당 작업이 완료되면 200(확인)의 두 개의 응답을 반환합니다.

### <a name="example-responses"></a>예제 응답

주문형 백업에 대한 *POST* 요청을 제출하면 초기 응답은 위치 헤더 또는 Azure-async-header를 사용한 202(수락됨)입니다.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

그런 다음 *GET* 명령을 사용하여 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

작업이 완료되면 응답 본문에서 결과 백업 작업의 ID를 사용하여 200(정상)을 반환합니다.

#### <a name="sample-response-body"></a>샘플 응답 본문

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

백업 작업은 장기 실행 작업이므로 [REST API를 사용한 모니터링 작업 문서](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job)에 설명된 대로 추적해야 합니다.

## <a name="next-steps"></a>다음 단계

- [나머지 API를 사용하여 Azure 파일 공유를 복원하는](restore-azure-file-share-rest-api.md)방법에 대해 알아봅니다.
