---
title: Azure Monitor를 사용하여 데이터 팩터리 모니터링
description: Azure Monitor를 사용 하 여 Data Factory 정보로 진단 로그를 사용 하도록 설정 하 여/Azure Data Factory 파이프라인을 모니터링 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: af274c9c50b514befb4a3ce5930877edf964d976
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638094"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Azure Monitor를 사용 하 여 Data Factory 모니터링 및 경고

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

클라우드 응용 프로그램은 복잡 하며 많은 부분을 이동 하 고 있습니다. 모니터는 응용 프로그램이 정상 상태로 유지 되 고 실행 되도록 하는 데 도움이 되는 데이터를 제공 합니다. 모니터는 잠재적인 문제를 방지 하 고 이전 문제를 해결 하는 데도 도움이 됩니다. 모니터링 데이터를 사용 하 여 응용 프로그램에 대 한 깊은 통찰력을 얻을 수 있습니다. 이 정보는 응용 프로그램 성능 및 유지 관리를 개선 하는 데 도움이 됩니다. 또한 수동 개입이 필요한 작업을 자동화 하는 데 도움이 됩니다.

Azure Monitor는 대부분의 Azure 서비스에 대 한 기본 수준의 인프라 메트릭과 로그를 제공 합니다. Azure 진단 로그는 리소스에서 내보내며, 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공합니다. ADF (Azure Data Factory)는 Azure Monitor에서 진단 로그를 쓸 수 있습니다. 7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

자세한 내용은 [Azure Monitor 개요](../azure-monitor/overview.md)를 참조하세요.

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Azure Data Factory 메트릭과 파이프라인 실행 데이터 유지

Data Factory은 파이프라인 실행 데이터를 45 일간만 저장 합니다. 데이터를 더 오랜 시간 동안 유지 하려면 Azure Monitor을 사용 합니다. 모니터를 사용 하면 분석을 위해 진단 로그를 서로 다른 여러 대상으로 라우팅할 수 있습니다.

* **Storage 계정** : 감사 또는 수동 검사를 위해 저장소 계정에 진단 로그를 저장 합니다. 진단 설정을 사용 하 여 보존 기간 (일)을 지정할 수 있습니다.
* **이벤트 허브** : 로그를 Azure Event Hubs로 스트리밍합니다. 로그는 Power BI와 같은 파트너 서비스/사용자 지정 분석 솔루션에 대 한 입력이 됩니다.
* **Log Analytics** : Log Analytics를 사용 하 여 로그를 분석 합니다. Azure Monitor와 Data Factory 통합은 다음 시나리오에서 유용 합니다.
  * Data Factory 모니터링을 위해 게시 되는 다양 한 메트릭 집합에 복잡 한 쿼리를 작성 하려고 합니다. 모니터를 통해 이러한 쿼리에 대 한 사용자 지정 경고를 만들 수 있습니다.
  * 데이터 팩터리를 모니터링하려고 합니다. 여러 데이터 팩터리에서 데이터를 단일 모니터 작업 영역으로 라우팅할 수 있습니다.

로그를 내보내는 리소스의 구독에 없는 저장소 계정 또는 이벤트 허브 네임 스페이스를 사용할 수도 있습니다. 설정을 구성 하는 사용자에 게 두 구독에 대 한 적절 한 Azure RBAC (역할 기반 액세스 제어) 액세스 권한이 있어야 합니다.

## <a name="configure-diagnostic-settings-and-workspace"></a>진단 설정 및 작업 영역 구성

데이터 팩터리에 대 한 진단 설정을 만들거나 추가 합니다.

1. 포털에서 모니터로 이동 합니다. **설정**  >  **진단 설정** 을 선택 합니다.

1. 진단 설정을 지정할 데이터 팩터리를 선택 합니다.

1. 선택한 데이터 팩터리에 설정이 없는 경우 설정을 만들라는 메시지가 표시 됩니다. **진단 켜기** 를 선택합니다.

   ![설정이 없는 경우 진단 설정 만들기](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Data factory에 기존 설정이 있으면 데이터 팩터리에 이미 구성 된 설정 목록이 표시 됩니다. **진단 설정 추가** 를 선택 합니다.

   ![설정이 있는 경우 진단 설정 추가](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 설정에 이름을 지정 하 고 **Log Analytics 보내기** 를 선택한 다음 **Log Analytics 작업 영역** 에서 작업 영역을 선택 합니다.

    * _Azure 진단_ 모드에서 진단 로그는 _azurediagnostics_ 테이블로 흐릅니다.

    * _리소스 관련_ 모드에서는 Azure Data Factory의 진단 로그가 다음 테이블로 흐릅니다.
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Log Analytics 테이블로 보낼 작업과 관련 된 다양 한 로그를 선택할 수 있습니다. 예를 들어 SQL Server Integration Services (SSIS)를 사용 하지 않는 경우 SSIS 로그를 선택할 필요가 없습니다. SSIS Integration Runtime (IR) 시작/중지/유지 관리 작업을 기록 하려는 경우 SSIS IR 로그를 선택할 수 있습니다. SSMS (SQL Server Management Studio), SQL Server 에이전트 또는 기타 지정 된 도구를 통해 SSIS 패키지 실행을 호출 하는 경우 SSIS 패키지 로그를 선택할 수 있습니다. ADF 파이프라인에서 SSIS 패키지 실행 작업을 통해 SSIS 패키지 실행을 호출 하는 경우 모든 로그를 선택할 수 있습니다.

    * _Allmetrics_ 을 선택 하는 경우 adf 작업, 파이프라인 및 트리거 실행의 메트릭과 ssis IR 작업 및 ssis 패키지 실행을 비롯 하 여 다양 한 adf 메트릭을 사용 하 여 경고를 모니터링 하거나 발생 시킬 수 있습니다.

   ![설정의 이름을로 설정 하 고 log analytics 작업 영역을 선택 합니다.](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Azure 로그 테이블에는 500 개 이상의 열을 사용할 수 없기 때문에 _리소스 특정 모드_ 를 선택 하 **는 것이 좋습니다** . 자세한 내용은 [Log Analytics 알려진 제한 사항](../azure-monitor/platform/resource-logs.md#column-limit-in-azurediagnostics)을 참조 하세요.

1. **저장** 을 선택합니다.

몇 분 후에이 데이터 팩터리에 대 한 설정 목록에 새 설정이 표시 됩니다. 진단 로그는 새 이벤트 데이터가 생성 되는 즉시 해당 작업 영역으로 스트리밍됩니다. 이벤트가 내보내지는 때와 Log Analytics에 표시 될 때까지 최대 15 분이 경과 될 수 있습니다.

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Azure Marketplace에서 Azure Data Factory 분석 솔루션 설치

이 솔루션은 세부 정보를 자세히 설명 하 고 예기치 않은 동작 패턴의 문제를 해결 하는 옵션과 함께 Data Factory의 전반적인 상태에 대 한 요약을 제공 합니다. 다양 한 기본 보기를 사용 하 여 다음을 비롯 한 주요 처리에 대 한 통찰력을 얻을 수 있습니다.

* 데이터 팩터리 파이프라인, 활동 및 트리거 실행에 대 한 간략 한 요약
* 유형별 데이터 팩터리 작업 실행 기능
* 데이터 팩터리 상위 파이프라인, 작업 오류 요약

1. **Azure Marketplace** 로 이동 하 여 **분석** 필터를 선택 하 고 **Azure Data Factory 분석 (미리 보기)** 을 검색 합니다.

   !["Azure Marketplace"으로 이동 하 여 "Analytics 필터"를 입력 하 고 "Azure Data Factory 분석 (미리 보기)"을 선택 합니다.](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. **Azure Data Factory 분석 (미리 보기)** 에 대 한 세부 정보

   !["Azure Data Factory 분석 (미리 보기)"에 대 한 세부 정보](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. **만들기** 를 선택 하 고 **Log Analytics 작업 영역** 을 만들거나 선택 합니다.

   ![새 솔루션 만들기](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory 메트릭 모니터링

이 솔루션을 설치 하면 선택한 Log Analytics 작업 영역의 통합 문서 섹션 내에 기본 보기 집합이 만들어집니다. 그 결과 다음과 같은 메트릭이 활성화 됩니다.

* ADF 실행-1) Data Factory에의 한 파이프라인 실행
* ADF 실행-2) Data Factory에의 한 작업 실행
* ADF 실행-3) Data Factory에서 트리거 실행
* ADF 오류-1) Data Factory 별 상위 10 개의 파이프라인 오류
* ADF 오류-2) Data Factory에서 상위 10 개의 작업 실행
* ADF 오류-3) Data Factory 별 상위 10 개 트리거 오류
* ADF 통계-1) 유형별 작업 실행
* ADF 통계-2) 유형별 트리거 실행
* ADF 통계-3) 최대 파이프라인 실행 기간

!["통합 문서 (미리 보기)" 및 "AzureDataFactoryAnalytics"가 강조 표시 된 창](media/data-factory-monitor-oms/monitor-oms-image6.png)

위의 메트릭을 시각화 하 고, 이러한 메트릭 뒤의 쿼리를 살펴보고, 쿼리를 편집 하 고, 경고를 만들고, 다른 작업을 수행할 수 있습니다.

![데이터 팩터리에서 파이프라인 실행의 그래픽 표현 "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory 분석 (미리 보기) _은 리소스 관련_ 대상 테이블에 진단 로그를 보냅니다. _ADFPipelineRun_ , _ADFTriggerRun_ 및 _adfactivityrun_ 테이블에 대해 쿼리를 작성할 수 있습니다.

## <a name="data-factory-metrics"></a>Data Factory 메트릭

모니터를 사용 하 여 Azure 워크 로드의 성능 및 상태를 파악할 수 있습니다. 가장 중요 한 유형의 모니터 데이터는 메트릭 (성능 카운터 라고도 함)입니다. 메트릭은 대부분의 Azure 리소스에서 내보내집니다. 모니터는 모니터링 및 문제 해결을 위해 이러한 메트릭을 구성 하 고 사용 하는 여러 가지 방법을 제공 합니다.

Azure Data Factory 버전 2에서 내보내는 메트릭은 다음과 같습니다.

| **메트릭**                           | **메트릭 표시 이름**                  | **단위** | **집계 유형** | **설명**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | 취소된 활동 실행 메트릭           | 개수    | 합계                | 1 분 기간 내에 취소 된 작업 실행의 총 수입니다. |
| ActivityFailedRuns                   | 실패한 활동 실행 메트릭             | 개수    | 합계                | 1 분 기간 내에 실패 한 작업 실행의 총 수입니다. |
| ActivitySucceededRuns                | 성공한 활동 실행 메트릭          | 개수    | 합계                | 1 분 기간 내에 성공한 총 활동 실행 수입니다. |
| PipelineCancelledRuns                 | 취소된 파이프라인 실행 메트릭           | 개수    | 합계                | 1 분 기간 내에 취소 된 총 파이프라인 실행 수입니다. |
| PipelineFailedRuns                   | 실패한 파이프라인 실행 메트릭             | 개수    | 합계                | 1 분 기간 내에 실패 한 총 파이프라인 실행 수입니다. |
| PipelineSucceededRuns                | 성공한 파이프라인 실행 메트릭          | 개수    | 합계                | 1 분 기간 내에 성공한 총 파이프라인 실행 수입니다. |
| TriggerCancelledRuns                  | 취소된 트리거 실행 메트릭            | 개수    | 합계                | 1 분 기간 내에 취소 된 총 트리거 실행 수입니다. |
| TriggerFailedRuns                    | 실패한 트리거 실행 메트릭              | 개수    | 합계                | 1 분 기간 내에 실패 한 총 트리거 실행 수입니다. |
| TriggerSucceededRuns                 | 성공한 트리거 실행 메트릭           | 개수    | 합계                | 1 분 기간 내에 성공한 총 트리거 실행 수입니다. |
| SSISIntegrationRuntimeStartCancelled  | SSIS 통합 런타임 시작 메트릭을 취소 했습니다.           | 개수    | 합계                | 1 분 기간 내에 취소 된 총 SSIS 통합 런타임 수입니다. |
| SSISIntegrationRuntimeStartFailed    | SSIS 통합 런타임 시작 메트릭이 실패 했습니다.             | 개수    | 합계                | 총 SSIS 통합 런타임 수가 분 기간 내에 실패 한 것으로 시작 합니다. |
| SSISIntegrationRuntimeStartSucceeded | SSIS 통합 런타임 시작 메트릭 성공          | 개수    | 합계                | 총 SSIS 통합 런타임 수가 1 분 이내에 성공 했습니다. |
| SSISIntegrationRuntimeStopStuck      | SSIS 통합 런타임 중지 메트릭 중지               | 개수    | 합계                | 분 기간 내에 중단 된 총 SSIS 통합 런타임 수입니다. |
| SSISIntegrationRuntimeStopSucceeded  | SSIS 통합 런타임 중지 메트릭 성공           | 개수    | 합계                | 1 분 기간 내에 성공한 총 SSIS 통합 런타임 수입니다. |
| SSISPackageExecutionCancelled         | 취소 한 SSIS 패키지 실행 메트릭  | 개수    | 합계                | 1 분 기간 내에 취소 된 SSIS 패키지 실행의 총 수입니다. |
| SSISPackageExecutionFailed           | SSIS 패키지 실행 메트릭이 실패 했습니다.    | 개수    | 합계                | 1 분 기간 내에 실패 한 총 SSIS 패키지 실행 수입니다. |
| SSISPackageExecutionSucceeded        | SSIS 패키지 실행 메트릭 성공 | 개수    | 합계                | 1 분 기간 내에 성공한 총 SSIS 패키지 실행 수입니다. |

메트릭에 액세스 하려면 [Azure Monitor data platform](../azure-monitor/platform/data-platform.md)의 지침을 완료 합니다.

> [!NOTE]
> 완료, 트리거된 활동 및 파이프라인 실행의 이벤트만 내보내집니다. 진행 중이 고 디버그 실행은 내보내지지 **않습니다** . 반면, **모든** SSIS 패키지 실행의 이벤트는 호출 메서드에 관계 없이 완료 되 고 진행 중인 이벤트를 포함 하 여 내보내집니다. 예를 들어 ADF 파이프라인에서 SSIS 패키지 실행 작업의 트리거된 실행 또는 디버그 실행으로, SSMS의 T-sql, SQL Server 에이전트 또는 기타 지정 된 도구를 사용 하 여 SSDT (Azure 사용 SQL Server Data Tools)에서 패키지 실행을 호출할 수 있습니다.

## <a name="data-factory-alerts"></a>Data Factory 경고

Azure Portal에 로그인 하 고 **Monitor**  >  **경고** 모니터링을 선택 하 여 경고를 만듭니다.

![포털 메뉴의 경고](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>경고 만들기

1. **+ 새로운 경고 규칙** 을 선택하여 새 경고를 만듭니다.

    ![새 경고 규칙](media/monitor-using-azure-monitor/alerts_image4.png)

1. 경고 조건을 정의 합니다.

    > [!NOTE]
    > **리소스 유형별 필터** 드롭다운 목록에서 **모두** 를 선택 해야 합니다.

    !["Select target" > "경고 조건 정의"를 선택 하면 "리소스 선택" 창이 열립니다. ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["경고 조건 정의" > "조건 추가"를 통해 "신호 논리 구성" 창이 열립니다.](media/monitor-using-azure-monitor/alerts_image6.png)

    !["신호 유형 구성" 창](media/monitor-using-azure-monitor/alerts_image7.png)

1. 경고 세부 정보를 정의합니다.

    ![경고 세부 정보](media/monitor-using-azure-monitor/alerts_image8.png)

1. 작업 그룹을 정의 합니다.

    !["새 작업 그룹"이 강조 표시 된 규칙 만들기](media/monitor-using-azure-monitor/alerts_image9.png)

    ![새 작업 그룹 만들기](media/monitor-using-azure-monitor/alerts_image10.png)

    ![전자 메일, SMS, 푸시 및 음성 구성](media/monitor-using-azure-monitor/alerts_image11.png)

    ![작업 그룹 정의](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Monitor REST API를 통해 진단 로그를 설정 합니다.

### <a name="diagnostic-settings"></a>진단 설정

진단 설정을 사용 하 여 비 계산 리소스에 대 한 진단 로그를 구성 합니다. 리소스 컨트롤에 대 한 설정에는 다음과 같은 기능이 있습니다.

* 진단 로그를 보낼 위치를 지정 합니다. 예를 들면 Azure storage 계정, Azure 이벤트 허브 또는 모니터 로그가 있습니다.
* 전송 되는 로그 범주를 지정 합니다.
* 각 로그 범주가 저장소 계정에 유지 되는 기간을 지정 합니다.
* 보존 기간이 0일이면 로그가 영구적으로 유지됩니다. 그렇지 않은 경우 값은 1에서 2147483647 사이의 모든 일 수 있습니다.
* 보존 정책이 설정 되었지만 저장소 계정에 로그를 저장 하지 않은 경우 보존 정책은 적용 되지 않습니다. 예를 들어 Event Hubs 또는 모니터 로그 옵션만 선택한 경우이 문제가 발생할 수 있습니다.
* 보존 정책은 하루에 적용 됩니다. 일 사이의 경계는 UTC (협정 세계시) 자정에 발생 합니다. 하루 끝에 보존 정책을 초과 하는 일의 로그가 삭제 됩니다. 예를 들어 1 일의 보존 정책이 있는 경우 오늘부터 어제 까지의 로그를 삭제 합니다.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Monitor REST API를 통해 진단 로그를 사용 하도록 설정

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>모니터 REST API에서 진단 설정을 만들거나 업데이트 합니다.

##### <a name="request"></a>요청

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>헤더

* `{api-version}`을 `2016-09-01`로 바꿉니다.
* `{resource-id}`진단 설정을 편집 하려는 리소스의 ID로 대체 합니다. 자세한 내용은 [리소스 그룹을 사용 하 여 Azure 리소스 관리](../azure-resource-manager/management/manage-resource-groups-portal.md)를 참조 하세요.
* `Content-Type` 헤더를 `application/json`으로 설정합니다.
* 권한 부여 헤더를 Azure Active Directory (Azure AD)에서 가져온 JSON 웹 토큰으로 설정 합니다. 자세한 내용은 [요청 인증](../active-directory/develop/authentication-vs-authorization.md)을 참조 하세요.

##### <a name="body"></a>본문

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| 속성 | Type | Description |
| --- | --- | --- |
| **storageAccountId** |String | 진단 로그를 보낼 저장소 계정의 리소스 ID입니다. |
| **serviceBusRuleId** |String | 진단 로그 스트리밍을 위해 Event Hubs 생성 하려는 service bus 네임 스페이스의 service bus 규칙 ID입니다. 규칙 ID의 형식은 `{service bus resource ID}/authorizationrules/{key name}` 입니다.|
| **workspaceId** | 복합 형식 | 메트릭 시간 조직 및 해당 보존 정책의 배열입니다. 이 속성의 값이 비어 있습니다. |
|**메트릭**| 호출된 파이프라인에 전달될 파이프라인 실행의 매개 변수 값| 매개 변수 이름을 인수 값에 매핑하는 JSON 개체입니다. |
| **로그온**| 복합 형식| 리소스 종류에 대 한 진단 로그 범주의 이름입니다. 리소스에 대 한 진단 로그 범주의 목록을 가져오려면 진단 설정 가져오기 작업을 수행 합니다. |
| **category**| String| 로그 범주 및 해당 보존 정책의 배열입니다. |
| **timeGrain** | String | ISO 8601 기간 형식으로 캡처된 메트릭의 세분성입니다. 속성 값은 `PT1M` 1 분을 지정 하는 이어야 합니다. |
| **사용**| 부울 | 이 리소스에 대해 메트릭 또는 로그 범주의 컬렉션을 사용할 수 있는지 여부를 지정 합니다. |
| **retentionPolicy**| 복합 형식| 메트릭 또는 로그 범주에 대한 보존 정책을 설명합니다. 이 속성은 저장소 계정에만 사용 됩니다. |
|**일별로**| Int| 메트릭 또는 로그를 유지할 일 수입니다. 속성 값이 0 이면 로그가 영구적으로 유지 됩니다. 이 속성은 저장소 계정에만 사용 됩니다. |

##### <a name="response"></a>응답

200 정상.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>모니터의 진단 설정에 대 한 정보를 가져옵니다 REST API

##### <a name="request"></a>요청

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>헤더

* `{api-version}`을 `2016-09-01`로 바꿉니다.
* `{resource-id}`진단 설정을 편집 하려는 리소스의 ID로 대체 합니다. 자세한 내용은 [리소스 그룹을 사용 하 여 Azure 리소스 관리](../azure-resource-manager/management/manage-resource-groups-portal.md)를 참조 하세요.
* `Content-Type` 헤더를 `application/json`으로 설정합니다.
* 권한 부여 헤더를 Azure AD에서 가져온 JSON 웹 토큰으로 설정 합니다. 자세한 내용은 [요청 인증](../active-directory/develop/authentication-vs-authorization.md)을 참조 하세요.

##### <a name="response"></a>응답

200 정상.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
자세한 내용은 [진단 설정](/rest/api/monitor/diagnosticsettings)을 참조 하세요.

## <a name="schema-of-logs-and-events"></a>로그 및 이벤트의 스키마

### <a name="monitor-schema"></a>모니터 스키마

#### <a name="activity-run-log-attributes"></a>작업-실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| 속성 | Type | Description | 예제 |
| --- | --- | --- | --- |
| **수준** |String | 진단 로그의 수준입니다. 활동 실행 로그에 대해 속성 값을 4로 설정 합니다. | `4` |
| **correlationId** |String | 특정 요청을 추적 하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Timespan UTC 형식의 이벤트 시간 `YYYY-MM-DDTHH:MM:SS.00000Z` 입니다. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| 작업 실행의 ID입니다. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| 파이프라인 실행의 ID입니다. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 데이터 팩터리 리소스와 연결 된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 진단 로그의 범주입니다. 속성 값을 `ActivityRuns`로 설정합니다. | `ActivityRuns` |
|**level**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**operationName**| String | 상태의 작업 이름입니다. 활동이 시작 하트 비트 이면 속성 값은 `MyActivity -` 입니다. 활동이 끝 하트 비트 이면 속성 값은 `MyActivity - Succeeded` 입니다. | `MyActivity - Succeeded` |
|**pipelineName**| String | 파이프라인의 이름 | `MyPipeline` |
|**activityName**| String | 작업의 이름입니다. | `MyActivity` |
|**start**| String | 작업의 시작 시간은 timespan UTC 형식으로 실행 됩니다. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | 활동의 종료 시간은 timespan UTC 형식으로 실행 됩니다. 진단 로그에 작업이 시작 되었지만 아직 종료 되지 않은 것으로 표시 되는 경우 속성 값은 `1601-01-01T00:00:00Z` 입니다. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>파이프라인-실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| 속성 | Type | Description | 예제 |
| --- | --- | --- | --- |
| **수준** |String | 진단 로그의 수준입니다. 활동 실행 로그에 대해 속성 값을 4로 설정 합니다. | `4` |
| **correlationId** |String | 특정 요청을 추적 하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Timespan UTC 형식의 이벤트 시간 `YYYY-MM-DDTHH:MM:SS.00000Z` 입니다. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| 파이프라인 실행의 ID입니다. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 데이터 팩터리 리소스와 연결 된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 진단 로그의 범주입니다. 속성 값을 `PipelineRuns`로 설정합니다. | `PipelineRuns` |
|**level**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**operationName**| String | 상태와 함께 파이프라인 이름입니다. 파이프라인 실행이 완료 되 면 속성 값은 `Pipeline - Succeeded` 입니다. | `MyPipeline - Succeeded`. |
|**pipelineName**| String | 파이프라인의 이름 | `MyPipeline` |
|**start**| String | 작업의 시작 시간은 timespan UTC 형식으로 실행 됩니다. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | 활동의 종료 시간은 timespan UTC 형식으로 실행 됩니다. 진단 로그에 시작 되었지만 아직 종료 되지 않은 작업이 표시 되는 경우 속성 값은 `1601-01-01T00:00:00Z` 입니다.  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | 파이프라인 실행의 최종 상태입니다. 가능한 속성 값은 `Succeeded` 및 `Failed` 입니다. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>트리거-실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| 속성 | Type | Description | 예제 |
| --- | --- | --- | --- |
| **수준** |String | 진단 로그의 수준입니다. 활동 실행 로그에 대해 속성 값을 4로 설정 합니다. | `4` |
| **correlationId** |String | 특정 요청을 추적 하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Timespan UTC 형식의 이벤트 시간 `YYYY-MM-DDTHH:MM:SS.00000Z` 입니다. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| 트리거의 실행 ID입니다. | `08587023010602533858661257311` |
|**resourceId**| String | 데이터 팩터리 리소스와 연결 된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 진단 로그의 범주입니다. 속성 값을 `PipelineRuns`로 설정합니다. | `PipelineRuns` |
|**level**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**operationName**| String | 트리거가 성공적으로 실행 되었는지 여부를 나타내는 최종 상태의 트리거의 이름입니다. 하트 비트가 성공적으로 완료 되 면 속성 값은 `MyTrigger - Succeeded` 입니다. | `MyTrigger - Succeeded` |
|**triggerName**| String | 트리거의 이름입니다. | `MyTrigger` |
|**triggerType**| String | 트리거의 유형입니다. 가능한 속성 값은 `Manual Trigger` 및 `Schedule Trigger` 입니다. | `ScheduleTrigger` |
|**triggerEvent**| String | 트리거의 이벤트입니다. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | Timespan UTC 형식의 트리거 시작 시간입니다. | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | 트리거가 성공적으로 실행 되었는지 여부를 보여 주는 최종 상태입니다. 가능한 속성 값은 `Succeeded` 및 `Failed` 입니다. | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>SSIS 통합 런타임 로그 특성

SSIS IR 시작/중지/유지 관리 작업의 로그 특성은 다음과 같습니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| 속성                   | Type   | Description                                                   | 예제                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 이벤트 시간 (UTC 형식): `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | SSIS IR 작업의 이름                            | `Start/Stop/Maintenance` |
| **category**               | String | 진단 로그의 범주입니다.                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | 특정 작업을 추적 하기 위한 고유 ID             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | ADF의 이름                                          | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                      | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                  | `Informational` |
| **resultType**             | String | SSIS IR 작업의 결과입니다.                          | `Started/InProgress/Succeeded/Failed` |
| **message**                | String | SSIS IR 작업의 출력 메시지                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | String | ADF 리소스의 고유 ID                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>SSIS 이벤트 메시지 컨텍스트 로그 특성

Ssis IR에서 SSIS 패키지 실행에 의해 생성 되는 이벤트 메시지와 관련 된 조건의 로그 특성은 다음과 같습니다. 이러한 속성은 여러 SSIS 패키지 속성의 런타임 값을 보여 주는 [SSISDB (ssis 카탈로그) 이벤트 메시지 컨텍스트 테이블 또는 뷰와](/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15) 유사한 정보를 전달 합니다. 로깅 수준을 선택 하면 생성 되 `Basic/Verbose` 고 디버깅/준수 검사에 유용 합니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| 속성                   | Type   | Description                                                          | 예제                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 이벤트 시간 (UTC 형식): `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 이는로 설정 됩니다. `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | String | 진단 로그의 범주입니다.                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | 특정 작업을 추적 하기 위한 고유 ID                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF의 이름                                                 | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                             | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                         | `Informational` |
| **operationId**            | String | SSISDB에서 특정 작업을 추적 하기 위한 고유 ID          | `1` (1은 T-SQL을 통해 SSISDB/호출 **된 패키지와** 관련 된 작업을 나타냅니다.) |
| **contextDepth**           | String | 이벤트 메시지 컨텍스트의 깊이입니다.                              | `0` (0은 패키지 실행이 시작 되기 전에 컨텍스트를 나타내고, 1은 오류가 발생 했을 때 컨텍스트를 나타내고, 컨텍스트는 오류에서 추가 될 때 더 커집니다.) |
| **packagePath**            | String | 이벤트 메시지 컨텍스트 원본인 패키지 개체의 경로입니다.      | `\Package` |
| **contextType**            | String | 이벤트 메시지 컨텍스트 원본인 패키지 개체의 유형입니다.      | `60`( [추가 컨텍스트 형식](/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15#remarks)참조) |
| **contextSourceName**      | String | 이벤트 메시지 컨텍스트 원본인 패키지 개체의 이름입니다.      | `MyPackage` |
| **contextSourceId**        | String | 이벤트 메시지 컨텍스트 원본인 패키지 개체의 고유 ID | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | String | 이벤트 메시지 컨텍스트 소스에 대 한 패키지 속성의 이름입니다.   | `DelayValidation` |
| **propertyValue**          | String | 이벤트 메시지 컨텍스트 원본의 package 속성 값  | `False` |
| **resourceId**             | String | ADF 리소스의 고유 ID                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>SSIS 이벤트 메시지 로그 특성

Ssis IR에서 SSIS 패키지 실행에 의해 생성 되는 이벤트 메시지의 로그 특성은 다음과 같습니다. 이벤트 메시지의 자세한 텍스트/메타 데이터를 보여 주는 [SSISDB 이벤트 메시지 테이블 또는 뷰와](/sql/integration-services/system-views/catalog-event-messages?view=sql-server-ver15) 유사한 정보를 전달 합니다. 을 제외한 모든 로깅 수준에서 생성 `None` 됩니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| 속성                   | Type   | Description                                                        | 예제                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | String | 이벤트 시간 (UTC 형식): `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 이는로 설정 됩니다. `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | String | 진단 로그의 범주입니다.                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | 특정 작업을 추적 하기 위한 고유 ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF의 이름                                               | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                           | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                       | `Informational` |
| **operationId**            | String | SSISDB에서 특정 작업을 추적 하기 위한 고유 ID        | `1` (1은 T-SQL을 통해 SSISDB/호출 **된 패키지와** 관련 된 작업을 나타냅니다.) |
| **messageTime**            | String | 이벤트 메시지가 UTC 형식으로 생성 되는 시간입니다.          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | String | 이벤트 메시지의 형식입니다.                                     | `70`( [자세한 메시지 유형](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks)참조) |
| **messageSourceType**      | String | 이벤트 메시지 원본의 형식입니다.                              | `20`( [자세한 메시지 원본 유형](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks)참조) |
| **message**                | String | 이벤트 메시지의 텍스트입니다.                                     | `MyPackage:Validation has started.` |
| **packageName**            | String | 실행 된 패키지 파일의 이름입니다.                             | `MyPackage.dtsx` |
| **eventName**              | String | 관련 런타임 이벤트의 이름입니다.                                 | `OnPreValidate` |
| **messageSourceName**      | String | 이벤트 메시지 원본인 패키지 구성 요소의 이름입니다.         | `Data Flow Task` |
| **messageSourceId**        | String | 이벤트 메시지 원본인 패키지 구성 요소의 고유 ID    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | String | 이벤트 메시지 원본인 데이터 흐름 구성 요소의 이름입니다.       | `SSIS.Pipeline` |
| **packagePath**            | String | 이벤트 메시지 원본인 패키지 개체의 경로입니다.            | `\Package\Data Flow Task` |
| **executionPath**          | String | 부모 패키지에서 실행 된 구성 요소로의 전체 경로입니다.            | `\Transformation\Data Flow Task` (이 경로는 구성 요소 반복도 캡처합니다.) |
| **threadId**               | String | 이벤트 메시지가 기록 될 때 실행 되는 스레드의 고유 ID | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>SSIS 실행 파일 통계 로그 특성

Ssis IR에서 SSIS 패키지 실행에 의해 생성 되는 실행 가능한 통계의 로그 특성은 다음과 같습니다. 여기서 실행 파일은 패키지 제어 흐름의 컨테이너 또는 작업입니다. 이러한 테이블은 실행 중인 각 실행 파일에 대 한 행을 보여 주는 [SSISDB 실행 파일 통계 테이블 또는 뷰와](/sql/integration-services/system-views/catalog-executable-statistics?view=sql-server-ver15) 유사한 정보를 해당 반복을 포함 하 여 전달 합니다. 이러한 `None` 오류는 작업 수준 병목 상태를 식별 하는 데 유용 하며 모든 로깅 수준에서 생성 됩니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| 속성                   | Type   | Description                                                      | 예제                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 이벤트 시간 (UTC 형식): `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 이는로 설정 됩니다. `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | String | 진단 로그의 범주입니다.                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | 특정 작업을 추적 하기 위한 고유 ID                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF의 이름                                             | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                         | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                     | `Informational` |
| **executionId**            | String | SSISDB에서 특정 실행을 추적 하기 위한 고유 ID      | `1` (1은 T-SQL을 통해 SSISDB/호출 **된 패키지와** 관련 된 실행을 나타냅니다.) |
| **executionPath**          | String | 부모 패키지에서 실행 된 구성 요소로의 전체 경로입니다.          | `\Transformation\Data Flow Task` (이 경로는 구성 요소 반복도 캡처합니다.) |
| **startTime**              | String | 실행 파일이 UTC 형식으로 실행 전 단계로 전환 되는 시간입니다.  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 실행 파일이 실행 후 단계를 UTC 형식으로 입력 하는 시간 | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | String | 실행 파일의 실행 시간 (밀리초)                   | `1,125` |
| **executionResult**        | String | 실행 중인 실행 파일의 결과                                 | `0` 0은 성공, 1은 실패, 2는 완료를 나타내고, 3은 취소를 의미 합니다. |
| **executionValue**         | String | 실행 중인 실행 파일에서 반환 하는 사용자 정의 값입니다.            | `1` |
| **resourceId**             | String | ADF 리소스의 고유 ID                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>SSIS 실행 구성 요소 단계 로그 특성

Ssis IR에서 SSIS 패키지 실행에 의해 생성 되는 데이터 흐름 구성 요소에 대 한 런타임 통계의 로그 특성은 다음과 같습니다. 모든 실행 단계에서 데이터 흐름 구성 요소에 소요 된 시간을 보여 주는 [SSISDB 실행 구성 요소 단계 테이블 또는 뷰와](/sql/integration-services/system-views/catalog-execution-component-phases?view=sql-server-ver15) 유사한 정보를 전달 합니다. `Performance/Verbose`로깅 수준을 선택 하 고 데이터 흐름 실행 통계를 캡처하는 데 유용 하 게 사용할 때 생성 됩니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| 속성                   | Type   | Description                                                         | 예제                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | 이벤트 시간 (UTC 형식): `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | 이는로 설정 됩니다. `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | String | 진단 로그의 범주입니다.                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | 특정 작업을 추적 하기 위한 고유 ID                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | ADF의 이름                                                | `MyADFv2` |
| **integrationRuntimeName** | String | SSIS IR의 이름                                            | `MySSISIR` |
| **level**                  | String | 진단 로그의 수준                                        | `Informational` |
| **executionId**            | String | SSISDB에서 특정 실행을 추적 하기 위한 고유 ID         | `1` (1은 T-SQL을 통해 SSISDB/호출 **된 패키지와** 관련 된 실행을 나타냅니다.) |
| **packageName**            | String | 실행 된 패키지 파일의 이름입니다.                              | `MyPackage.dtsx` |
| **열의**               | String | 실행 된 데이터 흐름 태스크의 이름입니다.                                 | `Data Flow Task` |
| **subcomponentName**       | String | 데이터 흐름 구성 요소의 이름입니다.                                     | `Derived Column` |
| **단계**                  | String | 실행 단계의 이름입니다.                                         | `AcquireConnections` |
| **startTime**              | String | 실행 단계가 UTC 형식으로 시작 되는 시간                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | 실행 단계가 UTC 형식으로 끝나는 시간                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | String | 데이터 흐름 태스크에 대 한 실행 경로                            | `\Transformation\Data Flow Task` |
| **resourceId**             | String | ADF 리소스의 고유 ID                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>SSIS 실행 데이터 통계 로그 특성

다음은 SSIS IR에서 SSIS 패키지 실행에 의해 생성 되는 업스트림에서 다운스트림 구성 요소에 이르기까지 데이터 흐름 파이프라인의 각 레그를 통한 데이터 이동의 로그 특성입니다. 데이터 흐름 태스크를 통해 이동 된 데이터의 행 수를 보여 주는 [SSISDB 실행 데이터 통계 테이블 또는 뷰와](/sql/integration-services/system-views/catalog-execution-data-statistics?view=sql-server-ver15) 유사한 정보를 전달 합니다. 로깅 수준을 선택 하면 생성 되 `Verbose` 고 데이터 흐름 처리량을 계산 하는 데 유용 합니다.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| 속성                     | Type   | Description                                                        | 예제                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | String | 이벤트 시간 (UTC 형식): `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | 이는로 설정 됩니다. `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | String | 진단 로그의 범주입니다.                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | 특정 작업을 추적 하기 위한 고유 ID                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | ADF의 이름                                               | `MyADFv2` |
| **integrationRuntimeName**   | String | SSIS IR의 이름                                           | `MySSISIR` |
| **level**                    | String | 진단 로그의 수준                                       | `Informational` |
| **executionId**              | String | SSISDB에서 특정 실행을 추적 하기 위한 고유 ID        | `1` (1은 T-SQL을 통해 SSISDB/호출 **된 패키지와** 관련 된 실행을 나타냅니다.) |
| **packageName**              | String | 실행 된 패키지 파일의 이름입니다.                             | `MyPackage.dtsx` |
| **열의**                 | String | 실행 된 데이터 흐름 태스크의 이름입니다.                                | `Data Flow Task` |
| **dataflowPathIdString**     | String | 추적 데이터 흐름 경로에 대 한 고유 ID                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | String | 데이터 흐름 경로의 이름입니다.                                         | `ADO NET Source Output` |
| **sourceComponentName**      | String | 데이터를 전송 하는 데이터 흐름 구성 요소의 이름                    | `SQLDB Table3` |
| **destinationComponentName** | String | 데이터를 수신 하는 데이터 흐름 구성 요소의 이름                 | `Derived Column` |
| **rowsSent**                 | String | 원본 구성 요소에서 보낸 행 수                        | `500` |
| **createdTime**              | String | 행 값을 UTC 형식으로 가져오는 시간                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | String | 데이터 흐름 태스크에 대 한 실행 경로                           | `\Transformation\Data Flow Task` |
| **resourceId**               | String | ADF 리소스의 고유 ID                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Log Analytics 스키마

Log Analytics는 다음과 같은 예외를 제외 하 고 모니터에서 스키마를 상속 합니다.

* 각 열 이름의 첫 글자는 대문자로 되어 있습니다. 예를 들어 Monitor의 열 이름 "correlationId"는 Log Analytics의 "CorrelationId"입니다.
* "Level" 열이 없습니다.
* 동적 "속성" 열은 다음과 같은 동적 JSON blob 유형으로 유지 됩니다.

    | Azure Monitor 열 | Log Analytics 열 | Type |
    | --- | --- | --- |
    | $. 속성. UserProperties | UserProperties | 동적 |
    | $. 속성. 달 | 주석 | 동적 |
    | $. 속성. 입력 | 입력 | 동적 |
    | $. 속성. 출력 | 출력 | 동적 |
    | $. 속성. 오류입니다. errorCode | 오류 코드 | int |
    | $. 속성. 오류 메시지 | ErrorMessage | 문자열 |
    | $. 속성. 메시지가 | 오류 | 동적 |
    | $. 속성. 이전 | 이전 | 동적 |
    | $. 속성. 변수의 | 매개 변수 | 동적 |
    | $.properties.SystemParameters | SystemParameters | 동적 |
    | $. 속성. 사이 | 태그들 | 동적 |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>Azure Monitor를 사용 하 여 SSIS 작업 모니터링

SSIS 워크 로드를 리프트 & 이동 하려면 다음을 지 원하는 [ADF에서 SSIS IR을 프로 비전](./tutorial-deploy-ssis-packages-azure.md) 할 수 있습니다.

- Azure SQL Database 서버/Managed Instance(프로젝트 배포 모델)가 호스트하는 SSIS 카탈로그(SSISDB)에 배포된 패키지 실행
- Azure SQL Managed Instance(패키지 배포 모델)가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)에 배포된 패키지 실행

프로 비전 된 후에는 [Azure PowerShell를 사용 하거나 ADF 포털의 **모니터** 허브에서 SSIS IR 작동 상태를 확인할](./monitor-integration-runtime.md#azure-ssis-integration-runtime)수 있습니다. 프로젝트 배포 모델을 사용 하면 SSIS 패키지 실행 로그가 SSISDB 내부 테이블 또는 뷰에 저장 되므로 SSMS와 같은 지정 된 도구를 사용 하 여 쿼리, 분석 및 시각적으로 표시할 수 있습니다. 패키지 배포 모델을 사용 하면 SSIS 패키지 실행 로그를 파일 시스템에 저장 하거나 다른 지정 된 도구를 사용 하 여 구문 분석 하 고 처리 해야 하는 CSV 파일로 Azure Files 저장할 수 있습니다.

이제 [Azure Monitor](../azure-monitor/platform/data-platform.md) 통합을 사용 하 여 ssis IR 작업 및 AZURE PORTAL의 ssis 패키지 실행에서 생성 된 모든 메트릭과 로그를 쿼리, 분석 및 시각적으로 표시할 수 있습니다. 또한 경고를 발생 시킬 수도 있습니다.

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>SSIS 작업을 위한 진단 설정 및 작업 영역 구성

SSIS IR 작업 및 SSIS 패키지 실행에서 생성 된 모든 메트릭과 로그를 Azure Monitor 전송 하려면 [ADF에 대 한 진단 설정 및 작업 영역을 구성](#configure-diagnostic-settings-and-workspace)해야 합니다.

### <a name="ssis-operational-metrics"></a>SSIS 작업 메트릭

Ssis 운영 [메트릭은](../azure-monitor/platform/data-platform-metrics.md) ssis IR 시작 및 중지 작업의 상태와 특정 시점에서 ssis 패키지 실행의 상태를 설명 하는 숫자 값 및 성능 카운터입니다. [Azure Monitor의 ADF 메트릭에](#data-factory-metrics)속합니다.

Azure Monitor에서 ADF에 대 한 진단 설정 및 작업 영역을 구성 하는 경우 _Allmetrics_ 확인란을 선택 하면 azure 메트릭 탐색기, [azure 대시보드의 프레젠테이션](../azure-monitor/learn/tutorial-app-dashboards.md)및 [거의 실시간 경고](../azure-monitor/platform/alerts-metric.md)를 [사용 하 여 대화형 분석](../azure-monitor/platform/metrics-getting-started.md)에 사용할 수 있는 SSIS 작업 메트릭을 만듭니다.

![설정의 이름을로 설정 하 고 log analytics 작업 영역을 선택 합니다.](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>SSIS 작업 경고

ADF 포털에서 SSIS 작업 메트릭에 대 한 경고를 발생 시키려면 [Adf **모니터** 허브의 **경고 & 메트릭** 페이지를 선택 하 고 제공 된 단계별 지침을 따르세요](./monitor-visually.md#alerts).

![ADF 포털에서 SSIS 작업 경고 발생](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

Azure Portal에서 SSIS 작업 메트릭에 대 한 경고를 발생 시키려면 [Azure **Monitor** hub의 **경고** 페이지를 선택 하 고 제공 된 단계별 지침을 따르세요](#data-factory-alerts).

![Azure Portal에서 SSIS 작업 경고 발생](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>SSIS 작업 로그

SSIS 작업 [로그](../azure-monitor/platform/data-platform-logs.md) 는 식별 된 문제에 대 한 충분 한 컨텍스트를 제공 하는 ssis IR 작업 및 ssis 패키지 실행에 의해 생성 되는 이벤트 이며 근본 원인 분석에 유용 합니다. 

Azure Monitor에서 ADF에 대 한 진단 설정 및 작업 영역을 구성 하는 경우 관련 SSIS 작업 로그를 선택 하 여 Azure 데이터 탐색기을 기반으로 하는 Log Analytics으로 보낼 수 있습니다. 여기에서 풍부한 쿼리 언어, [Azure 대시보드의 프레젠테이션](../azure-monitor/learn/tutorial-app-dashboards.md)및 [거의 실시간 경고](../azure-monitor/platform/alerts-log.md)를 [사용 하 여 분석](../azure-monitor/log-query/log-query-overview.md)을 수행할 수 있습니다.

![설정의 이름을로 설정 하 고 log analytics 작업 영역을 선택 합니다.](media/data-factory-monitor-oms/monitor-oms-image2.png)

Azure Monitor 및 Log Analytics에서 SSIS 패키지 실행 로그의 스키마 및 내용은 SSISDB 내부 테이블 또는 뷰의 스키마와 비슷합니다.

| 로그 범주 Azure Monitor          | Log Analytics 테이블                     | SSISDB 내부 테이블/뷰              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

SSIS 작업 로그 특성/속성에 대 한 자세한 내용은 [ADF의 Azure Monitor 및 Log Analytics 스키마](#schema-of-logs-and-events)를 참조 하십시오.

선택한 SSIS 패키지 실행 로그는 호출 메서드에 관계 없이 항상 Log Analytics 전송 됩니다. 예를 들어 ADF 파이프라인에서 SSIS 패키지 실행 작업의 트리거된 실행 또는 디버그 실행으로, SQL Server 에이전트 또는 다른 지정 된 도구의 T-sql을 통해 Azure 지원 SSDT에서 패키지 실행을 호출할 수 있습니다.

로그 분석에서 SSIS IR 작업 로그를 쿼리할 때는 각각 및로 설정 된 **OperationName** 및 **ResultType** 속성을 사용할 수 있습니다 `Start/Stop/Maintenance` `Started/InProgress/Succeeded/Failed` . 

![Log Analytics에서 SSIS IR 작업 로그 쿼리](media/data-factory-monitor-oms/log-analytics-query.png)

로그 분석에서 SSIS 패키지 실행 로그를 쿼리할 때 **OperationId** / **executionid** / **CorrelationId** 속성을 사용 하 여 조인할 수 있습니다. **OperationId** / **ExecutionId** `1` T-SQL을 통해 호출 되는 SSISDB에 저장 **되지 않은** 패키지와 관련 된 모든 작업/실행의 경우 executionid는 항상로 설정 됩니다.

![Log Analytics에서 SSIS 패키지 실행 로그 쿼리](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>다음 단계
[프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)