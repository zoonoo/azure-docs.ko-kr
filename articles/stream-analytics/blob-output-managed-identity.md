---
title: 관리되는 ID Azure 스트림 분석을 통해 Blob 출력 인증
description: 이 문서에서는 관리되는 ID를 사용하여 Azure Stream Analytics 작업을 Azure Blob 저장소 출력에 인증하는 방법을 설명합니다.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 13f48a9e0bc3ed8f8c4d5f1b7da4b6c03f54cdf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129979"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>관리되는 ID를 사용하여 Azure 스트림 분석 작업을 Azure Blob 저장소 출력으로 인증

Azure Blob 저장소에 대한 출력에 대한 [관리되는 ID 인증을](../active-directory/managed-identities-azure-resources/overview.md) 통해 Stream Analytics 작업은 연결 문자열을 사용하는 대신 저장소 계정에 직접 액세스할 수 있습니다. 이 기능을 사용하면 보안이 향상될 뿐만 아니라 Azure 내의 VNET(가상 네트워크)의 저장소 계정에 데이터를 쓸 수도 있습니다.

이 문서에서는 Azure 포털 및 Azure 리소스 관리자 배포를 통해 스트림 분석 작업의 Blob 출력에 대해 관리되는 ID를 사용하도록 설정하는 방법을 보여 주었습니다.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Azure 포털을 사용하여 스트림 분석 작업 만들기

1. 새 Stream Analytics 작업을 만들거나 Azure 포털에서 기존 작업을 엽니다. 화면 왼쪽에 있는 메뉴 모음에서 **구성**아래에 있는 **관리되는 ID를** 선택합니다. "시스템 할당된 관리 되는 ID 사용"을 선택 하 고 화면 하단에 **저장** 단추를 클릭 합니다.

   ![스트림 분석 관리 ID 구성](./media/common/stream-analytics-enable-managed-identity.png)

2. Azure Blob 저장소 출력 싱크의 출력 속성 창에서 인증 모드 드롭다운을 선택하고 **관리되는 ID를 선택합니다.** 다른 출력 속성에 대한 자세한 내용은 [Azure Stream Analytics의 출력 이해하기를](./stream-analytics-define-outputs.md)참조하십시오. 작업을 마쳤으면 **저장**을 클릭합니다.

   ![Azure Blob 저장소 출력 구성](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. 작업이 만들어지면 이 문서의 저장소 계정 계정에 대한 검색 분석 작업 액세스 권한 부여 섹션을 [참조하세요.](#give-the-stream-analytics-job-access-to-your-storage-account)

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 배포

Azure 리소스 관리자를 사용하면 스트림 분석 작업의 배포를 완전히 자동화할 수 있습니다. Azure PowerShell 또는 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)를 사용하여 리소스 관리자 템플릿을 배포할 수 있습니다. 아래 예제는 Azure CLI를 사용합니다.


1. 리소스 관리자 템플릿의 리소스 섹션에 다음 속성을 포함시켜 관리되는 ID를 사용하여 **Microsoft.StreamAnalytics/스트리밍작업** 리소스를 만들 수 있습니다.

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   이 속성은 Azure 리소스 관리자에게 스트림 분석 작업에 대한 ID를 만들고 관리하도록 지시합니다. 다음은 관리되는 ID를 사용하도록 설정한 스트림 분석 작업을 배포하는 리소스 관리자 템플릿과 관리되는 ID를 사용하는 Blob 출력 싱크입니다.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    위의 작업은 아래 Azure CLI 명령을 사용하여 리소스 그룹 **ExampleGroup에** 배포할 수 있습니다.

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 작업을 만든 후 Azure 리소스 관리자를 사용하여 작업의 전체 정의를 검색할 수 있습니다.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    위의 명령은 다음과 같은 응답을 반환합니다.

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Azure Active Directory 내에서 작업의 관리되는 ID를 식별하고 다음 단계에서 저장소 계정에 대한 Stream Analytics 작업 액세스 권한을 부여하는 데 사용되는 작업의 정의에서 **principalId를** 기록합니다.

3. 작업이 만들어지면 이 문서의 저장소 계정 계정에 대한 검색 분석 작업 액세스 권한 부여 섹션을 [참조하세요.](#give-the-stream-analytics-job-access-to-your-storage-account)


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>스토리지 계정에 대한 스트림 애널리틱스 작업 액세스 권한 부여

스트림 애널리틱스 작업을 제공하기 위해 선택할 수 있는 액세스 수준은 두 가지가 있습니다.

1. **컨테이너 수준 액세스:** 이 옵션은 특정 기존 컨테이너에 대한 작업 액세스를 제공합니다.
2. **계정 수준 액세스:** 이 옵션을 사용하면 새 컨테이너를 만드는 기능을 포함하여 저장소 계정에 대한 일반적인 액세스 권한을 작업에 부여합니다.

대신 컨테이너를 만드는 작업이 필요하지 않으면 이 옵션을 선택하면 작업에 필요한 최소 액세스 수준이 부여되므로 **컨테이너 수준 액세스를** 선택해야 합니다. Azure 포털 및 명령줄에 대해 두 옵션 모두 아래에 설명되어 있습니다.

### <a name="grant-access-via-the-azure-portal"></a>Azure 포털을 통해 액세스 권한 부여

#### <a name="container-level-access"></a>컨테이너 수준 액세스

1. 저장소 계정 내의 컨테이너 구성 창으로 이동합니다.

2. 왼쪽에서 **IAM(액세스 제어)을** 선택합니다.

3. "역할 할당 추가" 섹션에서 **추가**를 클릭합니다.

4. 역할 할당 창에서 다음을 수행합니다.

    1. "저장소 Blob 데이터 참여자"로 **역할** 설정
    2. **드롭다운에 대한 할당 액세스가** "Azure AD 사용자, 그룹 또는 서비스 주체"로 설정되어 있는지 확인합니다.
    3. 검색 필드에 검색 분석 작업 이름을 입력합니다.
    4. 스트림 분석 작업을 선택하고 **저장을**클릭합니다.

   ![컨테이너 액세스 권한 부여](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>계정 수준 액세스

1. 스토리지 계정으로 이동합니다.

2. 왼쪽에서 **IAM(액세스 제어)을** 선택합니다.

3. "역할 할당 추가" 섹션에서 **추가**를 클릭합니다.

4. 역할 할당 창에서 다음을 수행합니다.

    1. "저장소 Blob 데이터 참여자"로 **역할** 설정
    2. **드롭다운에 대한 할당 액세스가** "Azure AD 사용자, 그룹 또는 서비스 주체"로 설정되어 있는지 확인합니다.
    3. 검색 필드에 검색 분석 작업 이름을 입력합니다.
    4. 스트림 분석 작업을 선택하고 **저장을**클릭합니다.

   ![계정 액세스 권한 부여](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>명령줄을 통해 액세스 권한 부여

#### <a name="container-level-access"></a>컨테이너 수준 액세스

특정 컨테이너에 대한 액세스 권한을 부여하려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>계정 수준 액세스

전체 계정에 대한 액세스 권한을 부여하려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>VNET 액세스 사용

저장소 계정의 방화벽 및 **가상 네트워크를**구성할 때 다른 신뢰할 수 있는 Microsoft 서비스의 네트워크 트래픽을 선택적으로 허용할 수 있습니다. Stream Analytics가 관리되는 ID를 사용하여 인증하면 요청이 신뢰할 수 있는 서비스에서 시작된다는 증거를 제공합니다. 다음은 이 VNET 액세스 예외를 활성화하는 지침입니다.

1.  저장소 계정의 구성 창 내에서 "방화벽 및 가상 네트워크" 창으로 이동합니다.
2.  "신뢰할 수 있는 Microsoft 서비스가 이 저장소 계정에 액세스할 수 있도록 허용" 옵션이 활성화되어 있는지 확인합니다.
3.  사용하도록 설정한 경우 **저장을**클릭합니다.

   ![VNET 액세스 사용](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>제한 사항
다음은 이 기능의 현재 제한 사항입니다.

1. 클래식 Azure 저장소 계정입니다.

2. Azure Active 디렉터리없이 Azure 계정입니다.

3. 다중 테넌트 액세스는 지원되지 않습니다. 지정된 Stream Analytics 작업에 대해 만든 서비스 주체는 작업이 생성된 동일한 Azure Active Directory 테넌트에 있어야 하며 다른 Azure Active Directory 테넌트에 있는 리소스와 함께 사용할 수 없습니다.

4. [사용자 할당된 ID는](../active-directory/managed-identities-azure-resources/overview.md) 지원되지 않습니다. 즉, 사용자는 스트림 분석 작업에서 사용할 자신의 서비스 주체를 입력할 수 없습니다. 서비스 주체는 Azure 스트림 분석에서 생성해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics의 출력 이해](./stream-analytics-define-outputs.md)
* [Azure Stream Analytics 사용자 지정 Blob 출력 분할](./stream-analytics-custom-path-patterns-blob-storage-output.md)
