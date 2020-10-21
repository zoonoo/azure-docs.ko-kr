---
title: Azure Security Center에서 SIEM (보안 정보 및 이벤트 관리) 시스템 및 기타 모니터링 솔루션으로 경고 스트림
description: Azure 센티널, 타사 SIEMs, 대화 충성도 또는 ITSM 솔루션에 대 한 보안 경고를 스트리밍하는 방법에 대해 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: b4458a2b37a3da83591e101344d08c3090868696
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341807"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>SIEM, 대화 충성도 또는 IT 서비스 관리 솔루션에 대 한 경고 스트림

Azure Security Center 보안 경고를 가장 인기 있는 SIEM (보안 정보 및 이벤트 관리), 대화 충성도 (Security Orchestration 자동화 된 응답) 및 ITSM (IT Service Management) 솔루션으로 스트리밍할 수 있습니다.

현재 사용 중인 가장 인기 있는 모든 솔루션에서 경고 데이터를 볼 수 있도록 하는 Azure 네이티브 도구가 있습니다.

- **Azure Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **IBM의 QRadar**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Azure 센티널로 경고 스트림 

Security Center은 기본적으로 Azure 센티널, Azure의 클라우드 네이티브 SIEM 및 대화 충성도 솔루션과 통합 됩니다. 

[Azure 센티널에 대해 자세히 알아보세요](../sentinel/overview.md).

### <a name="azure-sentinels-connectors-for-security-center"></a>Security Center에 대 한 Azure 센티널 커넥터

Azure 센티널에는 구독 및 테 넌 트 수준에서 Azure Security Center에 대 한 기본 제공 커넥터가 포함 되어 있습니다.

- [구독 수준에서 Azure 센티널로 경고 스트리밍](../sentinel/connect-azure-security-center.md)
- [테 넌 트의 모든 구독을 Azure 센티널에 연결](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>모든 감사 로그를 Azure 센티널로 수집 구성 

Azure 센티널에서 Security Center 경고를 조사 하는 또 다른 대안은 감사 로그를 Azure 센티널로 스트리밍하는 것입니다.
    - [Windows 보안 이벤트 연결](../sentinel/connect-windows-security-events.md)
    - [Syslog를 사용 하 여 Linux 기반 원본에서 데이터 수집](../sentinel/connect-syslog.md)
    - [Azure 활동 로그에서 데이터 연결](../sentinel/connect-azure-activity.md)

> [!TIP]
> Azure 센티널은 Azure 센티널의 분석을 위한 데이터 수집의 양을 기준으로 청구 되 고 Azure Monitor Log Analytics 작업 영역에 저장 됩니다. Azure 센티널은 유연 하 고 예측 가능한 가격 책정 모델을 제공 합니다. [Azure 센티널 가격 책정 페이지에서 자세히 알아보세요](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Microsoft Graph 보안 API를 사용 하 여 경고 스트림

Security Center에는 Microsoft Graph Security API와의 기본 통합 기능이 있습니다. 구성이 필요 하지 않으며 추가 비용이 없습니다. 

이 API를 사용 하 여 **전체 테 넌 트** (및 기타 여러 Microsoft 보안 제품의 데이터)에서 타사 siems 및 기타 인기 있는 플랫폼으로 경고를 스트리밍할 수 있습니다.

- **Splunk Enterprise 및 Splunk Cloud**  -  [Splunk에 대 한 Microsoft Graph SECURITY API Add-On 사용](https://splunkbase.splunk.com/app/4564/) 
- **Power BI**  -  [Power BI Desktop에서 Microsoft Graph 보안 API에 연결](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow**  -  [지침에 따라 ServiceNow 저장소에서 Microsoft Graph SECURITY API 응용 프로그램을 설치 하 고 구성](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html) 합니다.
- **Qradar**  -  [MICROSOFT GRAPH API를 통한 Azure Security Center 용 IBM의 장치 지원 모듈](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **Anomali**, **망루**, **InSpark**및 [Microsoft Graph 보안 API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Microsoft Graph 보안 API에 대해 자세히 알아보세요](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Azure Monitor를 사용 하 여 경고 스트림 

경고를 **Arcsight**, **Splunk**, **sumologic**, Syslog 서버, **LogRhythm**, **Logz.io Cloud 관찰성 Platform**및 기타 모니터링 솔루션으로 스트리밍합니다. Azure Event Hubs를 통해 Azure monitor를 사용 하 여 Security Center 연결:

1. 구독 수준에서 전용 Azure 이벤트 허브로 Security Center 경고를 스트리밍하려면 [연속 내보내기를](continuous-export.md) 사용 하도록 설정 합니다. 
    > [!TIP]
    > Azure Policy를 사용 하 여 관리 그룹 수준에서이 작업을 수행 하려면 [대규모 연속 내보내기 자동화 구성 만들기](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies) 를 참조 하세요.

1. [Azure Monitor의 기본 제공 커넥터를 사용 하 여 Azure Event hub를 기본 솔루션에 연결](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)합니다.

1. 필요에 따라 원시 로그를 Azure 이벤트 허브로 스트리밍하 고 원하는 솔루션에 연결 합니다. [사용 가능한 모니터링 데이터](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#monitoring-data-available)에 대해 자세히 알아보세요.

> [!TIP]
> 내보낸 데이터 형식의 이벤트 스키마를 보려면 [이벤트 허브 이벤트 스키마](https://aka.ms/ASCAutomationSchemas)를 방문 하십시오.


## <a name="next-steps"></a>다음 단계

이 페이지에서는 SIEM, 대화 충성도 또는 ITSM 도구에서 Azure Security Center 경고 데이터를 사용할 수 있는지 확인 하는 방법에 대해 설명 했습니다. 관련 자료는 다음을 참조 하세요.

- [Azure Sentinel이란?](../sentinel/overview.md)
- [Azure Security Center의 경고 유효성 검사](security-center-alert-validation.md) -경고가 올바르게 구성 되었는지 확인
- [계속 해 서 보안 경고 및 권장 사항 내보내기](continuous-export.md)