---
title: Microsoft 고객 계약-Azure에 대 한 대금 청구 관리자 역할 이해
description: Azure에서 계정을 Microsoft 고객 계약에 대 한 청구에 대 한 청구 역할에 알아봅니다.
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 780870cc71e95507a52ba6a9338026f895a96ac1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370970"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Azure의 Microsoft 고객 계약 관리자 역할 이해

Microsoft 고객 계약에 대 한 대금 청구 계정을 관리 하려면 다음 섹션에서 설명 하는 역할을 사용 합니다. Azure 리소스에 대 한 액세스를 제어 하는 기본 제공 역할 외에도 이러한 역할은입니다. 자세한 내용은 [Azure 리소스에 대한 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

이 문서는 Microsoft 고객 계약에 대 한 대금 청구 계정에 적용 됩니다. Microsoft 고객 계약에 액세스할 수 있는지 확인 합니다.

## <a name="billing-role-definitions"></a>역할 정의 청구합니다.

다음 표에서 사용 하 여 관리 대금 청구 계정, 프로필, 청구 및 섹션 송장 청구 역할을 설명 합니다.

|역할|설명|
|---|---|
|청구 계정 소유자|대금 청구 계정에 대 한 모든 것을 관리합니다|
|청구 계정 참가자|대금 청구 계정에 대 한 권한을 제외한 모든 사항을 관리합니다|
|청구 계정 판독기|대금 청구 계정에 모든 항목의 읽기 전용 보기|
|청구 프로필 소유자|청구 프로필에 대 한 모든 것을 관리합니다|
|청구 프로필 참가자|청구 프로필에 대 한 권한을 제외한 모든 사항을 관리합니다|
|청구 프로필 판독기|청구 프로필에서 모든 항목의 읽기 전용 보기|
|청구서 관리자|보기 및 청구 프로필에 대 한 청구서를 지불|
|청구서 섹션 소유자|송장 섹션에 있는 모든 항목 관리|
|청구서 섹션 참가자|송장 섹션에 대 한 권한을 제외한 모든 사항을 관리합니다|
|청구서 섹션 리더|송장 섹션에서 모든 항목의 읽기 전용 보기|
|Azure 구독 작성자|Azure 구독을 만들려면|

## <a name="billing-account-roles-and-tasks"></a>청구 계정 역할 및 작업

대금 청구 계정을 조직에 대 한 청구를 관리할 수 있습니다. 계정을 사용 하 여 청구 비용, 모니터링 비용 및 청구서를 구성 하 고 조직에 대 한 청구 액세스를 제어 합니다. 자세한 내용은 [대금 청구 계정 이해](billing-mca-overview.md#understand-billing-account)합니다.

다음 표에 청구 계정의 컨텍스트에서 작업을 완료 해야 하는 어떤 역할을 보여 줍니다.

### <a name="manage-billing-account-permissions-and-properties"></a>청구 계정 권한 및 속성 관리

|Task|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기|
|---|---|---|---|
|대금 청구 계정에 대 한 기존 사용 권한을 보려면|✔|✔|✔|
|청구 계정 보기 및 관리에 대 한 권한을 다른 사용자에 게|✔|✘|✘|
|회사 이름, 주소 및 대금 청구 계정 속성 보기 등|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>대금 청구 계정에 대 한 청구 프로필 관리

|Task|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기|
|---|---|---|---|
|계정에서 모든 청구 프로필 보기|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>대금 청구 계정에 대 한 청구서를 관리 합니다.

|Task|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기|
|---|---|---|---|
|계정에서 모든 청구서 보기|✔|✔|✔|
|청구서, Azure 사용량 및 요금 파일, 가격표를 다운로드 하 고 계정에서 문서 세금|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>대금 청구 계정에 대 한 청구서 섹션 관리

|Task|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기|
|---|---|---|---|
|계정에서 모든 청구서 섹션 보기|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>대금 청구 계정에 대 한 트랜잭션 관리

|Task|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기|
|---|---|---|---|
|계정에 대 한 모든 청구 트랜잭션 보기|✔|✔|✔|
|계정에 대해 구매한 모든 제품 보기|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>대금 청구 계정에 대 한 구독 관리

|Task|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기|
|---|---|---|---|
|대금 청구 계정에서 모든 Azure 구독을 확인 합니다.|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>청구 프로필 역할 및 작업

청구 프로필 송장 및 지불 방법을 관리할 수 있습니다. 월별 청구서는 Azure 구독 및 청구 프로필을 사용 하 여 구매 하는 다른 제품에 대해 생성 됩니다. 결제 방법을 사용 하 여 청구서 금액을 지불 합니다. 자세한 내용은 [청구 프로필을 이해](billing-mca-overview.md#understand-billing-profiles)합니다.

다음 표에 청구 프로필의 컨텍스트에서 작업을 완료 해야 하는 어떤 역할을 보여 줍니다.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>청구 프로필 권한, 속성 및 정책 관리

|Task|청구 프로필 소유자|청구 프로필 참가자|청구 프로필 판독기|송장 관리자|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기
|---|---|---|---|---|---|---|---|
|청구 프로필에 대 한 기존 사용 권한을 보려면|✔|✔|✔|✔|✔|✔|✔|
|다른 사용자가 보고 청구 프로필을 관리 하는 권한 부여|✔|✘|✘|✘|✘|✘|✘|
|청구 프로필 속성 보기 같은 PO 번호, 전자 메일 청구서 우선, 등|✔|✔|✔|✔|✔|✔|✔|
|청구 프로필 속성 업데이트 |✔|✔|✘|✘|✘|✘|✘|
|정책 보기와 같은 청구 프로필에 적용 된 Azure 예약 구매를 사용 하도록 설정, Azure marketplace 구매 및 기타 정보를 사용 하도록 설정|✔|✔|✔|✔|✔|✔|✔|
|청구 프로필에 정책 적용 |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>청구 프로필에 대 한 청구서를 관리 합니다.

|Task|청구 프로필 소유자|청구 프로필 참가자|청구 프로필 판독기|송장 관리자|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기
|---|---|---|---|---|---|---|---|
|청구 프로필에 대 한 모든 청구서를 보려면|✔|✔|✔|✔|✔|✔|✔|
|청구서, Azure 사용량 및 요금 파일, 가격표를 다운로드 하 고 세금 청구 프로필에 대 한 문서|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>청구 프로필에 대 한 청구서 섹션 관리

|Task|청구 프로필 소유자|청구 프로필 참가자|청구 프로필 판독기|송장 관리자|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기
|---|---|---|---|---|---|---|---|
|청구 프로필에 대 한 모든 송장 섹션 확인|✔|✔|✔|✔|✔|✔|✔|
|청구 프로필에 대 한 새 청구서 섹션 만들기|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>청구 프로필에 대 한 트랜잭션 관리

|Task|청구 프로필 소유자|청구 프로필 참가자|청구 프로필 판독기|송장 관리자|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기
|---|---|---|---|---|---|---|---|
|청구 프로필에 대 한 모든 청구 트랜잭션 보기|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>청구 프로필에 대 한 결제 방법 관리

|Task|청구 프로필 소유자|청구 프로필 참가자|청구 프로필 판독기|송장 관리자|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기
|---|---|---|---|---|---|---|---|
|청구 프로필에 대 한 결제 방법 보기|✔|✔|✔|✔|✔|✔|✔|
|청구 프로필에 대 한 Azure 크레딧 잔액을 추적합니다|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>청구 프로필에 대 한 구독 관리

|Task|청구 프로필 소유자|청구 프로필 참가자|청구 프로필 판독기|송장 관리자|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기
|---|---|---|---|---|---|---|---|
|청구 프로필에 대 한 모든 Azure 구독을 보려면|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>청구서 섹션 역할 및 작업

청구서 섹션을 사용 하면 청구서에 비용이 구성할 수 있습니다. 조직의 요구에 따라 하거나 개발 환경, 부서별로 비용을 구성 하는 섹션을 만들 수 있습니다. 다른 섹션에 대 한 Azure 구독을 만들 수 있는 권한을 제공 합니다. 모든 사용 요금 및 구매는 구독에 청구서의 섹션에 표시 합니다. 자세한 내용은 [이해 송장 섹션](billing-mca-overview.md#understand-invoice-sections)합니다.

다음 표에서 청구서 섹션의 컨텍스트에서 작업을 완료 해야 하는 어떤 역할을 보여 줍니다.

### <a name="manage-invoice-section-permissions-and-properties"></a>송장 섹션 사용 권한 및 속성 관리

|태스크|청구서 섹션 소유자|청구서 섹션 참가자|청구서 섹션 리더|Azure 구독 작성자|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기 | |
|---|---|---|---|---|---|---|---|---|
|송장 섹션의 모든 사용 권한 보기|✔|✔|✔|✔|✔|✔|✔| |
|다른 사용자가 보고 송장 섹션을 관리 하는 권한 부여|✔|✘|✘|✘|✘|✘|✘| |
|송장 섹션 속성 보기|✔|✔|✔|✔|✔|✔|✔| |
|송장 섹션 속성 업데이트|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>송장 섹션에 대 한 제품 관리

|태스크|청구서 섹션 소유자|청구서 섹션 참가자|청구서 섹션 리더|Azure 구독 작성자|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기
|---|---|---|---|---|---|---|---|
|송장 섹션에서 구매한 모든 제품 보기|✔|✔|✔|✘|✔|✔|✔|
|취소와 같은 청구서 섹션에 대 한 제품에 대 한 청구를 관리, 자동 갱신을 등 해제|✔|✔|✘|✘|✘|✘|✘|
|제품에 대 한 변경 송장 섹션|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>청구서 섹션에 대한 구독 관리

|태스크|청구서 섹션 소유자|청구서 섹션 참가자|청구서 섹션 리더|Azure 구독 작성자|청구 계정 소유자|청구 계정 참가자|청구 계정 판독기
|---|---|---|---|---|---|---|---|
|송장 섹션에 대 한 모든 Azure 구독을 보려면|✔|✔|✔|✘|✔|✔|✔|
|구독에 대 한 변경 송장 섹션|✔|✔|✘|✘|✘|✘|✘|
|구독 청구 소유권을 다른 청구 계정에 사용자의 요청|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>구독 청구 역할 및 작업

다음 표에서 구독의 컨텍스트에서 작업을 완료 해야 하는 어떤 역할을 보여 줍니다.

|태스크|청구서 섹션 소유자|청구서 섹션 참가자|청구서 섹션 리더|Azure 구독 작성자|
|---|---|---|---|---|
|Azure 구독을 만들려면|✔|✔|✘|✔|
|구독에 대 한 비용 센터를 업데이트 합니다.|✔|✔|✘|✘|
|구독에 대 한 변경 송장 섹션|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Azure portal에서 청구 역할 관리

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**에서 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. 선택 **액세스 제어 (IAM)** 대금 청구 계정, 청구 프로필 또는 송장 섹션에서는 액세스 권한을 부여 하려면 저장할 같은 범위에서.

4. 액세스 제어 (IAM) 페이지가 사용자 및 해당 범위에 대 한 각 역할에 할당 된 그룹을 나열 합니다.

   ![대금 청구 계정에 대 한 관리자 목록을 보여 주는 스크린샷](./media/billing-understand-mca-roles/billing-list-admins.png)

5. 에 사용자에 게 액세스 권한을 제공 하려면 **추가** 페이지의 위쪽에서. 역할 드롭 다운 목록에서 역할을 선택 합니다. 액세스 권한을 부여 하려는 사용자의 이메일 주소를 입력 합니다. 선택 **저장할** 역할을 할당 합니다.

   ![관리자는 대금 청구 계정 추가 보여 주는 스크린샷](./media/billing-understand-mca-roles/billing-add-admin.png)

6. 사용자에 대 한 액세스를 제거 하려면 제거할 역할 할당을 사용 하 여 사용자를 선택 합니다. 제거를 선택 합니다.

   ![관리자는 대금 청구 계정에서 제거를 보여 주는 스크린샷](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원 문의
도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

대금 청구 계정에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [Microsoft 고객 계약에 대 한 대금 청구 계정으로 명시 된 가져오기](billing-mca-overview.md)
- [Microsoft 고객 계약에 대 한 대금 청구 계정에 대 한 Azure 구독 만들기](billing-mca-create-subscription.md)
