---
title: 리드 관리 질문 및 문제 해결-Microsoft 파트너 센터
description: 파트너 센터에서 상업적 marketplace 리드를 구성할 때 발생 하는 일반적인 오류 및 질문에 대해 알아보세요.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: b88c5d7692efa64349f9dbb01b2d4645ec0eb366
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654199"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>일반적인 질문 및 리드 구성 문제 해결

이 문서에서는 상업적 marketplace 제품에 대 한 리드 관리에 대 한 몇 가지 일반적인 질문에 답변 합니다. 또한 파트너 센터의 CRM (고객 관계 관리) 시스템에 대 한 잠재 고객을 구성할 때 발생할 수 있는 오류를 해결 합니다.

## <a name="common-questions-about-lead-management"></a>잠재 고객 관리에 대한 일반적인 질문

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>잠재 고객 대상을 설정하는 데 필요한 도움은 어디에서 받을 수 있나요?

상업적 marketplace 제품에 CRM 시스템을 연결 하는 방법에 대 한 개요는 [상업적 marketplace 제품의 고객 리드](partner-center-portal/commercial-marketplace-get-customer-leads.md) 를 참조 하세요. 오류가 있는 경우 아래 문제 해결 지침을 검토 합니다. 추가 지원이 필요한 경우 [파트너 센터 도움말 및 지원을](https://aka.ms/marketplacepublishersupport)통해 지원 티켓을 제출 합니다. 그런 다음, **제품 만들기** > **제품 유형** > **잠재 고객 관리 구성**을 선택합니다.

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>상업용 Marketplace에서 제품을 게시하기 위해 잠재 고객 대상을 구성해야 하나요?

답변은 게시하는 제품의 유형에 따라 달라집니다. SaaS(Software as a Service) 및 Dynamics 365 Customer Engagement에서는 **연락처**를 사용하여 Dynamics 365 for Finance and Operations 제품, 모든 Dynamics 365 Business Central 제품 및 모든 컨설팅 서비스 제품을 나열합니다. 따라서 이러한 제품은 잠재 고객 대상에 연결되어야 합니다. 제품 유형이 나열되지 않은 경우에는 잠재 고객 대상에 대한 연결이 필요하지 않습니다. 비즈니스 기회를 놓치지 않도록 잠재 고객 대상을 구성하는 것이 좋습니다.

#### <a name="how-can-i-find-the-test-lead"></a>테스트 잠재 고객을 찾으려면 어떻게 해야 하나요?

잠재 고객 대상에서 `"MSFT_TEST"`를 검색합니다. 다음은 Microsoft의 샘플 테스트 책임자입니다. 테스트 리드의 형식은 리드 대상에 따라 다릅니다.

```
{
    "UserDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>라이브 제품이 있지만 잠재 고객이 보이지 않는 이유는 무엇인가요?

잠재 고객 대상에 대한 연결이 올바른지 확인합니다. Microsoft는 파트너 센터에서 제품에 대해 **게시**를 선택한 후에 테스트 잠재 고객을 보내 드립니다. 테스트 잠재 고객이 표시되면 연결이 올바른 것입니다. 미리 보기 단계에서 제품 미리 보기를 가져와 잠재 고객 연결을 테스트할 수도 있습니다. 상업용 Marketplace의 목록에서 **지금 가져오기**, **연락처** 또는 **평가판**을 선택합니다.

또한 올바른 데이터를 검색하고 있는지 확인합니다. 잠재 고객 데이터에 대 한 설명은 잠재 고객 [데이터 이해](partner-center-portal/commercial-marketplace-get-customer-leads.md) 를 참조 하세요.

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Azure Blob 스토리지를 잠재 고객 대상으로 구성했는데 잠재 고객이 표시되지 않는 이유는 무엇인가요?

Azure Blob 스토리지는 더 이상 잠재 고객 대상으로 지원되지 않으므로 제품에서 생성된 잠재 고객이 누락됩니다. 다른 [잠재 고객 대상 옵션](partner-center-portal/commercial-marketplace-get-customer-leads.md)으로 전환합니다. 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>상업용 Marketplace에서 메일을 받았으나 CRM에서 리드를 찾을 수 없는 이유는 무엇인가요?

최종 사용자의 메일 도메인 원본이 .edu일 수 있습니다. 개인 정보 보호를 위해 .edu 도메인의 개인 정보를 전달하지 않습니다. [파트너 센터 도움말 및 지원](https://aka.ms/marketplacepublishersupport)을 통해 지원 티켓을 제출합니다.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Azure 테이블을 잠재 고객 대상으로 구성했습니다. 잠재 고객을 보려면 어떻게 해야 하나요?

Azure Portal에서 Azure 테이블에 저장된 잠재 고객 데이터에 액세스할 수 있습니다. 또한 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 다운로드한 후 설치하여 Azure Storage 계정의 테이블 데이터를 무료로 볼 수 있습니다.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Azure 테이블을 잠재 고객 대상으로 구성했습니다. 새 상업용 Marketplace 잠재 고객이 전송될 때마다 알림 메시지를 받을 수 있나요?

예. [Azure 테이블을 사용하여 잠재 고객 관리 구성](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md)의 지침에 따라 잠재 고객이 Azure 테이블에 추가될 경우 메일을 전송하는 Microsoft 흐름을 설정합니다.

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Salesforce를 내 잠재 고객 대상으로 구성했으나 잠재 고객을 찾을 수 없는 이유는 무엇인가요?

선택 목록에서 웹-잠재 고객 양식이 필수 필드인지 확인합니다. 그런 경우 해당 필드를 비필수 텍스트 필드로 전환합니다.

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>잠재 고객 대상에 문제가 있으며 일부 잠재 고객이 누락되었습니다. 메일로 보내줄 수 있나요?

개인 정보 정책으로 인해 안전하지 않은 메일을 통해 잠재 고객 정보를 공유할 수 없습니다.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Azure 테이블을 잠재 고객 대상으로 구성했습니다. 비용은 얼마인가요?

잠재 고객 생성 데이터가 부족합니다. 거의 모든 게시자에서 1GB 미만입니다. 비용은 받은 잠재 고객 수에 따라 달라집니다. 예를 들어 1달에 1,000명의 잠재 고객이 수신되면 비용은 50센트입니다. 스토리지 가격 책정에 대한 자세한 내용은 [Azure Storage 개요 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

질문에 대한 답변을 얻지 못할 경우 [파트너 센터 도움말 및 지원](https://aka.ms/marketplacepublishersupport)을 통해 Microsoft 지원 서비스에 문의하세요. 그런 다음, **제품 만들기** > **제품 유형** > **잠재 고객 관리 구성**을 선택합니다.

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>새 잠재 고객이 수신되면 메일 알림이 수신됩니다. 다른 사람이 이러한 메일을 받도록 구성하려면 어떻게 해야 하나요?

파트너 센터에서 제품에 액세스하고 **제품 설정** 페이지 > **잠재 고객 관리** > **편집**으로 이동합니다. **연락처 메일** 필드에서 메일 주소를 업데이트합니다.

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> 리드 구성 오류 문제 해결

**리드를 Dynamics CRM에 저장할 수 없습니다. Dynamics CRM 계정 설정을 확인하세요. LastCRMError: Dynamics CRM(LastCRMException:** )에 로그인할 수 없습니다. 

> 인증 Microsoft 365 선택 했으면 사용자 계정 및 암호가 올바른지 확인 합니다. Azure Active Directory가 선택 된 경우 테 넌 트 ID, 응용 프로그램 ID 및 응용 프로그램 비밀 키가 Azure Active Directory에 설정 된 것과 일치 하는지 확인 합니다. [여기](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)에 나와 있는 지침을 따르세요. 계정 사용자 이름/암호가 유효한 경우 Dynamics 365 액세스 권한이 있는지와 라이선스가 할당되었는지 확인합니다(Azure Active Directory를 사용하는 경우 11-15단계, Office 사용자를 사용하는 경우 보안 설정). 

**리드를 Dynamics CRM에 저장할 수 없습니다. 사용자에게 리드 엔터티의 leadsourcecode 특성에 대한 만들기 권한이 없습니다.** 

> 애플리케이션/사용자에게 Microsoft Marketplace 리드 작성기에 대한 보안 역할이 없습니다. Azure Active Directory를 사용 하는 경우 11-15 단계 또는 [여기](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)에서 Office 사용자를 사용 하는 경우 보안 설정을 참조 하세요.

**AAD를 사용하여 리드를 Dynamics CRM에 저장할 수 없습니다. 예외:: 테넌트를 찾을 수 없습니다. 이 인스턴스는 테넌트에 대한 활성 가입이 없는 경우 발생할 수 있습니다.**  

> 리드 관리 섹션에 제공된 디렉터리 ID가 유효한 디렉터리가 아닙니다. [여기](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)에서 2 단계의 지침에 따라 디렉터리 Id를 가져오세요 (Azure Active Directory).

**리드를 Dynamics CRM에 저장할 수 없습니다. LastCRMError: SecLib::RetrievePrivilegeForUser 실패 - 사용자에게 할당된 역할이 없습니다.**  

> 해결 방법: Microsoft Marketplace 리드 작성기에 보안 역할을 할당합니다. [여기](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)에서 보안 설정 아래의 지침을 따르세요.

**AAD를 사용하여 리드를 Dynamics CRM에 저장할 수 없습니다. 예외:: 식별자가 포함된 애플리케이션이 디렉터리에 없습니다** 

> 리드 관리 섹션에 제공된 애플리케이션 ID가 유효한 디렉터리가 아닙니다. [여기](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)에서 8단계(Azure Active Directory 아래)의 지침에 따라 디렉터리 ID를 가져오세요. 

**AAD를 사용하여 리드를 Dynamics CRM에 저장할 수 없습니다. 예외:: 요청된 테넌트 식별자가 유효하지 않으며 유효한 외부 도메인 형식이 아닙니다** 

> 리드 관리 섹션에 제공된 디렉터리 ID가 유효한 디렉터리가 아닙니다. [여기](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)에서 2단계(Azure Active Directory 아래)의 지침에 따라 디렉터리 ID를 가져오세요. 

**AAD를 사용하여 리드를 Dynamics CRM에 저장할 수 없습니다. 예외:: 자격 증명의 유효성 검사 오류.: 잘못된 클라이언트 비밀을 제공합니다.** 

> 해결 방법: Azure Portal에 로그인하고, 애플리케이션 키가 파트너 센터의 키와 일치하는지 확인합니다. [여기](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)에서 10단계(Azure Active Directory 아래)의 지침에 따라 암호를 생성하세요. 

**리드를 Dynamics CRM에 저장할 수 없습니다. LastCRMError: 00:02:00 이후 응답을 기다리는 중 요청 채널의 시간이 초과되었습니다. 요청 호출에 전달된 제한 시간 값을 늘리거나 Binding의 SendTimeout 값을 늘리세요. 이 작업에 할당된 시간이 보다 긴 시간 제한의 일부일 수 있습니다.**  

> 해결 방법: 파트너 센터에 로그인 하 고 고객 리드 >> URL >> 제공 설치를 선택 하 고, 유효한 동적 CRM 인스턴스인지 확인 합니다.

