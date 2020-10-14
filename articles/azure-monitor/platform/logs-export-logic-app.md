---
title: 논리 앱을 사용 하 여 Log Analytics 작업 영역에서 Azure storage로 데이터 보관
description: Azure Logic Apps 사용 하 여 Log Analytics 작업 영역에서 데이터를 쿼리하고 Azure Storage으로 보내는 방법을 설명 합니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: ed9942fa7b73418e3ef1ddf0651781d32b662995
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049948"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>논리 앱을 사용 하 여 Log Analytics 작업 영역에서 Azure storage로 데이터 보관
이 문서에서는 [Azure Logic Apps](../../logic-apps/index.yml) 를 사용 하 여 Azure Monitor의 Log Analytics 작업 영역에서 데이터를 쿼리하고 Azure Storage로 보내는 방법에 대해 설명 합니다. 감사 및 규정 준수 시나리오에 대 한 Azure Monitor 로그 데이터를 내보내야 하거나 다른 서비스에서이 데이터를 검색할 수 있도록 하려면이 프로세스를 사용 합니다.  

## <a name="other-export-methods"></a>기타 내보내기 방법
이 문서에서 설명 하는 메서드는 논리 앱을 사용 하 여 로그 쿼리에서 예약 된 내보내기를 설명 합니다. 특정 시나리오에 대 한 데이터를 내보내기 위한 기타 옵션은 다음과 같습니다.

- Log Analytics 작업 영역의 모든 데이터를 Azure storage 계정 또는 이벤트 허브로 내보내려면 Azure Monitor 로그의 Log Analytics 작업 영역 데이터 내보내기 기능을 사용 합니다. [Azure Monitor에서 Log Analytics 작업 영역 데이터 내보내기 (미리 보기)를](logs-data-export.md) 참조 하세요.
- 논리 앱을 사용 하 여 한 번 내보냅니다. [Logic Apps 및 전원 자동화에 대 한 Azure Monitor 로그 커넥터를](logicapp-flow-connector.md)참조 하세요.
- PowerShell 스크립트를 사용 하 여 로컬 컴퓨터에 한 번 내보냅니다. [AzOperationalInsightsQueryExport]] (를 참조 하세요 https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport) .

## <a name="overview"></a>개요
이 절차에서는 논리 앱에서 로그 쿼리를 실행 하 고 워크플로의 다른 작업에서 해당 출력을 사용할 수 있는 [Azure Monitor Logs 커넥터](https://docs.microsoft.com/connectors/azuremonitorlogs/) 를 사용 합니다. 이 절차에서는 [Azure Blob Storage 커넥터](https://docs.microsoft.com/connectors/azureblob/) 를 사용 하 여 쿼리 출력을 Azure Storage로 보냅니다. 다른 작업은 아래 섹션에 설명 되어 있습니다.

![논리 앱 개요](media/logs-export-logicapp/logic-app-overview.png)

Log Analytics 작업 영역에서 데이터를 내보낼 때는 로그 데이터를 필터링 및 집계 하 고 논리 앱 워크플로에서 처리 하는 데이터의 양을 필요한 데이터로 제한 하도록 쿼리를 최적화 해야 합니다. 예를 들어 로그인 이벤트를 보관 해야 하는 경우 필요한 이벤트를 필터링 하 고 다음 쿼리를 사용 하 여 필수 필드만 프로젝션 할 수 있습니다. 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

일정에 따라 데이터를 내보낼 때는 쿼리에 ingestion_time () 함수를 사용 하 여 지연 도착 데이터가 누락 되지 않도록 합니다. 네트워크 또는 플랫폼 문제로 인해 데이터가 지연 되는 경우 수집 시간을 사용 하면 다음 논리 앱 실행에 포함 될 수 있습니다. 예는 [Azure Monitor 로그 추가 작업](#add-azure-monitor-logs-action) 을 참조 하세요.

## <a name="prerequisites"></a>전제 조건
다음은이 절차를 완료 하기 전에 완료 해야 하는 필수 구성 요소입니다.

- Log Analytics 작업 영역. 논리 앱을 만드는 사용자에 게는 작업 영역에 대 한 읽기 이상의 권한이 있어야 합니다. 
- Azure 저장소 계정. 저장소 계정은 Log Analytics 작업 영역과 동일한 구독에 있을 필요가 없습니다. 논리 앱을 만드는 사용자에 게는 저장소 계정에 대 한 쓰기 권한이 있어야 합니다. 


## <a name="connector-limits"></a>커넥터 제한
Azure Monitor의 Log Analytics 작업 영역 및 로그 쿼리는 고객을 보호 하 고 서비스 품질을 유지 하는 제한을 포함 하는 배포할지에 services입니다. 많은 양의 데이터를 쿼리할 때 논리 앱 되풀이 및 로그 쿼리를 구성 하는 방법에 영향을 줄 수 있는 다음 제한을 고려해 야 합니다.

- 로그 쿼리는 50만 개 보다 많은 행을 반환할 수 없습니다.
- 로그 쿼리는 6400만 바이트를 초과할 수 없습니다.
- 로그 쿼리는 기본적으로 10 분 이상 실행할 수 없습니다. 
- Log Analytics 커넥터는 분당 100 호출로 제한 됩니다.


## <a name="create-container-in-the-storage-account"></a>저장소 계정에서 컨테이너 만들기
[컨테이너 만들기](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) 의 절차를 사용 하 여 내보낸 데이터를 보관할 컨테이너를 저장소 계정에 추가 합니다. 이 아티클의 컨테이너에 사용 되는 이름은 **loganalytics-데이터**이지만 임의의 이름을 사용할 수 있습니다.


## <a name="create-logic-app"></a>논리 앱 만들기

Azure Portal **Logic Apps** 로 이동 하 고 **추가**를 클릭 합니다. 새 논리 앱을 저장할 **구독**, **리소스 그룹**및 **지역을** 선택 하 고 고유한 이름을 지정 합니다. [Azure Monitor 로그 설정 및 Azure Logic Apps에 대 한 진단 데이터 수집](../../logic-apps/monitor-logic-apps-log-analytics.md)에 설명 된 대로 런타임 데이터 및 이벤트에 대 한 정보를 수집 하는 **Log Analytics** 설정을 켤 수 있습니다. Azure Monitor Logs 커넥터를 사용 하는 경우이 설정이 필요 하지 않습니다.

![논리 앱 만들기](media/logs-export-logicapp/create-logic-app.png)


**검토 + 만들기** 및 **만들기**를 차례로 클릭 합니다. 배포가 완료 되 면 **리소스로 이동** 을 클릭 하 여 **Logic Apps 디자이너**를 엽니다.

## <a name="create-a-trigger-for-the-logic-app"></a>논리 앱에 대 한 트리거 만들기
**일반적인 트리거로 시작**에서 **되풀이**를 선택 합니다. 이렇게 하면 일정 한 간격으로 자동으로 실행 되는 논리 앱이 만들어집니다. 작업의 **빈도** 상자에서 **시간** 을 선택 하 고 **간격** 상자에 **1** 을 입력 하 여 하루에 한 번 워크플로를 실행 합니다.

![되풀이 작업](media/logs-export-logicapp/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Azure Monitor 로그 추가 작업
**+ 새 단계** 를 클릭 하 여 되풀이 작업 후 실행 되는 작업을 추가 합니다. **작업 선택**아래에서 **azure monitor** 를 입력 하 고 **Azure Monitor 로그**를 선택 합니다.

![Azure Monitor 로그 작업](media/logs-export-logicapp/select-azure-monitor-connector.png)

**Azure Log Analytics – 쿼리 실행 및 결과 나열을**클릭 합니다.

![논리 앱 디자이너의 단계에 추가 되는 새 작업의 스크린샷 작업 선택에서 Azure Monitor 로그가 강조 표시 됩니다.](media/logs-export-logicapp/select-query-action-list.png)

테 넌 트를 선택 하 고 워크플로에서 쿼리를 실행 하는 데 사용할 계정을 사용 하 여 Log Analytics 작업 영역에 대 한 액세스 권한을 부여 하 라는 메시지가 표시 됩니다.


## <a name="add-azure-monitor-logs-action"></a>Azure Monitor 로그 추가 작업
Azure Monitor Logs 작업을 사용 하 여 실행할 쿼리를 지정할 수 있습니다. 이 예에 사용 된 로그 쿼리는 시간별 되풀이에 최적화 되어 특정 실행 시간에 대 한 데이터 수집 수집 합니다. 예를 들어 워크플로가 4:35에서 실행 되는 경우 시간 범위는 4:00 ~ 5:00입니다. 논리 앱을 다른 빈도로 실행 하도록 변경 하는 경우에도 쿼리를 변경 해야 합니다. 예를 들어 매일 되풀이를 실행 하도록 설정 하는 경우 쿼리에서 startTime을 startofday (make_datetime (year, month, day, 0, 0))로 설정 합니다. 

Log Analytics 작업 영역에 대 한 **구독** 및 **리소스 그룹** 을 선택 합니다. **리소스 종류** 에 대 한 *Log Analytics 작업 영역* 을 선택 하 고 **리소스 이름**아래에서 작업 영역 이름을 선택 합니다.

다음 로그 쿼리를 **쿼리** 창에 추가합니다.  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

**시간 범위** 는 **timegenerated** 열을 기반으로 쿼리에 포함 될 레코드를 지정 합니다. 쿼리에서 선택한 시간 범위 보다 크거나 같은 값으로 설정 해야 합니다. 이 쿼리는 **Timegenerated** 열을 사용 하지 않으므로 **쿼리에서 설정** 옵션을 사용할 수 없습니다. 시간 범위에 대 한 자세한 내용은 [쿼리 범위](../log-query/scope.md) 를 참조 하세요. 

**시간 범위**에 대해 **마지막 4 시간** 을 선택 합니다. 이렇게 하면 수집 시간이 **Timegenerated** 보다 큰 모든 레코드가 결과에 포함 됩니다.
   
![쿼리 실행 및 결과 시각화 라는 새 Azure Monitor 로그 동작에 대 한 설정의 스크린샷](media/logs-export-logicapp/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>JSON 구문 분석 작업 추가 (선택 사항)
**쿼리 실행 및 결과 목록** 작업의 출력은 JSON으로 형식이 지정 됩니다. 이 데이터를 구문 분석 하 고 **작성** 준비 작업의 일부로 조작할 수 있습니다. 

수신할 페이로드를 설명 하는 JSON 스키마를 제공할 수 있습니다. 이 디자이너는 이 스키마를 사용하여 JSON 콘텐츠를 구문 분석하고 JSON 콘텐츠의 속성을 나타내는 친숙한 토큰을 생성합니다. 그 후 이러한 속성을 논리 앱 워크플로 전체에서 간편하게 참조하고 사용할 수 있습니다. 


**+ 새 단계**를 클릭 한 다음 **+ 작업 추가**를 클릭 합니다. **작업 선택**아래에서 **json** 을 입력 하 고 **json 구문 분석**을 선택 합니다.

![JSON 작업 구문 분석 선택](media/logs-export-logicapp/select-parse-json.png)

**콘텐츠** 상자를 클릭 하 여 이전 작업의 값 목록을 표시 합니다. **쿼리 실행 및 결과 나열** 작업에서 **본문** 을 선택 합니다. 로그 쿼리의 출력입니다.

[![본문 선택](media/logs-export-logicapp/select-body.png)](media/logs-export-logicapp/select-body.png#lightbox)

5.  **샘플 페이로드를 사용 하 여 스키마 생성을**클릭 합니다. 로그 쿼리를 실행 하 고 샘플 페이로드에 사용할 출력을 복사 합니다.  예제 쿼리의 경우 다음 출력을 사용할 수 있습니다.


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![JSON 페이로드 구문 분석](media/logs-export-logicapp/parse-json-payload.png)

## <a name="add-the-compose-action"></a>작성 작업 추가
**작성** 작업은 구문 분석 된 JSON 출력을 사용 하 고 blob에 저장 해야 하는 개체를 만듭니다.

**+ 새 단계**를 클릭 한 다음 **+ 작업 추가**를 클릭 합니다. **작업 선택**아래에서 **작성** 을 입력 하 고 **작성** 작업을 선택 합니다.

![구성 작업 선택](media/logs-export-logicapp/select-compose.png)


**입력** 상자를 클릭 하 여 이전 작업의 값 목록을 표시 합니다. **JSON 구문 분석** 작업에서 **본문** 을 선택 합니다. 로그 쿼리에서 구문 분석 된 출력입니다.

[![작성 작업의 본문 선택](media/logs-export-logicapp/select-body-compose.png)](media/logs-export-logicapp/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>Blob 만들기 작업 추가
Blob 만들기 작업은 작성 된 JSON을 저장소에 기록 합니다.

**+ 새 단계**를 클릭 한 다음 **+ 작업 추가**를 클릭 합니다. **작업 선택**아래에서 **blob** 을 입력 한 다음 **blob 만들기** 작업을 선택 합니다.

![Blob 만들기 선택](media/logs-export-logicapp/select-create-blob.png)

**연결 이름** 에 저장소 계정에 대 한 연결의 이름을 입력 한 다음 **폴더 경로** 상자의 폴더 아이콘을 클릭 하 여 저장소 계정에서 컨테이너를 선택 합니다. 이전 작업의 값 목록을 보려면 **Blob 이름을** 클릭 합니다. **식** 을 클릭 하 고 시간 간격과 일치 하는 식을 입력 합니다. 매시간 실행 되는이 쿼리의 경우 다음 식은 이전 시간 마다 blob 이름을 설정 합니다. 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![Blob 식](media/logs-export-logicapp/blob-expression.png)](media/logs-export-logicapp/blob-expression.png#lightbox)

**Blob 콘텐츠** 상자를 클릭 하 여 이전 작업의 값 목록을 표시 한 다음 **작성** 섹션에서 **출력** 을 선택 합니다.


![Blob 식 만들기](media/logs-export-logicapp/create-blob.png)


## <a name="test-the-logic-app"></a>Logic Apps 테스트
**실행**을 클릭 하 여 워크플로를 테스트 합니다. 워크플로에 오류가 있으면 해당 단계에 문제가 있는 것으로 표시 됩니다. 실행을 확인 하 고 각 단계를 드릴 하 여 입력 및 출력을 확인 하 고 오류를 조사할 수 있습니다. 필요한 경우 [Azure Logic Apps에서 워크플로 오류 문제 해결 및 진단](../../logic-apps/logic-apps-diagnosing-failures.md) 을 참조 하세요.

[![실행 기록](media/logs-export-logicapp/runs-history.png)](media/logs-export-logicapp/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>저장소에서 로그 보기
Azure Portal의 **저장소 계정** 메뉴로 이동 하 여 저장소 계정을 선택 합니다. **Blob 타일을** 클릭 하 고 blob 만들기 작업에서 지정한 컨테이너를 선택 합니다. Blob 중 하나를 선택 하 고 **blob을 편집**합니다.

[![Blob 데이터](media/logs-export-logicapp/blob-data.png)](media/logs-export-logicapp/blob-data.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [Azure Monitor의 로그 쿼리](../log-query/log-query-overview.md)에 대해 자세히 알아봅니다.
- [Logic Apps](../../logic-apps/index.yml) 에 대 한 자세한 정보
- [전원 자동화](https://flow.microsoft.com)에 대해 자세히 알아보세요.
