---
title: Azure 보안 센터 경고 및 권장 사항을 SIEM에 내보내기 | 마이크로 소프트 문서
description: 이 문서에서는 SIEM에 대한 보안 경고 및 권장 사항의 지속적인 내보내기를 설정하는 방법에 대해 설명합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 19fdcc7b590c6ad6873c7808ae26d218bbda7f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158975"
---
# <a name="export-security-alerts-and-recommendations"></a>보안 경고 및 권장 사항 내보내기

Azure 보안 센터는 자세한 보안 경고 및 권장 사항을 생성합니다. 포털이나 프로그래밍 도구를 통해 볼 수 있습니다. 이 정보를 내보내거나 사용자 환경의 다른 모니터링 도구로 보내야 할 수도 있습니다. 

이 문서에서는 수동으로 또는 지속적으로 경고 및 권장 사항을 내보낼 수 있는 도구 집합에 대해 설명합니다.

다음 도구를 사용할 수 있습니다.

* 로그 분석 작업 영역으로 지속적으로 내보내기
* Azure 이벤트 허브로 지속적으로 내보내기(타사 SIEM과의 통합용)
* CSV로 내보내기(1회)


## <a name="setting-up-a-continuous-export"></a>연속 내보내기 설정

아래 단계는 Log Analytics 작업 영역 또는 Azure 이벤트 허브로의 지속적인 내보내기를 설정하는 데 필요합니다.

1. 보안 센터의 사이드바에서 **가격 & 설정을**선택합니다.

1. 데이터 내보내기를 구성할 특정 구독을 선택합니다.
    
1. 해당 구독에 대한 설정 페이지의 사이드바에서 **연속 내보내기를**선택합니다.

    [Azure 보안 센터에서 내보내기 옵션 ![](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) 여기에 내보내기 옵션이 표시됩니다. 사용 가능한 각 내보내기 대상에 대한 탭이 있습니다. 

1. 내보낼 데이터 형식을 선택하고 각 유형의 필터에서 선택합니다(예: 심각도가 높은 경고만 내보내기).

1. '대상 내보내기' 영역에서 데이터를 저장할 위치를 선택합니다. 데이터는 다른 구독의 대상에 저장할 수 있습니다(예: 중앙 이벤트 허브 인스턴스 또는 중앙 로그 분석 작업 영역).

1. **저장**을 클릭합니다.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>Azure 이벤트 허브를 통해 SIEM 통합 구성

Azure Event Hubs는 모든 스트리밍 데이터를 프로그래밍하여 사용할 수 있는 훌륭한 솔루션입니다. Azure 보안 센터 경고 및 권장 사항의 경우 타사 SIEM과 통합하는 것이 좋습니다.

> [!NOTE]
> 대부분의 경우 모니터링 데이터를 외부 도구로 스트리밍하는 가장 효과적인 방법은 Azure Event Hubs를 사용하는 것입니다. [이 문서에서는](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) 모니터링 데이터를 여러 소스에서 이벤트 허브로 스트리밍하는 방법에 대한 간략한 설명과 자세한 지침에 대한 링크를 제공합니다.

> [!NOTE]
> 이전에 Azure Activity 로그를 사용하여 SIEM에 보안 센터 경고를 내보낸 경우 아래 의 절차에서 해당 방법론을 대체합니다.

내보낸 데이터 형식의 이벤트 스키마를 보려면 [이벤트 허브 이벤트 스키마를](https://aka.ms/ASCAutomationSchemas)방문하십시오.


### <a name="to-integrate-with-a-siem"></a>SIEM과 통합하려면 

선택한 보안 센터 데이터를 Azure 이벤트 허브로 연속 내보내기하도록 구성한 후 SIEM에 적합한 커넥터를 설정할 수 있습니다.

* **Azure Sentinel** - 네이티브 Azure 보안 센터 경고 [데이터 커넥터를](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) 사용 합니다.
* **스플렁크** - [스플렁크에 Azure 모니터 추가 기능](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md) 사용
* **IBM QRadar** - [수동으로 구성된 로그 소스](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html) 사용
* **아크사이트** – [스마트 커넥터](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292) 사용

또한 구성된 이벤트 허브에서 Azure 데이터 탐색기로 지속적으로 내보낸 데이터를 자동으로 이동하려면 [이벤트 허브에서 Azure Data 탐색기로 데이터 수집의](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)지침을 사용합니다.



## <a name="continuous-export-to-a-log-analytics-workspace"></a>로그 분석 작업 영역으로 연속 내보내기

로그 분석 작업 영역 내에서 Azure 보안 센터 데이터를 분석하거나 보안 센터와 함께 Azure 경고를 사용하려는 경우 로그 분석 작업 영역으로 연속 내보내기를 설정합니다.

로그 분석 작업 영역으로 내보내려면 작업 영역에서 보안 센터의 로그 분석 솔루션을 사용하도록 설정해야 합니다. Azure 포털을 사용하는 경우 연속 내보내기를 사용하도록 설정하면 보안 센터의 프리 계층 솔루션이 자동으로 활성화됩니다. 그러나 연속 내보내기 설정을 프로그래밍 방식으로 구성하는 경우 **가격 책정 & 설정**내에서 필요한 작업 영역에 대한 무료 또는 표준 가격 책정 계층을 수동으로 선택해야 합니다.  

### <a name="log-analytics-tables-and-schemas"></a>로그 분석 테이블 및 스키마

보안 경고 및 권장 사항은 각각 *SecurityAlert* 및 *Security추천* 테이블에 저장됩니다. 이러한 테이블을 포함하는 Log Analytics 솔루션의 이름은 무료 계층인지 표준 계층에 있는지 여부에 따라 [달라집니다(가격 책정](security-center-pricing.md)참조): 보안('보안 및 감사') 또는 SecurityCenterFree.

![로그 분석의 *보안 경고* 테이블](./media/continuous-export/log-analytics-securityalert-solution.png)

내보낸 데이터 형식의 이벤트 스키마를 보려면 [Log Analytics 테이블 스키마를](https://aka.ms/ASCAutomationSchemas)방문하십시오.

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Azure 모니터에서 내보낸 보안 경고 및 권장 사항 보기

경우에 따라 [내보낸](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)보안 경고 및/또는 권장 사항을 Azure Monitor 에서 볼 수 있습니다. 

Azure Monitor는 로그 분석 작업 영역 쿼리를 기반으로 하는 진단 로그, 메트릭 경고 및 사용자 지정 경고를 비롯한 다양한 Azure 경고에 대한 통합 경고 환경을 제공합니다.

Azure Monitor의 보안 센터에서 경고 및 권장 사항을 보려면 로그 분석 쿼리(로그 경고)를 기반으로 경고 규칙을 구성합니다.

1. Azure 모니터의 경고 페이지에서 **새 경고 규칙을** **클릭합니다.**

    ![Azure 모니터의 경고 페이지](./media/continuous-export/azure-monitor-alerts.png)

1. 규칙 만들기 페이지에서 새 규칙을 구성합니다(Azure [Monitor에서 로그 경고 규칙을](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)구성하는 것과 동일한 방식으로)

    * **리소스의**경우 보안 경고 및 권장 사항을 내보낸 로그 분석 작업 영역을 선택합니다.

    * **조건의**경우 **사용자 지정 로그 검색을**선택합니다. 표시되는 페이지에서 쿼리, 조회 기간 및 빈도 기간을 구성합니다. 검색 쿼리에서 *SecurityAlert* 또는 *Security추천을* 입력하여 로그 분석 기능으로 연속 내보내기기능을 활성화할 때 보안 센터에서 지속적으로 내보내는 데이터 형식을 쿼리할 수 있습니다. 
    
    * 선택적으로 트리거할 [작업 그룹을](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 구성합니다. 작업 그룹은 이메일 전송, ITSM 티켓, WebHooks 등을 트리거할 수 있습니다.
    ![Azure 모니터 경고 규칙](./media/continuous-export/azure-monitor-alert-rule.png)

이제 Azure Monitor 경고에 새로운 Azure 보안 센터 경고 또는 권장 사항(구성에 따라 다름)이 표시되며, 작업 그룹의 자동 트리거(제공된 경우)가 표시됩니다.

## <a name="manual-one-time-export-of-security-alerts"></a>보안 경고의 수동 일회성 내보내기

경고 또는 권장 사항에 대한 CSV 보고서를 다운로드하려면 보안 경고 또는 **권장 사항** 페이지를 열고 **CSV 보고서 다운로드** 단추를 **클릭합니다.**

[![CSV 파일로 경고 데이터 다운로드](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> 이러한 보고서에는 현재 선택한 구독의 리소스에 대한 경고 및 권장 사항이 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 권장 사항 및 경고의 지속적인 내보내기를 구성하는 방법을 배웠습니다. 또한 경고 데이터를 CSV 파일로 다운로드하는 방법도 배웠습니다. 

관련 자료는 다음 설명서를 참조하십시오. 

- [Azure 이벤트 허브 설명서](https://docs.microsoft.com/azure/event-hubs/)
- [Azure 센티넬 문서](https://docs.microsoft.com/azure/sentinel/)
- [Azure 모니터 설명서](https://docs.microsoft.com/azure/azure-monitor/)
- [워크플로 자동화 및 지속적인 내보내기 데이터 유형 스키마](https://aka.ms/ASCAutomationSchemas)
