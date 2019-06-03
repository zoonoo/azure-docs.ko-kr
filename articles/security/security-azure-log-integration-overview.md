---
title: SIEM 시스템과 Azure 리소스의 로그 통합 | Microsoft Docs
description: Azure Log Integration, 주요 기능 및 작동 원리에 대해 알아봅니다.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/28/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 65e256b476c1e459ae937d9f6cbb43e0020fd9fe
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298126"
---
# <a name="introduction-to-azure-log-integration"></a>Azure Log Integration 소개

>[!IMPORTANT]
> Azure 로그 통합 기능을 06/15/2019 지원이 중지 될 예정입니다. AzLog 다운로드는 2018년 6월 27일부로 사용할 수 없습니다. 향후 작업 진행 방향에 대한 지침은 게시물 [Azure Monitor를 사용하여 SIEM 도구와 통합](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)을 검토하세요. 

Azure Log Integration을 통해 Azure 로그를 온-프레미스 SIEM(보안 정보 및 이벤트 관리) 시스템과 통합하는 작업을 간소화할 수 있습니다.

 Azure 로그를 통합하는 데 권장되는 방법은 SIEM 공급업체의 커넥터를 사용하는 것입니다. Azure Monitor는 로그를 이벤트 허브로 스트리밍하는 기능을 제공하며 SIEM 공급업체는 커넥터를 작성하여 이벤트 허브의 로그를 SIEM에 추가로 통합할 수 있습니다.  작동 방식에 대한 설명은 [데이터 이벤트 허브에 대한 모니터 스트림 모니터링](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)의 지침을 따르세요. 또한 이 문서는 직접 Azure 커넥터가 이미 제공되는 SIEM 목록도 제공합니다.  

> [!IMPORTANT]
> 가상 머신 로그를 수집하는 것이 주요 관심사인 경우, 이 옵션은 대부분의 SIEM 공급업체 솔루션에 포함됩니다. SIEM 공급업체의 커넥터를 사용하는 것이 항상 기본 대안입니다.

Azure Log Integration 기능에 대한 설명서는 기능이 사용되지 않을 때까지 계속 유지됩니다.

Azure Log Integration 기능에 대해 자세히 알아보려면 참조하세요.

Azure Log Integration은 Azure 리소스에서 Windows 이벤트 뷰어 로그, [Azure 활동 로그](../azure-monitor/platform/activity-logs-overview.md), [Azure Security Center 경고](../security-center/security-center-intro.md) 및 [Azure Diagnostics 로그](../azure-monitor/platform/diagnostic-logs-overview.md)의 Windows 이벤트를 수집합니다. 통합을 통해 SIEM 솔루션에서 온-프레미스 또는 클라우드에 있는 모든 자산에 대한 통합 대시보드를 제공할 수 있습니다. 대시보드를 사용하여 보안 이벤트에 대한 경고를 수신, 집계, 상호 연결 및 분석할 수 있습니다.

> [!NOTE]
> 현재 Azure Log Integration은 Azure 상용 클라우드 및 Azure Government 클라우드만 지원합니다. 다른 클라우드는 지원되지 않습니다.

![Azure Log Integration 프로세스][1]

## <a name="what-logs-can-i-integrate"></a>어떤 로그와 통합할 수 있나요?

Azure에서는 각 Azure 서비스에 대해 광범위한 로깅을 생성합니다. 로그에는 다음 세 가지 로그 유형이 있습니다.

* **컨트롤/관리 로그**: [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) CREATE, UPDATE 및 DELETE 작업에 대한 가시성을 제공합니다. Azure 활동 로그가 이 로그 유형에 속합니다.
* **데이터 평면 로그**: Azure 리소스를 사용할 때 발생하는 이벤트에 대한 가시성을 제공합니다. 이 로그 유형의 예는 Windows 가상 컴퓨터의 Windows 이벤트 뷰어에 있는 **시스템**, **보안** 및 **애플리케이션** 채널이 있습니다. 또 다른 예는 Azure Monitor를 통해 구성하는 Azure Diagnostics 로깅입니다.
* **처리된 이벤트**: 사용자를 위해 처리된 경고 정보 및 분석된 이벤트를 제공합니다. 이 이벤트 유형의 예는 Azure Security Center 경고입니다. Azure Security Center에서는 구독을 처리하고 분석하여 현재 보안 상태에 관련된 경고를 제공합니다.

Azure Log Integration은 ArcSight, QRadar 및 Splunk를 지원합니다. SIEM 공급업체에 문의하여 공급업체에 기본 커넥터가 있는지 확인합니다. 기본 커넥터를 사용할 수 있는 경우, Azure Log Integration을 사용하지 마세요.

사용할 수 있는 다른 옵션이 없는 경우에만 Azure Log Integration을 사용하는 것이 좋습니다. 다음 표에는 권장 사항이 포함되어 있습니다.

|SIEM | 고객이 Azure 로그 통합자를 이미 사용 중인 경우 | 고객이 SIEM 통합 옵션을 조사 중인 경우|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | [Splunk용 Azure Monitor 추가 기능](https://splunkbase.splunk.com/app/3534/)으로 마이그레이션을 시작합니다. | [Splunk 커넥터](https://splunkbase.splunk.com/app/3534/)를 사용합니다. |
|**QRadar** | [Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)의 마지막 섹션에 문서화된 QRadar 커넥터로 마이그레이션하거나 사용을 시작합니다. | [Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)의 마지막 섹션에 문서화된 QRadar 커넥터를 사용합니다. |
|**ArcSight** | 커넥터를 사용할 수 있을 때까지 Azure 로그 통합자를 계속 사용한 다음, 커넥터 기반 솔루션으로 마이그레이션합니다.  | 대 안으로 Azure Monitor 로그를 사용 하는 것이 좋습니다. 커넥터가 제공될 때 마이그레이션 프로세스를 수행할 의향이 없다면 Azure Log Integration을 사용하지 마세요. |

> [!NOTE]
> Azure Log Integration은 무료 솔루션이지만 로그 파일 정보 스토리지와 관련된 Azure Storage 비용이 발생합니다.

도움이 필요하면 [지원 요청](../azure-supportability/how-to-create-azure-support-request.md)을 만들 수 있습니다. 서비스에서 **Log Integration**을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Log Integration을 소개했습니다. Azure Log Integration 및 지원되는 로그 유형에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Log Integration 시작](security-azure-log-integration-get-started.md). 이 자습서에서는 Azure Log Integration 설치 과정을 안내합니다. WAD(Windows Azure Diagnostics) 스토리지 로그, Azure 활동 로그, Azure Security Center 경고 및 Azure Active Directory 감사 로그를 통합하는 방법에 대해서도 설명합니다.
* [Azure Log Integration FAQ(질문과 대답)](security-azure-log-integration-faq.md) 이 FAQ는 Azure Log Integration에 대해 자주 묻는 질문과 대답입니다.
* [Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)하는 방법에 대해 자세히 알아봅니다.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
