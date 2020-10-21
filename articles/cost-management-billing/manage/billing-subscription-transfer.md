---
title: Azure 구독의 청구 소유권 양도
description: Azure 구독의 청구 소유권을 다른 계정에 양도하는 방법을 설명합니다.
keywords: azure 구독 양도, 구독 양도 azure, azure 구독을 다른 계정에 양도, azure 구독 소유자 변경, azure 구독을 다른 계정으로 양도, azure 청구 양도
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/23/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: d2814a989cb82216081083f3198d52ccd70210ff
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131924"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure 구독의 청구 소유권을 다른 계정에 양도

이 문서에서는 Azure 구독의 청구 소유권을 다른 계정에 양도하는 데 필요한 단계를 보여 줍니다. 구독의 청구 소유권을 양도하기 전에 [Azure 구독의 청구 소유권을 양도하는 방법 소개](../understand/subscription-transfer.md)를 참조하세요.

청구 소유권을 유지하면서 구독 유형만 변경하려면 [Azure 구독을 다른 제안으로 전환](switch-azure-offer.md)을 참조하세요. 구독의 리소스에 액세스할 수 있는 사용자를 제어하려면 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

EA(기업계약) 고객인 경우 엔터프라이즈 관리자가 계정 간에 구독의 청구 소유권을 양도할 수 있습니다. 자세한 내용은 [계정 소유자 변경](ea-portal-get-started.md#change-account-owner)을 참조하세요.

계정의 대금 청구 관리자만 구독의 소유권을 양도할 수 있습니다.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure 구독의 청구 소유권 양도

1. 양도하려는 구독이 있는 청구 계정의 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 자신이 관리자인지 여부를 잘 모르는 경우 또는 누가 관리자인지 확인해야 하는 경우 [계정의 대금 청구 관리자 확인](../understand/subscription-transfer.md#whoisaa)을 참조하세요.
1. **Cost Management + 청구**를 검색합니다.  
   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. 왼쪽 창에서 **구독**을 선택합니다. 액세스 권한에 따라 청구 범위를 선택하고 **구독** 또는 **Azure 구독**을 선택해야 할 수도 있습니다.
1. 양도하려는 구독의 **청구 소유권 양도**를 선택합니다.  
   ![양도할 구독 선택](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. 구독의 새 소유자가 될 계정의 청구 관리자인 사용자의 이메일 주소를 입력합니다.
1. 구독을 다른 Azure AD 테넌트의 계정으로 양도하는 경우 구독을 새 계정의 테넌트로 이전할지 여부를 선택합니다. 자세한 내용은 [다른 Azure AD 테넌트의 계정으로 구독 양도](#transfer-a-subscription-to-another-azure-ad-tenant-account)를 참조하세요.
    > [!IMPORTANT]
    > 구독을 새 계정의 Azure AD 테넌트로 이동하기로 선택하면 구독의 리소스에 액세스하기 위한 모든 [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)이 영구적으로 제거됩니다. 양도 요청을 수락하는 새 계정의 사용자에게만 구독의 리소스를 관리할 수 있는 권한이 제공됩니다. 또는 구독을 새 계정의 테넌트로 양도하지 않고도 **구독 Azure AD 테넌트** 옵션의 선택을 취소하여 청구 소유권을 양도할 수 있습니다. 이렇게 하면 Azure 리소스에 액세스하기 위한 기존 Azure 역할 할당이 유지됩니다.  
    ![양도 요청 보내기 페이지](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. **이전 요청 보내기**를 선택합니다.
1. 사용자는 이전 요청을 검토하는 지침이 포함된 이메일을 받습니다.  
   ![받는 사람에게 전송된 구독 양도 이메일](./media/billing-subscription-transfer/billing-receiver-email.png)
1. 전송 요청을 승인하려면 사용자는 이메일의 링크를 선택하고 지침을 따릅니다. 그런 다음, 사용자는 구독 요금을 결제하는 데 사용할 결제 방법을 선택합니다. 사용자는 Azure 계정이 없는 경우 새 계정에 가입해야 합니다.  
   ![첫 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![두 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![세 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. 성공! 구독이 이제 양도됩니다.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>다른 Azure AD 테넌트 계정으로 구독 양도

Azure에 가입하면 Azure AD(Active Directory) 테넌트가 자동으로 만들어집니다. 테넌트는 계정을 나타냅니다. 테넌트를 사용하여 구독 및 리소스에 대한 액세스를 관리합니다.

새 구독을 만들면 계정의 Azure AD 테넌트에 호스팅됩니다. 다른 사람에게 구독 또는 해당 리소스에 대한 액세스 권한을 제공하려면 테넌트에 가입하도록 초대해야 합니다. 이렇게 하면 구독과 리소스에 대한 액세스를 제어하는 데 도움이 됩니다.

구독의 청구 소유권을 다른 Azure AD 테넌트의 계정에 양도할 때 구독을 새 계정의 테넌트에 양도할 수 있습니다. 이렇게 하면 구독 및 구독의 리소스를 관리하는 [Azure 역할이 할당된](../../role-based-access-control/role-assignments-portal.md) 모든 사용자, 그룹 또는 서비스 주체의 액세스 권한이 사라집니다. 양도 요청을 수락하는 새 계정의 사용자에게만 리소스를 관리할 수 있는 액세스 권한이 제공됩니다. 권한을 상실한 사용에 대한 액세스를 제공하려면 새 소유자가 이러한 사용자를 구독에 수동으로 추가해야 합니다. 자세한 내용은 [다른 Azure AD 디렉터리로 Azure 구독 양도](../../role-based-access-control/transfer-subscription.md)를 참조하세요.

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Visual Studio 및 파트너 네트워크 구독 양도

Visual Studio 및 Microsoft 파트너 네트워크 구독에는 월간 반복 Azure 크레딧이 연결되어 있습니다. 이러한 구독을 양도하면 대상 청구 계정에서 크레딧을 사용할 수 없습니다. 구독에서는 대상 청구 계정의 크레딧을 사용합니다. 예를 들어 Bob이 9월 9일에 Visual Studio Enterprise 구독을 Jane의 계정으로 양도하고 Jane이 양도를 수락한다고 가정하겠습니다. 양도가 완료되면 구독에서는 Jane 계정의 크레딧을 사용하여 구독을 시작합니다. 크레딧은 매월 9일에 다시 설정됩니다.

## <a name="next-steps-after-accepting-billing-ownership"></a>청구 소유권을 수락한 후의 다음 단계

Azure 구독의 청구 소유권을 수락한 경우 다음 단계를 검토하는 것이 좋습니다.

1. 서비스 관리자, 공동 관리자 및 Azure 역할 할당을 검토하고 업데이트합니다. 자세한 내용은 [Azure 구독 관리자 추가 또는 변경](add-change-subscription-administrator.md) 및 [Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.
1. 다음을 비롯한 이 구독의 서비스와 연결된 자격 증명을 업데이트합니다.
   1. 구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서. 자세한 내용은 [Azure 용 관리 인증서 만들기 및 업로드](../../cloud-services/cloud-services-certs-create.md)
   1. 스토리지와 같은 서비스에 대한 액세스 키. 자세한 내용은 [Azure Storage 계정 정보](../../storage/common/storage-account-create.md)를 참조하세요.
   1. Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.
1. 파트너와 함께 작업하는 경우 이 구독의 파트너 ID를 업데이트하는 것이 좋습니다. [Azure Portal](https://portal.azure.com)에서 파트너 ID를 업데이트할 수 있습니다. 자세한 내용은 [Azure 계정에 파트너 ID 연결](link-partner-id.md)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

구독을 양도하는 데 문제가 있는 경우 다음 문제 해결 정보를 참고하세요.

### <a name="the-transfer-subscription-option-is-unavailable"></a>"구독 양도" 옵션을 사용할 수 없음

<a name="no-button"></a> 

현재 청구 계정에는 셀프 서비스 구독 양도를 사용할 수 없습니다. 현재는 Azure Portal에서 EA(기업계약) 계정의 구독 청구 소유권을 양도할 수 없습니다. 또한 Microsoft 담당자와 함께 만든 Microsoft 고객 계약 계정은 청구 소유권 양도를 지원하지 않습니다.

###  <a name="not-all-subscription-types-can-transfer"></a>일부 구독 유형을 양도할 수 없음

일부 구독 유형은 청구 소유권 양도를 지원하지 않습니다. 양도를 지원하는 구독 유형 목록을 보려면 [지원되는 구독 유형](../understand/subscription-transfer.md#supported-subscription-types)을 참조하세요.

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>구독 청구 소유권을 양도하려고 하면 액세스 거부 오류가 표시됨

Microsoft Azure 플랜 구독을 전송하려고 시도하는데 필요한 권한이 없는 경우 이 오류가 발생합니다. Microsoft Azure 플랜 구독을 양도하려면 구독 요금이 청구되는 청구서 섹션의 소유자 또는 기여자여야 합니다. 자세한 내용은 [청구서 섹션에 대한 구독 관리](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- 서비스 관리자, 공동 관리자 및 Azure 역할 할당을 검토하고 업데이트합니다. 자세한 내용은 [Azure 구독 관리자 추가 또는 변경](add-change-subscription-administrator.md) 및 [Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.