---
title: Azure Monitor에 로그 업로드
description: Azure Arc 사용 가능 데이터 서비스에 대 한 로그를 Azure Monitor에 업로드
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92378148"
---
# <a name="upload-logs-to-azure-monitor"></a>Azure Monitor에 로그 업로드

정기적으로 로그를 내보낸 후 Azure에 업로드할 수 있습니다. 로그 내보내기 및 업로드는 또한 Azure에서 데이터 컨트롤러, SQL 관리 되는 인스턴스 및 PostgreSQL Hyperscale 서버 그룹 리소스를 만들고 업데이트 합니다.

> [!NOTE] 
> 미리 보기 기간 중에는 Azure Arc 사용 데이터 서비스를 사용 하는 비용이 없습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>시작하기 전에

로그를 업로드 하려면 먼저 다음을 수행 해야 합니다. 

1. [Log analytics 작업 영역 만들기](#create-a-log-analytics-workspace)
1. [환경 변수에 ID 및 공유 키 할당](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>Log analytics 작업 영역 만들기

Log analytics 작업 영역을 만들려면 다음 명령을 실행 하 여 Log Analytics 작업 영역을 만들고 액세스 정보를 환경 변수로 설정 합니다.

> [!NOTE]
> 작업 영역이 이미 있는 경우이 단계를 건너뜁니다.

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
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

로그 작업 영역 분석을 `customerId` 나중에 사용할 환경 변수로 저장 합니다.

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

이 명령은 log analytics 작업 영역에 연결 하는 데 필요한 액세스 키를 반환 합니다.

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

예제 출력:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

나중에 사용할 기본 키를 환경 변수에 저장 합니다.

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>최종 환경 변수 설정 및 확인

환경 변수에서 SPN 기관 URL을 설정 합니다.

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>환경 변수 확인

필요한 경우 다음과 같이 필요한 모든 환경 변수를 설정 했는지 확인 합니다.


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

환경 변수를 설정 하면 로그를 로그 작업 영역으로 업로드할 수 있습니다. 

## <a name="upload-logs-to-azure-monitor"></a>Azure Monitor에 로그 업로드

 Azure Arc 사용 SQL 관리 되는 인스턴스 및 AzureArc 사용 PostgreSQL Hyperscale 서버 그룹에 대 한 로그를 업로드 하려면 다음 CLI 명령을 실행 합니다.

1. 을 사용 하 여 Azure Arc 데이터 컨트롤러에 로그인 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 합니다.

   ```console
   azdata login
   ```

   프롬프트에 따라 네임 스페이스, 관리자 사용자 이름 및 암호를 설정 합니다. 

1. 모든 로그를 지정 된 파일로 내보내기:

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. Azure monitor log analytics 작업 영역에 로그를 업로드 합니다.

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Azure Portal에서 로그 보기

로그가 업로드되면 다음과 같이 로그 쿼리 탐색기를 사용하여 로그를 쿼리할 수 있습니다.

1. Azure Portal를 열고 맨 위에 있는 검색 표시줄에서 이름으로 작업 영역을 검색 한 다음 선택 합니다.
2. 왼쪽 패널에서 로그를 선택 합니다.
3. 시작을 선택 하거나 시작 페이지에서 링크를 선택 하 여 Log Analytics에 대 한 자세한 내용을 확인 합니다.
4. Log Analytics를 처음 사용 하는 경우 Log Analytics에 대해 자세히 알아보려면 자습서를 참조 하세요.
5. 테이블 목록의 맨 아래에 있는 사용자 지정 로그를 확장하면 'sql_instance_logs_CL'이라는 테이블이 표시됩니다.
6. 테이블 이름 옆의 ' 눈 ' 아이콘을 선택 합니다.
7. ' 쿼리 편집기에서 보기 ' 단추를 선택 합니다.
8. 이제 쿼리 편집기에서 가장 최근 10 개의 이벤트를 로그에 표시 하는 쿼리를 갖게 됩니다.
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

## <a name="next-steps"></a>다음 단계

[메트릭 및 로그 업로드 Azure Monitor](upload-metrics.md)

[Azure Monitor에 사용 현황 데이터, 메트릭 및 로그 업로드](upload-usage-data.md)

[청구 데이터를 Azure에 업로드 하 고 Azure Portal에서 확인](view-billing-data-in-azure.md)

[Azure Portal에서 Azure Arc 데이터 컨트롤러 리소스 보기](view-data-controller-in-azure-portal.md)
