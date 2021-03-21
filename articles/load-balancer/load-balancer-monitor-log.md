---
title: 공용 부하 분산 장치에 대 한 작업, 이벤트 및 카운터 모니터링
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
ms.openlocfilehash: 36937ace82d2bd8d4317f90a375042de10fe719f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709821"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Azure 표준 Load Balancer에 대 한 Azure Monitor 로그

서로 다른 유형의 Azure Monitor 로그를 사용 하 여 Azure 표준 Load Balancer를 관리 하 고 문제를 해결할 수 있습니다. 로그는 이벤트 허브 또는 Log Analytics 작업 영역으로 스트리밍할 수 있습니다. Azure Blob Storage에서 모든 로그를 추출 하 고 Excel 및 Power BI와 같은 도구에서 볼 수 있습니다. 

로그 유형은 다음과 같습니다.

* **활동 로그:** Azure 구독에 제출 되는 모든 활동을 상태와 함께 볼 수 있습니다. 자세한 내용은 [활동 로그를 확인 하 여 리소스에 대 한 작업 모니터링](../azure-resource-manager/management/view-activity-logs.md)을 참조 하세요. 활동 로그는 기본적으로 사용 하도록 설정 되며 Azure Portal에서 볼 수 있습니다. 이러한 로그는 Azure Basic Load Balancer와 표준 Load Balancer 모두 사용할 수 있습니다.
* **표준 Load Balancer 메트릭:** 이 로그를 사용 하 여 표준 Load Balancer에 대 한 로그로 내보낸 메트릭을 쿼리할 수 있습니다. 이러한 로그는 표준 Load Balancer에만 사용할 수 있습니다.

> [!IMPORTANT]
> 상태 프로브 및 Load Balancer 경고 이벤트 로그는 현재 작동 하지 않으며 [Azure Load Balancer에 대 한 알려진 문제](whats-new.md#known-issues)에 나열 되어 있습니다. 

> [!IMPORTANT]
> 로그는 Azure Resource Manager 배포 모델에 배포된 리소스에만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대 한 로그를 사용할 수 없습니다. 배포 모델에 대한 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](../azure-resource-manager/management/deployment-models.md)를 참조하세요.

## <a name="enable-logging"></a>로깅 사용

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이벤트 및 상태 프로브 로깅을 사용 하 여 해당 로그를 통해 사용 가능한 데이터 수집을 시작 합니다. 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 부하 분산 장치가 아직 없으면, 계속하기 전에 [부하 분산 장치를 만듭니다](./quickstart-load-balancer-standard-public-portal.md) .
1. 포털에서 **리소스 그룹** 을 선택 합니다.
2. **\<resource-group-name>** 부하 분산 장치가 있는 위치를 선택 합니다.
3. 부하 분산 장치를 선택합니다.
4. **활동 로그**  >  **진단 설정** 을 선택 합니다.
5. **진단 설정** 창의 **진단 설정** 에서 **+ 진단 설정 추가** 를 선택 합니다.
6. **진단 설정** 생성 창의 **이름** 상자에 **myLBDiagnostics** 을 입력 합니다.
7. **진단 설정** 에 대 한 세 가지 옵션이 있습니다. 하나, 두 개 또는 세 가지를 모두 선택 하 고 요구 사항에 맞게 구성할 수 있습니다.

   * **스토리지 계정에 보관**. 이 프로세스에 대해 이미 만들어진 저장소 계정이 필요 합니다. 스토리지 계정을 만들려면 [스토리지 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)를 참조하세요.
     1. **저장소 계정에 보관** 확인란을 선택 합니다.
     2. **구성** 을 선택 하 여 **저장소 계정 선택** 창을 엽니다.
     3. **구독** 드롭다운 목록에서 저장소 계정이 만들어진 구독을 선택 합니다.
     4. **저장소 계정** 드롭다운 목록에서 저장소 계정의 이름을 선택 합니다.
     5. **확인** 을 선택합니다.

   * **이벤트 허브로 스트림**. 이 프로세스에 대 한 이벤트 허브가 이미 생성 되어 있어야 합니다. 이벤트 허브를 만들려면 [빠른 시작: Azure Portal 사용 하 여 이벤트 허브 만들기](../event-hubs/event-hubs-create.md)를 참조 하세요.
     1. **이벤트 허브로의 스트림** 확인란을 선택합니다.
     2. **구성** 을 선택 하 여 **이벤트 허브 선택** 창을 엽니다.
     3. **구독** 드롭다운 목록에서 이벤트 허브가 만들어진 구독을 선택 합니다.
     4. **이벤트 허브 네임 스페이스 선택** 드롭다운 목록에서 네임 스페이스를 선택 합니다.
     5. **이벤트 허브 정책 이름 선택** 드롭다운 목록에서 이름을 선택 합니다.
     6. **확인** 을 선택합니다.

   * **Log Analytics으로 보냅니다**. 이 프로세스에 대 한 log analytics 작업 영역을 만들고 구성 해야 합니다. Log Analytics 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 참조 하세요.
     1. **Log Analytics에 보내기** 확인란을 선택합니다.
     2. **구독** 드롭다운 목록에서 Log Analytics 작업 영역이 인 구독을 선택 합니다.
     3. **Log Analytics 작업 영역** 드롭다운 목록에서 작업 영역을 선택 합니다.

8. **진단 설정** 창의 메트릭 섹션에서 **allmetrics** 확인란을 선택 합니다  .

9. 모든 항목이 올바른지 확인 한 다음 **진단 설정** 만들기 창의 위쪽에서 **저장** 을 선택 합니다.

## <a name="view-and-analyze-the-activity-log"></a>활동 로그 보기 및 분석

활동 로그는 기본적으로 생성 됩니다. [이 문서의 지침에](../azure-monitor/essentials/activity-log.md)따라 구독 수준에서 내보내도록 구성할 수 있습니다. [작업 로그 보기에서 리소스에 대 한 작업 모니터링](../azure-resource-manager/management/view-activity-logs.md) 문서를 읽어 이러한 로그에 대해 자세히 알아보세요.

다음 방법 중 하나를 사용 하 여 활동 로그 데이터를 보고 분석할 수 있습니다.

* **Azure 도구:** Azure PowerShell, Azure CLI, Azure REST API 또는 Azure Portal를 통해 활동 로그에서 정보를 검색 합니다. [리소스 관리자 감사 작업](../azure-resource-manager/management/view-activity-logs.md) 문서에서는 각 방법에 대 한 단계별 지침을 제공 합니다.
* **Power BI:**[Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우에는 무료로 사용해볼 수 있습니다. [Power BI에 대 한 Azure 감사 로그 통합](https://powerbi.microsoft.com/integrations/azure-audit-logs/)을 사용 하 여 미리 구성 된 대시보드로 데이터를 분석할 수 있습니다. 또는 요구 사항에 맞게 보기를 사용자 지정할 수 있습니다.

## <a name="view-and-analyze-metrics-as-logs"></a>메트릭을 로그로 보기 및 분석
Azure Monitor 내보내기 기능을 사용 하 여 Load Balancer 메트릭을 내보낼 수 있습니다. 이러한 메트릭은 1 분 간격의 각 샘플링 간격에 대 한 로그 항목을 생성 합니다.

메트릭-로그 내보내기는 리소스 수준에서 사용 하도록 설정 됩니다. 이러한 로그를 사용 하려면 다음을 수행 합니다.

1. **진단 설정** 창으로 이동 합니다.
1. 리소스 그룹별 필터링 한 다음 메트릭 내보내기를 사용 하도록 설정 하려는 Load Balancer 인스턴스를 선택 합니다. 
1. Load Balancer에 대 한 진단 설정 페이지가 켜져 있으면 **Allmetrics** 을 선택 하 여 적격 메트릭을 로그로 내보냅니다.

메트릭 내보내기 제한 사항은이 문서의 [제한 사항](#limitations) 섹션을 참조 하세요.

표준 Load Balancer의 진단 설정에서 **Allmetrics** 사용 하도록 설정 하 고 나면 이벤트 허브 또는 Log Analytics 작업 영역을 사용 하는 경우 이러한 로그는 **azuremonitor** 테이블에 채워집니다. 

저장소로 내보내는 경우 저장소 계정에 연결 하 고 이벤트 및 상태 프로브 로그에 대 한 JSON 로그 항목을 검색 합니다. JSON 파일을 다운로드 한 후에는 CSV로 변환 하 여 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다. 

> [!TIP]
> C #에서 상수 및 변수에 대 한 값을 변경 하는 방법 및 Visual Studio에 대해 잘 알고 있는 경우 GitHub에서 제공 되는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter) 를 사용할 수 있습니다.

## <a name="stream-to-an-event-hub"></a>이벤트 허브로 스트림
진단 정보를 이벤트 허브로 스트리밍하는 경우 Azure Monitor 통합을 사용 하 여 파트너 SIEM 도구의 중앙 집중식 로그 분석에 사용할 수 있습니다. 자세한 내용은 [Azure 모니터링 데이터를 이벤트 허브로 스트리밍](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)을 참조 하세요.

## <a name="send-to-log-analytics"></a>Log Analytics에 보내기
Azure의 리소스에 대 한 진단 정보를 Log Analytics 작업 영역에 직접 보낼 수 있습니다. 이 작업 영역에서 문제 해결 및 분석을 위한 정보에 대해 복잡 한 쿼리를 실행할 수 있습니다. 자세한 내용은 [Azure Monitor의 Log Analytics 작업 영역에서 Azure 리소스 로그 수집](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)을 참조 하세요.

## <a name="limitations"></a>제한 사항
Azure Load Balancer에 대 한 메트릭-로그 내보내기 기능에는 다음과 같은 제한 사항이 있습니다.
* 현재 메트릭은 로그로 내보낼 때 내부 이름을 통해 표시 됩니다. 아래 표에서 매핑을 찾을 수 있습니다.
* 메트릭의 차원은 유지 되지 않습니다. 예를 들어 **Dipavailability** (상태 프로브 상태)와 같은 메트릭을 사용 하는 경우 백 엔드 IP 주소를 사용 하 여 분할 하거나 볼 수 없습니다.
* 사용 된 SNAT 포트 및 할당 된 SNAT 포트에 대 한 메트릭은 현재 로그로 내보내기에 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [부하 분산 장치에 대해 사용 가능한 메트릭 검토](./load-balancer-standard-diagnostics.md)
* [Azure Monitor 지침에 따라 쿼리를 만들고 테스트 합니다.](../azure-monitor/logs/log-query-overview.md)