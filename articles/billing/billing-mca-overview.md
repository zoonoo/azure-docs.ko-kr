---
title: 대금 청구 계정에 대 한 시작 Microsoft 고객 계약-Azure | Microsoft Docs
description: Microsoft 고객 계약에 대 한 대금 청구 계정 이해
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: ea625a61ed600dbaa22fef85987e9570a6fb7dbc
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337416"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 대금 청구 계정 시작

대금 청구 계정 로그인 microsoft Azure를 사용 하 여 각 계약에 대해 만들어집니다. 대금 청구 계정을 사용 하 여 청구를 관리 하 고 비용을 추적 합니다. 여러 청구 계정에 대 한 액세스를 할 수 있습니다. 예를 들어 수 등록 한 Azure에 대 한 개인 프로젝트에 대 한 합니다. 조직의 기업 계약 또는 Microsoft 고객 계약을 통해 Azure에 액세스할을 수도 수 있습니다. 이러한 시나리오 각각에 대 한 별도 청구 계정을 사용 해야 합니다.

이 문서는 Microsoft 고객 계약에 대 한 대금 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인](#check-access-to-a-microsoft-customer-agreement)합니다.

## <a name="understand-billing-account"></a>대금 청구 계정 이해

Microsoft 고객 계약에 대 한 대금 청구 계정 송장 및 지불 방법을 관리할 수 있도록 하나 이상의 청구 프로필을 포함 합니다. 각 청구 프로필에는 비용 청구 프로필의 청구서를 구성할 수 있는 하나 이상의 송장 섹션이 포함 되어 있습니다.

다음 다이어그램은 대금 청구 계정, 청구 프로필 및 송장 섹션 간의 관계를 보여 줍니다.

![Microsoft 고객 계약에 대 한 청구 계층을 보여 주는 다이어그램](./media/billing-mca-overview/mca-billing-hierarchy.png)

대금 청구 계정에서 역할에는 가장 높은 수준의 권한 갖습니다. 기본적으로 조직의 Azure Active directory 전역 관리자만 청구 계정에 대 한 액세스를 가져옵니다. 송장 보기 및 재무 또는 IT 관리자와 같은 전체 조직에 대해 비용을 추적 해야 하는 사용자에 게 이러한 역할을 할당 되어야 합니다. 자세한 내용은 [청구 계정 역할 및 작업](billing-understand-mca-roles.md#billing-account-roles-and-tasks)합니다.

## <a name="understand-billing-profiles"></a>청구 프로필을 이해

청구 프로필을 사용 하 여 송장 및 지불 방법 관리 합니다. 월별 청구서는 Azure 구독 및 청구 프로필을 사용 하 여 구매 하는 다른 제품에 대해 생성 됩니다. 결제 방법을 사용 하 여 청구서 금액을 지불 합니다.

대금 청구 계정에 대 한 청구 프로필을 자동으로 만들어집니다. 추가 청구서를 설정 하는 새 청구 프로필을 만들 수 있습니다. 예를 들어 좋습니다 다른 송장 각 부서 또는 프로젝트에 대 한 조직에서.

또한 송장 비용 청구 프로필의 청구서를 구성 하는 섹션을 만들 수 있습니다. Azure 구독 및 송장 섹션에 구입한 제품에 대 한 요금 섹션에 표시 됩니다. 청구서의 청구 프로필의 모든 송장 섹션에 대 한 요금이 포함 됩니다.

청구 프로필에 역할에는 보기 및 송장 및 지불 방법 관리 권한을 갖습니다. 조직에서 계정 팀의 멤버와 같은 청구서 지불 하는 사용자에 게 이러한 역할을 할당 합니다. 자세한 내용은 [청구 프로필 역할 및 작업](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)합니다.

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>각 청구 프로필에 대해 생성 하는 월별 청구서

월별 청구서는 청구 각 프로필에 대 한 송장 날짜에 생성 됩니다. 청구서는 이전 달에 대 한 모든 요금을 포함합니다.

청구서를 확인 하 고, 문서를 다운로드 하 고, 전자 메일, Azure portal에서 향후 청구서를 가져오려면 설정을 변경할 수 있습니다. 자세한 내용은 [Microsoft 고객 계약에 대 한 송장을 다운로드](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)합니다.

### <a name="invoices-paid-through-payment-methods"></a>청구서 결제 메서드를 통해 지불

각 청구 프로필에 해당 청구서를 지불 하는 데 사용 되는 자체 지불 방법이 있습니다. 다음 지불 방법이 지원 됩니다.

| Type             | 정의  |
|------------------|-------------|
|Azure 크레딧    |  크레딧은 비용을 지불 해야 하는 횟수를 계산 청구서에 총 청구 크기를 자동으로 적용 됩니다. 자세한 내용은 [청구 프로필에 대 한 Azure 크레딧 잔액을 추적할](billing-mca-check-azure-credits-balance.md)합니다. |
|수표 또는 전신 송금 | 금액을 지불 하 청구서에 대 한 확인 또는 유선을 통해 전송 합니다. 청구서의 결제에 대 한 지침 제공 됩니다. |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>정책을 적용 하 여 컨트롤 Azure Marketplace 및 예약 구매

청구 프로필을 사용 하 여 구매를 제어 하는 정책을 적용 합니다. Azure 예약 및 Marketplace 제품의 구매를 사용 하지 않도록 설정 하는 정책을 설정할 수 있습니다. 정책이 적용 되 면 Azure 예약 및 Marketplace 제품을 구매 하려면 청구 프로필의 송장 섹션에 대해 만든 구독을 사용할 수 없습니다.

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>Azure 계획을 사용 하 여 Azure 구독을 만들 할 수 있습니다.

Azure 계획은 청구 프로필을 만들 때 자동으로 사용 됩니다. 청구 프로필의 모든 송장 섹션에는 이러한 계획에 액세스 하세요. 송장 섹션에 대 한 액세스를 사용 하 여 사용자가 계획을 사용 하 여 Azure 구독을 만듭니다. 청구 프로필에 대 한 계획을 Azure를 사용 하지 않으면 Azure 구독을 만들 수 없습니다. 다음과 같은 Azure 플랜은 Microsoft 고객 계약에 대 한 계정 청구에서 지원 됩니다.

| 계획             | 정의  |
|------------------|-------------|
|Microsoft Azure 플랜   | 사용자는 모든 워크 로드를 실행할 수 있는 구독을 만들 수 있습니다. 자세한 내용은 참조 하세요. [Microsoft Azure 계획](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|개발/테스트용 Microsoft Azure 플랜 | Visual Studio 구독자가 개발에 대 한 제한 된 구독을 만들 수 있도록 하거나 워크 로드를 테스트 합니다. 이러한 구독은 Azure portal에서 더 낮은 요금 및 전용 가상 머신 이미지에 대 한 액세스와 같은 혜택을 가져옵니다. 자세한 내용은 참조 하세요. [Microsoft Azure DevTest에 대 한 계획](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="understand-invoice-sections"></a>섹션에서는 송장 이해

청구 프로필의 청구서에 비용을 구성 하려면 청구서 섹션을 만듭니다. 예를 들어 비용 부서나 팀 프로젝트를 구성 하려고 하지만 조직에 대 한 단일 청구서를 해야 할 수 있습니다. 이 시나리오에서는 각 부서, 팀 또는 프로젝트에 대 한 청구서 섹션을 만든 단일 청구 프로필을 해야 합니다.

송장 섹션을 만들어지면 권한을 제공할 수 있습니다 다른 섹션에 대 한 Azure 구독을 만들려면. 모든 사용 요금 및 구독에 대 한 구매 후 청구서의 해당 섹션에 반영 됩니다.

송장 섹션에는 역할을 사용 하면 Azure 구독을 만들고 사람을 제어할 수 권한이 합니다. 엔지니어링 잠재 고객 및 기술 설계자와 같은 조직에서 팀을 위한 Azure 환경을 설정 하는 사용자에 게 이러한 역할을 할당 합니다. 자세한 내용은 [섹션에서는 역할 및 태스크를 송장](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)합니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

대금 청구 계정에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [Azure의 Microsoft 고객 계약 관리자 역할 이해](billing-understand-mca-roles.md)
- [Microsoft 고객 계약에 대 한 추가 Azure 구독 만들기](billing-mca-create-subscription.md)
- [비용을 구성 하려면 청구서에 섹션을 만들려면](billing-mca-section-invoice.md)