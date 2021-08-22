---
title: 보고서 및 모니터링 배포 계획 - Azure AD
description: 보고 및 모니터링의 구현을 계획하고 실행하는 방법을 설명합니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: f831acacfd1aaf2248e7993c3e7161cad62312b3
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733403"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Azure Active Directory 보고 및 모니터링 배포 계획

Azure AD(Azure Active Directory) 보고 및 모니터링 솔루션은 법적, 보안 및 운영 요구 사항과 기존 환경 및 프로세스에 따라 달라집니다. 이 문서에서는 다양한 설계 옵션을 제시하고 올바른 배포 전략을 안내합니다.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Azure AD 보고 및 모니터링의 이점

Azure AD 보고는 로그인 이벤트, 감사 이벤트 및 디렉터리 변경 내용을 포함하여 여러분의 환경에 Azure AD 활동에 대한 포괄적인 보기 및 로그를 제공합니다.

제공된 데이터를 통해 다음을 수행할 수 있습니다.

* 앱과 서비스의 사용 방법을 결정합니다.

* 환경의 상태에 영향을 줄 수 있는 위험을 탐지합니다.

* 사용자의 작업 진행에 장애가 되는 문제를 해결합니다.

* Azure AD 디렉터리에 대한 변경 내용 감사 이벤트를 확인하여 인사이트를 얻습니다.

> [!IMPORTANT]
> Azure AD 모니터링을 사용하면 Azure AD 보고에서 생성된 로그를 다른 대상 시스템으로 라우팅할 수 있습니다. 그런 다음, 활동 로그를 장기간 사용할 수 있도록 보존할 수도 있고, 타사 SIEM(보안 정보 및 이벤트 관리) 도구와 통합하여 환경에 대한 인사이트를 얻을 수도 있습니다.

Azure AD 모니터링을 사용하면 로그를 다음으로 라우팅할 수 있습니다.

* 보관용 Azure Storage 계정
* Azure Monitor 로그(이전의 Azure Log Analytics 작업 영역). 데이터를 분석하고, 대시보드를 만들며, 특정 이벤트에 대해 경고할 수 있습니다.
* Azure 이벤트 허브. Splunk, Sumologic 또는 QRadar와 같은 기존 SIEM 도구와 통합할 수 있습니다.

> [!NOTE]
> 최근에 Log Analytics 대신 Azure Monitor 로그라는 용어를 사용하기 시작했습니다. 로그 데이터는 여전히 Log Analytics 작업 영역에 저장되며 동일한 Log Analytics 서비스에 의해 계속 수집 및 분석됩니다. [Azure Monitor에서 로그](../../azure-monitor/data-platform.md)의 역할을 보다 잘 반영하기 위해 용어를 업데이트하고 있습니다. 자세한 내용은 [Azure Monitor 용어 변경](../../azure-monitor/terminology.md)을 참조하세요.

[보고서 보존 정책에 대해 자세히 알아보세요](./reference-reports-data-retention.md).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD 보고 및 모니터링을 위한 라이선스 및 필수 구성 요소

Azure AD 로그인 로그에 액세스하려면 Azure AD 프리미엄 라이선스가 필요합니다.

[Azure Active Directory 가격 책정 가이드](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)의 자세한 기능 및 라이선스 정보를 참조하세요.

Azure AD 모니터링 및 보고를 배포하려면 Azure AD 테넌트의 전역 관리자 또는 보안 관리자인 사용자가 필요합니다.

로그 데이터의 최종 대상에 따라 다음 중 하나가 필요합니다.

* ListKeys 권한이 있는 Azure Storage 계정. Blob Storage 계정이 아닌 일반 스토리지 계정을 사용하는 것이 좋습니다. 스토리지 가격 책정 정보는 [Azure Storage 가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/?service=storage)를 참조하세요.

* 타사 SIEM 솔루션과 통합할 Azure Event Hubs 네임스페이스.

* Azure Monitor 로그에 로그를 보내는 Azure Log Analytics 작업 영역

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Azure 보고 및 모니터링 배포 프로젝트 계획

이 프로젝트에서는 보고서를 사용하고 모니터링할 대상을 정의하고 Azure AD 모니터링 아키텍처를 정의합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자가 참여](../fundamentals/active-directory-deployment-plans.md)해야 합니다. 또한 관련자와 해당 프로젝트 입력 및 책임을 문서화하여 프로젝트의 관련자 역할을 숙지해야 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자의 경험이 어떻게 변화하고 언제 변할 것인지, 문제가 발생할 경우 지원을 받는 방법에 대해 사용자와 사전에 소통합니다.

### <a name="document-your-current-infrastructure-and-policies"></a>현재 인프라 및 정책 문서화

현재 인프라 및 정책은 보고 및 모니터링 설계의 기반이 됩니다. 다음을 숙지해야 합니다.

* 사용 중인 SIEM 도구(있는 경우).

* 기존 스토리지 계정 및 사용 중인 모니터링을 포함한 Azure 인프라.

* 필요한 규정 준수 프레임워크를 포함한 조직의 로그 보존 정책. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Azure AD 보고 및 모니터링 배포 계획

보고 및 모니터링은 비즈니스 요구 사항을 충족하고, 사용 패턴에 대한 인사이트를 얻고, 조직의 보안 태세를 강화하는 데 사용됩니다.

### <a name="business-use-cases"></a>비즈니스 사용 사례

* 솔루션이 비즈니스 요구 사항을 충족하려면 필수적임
* 비즈니스 요구 사항 충족을 위해 권장
* 해당 사항 없음

|영역 |Description |
|-|-|
|보존| **로그 보존 기간 30일 초과**. ‎법적 또는 비즈니스 요구 사항으로 인해 Azure AD의 감사 로그와 로그인 로그를 30일을 초과하여 저장해야 합니다. |
|분석| **검색 가능한 로그.** ‎저장된 로그는 분석 도구를 사용하여 검색할 수 있어야 합니다. |
| Operational Insights| **다양한 팀에 인사이트 제공.** 애플리케이션 사용량, 로그인 오류, 셀프 서비스 사용량, 추세 등의 운영 인사이트를 얻을 수 있도록 다른 사용자에게 액세스 권한을 부여해야 합니다. |
| 보안 인사이트| **다양한 팀에 인사이트 제공.** 애플리케이션 사용량, 로그인 오류, 셀프 서비스 사용량, 추세 등의 운영 인사이트를 얻을 수 있도록 다른 사용자에게 액세스 권한을 부여해야 합니다. |
| SIEM 시스템의 통합      | **SIEM 통합**. ‎Azure AD 로그인 로그와 감사 로그를 기존 SIEM 시스템에 통합하고 스트리밍해야 합니다. |

### <a name="choose-a-monitoring-solution-architecture"></a>모니터링 솔루션 아키텍처 선택

Azure AD 모니터링을 사용하면 Azure AD 활동 로그를 비즈니스 요구 사항을 가장 잘 충족하는 시스템으로 라우팅할 수 있습니다. 그러면 장기 보고와 분석을 위해 보존하여 사용자 환경에 대한 인사이트를 얻을 수 있고, SIEM 도구와 통합할 수도 있습니다.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>의사 결정 흐름 차트![후속 섹션에서 설명하는 내용을 보여 주는 이미지](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>스토리지 계정에 로그 보관

Azure Storage 계정으로 로그를 라우팅하면 [보존 정책](./reference-reports-data-retention.md)에 설명된 기본 보존 기간보다 더 오래 보존할 수 있습니다. 로그를 보관해야 하지만 SIEM 시스템과 통합할 필요가 없고 지속적인 쿼리와 분석이 필요하지 않은 경우에 이 방법을 사용합니다. 주문형 검색을 계속 사용해도 됩니다.

[데이터를 스토리지 계정으로 라우팅](./quickstart-azure-monitor-route-logs-to-storage-account.md)하는 방법을 알아보세요.

#### <a name="send-logs-to-azure-monitor-logs"></a>Azure Monitor 로그에 로그 보내기

[Azure Monitor 로그](../../azure-monitor/logs/log-query-overview.md)는 다양한 원본의 모니터링 데이터를 통합합니다. 또한 애플리케이션 작업과 리소스 사용에 대한 인사이트를 제공하는 쿼리 언어와 분석 엔진을 제공합니다. Azure AD 활동 로그를 Azure Monitor 로그에 보내면 수집된 데이터를 빠르게 검색, 모니터링, 경고할 수 있습니다. 데이터를 직접 보낼 기존 SIEM 솔루션이 없지만 쿼리와 분석을 원하는 경우 이 방법을 사용합니다. 데이터가 Azure Monitor 로그에 있으면 이를 이벤트 허브로 전송하고 원하는 경우 해당 위치에서 SIEM으로 보낼 수 있습니다.

[데이터를 Azure Monitor 로그에 보내는 방법](./howto-integrate-activity-logs-with-log-analytics.md)에 대해 알아보세요.

Azure AD 활동 로그용으로 미리 작성된 보기를 설치하여 로그인 및 감사 이벤트가 관련된 일반적인 시나리오를 모니터링할 수 있습니다.

[Azure AD 활동 로그에 대한 로그 분석 보기 설치 및 사용](./howto-install-use-log-analytics-views.md) 방법에 대해 알아보세요.

#### <a name="stream-logs-to-your-azure-event-hub"></a>Azure 이벤트 허브로 로그 스트리밍

로그를 Azure 이벤트 허브로 라우팅하면 타사 SIEM 도구와의 통합이 가능합니다. 이 통합을 통해 Azure AD 활동 로그 데이터를 SIEM에서 관리하는 다른 데이터와 결합하여 환경에 보다 풍부한 인사이트를 얻을 수 있습니다. 

[로그를 이벤트 허브로 스트리밍](./tutorial-azure-monitor-stream-logs-to-event-hub.md)하는 방법을 알아보세요.

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Azure AD 보고와 모니터링에 대한 작업 및 보안 계획

관련자는 운영 인사이트를 얻으려면 Azure AD 로그에 액세스해야 합니다. 보안 팀원, 내부 또는 외부 감사자, ID, 액세스 관리 운영 팀이 사용자에 포함될 수 있습니다.

Azure AD 역할을 사용하면 역할에 따라 Azure AD 보고서를 구성하고 보는 권한을 위임할 수 있습니다. 조직에서 Azure AD 보고서 읽기 권한이 필요한 사용자와 해당 사용자에게 적합한 역할을 파악합니다. 

다음 역할은 Azure AD 보고서를 읽을 수 있습니다.

* 전역 관리자

* 보안 관리자

* 보안 판독기

* 보고서 읽기 권한자

[Azure AD 관리자 역할](../roles/permissions-reference.md)에 대해 자세히 알아보세요.

‘계정 손상의 위험을 줄이려면 항상 최소 권한 개념을 적용’합니다. [Privileged Identity Management](../privileged-identity-management/pim-configure.md)를 구현하여 조직의 보안을 강화하는 것이 좋습니다.


## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Azure AD 보고 및 모니터링 배포

위의 설계 지침을 사용하여 이전에 결정한 사항에 따라 이 섹션에서는 다양한 배포 옵션에 대한 설명서를 안내합니다.

### <a name="consume-and-archive-azure-ad-logs"></a>Azure AD 로그 사용 및 보관

[Azure Portal에서 작업 보고서 찾기](./howto-find-activity-reports.md)

[Azure Storage 계정에 Azure AD 로그 보관](./quickstart-azure-monitor-route-logs-to-storage-account.md)

### <a name="implement-monitoring-and-analytics"></a>모니터링 및 분석 구현

[Azure Monitor로 로그 보내기](./howto-integrate-activity-logs-with-log-analytics.md)

[Azure Active Directory용 로그 분석 보기 설치 및 사용](./howto-install-use-log-analytics-views.md)

[Azure Monitor 로그를 사용하여 Azure AD 활동 로그 분석](./howto-analyze-activity-logs-log-analytics.md)

* [Azure Monitor에서 감사 로그 스키마 해석](./overview-reports.md)

* [Azure Monitor에서 로그인 로그 스키마 해석](./reference-azure-monitor-sign-ins-log-schema.md)

 * [Azure 이벤트 허브로 Azure AD 로그 스트리밍](./tutorial-azure-monitor-stream-logs-to-event-hub.md)

* [Azure Monitor를 사용하여 Splunk와 Azure AD 로그 통합](./howto-integrate-activity-logs-with-splunk.md)

* [Azure Monitor를 사용하여 SumoLogic과 Azure AD 로그 통합](./howto-integrate-activity-logs-with-sumologic.md)

 

 

## <a name="next-steps"></a>다음 단계

[Privileged Identity Management](../privileged-identity-management/pim-configure.md) 구현 고려 사항 

[Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 구현 고려 사항