---
title: Microsoft 고객 계약-Azure에 대 한 대금 청구 계정 설정
description: Microsoft 고객 계약에 대 한 대금 청구 계정 설정 방법에 알아봅니다.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 947bfe85d94a5d11eeb54bd6b24c4c515af024d4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490688"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 대금 청구 계정 설정

경우에 Enterprise Agreement 등록 만료 되었거나 곧 만료 등록을 갱신 하는 Microsoft 고객 계약에 서명할 수 있습니다. 이 문서는 설치 후 기존 청구 금액에 변경 내용을 설명 하 고 새 청구 계정 설정 안내. 갱신에는 다음 단계가 포함 됩니다.

1. 새 Microsoft 고객에 동의 합니다. Microsoft 필드 담당자 세부 정보를 이해 하 고 새 계약에 동의 하에 작동 합니다.
2. 새 Microsoft 고객 계약에 대해 만든 새 청구 계정 설정 합니다.

청구 계정을 설정 하는 새 계정에 기업 계약 등록에서 Azure 구독의 청구를 전환할 해야 있습니다. 설치를 구독에서 실행 되는 Azure 서비스에 영향을 주지 않습니다. 그러나 구독에 대 한 청구를 관리 하려는 방식으로 변경 됩니다.

- 대신 합니다 [EA 포털](https://ea.azure.com)를 관리 하려는 Azure 서비스 및 청구에는 [Azure portal](https://portal.azure.com)합니다.
- 사용자 요금에 대 한 디지털 월별 청구서를 볼 수 있습니다. 확인 하 고 Azure Cost Management + 청구 페이지 청구서를 분석할 수 있습니다.
- 부서 및 기업 계약 등록에 대 한 계정 대신 관리 하 고 결제 구성 청구 구조와 새 계정에서 범위를 사용 합니다.

설치를 시작 하기 전에 다음을 수행 하는 것이 좋습니다.

- **새 청구 계정 이해**
  - 새 계정의 조직에 대 한 청구를 간소화합니다. [새 청구 계정 개요](billing-mca-overview.md)
- **설치 완료에 대 한 액세스를 확인 합니다.**
  - 특정 관리 권한이 있는 사용자만의 설치를 완료할 수 있습니다. 있는지 확인 합니다 [설치를 완료 하는 데 필요한 액세스](#access-required-to-complete-the-setup)합니다.
- **청구 계층으로 변경 내용을 이해합니다**
  - 새 계정 대금 청구 하는 기업 계약 등록 보다 다르게 구성 됩니다. [새 계정에 청구 계층으로 변경 내용을 이해](#understand-changes-to-your-billing-hierarchy)합니다.
- **대금 청구 관리자의 액세스 변경 내용을 이해합니다**
  - 기업 계약 등록의 관리자가 새 계정에 청구 범위에 대 한 액세스를 가져옵니다. [에 대 한 액세스 변경 내용을 이해](#changes-to-billing-administrator-access)합니다.
- **새 계정으로 대체 되는 뷰 기업 계약 기능**
  - 새 계정에는 기능으로 대체 되는 Enterprise Agreement 등록 중 보기 기능입니다.
- **가장 일반적인 질문에 답변 보기**
  - 뷰 [자세한 내용은](#additional-information) 설치에 자세히 알아보려면 합니다.

## <a name="access-required-to-complete-the-setup"></a>설치를 완료 하는 데 필요한 액세스

설치를 완료 하려면 다음 액세스를 해야 합니다.

- Microsoft 고객 계약에 서명한 경우 생성 된 청구 프로필의 소유자입니다. 청구 프로필에 대 한 자세한 내용은 참조 하세요 [청구 프로필을 이해](billing-mca-overview.md#billing-profiles)합니다.

- 엔터프라이즈 관리자가 갱신 되는 등록 합니다.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>사용자 등록에서 엔터프라이즈 관리자가 아닌 경우

등록의 대금 청구 계정 설정을 완료 하려면 엔터프라이즈 관리자를 요청할 수 있습니다.

1. Microsoft 고객 계약을 서명 하는 경우 사용자에 게 전송 된 전자 메일의 링크를 사용 하 여 Azure portal에 로그인 합니다.

2. 조직 내에서 다른 사람이 서명 계약 또는 전자 메일이 없는 경우 다음 링크를 사용 하 여 로그인 합니다. 바꿉니다 **enrollmentNumber** 갱신 된 기업 계약 등록 번호를 사용 하 여 합니다.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. 요청을 전송 하려는 엔터프라이즈 관리자를 선택 합니다.

   ![보여 주는 스크린샷 엔터프라이즈 관리자 초대](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. 선택 **보내기 요청**합니다.

   관리자는 설치를 완료 하는 지침이 포함 된 이메일을 받게 됩니다.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>청구 프로필의 소유자로 알지 못하는 경우

사용자 조직에 청구 프로필에 소유자로 추가 되는 Microsoft 고객 계약을 서명 합니다. 사용자는 설치를 완료할 수 있도록 소유자로 추가 하도록 요청 합니다.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>청구 계층으로 변경 내용을 이해합니다

새 청구 계정 청구 강화 및 비용 관리 기능을 제공 하는 동안 조직에 대 한 청구를 간소화 합니다. 다음 다이어그램은 새 청구 계정에 청구 구성 되는 방식을 설명 합니다.

![Ea-mca-post-전환-계층의 이미지](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. 대금 청구 계정을 사용 하 여 Microsoft 고객 계약에 대 한 청구를 관리 합니다. 대금 청구 계정에 대 한 자세한 내용은 참조 하세요 [대금 청구 계정 이해](billing-mca-overview.md#your-billing-account)합니다.
2. 청구 프로필을 사용 하 여 기업 계약 등록 하 여 조직에 대 한 청구를 관리 합니다. 엔터프라이즈 관리자를 사용 하면 청구 프로필의 소유자로 합니다. 청구 프로필에 대 한 자세한 내용은 참조 하세요 [청구 프로필을 이해](billing-mca-overview.md#billing-profiles)합니다.
3. 요구 사항에 따라, 기업 계약 등록 학부에 유사한 비용을 구성 하는 송장 섹션을 사용 합니다. 부서 송장 섹션 되며 각 청구서 섹션의 소유자로 부서 관리자. 송장 섹션에 대 한 자세한 내용은 참조 하세요 [청구서 구역 이해](billing-mca-overview.md#invoice-sections)합니다.
4. 기업 계약에서 생성 된 계정은 새 청구 계정에 지원 되지 않습니다. 계정의 구독 부서에 대 한 해당 청구서 섹션에 속합니다. 계정 소유자를 만들고 해당 청구서 섹션에 대 한 구독을 관리할 수 있습니다.

## <a name="changes-to-billing-administrator-access"></a>관리자 액세스 대금 청구 변경 내용

해당 액세스에 따라 기업 계약 등록에 대 한 대금 청구 관리자는 새 계정에 청구 범위에 대 한 액세스를 가져옵니다. 다음 표에서 설치 하는 동안 액세스 변경:

| 기존 역할 | Post 전환 역할 |
| --- | --- |
| **엔터프라이즈 관리자 (읽기 전용 = 아니요)** | **청구 프로필 소유자** </br> 청구 프로필에 있는 모든 항목 관리 </br> - **모든 송장 섹션에서 송장 섹션 소유자** </br> 송장 섹션에 있는 모든 항목 관리 |
| **엔터프라이즈 관리자 (읽기만 = Yes)** | **청구 프로필 독자** </br> 대금 청구 계정에 모든 항목의 읽기 전용 보기</br>**모든 송장 섹션에서 송장 섹션 판독기**</br> 송장 섹션에서 모든 항목의 읽기 전용 보기|
| **부서 관리자 (읽기 전용 = 아니요)** |**각 부서에 대해 만든 송장 섹션에서 송장 섹션 소유자** </br>송장 섹션에 있는 모든 항목 관리|
| **부서 관리자 (읽기만 = Yes)**|**각 부서에 대해 만든 송장 섹션에서 송장 섹션 판독기**</br> 송장 섹션에서 모든 항목의 읽기 전용 보기|
| **계정 소유자** | **-각 부서에 대해 만든 송장 섹션 azure 구독 작성자** </br>  해당 청구서 섹션에 대 한 Azure 구독을 만들려면|

Azure Active Directory 테 넌 트 Microsoft 고객 계약을 서명 하는 동안 새 청구 계정에 대 한 선택 됩니다. 테 넌 트 조직에 대 한 존재 하지 않는 경우 새 테 넌 트 생성 됩니다. 테 넌 트 Azure Active Directory 내의 조직을 나타냅니다. 조직에서 데이터 및 응용 프로그램의 액세스를 관리 하는 테 넌 트를 사용 하는 조직에서 전역 테 넌 트 관리자입니다.

새 계정에만 Microsoft 고객 계약을 서명 하는 동안 선택 된 테 넌 트에서 사용자를 지원 합니다. 기업 계약에 대 한 관리 권한이 있는 사용자가 테 넌 트의 일부 경우 설치 하는 동안 새 청구 계정에 액세스 하 게 합니다. 있지 않은 테 넌 트의 일부에 초대 하지 않는 한 새 청구 계정에 액세스할 수 없습니다.

사용자를 초대 하는 경우 게스트 사용자로 테 넌 트에 추가 됩니다 않으며 대금 청구 계정에 대 한 액세스를 가져옵니다. 사용자를 초대 하려면 테 넌 트에 대 한 게스트 액세스 설정 해야 합니다. 자세한 내용은 [Azure Active Directory에서 게스트 액세스를 제어](https://docs.microsoft.com/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory)입니다. 게스트 액세스를 해제 하는 경우를 켜라는 테 넌 트의 전역 관리자에 게 문의 합니다. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>대체 뷰 기능

다음 기업 계약의 기능 Microsoft 고객 계약에 대 한 대금 청구 계정에 새 기능으로 바뀝니다.

### <a name="enterprise-agreement-accounts"></a>기업 계약 계정

기업 계약 등록에서 만들어진 계정은 새 청구 계정에 지원 되지 않습니다. 계정의 구독은 해당 학부는 청구서 섹션에 속합니다. 계정 소유자 될 Azure 구독 작성자 수 만들고 해당 청구서 섹션에 대 한 구독을 관리 합니다.

### <a name="notification-contacts"></a>알림 연락처

알림 연락처는 Azure 기업 계약에 대 한 전자 메일 통신 전송 됩니다. 새 청구 계정에는 지원 되지 않습니다. 대금 청구 계정에 청구 프로필에 대 한 액세스 권한이 있는 사용자를 Azure 크레딧 및 송장에 대 한 전자 메일 전송 됩니다.

### <a name="spending-quotas"></a>지출 할당액

기업 계약 등록의 부서에 대해 설정 된 지출 할당량 새 청구 계정에 대 한 예산으로 바뀝니다. 예산 등록에서 부서에 설정 된 각 지출 할당량에 대해 만들어집니다. 예산에 대 한 자세한 내용은 참조 하세요. [만들기 및 관리 Azure 예산](../cost-management/manage-budgets.md)합니다.

### <a name="cost-centers"></a>비용된 센터

기업 계약 등록에 Azure 구독에 설정 된 비용된 센터 새 청구 계정에 전달 됩니다. 그러나 부서와 기업 계약 계정에 대 한 비용 센터 지원 되지 않습니다.

## <a name="additional-information"></a>추가 정보

다음 섹션에서는 대금 청구 계정 설정에 대 한 추가 정보를 제공 합니다.

### <a name="no-service-downtime"></a>서비스 가동 중지 시간이 없습니다.

구독에 포함된 Azure 서비스는 계속해서 중단 없이 실행됩니다. Azure 구독의 청구 관계만 전환됩니다. 기존 리소스, 리소스 그룹 또는 관리 그룹에는 영향이 없습니다.

### <a name="user-access-to-azure-resources"></a>Azure 리소스에 대 한 사용자 액세스

Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 설정 된 Azure 리소스에 대 한 액세스를 전환 하는 동안 적용 되지 않습니다.

### <a name="azure-reservations"></a>Azure Reservations

기업 계약 등록의 모든 Azure 예약 새 청구 계정으로 이동 합니다. 구독에 적용되는 예약 할인은 전환 과정에서 변경되지 않습니다.

### <a name="azure-marketplace-products"></a>Azure Marketplace 제품

기업 계약 등록의 Azure Marketplace 제품 구독 함께 이동 됩니다. 전환 하는 동안 Marketplace 제품의 서비스 액세스 변경 내용이 없습니다.

### <a name="support-plan"></a>지원 계획

지원 혜택 전환의 일부로 전송 하지 않습니다. 새 청구 계정에 Azure 구독을 활용 하려면 새 지원 플랜을 구입 합니다.

### <a name="past-charges-and-balance"></a>과거 청구 금액 및 분산

Azure portal 통해 기업 계약 등록에서 전환 하기 전에 요금과 크레딧 잔액을 볼 수 있습니다. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>설치를 완료 하면?

기업 계약 등록 만료 되기 전에 대금 청구 계정 설정을 완료 합니다. 등록이 만료 되 면 Azure 구독에서 서비스는 여전히 유지에 실행 중단 없이 합니다. 그러나 서비스에 대 한 일반 소매 요금이 부과 됩니다.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>설치 후 기업 계약 등록에 대 한 변경 내용

새 청구 계정에 수동으로 이동할 수 전환 후 Enterprise Agreement 등록에 대해 생성 된 azure 구독입니다. 자세한 내용은 [다른 사용자의 Azure 구독의 소유권 청구](billing-mca-request-billing-ownership.md)합니다. 전환 후 구매 하는 Azure 예약을 이동할 [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)합니다. 또한 사용자 제공할 수 있습니다 대금 청구 계정에 대 한 액세스 전환 후 합니다. 자세한 내용은 참조 하세요. [Azure portal에서 청구 역할 관리](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>전환 되돌리기

전환 되돌릴 수 없습니다. Azure 구독의 청구는 새 청구 계정으로 전환 되 면 기업 계약 등록으로 되돌릴 수 없습니다.

### <a name="closing-your-browser-during-setup"></a>설치 하는 동안 브라우저를 닫지

클릭 하기 전에 **전환 시작**, 브라우저를 닫을 수 있습니다. 전자 메일에서 가져온 링크를 사용 하 여 설치를 다시 방문 하 고 전환을 시작할 수 있습니다. 전환을 시작 된 후 브라우저를 닫으면 전환을 계속에 실행 됩니다. 전환의 최신 상태를 모니터링 하려면 전환 상태 페이지로 돌아올. 전환이 완료 되 면 전자 메일을 받습니다.

## <a name="complete-the-setup-in-the-azure-portal"></a>Azure portal에서 설정을 완료합니다

설치를 완료 하려면 새 청구 계정 및 Enterprise Agreement 등록 모두에 대 한 액세스를 해야 합니다. 자세한 내용은 [대금 청구 계정에는 설정 완료 하는 데 필요한 액세스](#access-required-to-complete-the-setup)합니다.

1. Microsoft 고객 계약을 서명 하는 경우 사용자에 게 전송 된 전자 메일의 링크를 사용 하 여 Azure portal에 로그인 합니다.

2. 조직 내에서 다른 사람이 서명 계약 또는 전자 메일이 없는 경우 다음 링크를 사용 하 여 로그인 합니다. 바꿉니다 **enrollmentNumber** 갱신 된 기업 계약 등록 번호를 사용 하 여 합니다.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. 선택 **전환 시작** 설치의 마지막 단계에서 합니다. 한 번 시작 변환을 선택합니다.

    ![설치 마법사를 보여 주는 스크린샷](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - 기업 계약 계층 구조에 해당 청구 계층을 새 청구 계정에 만들어집니다. 자세한 내용은 [청구 계층으로 변경 내용을 이해](#understand-changes-to-your-billing-hierarchy)합니다.
    - 기업 계약 등록에서 관리자는 계속 조직에 대 한 결제를 관리할 수 있도록 새 청구 계정에 대 한 액세스를 제공 됩니다.
    - Azure 구독의 청구는 새 계정으로 전환 됩니다. **없습니다 영향은 Azure 서비스에서이 전환 중입니다. 중단 시간 없이 실행 유지 됩니다**합니다.
    - Azure 예약에 있는 경우 동일한 할인 및 용어를 사용 하 여 새 청구 계정으로 이동 합니다. 예약 할인을 전환 하는 동안 적용할 계속 됩니다.

4. 전환 상태를 모니터링할 수 있습니다 합니다 **상태 전환** 페이지입니다.

   ![전환 상태를 보여 주는 스크린샷](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-set-up"></a>대금 청구 계정 설정의 유효성을 검사합니다

 새 대금 청구 계정을 제대로 설정 되었는지 확인 하려면 다음을 확인 합니다.

### <a name="azure-subscriptions"></a>Azure 구독

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 검색할 **비용 관리 + 청구**합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. 청구 프로필을 선택 합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 선택 **청구 프로필** 및 청구 프로필 합니다.

4. 선택 **Azure 구독** 왼쪽에서 합니다.

   ![구독 목록을 보여 주는 스크린샷](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

새 청구 계정에 기업 계약 등록에서 전환 되는 azure 구독에 Azure 구독 페이지에 표시 됩니다. 구독이 없는 경우 Azure portal에서 수동으로 구독의 청구를 전환 합니다. 자세한 내용은 참조 하세요. [가져오기 청구 소유권을 다른 사용자의 Azure 구독](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Azure Reservations

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 검색할 **비용 관리 + 청구**합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. 송장 섹션을 선택 합니다. 액세스 권한에 따라 청구 계정 또는 청구 프로필을 선택해야 할 수도 있습니다.  대금 청구 계정 또는 청구 프로필에서 선택 **섹션에서는 송장** 와 다음 송장 부분을 지정 합니다.

    ![송장 섹션 post 전환 목록을 보여 주는 스크린샷](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. 선택 **All products** 왼쪽에서 합니다.

5. 검색할 **예약 된**합니다.

    ![구독 post 전환 목록을 보여 주는 스크린샷](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Azure 예약 새 청구 계정에 기업 계약 등록에서 이동 된 모든 제품 페이지에 표시 됩니다. 기업 계약 등록에서 이동할 수 있는 모든 Azure 예약을 확인 하려면 모든 송장 섹션에 대 한 단계를 반복 합니다. Azure 예약을 사용할 수 없는 경우 [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 예약 새 청구 계정으로 이동 합니다.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>청구 프로필에 대 한 엔터프라이즈 관리자의 액세스

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 검색할 **비용 관리 + 청구**합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. 등록에 대 한 만든 청구 프로필을 선택 합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다.  청구 계정에서 선택 **청구 프로필** 및 청구 프로필 합니다.

4. 선택 **액세스 제어 (IAM)** 왼쪽에서 합니다.

   ![엔터프라이즈 관리자 post 전환에 대 한 액세스를 보여 주는 스크린샷](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

엔터프라이즈 관리자는 청구 프로필 독자로 엔터프라이즈 읽기 전용 권한이 있는 관리자 나열 하는 동안 프로필 소유자 청구로 나열 됩니다. 엔터프라이즈 관리자에 대 한 액세스를 사용할 수 없는 경우 액세스 제공할 수 있습니다 하는 Azure 포털에서. 자세한 내용은 [Azure portal에서 청구 역할 관리](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)합니다.

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>엔터프라이즈 관리자, 부서 관리자 및 송장 섹션에서 계정 소유자의 액세스

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 검색할 **비용 관리 + 청구**합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. 송장 섹션을 선택 합니다. 송장 섹션에서는 각 부서 이름이 같은 기업 계약 등록에 있습니다. 사용자 액세스에 따라 청구 프로필 또는 대금 청구 계정을 선택 해야 합니다. 청구 프로필 또는 대금 청구 계정에서 선택 **섹션에서는 송장** 를 선택한 다음 송장 섹션을 합니다.

   ![송장 섹션 post 전환 목록을 보여 주는 스크린샷](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. 선택 **액세스 제어 (IAM)** 왼쪽에서 합니다.

    ![부서 및 관리자 액세스 post 전환 하는 계정에 대 한 액세스를 보여 주는 스크린샷](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

엔터프라이즈 관리자 및 부서 관리자 계정 소유자는 부서에서 Azure 구독 작성자도 표시 되는 동안 송장 섹션 소유자 또는 송장 섹션 판독기로 나열 됩니다. 기업 계약 등록의 모든 부서에 대 한 액세스를 확인 하려면 모든 송장 섹션의 단계를 반복 합니다. 모든 부서에 속하지 않았던 계정 소유자는 청구서 섹션에서 권한을 얻을 **기본 송장 섹션**합니다. 모든 관리자에 대 한 액세스를 사용할 수 없는 경우 액세스 제공할 수 있습니다 하는 Azure 포털에서. 자세한 내용은 [Azure portal에서 청구 역할 관리](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)합니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- [새 청구 계정 시작](billing-mca-overview.md)

- [Microsoft 고객 계약에 대 한 대금 청구 계정에 기업 계약 작업을 완료 합니다.](billing-mca-enterprise-operations.md)

- [대금 청구 계정에 대 한 액세스 관리](billing-understand-mca-roles.md)
