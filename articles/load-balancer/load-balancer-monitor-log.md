---
title: 공용 기본 Load Balancer에 대한 작업, 이벤트 및 카운터 모니터링
titleSuffix: Azure Load Balancer
description: Azure Load Balancer에 대 한 로깅을 사용 하도록 설정 하는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572782"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>표준 Azure Load Balancer에 대 한 Azure Monitor 로그

Azure에서 다양 한 유형의 로그를 사용 하 여 표준 부하 분산 장치를 관리 하 고 문제를 해결할 수 있습니다. 로그는 이벤트 허브 또는 Log Analytics 작업 영역으로 스트리밍할 수 있습니다. Azure blob storage에서 모든 로그를 추출 하 고 Excel 및 Power BI와 같은 다른 도구에서 볼 수 있습니다.  아래 목록에서 다른 종류의 로그에 대해 자세히 알아볼 수 있습니다.

* **활동 로그:** [활동 로그 보기를 사용 하 여 리소스에 대 한 작업을 모니터링](../azure-resource-manager/management/view-activity-logs.md) 하 여 Azure 구독에 제출 되는 모든 활동 및 해당 상태를 볼 수 있습니다. 활동 로그는 기본적으로 사용 하도록 설정 되며 Azure Portal에서 볼 수 있습니다. 이러한 로그는 기본 및 표준 부하 분산 장치에 모두 사용할 수 있습니다.
* **표준 Load Balancer 메트릭:** 이 로그를 사용 하 여 표준 Azure Load Balancer에 대 한 로그로 내보낸 메트릭을 쿼리할 수 있습니다. 이러한 로그는 표준 부하 분산 장치에만 사용할 수 있습니다.

> [!IMPORTANT]
> **상태 프로브 및 Load Balancer 경고 이벤트 로그는 현재 작동 하지 않으며 [Azure Load Balancer에 대 한 알려진 문제](whats-new.md#known-issues)에 나열 됩니다.** 

> [!IMPORTANT]
> 로그는 Resource Manager 배포 모델에 배포된 리소스에서만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 배포 모델에 대한 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](../azure-resource-manager/management/deployment-models.md)를 참조하세요.

## <a name="enable-logging"></a>로깅 사용

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이벤트 및 상태 프로브 로깅을 사용 하 여 해당 로그를 통해 사용 가능한 데이터 수집을 시작 합니다. 로깅을 사용하려면 다음 단계를 사용합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다. 부하 분산 장치가 아직 없으면, 계속하기 전에 [부하 분산 장치를 만듭니다](./quickstart-load-balancer-standard-public-portal.md) .

1. 포털에서 **리소스 그룹** 을 클릭 합니다.
2. **\<resource-group-name>** 부하 분산 장치가 있는 위치를 선택 합니다.
3. 부하 분산 장치를 선택합니다.
4. **활동 로그**  >  **진단 설정** 을 선택 합니다.
5. **진단 설정** 창의 **진단 설정** 에서 **+ 진단 설정 추가** 를 선택 합니다.
6. **진단 설정** 만들기 창에서 **이름** 필드에 **myLBDiagnostics** 을 입력 합니다.
7. **진단 설정** 에 대 한 세 가지 옵션이 있습니다.  하나, 둘 또는 세 가지를 모두 선택 하 고 요구 사항에 맞게 구성할 수 있습니다.
   * **스토리지 계정에 보관**
   * **이벤트 허브로 스트림**
   * **Log Analytics에 보내기**

    ### <a name="archive-to-a-storage-account"></a>스토리지 계정에 보관
    이 프로세스에 대해 이미 만들어진 저장소 계정이 필요 합니다.  저장소 계정을 만들려면 [저장소 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal) 를 참조 하세요.

    1. **저장소 계정에 보관** 옆의 확인란을 선택 합니다.
    2. **구성** 을 선택 하 여 **저장소 계정 선택** 창을 엽니다.
    3. 풀 다운 상자에서 저장소 계정이 만들어진 **구독** 을 선택 합니다.
    4. 풀 다운 상자 **에서 저장소 계정 아래에** 있는 저장소 계정의 이름을 선택 합니다.
    5. 확인을 선택합니다.

    ### <a name="stream-to-an-event-hub"></a>이벤트 허브로 스트림
    이 프로세스에 대 한 이벤트 허브가 이미 생성 되어 있어야 합니다.  이벤트 허브를 만들려면 [빠른 시작: Azure Portal 사용 하 여 이벤트 허브 만들기](../event-hubs/event-hubs-create.md) 를 참조 하세요.

    1. **이벤트 허브로 스트림** 옆의 확인란을 선택 합니다.
    2. **구성** 을 선택 하 여 **이벤트 허브 선택** 창을 엽니다.
    3. 풀 다운 상자에서 이벤트 허브가 만들어진 **구독** 을 선택 합니다.
    4. 풀 다운 상자에서 **이벤트 허브 네임 스페이스를 선택** 합니다.
    5. 풀 다운 상자에서 **이벤트 허브 정책 이름을 선택** 합니다.
    6. 확인을 선택합니다.

    ### <a name="send-to-log-analytics"></a>Log Analytics에 보내기
    이 프로세스에 대 한 log analytics 작업 영역을 만들고 구성 해야 합니다.  Log Analytics 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace.md) 를 참조 하세요.

    1. **Log Analytics 보내기** 옆의 확인란을 선택 합니다.
    2. 풀 다운 상자에서 Log Analytics 작업 영역이 있는 **구독** 을 선택 합니다.
    3. 풀 다운 상자에서 **Log Analytics 작업 영역** 을 선택 합니다.


8.  **진단 설정** 창의 **메트릭** 섹션 아래에서 옆에 있는 확인란을 선택 **합니다.**

9. 모든 항목이 올바른지 확인 하 고 **진단 설정** 만들기 창의 맨 위에서 **저장** 을 클릭 합니다.

## <a name="activity-log"></a>활동 로그

활동 로그는 기본적으로 생성 됩니다. [이 문서의 지침](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)에 따라 구독 수준에서 내보내도록 구성할 수 있습니다. [작업 로그 보기에서 리소스에 대 한 작업 모니터링](../azure-resource-manager/management/view-activity-logs.md) 문서를 읽어 이러한 로그에 대해 자세히 알아보세요.

### <a name="view-and-analyze-the-activity-log"></a>활동 로그 보기 및 분석

다음 방법을 사용하여 활동 로그 데이터를 보고 분석할 수 있습니다.

* **Azure 도구:** Azure PowerShell, Azure CLI (명령줄 인터페이스), Azure REST API 또는 Azure Portal를 통해 활동 로그에서 정보를 검색 합니다. 각 방법에 대한 단계별 지침은 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md) 문서에 자세히 나와 있습니다.
* **Power BI:**[Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우에는 무료로 사용해볼 수 있습니다. [Power BI에 대 한 Azure 감사 로그 통합](https://powerbi.microsoft.com/integrations/azure-audit-logs/)을 사용 하 여 미리 구성 된 대시보드를 사용 하 여 데이터를 분석 하거나 요구 사항에 맞게 보기를 사용자 지정할 수 있습니다.

## <a name="metrics-as-logs"></a>로그로 메트릭
메트릭을 사용 하 여 Azure Monitor에서 제공 하는 내보내기 기능을 기록 Load Balancer 메트릭을 내보낼 수 있습니다. 이러한 메트릭은 1 분의 샘플링 간격 마다 로그 항목을 생성 합니다.

로그 내보내기에 대 한 메트릭은 리소스 수준에 따라 설정 됩니다. 진단 설정 블레이드로 이동 하 여 리소스 그룹별로 필터링 하 고 메트릭 내보내기를 사용 하도록 설정할 Load Balancer 선택 하 여 이러한 로그를 사용 하도록 설정할 수 있습니다. Load Balancer 진단 설정 페이지가 up 이면 AllMetrics을 선택 하 여 적격 메트릭을 로그로 내보냅니다.

메트릭 내보내기 제한 사항은이 문서의 [제한 사항](#limitations) 섹션을 참조 하세요.

### <a name="view-and-analyze-metrics-as-logs"></a>메트릭을 로그로 보기 및 분석
표준 Load Balancer의 진단 설정에서 AllMetrics 사용 하도록 설정한 후 이벤트 허브 또는 Log Analytics 작업 영역을 사용 하는 경우 이러한 로그는 AzureMonitor 테이블에 채워집니다. 저장소로 내보내는 경우 저장소 계정에 연결 하 고 이벤트 및 상태 프로브 로그에 대 한 JSON 로그 항목을 검색 합니다. JSON 파일을 다운로드 한 후에는 CSV로 변환 하 고 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다. 

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 GitHub에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.

## <a name="stream-to-an-event-hub"></a>이벤트 허브로 스트림
진단 정보는 이벤트 허브로 스트리밍되는 경우 타사 SIEM 도구에서 Azure Monitor 통합을 통해 중앙 집중식 로그 분석에 사용할 수 있습니다. 자세한 내용은 [Azure 모니터링 데이터를 이벤트 허브로 스트리밍](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration) 을 참조 하세요.

## <a name="send-to-log-analytics"></a>Log Analytics에 보내기
Azure의 리소스에는 문제 해결 및 분석을 위한 정보에 대해 복잡 한 쿼리를 실행할 수 있는 Log Analytics 작업 영역으로 직접 전송 되는 진단 정보가 있을 수 있습니다.  자세한 내용은 [Log Analytics 작업 영역에서 Azure 리소스 로그 수집](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) 을 참조 하세요 Azure Monitor

## <a name="limitations"></a>제한 사항
부하 분산 장치에 대 한 내보내기 기능을 기록 하는 데 메트릭을 사용 하는 경우 현재 다음과 같은 제한 사항이 있습니다.
* 현재 메트릭은 로그로 내보낼 때 내부 이름을 사용 하 여 표시 되며 아래 표에서 매핑을 찾을 수 있습니다.
* 메트릭의 차원은 유지 되지 않습니다. 예를 들어 DipAvailability (상태 프로브 상태)와 같은 메트릭을 사용 하는 경우 백 엔드 IP 주소를 사용 하 여 분할 하거나 볼 수 없습니다.
* 사용 된 SNAT 포트 및 할당 된 SNAT 포트 메트릭은 현재 로그로 내보내기에 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [Load Balancer에 대해 사용 가능한 메트릭을 검토 합니다.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Azure Monitor 지침에 따라 쿼리를 만들고 테스트 합니다.](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* 아래 링크를 사용 하 여이 문서 또는 Load Balancer 기능에 대 한 피드백 제공
