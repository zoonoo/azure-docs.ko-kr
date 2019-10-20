---
title: 파트너에 대 한 Azure Cost Management 시작
description: 이 문서에서는 파트너가 Azure Cost Management 기능을 사용 하는 방법과 고객이 자신의 고객에 게 Cost Management 액세스를 사용 하도록 설정 하는 방법을 설명 합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 661e1b6e3811ff2b5ae25e4fd59764a69e9ebe48
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596683"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>파트너에 대 한 Azure Cost Management 시작

Azure Cost Management는 고객이 Microsoft 고객 계약에 등록 하는 파트너에 게 기본적으로 제공 됩니다. 이 문서에서는 파트너가 [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/) 기능을 사용 하는 방법을 설명 합니다. 또한 파트너가 고객에 게 Cost Management 액세스를 사용 하도록 설정 하는 방법을 설명 합니다. 고객은 CSP 파트너가 사용 하도록 설정 된 경우 Cost Management 기능을 사용할 수 있습니다.

CSP 파트너는 Cost Management을 사용 하 여 다음을 수행 합니다.

- 송장 발부 된 비용을 이해 하 고 비용을 고객, 구독, 리소스 그룹 및 서비스에 연결 합니다.
- 고객, 구독, 리소스 그룹, 리소스, 측정기, 서비스 및 기타 여러 차원 별로 비용을 분석 하는 기능을 사용 하 여 [비용 분석](quick-acm-cost-analysis.md) 의 Azure 비용에 대 한 직관적인 보기를 확인 하세요.
- 비용 분석에서 PEC (파트너 획득 신용)가 적용 된 리소스 비용을 봅니다.
- 비용이 예산을 초과 하는 경우 프로그래밍 [예산](tutorial-acm-create-budgets.md) 및 경고를 사용 하 여 알림 및 자동화를 설정 합니다.
- Cost Management 데이터에 대 한 고객 액세스를 제공 하는 Azure Resource Manager 정책을 사용 하도록 설정 합니다. 그러면 고객은 [종 량 제 요금](https://azure.microsoft.com/pricing/calculator/)을 사용 하 여 구독에 대 한 소비 비용 데이터를 볼 수 있습니다.

모든 고객에 대 한 비용을 보여 주는 예제는 다음과 같습니다.
모든 고객에 대 한 비용을 보여 주는 ![Example ](./media/get-started-partners/customer-costs1.png)

단일 고객에 대 한 비용을 보여 주는 예제는 다음과 같습니다.
단일 고객에 대 한 비용을 보여 주는 ![Example ](./media/get-started-partners/customer-costs2.png)

Azure Cost Management에서 사용할 수 있는 모든 기능을 REST Api와 함께 사용할 수도 있습니다. Api를 사용 하 여 비용 관리 작업을 자동화 합니다.

## <a name="prerequisites"></a>전제 조건

Azure Cost Management에는 청구 계정 또는 구독에 대 한 읽기 권한이 필요 합니다. 액세스는 리소스의 상위 수준, 즉 청구 계정 또는 관리 그룹에서 앱을 관리 하는 개별 리소스 그룹까지 부여할 수 있습니다. 청구 계정의 Azure Cost Management에 대 한 액세스를 설정 하 고 할당 하는 방법에 대 한 자세한 내용은 [사용자 역할 및 사용 권한 할당](/partner-center/permissions-overview)을 참조 하세요. **전역 관리자** 및 **관리 에이전트** 역할은 청구 계정에 대 한 비용을 관리할 수 있습니다.

지원 되는 계정 유형의 전체 목록을 보려면 [데이터 Cost Management 이해](understand-cost-mgt-data.md)를 참조 하세요.


## <a name="how-cost-management-uses-scopes"></a>Cost Management 범위를 사용 하는 방법

범위는 청구 데이터를 관리 하 고, 지불액과 관련 된 역할이 있으며, 송장을 보고, 일반 계정 관리를 수행 하는 위치입니다. 청구 및 계정 역할은 RBAC를 사용 하는 리소스 관리에 사용 되는 범위와는 별도로 관리 됩니다. 액세스 제어 차이를 비롯 하 여 별도의 범위를 명확 하 게 구분 하기 위해 각각 청구 범위 및 RBAC 범위 라고 합니다.

청구 범위 및 RBAC 범위와 비용 관리가 범위에서 작동 하는 방식을 이해 하려면 [범위 이해 및](understand-work-scopes.md)사용을 참조 하세요.

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>파트너 테 넌 트 요금 청구 범위를 사용 하 여 비용 관리

고객을 Microsoft 고객 계약에 등록 한 후에는 다음 _청구 범위_ 를 테 넌 트에서 사용할 수 있습니다. 범위를 사용 하 여 Cost Management 비용을 관리 합니다.

### <a name="billing-account-scope"></a>청구 계정 범위

청구 계정 범위를 사용 하 여 모든 고객 및 청구 프로필에 대 한 세금을 확인할 수 있습니다. 청구서 비용은 Microsoft 고객 계약의 고객 소비량 기반 제품에 대해서만 표시 됩니다. 그러나 Microsoft 고객 계약과 CSP 제품 모두에서 고객을 위해 구매한 기반 제품에 대해 청구서 비용이 표시 됩니다. 현재 범위에서 비용을 볼 수 있는 기본 통화는 미국 달러입니다. 범위에 대 한 예산은 USD에도 있습니다.

고객 청구 통화에 관계 없이 파트너는 청구 계정 범위를 사용 하 여 예산을 설정 하 고 고객, 구독, 리소스 및 리소스 그룹에 대 한 비용을 USD로 관리 합니다.

또한 파트너는 비용 분석 보기에서 고객 간의 특정 청구 통화 비용을 필터링 합니다. **실제 비용** 목록을 선택 하 여 지원 되는 고객 청구 통화의 비용을 확인 합니다.

![통화에 대 한 실제 비용 선택을 보여 주는 예제](./media/get-started-partners/actual-cost-selector.png)

청구 범위에서 [분할 상환 cost 뷰](quick-acm-cost-analysis.md#customize-cost-views) 를 사용 하 여 예약 된 기간 동안 예약 인스턴스 분할 상환 비용을 볼 수 있습니다.

### <a name="billing-profile-scope"></a>청구 프로필 범위

청구 프로필 범위를 사용 하 여 청구서에 포함 된 모든 제품 및 구독에 대해 모든 고객의 청구 통화에 대 한 세금을 확인할 수 있습니다. **InvoiceID** 필터를 사용 하 여 특정 송장의 청구 프로필에 대 한 비용을 필터링 할 수 있습니다. 필터는 특정 송장의 소비 및 제품 구매 비용을 보여 줍니다. 청구서의 특정 고객에 대 한 비용을 필터링 하 여 세 전 비용을 확인할 수도 있습니다.

Microsoft 고객 계약에 고객을 등록 한 후 Microsoft 고객 계약에 따라 고객에 대 한 모든 제품 (소비, 구매 및 권리)에 대 한 모든 요금이 포함 된 송장을 받게 됩니다. 동일한 통화로 청구 될 때 이러한 청구서에는 SaaS, Azure Marketplace 및 SaaS와 같은 구매한 제품에 대 한 요금 (CSP 제안에 아직 있는 고객에 대 한 예약)도 포함 됩니다.

고객 청구서에 대 한 요금을 조정 하기 위해 청구 프로필 범위를 사용 하 여 고객의 청구서에 대해 계산 되는 모든 비용을 확인할 수 있습니다. 청구서와 마찬가지로 범위는 새 Microsoft 고객 계약의 모든 고객에 대 한 비용을 표시 합니다. 범위에는 현재 CSP 제품의 고객 자격 제품에 대 한 모든 요금이 표시 됩니다.

청구 프로필 및 청구 계정 범위는 Azure Marketplace 및 예약 구매와 같은 권한 부여 및 구매 기반 제품에 대 한 요금을 보여 주는 유일한 적용 가능한 범위입니다.

청구 프로필은 청구서에 포함 되는 구독을 정의 합니다. 청구 프로필은 기업 계약 등록에 해당 하는 기능입니다. 청구 프로필은 송장이 생성 되는 범위입니다.

현재 청구 프로필 범위에서 비용을 볼 때 고객의 청구 통화는 기본 통화입니다. 청구 프로필 범위의 예산 집합은 청구 통화입니다.

파트너는이 범위를 사용 하 여 송장을 조정할 수 있습니다. 또한 범위를 사용 하 여 다음 항목에 대 한 청구 통화의 예산을 설정 합니다.

- 필터링 된 특정 송장
- 고객
- Subscription
- Resource group
- 리소스
- Azure 서비스
- 미터
- ResellerMPNID

### <a name="customer-scope"></a>고객 범위

파트너는 범위를 사용 하 여 Microsoft 고객 계약에 등록 하는 고객과 관련 된 비용을 관리 합니다. 이 범위를 통해 파트너는 특정 고객에 대 한 세 전 비용을 볼 수 있습니다. 특정 구독, 리소스 그룹 또는 리소스에 대 한 세 전 비용을 필터링 할 수도 있습니다.

고객 범위에는 현재 CSP 제품에 있는 고객이 포함 되지 않습니다. 범위에는 Microsoft 고객 계약이 있는 고객만 포함 됩니다. Azure 사용량이 아닌 권한 부여 비용은 고객 필터를 적용할 때 고객에 게 청구 계정 및 청구 프로필 범위에서 제공 됩니다.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Cost Management에서 청구 범위에 대 한 파트너 액세스

**전역 관리자** 및 **관리 에이전트** 역할이 있는 사용자만 파트너의 Azure 테 넌 트에서 직접 청구 계정, 청구 프로필 및 고객에 대 한 비용을 관리 하 고 볼 수 있습니다. 파트너 센터 역할에 대 한 자세한 내용은 [사용자 역할 및 사용 권한 할당](/partner-center/permissions-overview)을 참조 하세요.

### <a name="enable-cost-management-in-the-customer-tenant"></a>고객 테 넌 트에서 비용 관리 사용

파트너는 고객이 Microsoft 고객 계약에 등록 한 후 Cost Management에 대 한 액세스를 사용할 수 있습니다. 그런 다음 파트너는 고객이 종 량 제 요금으로 계산 된 비용을 볼 수 있도록 하는 정책을 사용 하도록 설정할 수 있습니다. 비용은 RBAC 구독 및 리소스 그룹 범위에서 사용 되는 사용량에 대 한 고객의 청구 통화로 표시 됩니다.

파트너가 비용 표시를 위한 정책을 사용 하도록 설정 하는 경우 구독에 대 한 Azure Resource Manager 액세스 권한이 있는 사용자는 종 량 제 요금으로 비용을 관리 하 고 분석할 수 있습니다. Azure 구독에 대 한 적절 한 RBAC 액세스 권한이 있는 대리점 및 고객은 효과적으로 비용을 볼 수 있습니다.

정책에 관계 없이 파트너는 구독 및 리소스 그룹에 대 한 액세스 권한이 있는 경우에도 비용을 볼 수 있습니다.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>정책을 사용 하 여 Azure 사용 요금 보기

파트너는 다음 정보를 사용 하 여 정책에서 고객에 대 한 Azure 사용 요금을 볼 수 있도록 합니다.

Azure Portal에서 파트너 테 넌 트에 로그인 하 고 **Cost Management + 청구**를 클릭 합니다. 청구 계정을 선택한 다음 **고객**을 클릭 합니다. 고객 목록은 청구 계정과 연결 됩니다.

고객 목록에서 비용을 볼 수 있도록 허용 하려는 고객을 선택 합니다.

![Cost Management에서 고객 선택](./media/get-started-partners/customer-list.png)

**설정**아래에서 **정책**을 클릭 합니다.

선택한 고객에 대 한 구독과 연결 된 **Azure 사용** 요금에 대 한 현재 비용 표시 정책이 표시 됩니다.
고객이 종 량 제 요금을 볼 수 있도록 ![Policy ](./media/get-started-partners/cost-management-billing-policies.png)

정책이 **아니요**로 설정 된 경우에는 고객과 연결 된 구독 사용자에 대해 Azure Cost Management를 사용할 수 없습니다. 파트너가 사용 하도록 설정 되지 않은 경우 모든 구독 사용자에 대해 기본적으로 비용 표시 정책이 사용 되지 않습니다.

비용 정책이 **예**로 설정 된 경우 고객 테 넌 트에 연결 된 구독 사용자는 종 량 제 요금으로 사용 요금을 볼 수 있습니다.

비용 표시 정책을 사용 하는 경우 구독 사용이 있는 모든 서비스는 종 량 제 요금으로 비용을 표시 합니다. 예약 사용은 실제 및 분할 상환 비용에 대 한 요금이 0으로 표시 됩니다. 구매 및 자격은 특정 구독에 연결 되지 않습니다. 따라서 구매는 구독 범위에 표시 되지 않습니다.

고객 테 넌 트에 대 한 비용을 보려면 Cost Management + 청구를 연 후 대금 청구 계정을 클릭 합니다. 청구 계정 목록에서 청구 계정을 클릭 합니다.

![청구 계정 선택](./media/get-started-partners/select-billing-account.png)

**청구**아래에서 **Azure 구독**을 클릭 한 다음 고객을 클릭 합니다.

![Azure 구독 고객 선택](./media/get-started-partners/subscriptions-select-customer.png)

**비용 분석** 을 클릭 하 고 비용 검토를 시작 합니다.
이제 종 량 제 요금 기반 비용으로 구독 및 리소스 그룹 RBAC 범위에서 비용 분석, 예산 및 경고를 사용할 수 있습니다.

![고객으로 비용 분석 보기 ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

RBAC 범위에서 예약 된 인스턴스에 대 한 분할 상환 보기 및 실제 비용은 0 요금을 표시 합니다. 예약 인스턴스 비용은 구매가 수행 된 청구 범위에만 표시 됩니다.

## <a name="analyze-costs-in-cost-analysis"></a>비용 분석에서 비용 분석

파트너는 특정 고객 또는 청구서에 대해 고객 전체의 비용 분석 비용을 탐색 하 고 분석할 수 있습니다. 필터 및 그룹화 방법 기능을 사용 하면 다음을 비롯 한 여러 필드를 기준으로 비용을 분석할 수 있습니다.

| **필드** | **설명** |
| --- | --- |
| 대 여 | 파트너 Azure Active Directory 테 넌 트의 식별자 |
| PartnerName | 파트너 Azure Active Directory 테 넌 트의 이름 |
| CustomerTenantID | 고객 구독의 Azure Active Directory 테 넌 트 식별자 |
| CustomerName | 고객의 구독을 포함 하는 Azure Active Directory 테 넌 트의 이름입니다. |
| ResellerMPNID | 구독과 연결 된 재판매인의 MPNID |
| 구독 ID | Azure 구독에 대 한 고유한 Microsoft 생성 식별자 |
| subscriptionName | Azure 구독의 이름 |
| billingProfileID | 청구 프로필의 식별자입니다. 고객 간에 단일 요금 청구 통화로 청구서에서 비용을 그룹화 합니다.
| invoiceID | 특정 트랜잭션이 표시 되는 송장의 청구서 ID |
| resourceGroup | Azure 리소스 그룹의 이름입니다. 리소스 수명 주기 관리에 사용 됩니다. |
| partnerEarnedCreditRate | 파트너 관리자 링크 액세스를 기반으로 하는 파트너 획득 신용 (PEC)가 있는 경우 할인 요금이 적용 됩니다. |
| partnerEarnedCreditApplied | 파트너의 획득 크레딧을 적용 했는지 여부를 나타냅니다. |

[비용 분석](quick-acm-cost-analysis.md) 보기에서는 [보기를 저장](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) 하 고 데이터를 [CSV 및 PNG 파일로](quick-acm-cost-analysis.md#automation-and-offline-analysis)내보낼 수도 있습니다.

## <a name="view-partner-earned-credit-pec-resource-costs"></a>파트너 획득 크레딧 (PEC) 리소스 비용 보기

Azure Cost Management에서 파트너는 비용 분석을 사용 하 여 PEC 혜택을 받은 비용을 볼 수 있습니다.

Azure Portal에서 파트너 테 넌 트에 로그인 하 고 **Cost Management + 청구**를 선택 합니다. **Cost Management**에서 **비용 분석**을 클릭 합니다.

비용 분석 보기에는 파트너에 대 한 청구 계정의 비용이 표시 됩니다. 파트너, 특정 고객 또는 청구 프로필에 대 한 필요에 따라 청구서를 조정 하는 **범위** 를 선택 합니다.

도넛형 차트에서 드롭다운 목록을 클릭 하 고 **PartnerEarnedCreditApplied** 를 선택 하 여 PEC 비용을 분석 합니다.

![파트너 획득 크레딧을 보는 방법을 보여 주는 예제](./media/get-started-partners/cost-analysis-pec1.png)

**PartnerEarnedCreditApplied** 속성이 _True_인 경우 연결 된 비용은 파트너의 획득 관리자 액세스를 제공 합니다.

**PartnerEarnedCreditApplied** 속성이 _False_인 경우 관련 비용이 크레딧에 필요한 자격을 충족 하지 않습니다. 또는 구매한 서비스는 파트너의 획득 크레딧을 받을 자격이 없습니다.

서비스 사용 데이터는 일반적으로 Cost Management에 표시 되는 데 8-24 시간이 걸립니다. 자세한 내용은 [사용 현황 데이터 업데이트 빈도](understand-cost-mgt-data.md#usage-data-update-frequency-varies)변경을 참조 하세요. PEC 크레딧을 Azure Cost Management의 액세스 시간에서 48 시간 이내에 표시 됩니다.


**그룹화 방법** 옵션을 사용 하 여 **PartnerEarnedCreditApplied** 속성을 기준으로 그룹화 하 고 필터링 할 수도 있습니다. 이러한 옵션을 사용 하 여 PEC가 없는 비용을 검사할 수 있습니다.

![파트너-획득 크레딧을 그룹화 하거나 필터링 합니다.](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="cost-management-rest-apis"></a>REST Api Cost Management

파트너와 고객은 다음 섹션에서 설명 하는 Cost Management Api를 사용 하 여 일반적인 작업을 수행할 수 있습니다.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management Api-직접 및 간접 공급자

파트너 테 넌 트의 청구 범위에 대 한 액세스 권한이 있는 파트너는 다음 Api를 사용 하 여 송장 발부 된 비용을 볼 수 있습니다.

구독 범위의 Api는 구독에 대 한 액세스 권한이 있는 경우 비용 정책에 관계 없이 파트너에 의해 호출 될 수 있습니다. 고객 또는 재판매인과 같이 구독에 대 한 액세스 권한이 있는 다른 사용자는 파트너가 고객 테 넌 트에 대 한 비용 정책을 사용 하도록 설정한 후에만 Api를 호출할 수 있습니다.


#### <a name="to-get-a-list-of-billing-accounts"></a>청구 계정 목록을 가져오려면

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>고객 목록을 가져오려면

```
armclient get "providers/Microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>구독 목록을 가져오려면

```
armclient get "/providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>고객이 비용을 볼 수 있는 정책을 가져오려면

```
armclient get "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>비용을 볼 수 있도록 고객에 대 한 정책을 설정 하려면

```
armclient put "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/policies/default?api-version=2019-10-01-preview" @policy.json
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>청구 계정에 대 한 Azure 서비스 사용을 가져오려면

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>고객의 Azure 서비스 사용을 다운로드 하려면

다음 get 호출은 비동기 작업입니다.

```
armclient get providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

응답에서 반환 된 `Location` URI를 호출 하 여 작업 상태를 확인 합니다. 상태가 *완료*됨 인 경우 `downloadUrl` 속성에는 생성 된 보고서를 다운로드 하는 데 사용할 수 있는 링크가 포함 되어 있습니다.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>사용 되는 Azure 서비스에 대 한 가격 책정 시트를 다운로드 하거나 다운로드 하려면

먼저 다음 게시물을 사용 합니다.

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/billingProfiles/YYYY-YYYY-YYY-YYYY-YYY/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

그런 다음 비동기 작업 속성 값을 호출 합니다. 다음은 그 예입니다.

```
armclient get "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/billingProfiles/YYYY-YYYY-YYY-YYYY-YYY/pricesheetDownloadOperations/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX?sessiontoken=0:11186&api-version=2019-10-01-preview"
```
위의 get 호출은 가격표를 포함 하는 다운로드 링크를 반환 합니다.

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>지난 2 달 동안의 고객 비용을 얻으려면 월별로 정렬 합니다.

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>지난 2 달 동안 Azure 구독 비용을 얻으려면 월별로 정렬 합니다.

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>이번 달에 대 한 일별 비용을 얻으려면

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="create-a-budget-for-a-partner"></a>파트너에 대 한 예산 만들기

```
armclient put providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01 @budgetCreate.json
```


#### <a name="create-a-budget-for-a-customer"></a>고객에 대 한 예산 만들기

```
armclient put providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/providers/Microsoft.Consumption/budgets/test-partner-demo?api-version=2019-10-01 @budgetCreate.json
```
#### <a name="delete-a-budget"></a>예산 삭제

```
armclient delete providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```


## <a name="next-steps"></a>다음 단계
- Cost Management [비용 분석 시작](quick-acm-cost-analysis.md)
- Cost Management에서 [예산 작성 및 관리](tutorial-acm-create-budgets.md)
