---
title: Azure 구독의 청구 소유권을 다른 계정으로 양도 | Microsoft Docs
description: Azure 구독의 청구 소유권을 다른 계정으로 전송 하는 방법 및 프로세스에 대 한 몇 가지 FAQ (질문과 대답)를 설명 합니다.
keywords: azure 구독을 전송 하 고, azure 구독을 다른 계정으로 이동 하 고, azure 구독을 다른 계정으로 이동 하 고, azure 구독을 다른 계정으로 양도 하 고, azure 이전 요금 청구
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
ms.openlocfilehash: 1e4496e2b5d2b21fd878ef68665b8e5b06fa6cc5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012535"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure 구독의 청구 소유권을 다른 계정으로 이전

조직을 탈퇴 하거나 구독을 다른 계정으로 청구 하려는 경우 Azure 구독의 청구 소유권을 양도 하는 것이 좋습니다. 청구 소유권을 다른 계정으로 이전 하는 작업은 지불 방법 변경, 요금 청구 및 구독 취소와 같은 청구 작업을 수행할 수 있는 새 계정 권한을 관리자에 게 제공 합니다.

청구 소유권을 유지 하지만 구독의 유형을 변경 하려면 [Azure 구독을 다른 제품으로 전환](billing-how-to-switch-azure-offer.md)을 참조 하세요. 구독에서 리소스를 관리할 수 있는 사용자를 제어 하려면 [Azure 리소스에 대 한 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 참조 하세요.

EA (기업계약) 고객 인 경우 엔터프라이즈 관리자는 계정 간에 구독의 청구 소유권을 양도할 수 있습니다. 자세한 내용은 [기업계약 (EA) 구독의 청구 소유권 이전](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions)을 참조 하세요.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure 구독의 청구 소유권 이전

1. 전송 하려는 구독이 있는 청구 계정의 관리자 권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다. 관리자 인지 확인 하려면 질문과 [대답](#faq)을 참조 하세요.

1. **Cost Management + 청구**에서 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. 왼쪽 창에서 **구독** 을 선택 합니다. 액세스에 따라 청구 범위를 선택한 다음 **구독** 또는 **Azure 구독**을 선택 해야 할 수 있습니다.

1. 전송 하려는 구독에 대 한 **청구 소유권을 전송** 합니다 .를 선택 합니다. 

   ![전송할 구독 선택](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. 구독의 새 소유자가 될 계정의 대금 청구 관리자 인 사용자의 메일 주소를 입력 합니다.

1. 구독을 다른 Azure AD 테 넌 트의 계정으로 전송 하는 경우 구독을 새 계정의 테 넌 트로 이동할지 여부를 선택 합니다. 자세한 내용은 [다른 AZURE AD 테 넌 트에서 계정으로 구독 전송](#transferring-subscription-to-an-account-in-another-azure-ad-tenant) 을 참조 하세요.

    > [!IMPORTANT]
    >
    > 구독을 새 계정의 Azure AD 테 넌 트로 이동 하도록 선택 하면 구독에서 리소스를 관리 하기 위한 모든 [RBAC (역할 기반 액세스 제어)](../role-based-access-control/overview.md) 할당이 영구적으로 제거 됩니다. 전송 요청을 수락 하는 새 계정의 사용자 에게만 구독의 리소스를 관리할 수 있는 권한이 있습니다. 자세한 내용은 [다른 AZURE AD 테 넌 트의 사용자에 게 구독 전송](../active-directory/managed-identities-azure-resources/known-issues.md)을 참조 하세요. 또는 구독 Azure AD 테 넌 트의 확인란을 선택 취소 하 여 구독을 새 계정의 테 넌 트로 이동 하지 않고도 청구 소유권을 전송할 수 있습니다. 이렇게 하면 Azure 리소스를 관리 하는 기존 RBAC 권한이 유지 됩니다.
  
    ![전송 페이지 보내기](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. **전송 요청 보내기**를 선택 합니다.

1. 사용자는 전송 요청을 검토 하는 지침이 포함 된 전자 메일을 가져옵니다.

   ![받는 사람에 게 보낸 구독 전송 전자 메일](./media/billing-subscription-transfer/billing-receiver-email.png)

1. 전송 요청을 승인 하기 위해 사용자는 전자 메일의 링크를 선택 하 고 지침을 따릅니다. 사용자는 구독 요금을 지불 하는 데 사용 될 지불 방법을 선택 해야 합니다. 또한 사용자에 게 Azure 계정이 없는 경우 새 계정을 등록 해야 합니다. 

   ![첫 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![두 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![두 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. 성공 구독이 이제 양도됩니다.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>다른 Azure AD 테 넌 트에서 계정으로 구독 전송

Azure에 등록할 때 AD (Azure Active Directory) 테 넌 트가 만들어집니다. 테 넌 트는 사용자 계정을 나타냅니다. 테 넌 트를 사용 하 여 구독 및 리소스에 대 한 액세스를 관리 합니다.

새 구독을 만들 때 계정에 대 한 Azure AD 테 넌 트에서 호스팅됩니다. 구독 또는 해당 리소스에 대 한 액세스를 다른 사용자에 게 제공 하려면 테 넌 트에 가입 하도록 초대 해야 합니다. 그러면 구독과 리소스에 대 한 액세스를 제어할 수 있습니다.

구독의 청구 소유권을 다른 Azure AD 테 넌 트의 계정으로 전송 하는 경우 구독을 새 계정의 테 넌 트로 이동할 수 있습니다. 이렇게 하면 구독 및 해당 리소스를 관리 하기 위해 [RBAC (역할 기반 액세스)](../role-based-access-control/role-assignments-portal.md) 가 있는 모든 사용자, 그룹 또는 서비스 사용자의 액세스 권한이 손실 됩니다. 전송 요청을 수락 하는 새 계정의 사용자 에게만 리소스를 관리할 수 있는 권한이 있습니다. 원래 액세스 권한이 있는 사용자에 게 액세스 권한을 제공 하려면 새 소유자가 해당 사용자를 [구독에 수동으로 추가](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)해야 합니다.


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Visual Studio 전송, MPN 및 종 량 제 개발/테스트 구독

Visual Studio 및 Microsoft 파트너 네트워크 구독에는 월간 되풀이 Azure 크레딧이 연결 되어 있습니다. 이러한 구독을 전송 하는 경우 대상 청구 계정에서 크레딧을 사용할 수 없습니다. 구독은 대상 청구 계정의 크레딧을 사용 합니다. 예를 들어 Bob이 9 월 9 일에 Visual Studio Enterprise 구독을 Jane의 계정으로 전송 하 고 Jane은 전송을 허용 합니다. 전송이 완료 되 면 Jane의 계정에서 크레딧을 사용 하 여 구독을 시작 합니다. Credit은 매월 9 분에 다시 설정 됩니다. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>EA (기업계약) 구독의 청구 소유권 이전

엔터프라이즈 관리자는 등록 내의 계정 간에 구독 소유권을 이전할 수 있습니다. 자세한 내용은 EA 포털에서 [계정 소유권 이전](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) 을 참조 하세요.

## <a name="next-steps-after-accepting-billing-ownership"></a>청구 소유권을 승인한 후 다음 단계

Azure 구독의 청구 소유권을 수락한 경우 다음 단계를 검토 하는 것이 좋습니다.

1. 서비스 관리자, 공동 관리자 및 다른 RBAC 역할을 검토하고 업데이트합니다. 자세한 내용은 [Azure 구독 관리자 추가 또는 변경](billing-add-change-azure-subscription-administrator.md) 및 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.
1. 다음을 비롯한 이 구독의 서비스와 연결된 자격 증명을 업데이트합니다.
   1. 구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서. 자세한 내용은 [Azure 용 관리 인증서 만들기 및 업로드](../cloud-services/cloud-services-certs-create.md)
   1. 스토리지와 같은 서비스에 대한 액세스 키. 자세한 내용은 [Azure Storage 계정 정보](../storage/common/storage-create-storage-account.md)를 참조하세요.
   1. Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.
1. 파트너와 함께 작업하는 경우 이 구독에서 파트너 ID를 업데이트하는 것이 좋습니다. [Azure Portal](https://portal.azure.com)에서 파트너 ID를 업데이트할 수 있습니다. 자세한 내용은 [Azure 계정에 파트너 ID 연결](billing-partner-admin-link-started.md) 을 참조 하세요.

<a id="supported"></a>

## <a name="supported-subscription-types"></a>지원되는 구독 유형

Azure Portal의 구독 전송은 아래 나열 된 구독 유형에 사용할 수 있습니다. 현재는 AIO ( [무료 평가판](https://azure.microsoft.com/offers/ms-azr-0044p/) 또는 [Azure in Open)](https://azure.microsoft.com/offers/ms-azr-0111p/) 구독에 대해 전송이 지원 되지 않습니다. 해결 방법을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요. 다른 구독 (예: [후원](https://azure.microsoft.com/offers/ms-azr-0036p/) 또는 지원 계획)을 전송 하려면 [Azure 지원에 문의 하세요](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

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
- [Microsoft Azure 계획](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\*[EA 포털을 통해](#EA).

\*\*Azure 웹 사이트에서 등록 하는 동안 생성 된 계정에 대해서만 지원 됩니다. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>발신자를 위한 질문과 대답 (FAQ)

이러한 Faq는 Azure 구독의 청구 소유권을 다른 계정으로 전송 하는 사용자에 게 적용 됩니다.

### <a name="whoisaa"></a>계정에 대 한 대금 청구 관리자는 누구 인가요?

청구 관리자는 계정에 대 한 청구를 관리할 수 있는 권한이 있는 사용자입니다. [Azure Portal](https://portal.azure.com) 에 대 한 청구에 액세스 하 고 구독 만들기, 청구서 보기 및 지불, 지불 방법 업데이트와 같은 다양 한 청구 작업을 수행할 수 있는 권한이 있습니다.

청구 관리자 인 계정을 식별 하려면 다음 단계를 사용 합니다.

1. [Azure Portal에서 Cost Management + 청구 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)를 방문 하세요.
1. 왼쪽 창에서 **모든 청구 범위** 를 선택 합니다. 
1. 구독 페이지에는 사용자가 청구 관리자 인 모든 구독이 나열 됩니다.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>모든 것이 양도되나요? 양도 항목에 리소스 그룹, VM, 디스크 및 기타 실행 중인 서비스가 포함되나요?

Vm, 디스크, 웹 사이트 등의 모든 리소스는 새 계정으로 전송 됩니다. 그러나 다른 Azure AD 테 넌 트의 계정에 구독을 전송 하는 경우 구독에 대 한 [관리자 역할](billing-add-change-azure-subscription-administrator.md) 및 [RBAC (역할 기반 Access Control)](../role-based-access-control/role-assignments-portal.md) 할당은 [전송 되지](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)않습니다. 또한 [앱 등록](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) 및 기타 테 넌 트 관련 서비스는 구독과 함께 전송 되지 않습니다.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>소유권을 다른 국가의 계정으로 이전할 수 있나요?
그러나 Azure Portal에서 국가 간 전송을 수행할 수 없습니다. 국가 간에 구독을 양도 하려면 [지원 담당자에 게 문의 하세요](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>두 계정에 대 한 관리자입니다. 내 계정 간에 구독을 양도할 수 있나요?
예, 계정 간에 구독을 전송할 수 있습니다. 사용자 계정은 개념적으로 두 명의 사용자 계정을 고려 하므로 위의 단계를 사용 하 여 계정 간에 구독을 전송할 수 있습니다.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>구독 양도로 인해 서비스 가동 중지 시간이 발생합니까?

동일한 Azure AD 테 넌 트의 계정에 구독을 전송 하는 경우 구독에서 실행 중인 리소스에는 영향을 주지 않습니다. 그러나 구독을 다른 테 넌 트의 계정으로 전송 하 고 구독을 테 넌 트로 이동 하기로 결정 한 경우에는 [RBAC (역할 기반 액세스)](../role-based-access-control/overview.md) 를 사용 하 여 구독의 리소스를 관리 하는 모든 사용자, 그룹 및 서비스 사용자의 액세스 권한이 손실 됩니다. . 이로 인해 서비스 가동 중지 시간이 발생할 수 있습니다.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>새 계정의 사용자는 사용량 및 청구 내역에 액세스할 수 있나요?

새 계정의 사용자에 게 제공 되는 유일한 정보는 구독에 대 한 마지막 월의 비용입니다. 나머지 사용 및 청구 기록은 구독과 함께 전송 되지 않습니다.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>내 Azure 구독에 대한 데이터 및 서비스를 새 구독으로 마이그레이션하려면 어떻게 해야 하나요?

구독 소유권을 양도할 수 없는 경우 리소스를 수동으로 마이그레이션할 수 있습니다. [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요.

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Visual Studio 또는 Microsoft 파트너 네트워크 구독을 전송 하는 경우 크레딧이 새 계정에서 구독과 함께 전달 되나요?

아니요, 새 계정에서 크레딧을 사용할 수 없습니다. 전송 요청을 수락 하는 사용자에 게는 전송 요청을 수락 하기 위한 Visual Studio 라이선스가 있어야 합니다. 구독은 사용자 계정에서 사용할 수 있는 Visual Studio 크레딧을 사용 합니다. 자세한 내용은 [Visual Studio 전송, Microsoft 파트너 네트워크 (MPN) 및 종 량 제 개발/테스트 구독](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions)을 참조 하세요.


## <a name="frequently-asked-questions-faq-for-recipients"></a>받는 사람에 대 한 FAQ (질문과 대답)

이러한 Faq는 다른 계정에서 Azure 구독의 청구 소유권을 수락 하는 사용자에 게 적용 됩니다.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>다른 계정에서 구독의 청구 소유권을 초과 하는 경우 해당 계정의 사용자는 계속 해 서 내 리소스에 액세스할 수 있나요?

예. 그러나 계정이 구독의 테 넌 트와 다른 Azure AD 테 넌 트에 있고 전송 요청을 보낸 사용자가 구독을 계정의 테 넌 트로 이동 하는 경우, [관리자 역할](billing-add-change-azure-subscription-administrator.md) 및 [RBAC (역할 기반 Access Control) ](../role-based-access-control/role-assignments-portal.md)할당이 제거 됩니다. 구독에서 리소스를 관리 하기 위해 [RBAC (역할 기반 액세스)](../role-based-access-control/overview.md) 액세스 권한이 있는 사용자를 보려면 다음 단계를 사용 합니다.

1. [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)를 방문 하세요.
1. 확인 하려는 구독을 선택 하 고 왼쪽 창에서 **액세스 제어 (IAM)** 를 선택 합니다.
1. 페이지 맨 위에서 **역할 할당** 을 선택 합니다. 역할 할당 페이지에는 구독에 대 한 RBAC 액세스 권한이 있는 모든 사용자가 나열 됩니다.

전송 하는 동안 [RBAC (역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md) ) 할당이 제거 되더라도 원래 소유자 계정의 사용자는 다음을 비롯 한 몇 가지 보안 메커니즘을 통해 구독에 계속 액세스할 수 있습니다.

* 구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서. 자세한 내용은 [Azure용 관리 인증서 만들기 및 업로드](../cloud-services/cloud-services-certs-create.md)를 참조하세요.
* 스토리지와 같은 서비스에 대한 액세스 키. 자세한 내용은 [Azure Storage 계정 정보](../storage/common/storage-create-storage-account.md)를 참조하세요.
* Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.

받는 사람이 해당 리소스에 대 한 액세스를 제한 해야 하는 경우 서비스와 연결 된 암호를 업데이트 하는 것을 고려해 야 합니다. 다음 단계를 사용하여 대부분의 리소스를 업데이트할 수 있습니다.

  1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
  2. 허브 메뉴에서 **모든 리소스**를 선택합니다.
  3. 리소스를 선택합니다.
  4. 리소스 페이지에서 **설정**을 클릭 합니다. 여기서 기존 암호를 보고 업데이트할 수 있습니다.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>청구 주기 중에 구독의 청구 소유권을 가져오는 경우 전체 청구 주기에 대해 지불 해야 하나요?

사용자 계정은 전송 시점부터 보고 되는 모든 사용량에 대 한 지불을 담당 합니다. 양도하기 전에 발생하지만 나중에 보고되는 일부 사용량이 있을 수 있습니다. 사용량은 계정 청구서에 포함 됩니다.

### <a name="can-i-use-a-different-payment-method"></a>다른 지불 방법을 사용할 수 있나요?

예. 전송 요청을 수락 하는 동안 계정에 연결 된 기존 지불 방법을 선택 하거나 새 지불 방법을 추가할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

### <a id="no-button"></a> "구독 양도" 단추가 보이지 않는 이유는 무엇인가요?

청구 계정에는 셀프 서비스 구독 양도를 사용할 수 없습니다. 현재는 Azure Portal의 EA (기업계약) 계정에서 구독의 청구 소유권을 전송 하는 기능을 지원 하지 않습니다. 또한 Microsoft 담당자를 사용 하 여 작업 하는 동안 생성 된 Microsoft 고객 계약 계정은 청구 소유권의 양도를 지원 하지 않습니다. 

### <a id="no-button"></a>내 구독 유형에 서 전송이 지원 되지 않는 이유는 무엇 인가요? 

일부 구독 유형은 청구 소유권 이전을 지원 하지 않습니다. 전송을 지 원하는 구독 유형 목록을 보려면 [지원 되는 구독 유형](#supported-subscription-types) 을 참조 하세요.

### <a id="no-button"></a>구독의 청구 소유권을 이전 하려고 할 때 액세스 거부 오류가 발생 하는 이유는 무엇 인가요? 

Microsoft Azure 계획 구독을 전송 하려고 하는데 필요한 권한이 없는 경우이 오류가 표시 됩니다. Microsoft Azure 계획 구독을 전송 하려면 구독 요금이 청구 되는 청구서 섹션에 소유자 또는 참가자가 있어야 합니다. 자세한 내용은 [청구서에 대 한 구독 관리 섹션](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section)을 참조 하세요.


## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

궁금한 사항이 있거나 도움이 필요 하면 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- 서비스 관리자, 공동 관리자 및 다른 RBAC 역할을 검토하고 업데이트합니다. 자세한 내용은 [Azure 구독 관리자 추가 또는 변경](billing-add-change-azure-subscription-administrator.md) 및 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.
