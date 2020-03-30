---
title: Azure AD 도메인 서비스와 함께 Azure 모니터 통합 문서 사용 | 마이크로 소프트 문서
description: Azure 모니터 통합 문서를 사용하여 보안 감사를 검토하고 Azure Active Directory 도메인 서비스 관리 도메인의 문제를 이해하는 방법을 알아봅니다.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
ms.openlocfilehash: 4bf6926651a0e2e9289b3c729abea16b1c66bce6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108641"
---
# <a name="review-security-audit-events-in-azure-ad-domain-services-using-azure-monitor-workbooks"></a>Azure 모니터 통합 문서를 사용하여 Azure AD 도메인 서비스의 보안 감사 이벤트 검토

Azure Active Directory 도메인 서비스(Azure AD DS) 관리 도메인의 상태를 이해하는 데 도움이 있으므로 보안 감사 이벤트를 사용하도록 설정할 수 있습니다. 그런 다음 텍스트, 분석 쿼리 및 매개 변수를 풍부한 대화형 보고서로 결합하는 Azure Monitor 통합 문서를 사용하여 이러한 보안 감사 이벤트를 검토할 수 있습니다. Azure AD DS에는 감사 이벤트를 자세히 조사하고 환경을 관리할 수 있는 보안 개요 및 계정 활동에 대한 통합 문서 템플릿이 포함되어 있습니다.

이 문서에서는 Azure 모니터 통합 문서를 사용하여 Azure AD DS의 보안 감사 이벤트를 검토하는 방법을 보여 주며 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 다음과 같은 리소스와 권한이 필요합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료하여 [Azure Active Directory 도메인 서비스 인스턴스를 만들고 구성합니다.][create-azure-ad-ds-instance]
* Azure Active Directory 도메인 서비스 관리 도메인에 대해 데이터를 Log Analytics 작업 영역으로 스트리밍하는 보안 감사 이벤트가 활성화되었습니다.
    * 필요한 경우 [Azure Active Directory 도메인 서비스에 대한 보안 감사를 사용하도록 설정합니다.][enable-security-audits]

## <a name="azure-monitor-workbooks-overview"></a>Azure 모니터 통합 문서 개요

Azure AD DS에서 보안 감사 이벤트가 켜져 있으면 관리되는 도메인의 문제를 분석하고 식별하기 어려울 수 있습니다. Azure Monitor를 사용하면 이러한 보안 감사 이벤트를 집계하고 데이터를 쿼리할 수 있습니다. Azure Monitor 통합 문서를 사용하면 이 데이터를 시각화하여 문제를 더 빠르고 쉽게 식별할 수 있습니다.

통합 문서 템플릿은 여러 사용자와 팀에서 유연하게 재사용할 수 있도록 설계된 선별된 보고서입니다. 통합 문서 템플릿을 열면 Azure Monitor 환경의 데이터가 로드됩니다. 조직의 다른 사용자에게 영향을 주지 않고 템플릿을 사용할 수 있으며 템플릿을 기반으로 자신의 통합 문서를 저장할 수 있습니다.

Azure AD DS에는 다음 두 통합 문서 템플릿이 포함됩니다.

* 보안 개요 보고서
* 계정 활동 보고서

통합 문서를 편집하고 관리하는 방법에 대한 자세한 내용은 [Azure Monitor 통합 문서 개요를](../azure-monitor/platform/workbooks-overview.md)참조하십시오.

## <a name="use-the-security-overview-report-workbook"></a>보안 개요 보고서 통합 문서 사용

사용 현황을 더 잘 이해하고 잠재적인 보안 위협을 식별하는 데 도움이 되는 보안 개요 보고서는 로그인 데이터를 요약하고 확인할 수 있는 계정을 식별합니다. 특정 날짜 범위의 이벤트를 보고 잘못된 암호 시도 또는 계정이 비활성화된 위치와 같은 특정 로그인 이벤트로 드릴다운할 수 있습니다.

보안 개요 보고서에 대한 통합 문서 템플릿에 액세스하려면 다음 단계를 완료하십시오.

1. Azure 포털에서 **Azure Active 디렉터리 도메인 서비스를** 검색하고 선택합니다.
1. *aaddscontoso.com* 등 관리형 도메인선택
1. 왼쪽 메뉴에서 통합 문서 > **모니터링을** 선택합니다.

    ![Azure 포털에서 통합 문서 메뉴 옵션을 선택합니다.](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. 보안 **개요 보고서를 선택합니다.**
1. 통합 문서 맨 위에 있는 드롭다운 메뉴에서 Azure 구독을 선택한 다음 Azure Monitor 작업 영역을 선택합니다. *지난 7일과*같은 **시간 범위를 선택합니다.**

    ![Azure 포털에서 통합 문서 메뉴 옵션을 선택합니다.](./media/use-azure-monitor-workbooks/select-query-filters.png)

    **타일 보기** 및 **차트 보기** 옵션을 변경하여 원하는 대로 데이터를 분석하고 시각화할 수도 있습니다.

1. 특정 이벤트 유형으로 드릴다운하려면 다음 예제와 같이 *계정 잠금과*같은 **로그인 결과** 카드 중 하나를 선택합니다.

    ![Azure 모니터 통합 문서에서 시각화된 보안 개요 보고서 데이터 예제](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. 차트 아래의 보안 개요 보고서의 아래쪽 부분은 선택한 활동 유형을 세분화합니다. 다음 예제 보고서에 표시된 것처럼 오른쪽에 있는 사용자 이름으로 필터링할 수 있습니다.

    [![](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png "Details of account lockouts in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>계정 활동 보고서 통합 문서 사용

특정 사용자 계정의 문제 해결을 돕기 위해 계정 활동 보고서는 자세한 감사 이벤트 로그 정보를 세분화합니다. 로그인 시 잘못된 사용자 이름 또는 암호가 제공된 경우및 로그인 시도의 출처를 검토할 수 있습니다.

계정 활동 보고서의 통합 문서 템플릿에 액세스하려면 다음 단계를 완료합니다.

1. Azure 포털에서 **Azure Active 디렉터리 도메인 서비스를** 검색하고 선택합니다.
1. *aaddscontoso.com* 등 관리형 도메인선택
1. 왼쪽 메뉴에서 통합 문서 > **모니터링을** 선택합니다.
1. 계정 **활동 보고서를**선택합니다.
1. 통합 문서 맨 위에 있는 드롭다운 메뉴에서 Azure 구독을 선택한 다음 Azure Monitor 작업 영역을 선택합니다. *지난 30일과*같은 **시간 범위를**선택한 다음 **타일 보기가** 데이터를 나타내는 방법을 선택합니다. 다음 예제 보고서에 표시된 것처럼 *felix와*같은 **계정 사용자 이름으로**필터링할 수 있습니다.

    [![](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png "Account activity report in Azure Monitor Workbooks")](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    차트 아래의 영역에는 활동 결과 및 소스 워크스테이션과 같은 정보와 함께 개별 로그인 이벤트가 표시됩니다. 이 정보는 계정 잠금을 유발하거나 잠재적인 공격을 나타낼 수 있는 로그인 이벤트의 반복된 소스를 확인하는 데 도움이 될 수 있습니다.

보안 개요 보고서와 마찬가지로 보고서 맨 위에 있는 여러 타일로 드릴다운하여 필요에 따라 데이터를 시각화하고 분석할 수 있습니다.

## <a name="save-and-edit-workbooks"></a>통합 문서 저장 및 편집

Azure AD DS에서 제공하는 두 개의 템플릿 통합 문서는 사용자 고유의 데이터 분석으로 시작하는 데 적합합니다. 데이터 쿼리 및 조사를 보다 세부적으로 처리해야 하는 경우 고유한 통합 문서를 저장하고 쿼리를 편집할 수 있습니다.

1. 통합 문서 템플릿 중 하나의 복사본을 저장하려면 **> > 공유 보고서로 저장편집을**선택한 다음 이름을 제공하고 저장합니다.
1. 템플릿 의 복사본에서 **편집을** 선택하여 편집 모드로 들어갑니다. 보고서의 모든 부분 옆에 있는 파란색 **편집** 단추를 선택하고 변경할 수 있습니다.

Azure 모니터 통합 문서의 모든 차트와 테이블은 Kusto 쿼리를 사용하여 생성됩니다. 사용자 고유의 쿼리를 만드는 방법에 대한 자세한 내용은 [Azure Monitor 로그 쿼리][azure-monitor-queries] 및 [Kusto 쿼리 자습서를][kusto-queries]참조하십시오.

## <a name="next-steps"></a>다음 단계

암호 및 잠금 정책을 조정해야 하는 경우 [관리되는 도메인의 암호 및 계정 잠금 정책을 참조하세요.][password-policy]

사용자 문제의 경우 계정 로그인 [문제][troubleshoot-sign-in] 또는 계정 [잠금 문제를][troubleshoot-account-lockout]해결하는 방법을 알아봅니다.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: https://docs.microsoft.com/azure/kusto/query/tutorial?pivots=azuredataexplorer
