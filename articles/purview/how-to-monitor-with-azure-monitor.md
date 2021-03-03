---
title: Azure 부서의 범위를 모니터링 하는 방법
description: Azure Monitor를 사용 하 여 Azure 부서의 범위 메트릭, 경고 및 진단 설정을 구성 하는 방법에 대해 알아봅니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 4cb3965d359980856c238cd563ed8b761754660b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667733"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Azure Monitor Azure 부서의 범위 메트릭

이 문서에서는 Azure Monitor를 사용 하 여 Azure 부서의 범위에 대 한 메트릭, 경고 및 진단 설정을 구성 하는 방법을 설명 합니다.

## <a name="monitor-azure-purview"></a>Azure 부서의 범위 모니터링

Azure 부서의 범위 관리자는 Azure Monitor를 사용 하 여 부서의 범위 계정의 작동 상태를 추적할 수 있습니다. 메트릭은 잠재적 문제를 추적 하 고, 문제를 해결 하 고, 부서의 범위 계정의 안정성을 향상 시킬 수 있는 데이터 요소를 제공 하기 위해 수집 됩니다. 메트릭은 Azure 부서의 범위에서 발생 하는 이벤트에 대해 Azure monitor로 전송 됩니다.

## <a name="aggregated-metrics"></a>집계 된 메트릭

부서의 범위 계정에 대 한 Azure Portal에서 메트릭에 액세스할 수 있습니다. 메트릭에 대 한 액세스는 부서의 범위 계정의 역할 할당에 의해 제어 됩니다. 메트릭을 보려면 Azure 부서의 범위의 "모니터링 독자" 역할의 일부 여야 합니다. 역할 액세스 수준에 대 한 자세한 내용은 [모니터링 읽기 권한자 역할 권한](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) 을 참조 하세요.

부서의 범위 계정을 만든 사람에 게는 메트릭을 볼 수 있는 권한이 자동으로 부여 됩니다. 다른 사용자가 메트릭을 보려면 다음 단계를 수행 하 여 **모니터링 판독기** 역할에 추가 합니다.

### <a name="add-a-user-to-the-monitoring-reader-role"></a>모니터링 판독기 역할에 사용자 추가

**모니터링 판독기** 역할에 사용자를 추가 하려면 부서의 범위 계정 또는 구독 소유자의 소유자가 다음 단계를 따를 수 있습니다.

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 Azure 부서의 범위 계정 이름을 검색 합니다.

2. **액세스 제어(IAM)** 를 선택합니다.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="IAM에 액세스 하는 방법을 보여 주는 스크린샷":::

3. **역할 할당 추가를** 선택 합니다.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="역할 할당을 추가 하는 방법을 보여 주는 스크린샷":::

4. 역할 **모니터링 판독기** 를 선택 하 고 **Azure AD 사용자, 그룹 또는 서비스 주체** 에 대 한 액세스 할당을 설정 합니다. 그리고 메트릭에 액세스 하기 위해 AAD 계정을 할당 합니다.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="모니터링 판독기 역할을 추가 하는 방법을 보여 주는 스크린샷":::

## <a name="metrics-visualization"></a>메트릭 시각화

**모니터링 판독기** 역할의 사용자는 Azure Monitor로 전송 된 집계 메트릭 및 진단 로그를 볼 수 있습니다. 메트릭은 해당 부서의 범위 계정에 대 한 Azure Portal에 나열 됩니다. Azure Portal에서 메트릭 섹션을 선택 하 여 사용 가능한 모든 메트릭의 목록을 표시 합니다.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="사용 가능한 부서의 범위 메트릭 섹션을 보여 주는 스크린샷" lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Azure 부서의 범위 사용자는 Azure 부서의 범위 계정의 관리 센터에서 직접 메트릭 페이지에 액세스할 수도 있습니다. 부서의 범위 management center의 기본 페이지에서 Azure Monitor를 선택 하 여 Azure Portal를 시작 합니다.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="관리 센터에서 부서의 범위 메트릭을 시작 하는 스크린샷" lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>사용 가능한 메트릭

Azure Portal에서 메트릭 섹션을 사용 하는 방법에 대 한 자세한 내용을 보려면 다음 두 문서를 미리 읽으십시오. 메트릭 탐색기 및 [메트릭 탐색기의 고급 기능](../azure-monitor/essentials/metrics-charts.md) [을 시작](../azure-monitor/essentials/metrics-getting-started.md) 합니다.

다음 표에는 Azure Portal에서 탐색할 수 있는 메트릭 목록이 포함 되어 있습니다.

| 메트릭 이름 | 메트릭 네임스페이스 | 집계 유형 | 설명 |
| ------------------- | ------------------- | ------------------- | ----------------- |
| 검사 취소 됨 | 자동 검색 | 합계 <br> 개수 | 기간에 따라 취소 된 데이터 원본 검색 집계 |
| 검사 완료 | 자동 검색 | 합계 <br> 개수 | 기간에 따라 완료 된 데이터 원본 검색 집계 |
| 검사 실패 | 자동 검색 | 합계 <br> 개수 | 기간별로 실패 한 데이터 원본 검색 집계 |
| 검색 된 시간 | 자동 검색 | 최소값 <br> 최대값 <br> 합계 <br> Avg | 검색 시간 동안 검색에 소요 된 총 시간 집계 |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Azure Storage 계정에 대 한 진단 로그

원시 원격 분석 이벤트는 Azure Monitor 내보내집니다. 추가 분석을 위해 선택한 고객 저장소 계정에 이벤트를 기록할 수 있습니다. 로그 내보내기는 Azure Portal의 부서의 범위 계정에 대 한 진단 설정을 통해 수행 됩니다.

단계에 따라 Azure 부서의 범위 계정에 대 한 진단 설정을 만듭니다.

1. 플랫폼 로그 및 메트릭을 [다른 대상으로 보내기 위한 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)문서에 따라 새 진단 설정을 만들어 플랫폼 로그 및 메트릭을 수집 합니다. 대상만 Azure storage 계정으로 선택 합니다.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="진단 로그를 만드는 과정을 보여 주는 스크린샷" lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. 저장소 계정에 이벤트를 기록 합니다. 진단 로그를 보관 하는 데 전용 저장소 계정이 권장 됩니다. 이 문서에 따라 [저장소 계정을 만듭니다](../storage/common/storage-account-create.md?tabs=azure-portal).

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="진단 로그에 대 한 저장소 계정 할당을 보여 주는 스크린샷" lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

새로 만든 저장소 계정에서 로그 수신을 시작 하는 데 최대 15 분이 소요 됩니다. [Azure Storage 계정에서 리소스 로그의 데이터 보존 및 스키마를 참조](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)하세요. 진단 로그가 구성 되 면 이벤트는 저장소 계정으로 전달 됩니다.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

이벤트는 검색 수명 주기를 추적 합니다. 검색 작업은 대기 중, 실행 중, 마지막으로 성공의 터미널 상태와 같은 상태 시퀀스를 통해 진행 됩니다. 실패 | 님. 이벤트는 각 상태 전환에 대해 기록 되 고 이벤트의 스키마에는 다음 속성이 포함 됩니다.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

이벤트 인스턴스의 예제 로그는 아래 섹션에 나와 있습니다.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>다음 단계

[Asset insights 보기](asset-insights.md)