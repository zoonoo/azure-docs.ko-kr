---
title: Azure Monitor에 메트릭 업로드
description: Azure Monitor에 Azure Arc 사용 데이터 서비스 메트릭을 업로드 합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f31cf22ae8814975dcee92c33026d223275cf121
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92378134"
---
# <a name="upload-metrics-to-azure-monitor"></a>Azure Monitor에 메트릭 업로드

정기적으로 모니터링 메트릭을 내보낸 다음 Azure에 업로드할 수 있습니다. 데이터의 내보내기 및 업로드는 또한 Azure에서 데이터 컨트롤러, SQL 관리 되는 인스턴스 및 PostgreSQL Hyperscale 서버 그룹 리소스를 만들고 업데이트 합니다.

> [!NOTE] 
> 미리 보기 기간 중에는 Azure Arc 사용 데이터 서비스를 사용 하는 비용이 없습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

계속 하기 전에 필요한 서비스 주체를 만들고 적절 한 역할에 할당 했는지 확인 합니다. 자세한 내용은 다음을 참조하세요.
* [서비스 주체를 만듭니다](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [서비스 사용자에 게 역할 할당](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>메트릭 업로드

Azure Arc data services를 사용 하 여 필요에 따라 메트릭을 집계 및 분석 하거나, 경고를 발생 시키거나, 알림을 보내거나, 자동화 된 작업을 트리거할 수 있도록 Azure Monitor에 메트릭을 업로드할 수 있습니다. 

Azure Monitor로 데이터를 보내면 메트릭 데이터를 사이트 외부 및 대규모로 저장할 수 있으므로 고급 분석을 위해 데이터의 장기 저장소를 사용할 수 있습니다.

Azure Arc data services가 있는 여러 사이트가 있는 경우 Azure Monitor를 중앙 위치로 사용 하 여 사이트 전체에서 모든 로그와 메트릭을 수집할 수 있습니다.

## <a name="set-final-environment-variables-and-confirm"></a>최종 환경 변수 설정 및 확인

환경 변수에서 SPN 기관 URL을 설정 합니다.

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

필요한 경우 다음과 같이 필요한 모든 환경 변수를 설정 했는지 확인 합니다.


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>Azure Monitor에 메트릭 업로드

Azure arc 사용 설정 SQL 관리 되는 인스턴스 및 Azure Arc 사용 PostgreSQL Hyperscale server 그룹이 실행 되는 메트릭을 업로드 하려면 다음 CLI 명령을 실행 합니다.

1. 를 사용 하 여 데이터 컨트롤러에 로그인 `azdata` 합니다.
 
1. 모든 메트릭을 지정 된 파일로 내보냅니다.

   ```console
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Azure monitor에 메트릭 업로드:

   ```console
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >첫 번째 업로드에 대해 Azure Arc 사용 데이터 인스턴스를 만든 후 30 분 이상 기다립니다.
   >
   >`upload`Azure Monitor 후에 바로 메트릭이 `export` 지난 30 분 동안의 메트릭을 허용 하는지 확인 합니다. [자세히 알아보기](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting).


내보내기 중에 "메트릭 가져오기 실패"를 나타내는 오류가 표시 되는 경우 `true` 다음 명령을 실행 하 여 데이터 수집이로 설정 되었는지 확인 합니다.

```console
azdata arc dc config show
```

"보안 섹션" 아래에서 확인

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

[Azure Monitor에 로그 업로드](upload-logs.md)

[Azure Monitor에 사용 현황 데이터, 메트릭 및 로그 업로드](upload-usage-data.md)

[청구 데이터를 Azure에 업로드 하 고 Azure Portal에서 확인](view-billing-data-in-azure.md)

[Azure Portal에서 Azure Arc 데이터 컨트롤러 리소스 보기](view-data-controller-in-azure-portal.md)
