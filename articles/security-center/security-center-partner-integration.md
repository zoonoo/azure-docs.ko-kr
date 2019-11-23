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
ms.openlocfilehash: 435ae9f08f718a9310fd1687fb7859058edf8b45
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384255"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Security Center에서 보안 솔루션 통합
이 문서를 통해 이미 Azure Security Center에 연결된 보안 솔루션을 관리하고 새로 추가할 수 있습니다.

> [!NOTE]
> A subset of security solutions has been retired on July 31st, 2019. For more information and alternative services, see [Retirement of Security Center features (July 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>통합된 Azure 보안 솔루션
Security Center를 사용하면 Azure에서 통합된 보안 솔루션을 쉽게 사용할 수 있습니다. 제공되는 혜택:

- **배포 간소화**: Security Center에서는 통합된 파트너 솔루션의 간결한 프로비전을 제공합니다. For solutions like antimalware and vulnerability assessment, Security Center can provision the agent on your virtual machines. For firewall appliances, Security Center can take care of much of the network configuration required.
- **통합된 감지**: 파트너 솔루션의 보안 이벤트는 자동으로 수집, 집계되며 Security Center 알림 및 사고의 일부로 표시됩니다. 또한 이러한 이벤트는 다른 원본의 감지를 결합하여 고급 위협 감지 기능을 제공합니다.
- **통합된 상태 모니터링 및 관리**: 고객은 통합된 상태 이벤트를 사용하여 한 눈에 모든 파트너 솔루션을 모니터링할 수 있습니다. 기본 관리는 파트너 솔루션을 사용하여 고급 설정에 쉽게 액세스하여 사용할 수 있습니다.

Currently, integrated security solutions include vulnerability assessment by [Qualys](https://www.qualys.com/public-cloud/#azure) and [Rapid7](https://www.rapid7.com/products/insightvm/) and Microsoft Application Gateway Web application firewall.

> [!NOTE]
> Security Center does not install the Microsoft Monitoring Agent on partner virtual appliances because most security vendors prohibit external agents running on their appliances.
>
>

## <a name="how-security-solutions-are-integrated"></a>보안 솔루션을 통합하는 방법
Security Center에서 배포된 Azure 보안 솔루션은 자동으로 연결됩니다. You can also connect other security data sources, including computers running on-premises or in other clouds.

![파트너 솔루션 통합](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>통합된 Azure 보안 솔루션 및 다른 데이터 원본 관리

1. [Azure portal](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.

2. **Microsoft Azure 메뉴**에서 **Security Center**를 선택합니다. **Security Center - 개요**가 열립니다.

3. Security Center 메뉴에서 **보안 솔루션**을 선택합니다.

   ![Security Center 개요](./media/security-center-partner-integration/overview.png)

In **Security solutions**, you can see the health of integrated Azure security solutions and run basic management tasks.

### <a name="connected-solutions"></a>연결된 솔루션

The **Connected solutions** section includes security solutions that are currently connected to Security Center. It also shows the health status of each solution.  

![연결된 솔루션](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

파트너 솔루션의 상태는 다음과 같을 수 있습니다.

* Healthy (green) - no health issues.
* Unhealthy (red) - there's a health issue that requires immediate attention.
* 상태 문제(주황색) - 솔루션이 상태의 보고를 중지했습니다.
* Not reported (gray) - the solution hasn't reported anything yet and no health data is available. A solution's status may be unreported if it was connected recently and is still deploying.

> [!NOTE]
> 상태 데이터를 사용할 수 없는 경우 Security Center에 마지막으로 수신된 이벤트의 날짜와 시간을 통해 솔루션이 보고 중인지 여부가 표시됩니다. If no health data is available and no alerts were received within the last 14 days, Security Center indicates that the solution is unhealthy or not reporting.
>
>

1. Select **VIEW** for additional information and options such as:

   - **솔루션 콘솔**. 이 솔루션에 대한 관리 환경을 엽니다.
   - **VM 연결**. Opens the Link Applications page. 여기서 리소스를 파트너 솔루션에 연결할 수 있습니다.
   - **솔루션 삭제**
   - **구성**

   ![파트너 솔루션 세부 정보](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>검색된 솔루션

Security Center automatically discovers security solutions running in Azure but not connected to Security Center and displays the solutions in the **Discovered solutions** section. These  solutions include Azure solutions, like [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), and partner solutions.

> [!NOTE]
> Security Center의 표준 계층은 검색된 솔루션 기능의 구독 수준에서 필요합니다. See [Pricing](security-center-pricing.md) to learn more about the pricing tiers.
>
>

Select **CONNECT** under a solution to integrate with Security Center and be notified of security alerts.

### <a name="add-data-sources"></a>데이터 원본 추가

**데이터 원본 추가** 섹션에는 연결할 수 있는 다른 사용 가능한 데이터 원본이 포함됩니다. 이러한 원본의 데이터를 추가하는 방법에 대한 지침은 **추가**를 클릭합니다.

![데이터 원본](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>SIEM으로 데이터 내보내기

You can configure your SIEMs or other monitoring tools to receive Azure Security Center events.

All events from Azure Security Center are published to Azure Monitor's Azure [Activity log](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Azure Monitor uses [a consolidated pipeline](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) to  stream the data to an Event Hub where it can then be pulled into your monitoring tool.

다음 섹션에서는 데이터를 이벤트 허브로 스트리밍하도록 구성하는 방법에 대해 설명합니다. 다음 단계에서는 Azure Security Center가 Azure 구독에 이미 구성되어 있다고 가정합니다.

### <a name="high-level-overview"></a>고급 개요

![대략적인 개요](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>SIEM에 공개된 Azure 보안 데이터는 무엇입니까?

In this version, we expose the [security alerts.](../security-center/security-center-managing-and-responding-alerts.md) 이후 릴리스에는 보안 권장 사항이 포함된 데이터 집합을 보강합니다.

### <a name="how-to-set-up-the-pipeline"></a>How to set up the pipeline

#### <a name="create-an-event-hub"></a>이벤트 허브 만들기

Before you begin, [create an Event Hubs namespace](../event-hubs/event-hubs-create.md) - the destination for all your monitoring data.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Azure 활동 로그를 Event Hubs로 스트림

See the following article [stream activity log to Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>파트너 SIEM 커넥터 설치 

Azure Monitor를 사용하여 모니터링 데이터를 이벤트 허브로 라우팅하면 파트너 SIEM 및 모니터링 도구와 쉽게 통합할 수 있습니다.

See the following article for the list of [supported SIEMs](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub)

### <a name="example-for-querying-data"></a>데이터 쿼리 예제 

Here are some Splunk queries you can use to pull alert data:

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
