---
title: Azure Security Center에서 보안 솔루션 통합 | Microsoft Docs
description: Azure Security Center를 파트너와 통합하여 Azure 리소스의 전반적인 보안을 강화하는 방법에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 7174003485d51cf582c798c4b18404b1b72de0fb
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530955"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Security Center에서 보안 솔루션 통합
이 문서를 통해 이미 Azure Security Center에 연결된 보안 솔루션을 관리하고 새로 추가할 수 있습니다.

> [!NOTE]
> 보안 솔루션의 하위 집합은 2019 년 7 월 31 일에 사용이 중지 되었습니다. 자세한 내용 및 대체 서비스는 [Security Center 기능 사용 중지 (7 월 2019)](security-center-features-retirement-july2019.md#menu_solutions)를 참조 하세요.

## <a name="integrated-azure-security-solutions"></a>통합된 Azure 보안 솔루션
Security Center를 사용하면 Azure에서 통합된 보안 솔루션을 쉽게 사용할 수 있습니다. 제공되는 혜택:

- **배포 간소화**: Security Center에서는 통합된 파트너 솔루션의 간결한 프로비전을 제공합니다. 맬웨어 방지 및 취약성 평가와 같은 솔루션의 경우 가상 머신에서 에이전트를 프로 비전 할 수 Security Center. 방화벽 어플라이언스의 경우 필요한 네트워크 구성의 대부분을 처리할 수 Security Center.
- **통합된 감지**: 파트너 솔루션의 보안 이벤트는 자동으로 수집, 집계되며 Security Center 알림 및 사고의 일부로 표시됩니다. 또한 이러한 이벤트는 다른 원본의 감지를 결합하여 고급 위협 감지 기능을 제공합니다.
- **통합된 상태 모니터링 및 관리**: 고객은 통합된 상태 이벤트를 사용하여 한 눈에 모든 파트너 솔루션을 모니터링할 수 있습니다. 기본 관리는 파트너 솔루션을 사용하여 고급 설정에 쉽게 액세스하여 사용할 수 있습니다.

현재 통합 보안 솔루션은 [Qualys](https://www.qualys.com/public-cloud/#azure) 및 [Rapid7](https://www.rapid7.com/products/insightvm/) 및 Microsoft Application Gateway 웹 응용 프로그램 방화벽에의 한 취약성 평가를 포함 합니다.

> [!NOTE]
> 대부분의 보안 공급 업체는 해당 어플라이언스에서 실행 되는 외부 에이전트를 금지 하므로 Security Center는 파트너 가상 어플라이언스에 Microsoft Monitoring Agent를 설치 하지 않습니다.
>
>

## <a name="how-security-solutions-are-integrated"></a>보안 솔루션을 통합하는 방법
Security Center에서 배포된 Azure 보안 솔루션은 자동으로 연결됩니다. 온-프레미스 또는 다른 클라우드에서 실행 되는 컴퓨터를 포함 하 여 다른 보안 데이터 원본에 연결할 수도 있습니다.

![파트너 솔루션 통합](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>통합된 Azure 보안 솔루션 및 다른 데이터 원본 관리

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.

2. **Microsoft Azure 메뉴**에서 **Security Center**를 선택합니다. **Security Center - 개요**가 열립니다.

3. Security Center 메뉴에서 **보안 솔루션**을 선택합니다.

   ![Security Center 개요](./media/security-center-partner-integration/overview.png)

**보안 솔루션**에서 통합 된 Azure 보안 솔루션의 상태를 확인 하 고 기본 관리 작업을 실행할 수 있습니다.

### <a name="connected-solutions"></a>연결된 솔루션

**연결 된 솔루션** 섹션에는 현재 Security Center에 연결 된 보안 솔루션이 포함 되어 있습니다. 또한 각 솔루션의 상태를 보여 줍니다.  

![연결된 솔루션](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

파트너 솔루션의 상태는 다음과 같을 수 있습니다.

* 정상 (녹색)-상태 문제가 없습니다.
* 비정상 (빨강)-즉각적인 주의가 필요한 상태 문제가 있습니다.
* 상태 문제(주황색) - 솔루션이 상태의 보고를 중지했습니다.
* 보고 되지 않음 (회색)-솔루션이 아직 아무것도 보고 하지 않았으며 상태 데이터를 사용할 수 없습니다. 최근에 연결 되었고 여전히 배포 중인 솔루션의 상태가 보고 되지 않은 될 수 있습니다.

> [!NOTE]
> 상태 데이터를 사용할 수 없는 경우 Security Center에 마지막으로 수신된 이벤트의 날짜와 시간을 통해 솔루션이 보고 중인지 여부가 표시됩니다. 상태 데이터를 사용할 수 없고 지난 14 일 내에 경고가 수신 되지 않은 경우 Security Center는 솔루션이 비정상 이거나 보고 되지 않음을 나타냅니다.
>
>

1. **보기** 를 선택 하 여 다음과 같은 추가 정보 및 옵션을 선택 합니다.

   - **솔루션 콘솔**. 이 솔루션에 대한 관리 환경을 엽니다.
   - **VM 연결**. 응용 프로그램 연결 페이지를 엽니다. 여기서 리소스를 파트너 솔루션에 연결할 수 있습니다.
   - **솔루션 삭제**
   - **구성**

   ![파트너 솔루션 세부 정보](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>검색된 솔루션

Security Center는 Azure에서 실행 되는 보안 솔루션을 자동으로 검색 하지만 Security Center에 연결 되지 않고 **검색 된 솔루션** 섹션에 솔루션을 표시 합니다. 이러한 솔루션에는 [Azure AD ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)및 파트너 솔루션과 같은 Azure 솔루션이 포함 됩니다.

> [!NOTE]
> Security Center의 표준 계층은 검색된 솔루션 기능의 구독 수준에서 필요합니다. 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md) 을 참조 하세요.
>
>

Security Center와 통합 하기 위해 솔루션에서 **연결** 을 선택 하 고 보안 경고에 대 한 알림을 받습니다.

### <a name="add-data-sources"></a>데이터 원본 추가

**데이터 원본 추가** 섹션에는 연결할 수 있는 다른 사용 가능한 데이터 원본이 포함됩니다. 이러한 원본의 데이터를 추가하는 방법에 대한 지침은 **추가**를 클릭합니다.

![데이터 원본](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>SIEM으로 데이터 내보내기

> [!NOTE]
> 더 간단한 방법 (현재 미리 보기 상태)에서 SIEM으로 데이터를 내보내는 방법에 대 한 자세한 내용은 [보안 경고 및 권장 사항 내보내기 (미리 보기)](continuous-export.md)를 참조 하세요. 새 메서드는 활동 로그를 intermediator 사용 하지 않으며 Security Center에서 Event Hubs (그리고 SIEM으로) 직접 내보내기를 허용 하 고 보안 권장 사항 내보내기도 지원 합니다.


SIEMs 또는 기타 모니터링 도구를 구성 하 여 Azure Security Center 이벤트를 받을 수 있습니다.

Azure Security Center의 모든 이벤트는 Azure Monitor의 Azure [활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)에 게시 됩니다. Azure Monitor는 [통합 파이프라인을](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) 사용 하 여 데이터를 이벤트 허브로 스트리밍하 고 나 서 모니터링 도구로 끌어올 수 있습니다.

다음 섹션에서는 데이터를 이벤트 허브로 스트리밍하도록 구성하는 방법에 대해 설명합니다. 다음 단계에서는 Azure Security Center가 Azure 구독에 이미 구성되어 있다고 가정합니다.

### <a name="high-level-overview"></a>고급 개요

![대략적인 개요](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>SIEM에 공개된 Azure 보안 데이터는 무엇입니까?

이 버전에서는 보안 경고를 노출 [합니다.](../security-center/security-center-managing-and-responding-alerts.md) 이후 릴리스에는 보안 권장 사항이 포함된 데이터 집합을 보강합니다.

### <a name="how-to-set-up-the-pipeline"></a>파이프라인을 설정 하는 방법

#### <a name="create-an-event-hub"></a>이벤트 허브 만들기

시작 하기 전에 모든 모니터링 데이터에 대 한 대상 [Event Hubs 네임 스페이스를 만듭니다](../event-hubs/event-hubs-create.md) .

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Azure 활동 로그를 Event Hubs로 스트림

다음 문서 [스트림 활동 로그를](../azure-monitor/platform/activity-logs-stream-event-hubs.md) 참조 하세요 Event Hubs

#### <a name="install-a-partner-siem-connector"></a>파트너 SIEM 커넥터 설치 

Azure Monitor를 사용하여 모니터링 데이터를 이벤트 허브로 라우팅하면 파트너 SIEM 및 모니터링 도구와 쉽게 통합할 수 있습니다.

[지원 되는 SIEMs](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-platform-logs-sent-to-an-event-hub) 목록은 다음 문서를 참조 하세요.

### <a name="example-for-querying-data"></a>데이터 쿼리 예제 

경고 데이터를 가져오는 데 사용할 수 있는 몇 가지 Splunk 쿼리는 다음과 같습니다.

| **쿼리 설명** | **쿼리** |
|----|----|
| All Alerts| index=main Microsoft.Security/locations/alerts|
| 이름별로 작업 횟수를 요약합니다.| operationName별 index=main sourcetype="amal:security" \| table operationName \| 통계치|
| 경고 정보 얻기: 시간, 이름, 상태, ID 및 구독 | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center에서 파트너 솔루션을 통합하는 방법을 살펴보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Security Center에서 보안 상태 모니터링](security-center-monitoring.md) Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) Security Center 사용에 관한 질문과 대답에 대한 답을 가져옵니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
