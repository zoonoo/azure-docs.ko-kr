---
title: Azure portal에서 청구 계정 보기 | Microsoft Docs
description: Azure portal에서 청구 계정의 보는 방법에 알아봅니다.
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
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 36430e9b0a4554761d53b537d3c32fa57068eabb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490209"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Azure portal에서 청구 계정 보기  

대금 청구 계정에 Azure를 사용 하도록 등록할 때 만들어집니다. 청구 계정을 사용 하 여 청구서 지불을 관리 하 고 비용을 추적할 합니다. 여러 청구 계정에 대 한 액세스를 할 수 있습니다. 예를 들어 수 등록 한 Azure에 대 한 개인 프로젝트에 대 한 합니다. 조직의 기업 계약 또는 Microsoft 고객 계약을 통해 액세스할을 수도 수 있습니다. 이러한 시나리오 각각에 대 한 별도 청구 계정을 사용 해야 합니다.

Azure portal에는 현재 다음과 같은 유형의 청구 계정 지원합니다.

- **Microsoft Online Services 프로그램**: Microsoft Online Services 프로그램에 대 한 대금 청구 계정에 Azure 웹 사이트를 통해 Azure에 등록할 때 만들어집니다. 예를 들어, 로그인 하면 등록을 [Azure 무료 계정](https://azure.microsoft.com/offers/ms-azr-0044p/), [종 량 제 요금으로 계정](https://azure.microsoft.com/offers/ms-azr-0003p/) 또는 [Visual studio 구독자](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)합니다.

- **기업 계약**: 조직에 기업 계약에 대 한 대금 청구 계정을 생성 됩니다는 [EA (기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/) Azure를 사용 하도록 합니다.

- **Microsoft 고객 계약**: 조직에 Microsoft 고객 계약을 서명 하려면 Microsoft 담당자와 작동 하는 경우 Microsoft 고객 계약에 대 한 대금 청구 계정을 생성 됩니다. 선택 지역에서 Azure 웹 사이트를 통해 등록 하는 일부 고객이 [종 량 제 요금으로 계정을](https://azure.microsoft.com/offers/ms-azr-0003p/) 업그레이드 또는 해당 [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/) Microsoft 고객에 대 한 대금 청구 계정이 있을 수 있습니다 계약에도 해당 합니다. 자세한 내용은 [Microsoft 고객 계약에 대 한 대금 청구 계정 시작](billing-mca-overview.md)합니다.

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>계정 청구에 대 한 범위
범위는 보기 및 청구를 관리 하는 데 사용할 청구 계정 내에서 노드입니다. 사용자가 청구 데이터, 지불 송장이 관리 하 고 일반 계정 관리를 수행 하는 위치 이며 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services 프로그램

|Scope  |정의  |
|---------|---------|
|청구 계정     | 하나 이상의 Azure 구독에 대 한 단일 소유자를 (계정 관리자)를 나타냅니다. 계정 관리자는 구독을 만들고, 청구서 보기 또는 구독에 대 한 청구 변경 등의 다양 한 청구 작업을 수행 하도록 허용 합니다.  |
|구독     |  Azure 리소스의 그룹화를 나타냅니다. 청구서는이 범위에서 생성 됩니다. 해당 청구서를 지불 하는 데 사용 되는 자체 지불 메서드를가지고 있습니다.|


### <a name="enterprise-agreement"></a>기업 계약

|Scope  |정의  |
|---------|---------|
|청구 계정    | 기업 계약 등록을 나타냅니다. 청구서는이 범위에서 생성 됩니다. 이 부서 및 등록 계정을 사용 하 여 구조화 됩니다.  |
|department     |  등록 계정의 그룹화 선택 사항입니다.      |
|등록 계정     |  단일 계정 소유자를 나타냅니다. Azure 구독이이 범위에서 생성 됩니다.  |


### <a name="microsoft-customer-agreement"></a>Microsoft 고객 계약

|Scope  |태스크  |
|---------|---------|
|청구 계정     |   여러 Microsoft 제품 및 서비스에 대 한 고객 계약을 나타냅니다. 청구 프로필 및 송장 섹션을 사용 하 여 구조화 된 것입니다.   |
|청구 프로필     |  송장 및 지불 메서드를 나타냅니다. 청구서는이 범위에서 생성 됩니다. 해당 청구서 섹션이 여러 개 있을 수 있습니다.      |
|송장 섹션     |   송장에 대 한 비용의 그룹을 나타냅니다. 구독 및 다른 구매가이 범위에 연결 됩니다.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Azure portal에서 청구 범위 전환


1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 검색할 **비용 관리 + 청구**합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. 선택 **모든 청구 범위** 왼쪽에서 합니다.

   ![모든 청구 범위를 보여 주는 스크린샷](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   \* * 표시 되지 것입니다 **모든 청구 범위** 하나의 범위에 대 한 액세스 권한만 있으면 됩니다.

4. 세부 정보를 보려면 범위를 선택 합니다.



## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계
- 시작 하는 방법을 알아봅니다 [비용 분석](../cost-management/quick-acm-cost-analysis.md)합니다.