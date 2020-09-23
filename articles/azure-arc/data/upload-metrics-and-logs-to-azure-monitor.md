---
title: Azure Monitor에 리소스 인벤토리, 사용 현황 데이터, 메트릭 및 로그 업로드
description: Azure Monitor에 리소스 인벤토리, 사용 현황 데이터, 메트릭 및 로그 업로드
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939104"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>Azure Monitor에 리소스 인벤토리, 사용 현황 데이터, 메트릭 및 로그 업로드

Azure Arc data services를 사용 하면 *필요에 따라* 메트릭과 로그를 Azure Monitor에 업로드 하 여 메트릭, 로그, 경고 생성, 알림 전송 또는 자동화 된 작업 트리거를 집계 하 고 분석할 수 있습니다. 데이터를 Azure Monitor로 보내면 모니터링 및 로그 데이터를 사이트에 저장할 수 있으며, 대규모로 고급 분석을 위해 데이터의 장기 저장소를 사용할 수 있습니다.  Azure Arc data services가 있는 여러 사이트가 있는 경우 Azure Monitor를 중앙 위치로 사용 하 여 사이트 전체에서 모든 로그와 메트릭을 수집할 수 있습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>시작하기 전에

로그 및 메트릭 업로드 시나리오를 사용 하도록 설정 하는 데 필요한 몇 가지 일회성 설정 단계가 있습니다.

1) 클라이언트 액세스 암호 만들기를 포함 하 여 서비스 사용자/Azure Active Directory 응용 프로그램을 만들고 데이터베이스 인스턴스 리소스가 있는 구독의 ' 모니터링 메트릭 게시자 ' 역할에 서비스 주체를 할당 합니다.
2) Log analytics 작업 영역을 만들고, 키를 가져오고, 환경 변수에서 정보를 설정 합니다.

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

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

나중에 사용 하기 위해 appId 및 테 넌 트 값을 환경 변수에 저장 합니다.

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

다음 명령을 실행 하 여 데이터베이스 인스턴스 리소스가 있는 구독의 ' 모니터링 메트릭 게시자 ' 역할에 서비스 주체를 할당 합니다.

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

예제 출력:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Log analytics 작업 영역 만들기

다음으로 다음 명령을 실행 하 여 Log Analytics 작업 영역을 만들고 액세스 정보를 환경 변수로 설정 합니다.

> [!NOTE]
> 작업 영역이 이미 있는 경우이 단계를 건너뜁니다.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

예제 출력:

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
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

Azure SQL 관리 되는 인스턴스 및 Azure Database for PostgreSQL Hyperscale server 그룹이 실행 되는 메트릭을 업로드 하려면 다음 CLI 명령을 실행 합니다.

이렇게 하면 모든 메트릭이 지정된 파일로 내보냅니다.

```console
azdata arc dc export -t metrics --path metrics.json
```

Azure monitor에 메트릭을 업로드합니다.

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>포털에서 메트릭 보기

메트릭을 업로드하면 Azure Portal에서 시각화할 수 있습니다.

포털에서 메트릭을 보려면이 특수 링크를 사용 하 여 포털을 연 <https://portal.azure.com> 다음 검색 표시줄에서 이름으로 데이터베이스 인스턴스를 검색 합니다.

개요 페이지에서 CPU 사용률을 보거나, 왼쪽 탐색 패널에서 메트릭을 클릭 하 여 더 자세한 메트릭을 원할 수 있습니다.

메트릭 네임 스페이스로 sql server를 선택 합니다.

시각화할 메트릭을 선택 합니다 (여러 항목을 선택할 수도 있음).

빈도를 최근 30 분으로 변경 합니다.

> [!NOTE]
> 최근 30 분 동안만 메트릭을 업로드할 수 있습니다. Azure Monitor는 30 분 보다 오래 된 메트릭을 거부 합니다.

## <a name="upload-logs-to-azure-monitor"></a>Azure Monitor에 로그 업로드

 Azure SQL 관리 되는 인스턴스 및 Azure Database for PostgreSQL Hyperscale 서버 그룹에 대 한 로그를 업로드 하려면 다음 CLI 명령을 실행 합니다.

이렇게 하면 모든 로그가 지정된 파일로 내보냅니다.

```console
azdata arc dc export -t logs --path logs.json
```

Azure monitor log analytics 작업 영역에 로그를 업로드 합니다.

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>Azure Portal에서 로그 보기

로그가 업로드되면 다음과 같이 로그 쿼리 탐색기를 사용하여 로그를 쿼리할 수 있습니다.

1. Azure Portal를 열고 맨 위에 있는 검색 표시줄에서 이름으로 작업 영역을 검색 한 다음 선택 합니다.
2. 왼쪽 패널에서 로그를 클릭합니다.
3. 시작 하기를 클릭 하거나 시작 페이지의 링크를 클릭 하 여 Log Analytics에 대 한 자세한 내용을 확인 합니다.
4. 이 처음 인 경우 Log Analytics에 대해 자세히 알아보려면 자습서를 따르세요.
5. 테이블 목록의 맨 아래에 있는 사용자 지정 로그를 확장하면 'sql_instance_logs_CL'이라는 테이블이 표시됩니다.
6. 테이블 이름 옆의 '눈' 아이콘을 클릭합니다.
7. '쿼리 편집기에서 보기' 단추를 클릭합니다.
8. 이제 쿼리 편집기에 쿼리를 포함하여 로그에 최근 10개의 이벤트를 표시합니다.
9. 여기에서 쿼리 편집기를 사용하여 로그 쿼리, 경고 설정 등을 시험해 볼 수 있습니다.

## <a name="automating-metrics-and-logs-uploads-optional"></a>메트릭 및 로그 업로드 자동화 (선택 사항)

메트릭 및 로그를 지속적으로 업로드 하려는 경우 몇 분 마다 스크립트를 만들어 타이머에서 실행할 수 있습니다.  다음은 Linux shell 스크립트를 사용 하 여 업로드를 자동화 하는 예제입니다.

자주 사용 하는 텍스트/코드 편집기에서 파일에 다음을 추가 하 고. sh (Linux/Mac) 또는 .cmd, .bat, ps1와 같은 스크립트 실행 파일로 저장 합니다.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

스크립트 파일 실행 파일 만들기

```console
chmod +x myuploadscript.sh
```

2 분 마다 스크립트를 실행 합니다.

```console
watch -n 120 ./myuploadscript.sh
```

Cron 또는 Windows 작업 스케줄러와 같은 작업 스케줄러 또는 Ansible, 퍼핏 또는 Chef와 같은 orchestrator를 사용할 수도 있습니다.
