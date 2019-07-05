---
title: 다른 계정으로 Azure 구독의 청구 소유권을 이전 | Microsoft Docs
description: 프로세스에 대 한 일부 자주 묻는 질문 (FAQ) 및 다른 계정으로 Azure 구독의 청구 소유권을 전송 하는 방법을 설명 합니다.
keywords: azure 구독에 azure 구독을 전송, azure 구독을 다른 계정, azure 구독 소유자 변경 이동, 청구 다른 계정, azure 전송할 azure 구독
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff3c57c31526119ab81225a1c70b163173be937
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514431"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>다른 계정으로 Azure 구독의 청구 소유권 이전

조직에서 나가는 또는 구독을 다른 계정에 요금이 부과 하려는 경우 Azure 구독의 청구 소유권을 전송 하려는 경우. 다른 계정에 대 한 청구 소유권 같은 청구 작업을 수행 하는 새 계정 권한 관리자가 지불 방법 변경, 요금, 보기 및 구독 취소를 제공 합니다.

구독 유형을 변경 하지만 청구 소유권을 유지 하려는 경우 참조 [Azure 구독을 다른 제품으로 전환](billing-how-to-switch-azure-offer.md)합니다. 구독에서 리소스를 관리할 수 있는 사용자를 제어 하려는 경우 참조 [Azure 리소스에 대 한 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)입니다.

엔터프라이즈 Agreement(EA) 고객 인 경우 엔터프라이즈 관리자 계정 간에 구독 청구 소유권을 전송할 수 있습니다. 자세한 내용은 [EA (기업 계약) 구독 청구 소유권을 전송](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions)합니다.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure 구독의 청구 소유권 이전

1. 에 로그인 합니다 [Azure portal](https://portal.azure.com) 전송 하려는 구독에 청구 계정의 관리자 권한으로 합니다. 참조 관리자 인 경우를 찾으려면 [질문과 대답](#faq)합니다.

1. **Cost Management + 청구**에서 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. 선택 **구독** 왼쪽 창에서. 사용자 액세스에 따라 청구 범위를 선택한 다음 선택 해야 할 수 있습니다 **구독** 하거나 **Azure 구독**합니다.

1. 선택 **다른 계정으로 전송** 전송 하려는 구독에 대 한 합니다. 

   ![양도할 구독을 선택 합니다.](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. 구독에 대 한 새 소유자 되며 선택한 계정의 대금 청구 관리자 인 사용자의 전자 메일 주소를 입력 **전송 요청 보내기**합니다.

    > [!IMPORTANT]
    >
    > 다른 Azure AD에서 사용자의 계정에 구독 청구 소유권을 양도할 경우 테 넌 트, 모든 [역할 기반 액세스 제어 (RBAC)](../role-based-access-control/overview.md) 구독에서 리소스를 관리에 대 한 할당은 영구적으로 제거 합니다. 새 소유자만 구독에서 리소스를 관리 하기 위해 액세스를 해야 합니다. 자세한 내용은 [다른 Azure AD 테 넌 트의 사용자에 게 구독을 전송](../active-directory/managed-identities-azure-resources/known-issues.md)합니다.
  
    ![전송 페이지 보내기](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. 사용자 전자 메일을 전송 요청을 검토 하기 위한 지침을 가져옵니다.

   ![받는 사람에게 구독 양도 전자 메일](./media/billing-subscription-transfer/billing-receiver-email.png)

1. 전송 요청을 승인, 사용자 전자 메일의 링크를 선택 하 고 지침을 따릅니다. 사용자 구독에 대 한 요금을 지불 하는 데 사용할 결제 방법을 선택 해야 합니다. 또한 사용자는 Azure 계정에 없는 경우 새 계정을 등록 해야 됩니다. 

   ![첫 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![두 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![두 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. 성공! 구독이 이제 양도됩니다.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>다른 Azure AD 테 넌 트의 계정에 구독을 전송합니다.

Azure에 등록 하는 경우에 Azure Active Directory (AD) 테 넌 트를 만들어집니다. 테 넌 트 계정을 나타냅니다. 테 넌 트를 사용 하 여 구독 및 리소스에 대 한 액세스 관리.

새 구독을 만들 때 Azure AD 테 넌 트 계정에 호스팅됩니다. 다른 구독 또는 리소스에 액세스할 수 있도록 하려는 경우에 테 넌 트에 초대 해야 합니다. 이 사용 하면 구독 및 리소스에 대 한 액세스를 제어 합니다.

다른 Azure AD 테 넌 트의 계정에 구독 청구 소유권을 전송할 때 테 넌 트 새 계정의 구독 이동 합니다. 모든 사용자, 그룹 또는 서비스 주체 가졌던 [역할 기반 액세스 (RBAC)](../role-based-access-control/overview.md) 구독에서 리소스를 관리 하는 액세스할 수 없게 합니다. 전송 요청을 수락 하는 새 계정에 사용자만 리소스를 관리 하기 위해 액세스를 해야 합니다. 원래 액세스 한 사용자에 게 액세스를 제공 하려면 새 소유자는 것 [구독에 이러한 사용자를 수동으로 추가](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)합니다.


## <a name="transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Visual Studio, Microsoft 파트너 네트워크 (MPN) 및 종 량 제 개발/테스트 구독 전송

Visual Studio 및 Microsoft 파트너 네트워크 구독의 경우 연결 된 되풀이 월간 Azure 크레딧 이러한 구독을 전송할 때 크레딧을 대상 청구 계정에 사용할 수 없는 경우 구독 대상 청구 계정에 신용을 사용합니다. 예를 들어 bob이 Jane의 9 월 9 일에 계정에 jane은 Visual Studio Enterprise 구독을 양도 하는 경우에 전송을 허용 합니다. 전송이 완료 되 면 구독 크레딧 Jane의 계정에서 사용 하 여 시작 합니다. 크레딧을 매월 9에 다시 설정 됩니다. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>EA (기업 계약) 구독 청구 소유권 이전

엔터프라이즈 관리자는 등록 내 계정 간에 구독 소유권을 전송할 수 있습니다. 자세한 내용은 [계정 소유권 전송](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) EA 포털에서 합니다.

<a id="CSP"></a>

## <a name="next-steps-after-accepting-billing-ownership"></a>청구 소유권을 수락한 후 다음 단계

Azure 구독의 청구 소유권을 수락한 경우에 다음 단계를 검토 하는 것이 좋습니다.

1. 서비스 관리자, 공동 관리자 및 다른 RBAC 역할을 검토하고 업데이트합니다. 자세한 내용은 [Azure 구독 관리자 추가 또는 변경](billing-add-change-azure-subscription-administrator.md) 및 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.
1. 다음을 비롯한 이 구독의 서비스와 연결된 자격 증명을 업데이트합니다.
   1. 구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서. 자세한 내용은 [Azure 용 관리 인증서 만들기 및 업로드](../cloud-services/cloud-services-certs-create.md)
   1. 저장소와 같은 서비스에 대한 액세스 키. 자세한 내용은 [Azure Storage 계정 정보](../storage/common/storage-create-storage-account.md)를 참조하세요.
   1. Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.
1. 파트너와 함께 작업하는 경우 이 구독에서 파트너 ID를 업데이트하는 것이 좋습니다. [Azure Portal](https://portal.azure.com)에서 파트너 ID를 업데이트할 수 있습니다. 자세한 내용은 참조 하세요. [Azure 계정에 파트너 ID 연결](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>지원되는 구독 유형

Azure portal의 구독 이전이 아래에 나열 된 구독 유형에 사용할 수 있습니다. 현재 전송에 대 한 지원 되지 않습니다 [무료 평가판](https://azure.microsoft.com/offers/ms-azr-0044p/) 하거나 [Open (AIO)에서 Azure](https://azure.microsoft.com/offers/ms-azr-0111p/) 구독 합니다. 해결 방법을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요. 와 같은 다른 구독을 전송 [스폰서쉽](https://azure.microsoft.com/offers/ms-azr-0036p/) 또는 지원 플랜과 [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)합니다.

- [EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft 파트너 네트워크](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise(MPN) 구독자](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [MSDN 플랫폼](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [EA 포털을 통해](#EA)합니다.

\*\* Azure 웹 사이트에서 구성 로그온 시 생성 된 계정에 대해서만 지원 합니다. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>질문과 대답 (FAQ) 발신자에 대 한

이 Faq는 Azure 구독의 청구 소유권을 다른 계정으로 전송 하는 사용자에 적용 됩니다.

### <a name="whoisaa"></a> 계정 대금 청구 관리자는 누구 인가요?

대금 청구 관리자는 계정의 결제를 관리할 수 있는 권한을 가진 사람입니다. 청구에 액세스할 수 있는 권한이 있는 합니다 [Azure portal](https://portal.azure.com) 구독, 보기 및 지불 청구서 또는 업데이트를 만드는 등의 다양 한 청구 작업을 수행 하 고 결제 방법입니다.

대금 청구 관리자 라면 구독을 식별 하려면 다음 단계를 사용 합니다.

1. 방문 합니다 [Cost Management + Azure portal의 청구 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)합니다.
1. 선택 **구독** 왼쪽 창에서. 사용자 액세스에 따라 청구 범위를 선택한 다음 선택 해야 할 수 있습니다 **구독** 또는 **Azure 구독**
1. 구독 페이지는 대금 청구 관리자는 모든 구독을 나열 합니다.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>모든 것이 양도되나요? 양도 항목에 리소스 그룹, VM, 디스크 및 기타 실행 중인 서비스가 포함되나요?

모든 리소스는 Vm, 디스크 및 새 계정으로 웹 사이트 전송 등입니다. 그러나 다른 Azure AD의 계정에 구독을 전송 하는 경우 테 넌 트, 모든 [관리자 역할](billing-add-change-azure-subscription-administrator.md) 하 고 [역할 기반 Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) 구독에서 할당 [하지 전송](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)합니다. 또한 [앱 등록](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) 및 다른 테 넌 트 별 서비스 구독과 함께 전송 하지 않습니다.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>다른 국가에서 계정에 소유권을 양도할 수 있습니까?
그러나 Azure portal에서 국가 간 전송은 수행할 수 없습니다. 국가, 구독을 전송할 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)합니다.

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>두 계정에서 관리자입니다. 수는 구독 내 계정 중 하나에서 다른 전송?
예, 계정 간에 구독을 전송할 수 있습니다. 개념적으로 계정에는 계정 간에 구독을 전송 하려면 위의 단계를 사용할 수 있도록 두 개의 서로 다른 사용자의 계정을 간주 됩니다.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>구독 양도로 인해 서비스 가동 중지 시간이 발생합니까?

동일한 Azure AD 테 넌 트의 사용자에 게 구독을 양도할 경우 구독에서 실행 되는 리소스에 영향을 주지 않습니다.  그러나 다른 사용자에 게 구독을 전송 하는 경우 테 넌 트를 모든 사용자, 그룹 및 서비스 주체 가졌던 [역할 기반 액세스 (RBAC)](../role-based-access-control/overview.md) 구독에서 리소스를 관리 하는 액세스할 수 없게 합니다. 

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>받는 사람은 사용량 및 청구 내역에 액세스할 수 있습니까?

받는 사람에 게 제공 되는 유일한 정보는 구독에 대 한 지난 달의 비용. 사용량 및 청구 내역 나머지 구독을 사용 하 여 전송 되지 않습니다.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>내 Azure 구독에 대한 데이터 및 서비스를 새 구독으로 마이그레이션하려면 어떻게 해야 하나요?

구독 소유권을 양도할 수 없는 경우 리소스를 수동으로 마이그레이션할 수 있습니다. [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요.

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Visual Studio 또는 Microsoft 파트너 네트워크 구독을 양도 하는 경우는 크레딧을 전달 된 새 계정의 구독?

아니요, 크레딧을 새 계정에 사용할 수 없는 경우 전송 요청을 수락 하는 사용자는 Visual Studio 라이선스가 전송 요청을 수락 해야 합니다. 구독에는 사용자의 계정에서 사용할 수 있는 Visual Studio 크레딧을 사용 합니다. 자세한 내용은 참조 하세요. [전송 Visual Studio, Microsoft 파트너 네트워크 (MPN) 및 종 량 제 개발/테스트 구독](#transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions)


## <a name="frequently-asked-questions-faq-for-recipients"></a>질문과 대답 (FAQ) 받는 사람에 게

이 Faq는 다른 계정에서 Azure 구독의 청구 소유권 수강 하는 사용자에 게 적용 합니다.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>다른 계정에서 구독 청구 소유권을 수행 하는 경우 사용자는 계정 내 리소스에 액세스할 수 있도록 계속 합니까?

동일한 Azure AD 테 넌 트, 모든 사용자, 그룹 및 가진 서비스 주체에서 계정에 구독을 양도할 경우 [역할 기반 액세스 (RBAC)](../role-based-access-control/overview.md) 구독에서 리소스를 관리 하에 대 한 액세스를 유지 합니다. 구독에 대 한 RBAC 액세스 권한이 있는 사용자를 보려면 다음 단계를 사용 합니다.

1. 방문 합니다 [Azure portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)합니다.
1. 를 확인 하 고 선택 하려는 구독을 선택 **액세스 제어 (IAM)** 왼쪽 창에서.
1. 선택 **역할 할당** 페이지의 위쪽에서. 역할 할당 페이지에서 구독에 대 한 RBAC 액세스를 가진 모든 사용자를 나열 합니다.

다른 Azure AD 테 넌 트, 모든 사용자, 그룹 및 가진 서비스 주체에서 계정에 구독을 양도할 경우 [역할 기반 액세스 (RBAC)](../role-based-access-control/overview.md) 구독에서 리소스를 관리 하는 액세스할 수 없게 합니다. 그러나 RBAC 액세스 권한이 더 이상, 경우에 포함 하는 일부 보안 메커니즘을 통해 구독에 대 한 액세스를 겪을 수 있습니다 이러한.

* 구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서. 자세한 내용은 [Azure용 관리 인증서 만들기 및 업로드](../cloud-services/cloud-services-certs-create.md)를 참조하세요.
* 저장소와 같은 서비스에 대한 액세스 키. 자세한 내용은 [Azure 저장소 계정 정보](../storage/common/storage-create-storage-account.md)를 참조하세요.
* Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.

받는 사람을 제한, 해당 리소스에 액세스 하는 경우 서비스와 연결 된 암호를 업데이트 하는 중 것이 좋습니다. 다음 단계를 사용하여 대부분의 리소스를 업데이트할 수 있습니다.

  1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
  2. 허브 메뉴에서 **모든 리소스**를 선택합니다.
  3. 리소스를 선택합니다.
  4. 리소스 페이지에서 클릭 **설정을**합니다. 여기서 기존 암호를 보고 업데이트할 수 있습니다.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>청구 주기 도중 구독 청구 소유권을 수행 하는 경우 전체 청구 주기에 대 한 요금을 지불 해야 합니까?

계정에 양도에서 보고 되는 모든 사용량에 대 한 지불 책임이 있습니다. 양도하기 전에 발생하지만 나중에 보고되는 일부 사용량이 있을 수 있습니다. 사용량은 계정의 청구서에 포함 됩니다.

### <a name="can-i-use-a-different-payment-method"></a>다른 지불 방법을 사용할 수 있습니까?

예. 전송 요청에 동의 하는 동안 계정에 연결 되어 있거나 새 결제 방법을 추가 하는 기존 지불 방법을 선택할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

### <a id="no-button"></a> "구독 양도" 단추가 보이지 않는 이유는 무엇인가요?

아쉽게도 셀프 서비스 구독 양도 대금 청구 계정에 사용할 수 없습니다. 현재 Azure portal에서 계정 EA (기업 계약)의 청구 소유권을 전송할 수 없습니다. 또한 Microsoft sales 진행 하면서 만든 고객 계약을 Microsoft 계정 구독 청구 소유권을 지원 하지 않습니다. 

### <a id="no-button"></a> 내 구독 하지 않는 이유는 입력 지원 전송? 

그러나 일부 유형의 구독 청구 소유권 양도 지원 합니다. 전송을 지 원하는 구독 형식의 목록을 보려면를 참조 하세요. [지원 되는 구독 유형](#supported-subscription-types)

### <a id="no-button"></a> 액세스 거부 오류가 구독 청구 소유권을 전송 하려고 하면 이유는 무엇입니까? 

Microsoft Azure 계획 구독을 양도 하려고 하는 데 필요한 권한이 없는 경우이 오류가 표시 됩니다. Microsoft Azure 계획 구독을 전송 하려면 소유자 또는 참가자는 구독 요금이 청구 됩니다 송장 섹션에는 그대로 유지 되도록 해야 합니다. 자세한 내용은 [청구서 섹션에 대 한 구독을 관리할](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section)합니다.


## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계

- 서비스 관리자, 공동 관리자 및 다른 RBAC 역할을 검토하고 업데이트합니다. 자세한 내용은 [Azure 구독 관리자 추가 또는 변경](billing-add-change-azure-subscription-administrator.md) 및 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.
