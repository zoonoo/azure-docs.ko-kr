---
title: 보고서 전용 모드에서 조건부 액세스 정책 구성 - Azure Active Directory
description: 조건부 액세스에서 보고서 전용 모드를 사용하여 채택을 돕습니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9bec829d7fb3e76eb243bda73423303670585e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295124"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>보고서 전용 모드에서 조건부 액세스 정책 구성(미리 보기)

보고서 전용 모드에서 조건부 액세스 정책을 구성하려면 다음을 수행하십시오.

> [!IMPORTANT]
> 조직에 아직 없는 경우 [Azure AD .](#set-up-azure-monitor-integration-with-azure-ad) 이 프로세스는 데이터를 검토할 수 있도록 하기 전에 수행해야 합니다.

1. Azure **포털에** 조건부 액세스 관리자, 보안 관리자 또는 전역 관리자로 로그인합니다.
1. Azure **Active 디렉터리** > **보안** > **조건부 액세스로**이동합니다.
1. **새 정책**을 선택합니다.
1. 필요에 따라 정책 조건 및 필요한 권한 부여 컨트롤을 구성합니다.
1. **활성화 정책에서** 토글을 **보고서 전용** 모드로 설정합니다.
1. **저장** 선택

> [!TIP]
> 기존 정책의 **활성화 정책** 상태를 **보고서에서** **보고서 전용으로** 편집할 수 있지만 이렇게 하면 정책 적용이 비활성화됩니다. 

Azure AD 로그인 로그에서 보고서 전용 결과를 봅니다.

특정 로그인에 대한 보고서 전용 정책의 결과를 보려면 다음을 수행하십시오.

1. 보고서 판독기, 보안 판독기, 보안 관리자 또는 전역 관리자로 **Azure 포털에** 로그인합니다.
1. Azure **Active 디렉터리** > **로그인으로**검색합니다.
1. 로그인을 선택하거나 필터를 추가하여 결과를 좁힐 수 있습니다.
1. 세부 **정보** 서랍에서 **보고서 전용(미리 보기)** 탭을 선택하여 로그인 중에 평가된 정책을 봅니다.

> [!NOTE]
> 로그인 로그를 다운로드할 때 조건부 액세스 보고서 전용 결과 데이터를 포함하도록 JSON 형식을 선택합니다.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Azure AD로 Azure 모니터 통합 설정

새 조건부 Access Insights 통합 문서를 사용하여 조건부 액세스 정책의 집계 영향을 보려면 Azure Monitor를 Azure AD와 통합하고 로그인 로그를 내보내야 합니다. 이 통합을 설정하는 두 단계가 있습니다. 

1. [Azure Monitor 구독에 등록하고 작업 영역을 만듭니다.](/azure/azure-monitor/learn/quick-create-workspace)
1. [Azure AD에서 Azure 모니터로 로그인 로그를 내보냅니다.](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

Azure 모니터 가격 책정에 대한 자세한 내용은 [Azure 모니터 가격 책정 페이지에서](https://azure.microsoft.com/pricing/details/monitor/)확인할 수 있습니다. 비용을 추정하거나, 일일 한도를 설정하거나, 데이터 보존 기간을 사용자 지정하는 리소스는 문서에서 찾을 수 [있습니다.](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)

## <a name="view-conditional-access-insights-workbook"></a>조건부 액세스 인사이트 통합 문서 보기

Azure AD 로그를 Azure 모니터와 통합한 후에는 새 조건부 Access 인사이트 통합 문서를 사용하여 조건부 액세스 정책의 영향을 모니터링할 수 있습니다.

1. **Azure 포털에** 보안 관리자 또는 전역 관리자로 로그인합니다.
1. Azure **Active 디렉터리** > **통합 문서로**이동합니다.
1. **조건부 액세스 인사이트(미리 보기)를 선택합니다.**
1. **조건부 액세스 정책** 드롭다운에서 하나 이상의 정책을 선택합니다. 모든 활성화된 정책은 기본적으로 선택됩니다.
1. 시간 범위를 선택합니다(시간 범위가 사용 가능한 데이터 집합을 초과하는 경우 보고서에 사용 가능한 모든 데이터가 표시됩니다). **조건부 액세스 정책** 및 **시간 범위** 매개 변수를 설정하면 보고서가 로드됩니다.
   1. 선택적으로 개별 **사용자** 또는 **앱을** 검색하여 보고서 범위를 좁힐 수 있습니다.
1. 시간 범위의 데이터를 사용자 수 또는 로그인 수에 따라 선택합니다.
1. **데이터 보기에**따라 **영향 요약은** 총 수, **성공,** **실패,** **사용자 작업 필요**및 적용되지 **않음으로**그룹화된 선택한 매개 변수의 범위에 사용자 또는 로그인 수를 표시합니다. 타일을 선택하여 특정 결과 유형의 로그인을 검사합니다. 
   1. 통합 문서 매개 변수를 변경한 경우 나중에 사용할 수 있도록 복사본을 저장하도록 선택할 수 있습니다. 보고서 상단의 저장 아이콘을 선택하고 저장할 이름과 위치를 제공합니다.
1. 아래로 스크롤하여 각 조건에 대한 로그인 내역을 봅니다.
1. 보고서 하단의 **로그인 세부 정보를** 보고 위의 선택으로 필터링된 개별 로그인 이벤트를 조사합니다.

> [!TIP] 
> 특정 쿼리를 드릴다운하거나 로그인 세부 정보를 내보내야 합니까? 로그 애널리틱스에서 쿼리를 열려면 쿼리 오른쪽에 있는 단추를 선택합니다. CSV 또는 Power BI로 내보낼 내보내기를 선택합니다.

## <a name="common-problems-and-solutions"></a>일반적인 문제 및 해결 방법

### <a name="why-are-the-queries-in-the-workbook-failing"></a>통합 문서의 쿼리가 실패하는 이유는 무엇입니까?

잘못되었거나 여러 작업 영역이 통합 문서와 연결된 경우 쿼리가 실패하는 경우가 있습니다. 이 문제를 해결하려면 통합 문서 상단의 **편집을** 클릭한 다음 설정 기어를 클릭합니다. 통합 문서와 연결되지 않은 작업 영역을 선택하고 제거합니다. 각 통합 문서와 연결된 작업 영역은 하나만 있어야 합니다.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>조건부 액세스 정책 드롭다운 매개 변수에 내 정책이 포함되지 않는 이유는 무엇입니까?

조건부 액세스 정책 드롭다운은 4시간 동안 가장 최근의 로그인을 쿼리하여 채워집니다. 테넌트에 지난 4시간 동안 로그인이 없는 경우 드롭다운이 비어 있을 수 있습니다. 이 지연이 자주 로그인되지 않은 소규모 테넌트와 같이 지속적인 문제인 경우 관리자는 조건부 액세스 정책 드롭다운에 대한 쿼리를 편집하고 쿼리 시간을 4시간 이상 연장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

Azure AD 통합 문서에 대한 자세한 내용은 Azure [Active Directory 보고서에 Azure 모니터 통합 문서를 사용하는 방법](../reports-monitoring/howto-use-azure-monitor-workbooks.md)문서를 참조하세요.
