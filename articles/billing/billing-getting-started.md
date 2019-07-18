---
title: Azure에서 예기치 않은 비용 방지 및 청구 관리 | Microsoft Docs
description: Azure 청구서에 예기치 않은 요금이 부과되지 않도록 하는 방법을 알아봅니다. Microsoft Azure 구독에 대한 비용 추적 및 관리 기능을 사용합니다.
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: banders
ms.openlocfilehash: 146c74fe751e75fb85563378be6f812802928fe2
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918927"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Azure 청구 및 비용 관리를 사용하여 예기치 않은 비용 방지

Azure에 등록 하는 경우 몇 가지 방법으로의 지출을 보다 명확히 파악할를 수행할 수 있습니다. [가격 계산기](https://azure.microsoft.com/pricing/calculator/)는 Azure 리소스를 만들기 전에 예상 비용을 제공할 수 있습니다. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)은 사용자 구독에 대한 현재 비용 분석 및 예측을 제공합니다. 여러 다른 프로젝트 또는 팀의 비용을 그룹화하고 파악하려는 경우 [리소스 태그](../azure-resource-manager/resource-group-using-tags.md)를 살펴봅니다. 선호하는 보고 시스템이 조직에 있는 경우 [청구 API](billing-usage-rate-card-overview.md)를 확인하세요.

- 구독이 EA(기업 계약)인 경우 Azure Portal에서 비용을 확인하기 위한 공개 미리 보기를 사용할 수 있습니다. CSP(클라우드 솔루션 공급자) 또는 Azure 스폰서쉽을 통한 구독인 경우 다음 기능 중 일부는 적용되지 않습니다. 자세한 내용은 [EA, CSP 및 스폰서쉽에 대한 추가 리소스](#other-offers)를 참조하세요.

- 구독이 평가판, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), AIO(Azure in Open) 또는 BizSpark인 경우, 크레딧을 모두 사용하면 구독이 자동으로 사용되지 않도록 설정됩니다. 구독이 예기치 않게 사용할 수 없도록 설정되는 것을 방지하려면 [지출 한도](#spending-limit)에 대해 자세히 알아보세요.

- [Azure 체험 계정](https://azure.microsoft.com/free/)에 가입하는 경우 [12개월 동안 체험용으로 가장 널리 사용되는 Azure 서비스의 일부를 사용할 수 있습니다](billing-create-free-services-included-free-account.md). 아래에 나열된 권장 사항과 함께 [체험 계정에서 요금 청구 방지](billing-avoid-charges-free-account.md)를 참조하세요.

## <a name="get-estimated-costs-before-adding-azure-services"></a>Azure 서비스를 추가하기 전에 예상 비용 얻기

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>가격 계산기를 사용하여 온라인으로 비용 예측

[가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 확인하여 관심 있는 서비스의 월별 비용을 예측하세요. 예상 비용을 얻기 위해 첫 번째 파티 Azure 리소스를 추가할 수 있습니다.

![가격 계산기 메뉴의 스크린샷](./media/billing-getting-started/pricing-calc.png)

예를 들어 A1 Windows VM(Virtual Machine)은 계속 실행 상태를 유지할 경우 월별 컴퓨팅 시간이 $66.96 USD로 예측됩니다.

![A1 Windows VM이 예상 월별 비용이 $66.96 USD임을 보여 주는 가격 계산기 스크린샷](./media/billing-getting-started/pricing-calcVM.png)

가격 책정에 대한 자세한 내용은 이 [FAQ](https://azure.microsoft.com/pricing/faq/)를 참조하세요. 또는 Azure 판매원에게 문의하려면 1-800-867-1389번으로 전화하세요.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Azure Portal에서 예상 비용 검토

일반적으로 Azure Portal에서 서비스를 추가하는 경우 유사한 월별 예상 비용이 표시되는 보기가 제공됩니다. 예를 들어 Windows VM의 크기를 선택하는 경우 컴퓨팅 시간에 대해 예상되는 월별 비용이 표시됩니다.

![예제: A1 Windows VM의 월별 비용이 $66.96 USD로 예측됨](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="spending-limit"></a> 지출 한도가 설정되어 있는지 확인

크레딧을 사용하는 구독이 있는 경우 기본적으로 지출 한도가 켜져 있습니다. 이러한 방식으로 모든 크레딧을 지출하면 신용 카드에 요금이 청구되지 않습니다. [전체 Azure 제품 목록 및 지출 한도 가용성](https://azure.microsoft.com/support/legal/offer-details/)을 참조하세요.

그러나 지출 한도에 도달하면 서비스는 사용되지 않도록 설정됩니다. 즉, VM이 할당 취소됩니다. 서비스 가동 중지를 방지하려면 지출 한도를 해제해야 합니다. 초과분이 파일의 신용 카드에 부과됩니다. 

지출 한도가 있는지 확인하려면 [계정 센터의 구독 보기](https://account.windowsazure.com/Subscriptions)로 이동합니다. 지출 한도가 켜져 있으면 배너가 표시됩니다.

![계정 센터에서 지출 한도가 설정되어 있다는 사실에 대한 경고를 표시하는 스크린 샷](./media/billing-getting-started/spending-limit-banner.PNG)

배너를 클릭하고 지출 한도 제거 지침을 따릅니다. 등록 시 신용 카드 정보를 입력하지 않은 경우 지출 한도를 제거하려면 해당 정보를 입력해야 합니다. 자세한 내용은 [Azure 지출 한도 – 작동 방식 및 사용 또는 제거하는 방법](https://azure.microsoft.com/pricing/spending-limits/)을 참조하세요.

[Cloudyn](https://www.cloudyn.com/) 서비스를 사용하면 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 경고를 만들 수 있습니다. 예산 및 비용 임계값에 따라 경고를 지원하는 보고서를 사용하여 경고를 만들 수 있습니다. Cloudyn 사용에 대한 자세한 내용은 [자습서: 사용량 및 비용 검토](../cost-management/tutorial-review-usage.md)를 참조하세요.

이 예에서는 **시간 경과별 실제 비용** 보고서를 사용하여 Azure VM에 대한 지출이 총 예산에 도달할 때 알림을 보냅니다. 이 시나리오에서는 총 예산이 $20,000이고 비용이 예산의 절반 정도인 $9,000에 도달할 때 알림을 받기를 원하고 비용이 $10,000에 도달하는 경우 추가 경고를 받기를 원합니다.

1. Cloudyn 포털의 맨 위에 있는 메뉴에서 **비용** > **비용 분석** > **시간 경과별 실제 비용**을 차례로 선택합니다. 
2. **그룹**을 **서비스**로 설정하고, **서비스 기준 필터**를 **Azure/VM**으로 설정합니다. 
3. 보고서의 오른쪽 위에서 **작업**을 선택한 다음, **보고서 예약**을 선택합니다.
4. 예약된 간격으로 자신에게 보고서 이메일을 보내려면 **이 보고서 저장 또는 예약** 대화 상자에서 **일정 예약** 탭을 선택합니다. **이메일로 전송**을 선택해야 합니다. 사용하는 모든 태그, 그룹 및 필터링이 이메일로 보내는 보고서에 포함됩니다. 
5. **임계값** 탭을 선택한 다음, **실제 비용 대 임계값**을 선택합니다. 
   1. **빨간색 경고** 임계값 상자에 10000을 입력합니다. 
   2. **노란색 경고** 임계값 상자에 9000을 입력합니다. 
   3. **연속되는 경고 수** 상자에 수신할 연속 경고 수를 입력합니다. 지정한 총 경고 수를 받으면 추가 경고가 전송되지 않습니다. 
6. **저장**을 선택합니다.

    ![지출 임계값에 따라 빨간색 및 노란색 경고를 보여주는 예제](./media/billing-getting-started/schedule-alert01.png)

또한 **비용 백분율 대 예산 임계값 메트릭을 선택하여 경고를** 만들 수도 있습니다. 이렇게 하면 통화 값 대신 예산 백분율로 임계값을 지정할 수 있습니다.

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Azure 서비스를 사용할 때 비용을 모니터링하는 방법

### <a name="tags"></a> 리소스에 태그를 추가하여 청구 데이터 그룹화

태그를 사용하여 지원되는 서비스에 대한 청구 데이터를 그룹화할 수 있습니다. 예를 들어 여러 다른 팀에 대한 여러 VM을 실행하는 경우 태그를 사용하여 비용 센터(HR, 마케팅, 재무) 또는 환경(프로덕션, 프로덕션 전 테스트)별로 비용을 분류할 수 있습니다. 

![포털의 태그 설정을 보여 주는 스크린 샷](./media/billing-getting-started/tags.PNG)

태그는 여러 다른 비용 보고 보기에 표시됩니다. 예를 들어 지금 바로 [비용 분석 보기](#costs)에 표시되거나 첫 번째 청구 기간 후에 상세 사용 현황 .csv에 표시됩니다.

자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/resource-group-using-tags.md)을 참조하세요.

### <a name="costs"></a> 포털에서 정기적으로 비용 분석 및 진행 속도 확인

서비스가 실행 중일 때 부과되는 요금을 정기적으로 확인합니다. Azure Portal에서 현재 사용량 및 진행 속도를 확인할 수 있습니다.

1. [Azure Portal의 구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)을 방문하여 구독을 선택합니다.

2. 구독에 지원되는 경우 비용 분석 및 진행 속도가 표시됩니다.

    ![Azure Portal의 진행 속도 및 분석 스크린 샷](./media/billing-getting-started/burn-rate.PNG)

3. 왼쪽 목록에서 **비용 분석**을 클릭하여 리소스별 비용 분석을 표시합니다. 서비스를 추가하고 24시간 정도 데이터가 채워질 때까지 기다립니다.

    ![Azure Portal의 비용 분석 보기의 스크린 샷](./media/billing-getting-started/cost-analysis.PNG)

4. [태그](#tags), 리소스 종류, 리소스 그룹 및 시간 간격 등의 다양한 속성별로 필터링할 수 있습니다. 보기를 쉼표로 구분된 값(.csv) 파일로 내보내려면 **적용**을 클릭하여 필터를 확인하고 **다운로드**를 클릭합니다.

5. 또한 리소스를 클릭하여 매일 사용 기록과 일일 리소스 비용을 확인할 수 있습니다.

    ![Azure Portal의 사용 기록 보기 스크린 샷](./media/billing-getting-started/costhistory.PNG)

표시되는 비용과 서비스를 선택할 때 확인한 예측 비용을 비교하는 것이 좋습니다. 비용이 예측 비용과 크게 다른 경우 리소스에 대해 선택한 가격 책정 플랜을 다시 확인합니다.

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>VM에 대한 자동 종료와 같은 비용 절감에 기능 사용 고려

시나리오에 따라 Azure Portal에서 VM에 대한 자동 종료를 구성할 수 있습니다. 자세한 내용은 [Azure Resource Manager를 사용한 VM에 대한 자동 종료](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)를 참조하세요.

![포털의 자동 종료 옵션 스크린샷](./media/billing-getting-started/auto-shutdown.PNG)

자동 종료는 전원 옵션을 사용하여 VM 내에서 종료할 때와는 다릅니다. 자동 종료는 VM을 중지한 후 할당 취소하여 추가 사용 요금 부과를 중지합니다. 자세한 내용은 VM 상태에 대한 [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)의 가격 책정 FAQ를 참조하세요.

개발 및 테스트 환경에 대한 추가 비용 절감에 기능에 대해서는 [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/)를 확인하세요.

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Azure Advisor 권장 지침 설정 및 확인

[Azure Advisor](../advisor/advisor-overview.md)는 사용량이 낮은 리소스를 식별하여 비용을 절감하는 데 도움을 주는 기능입니다. Azure Portal에서 Advisor를 방문합니다.

![Azure Portal의 Azure Advisor 단추 스크린 샷](./media/billing-getting-started/advisor-button.PNG)

Advisor 대시보드의 **비용** 탭에서 실행 가능한 권장 지침을 얻을 수 있습니다.

![Advisor cost 권장 지침 예제 스크린 샷](./media/billing-getting-started/advisor-action.PNG)

자세한 내용은 [Advisor 비용 권장 사항](../advisor/advisor-cost-recommendations.md)을 참조하세요.

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>대금 청구 주기 종료 시 비용 검토

대금 청구 주기가 종료되면 청구서를 사용할 수 있게 됩니다. [과거 청구서 및 자세한 사용 현황 파일을 다운로드](billing-download-azure-invoice-daily-usage-date.md)하여 요금이 정확하게 부과되었는지 확인할 수 있습니다. 청구서에서 일간 사용 현황 비교에 대한 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하세요.

### <a name="billing-api"></a>청구 API

청구 API를 사용하여 프로그래밍 방식으로 사용 현황 데이터를 가져옵니다. RateCard API 및 사용 현황 API를 함께 사용하여 청구된 사용량을 확인합니다. 자세한 내용은 [Microsoft Azure 리소스 소비에 대한 통찰력 얻기](billing-usage-rate-card-overview.md)를 참조하세요.

## <a name="other-offers"></a> 추가 리소스 및 특수 사례

### <a name="ea-csp-and-sponsorship-customers"></a>EA, CSP 및 스폰서쉽 고객
계정 관리자 또는 Azure 파트너에 시작하도록 알립니다.

| 제안 | 리소스 |
|-------------------------------|-----------------------------------------------------------------------------------|
| EA(기업 계약) | [EA 포털](https://ea.azure.com/), [도움말 문서](https://ea.azure.com/helpdocs) 및 [Power BI 보고서](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| CSP(클라우드 솔루션 공급자) | 공급자에게 알림 |
| Azure 스폰서쉽 | [스폰서쉽 포털](https://www.microsoftazuresponsorships.com/) |

대기업의 IT를 관리하는 경우 [Azure 엔터프라이즈 스 캐폴드](/azure/architecture/cloud-adoption-guide/subscription-governance) 및 [엔터프라이즈 IT 백서](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf)(.pdf 다운로드, 영어 버전만 제공)를 읽어보세요.

#### <a name="EA"></a> Azure Portal 내에서 기업계약 비용 보기 미리 보기 

엔터프라이즈 비용 보기는 현재 공개 미리 보기 상태입니다. 참고할 항목:

- 구독 비용은 사용을 기반으로 하며 사전에 지불한 금액, 초과분, 포함된 수량, 조정 및 세금을 포함하지 않습니다. 실제 요금은 등록 수준에서 계산됩니다.
- Azure Portal 내에 표시되는 금액은 엔터프라이즈 포털에 있는 금액과 다를 수 있습니다. 엔터프라이즈 포털의 업데이트는 Azure Portal에 변경 내용이 표시되기 전에 몇 분 정도 걸릴 수 있습니다.
- 비용이 표시되지 않는 경우 다음 이유 중 하나에 해당할 수 있습니다.
    - 구독 수준에서 권한이 없습니다. Enterprise 비용 보기를 표시하려면 구독 수준에서 청구 읽기 권한자, 읽기 권한자, 참가자 또는 소유자여야 합니다.
    - 사용자는 계정 소유자이며 등록 관리자가 “AO 보기 요금” 설정을 비활성화했습니다.  등록 관리자에게 비용에 대한 액세스 권한을 문의하세요. 
    - 사용자는 부서 관리자이며 등록 관리자가 “DA 보기 요금” 설정을 비활성화했습니다.  등록 관리자에게 액세스 권한을 문의하세요.
    - 채널 파트너를 통해 Azure를 구매했고 파트너가 가격 정보를 릴리스하지 않았습니다.  
- 엔터프라이즈 포털에서 비용 액세스와 관련된 설정을 업데이트하는 경우 Azure Portal에 변경 내용이 표시되기 전에 몇 분 정보 지연됩니다.
- 지출 한도 및 송장 지침은 EA 구독에 적용되지 않습니다.

### <a name="check-your-subscription-and-access"></a>구독 및 액세스 권한 확인

비용을 보려면 [청구 정보에 대한 구독 수준 액세스 권한](billing-manage-access.md)이 있어야 합니다. 계정 관리자만 [계정 센터](https://account.azure.com/Subscriptions)에 액세스하고, 청구 정보를 변경하고, 구독을 관리할 수 있습니다. 계정 관리자는 가입 프로세스를 거친 사용자입니다. 자세한 내용은 [구독 또는 서비스를 관리하는 Azure 관리자 역할 추가 또는 변경](billing-add-change-azure-subscription-administrator.md)을 참조하세요.

계정 관리자인지 확인하려면 [Azure Portal의 구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)으로 이동합니다. 액세스 권한이 있는 구독 목록을 확인합니다. **내 역할** 아래에서 확인합니다. *계정 관리자*가 표시되면 문제가 없는 것입니다. *소유자* 등이 표시되면 모든 권한이 있는 것은 아닙니다.

![Azure Portal의 구독 보기에 표시되는 역할의 스크린 샷](./media/billing-getting-started/sub-blade-view.PNG)

계정 관리자가 아니면 누군가가 [Azure Active Directory RBAC](../role-based-access-control/role-assignments-portal.md)(역할 기반 액세스 제어)를 사용하여 부분적인 액세스 권한을 부여한 것입니다. 구독을 관리하고 청구 정보를 변경하려면 [계정 관리자를 찾습니다](billing-subscription-transfer.md#whoisaa). 계정 관리자에게 작업을 수행하거나 [구독을 전송](billing-subscription-transfer.md)하도록 요청합니다.

계정 관리자가 더 이상 조직에 있지 않은 상태에서 청구를 관리해야 하는 경우에는 [문의하세요](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="how-to-request-a-service-level-agreement-credit-for-a-service-incident"></a>서비스 인시던트에 대 한 서비스 수준 계약 크레딧을 요청 하는 방법

SLA(서비스 수준 계약)에서는 작동 시간 및 연결에 대한 Microsoft의 정책을 설명합니다. Azure 서비스는 해당 영향을 미치는 가동 시간 또는 연결을 "중단"입니다.이 라 불리는 문제가 발생 하는 경우 서비스 인시던트를 보고 됩니다. 경우 달성 하지 않으며 SLA에 설명 된 대로 각 서비스에 대 한 서비스 수준을 유지 관리에서는 다음 수 있습니다에 월간 서비스 비용의 일부 크레딧을 받을 수 있습니다.

크레딧을 요청:

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다. 계정이 여러 개인 경우 Azure 가동 중지 시간에 영향을 받은 하나를 사용 해야 합니다. 이렇게 하면 자동으로 필요한 배경 정보를 수집 하 고 대/소문자를 더 빠르게 해결 지원 합니다.
2. 새 지원 요청을 만듭니다.
3. 아래 **문제 유형**를 선택 **청구**합니다.
4. 아래 **문제 유형**를 선택 **환불 요청**합니다.
5. 추가 세부 정보는 SLA 크레딧 요청을 지정 하려면 영향을 받는 서비스 (Vm, 웹 사이트 등) 뿐만 아니라는 날짜/시간/표준 시간대를 언급 합니다.
6. 연락처 세부 정보를 확인 하 고 선택 합니다 **만들기** 단추 요청을 제출 합니다.

SLA 임계값 서비스에 따라 다릅니다. 예를 들어 SQL 웹 계층은 99.9%의 SLA, 99.95%의 SLA가 있는 Vm 및 SQL 표준 계층에는 SLA가 99.99%입니다.

일부 서비스는 SLA 적용 하기 위한 필수 구성 요소는 있습니다. 예를 들어, 가상 컴퓨터에 동일한 가용성 집합에 배포 된 인스턴스가 두 개 이상 있어야 합니다.

자세한 내용은 참조는 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/) 설명서와 [Azure 서비스에 대 한 SLA 요약](https://azure.microsoft.com/support/legal/sla/summary/) 설명서.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.
