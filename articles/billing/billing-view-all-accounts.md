---
title: Azure Portal에서 청구 계정 보기 | Microsoft Docs
description: Azure Portal에서 청구 계정을 보는 방법을 알아봅니다.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 6df787dbc06f7ee56ba9a26c8382396bf3b6e74a
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719590"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Azure Portal에서 청구 계정 보기  

청구 계정은 Azure를 사용하기 위해 가입할 때 생성됩니다. 청구 계정을 사용하여 청구서, 결제를 관리하고 비용을 추적할 수 있습니다. 여러 청구 계정에 액세스할 수 있습니다. 예를 들어 개인 프로젝트를 위해 Azure에 가입한 경우가 있을 수 있습니다. 또한 조직의 기업계약 또는 Microsoft 고객 계약을 통해 액세스할 수 있습니다. 이러한 각 시나리오의 경우 별도의 청구 계정을 가질 수 있습니다.

현재 Azure Portal에서는 다음과 같은 유형의 청구 계정을 지원합니다.

- **Microsoft Online Services 프로그램**: Microsoft Online Services 프로그램의 청구 계정은 Azure 웹 사이트를 통해 Azure에 가입할 때 생성됩니다. 예를 들어 [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)에 가입하는 경우 [종량제 요금을 사용하는 계정](https://azure.microsoft.com/offers/ms-azr-0003p/) 또는 [Visual studio 구독자](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) 자격입니다.

- **기업 계약**: 기업계약에 대한 청구 계정은 조직에서 Azure를 사용하기 위해 [EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)에 서명할 때 생성됩니다.

- **Microsoft 고객 계약**: Microsoft 고객 계약에 대한 청구 계정은 조직이 Microsoft 담당자와 협력하여 Microsoft 고객 계약에 서명할 때 생성됩니다. Azure 웹 사이트를 통해 [종량제 요금을 사용한 계정](https://azure.microsoft.com/offers/ms-azr-0003p/)에 가입하거나 [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)을 업그레이드한 특정 지역의 일부 고객은 Microsoft 고객 계약에 대한 청구 계정도 가질 수도 있습니다. 자세한 내용은 [Microsoft 고객 계약의 청구 계정 시작](billing-mca-overview.md)을 참조하세요.

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>청구 계정의 범위
범위는 청구 계정 내에서 사용자가 청구를 보고 관리하는 데 사용하는 노드입니다. 사용자가 청구 데이터, 결제, 청구서를 관리하고 일반 계정 관리를 수행하는 위치입니다. 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services 프로그램

|범위  |정의  |
|---------|---------|
|청구 계정     | 하나 이상의 Azure 구독에 대한 단일 소유자(계정 관리자)를 나타냅니다. 계정 관리자는 구독 만들기, 청구서 보기 또는 구독에 대한 청구 변경 등의 다양한 청구 작업을 수행할 수 있는 권한이 있습니다.  |
|Subscription     |  Azure 리소스의 그룹화를 나타냅니다. 이 범위에서 청구서가 생성됩니다. 청구서를 결제하는 데 사용되는 고유한 결제 방법이 있습니다.|


### <a name="enterprise-agreement"></a>기업 계약

|범위  |정의  |
|---------|---------|
|청구 계정    | 기업계약 등록을 나타냅니다. 이 범위에서 청구서가 생성됩니다. 부서 및 등록 계정을 사용하여 구성됩니다.  |
|department     |  등록 계정의 선택적 그룹화입니다.      |
|등록 계정     |  단일 계정 소유자를 나타냅니다. Azure 구독은 이 범위 아래에 생성됩니다.  |


### <a name="microsoft-customer-agreement"></a>Microsoft 고객 계약

|범위  |작업  |
|---------|---------|
|청구 계정     |   여러 Microsoft 제품 및 서비스에 대한 고객 계약을 나타냅니다. 청구 프로필 및 청구서 섹션을 사용하여 구성됩니다.   |
|청구 프로필     |  청구서 및 결제 방법을 나타냅니다. 이 범위에서 청구서가 생성됩니다. 청구서 섹션은 여러 개 있을 수 있습니다.      |
|청구서 섹션     |   청구서의 비용 그룹을 나타냅니다. 구독과 기타 구매는 이 범위에 연결됩니다.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Azure Portal에서 청구 범위 전환


1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. 왼쪽에서 **모든 청구 범위**를 선택합니다.

   ![모든 청구 범위를 보여주는 스크린샷](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** 한 범위에만 액세스할 수 있는 경우에는 **모든 청구 범위**가 표시되지 않습니다.

4. 세부 정보를 볼 범위를 선택합니다.



## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- [비용 분석](../cost-management/quick-acm-cost-analysis.md)을 시작하는 방법을 알아봅니다.