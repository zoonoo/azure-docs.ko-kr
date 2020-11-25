---
title: Azure AD B2C 사용 하 여 모니터링 및 분석을 통한 복원 력 Microsoft Docs
description: Azure AD B2C를 사용한 모니터링 및 분석을 통한 복원 력
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb63931f883c6061ded996621a09aacbacd14af
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919847"
---
# <a name="resilience-through-monitoring-and-analytics"></a>모니터링 및 분석을 통한 복원 력

모니터링은 응용 프로그램 및 서비스의 가용성과 성능을 극대화 합니다. 인프라 및 응용 프로그램에서 원격 분석을 수집, 분석 및 작동 하기 위한 종합적인 솔루션을 제공 합니다. 경고는 사용자의 서비스 또는 응용 프로그램에서 문제가 발견 될 때 사용자에 게 사전에 알려 줍니다. 이를 통해 최종 사용자가 서비스를 확인 하기 전에 문제를 식별 하 고 해결할 수 있습니다. [AZURE AD Log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) 를 사용 하 여 분석 하 고, 감사 로그와 로그인 로그를 검색 하 고, 사용자 지정 보기를 작성할 수 있습니다.

## <a name="monitor-and-get-notified-through-alerts"></a>경고 모니터링 및 알림 받기

시스템 및 인프라를 모니터링 하는 것은 서비스의 전반적인 상태를 확인 하는 데 매우 중요 합니다. 새 사용자 도착, 최종 사용자의 인증 속도 및 변환과 같은 비즈니스 메트릭의 정의로 시작 합니다. 모니터링할 이러한 표시기를 구성 합니다. 프로 모션 또는 휴일 트래픽으로 인해 예정 된 서 수를 계획 하는 경우 비즈니스 메트릭에 대 한 이벤트 및 해당 벤치 마크에 대해 구체적으로 예측을 수정 합니다. 이벤트가 발생 한 후 이전 벤치 마크로 대체 합니다.

마찬가지로, 오류 또는 성능 문제를 감지 하려면 적절 한 기준선을 설정 하 고 경고를 정의 하는 것이 새로운 문제에 즉시 대응 하기 위한 필수적인 방법입니다.

![모니터링 및 분석 구성 요소를 보여 주는 이미지](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>모니터링 및 경고를 구현 하는 방법

- **모니터링**: [Azure Monitor](https://docs.microsoft.com/azure/active-directory-b2c/azure-monitor) 을 사용 하 여 주요 SLO (서비스 수준 목표)에 대해 상태를 지속적으로 모니터링 하 고 중요 한 변경이 발생할 때마다 알림을 받습니다. 먼저 SLO를 유지 하기 위해 상태를 모니터링 해야 하는 비즈니스의 중요 한 구성 요소로 Azure AD B2C 정책 또는 응용 프로그램을 식별 합니다. Slo와 일치 하는 주요 표시기를 식별 합니다.
예를 들어 다음 메트릭을 추적 합니다 .이 경우에는 갑작스러운 삭제로 인해 비즈니스 손실이 발생 하기 때문입니다.

  - **총 요청** 수: Azure AD B2C 정책에 전송 된 총 "n" 개의 요청 수입니다.

  - **성공 비율 (%)**: 성공한 요청 수/총 요청 수입니다.

  Azure AD B2C 정책 기반 로그, [감사 로그](https://docs.microsoft.coms/azure/active-directory-b2c/analytics-with-application-insights)및 로그인 로그가 저장 되는 [application insights](https://docs.microsoft.com/azure/active-directory-b2c/analytics-with-application-insights) 의 [키 표시기](https://docs.microsoft.com/azure/active-directory-b2c/view-audit-logs) 에 액세스 합니다.  

   - **시각화**: Log analytics 빌드 대시보드를 사용 하 여 키 표시기를 시각적으로 모니터링 합니다.

   - **현재 기간**: 전체 요청 및 성공률 (%)의 변경 내용을 표시 하는 임시 차트를 만듭니다. 현재 기간에서 (예: 현재 주)

   - **이전 기간**: 전체 요청 및 성공률 (%)의 변경 내용을 표시 하는 임시 차트 만들기 이전 기간 동안 (예: 지난 주)

- **경고**: log analytics를 사용 하면 키 표시기가 급격 하 게 변경 될 때 트리거되는 [경고](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) 를 정의 합니다. 이러한 변경으로 인해 Slo에 부정적인 영향을 줄 수 있습니다. 경고는 전자 메일, SMS 및 웹 후크를 비롯 한 다양 한 형태의 알림 방법을 사용 합니다. 먼저 경고가 트리거되는 임계값으로 작동 하는 조건을 정의 합니다. 예들 들어 다음과 같습니다.
  - 전체 요청에 대 한 갑작스러운 삭제에 대 한 경고: 총 요청 수가 갑자기 삭제 될 때 경고를 트리거합니다. 예를 들어 이전 기간과 비교 하 여 총 요청 수에 25% 놓기가 있으면 경고를 발생 시킵니다.  
  - 성공 율의 중요 한 삭제에 대 한 경고 (%): 선택한 정책의 성공률을 크게 떨어지면 경고를 트리거합니다.
  - 경고가 수신 되 면 Azure AD B2C에 대 한 [Log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views), [Application Insights](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-with-application-insights#:~:text=Setup%20Application%20Insights%201%20Go%20to%20the%20Azure,left-menu%2C%20and%20click%20on%20it.%20More%20items...%20)및 [VS Code 확장](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) 을 사용 하 여 문제를 해결 합니다. 문제를 해결 하 고 업데이트 된 응용 프로그램 또는 정책을 배포한 후에는 정상 범위로 돌아갈 때까지 키 표시기를 계속 모니터링 합니다.

- **서비스 경고**: 서비스 [수준 경고 Azure AD B2C](https://docs.microsoft.com/azure/service-health/service-health-overview) 를 사용 하 여 서비스 문제, 계획 된 유지 관리, 상태 자문 및 보안 권고에 대 한 알림을 받습니다.

- **보고**: [log analytics를 사용 하 여](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)사용자 정보, 기술 과제 및 성장 기회를 이해 하는 데 도움이 되는 보고서를 작성 합니다.
  - **상태 대시보드**: Log Analytics 쿼리를 사용 하 여 차트를 추가할 수 있도록 하는 [Azure 대시보드 기능을 사용 하 여 사용자 지정 대시보드](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards) 를 만듭니다. 예를 들어 요청을 수행 하는 데 사용 된 장치에 대 한 성공 및 실패 한 로그인, 실패 원인 및 원격 분석 패턴을 식별 합니다.
  - **Azure AD B2C 경험 중단**: [통합 문서](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) 를 사용 하 여 사용자가 로그인 또는 등록 과정을 시작 했지만 완료 하지 않은 중단 된 Azure AD B2C 경험 목록을 추적 합니다. 이를 통해 사용자가 사용 하는 작업의 정책 ID 및 분석에 대 한 세부 정보를 제공 합니다.
  - **Azure AD B2C 모니터링 통합 문서**: Azure AD B2C 대시보드, MFA (multi-factor authentication) 작업, 조건부 액세스 보고서 및 CorrelationId 별 검색 로그를 포함 하는 [모니터링 통합 문서](https://github.com/azure-ad-b2c/siem)를 사용 하 여 Azure AD B2C 환경의 상태에 대 한 정보를 더 잘 파악할 수 있습니다.
  
## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 개발자를 위한 복원 력 리소스](resilience-b2c.md)
  - [복원 력 최종 사용자 환경](resilient-end-user-experience.md)
  - [외부 프로세스를 사용 하는 복원 인터페이스](resilient-external-processes.md)
  - [개발자 모범 사례를 통한 복원 력](resilience-b2c-developer-best-practices.md)
- [인증 인프라의 빌드 복원 력](resilience-in-infrastructure.md)
- [응용 프로그램에서 인증 및 권한 부여의 복원 력 향상](resilience-app-development-overview.md)
