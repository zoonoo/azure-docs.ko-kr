---
title: 상업적 marketplace의 Azure Active Directory 및 불가능 SaaS 제품
description: Microsoft 상업적 marketplace에서 불가능 SaaS 제품 Azure Active Directory 작동 하는 방법에 대해 알아봅니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 5a09105dac89f3dc241140f16f3d4be72cc97493
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89483629"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>상업적 marketplace의 Azure AD 및 불가능 SaaS 제품

Microsoft 클라우드 기반 id 및 액세스 관리 서비스인 Azure AD ( [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) )를 사용 하면 사용자가 로그인 하 여 내부 및 외부 리소스에 액세스할 수 있습니다. Microsoft 상업적 marketplace에서 Azure AD는 게시자, 구매자 및 사용자를 포함 하 여 모든 사용자에 게 더 쉽고 안전 하 게 불가능 SaaS를 제공 합니다. Azure AD를 사용 하면 게시자가 SaaS (software as a service) 앱에 사용자를 프로 비전 하는 것을 자동화할 수 있으며 구매자 자체는 이러한 프로 비전 된 사용자를 관리할 수 있습니다. 

또한 [azure ad Single Sign-On](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (SSO)는 사용자가 azure ad에서 앱에 로그인 할 때 보안 및 편리 함을 제공 합니다. 또한 더 빠른 참여 및 최적화 된 환경을 통해 게시자의 SaaS 앱과의 첫 번째 상호 작용에서 구매자 및 사용자 자신감을 영감 수 있습니다. 이를 통해 가시성을 빌드하고 비즈니스를 반복 하는 것이 좋습니다.

이 문서의 지침에 따라 상업적 marketplace에서 SaaS 제품을 인증 하는 데 도움을 얻을 수 있습니다. 인증에 대 한 자세한 내용은 [SaaS와 관련](https://aka.ms/commercial-marketplace-certification-policies#1000-software-as-a-service-saas)된 정보를 포함 하 여 자세한 [상용 marketplace 인증 정책](https://aka.ms/commercial-marketplace-certification-policies#100-general)을 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

파트너 센터에서 [SaaS 제품을 만들](./partner-center-portal/create-new-saas-offer.md) 때 제품 목록에 표시 되는 특정 목록 옵션 집합에서 선택 합니다. 선택한 항목은 상업적 marketplace에서 제품이 트랜잭션 되는 방법을 결정 합니다. Microsoft를 통해 판매 되는 제품을 불가능 제품 이라고 합니다. 모든 불가능 제품을 대신 하 여 고객에 게 요금을 청구 합니다. Microsoft를 통해 판매 하도록 선택 하 고 사용자를 대신 하 여 트랜잭션을 호스트 하는 경우 ( **예** 옵션) 불가능 제품을 만들도록 선택 하 고이 문서는 사용자를 위한 것입니다. 전체적으로 읽는 것이 좋습니다.

상업적 marketplace를 통해 제품을 나열 하 고 트랜잭션을 독립적으로 (옵션 **없음** ) 선택 하는 경우 잠재 고객이 귀하의 제품에 액세스 하는 방법에 대 한 세 가지 옵션을 사용할 수 있습니다. (무료), 무료 평가판 및 담당자에 게 문의 하세요. **지금 받기 (무료)** 또는 **무료 평가판**을 선택 하는 경우에는이 문서를 사용할 수 없습니다. 대신, 자세한 내용은 [상용 marketplace에서 무료 또는 평가판 SaaS 제품에 대 한 방문 페이지 빌드](./azure-ad-free-or-trial-landing-page.md) 를 참조 하세요. **연락처**를 선택 하는 경우 직접 게시자 책임은 없습니다. 파트너 센터에서 제품 만들기를 계속 합니다.

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Azure AD가 SaaS 제품에 대해 상업적 marketplace에서 작동 하는 방식

Azure AD는 상업적 marketplace 솔루션의 원활한 구매, 처리 및 관리를 가능 하 게 합니다. 그림 1에서는 게시자, 구매자 및 사용자가 구독을 구매 하 고 활성화 하는 방법을 보여 줍니다. 또한 고객이 상용 marketplace에서 받은 SaaS 응용 프로그램을 사용 하 고 관리 하는 방법을 보여 줍니다. 이 그림의 목적상 구매자는 상업적 marketplace에서 구매를 시작 하는 SaaS 응용 프로그램 사용자입니다.

그림 1에 표시 된 것 처럼 구매자가 제품을 선택 하면 구매, 구독 및 사용자 관리를 포함 하는 워크플로 체인을 시작 합니다. Microsoft에서 핵심 지점에 대 한 지원을 제공 하는 경우이 체인 내에서 게시자는 특정 요구 사항을 담당 하 게 됩니다.

***그림 1: 상업적 marketplace에서 SaaS 제품에 Azure AD 사용***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="구매 관리, 구독 관리 및 선택적 사용자 관리 프로세스 단계를 보여 줍니다.":::

다음 섹션에서는 각 프로세스 단계에 대 한 요구 사항에 대 한 세부 정보를 제공 합니다.

## <a name="process-steps-for-purchase-management"></a>구매 관리를 위한 프로세스 단계

다음 그림에서는 구매 관리를 위한 4 가지 프로세스 단계를 보여 줍니다.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="구매 관리, 구독 관리 및 선택적 사용자 관리 프로세스 단계를 보여 줍니다.":::

이 표에서는 구매 관리 프로세스 단계에 대 한 세부 정보를 제공 합니다.

| 처리 단계 | 게시자 동작 | 게시자에 대해 권장 또는 필수 |
| ------------ | ------------- | ------------- |
| 1. 구매자는 Azure ID id를 사용 하 여 상업적 marketplace에 로그인 하 고 SaaS 제안을 선택 합니다. | 게시자 작업이 필요 하지 않습니다. | 적용할 수 없음 |
| 2. 구매 후에 구매자는 Azure Marketplace에서 **계정 구성** 을 선택 하거나 appsource에서 **지금 구성** 합니다 .이는 구매자를이 제안의 게시자의 방문 페이지로 안내 합니다. 구매자는 Azure AD SSO를 사용 하 여 게시자의 SaaS 응용 프로그램에 로그인 할 수 있어야 하며, Azure AD 관리자 승인이 필요 하지 않은 최소한의 동의를 요청 해야 합니다. | Azure AD 또는 Microsoft 계정 (MSA) id를 사용 하 여 사용자를 받고 필요한 추가 프로 비전 또는 설치를 용이 하 게 하는 제품의 [방문 페이지](azure-ad-transactable-saas-landing-page.md) 를 디자인 합니다. | 필수 |
| 3. 게시자가 SaaS 처리 API에서 구매 정보를 요청 합니다. | 방문 페이지의 응용 프로그램 ID에서 생성 된 [액세스 토큰](./partner-center-portal/pc-saas-registration.md) 을 사용 하 여 [확인 끝점을 호출](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) 하 여 구매에 대 한 세부 정보를 검색 합니다. | 필수 |
| 4. Azure AD 및 Microsoft Graph API를 통해 게시자는 게시자의 SaaS 응용 프로그램에서 구매자를 프로 비전 하는 데 필요한 회사 및 사용자 세부 정보를 수집 합니다.  | Azure AD 사용자 토큰을 분해 하 여 이름 및 전자 메일을 찾거나, [MICROSOFT GRAPH API를 호출](https://docs.microsoft.com/graph/use-the-api) 하 고, 위임 된 사용 권한을 사용 하 여 로그인 한 사용자에 대 한 [정보를 검색](https://docs.microsoft.com/graph/api/user-get) 합니다. | 필수 |
||||

## <a name="process-steps-for-subscription-management"></a>구독 관리를 위한 프로세스 단계

다음 그림에서는 구독 관리에 대 한 두 가지 프로세스 단계를 보여 줍니다.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="구매 관리, 구독 관리 및 선택적 사용자 관리 프로세스 단계를 보여 줍니다.":::

다음 표에서는 구독 관리 프로세스 단계에 대 한 세부 정보를 설명 합니다.

| 처리 단계 | 게시자 동작 | 게시자에 대해 권장 또는 필수 |
| ------------ | ------------- | ------------- |
| 5. 게시자는 SaaS 처리 API를 통해 SaaS 응용 프로그램에 대 한 구독을 관리 합니다. | [SaaS 처리 api](./partner-center-portal/pc-saas-fulfillment-api-v2.md)를 통해 구독 변경 및 기타 관리 작업을 처리 합니다.<br><br>이 단계에서는 3 단계 프로세스에 설명 된 액세스 토큰이 필요 합니다. | 필수 |
| 6. 요금제 가격 책정을 사용 하는 경우 게시자는 사용량 이벤트를 계량 서비스 API로 내보냅니다. | SaaS 앱이 사용량 기반 청구 기능을 사용 하는 경우 [Marketplace 계량 서비스 api](./partner-center-portal/marketplace-metering-service-apis.md)를 통해 사용 알림을 만듭니다.<br><br>이 단계에서는 3 단계에서 설명한 대로 액세스 토큰이 필요 합니다. | 계량에 필요 |
||||

## <a name="process-steps-for-user-management"></a>사용자 관리를 위한 프로세스 단계

이 그림에서는 사용자 관리를 위한 세 가지 프로세스 단계를 보여 줍니다.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="구매 관리, 구독 관리 및 선택적 사용자 관리 프로세스 단계를 보여 줍니다.":::

프로세스 7 ~ 9 단계는 선택적 사용자 관리 프로세스 단계입니다. Azure AD Single Sign-On (SSO)를 지 원하는 게시자에 게 추가 혜택을 제공 합니다. 다음 표에서는 사용자 관리 프로세스 단계에 대 한 세부 정보를 설명 합니다.

| 처리 단계 | 게시자 동작 | 게시자에 대해 권장 또는 필수 |
| ------------ | ------------- | ------------- |
| 7. 구매자의 회사에서 azure AD 관리자는 Azure AD를 통해 사용자 및 그룹에 대 한 액세스를 선택적으로 관리할 수 있습니다. | 사용자에 대해 Azure AD SSO를 설정 하는 경우 (9 단계) 게시자 작업이 필요 하지 않습니다. | 적용할 수 없음 |
| 8. azure ad 프로 비전 서비스는 Azure AD와 게시자의 SaaS 응용 프로그램 간에 변경 사항을 전달 합니다. | 사용자가 추가 및 제거 될 때 Azure AD에서 업데이트를 수신 하도록 [SCIM 끝점을 구현](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) 합니다. | 권장 |
| 9. 앱이 permissioned 되 고 프로 비전 되 면 구매자 회사의 사용자가 Azure AD SSO를 사용 하 여 게시자의 SaaS 응용 프로그램에 로그인 할 수 있습니다. | [AZURE AD SSO를 사용](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) 하 여 사용자가 한 계정으로 게시자의 SaaS 응용 프로그램에 한 번 로그인 할 수 있도록 합니다. | 권장 |
||||

## <a name="next-steps"></a>다음 단계

- [상업적 marketplace에서 불가능 SaaS 제품에 대 한 방문 페이지 빌드](azure-ad-transactable-saas-landing-page.md)
- [상용 marketplace에서 무료 또는 평가판 SaaS 제품에 대 한 방문 페이지 빌드](azure-ad-free-or-trial-landing-page.md)
- [상업적 marketplace에서 SaaS 제품을 만드는 방법](create-new-saas-offer.md)
