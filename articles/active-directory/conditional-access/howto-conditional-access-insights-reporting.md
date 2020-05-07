---
title: 조건부 액세스 정보 및 보고 통합 문서-Azure Active Directory
description: Azure AD 조건부 액세스 정보 및 보고 통합 문서를 사용 하 여 정책 문제 해결
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4113328a5de02c36b7285c837bd5314d11e526b
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690429"
---
# <a name="conditional-access-insights-and-reporting"></a>조건부 액세스 정보 및 보고

조건부 액세스 정보 및 보고 통합 문서를 사용 하면 시간에 따라 조직에서 조건부 액세스 정책의 영향을 이해할 수 있습니다. 로그인 하는 동안 특정 권한 부여 컨트롤이 만족 하거나 액세스를 거부 하는 경우 액세스 권한을 부여 하는 하나 이상의 조건부 액세스 정책이 적용 될 수 있습니다. 각 로그인 중에 여러 조건부 액세스 정책을 평가할 수 있으므로 정보 및 보고 통합 문서를 사용 하면 개별 정책의 영향 또는 모든 정책의 하위 집합을 검사할 수 있습니다.  

## <a name="prerequisites"></a>사전 요구 사항

Insights 및 보고 통합 문서를 사용 하려면 테 넌 트에 로그인 로그 데이터를 유지 하기 위한 Log Analytics 작업 영역이 있어야 합니다. 조건부 액세스를 사용 하려면 사용자에 게 Azure AD Premium P1 또는 P2 라이선스가 있어야 합니다.

다음 역할은 정보 및 보고에 액세스할 수 있습니다.  

- 조건부 액세스 관리자 
- 보안 판독기 
- 보안 관리자 
- 전역 판독기 
- 전역 관리자 

사용자는 또한 다음 Log Analytics 작업 영역 역할 중 하나가 필요 합니다.  

- 판독기 
- 모니터링 판독기 
- Log Analytics 판독기 
- 참가자  
- 소유자 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Azure AD에서 Azure Monitor 로그로 로그인 로그 스트림 

Azure AD 로그를 Azure Monitor 로그와 통합 하지 않은 경우 통합 문서를 로드 하기 전에 다음 단계를 수행 해야 합니다.  

1. [Azure Monitor에서 Log Analytics 작업 영역을 만듭니다](../../azure-monitor/learn/quick-create-workspace.md).
1. [AZURE AD 로그를 Azure Monitor 로그와 통합](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)합니다.

## <a name="how-it-works"></a>작동 방법 

Insights 및 보고 통합 문서에 액세스 하려면:  

1. **Azure Portal**에 로그인합니다.
1. **Azure Active Directory** > **Security**보안 > **조건부**액세스 > **정보 및 보고**로 이동 합니다.

### <a name="get-started-select-parameters"></a>시작 하기: 매개 변수 선택 

정보 및 보고 대시보드를 사용 하면 지정 된 기간 동안 하나 이상의 조건부 액세스 정책의 영향을 볼 수 있습니다. 통합 문서의 맨 위에 있는 각 매개 변수를 설정 하 여 시작 합니다. 

![Azure Portal의 조건부 액세스 정보 및 보고 대시보드](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**조건부 액세스 정책**: 결합 된 영향을 보려면 조건부 액세스 정책을 하나 이상 선택 합니다. 정책은 사용 및 보고서 전용 정책의 두 그룹으로 구분 됩니다. 기본적으로 사용 하도록 설정 된 모든 정책이 선택 됩니다. 이러한 사용 정책은 테 넌 트에 현재 적용 되는 정책입니다.  

**시간 범위**: 4 시간에서 90 일 까지의 시간 범위를 선택 합니다. Azure AD 로그를 Azure Monitor와 통합 한 경우 보다 더 뒤로 시간 범위를 선택 하는 경우 통합 시간 이후의 로그인만 표시 됩니다.  

**사용자**: 기본적으로 대시보드는 모든 사용자에 대해 선택한 정책의 영향을 보여 줍니다. 개별 사용자를 기준으로 필터링 하려면 텍스트 필드에 사용자 이름을 입력 합니다. 모든 사용자를 기준으로 필터링 하려면 텍스트 필드에 "모든 사용자"를 입력 하거나 매개 변수를 비워 둡니다. 

**앱**: 기본적으로 대시보드는 모든 앱에 대해 선택한 정책의 영향을 보여 줍니다. 개별 앱을 기준으로 필터링 하려면 앱의 이름을 텍스트 필드에 입력 합니다. 모든 앱을 기준으로 필터링 하려면 텍스트 필드에 "모든 앱"을 입력 하거나 매개 변수를 비워 둡니다. 

**데이터 보기**: 사용자 수 또는 로그인 수를 기준으로 대시보드에 결과를 표시할지 여부를 선택 합니다. 개별 사용자에 게는 지정 된 시간 범위 동안 다양 한 결과가 있는 많은 앱에 대 한 수백 개의 로그인이 있을 수 있습니다. 사용자를 위해 데이터 뷰를 선택 하는 경우 사용자가 성공 및 실패 횟수에 모두 포함 될 수 있습니다. 예를 들어 10 명의 사용자가 있는 경우 지난 30 일 동안 8 개의 사용자가 성공 했을 수 있으며 그 중 9 개는 지난 30 일 동안 오류가 발생 했을 수 있습니다.

## <a name="impact-summary"></a>영향 요약 

매개 변수가 설정 되 면 영향 요약이 로드 됩니다. 요약에는 선택한 정책을 평가할 때 "성공", "실패", "사용자 작업이 필요 함" 또는 "적용 되지 않음"으로 표시 되는 시간 범위 동안의 사용자 또는 로그인 수가 표시 됩니다.  

![조건부 액세스 통합 문서에서 영향 요약](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**합계**: 선택한 정책 중 하나 이상이 평가 된 기간 동안의 사용자 또는 로그인 수입니다.

**성공**: 선택한 정책의 결합 된 결과가 "성공" 또는 "보고 전용: 성공" 인 기간 동안의 사용자 또는 로그인 수입니다.

**실패**: 선택한 정책 중 하나 이상의 결과가 "실패" 또는 "보고 전용: 실패" 인 기간 동안의 사용자 또는 로그인 수입니다.

**사용자 작업 필요**: 선택한 정책의 결합 된 결과가 "보고서 전용: 사용자 작업 필요" 인 기간 동안의 사용자 또는 로그인 수입니다. 사용자 동작은 보고서 전용 조건부 액세스 정책에서 multi-factor authentication과 같은 대화형 권한 부여 컨트롤을 요구 하는 경우에 필요 합니다. 대화형 권한 부여 컨트롤은 보고서 전용 정책에 의해 적용 되지 않으므로 성공 또는 실패를 확인할 수 없습니다.  

**적용 되지 않음**: 선택한 정책이 적용 되지 않은 기간 동안의 사용자 또는 로그인 수입니다.

### <a name="understanding-the-impact"></a>영향 이해 

![조건 및 상태별 통합 문서 분석](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

각 조건에 대 한 사용자 분석 또는 로그인을 볼 수 있습니다. 통합 문서 위쪽의 요약 타일을 선택 하 여 특정 결과의 로그인 (예: 성공 또는 실패)을 필터링 할 수 있습니다. 각 조건부 액세스 조건에 대 한 로그인의 분석 (장치 상태, 장치 플랫폼, 클라이언트 앱, 위치, 응용 프로그램 및 로그인 위험)을 확인할 수 있습니다.  

## <a name="sign-in-details"></a>로그인 세부 정보 

![통합 문서 로그인 세부 정보](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

대시보드 아래쪽에서 로그인을 검색 하 여 특정 사용자의 로그인을 조사할 수도 있습니다. 왼쪽의 쿼리는 가장 자주 사용자를 표시 합니다. 사용자를 선택 하면 쿼리가 오른쪽으로 필터링 됩니다.  

## <a name="troubleshooting"></a>문제 해결

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>통합 문서를 로드 하는 데 시간이 오래 걸리는 이유는 무엇 인가요?  

선택한 시간 범위 및 테 넌 트의 크기에 따라 통합 문서는 상당히 많은 수의 로그인 이벤트를 평가할 수 있습니다. 대규모 테 넌 트의 경우 로그인 볼륨이 Log Analytics의 쿼리 용량을 초과할 수 있습니다. 통합 문서가 로드 되는지 확인 하려면 시간 범위를 4 시간으로 줄여 보세요.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>몇 분 동안 로드 한 후에는 통합 문서에 0 결과가 반환 되는 이유는 무엇 인가요? 

로그인 볼륨이 Log Analytics의 쿼리 용량을 초과 하는 경우 통합 문서는 0 개의 결과를 반환 합니다. 통합 문서가 로드 되는지 확인 하려면 시간 범위를 4 시간으로 줄여 보세요.  

### <a name="can-i-save-my-parameter-selections"></a>매개 변수 선택 항목을 저장할 수 있나요?  

**Azure Active Directory** > Azure Active Directory > **통합 문서****조건부 액세스 정보 및 보고**로 이동 하 여 통합 문서 맨 위에 매개 변수 선택을 저장할 수 있습니다. 여기에서 통합 문서를 편집 하 고 **내 보고서** 또는 **공유 보고서**에서 매개 변수 선택 항목을 포함 하 여 작업 영역에 복사본을 저장할 수 있습니다. 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>추가 쿼리를 사용 하 여 통합 문서를 편집 하 고 사용자 지정할 수 있나요? 

**Azure Active Directory** > **Workbooks**통합 > 문서**조건부 액세스 정보 및 보고**로 이동 하 여 통합 문서를 편집 하 고 사용자 지정할 수 있습니다. 여기에서 통합 문서를 편집 하 고 **내 보고서** 또는 **공유 보고서**에서 매개 변수 선택 항목을 포함 하 여 작업 영역에 복사본을 저장할 수 있습니다. 쿼리 편집을 시작 하려면 통합 문서의 위쪽에서 **편집** 을 클릭 합니다.  
 
## <a name="next-steps"></a>다음 단계

[조건부 액세스 보고서 전용 모드](concept-conditional-access-report-only.md)
