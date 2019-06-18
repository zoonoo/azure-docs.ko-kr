---
title: Microsoft 고객 계약-Azure의에서 기업 계약 작업을 완료 | Microsoft Docs
description: 새 청구 계정에 기업 계약 작업을 완료 하는 방법을 알아봅니다.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2018
ms.author: banders
ms.openlocfilehash: 9b76fa935263904957c87cd062c84d0607771369
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371339"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 대금 청구 계정에 기업 계약 작업을 완료 합니다.

조직에 기업 계약 등록을 갱신 하는 Microsoft 고객 계약을 서명 하는 경우 새 청구 계정 규약에 대해 만들어집니다. 새 계정에 청구는 기업 계약 다르게 구성 됩니다. 이 문서에서는 새 청구 계정을 사용 하 여 기업 계약에서 수행한 작업을 수행 하는 방법을 설명 합니다.

## <a name="how-billing-is-organized-in-the-new-account"></a>새 계정에 청구 구성 방법

다음 다이어그램은 새 청구 계정에 청구 구성 되는 방식을 설명 합니다.

![Ea-mca-post-전환-계층의 이미지](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| 기업 계약   | Microsoft 고객 계약    |
|------------------------|--------------------------------------------------------|
| 등록            | 청구 프로필을 사용 하 여 기업 계약 등록 하 여 조직에 대 한 청구를 관리 합니다. 엔터프라이즈 관리자를 사용 하면 청구 프로필의 소유자로 합니다. 청구 프로필에 대 한 자세한 내용은 참조 하세요 [청구 프로필을 이해](billing-mca-overview.md#understand-billing-profiles)합니다.
| department            | 기업 계약 등록 학부에 고 비용을 구성 하는 송장 섹션을 사용 합니다. 부서 송장 섹션 되며 각 청구서 섹션의 소유자로 부서 관리자. 송장 섹션에 대 한 자세한 내용은 참조 하세요 [이해 송장 섹션](billing-mca-overview.md#understand-invoice-sections)합니다. |
| 계좌               | 기업 계약에서 생성 된 계정은 새 청구 계정에 지원 되지 않습니다. 계정의 구독 부서에 대 한 해당 청구서 섹션에 속합니다. 계정 소유자를 만들고 해당 청구서 섹션에 대 한 구독을 관리할 수 있습니다. |

## <a name="changes-for-enterprise-administrators-in-the-new-billing-account"></a>엔터프라이즈 관리자는 새 청구 계정에 대 한 변경 내용

다음 변경 내용을 엔터프라이즈 관리자가 Microsoft 고객 계약을 갱신 하는 기업 계약이 적용 됩니다.

- 청구 프로필 등록에 대해 만들어집니다. 기업 계약 등록 같은 조직에 대 한 청구를 관리 하는 청구 프로필을 사용 합니다. 청구 프로필에 자세히 알아보려면 [청구 프로필을 이해](billing-mca-overview.md#understand-billing-profiles)합니다.
- 송장 섹션을 각 부서 기업 계약 등록에 대해 생성 됩니다. 부서를 관리 하는 송장 섹션을 사용 합니다. 추가 부서를 설정 하려면 새 청구서 섹션을 만들 수 있습니다. 송장 섹션에 대 한 자세한 내용은 참조 하세요 [청구서 구역 이해](billing-mca-overview.md#understand-invoice-sections)합니다.
- 다른 사용자가 기업 계약 등록에서 만든 계정은 같은 Azure 구독을 만들 수 있는 권한이 있도록 송장 섹션에서 Azure 구독 작성자 역할을 사용 합니다.
- 사용 하 여 합니다 [Azure portal](https://portal.azure.com) Azure EA 포털 대신 조직에 대 한 청구를 관리 합니다.

다음 역할은 새 청구 계정에 제공 됩니다.

**청구 프로필 소유자** -계약에 서명한 경우 생성 된 청구 프로필의 청구 프로필 소유자 역할이 할당 됩니다. 역할에는 조직에 대 한 청구를 관리할 수 있습니다. 요금과 청구서를 보려면 청구서의 비용을 구성 및 결제 방법 관리 조직의 대금 청구에 대 한 액세스를 제어할 수 있습니다.

**송장 섹션 소유자** -기업 계약 등록의 학과 대해 만들어진 모든 송장 섹션에서 송장 섹션 소유자 역할이 할당 됩니다. 역할을 사용 하면 Azure 구독을 만들고 다른 제품을 구입할 수 있는 사용자를 제어 합니다.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>조직에 대 한 요금과 크레딧 잔액 확인

청구 프로필을 사용 하 여 조직에 기업 계약 등록과 비슷합니다에 대 한 요금과 Azure 크레딧 잔액을 추적할 수 있습니다.

청구 프로필에 대 한 크레딧 잔액을 보는 방법에 알아보려면 참조 [청구 프로필에 대 한 Azure 크레딧 잔액을 추적할](billing-mca-check-azure-credits-balance.md)합니다.

청구 프로필에 대 한 요금을 보는 방법에 알아보려면 참조 [Microsoft 고객 계약의 청구서 요금 이해](billing-mca-understand-your-bill.md)합니다.

### <a name="view-charges-for-a-department"></a>부서에 대 한 요금 보기

송장 섹션을 기업 계약에서 제공 되었던 각 부서에 대해 생성 됩니다. Azure portal에는 송장 섹션에 대 한 요금을 볼 수 있습니다. 자세한 내용은 [청구서 섹션에서 트랜잭션을 보고](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections)합니다.

### <a name="view-charges-for-an-account"></a>계정에 대 한 요금 보기

기업 계약 등록에서 만들어진 계정은 새 청구 계정에 지원 되지 않습니다. 계정의 구독 부서에 대 한 해당 청구서 섹션에 속합니다. 계정 소유자를 만들고 해당 청구서 섹션에 대 한 구독을 관리할 수 있습니다.

계정에 속한 구독에 대 한 집계 비용을 보려면 각 구독에 대 한 비용 센터를 설정 해야 합니다. 비용 센터 별로 등록을 필터링 하 여 Azure 사용량 및 요금이 csv 파일을 사용할 수 있습니다.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>다운로드 사용량 및 요금이 csv, 가격표, 및 세금 문서

월별 청구서는 대금 청구 계정에서 청구 각 프로필에 대해 생성 됩니다. 각 청구서에 대해 해당 하는 경우 Azure 사용량 및 요금이 csv 파일, 가격표, 및 세금 문서 다운로드할 수 있습니다. 또한 이번 달 요금에 대 한 Azure 사용량 및 요금이 csv 파일을 다운로드할 수 있습니다.

Azure 사용량 및 요금이 csv 파일을 다운로드 하는 방법에 알아보려면 참조 [Microsoft 고객 계약에 대 한 사용 현황 다운로드](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement)합니다.

가격표를 다운로드 하는 방법에 알아보려면 참조 [Microsoft 고객 계약에 대 한 가격 책정 다운로드](billing-ea-pricing.md#view-and-download-pricing-for-your-microsoft-customer-agreement)합니다.

세금 문서를 다운로드 하는 방법에 알아보려면 참조 [Microsoft 고객 계약에 대 한 세금 문서를 보고](billing-mca-download-tax-document.md#view-and-download-tax-documents)합니다.

### <a name="add-an-additional-enterprise-administrator"></a>추가 엔터프라이즈 관리자를 추가 합니다.

보기 수 있도록 청구 프로필에 대 한 사용자 액세스를 제공 하 고 조직에 대 한 청구를 관리 합니다. 사용할 수는 **액세스 제어 (IAM)** 액세스 권한을 부여 하려면 Azure 포털의 페이지입니다.  청구 프로필 역할에 대 한 자세한 내용은 참조 하세요 [청구 프로필 역할 및 작업](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)합니다.

청구 프로필에 대 한 액세스를 제공 하는 방법에 알아보려면 참조 [Azure portal에서 청구 역할 관리](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)합니다.

### <a name="create-a-new-department"></a>새 학과 만들려면

기업 계약 등록의 부서와 마찬가지로 요구 사항에 따라 비용을 구성 하는 송장 섹션을 만듭니다. Azure portal에서 새 청구서 섹션을 만들 수 있습니다. 자세한 내용은 참조 하세요 [비용을 구성 하려면 청구서에 섹션을 만들](billing-mca-section-invoice.md)합니다.

### <a name="create-a-new-account"></a>새 계정 만들기

사용자가 기업 계약 등록에서 만든 계정은 같은 Azure 구독을 만들 수 있는 권한을 부여할 송장 섹션에서 Azure 구독 작성자 역할을 할당 합니다. 자세한 내용은 [다른 사용자가 Azure 구독을 만들 수 있는 권한이 있도록](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions)입니다.

## <a name="changes-for-department-administrators-in-the-new-billing-account"></a>부서 관리자는 새 청구 계정에 대 한 변경 내용

다음과 같이 변경 부서 관리자가 Microsoft 고객 계약을 갱신 하는 기업 계약이 적용 됩니다.

- 송장 섹션을 각 부서 기업 계약 등록에 대해 생성 됩니다. 송장 섹션이 부서를 관리 하려면 사용 합니다. 송장 섹션에 대 한 자세한 내용은 참조 하세요 [청구서 구역 이해](billing-mca-overview.md#understand-invoice-sections)합니다.
- 다른 사용자가 기업 계약 등록에서 만든 계정은 같은 Azure 구독을 만들 수 있는 권한이 있도록 송장 섹션에서 Azure 구독 작성자 역할을 사용 합니다.
- Azure EA 포털 대신 조직에 대 한 청구를 관리 하려면 Azure portal을 사용 합니다.

다음 역할은 새 청구 계정에 제공 됩니다.

**송장 섹션 소유자** -부서 기업 계약에 대해 생성 된 송장 섹션에서 송장 섹션 소유자 역할이 할당 됩니다. 역할 및 보기 및 추적 비용 제어 송장 섹션에 대 한 다른 제품을 구입 하 고 Azure 구독을 만들 수 있는 수 있습니다.

### <a name="view-charges-for-your-department"></a>부서에 대 한 요금 보기

Azure 포털의 부서를 만들어지는 송장 섹션에 대 한 요금을 볼 수 있습니다 [Cost Management + 청구 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)합니다.

### <a name="add-an-additional-department-administrator"></a>추가 부서 관리자 추가

송장 섹션을 기업 계약에서 제공 되었던 각 부서에 대해 생성 됩니다. 사용할 수는 **액세스 제어 (iam)** 다른 보기 및 송장 섹션 관리에 대 한 액세스를 부여 하려면 Azure 포털의 페이지입니다. 송장 섹션 역할에 대 한 자세한 내용은 참조 하세요 [송장 섹션 역할 및 태스크](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)합니다.

송장 섹션에 대 한 액세스를 제공 하는 방법에 알아보려면 참조 [Azure portal에서 청구 역할 관리](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)합니다.

### <a name="create-a-new-account-in-your-department"></a>부서에서 새 계정 만들기

사용자 부서에서 사용자에 대해 생성 된 송장 섹션에서 Azure 구독 작성자 역할을 할당 합니다. 자세한 내용은 [다른 사용자가 Azure 구독을 만들 수 있는 권한이 있도록](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions)입니다.

### <a name="view-charges-for-accounts-in-your-departments"></a>학부에서 계정에 대 한 요금 보기

기업 계약 등록에서 만들어진 계정은 새 청구 계정에 지원 되지 않습니다. 계정의 구독 부서에 대 한 해당 청구서 섹션에 속합니다. 계정 소유자를 만들고 해당 청구서 섹션에 대 한 구독을 관리할 수 있습니다.

부서에 속한 계정에 속한 구독에 대 한 집계 비용을 보려면 각 구독에 대 한 비용 센터를 설정 해야 합니다. 비용 센터 별로 등록을 필터링 하 여 Azure 사용량 및 요금 파일을 사용할 수 있습니다.

## <a name="changes-for-account-owners-in-the-new-billing-account"></a>새 청구 계정의 계정 소유자에 대 한 변경 내용

기업 계약에서 계정 소유자는 새 청구 계정에 Azure 구독을 만들 수 있는 권한을 가져옵니다. 기존 Azure 구독의 부서에서 사용자에 대해 생성 된 송장 섹션에 속합니다. 계정의 부서에 속하지 않습니다, 구독 기본 송장 섹션 이라는 송장 섹션을에 속해야 합니다.

추가 Azure 구독을 만들려면 다음과 같은 역할을 새 청구 계정에 제공 됩니다.

**Azure 구독 작성자** -을 기업 계약에서 부서 만들어지는 송장 섹션에서 azure 구독 작성자 역할을 할당 됩니다. 계정의 부서에 속하지 않습니다, 경우 기본 송장 섹션 라는 섹션에서 Azure 구독 작성자 역할을 가져옵니다. 역할을 통해 청구서 섹션에 대 한 Azure 구독을 만들 수 있습니다.

### <a name="create-an-azure-subscription"></a>Azure 구독을 만듭니다.

Azure portal에서 청구서 섹션에 대 한 Azure 구독을 만들 수 있습니다. 자세한 내용은 참조 하세요. [Microsoft 고객 계약에 대 한 추가 Azure 구독 만들기](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>계정에 대 한 요금 보기

계정에 속한 구독에 대 한 요금을 보려면로 이동 합니다 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) Azure portal에서 합니다. 구독 페이지에는 모든 구독에 대 한 요금이 표시 됩니다.

### <a name="view-charges-for-a-subscription"></a>구독에 대 한 요금 보기

하거나 구독에 대 한 요금을 볼 수 있습니다에 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 또는 Azure 비용 분석 합니다. Azure 비용 분석에 대 한 자세한 내용은 참조 하세요. [탐색 및 분석 비용 분석을 사용 하 여 비용](../cost-management/quick-acm-cost-analysis.md)합니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft 고객 계약에 대 한 대금 청구 계정 이해](billing-mca-overview.md)
- [청구서 이해](billing-understand-your-bill.md)
- [요금 청구 방식](billing-understand-your-invoice.md)
- [다른 사용자의 Azure 구독의 소유권 청구](billing-mca-request-billing-ownership.md)
