---
title: SIEMs에 대 한 Azure Security Center 경고 및 권장 사항 내보내기 | Microsoft Docs
description: 이 문서에서는 SIEMs에 대 한 보안 경고 및 권장 사항의 연속 내보내기를 설정 하는 방법을 설명 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cd26ed446ce676bcec85d8e413d3ec37ac236869
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521996"
---
# <a name="export-security-alerts-and-recommendations-preview"></a>보안 경고 및 권장 사항 내보내기 (미리 보기)

Azure Security Center은 자세한 보안 경고 및 권장 사항을 생성 합니다. 포털에서 또는 프로그래밍 방식 도구를 통해 볼 수 있습니다. 이 정보를 내보내거나 사용자 환경의 다른 모니터링 도구에 보내야 할 수도 있습니다. 

이 문서에서는 경고와 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보낼 수 있는 (미리 보기) 도구 집합을 설명 합니다.

이러한 도구를 사용 하 여 다음을 수행할 수 있습니다.

* 상세 보고서를 CSV로 생성
* Log Analytics 작업 영역으로 내보내기
* Azure Event Hubs로 내보내기 (타사 SIEMs와 통합)

## <a name="setting-up-a-continuous-export"></a>연속 내보내기 설정

1. Security Center의 사이드바에서 **가격 책정 & 설정**을 클릭 합니다.

1. 데이터 내보내기를 구성할 특정 구독을 선택 합니다.
    
1. 해당 구독에 대 한 설정 페이지의 사이드바에서 **연속 내보내기 (미리 보기)** 를 선택 합니다.

    [Azure Security Center![내보내기 옵션](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) 여기에서 내보내기 옵션을 볼 수 있습니다. 사용 가능한 각 내보내기 대상에 대 한 탭이 있습니다. 

1. 내보낼 데이터 형식을 선택 하 고 각 유형의 필터에서 선택 합니다 (예: 높은 심각도 경고만 내보내기).

1. "대상 내보내기" 영역에서 데이터를 저장 하려는 위치를 선택 합니다. 데이터는 다른 구독 (예: 중앙 이벤트 허브 인스턴스 또는 중앙 Log Analytics 작업 영역)의 대상에 저장할 수 있습니다.

1. **Save**를 클릭합니다.

## <a name="continuous-export-through-azure-event-hubs"></a>Azure Event Hubs를 통한 연속 내보내기  

> [!NOTE]
> 대부분의 경우 모니터링 데이터를 외부 도구로 스트리밍하는 가장 효과적인 방법은 Azure Event Hubs를 사용 하는 것입니다. [이 문서](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) 에서는 다양 한 원본에서 이벤트 허브로 모니터링 데이터를 스트리밍하는 방법에 대 한 간략 한 설명을 제공 하 고 자세한 지침에 대 한 링크를 제공 합니다.

> [!NOTE]
> Azure 활동 로그를 사용 하 여 이전에 Security Center 경고를 SIEM으로 내보낸 경우 아래 절차가 해당 방법론을 대체 합니다.

### <a name="to-integrate-with-a-siem"></a>SIEM과 통합 하려면 

선택한 Security Center 데이터의 연속 내보내기를 Azure Event Hubs로 구성한 후에는 아래 지침에 따라 SIEM에서 적절 한 커넥터를 설정할 수 있습니다.

[이 페이지](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/?cdn=disable) 에서 siem과 관련 된 지침을 따르고 관련 커넥터를 사용 합니다.

* **Splunk** - [Splunk에 대 한 Azure Monitor 추가 기능](https://splunkbase.splunk.com/app/3534/) 사용
* **IBM QRadar** - [수동으로 구성 된 로그 원본](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_logsource_microsoft_azure_event_hubs.html) 사용
* **Arcsight** – [smartconnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292) 사용

**Azure 센티널**를 사용 하는 경우 여기에 제공 된 기본 Azure Security Center 경고 [데이터 커넥터](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) 를 사용 합니다.

또한 지속적으로 내보낸 데이터를 구성 된 이벤트 허브에서 Azure 데이터 탐색기로 자동으로 이동 하려면 [이벤트 허브에서 azure 데이터 탐색기로 데이터 수집](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)의 지침을 사용 하세요.


## <a name="continuous-export-to-log-analytics-workspace"></a>Log Analytics 작업 영역으로 연속 내보내기

Log Analytics 작업 영역으로 내보내려면 작업 영역에서 Security Center의 무료 또는 표준 계층 Log Analytics 솔루션을 사용 하도록 설정 해야 합니다. Azure Portal 사용 하는 경우 연속 내보내기를 사용 하도록 설정 하면 Security Center 무료 계층 솔루션이 자동으로 사용 하도록 설정 됩니다. 그러나 연속 내보내기 설정을 프로그래밍 방식으로 구성 하는 경우 **가격 책정 & 설정**에서 필수 작업 영역에 대 한 무료 또는 표준 가격 책정 계층을 수동으로 선택 해야 합니다.  

보안 경고 및 권장 사항은 각각 *Securityalert* 및 *securityalert* 테이블에 저장 됩니다. 이러한 테이블을 포함 하는 Log Analytics 솔루션의 이름은 무료 또는 표준 계층에 있는지 여부에 따라 달라 집니다 ( [가격 책정](security-center-pricing.md)참조). Security 또는 Securitycenter free.

![Log Analytics의 * SecurityAlert * 테이블](./media/continuous-export/log-analytics-securityalert-solution.png)

## <a name="manual-one-time-export-of-security-alerts"></a>보안 경고의 수동 일회성 내보내기

경고 또는 권장 사항에 대 한 CSV 보고서를 다운로드 하려면 **보안 경고** 또는 **권장 사항** 페이지를 열고 **Csv 보고서 다운로드 (미리 보기)** 단추를 클릭 합니다.

[경고 데이터를 CSV 파일로 다운로드 ![](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> 이러한 보고서에는 Azure Portal의 디렉터리 + 구독 필터에서 현재 선택한 구독의 리소스에 대 한 경고 및 권장 사항이 포함 되어 있습니다. 디렉터리 + 구독을 선택 하는 필터를 ![](./media/continuous-export/filter-for-export-csv.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 권장 사항 및 경고의 연속 내보내기를 구성 하는 방법에 대해 알아보았습니다. 또한 경고 데이터를 CSV 파일로 다운로드 하는 방법도 배웠습니다. 

관련 자료는 다음 설명서를 참조 하세요. 

- [Azure Event Hubs 설명서](https://docs.microsoft.com/azure/event-hubs/)
- [Azure 센티널 설명서](https://docs.microsoft.com/azure/sentinel/)
- [Azure Monitor 설명서](https://docs.microsoft.com/azure/azure-monitor/)