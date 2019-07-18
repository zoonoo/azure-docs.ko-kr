---
title: Microsoft 고객 계약 대금 청구 계정-Azure 사용 하 여 시작
description: 대금 청구 계정에 Microsoft 고객 계약을 이해
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 87483c967641489e9548f38c99eebbf121d0d252
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490744"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Microsoft 고객 계약 대금 청구 계정 시작

대금 청구 계정에 Azure를 사용 하도록 등록할 때 만들어집니다. 대금 청구 계정 청구서를 결제를 관리 하는 데 하 고 비용을 추적 합니다. 여러 청구 계정에 대 한 액세스를 할 수 있습니다. 예를 들어 수 등록 한 Azure에 대 한 개인 프로젝트에 대 한 합니다. 조직의 기업 계약 또는 Microsoft 고객 계약을 통해 Azure에 액세스할을 수도 수 있습니다. 이러한 시나리오 각각에 대 한 별도 청구 계정을 사용 해야 합니다.

이 문서는 Microsoft 고객 계약에 대 한 대금 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인](#check-access-to-a-microsoft-customer-agreement)합니다.

## <a name="your-billing-account"></a>대금 청구 계정

Microsoft 고객 계약에 대 한 대금 청구 계정 송장 및 지불 방법을 관리할 수 있도록 하나 이상의 청구 프로필을 포함 합니다. 각 청구 프로필에는 비용 청구 프로필의 청구서를 구성할 수 있는 하나 이상의 송장 섹션이 포함 되어 있습니다.

다음 다이어그램은 대금 청구 계정, 청구 프로필 및 송장 섹션 간의 관계를 보여줍니다.

![계층 청구는 Microsoft 고객 계약을 보여 주는 다이어그램](./media/billing-mca-overview/mca-billing-hierarchy.png)

대금 청구 계정에서 역할에는 가장 높은 수준의 권한 갖습니다. 기본적으로 Azure에 액세스할 수 있게 대금 청구 계정에 등록 한 사용자만 합니다. 송장 보기 및 재무 또는 IT 관리자와 같은 전체 조직에 대해 비용을 추적 해야 하는 사용자에 게 이러한 역할을 할당 되어야 합니다. 자세한 내용은 [청구 계정 역할 및 작업](billing-understand-mca-roles.md#billing-account-roles-and-tasks)합니다.

## <a name="billing-profiles"></a>청구 프로필

청구 프로필을 사용 하 여 송장 및 지불 방법 관리 합니다. 월별 청구서는 계정의 각 청구 프로필에 대 한 월의 시작 부분에 생성 됩니다. 청구서는 모든 Azure 구독 및 이전 달의 다른 구매에 대 한 해당 요금을 포함 합니다.

대금 청구 계정에 대 한 청구 프로필을 자동으로 만들어집니다. 기본적으로 하나의 송장 영역을 포함 합니다. 쉽게 추적 하 고 필요에 따라 비용을 구성 하려면 추가 섹션을 만들 수 있습니다 프로젝트, 부서 또는 개발 환경 별로 인지 합니다. 이 섹션에서는 각 구독 및 구매를 할당 한 사용을 반영 하는 청구 프로필의 청구서에 표시 됩니다.

청구 프로필에 역할에는 보기 및 송장 및 지불 방법 관리 권한을 갖습니다. 조직에서 계정 팀의 멤버와 같은 청구서 지불 하는 사용자에 게 이러한 역할을 할당 합니다. 자세한 내용은 [청구 프로필 역할 및 작업](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)합니다.

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>각 청구 프로필 월별 청구서를 가져옵니다.

월별 청구서는 청구 프로필 각각에 대해 월의 시작 부분에 생성 됩니다. 청구서는 지난 달의 모든 요금을 포함합니다.

청구서를 확인 하 고, 문서를 다운로드 하 고, 전자 메일, Azure portal에서 향후 청구서를 가져오려면 설정을 변경할 수 있습니다. 자세한 내용은 [Microsoft 고객 계약에 대 한 송장을 다운로드](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)합니다.

### <a name="invoice-payment-methods"></a>청구서 결제 방법

각 청구 프로필에 해당 청구서를 지불 하는 데 사용 되는 자체 지불 방법이 있습니다. 다음 지불 방법이 지원 됩니다.

| Type             | 정의  |
|------------------|-------------|
|Azure 크레딧    |  크레딧은은 청구서 요금을 지불 해야 하는 양을 줄이는의 적격 요금에 자동으로 적용 됩니다. 자세한 내용은 [청구 프로필에 대 한 Azure 크레딧 잔액을 추적할](billing-mca-check-azure-credits-balance.md)합니다. |
|확인/통신 전송 | 계정 확인/통신 전송을 통해 지불 승인 되 면 합니다. 용량 요금을 지불할 수 있습니다 확인/통신 전송을 통해 청구서에 도달 합니다. 청구서의 결제에 대 한 지침 제공 됩니다. |
|신용 카드 | Azure 웹 사이트를 통해 Azure에 등록 하는 고객은 신용 카드를 통해 지불할 수 있습니다. |

### <a name="apply-policies-to-control-purchases"></a>구매를 제어 하는 정책을 적용 합니다.

컨트롤과 Azure Marketplace 및 청구 프로필을 사용 하 여 예약 구매에 정책을 적용 합니다. Azure 예약 및 Marketplace 제품의 구매를 사용 하지 않도록 설정 하는 정책을 설정할 수 있습니다. 정책이 적용 되 면 이러한 구매를 청구 프로필에 청구 되는 구독을 사용할 수 없습니다.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Azure 계획 구독에 대 한 가격 책정 및 서비스 수준 계약 확인

Azure 계획 가격 책정을 확인 및 서비스 수준 계약 Azure 구독에 대 한 합니다. 이러한 값은 청구 프로필을 만들 때 자동으로 사용 됩니다. 청구 프로필을 사용 하 여 연결 된 모든 송장 섹션에서는 이러한 계획을 사용할 수 있습니다. 송장 섹션에 대 한 액세스를 사용 하 여 사용자가 계획을 사용 하 여 Azure 구독을 만듭니다. 다음과 같은 Azure 플랜은 Microsoft 고객 계약에 대 한 계정 청구에서 지원 됩니다.

| 계획             | 정의  |
|------------------|-------------|
|Microsoft Azure Plan   | 사용자는 모든 워크 로드를 실행할 수 있는 구독을 만들 수 있습니다. 자세한 내용은 참조 하세요. [Microsoft Azure 계획](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Microsoft Azure 개발/테스트 계획 | Visual Studio 구독자가 개발에 대 한 제한 된 구독을 만들 수 있도록 하거나 워크 로드를 테스트 합니다. 이러한 구독은 Azure portal에서 더 낮은 요금 및 전용 가상 머신 이미지에 대 한 액세스와 같은 혜택을 가져옵니다. 자세한 내용은 참조 하세요. [Microsoft Azure DevTest에 대 한 계획](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="invoice-sections"></a>송장 섹션

청구서의 비용을 구성 하려면 청구서 섹션을 만듭니다. 예를 들어 비용 부서나 팀 프로젝트를 구성 하려고 하지만 조직에 대 한 단일 청구서를 해야 할 수 있습니다. 이 시나리오에서는 각 부서, 팀 또는 프로젝트에 대 한 청구서 섹션을 만든 단일 청구 프로필을 해야 합니다.

송장 섹션을 만들어지면 권한을 제공할 수 있습니다 다른 섹션으로 청구 되는 Azure 구독을 만들 합니다. 모든 사용 요금 및 구독에 대 한 구매 다음 섹션에 청구 됩니다.

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
