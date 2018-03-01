---
title: "Azure 리소스에서 SIEM 시스템으로 로그 통합 | Microsoft Docs"
description: "Azure 로그 통합, 주요 기능 및 작동 원리에 대해 알아봅니다."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: e427e2f7dafb6db9bc5c15d841fbbf83a02fc0e1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Microsoft Azure 로그 통합 소개

SIEM 공급업체에서 [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md)에 대한 커넥터를 아직 제공하지 않은 경우에는, Azure Log Integration을 통해 Azure 리소스의 원시 로그를 온-프레미스 SIEM(보안 정보 및 이벤트 관리) 시스템과 통합할 수 있습니다.

Azure 로그를 통합하는 가장 좋은 방법은 SIEM 공급업체의 Azure Monitor 커넥터를 사용하고 다음 [지침](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)을 따르는 것입니다. 하지만, SIEM 공급업체에서 Azure Monitor에 대한 커넥터를 제공하지 않은 경우에는 해당 커넥터를 사용할 수 있을 때까지 임시 해결책으로 Azure Log Integration을 사용할 수 있습니다(Azure Log Integration에서 해당 SIEM이 지원되는 경우).

>[!IMPORTANT]
>가상 머신 로그를 수집하는 것이 주요 관심사인 경우, 이 기능은 대부분의 SIEM 공급업체 솔루션에 포함됩니다. SIEM 공급업체의 커넥터를 사용하는 것이 항상 선호하는 대안이 되어야 합니다.

Azure 로그 통합은 Azure 리소스에서 Windows 이벤트 뷰어 로그, [Azure 활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [Azure Security Center 경고](../security-center/security-center-intro.md) 및 [Azure 진단 로그](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)로부터 Windows 이벤트를 수집합니다. 이 통합을 통해 SIEM 솔루션에서 보안 이벤트를 집계하고, 상관 관계를 설정하고, 분석하고, 경고할 수 있도록 온-프레미스 또는 클라우드의 모든 자산에 대한 통합 대시보드를 제공할 수 있습니다.

>[!NOTE]
현재 지원되는 클라우드는 Azure Commercial 및 Azure Government뿐입니다. 다른 클라우드는 지원되지 않습니다.

![Azure 로그 통합][1]

## <a name="what-logs-can-i-integrate"></a>어떤 로그와 통합할 수 있나요?

Azure에서는 모든 Azure 서비스에 대해 광범위한 로깅을 생성합니다. 이러한 로그는 세 가지 유형의 로그를 나타냅니다.

* **컨트롤/관리 로그**는 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) CREATE, UPDATE 및 DELETE 작업에 대한 가시성을 제공합니다. Azure 활동 로그가 이 로그 유형에 속합니다.
* **데이터 평면 로그**는 Azure 리소스를 사용할 때 발생하는 이벤트에 대한 가시성을 제공합니다. 이 로그 유형의 예는 Windows 가상 컴퓨터의 Windows 이벤트 뷰어에 있는 **시스템**, **보안** 및 **응용 프로그램** 채널이 있습니다. 또 다른 예는 Azure Monitor를 통해 구성된 진단 로깅입니다.
* **처리된 이벤트**는 사용자 대신 처리된 경고 정보 및 분석된 이벤트를 제공합니다. 이 이벤트 유형의 예로 Azure Security Center 경고가 있습니다. Azure Security Center에서는 현재 보안 상태와 관련된 경고를 제공하기 위해 구독을 처리하고 분석합니다.

Azure 로그 통합은 ArcSight, QRadar 및 Splunk를 지원합니다. 모든 상황에서 SIEM 공급업체에 문의하여 기본 커넥터가 있는지 확인하십시오. 네이티브 커넥터를 사용할 수 있는 경우에는 Azure Log Integration을 사용하지 말아야 합니다.

다른 옵션이 없는 경우에만 Azure Log Integration을 고려할 수 있습니다. 다음 표에는 권장 사항이 포함되어 있습니다.

|**SIEM** | **고객이 로그 통합자를 이미 사용 중인 경우** | **고객이 SIEM 통합 옵션을 조사 중인 경우**|
|---------|--------------------------|-------------------------------------------|
|**SPLUNK** | [Splunk용 Azure Monitor 추가 기능](https://splunkbase.splunk.com/app/3534/)에 마이그레이션 시작 | [SPLUNK 커넥터](https://splunkbase.splunk.com/app/3534/) 사용 |
|**IBM QRADAR** | http://aka.ms/azmoneventhub의 끝 부분에 설명되어 있는 QRadar 커넥터로 마이그레이션 또는 사용 | http://aka.ms/azmoneventhub의 끝 부분에 설명되어 있는 QRadar 커넥터 사용  |
|**ARCSIGHT** | 커넥터를 사용할 수 있을 때까지 로그 통합자를 계속 사용한 다음, 커넥터 기반 솔루션으로 마이그레이션합니다.  | Azure Log Analytics를 대안으로 고려합니다. 커넥터를 사용할 수 있게 되었을 때 마이그레이션 프로세스를 수행할 의향이 없다면 Azure Log Integration을 사용하지 마십시오. |

>[!NOTE]
>Azure 로그 통합은 무료 솔루션이지만 로그 파일 정보 저장소로 인한 Azure Storage 비용이 발생합니다.

도움이 필요하면 [지원 요청](../azure-supportability/how-to-create-azure-support-request.md)을 열 수 있습니다. 지원 요청을 열려면 지원을 요청하는 서비스로 **로그 통합**을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 로그 통합을 소개했습니다. Azure 로그 통합 및 지원되는 로그 유형에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 로그 통합 시작](security-azure-log-integration-get-started.md) - 이 자습서에서는 Azure 로그 통합을 설치하고 Azure WAD 저장소, Azure 활동 로그, Azure Security Center 경고 및 의 Azure Active Directory 감사 로그의 로그를 통합하는 방법을 안내합니다.
* [Azure 로그 통합 FAQ(질문과 대답)](security-azure-log-integration-faq.md) - 이 FAQ는 Azure 로그 통합에 대한 질문에 답변합니다.
* [Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
