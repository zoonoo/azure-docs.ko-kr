---
title: Azure Portal에서 청구 계정 보기 | Microsoft Docs
description: Azure Portal에서 청구 계정을 보는 방법을 알아봅니다.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 2768d6e146a37e86bb36353f661179ebd7b5033d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994130"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Azure Portal의 요금 청구 계정 및 범위

청구 계정은 Azure를 사용하기 위해 가입할 때 생성됩니다. 청구 계정을 사용하여 청구서, 결제를 관리하고 비용을 추적할 수 있습니다. 여러 청구 계정에 액세스할 수 있습니다. 예를 들어 개인 프로젝트를 위해 Azure에 가입한 경우가 있을 수 있습니다. 또한 조직의 기업계약 또는 Microsoft 고객 계약을 통해 액세스할 수 있습니다. 이러한 각 시나리오의 경우 별도의 청구 계정을 가질 수 있습니다.

Azure Portal는 다음과 같은 유형의 청구 계정을 지원 합니다.

- **Microsoft Online Services 프로그램**: azure 웹 사이트를 통해 azure에 등록할 때 Microsoft Online services 프로그램의 청구 계정이 생성 됩니다. 예를 들어 [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)에 가입하는 경우 [종량제 요금을 사용하는 계정](https://azure.microsoft.com/offers/ms-azr-0003p/) 또는 [Visual studio 구독자](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) 자격입니다.

- **기업계약**: 조직에서 Azure를 사용 하기 위해 [EA (기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/) 에 서명 하면 기업계약 청구 계정이 만들어집니다.

- **Microsoft 고객 계약**: microsoft 고객 계약에 대 한 청구 계정은 microsoft 담당자와 협력 하 여 Microsoft 고객 계약에 서명할 때 생성 됩니다. [종 량 제 요금](https://azure.microsoft.com/offers/ms-azr-0003p/) 또는 [azure 무료 계정](https://azure.microsoft.com/offers/ms-azr-0044p/) 에 대 한 azure 웹 사이트를 통해 등록 하는 선택 지역의 일부 고객은 Microsoft 고객 계약에 대 한 청구 계정도 가질 수 있습니다. 자세한 내용은 [Microsoft 고객 계약의 청구 계정 시작](../understand/mca-overview.md)을 참조하세요.

- **Microsoft 파트너 규약**: CSP (클라우드 솔루션 공급자) 파트너가 새 상거래 환경에서 고객을 관리 하기 위해 Microsoft 파트너 규약에 대 한 청구 계정을 만듭니다. 파트너는 [Azure 요금제](https://docs.microsoft.com/partner-center/purchase-azure-plan) 를 사용 하 여 Azure Portal에서 청구 계정을 관리 하는 고객이 하나 이상 있어야 합니다. 자세한 내용은 [Microsoft 파트너 계약에 대 한 청구 계정 시작](../understand/mpa-overview.md)을 참조 하세요.

청구 계정의 유형을 확인하려면 [청구 계정의 유형 확인](#check-the-type-of-your-account)을 참조하세요.

## <a name="scopes-for-billing-accounts"></a>청구 계정의 범위
범위는 청구 계정 내에서 청구를 보고 관리 하는 데 사용 하는 노드입니다. 여기에서 청구 데이터, 지불, 송장을 관리 하 고 일반 계정 관리를 수행 합니다.

### <a name="microsoft-online-services-program"></a>Microsoft Online Services 프로그램

 ![MOSP 계층 구조를 표시 하는 스크린샷](./media/view-all-accounts/mosp-hierarchy.png)

|범위  |정의  |
|---------|---------|
|청구 계정     | 고객이 Azure를 사용 하기 위해 수락 하는 규약을 나타냅니다. 하나 이상의 구독을 포함 합니다.  |
|Subscription     |  Azure 리소스의 그룹화를 나타냅니다. 이 범위에서 청구서가 생성됩니다. 지불 방법 및 사용 주소와 같은 기타 청구 정보는이 범위에 연결 됩니다.|

### <a name="enterprise-agreement"></a>기업계약

![EA 계층 구조를 표시 하는 스크린샷](./media/view-all-accounts/ea-hierarchy.png)

|범위  |정의  |
|---------|---------|
|청구 계정    | 기업계약 등록을 나타냅니다. 하나 이상의 부서와 계정이 포함 되어 있습니다. 이 범위에서 청구서가 생성됩니다. |
|department     |  비용을 논리적 그룹화로 분할 하 고 예산을 설정 하기 위한 선택적 계정 그룹입니다.     |
|계정     |  단일 계정 소유자를 나타냅니다. 계정 소유자에 게는 등록에 대해 청구 되는 Azure 구독을 만들고 관리할 수 있는 권한이 있습니다. |

### <a name="microsoft-customer-agreement"></a>Microsoft 고객 계약

![MCA 계층 구조를 보여 주는 스크린샷](./media/view-all-accounts/mca-hierarchy.png)

|범위  |작업  |
|---------|---------|
|청구 계정     |   고객이 Microsoft 제품 및 서비스를 사용 하기 위해 허용 하는 규약을 나타냅니다. 하나 이상의 청구 프로필을 포함 합니다. |
|청구 프로필     |   지불 방법 및 청구 주소와 같은 송장 및 관련 청구 정보를 나타냅니다. 송장 섹션이 하나 이상 포함 되어 있습니다. |
|청구서 섹션     |   송장의 비용 그룹화를 나타냅니다. Azure 구독 및 Azure Marketplace 및 앱 원본 제품과 같은 기타 구매는이 범위에 연결 됩니다.    |

### <a name="microsoft-partner-agreement"></a>Microsoft 파트너 계약

![MPA 계층 구조를 보여 주는 스크린샷](./media/view-all-accounts/mpa-hierarchy.png)

|범위  |작업  |
|---------|---------|
|청구 계정     |   새 상거래 환경에서 고객의 Microsoft 제품 및 서비스를 관리 하는 파트너 규약을 나타냅니다. 하나 이상의 청구 프로필 및 고객을 포함 합니다.   |
|청구 프로필     |   통화에 대 한 송장을 나타냅니다.     |
|Customer    |   CSP (클라우드 솔루션 공급자) 파트너에 대 한 고객을 나타냅니다.  Azure 구독 및 Azure Marketplace 및 앱 원본 제품과 같은 기타 구매는이 범위에 연결 됩니다.  |
|Reseller    |   고객에 게 서비스를 제공 하는 대리점입니다. 구독에 대 한 선택적 필드 이며 CSP 2 계층 모델의 간접 공급자에만 적용 됩니다.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Azure Portal에서 청구 범위 전환

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. 개요 페이지에서 **범위 전환**을 선택 합니다.

   ![청구 범위를 보여 주는 스크린샷](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > 한 범위에만 액세스할 수 있는 경우에는 스위치 범위가 표시 되지 않습니다.

4. 세부 정보를 볼 범위를 선택합니다.

   ![청구 범위를 보여 주는 스크린샷](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>계정 유형 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>도움이 필요하세요? 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- [비용 분석](../costs/quick-acm-cost-analysis.md)을 시작하는 방법을 알아봅니다.
