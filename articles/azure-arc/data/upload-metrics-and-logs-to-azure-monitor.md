---
title: Azure Monitor에 사용 현황 데이터, 메트릭 및 로그 업로드
description: 리소스 인벤토리, 사용 현황 데이터, 메트릭 및 로그를 Azure Monitor에 업로드 합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3e3b804e2a3c43eb9579d1c6a1195511df528de2
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630001"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Azure Monitor에 사용 현황 데이터, 메트릭 및 로그 업로드

정기적으로 청구 목적, 모니터링 메트릭 및 로그에 대 한 사용 정보를 내보낸 후 Azure에 업로드할 수 있습니다.  이러한 세 가지 유형의 데이터를 내보내기와 업로드 하면 Azure에서 데이터 컨트롤러, SQL 관리 되는 인스턴스 및 PostgreSQL Hyperscale 서버 그룹 리소스도 생성 및 업데이트 됩니다.

> [!NOTE] 
미리 보기 기간 중에는 Azure Arc 사용 데이터 서비스를 사용 하는 비용이 없습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure CLI (az) 및 Azure Data CLI (azdata)가 설치 되어 있어야 합니다.  [도구를 설치](./install-client-tools.md)합니다.

Azure에 데이터를 업로드 하기 전에 Azure 구독에 Microsoft AzureData 리소스 공급자가 등록 되어 있는지 확인 해야 합니다.

다음 명령을 실행 하 여이를 확인할 수 있습니다.

```console
az provider show -n Microsoft.AzureData -o table
```

리소스 공급자가 현재 구독에 등록 되어 있지 않은 경우 다음 명령을 실행 하 여 등록할 수 있습니다.  이 명령을 완료 하려면 1 ~ 2 분이 소요 됩니다.

```console
az provider register -n Microsoft.AzureData --wait
```

## <a name="upload-usage-data"></a>사용 현황 데이터 업로드

인벤토리 및 리소스 사용량과 같은 사용 정보는 다음과 같은 두 단계를 통해 Azure에 업로드할 수 있습니다.

1. 다음과 같이 명령을 사용 하 여 사용 현황 데이터를 내보냅니다 ```azdata export``` .

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   이 명령은 `usage.json` 데이터 컨트롤러에 생성 된 모든 Azure Arc 사용 가능한 데이터 리소스 (예: SQL 관리 되는 인스턴스 및 PostgreSQL Hyperscale 인스턴스 등)를 사용 하 여 파일을 만듭니다.

2. 명령을 사용 하 여 사용 현황 데이터 업로드 ```azdata upload```

   > [!NOTE]
   > 업로드를 실행 하기 전에 Azure Arc 데이터 컨트롤러를 만든 후 최소 24 시간 동안 기다려 주세요.

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>메트릭 및 로그 업로드

Azure Arc data services를 사용 하면 필요에 따라 메트릭 및 로그를 Azure Monitor에 업로드 하 여 메트릭, 로그, 경고 발생, 알림 전송 또는 자동화 된 작업 트리거를 집계 하 고 분석할 수 있습니다. 

데이터를 Azure Monitor으로 전송 하면 모니터링 및 로그 데이터를 오프 사이트에 저장 하 고 대규모 분석을 위해 데이터의 장기 저장소를 사용할 수 있도록 대규모로 확장할 수 있습니다.

Azure Arc data services가 있는 여러 사이트가 있는 경우 Azure Monitor를 중앙 위치로 사용 하 여 사이트 전체에서 모든 로그와 메트릭을 수집할 수 있습니다.

### <a name="before-you-begin"></a>시작하기 전에

로그 및 메트릭 업로드 시나리오를 사용 하도록 설정 하는 데 필요한 몇 가지 일회성 설정 단계가 있습니다.

1. 클라이언트 액세스 암호 만들기를 포함 하 여 서비스 사용자/Azure Active Directory 응용 프로그램을 만들고 데이터베이스 인스턴스 리소스가 있는 구독의 ' 모니터링 메트릭 게시자 ' 역할에 서비스 주체를 할당 합니다.
2. Log analytics 작업 영역을 만들고, 키를 가져오고, 환경 변수에서 정보를 설정 합니다.

첫 번째 항목은 메트릭을 업로드 하 고 두 번째 항목은 로그를 업로드 하는 데 필요 합니다.

다음 명령을 수행 하 여 메트릭 업로드 서비스 주체를 만들고, 서비스 주체가 메트릭을 업로드 하 고 만들기 및 업로드 작업을 수행할 수 있도록 ' 모니터링 메트릭 게시자 ' 및 ' 참가자 ' 역할에 할당 합니다.

## <a name="create-service-principal-and-assign-roles"></a>서비스 사용자 만들기 및 역할 할당

다음 명령을 수행 하 여 메트릭 업로드 서비스 주체를 만들고 ' 모니터링 메트릭 게시자 ' 역할에 할당 합니다.

서비스 주체를 만들려면 다음 명령을 실행 합니다.

> [!NOTE]
> 서비스 주체를 만들려면 [Azure에서 특정 권한이](/active-directory/develop/howto-create-service-principal-portal#required-permissions)필요 합니다.

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

예제 출력:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

나중에 사용 하기 위해 appId 및 테 넌 트 값을 환경 변수에 저장 합니다. 

PowerShell을 사용 하 여 appId 및 테 넌 트 값을 저장 하려면 다음 예제를 따르세요.

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

또는 Linux 또는 macOS에서 다음 예제를 사용 하 여 appId 및 테 넌 트 값을 저장할 수 있습니다.

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

다음 명령을 실행 하 여 데이터베이스 인스턴스 리소스가 있는 구독의 ' 모니터링 메트릭 게시자 ' 역할에 서비스 주체를 할당 합니다.


> [!NOTE]
> Windows 환경에서 실행할 때 역할 이름에 큰따옴표를 사용 해야 합니다.


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

예제 출력:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Log analytics 작업 영역 만들기

다음으로 다음 명령을 실행 하 여 Log Analytics 작업 영역을 만들고 액세스 정보를 환경 변수로 설정 합니다.

> [!NOTE]
> 작업 영역이 이미 있는 경우이 단계를 건너뜁니다.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

예제 출력:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>환경 변수에 ID 및 공유 키 할당

CustomerId (작업 영역 ID)를 나중에 사용할 환경 변수로 저장 합니다.

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

이 명령은 log analytics 작업 영역에 연결 하는 데 필요한 액세스 키를 인쇄 합니다.

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

예제 출력:

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

나중에 사용할 기본 키를 환경 변수에 저장 합니다.

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>최종 환경 변수 설정 및 확인

환경 변수에서 SPN 기관 URL을 설정 합니다.

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

필요한 경우 다음과 같이 필요한 모든 환경 변수를 설정 했는지 확인 합니다.

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>Azure Monitor에 메트릭 업로드

Azure arc 사용 설정 SQL 관리 되는 인스턴스 및 Azure Arc 사용 PostgreSQL Hyperscale server 그룹이 실행 되는 메트릭을 업로드 하려면 다음 CLI 명령을 실행 합니다.

1. 모든 메트릭을 지정 된 파일로 내보냅니다.

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Azure monitor에 메트릭 업로드:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >첫 번째 업로드에 대해 Azure Arc 사용 데이터 인스턴스를 만든 후 30 분 이상 기다립니다.
   >
   >`upload`Azure Monitor 후에 바로 메트릭이 `export` 지난 30 분 동안의 메트릭을 허용 하는지 확인 합니다. [자세한 정보](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


내보내기 중에 "메트릭 가져오기 실패"를 나타내는 오류가 표시 되는 경우 ```true``` 다음 명령을 실행 하 여 데이터 수집이로 설정 되었는지 확인 합니다.

```console
azdata arc dc config show
```

"보안 섹션" 아래에서 확인 합니다.

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

`allowNodeMetricsCollection`및 `allowPodMetricsCollection` 속성이로 설정 되어 있는지 확인 `true` 합니다.

## <a name="view-the-metrics-in-the-portal"></a>포털에서 메트릭 보기

메트릭이 업로드 되 면 Azure Portal에서 메트릭을 볼 수 있습니다.
> [!NOTE]
> 업로드 된 데이터를 처리 하는 데 몇 분 정도 걸릴 수 있으므로 포털에서 메트릭을 볼 수 있습니다.


포털에서 메트릭을 보려면이 링크를 사용 하 여 포털을 연 <https://portal.azure.com> 다음 검색 표시줄에서 이름으로 데이터베이스 인스턴스를 검색 합니다.

개요 페이지에서 CPU 사용률을 보거나, 왼쪽 탐색 패널에서 메트릭을 클릭 하 여 더 자세한 메트릭을 원할 수 있습니다.

메트릭 네임 스페이스로 sql server를 선택 합니다.

시각화할 메트릭을 선택 합니다 (여러 항목을 선택할 수도 있음).

빈도를 최근 30 분으로 변경 합니다.

> [!NOTE]
> 최근 30 분 동안만 메트릭을 업로드할 수 있습니다. Azure Monitor는 30 분 보다 오래 된 메트릭을 거부 합니다.

## <a name="upload-logs-to-azure-monitor"></a>Azure Monitor에 로그 업로드

 Azure Arc 사용 SQL 관리 되는 인스턴스 및 AzureArc 사용 PostgreSQL Hyperscale 서버 그룹에 대 한 로그를 업로드 하려면 다음 CLI 명령을 실행 합니다.

1. 모든 로그를 지정 된 파일로 내보내기:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. Azure monitor log analytics 작업 영역에 로그를 업로드 합니다.

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Azure Portal에서 로그 보기

로그가 업로드되면 다음과 같이 로그 쿼리 탐색기를 사용하여 로그를 쿼리할 수 있습니다.

1. Azure Portal를 열고 맨 위에 있는 검색 표시줄에서 이름으로 작업 영역을 검색 한 다음 선택 합니다.
2. 왼쪽 패널에서 로그를 클릭합니다.
3. 시작 하기를 클릭 하거나 시작 페이지의 링크를 클릭 하 여 Log Analytics에 대 한 자세한 내용을 확인 합니다.
4. 처음 사용 하는 경우 Log Analytics에 대 한 자세한 내용은 자습서를 참조 하세요 Log Analytics
5. 테이블 목록의 맨 아래에 있는 사용자 지정 로그를 확장하면 'sql_instance_logs_CL'이라는 테이블이 표시됩니다.
6. 테이블 이름 옆의 '눈' 아이콘을 클릭합니다.
7. '쿼리 편집기에서 보기' 단추를 클릭합니다.
8. 이제 쿼리 편집기에는 로그에 최근 10 개의 이벤트를 표시 하는 쿼리가 있습니다.
9. 여기에서 쿼리 편집기를 사용하여 로그 쿼리, 경고 설정 등을 시험해 볼 수 있습니다.

## <a name="automating-uploads-optional"></a>업로드 자동화 (선택 사항)

일정에 따라 메트릭과 로그를 업로드 하려는 경우 몇 분 마다 스크립트를 만들어 타이머에서 실행할 수 있습니다. 다음은 Linux shell 스크립트를 사용 하 여 업로드를 자동화 하는 예제입니다.

자주 사용 하는 텍스트/코드 편집기에서 다음 스크립트를 파일에 추가 하 고 스크립트 실행 파일 (예: sh (Linux/Mac) 또는 .cmd, .bat, ps1)로 저장 합니다.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

스크립트 파일 실행 파일 만들기

```console
chmod +x myuploadscript.sh
```

20 분 마다 스크립트를 실행 합니다.

```console
watch -n 1200 ./myuploadscript.sh
```

Cron 또는 Windows 작업 스케줄러와 같은 작업 스케줄러 또는 Ansible, 퍼핏 또는 Chef와 같은 orchestrator를 사용할 수도 있습니다.

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>사용, 메트릭 내보내기 및 업로드에 대 한 일반 지침

Azure Arc 활성화 된 데이터 서비스에 대 한 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업은 청구 및 모니터링을 위해 기록 됩니다. 이러한 CRUD 작업을 모니터링 하 고 소비를 적절 하 게 계산 하는 백그라운드 서비스가 있습니다. 사용 또는 소비의 실제 계산은 일정에 따라 수행 되며 백그라운드에서 수행 됩니다. 

미리 보기 중에이 프로세스는 밤에 발생 합니다. 일반적인 지침은 하루에 한 번만 사용을 업로드 하는 것입니다. 사용 정보를 동일한 24 시간 내에 여러 번 내보내고 업로드 하면 리소스 인벤토리가 Azure Portal 업데이트 되지만 리소스 사용량은 업데이트 되지 않습니다.

메트릭 업로드의 경우 Azure monitor는 최근 30 분의 데이터만 수락 합니다 ([자세한 정보](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). 메트릭을 업로드 하기 위한 지침은 내보내기 파일을 만든 후 즉시 메트릭을 업로드 하 여 Azure Portal에서 전체 데이터 집합을 볼 수 있도록 하는 것입니다. 예를 들어, 메트릭을 2:00 PM으로 내보내고 2:50 PM에 업로드 명령을 실행 한 경우 Azure Monitor는 최근 30 분 동안의 데이터만 허용 하므로 포털에 데이터가 표시 되지 않을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[청구 데이터를 Azure에 업로드 하 고 Azure Portal에서 확인](view-billing-data-in-azure.md)

[Azure Portal에서 Azure Arc 데이터 컨트롤러 리소스 보기](view-data-controller-in-azure-portal.md)
