---
title: Microsoft 상업용 Marketplace에서 SaaS 제품을 만드는 방법
description: Microsoft 파트너 센터의 상업용 Marketplace 프로그램을 사용하여 Microsoft AppSource, Azure Marketplace 또는 CSP(클라우드 솔루션 공급자) 프로그램을 통해 등록 또는 판매할 새 SaaS(Software as a Service) 제품을 만드는 방법을 알아봅니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 74d30b7c42002c8f134520e0198774eba1519bcd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553841"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>상업용 Marketplace에서 SaaS 제품을 만드는 방법

상업용 Marketplace 게시자는 잠재적 고객이 SaaS 기반 기술 솔루션을 구매할 수 있도록 SaaS(Software as a Service) 제품을 만들 수 있습니다. 이 문서에서는 Microsoft 상업용 Marketplace를 위한 SaaS 제품을 만드는 프로세스에 대해 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

[상업용 Marketplace를 위한 SaaS 제품 계획](plan-saas-offer.md)을 아직 읽어 보지 않았다면 읽어 보세요. SaaS 앱에 대한 기술 요구 사항 및 제품을 만들 때 필요한 정보 및 자산을 설명합니다. 상업용 Marketplace에서 간단한 목록(**문의처** 목록 옵션)을 게시하려는 경우가 아니라면 SaaS 애플리케이션이 인증에 대한 기술 요구 사항을 충족해야 합니다.

> [!IMPORTANT]
> 별도의 개발/테스트(DEV) 제품 및 별도의 프로덕션(PROD) 제품을 만드는 것이 좋습니다. 이 문서에서는 PROD 제품을 만드는 방법을 설명합니다. DEV 제품을 만드는 방법에 대한 자세한 내용은 [개발 및 테스트 제품 만들기](create-saas-dev-test-offer.md)를 참조하세요.

## <a name="create-a-new-saas-offer"></a>새로운 SaaS 제품 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요** 를 선택합니다.
1. **개요** 탭에서 **+ 새 제품** > **Software as a Service** 를 선택합니다.

   :::image type="content" source="media/new-offer-saas.png" alt-text="왼쪽 탐색 메뉴와 새 제품 목록을 보여 주는 그림":::

1. **새 제품** 대화 상자에서 **제품 ID** 를 입력합니다. 이 ID는 상업용 Marketplace 목록의 URL 및 Azure Resource Manager 템플릿에 표시됩니다(해당하는 경우). 예를 들어 이 입력란에 **test-offer-1** 을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
   + 계정의 각 제품에는 고유한 제품 ID가 있어야 합니다.
   + 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다.
   + **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

1. **제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

   + 이 이름은 상업용 Marketplace에 표시되지 않으며 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.
   + **만들기** 를 선택한 후에는 제품 별칭을 변경할 수 없습니다.
1. 제품을 생성하고 계속하려면 **만들기** 를 선택합니다.

## <a name="configure-your-saas-offer-setup-details"></a>SaaS 제품 설정 세부 정보 구성

**제품 설정** 탭의 **설정 세부 정보** 에서 Microsoft를 통해 제품을 판매할지, 아니면 트랜잭션을 독립적으로 관리할지 선택합니다. Microsoft를 통해 판매되는 제품을 _거래 가능한 제품_ 이라고 합니다. 즉, Microsoft에서 게시자를 대신해 소프트웨어 라이선스에 대한 거래를 진행합니다. 이러한 옵션에 대한 자세한 내용은 [등록 옵션](plan-saas-offer.md#listing-options) 및 [게시 옵션 확인](determine-your-listing-type.md)을 참조하세요.

1. Microsoft를 통해 판매하고 Microsoft에서 게시자 대신 거래를 진행하도록 하려면 **예** 를 선택합니다. [시험 사용 지원](#enable-a-test-drive-optional)을 진행합니다.

1. 상업용 Marketplace를 통해 제품을 등록하고 거래를 독립적으로 처리하려면 **아니요** 를 선택하고 다음 중 하나를 수행합니다.
   + 제품에 대한 무료 구독을 제공하려면 **지금 받기(무료)** 를 선택합니다. 그런 다음, 표시되는 **제품 URL** 입력란에 *http* 또는 *https* 로 시작하는 URL을 입력합니다. 고객은 여기서 [Azure AD(Azure Active Directory)를 사용하여 원클릭 인증](azure-ad-saas.md)을 통해 평가판을 받을 수 있습니다. 예들 들어 `https://contoso.com/saas-app`입니다.
   + 30일 평가판을 제공하려면 **평가판** 을 선택한 다음, 표시되는 **평가판 URL** 상자에 *http* 또는 *https* 로 시작하는 URL을 입력합니다. 고객은 여기서 [Azure AD(Azure Active Directory)를 사용하여 원클릭 인증](azure-ad-saas.md)을 통해 평가판에 액세스할 수 있습니다. 예들 들어 `https://contoso.com/trial/saas-app`입니다.
   + 잠재 고객이 게시자에게 연락해서 제품을 구매할 수 있도록 하려면 **문의처** 를 선택합니다.

## <a name="enable-a-test-drive-optional"></a>시험 사용 지원(선택 사항)

시험 사용은 잠재 고객에게 정해진 시간 동안 미리 구성된 환경에 대한 액세스 권한을 부여하여 제품을 소개하는 좋은 방법입니다. 시험 사용을 제공하면 전환율이 증가하고 충분한 자격을 갖춘 잠재 고객이 생성됩니다. 시험 사용에 대한 자세한 내용은 [시험 사용이란?](./what-is-test-drive.md)을 참조하세요.

> [!TIP]
> 시험 사용은 평가판과 다릅니다. 시험 사용, 평가판 또는 둘 다를 제공할 수 있습니다. 둘 다 정해진 기간 동안 고객에게 솔루션을 제공합니다. 하지만 시험 사용에는 실제 구현 시나리오에서 입증된 제품의 주요 기능과 이점에 대한 실습 중심의 자기 주도형 체험도 포함됩니다.

**시험 사용을 지원** 하려면 다음을 수행합니다.
1.  **시험 사용** 에서 **시험 사용 지원** 확인란을 선택합니다.
1.  표시되는 목록에서 시험 사용 유형을 선택합니다.

## <a name="configure-lead-management"></a>잠재 고객 관리 구성

고객이 관심을 표시하거나 제품을 배포할 때 고객 연락처 정보를 받을 수 있도록 CRM(고객 관계 관리) 시스템을 상업용 Marketplace 제품에 연결합니다. 제품 생성 도중이나 생성 후에 언제든지 이 연결을 수정할 수 있습니다.

> [!NOTE]
> Microsoft를 통해 제품을 판매하고 있거나 **문의처** 목록 옵션을 선택한 경우에는 잠재 고객 관리를 구성해야 합니다. 자세한 참고 자료는 [상업용 Marketplace 제품의 잠재 고객](partner-center-portal/commercial-marketplace-get-customer-leads.md)을 참조하세요.

### <a name="configure-the-connection-details-in-partner-center"></a>파트너 센터에서 연결 정보 구성

1.  **잠재 고객** 아래에서 **연결** 링크를 선택합니다.
1. **연결 정보** 대화 상자의 목록에서 잠재 고객 대상을 선택합니다.
1. 표시되는 필드를 완성합니다. 자세한 단계는 다음 문서를 참조하세요.

   - [Azure 테이블로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Dynamics 365 Customer Engagement로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)(이전 명칭: Dynamics CRM Online)
   - [HTTPS 엔드포인트로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Marketo로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Salesforce로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 제공된 구성의 유효성을 검사하려면 **유효성 검사** 링크를 선택합니다.
1. 대화 상자를 닫으려면 **확인** 을 선택합니다.

## <a name="configure-microsoft-365-app-integration"></a>Microsoft 365 앱 통합 구성

SaaS 제품 및 관련 Microsoft 365 앱 사용량을 연결하여 [통합 검색 및 제공](./plan-SaaS-offer.md)을 강화할 수 있습니다.

### <a name="integrate-with-microsoft-api"></a>Microsoft API와 통합

1. SaaS 제품이 Microsoft Graph API와 통합되지 않는 경우 **아니요** 를 선택합니다. 게시된 Microsoft 365 앱 사용량 클라이언트 연결을 진행합니다.  

1. SaaS 제품이 Microsoft Graph API와 통합되는 경우 **예** 를 선택한 후 자신이 만들고 등록한 Azure Active Directory 앱 ID를 제공하여 Microsoft Graph API와 통합합니다. 

### <a name="link-published-microsoft-365-app-consumption-clients"></a>게시된 Microsoft 365 앱 사용량 클라이언트 연결

1. SaaS 제품과 연동되는 Office 추가 기능, Teams 앱 또는 SharePoint Framework 솔루션을 게시하지 않은 경우 **아니요** 를 선택합니다.

1. SaaS 제품과 연동되는 Office 추가 기능, Teams 앱 또는 SharePoint Framework 솔루션을 게시한 경우 **예** 를 선택한 다음, **+다른 AppSource 링크 추가** 를 선택하여 새 링크를 추가합니다.  

1. 유효한 AppSource 링크를 제공하세요.

1. **+다른 AppSource 링크 추가** 를 선택하고 유효한 AppSource 링크를 제공하여 모든 링크를 계속 추가합니다.  

1. 연결된 제품이 SaaS 제품의 목록 페이지에 표시되는 순서는 Rank 값으로 표시되며, = 아이콘을 선택하고 길게 누른 후 목록에서 위아래로 이동하면 변경할 수 있습니다. 

1. 제품 행에서 **삭제** 를 선택하면 연결된 제품을 삭제할 수 있습니다.  


> [!IMPORTANT]
> 연결된 제품의 판매를 중지해도 SaaS 제품에 대한 연결이 자동으로 해제되지는 않으며, 연결된 제품 목록에서 해당 제품을 삭제하고 SaaS 제품을 다시 제출해야 합니다.  

 

## <a name="next-steps"></a>다음 단계

- [SaaS 제품 속성을 구성하는 방법](create-new-saas-offer-properties.md)