---
title: 관리되는 ID를 사용하여 Azure Stream Analytics 작업을 POWER BI 출력으로 인증
description: 이 문서에서는 관리되는 ID를 사용하여 Azure Stream Analytics 작업을 Power BI 출력에 인증하는 방법을 설명합니다.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 8a7dfd7c690d79d8430f7c33a25b38949dbd06c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086336"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>관리되는 ID를 사용하여 Azure Stream Analytics 작업을 Power BI에 인증

Power BI에 출력하기 위한 [관리되는 ID 인증을](../active-directory/managed-identities-azure-resources/overview.md) 통해 Stream Analytics 작업에서 Power BI 계정 내의 작업 영역에 직접 액세스할 수 있습니다. 이 기능을 사용하면 사용자가 Azure 포털을 통해 Power BI에 대화형으로 로그인할 필요가 없으므로 스트림 애널리틱스 작업의 배포를 완전히 자동화할 수 있습니다. 또한 Power BI에 쓰는 장기 실행 작업은 이제 작업을 주기적으로 다시 인증할 필요가 없으므로 더 잘 지원됩니다.

이 문서에서는 Azure 포털및 Azure 리소스 관리자 배포를 통해 스트림 분석 작업의 Power BI 출력에 대해 관리되는 ID를 사용하도록 설정하는 방법을 보여 주었습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 기능을 사용하려면 다음이 필요합니다.

- [프로 라이센스가](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro)있는 Power BI 계정.

- Power BI 계정 내에서 업그레이드된 작업 영역입니다. 자세한 내용은 [Power BI의](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) 이 기능 발표를 참조하십시오.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Azure 포털을 사용하여 스트림 분석 작업 만들기

1. 새 Stream Analytics 작업을 만들거나 Azure 포털에서 기존 작업을 엽니다. 화면 왼쪽에 있는 메뉴 모음에서 **구성**아래에 있는 **관리되는 ID를** 선택합니다. "시스템 할당된 관리 되는 ID 사용"을 선택 하 고 화면 하단에 **저장** 단추를 선택 합니다.

   ![스트림 분석 관리 ID 구성](./media/common/stream-analytics-enable-managed-identity.png)

2. 출력을 구성하기 전에 이 문서의 Power BI 작업 영역에 대한 스트림 분석 작업 액세스 권한 부여 섹션의 지침에 따라 Power BI 작업 영역에 대한 스트림 애널리틱스 [작업 액세스 권한을 부여합니다.](#give-the-stream-analytics-job-access-to-your-power-bi-workspace)

3. 스트림 분석 작업의 **출력** 섹션으로 이동하여 **+ 추가**를 선택한 다음 **Power BI를**선택합니다. 그런 다음 **권한 부여** 단추를 선택하고 Power BI 계정으로 로그인합니다.

   ![Power BI 계정으로 권한 부여](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. 권한이 부여되면 액세스 권한이 있는 모든 작업 영역으로 드롭다운 목록이 채워집니다. 이전 단계에서 권한부여한 작업 영역을 선택합니다. 그런 다음 **관리되는 ID를** "인증 모드"로 선택합니다. 마지막으로 **저장** 단추를 선택합니다.

   ![관리되는 ID로 Power BI 출력 구성](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 배포

Azure 리소스 관리자를 사용하면 스트림 분석 작업의 배포를 완전히 자동화할 수 있습니다. Azure PowerShell 또는 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)를 사용하여 리소스 관리자 템플릿을 배포할 수 있습니다. 아래 예제는 Azure CLI를 사용합니다.


1. 리소스 관리자 템플릿의 리소스 섹션에 다음 속성을 포함시켜 관리되는 ID를 사용하여 **Microsoft.StreamAnalytics/스트리밍작업** 리소스를 만들 수 있습니다.

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   이 속성은 Azure 리소스 관리자에게 스트림 분석 작업에 대한 ID를 만들고 관리하도록 지시합니다. 다음은 관리 되는 ID를 사용 하 여 스트림 분석 작업을 배포 하는 리소스 관리자 템플릿 및 관리 되는 ID를 사용 하는 Power BI 출력 싱크:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
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
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
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

    아래 Azure CLI 명령을 사용하여 위의 작업을 리소스 그룹 **ExampleGroup에** 배포합니다.

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 작업을 만든 후 Azure 리소스 관리자를 사용하여 작업의 전체 정의를 검색합니다.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    위의 명령은 다음과 같은 응답을 반환합니다.

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
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
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Power BI의 REST API를 사용하여 Power BI 작업 영역에 스트림 분석 작업을 추가하려는 경우 반환된 "principalId"를 기록합니다.

3. 작업이 만들어지면 이 문서의 [Power BI 작업 영역에 대한 검색 분석 작업 액세스 권한 부여](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) 섹션을 계속 합니다.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Power BI 작업 영역에 대한 스트림 애널리틱스 작업 액세스 권한 부여

이제 스트림 분석 작업이 만들어졌으니 Power BI 작업 영역에 대한 액세스 권한을 부여할 수 있습니다.

### <a name="use-the-power-bi-ui"></a>전원 BI UI 사용

   > [!Note]
   > UI를 사용하여 Power BI 작업 영역에 스트림 분석 작업을 추가하려면 Power BI 관리자 포털의 **개발자 설정에서** 서비스 주체 액세스를 사용하도록 설정해야 합니다. 자세한 내용은 [서비스 주체로 시작하기를](https://docs.microsoft.com/power-bi/developer/embed-service-principal#get-started-with-a-service-principal) 참조하십시오.

1. 작업 영역의 액세스 설정으로 이동합니다. 자세한 내용은 이 문서를 [참조하십시오.](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)

2. 텍스트 상자에 스트림 분석 작업의 이름을 입력하고 **참여자가** 액세스 수준으로 선택합니다.

3. 창 **추가** 및 닫기 선택합니다.

   ![Power BI 작업 영역에 스트림 분석 작업 추가](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>파워 BI 파워쉘 cmdlet 사용

1. Power BI `MicrosoftPowerBIMgmt` PowerShell cmdlet을 설치합니다.

   > [!Important]
   > cmdlet의 버전 1.0.821 이상버전을 사용하고 있는지 확인하십시오.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Power BI에 로그인합니다.

```powershell
Login-PowerBI
```

3. 작업 영역에 기여자로 스트림 분석 작업을 추가합니다.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>전원 BI REST API 사용

스트림 분석 작업은 "그룹 사용자 추가" REST API를 직접 사용하여 작업 영역에 기여자로 추가할 수도 있습니다. 이 API에 대한 전체 설명서는 여기에서 찾을 수 있습니다: [그룹 - 그룹 사용자 추가](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser).

**샘플 요청**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
요청 본문
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>제한 사항
다음은 이 기능의 제한 사항입니다.

- 클래식 Power BI 작업 영역은 지원되지 않습니다.

- Azure Active 디렉터리없이 Azure 계정입니다.

- 다중 테넌트 액세스는 지원되지 않습니다. 지정된 Stream Analytics 작업에 대해 만든 서비스 주체는 작업이 생성된 동일한 Azure Active Directory 테넌트에 있어야 하며 다른 Azure Active Directory 테넌트에 있는 리소스와 함께 사용할 수 없습니다.

- [사용자 할당된 ID는](../active-directory/managed-identities-azure-resources/overview.md) 지원되지 않습니다. 즉, 스트림 분석 작업에서 사용할 자체 서비스 주체를 입력할 수 없습니다. 서비스 주체는 Azure 스트림 분석에서 생성해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics를 사용하여 Power BI 대시보드 통합](./stream-analytics-power-bi-dashboard.md)
* [Azure Stream Analytics의 출력 이해](./stream-analytics-define-outputs.md)
