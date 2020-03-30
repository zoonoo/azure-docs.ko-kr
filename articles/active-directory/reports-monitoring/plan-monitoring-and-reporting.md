---
title: 모니터링 배포& 계획 보고서 - Azure AD
description: 보고 및 모니터링의 실행을 계획하고 실행하는 방법을 설명합니다.
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
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232117"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Azure Active Directory 보고 및 모니터링 배포 계획

Azure Active Directory(Azure AD) 보고 및 모니터링 솔루션은 법률, 보안 및 운영 요구 사항과 기존 환경 및 프로세스에 따라 달라집니다. 이 문서에서는 다양한 디자인 옵션을 제시하고 올바른 배포 전략에 대한 안내를 제공합니다.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Azure AD 보고 및 모니터링의 이점

Azure AD 보고는 로그인 이벤트, 감사 이벤트 및 디렉터리 변경 사항을 포함하여 사용자 환경에서 Azure AD 활동의 포괄적인 보기 및 로그를 제공합니다.

제공된 데이터를 통해 다음을 수행할 수 있습니다.

* 앱 및 서비스가 사용되는 방식을 결정합니다.

* 환경의 상태에 영향을 미치는 잠재적 위험을 감지할 수 있습니다.

* 문제 해결을 통해 사용자가 작업을 완료할 수 없습니다.

* Azure AD 디렉터리변경에 대한 감사 이벤트를 확인하여 통찰력을 얻을 수 있습니다.

> [!IMPORTANT]
> Azure AD 모니터링을 사용하면 Azure AD 보고에서 생성된 로그를 다른 대상 시스템으로 라우팅할 수 있습니다. 그런 다음, 활동 로그를 장기간 사용할 수 있도록 보존할 수도 있고, 타사 SIEM(보안 정보 및 이벤트 관리) 도구와 통합하여 환경에 대한 인사이트를 얻을 수도 있습니다.

Azure AD 모니터링을 사용하면 로그를 다음으로 라우팅할 수 있습니다.

* 보관을 위한 Azure 저장소 계정입니다.
* Azure Log Analytics 작업 영역이라고 도는 데이터를 분석하고 대시보드를 만들고 특정 이벤트에 대해 경고할 수 있는 Azure Monitor 로그입니다.
* 스플렁크, 수모로직 또는 QRadar와 같은 기존 SIEM 도구와 통합할 수 있는 Azure 이벤트 허브입니다.

> [!NOTE]
최근에 는 로그 분석 대신 Azure Monitor 로그라는 용어를 사용하기 시작했습니다. 로그 데이터는 여전히 Log Analytics 작업 영역에 저장되며 동일한 Log Analytics 서비스에 의해 계속 수집 및 분석됩니다. [Azure Monitor에서 로그](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection)의 역할을 보다 잘 반영하기 위해 용어를 업데이트하고 있습니다. 자세한 내용은 [Azure Monitor 용어 변경](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand)을 참조하세요.

[보고서 보존 정책에 대해 자세히 알아보세요.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD 보고 및 모니터링을 위한 라이선스 및 필수 구성

Azure AD 로그인 로그인 로그인에 액세스하려면 Azure AD 프리미엄 라이선스가 필요합니다.

[Azure Active Directory 가격 책정 가이드에서](https://azure.microsoft.com/pricing/details/active-directory/)자세한 기능 및 라이선스 정보를 보려면

Azure AD 모니터링 및 보고를 배포하려면 Azure AD 테넌트의 글로벌 관리자 또는 보안 관리자인 사용자가 필요합니다.

로그 데이터의 최종 대상에 따라 다음 중 하나가 필요합니다.

* ListKeys 권한이 있는 Azure Storage 계정. Blob Storage 계정이 아닌 일반 스토리지 계정을 사용하는 것이 좋습니다. 스토리지 가격 책정 정보는 [Azure Storage 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=storage)를 참조하세요.

* 타사 SIEM 솔루션과 통합할 Azure 이벤트 허브 네임스페이스입니다.

* Azure Monitor 로그에 로그를 보내는 Azure Log Analytics 작업 영역

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Azure 보고 및 모니터링 배포 프로젝트 계획

이 프로젝트에서는 보고서를 사용하고 모니터링할 대상을 정의하고 Azure AD 모니터링 아키텍처를 정의합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 이해 관계자 참여

기술 프로젝트가 실패하면 일반적으로 영향, 결과 및 책임에 대한 불일치 기대로 인해 그렇게 합니다. 이러한 함정을 방지하려면 [올바른 이해 관계자를 참여시키는지 확인하십시오.](https://aka.ms/deploymentplans) 또한 이해 관계자와 프로젝트 입력 및 책임을 문서화하여 프로젝트의 이해 관계자 역할을 잘 이해할 수 있도록 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새로운 서비스의 성공에 매우 중요합니다. 사용자 환경이 어떻게 변할지, 언제 변경될지, 문제가 발생할 경우 지원을 받는 방법을 사전에 사용자와 전달합니다.

### <a name="document-your-current-infrastructure-and-policies"></a>현재 인프라 및 정책 문서화

현재 인프라및정책은보고및모니터링설계를 촉진합니다. 알고 있는지 확인

* 사용 중이면 SIEM 도구는 무엇입니까?

* 사용 중인 기존 저장소 계정 및 모니터링을 비롯한 Azure 인프라입니다.

* 필요한 적용 가능한 규정 준수 프레임워크를 포함하여 로그에 대한 조직 보존 정책입니다. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Azure AD 보고 및 모니터링 배포 계획

보고 및 모니터링은 비즈니스 요구 사항을 충족하고, 사용 패턴에 대한 통찰력을 얻고, 조직의 보안 태세를 높이는 데 사용됩니다.

### <a name="business-use-cases"></a>비즈니스 사용 사례

* 비즈니스 요구 사항을 충족하는 솔루션에 필요한 경우
* 비즈니스 요구 사항을 충족해야 하는 좋은
* 해당 없음

|영역 |설명 |
|-|-|
|보존| **30일 이상의 로그 보존.** 법적 또는 비즈니스 요구 사항으로 인해 감사 로그를 저장하고 30일 이상 Azure AD의 로그인에 로그인해야 합니다. |
|분석| **로그를 검색할 수 있어야 합니다.** 저장된 로그는 분석 도구를 사용하여 검색할 수 있어야 합니다. |
| Operational Insights| **다양한 팀에 대한 통찰력**. 응용 프로그램 사용, 로그인 오류, 셀프 서비스 사용, 추세 등과 같은 운영 통찰력을 얻기 위해 다양한 사용자에게 액세스 권한을 부여해야 합니다. |
| 보안 인사이트| **다양한 팀에 대한 통찰력**. 응용 프로그램 사용, 로그인 오류, 셀프 서비스 사용, 추세 등과 같은 운영 통찰력을 얻기 위해 다양한 사용자에게 액세스 권한을 부여해야 합니다. |
| SIEM 시스템의 통합      | **SIEM 통합**. Azure AD 로그인 로그를 통합하고 스트리밍해야 하며 로그를 기존 SIEM 시스템에 감사해야 합니다. |

### <a name="choose-a-monitoring-solution-architecture"></a>모니터링 솔루션 아키텍처 선택

Azure AD 모니터링을 사용하면 Azure AD 활동 로그를 비즈니스 요구 사항을 가장 잘 충족하는 시스템으로 라우팅할 수 있습니다. 그런 다음 장기 보고 및 분석을 위해 유지하여 환경에 대한 통찰력을 얻고 SIEM 도구와 통합할 수 있습니다.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>의사 결정 흐름 차트![후속 섹션에서 설명된 내용을 보여 주면 됩니다.](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>저장소 계정에 로그 보관

Azure 저장소 계정에 로그를 라우팅하면 [보존 정책에](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)설명된 기본 보존 기간보다 더 오래 유지할 수 있습니다. 로그를 보관해야 하지만 SIEM 시스템과 통합할 필요가 없고 지속적인 쿼리 및 분석이 필요하지 않은 경우 이 방법을 사용합니다. 주문형 검색을 계속 할 수 있습니다.

[데이터를 스토리지 계정으로 라우팅](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)하는 방법을 알아보세요.

#### <a name="send-logs-to-azure-monitor-logs"></a>Azure Monitor 로그에 로그 보내기

[Azure Monitor 로그는](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 서로 다른 원본의 모니터링 데이터를 통합합니다. 또한 응용 프로그램 운영 및 리소스 사용에 대한 통찰력을 제공하는 쿼리 언어 및 분석 엔진도 제공합니다. Azure AD 활동 로그를 Azure Monitor 로그로 보내면 수집된 데이터를 신속하게 검색, 모니터링 및 경고할 수 있습니다. 데이터를 직접 전송하고 싶지만 쿼리 및 분석을 원하는 기존 SIEM 솔루션이 없는 경우 이 방법을 사용합니다. 데이터가 Azure Monitor 로그에 있으면 이벤트 허브로 보내고 원하는 경우 SIEM으로 보낼 수 있습니다.

[데이터를 Azure Monitor 로그에 보내는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)에 대해 알아보세요.

Azure AD 활동 로그에 대해 미리 빌드된 보기를 설치하여 로그인 및 감사 이벤트와 관련된 일반적인 시나리오를 모니터링할 수도 있습니다.

[Azure AD 활동 로그에 대한 로그 분석 보기를 설치하고 사용하는](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)방법에 대해 알아봅니다.

#### <a name="stream-logs-to-your-azure-event-hub"></a>Azure 이벤트 허브로 로그 스트리밍

Azure 이벤트 허브로 로그를 라우팅하면 타사 SIEM 도구와 통합할 수 있습니다. 이 통합을 통해 Azure AD 활동 로그 데이터를 SIEM에서 관리하는 다른 데이터와 결합하여 환경에 보다 풍부한 인사이트를 얻을 수 있습니다. 

[로그를 이벤트 허브로 스트리밍](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)하는 방법을 알아보세요.

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Azure AD 보고 및 모니터링을 위한 운영 및 보안 계획

이해 관계자는 운영 통찰력을 얻기 위해 Azure AD 로그에 액세스해야 합니다. 보안 팀 구성원, 내부 또는 외부 감사자, ID 및 액세스 관리 운영 팀이 포함될 수 있습니다.

Azure AD 역할을 사용하면 역할에 따라 Azure AD 보고서를 구성하고 볼 수 있는 기능을 위임할 수 있습니다. 조직에서 Azure AD 보고서를 읽을 수 있는 권한이 필요한 사용자및 해당 보고서에 적합한 역할을 식별합니다. 

다음 역할은 Azure AD 보고서를 읽을 수 있습니다.

* 전역 관리자

* 보안 관리자

* 보안 판독기

* 보고서를 읽는 사람

[Azure AD 관리 역할에](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)대해 자세히 알아보기.

*항상 계정 손상의 위험을 줄이기 위해 최소 권한의 개념을 적용합니다.* 조직을 더욱 안전하게 보호하기 위해 [권한 있는 ID 관리를](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 구현하는 것이 좋습니다.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Azure AD 보고 및 모니터링 배포

위의 디자인 지침을 사용하여 이전에 내린 결정에 따라 이 섹션에서는 다양한 배포 옵션에 대한 설명서를 안내합니다.

### <a name="consume-and-archive-azure-ad-logs"></a>Azure AD 로그 사용 및 보관

[Azure Portal에서 작업 보고서 찾기](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Azure 저장소 계정에 Azure AD 로그 보관](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>모니터링 및 분석 구현

[Azure 모니터로 로그 보내기](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Azure Active Directory에 대한 로그 분석 보기 설치 및 사용](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Azure Monitor 로그를 사용하여 Azure AD 활동 로그 분석](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Azure 모니터에서 감사 로그 스키마 해석](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Azure 모니터에서 로그인 스키마 해석](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Azure AD 로그를 Azure 이벤트 허브로 스트리밍](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Azure Monitor를 사용하여 Splunk와 Azure AD 로그 통합](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [Azure Monitor를 사용하여 SumoLogic과 Azure AD 로그 통합](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>다음 단계

권한 [있는 ID 관리](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 구현 고려 

[RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/overview) 구현 고려

 
