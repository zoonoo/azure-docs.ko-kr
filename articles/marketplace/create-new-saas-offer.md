---
title: Microsoft 상업적 marketplace에서 SaaS 제품을 만드는 방법
description: Microsoft 파트너 센터의 상용 Marketplace 포털을 사용 하 여 Microsoft AppSource, Azure Marketplace 또는 CSP (클라우드 솔루션 공급자) 프로그램을 통해 나열 하거나 판매 하기 위해 새 SaaS (software as a service) 제품을 만드는 방법에 대해 알아봅니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: f689993ce56a1125a1d1de8f65ce05d01f776ea9
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130071"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>상업적 marketplace에서 SaaS 제품을 만드는 방법

상업적 마켓플레이스 게시자는 잠재적 고객이 SaaS 기반 기술 솔루션을 구매할 수 있도록 SaaS (software as a service) 제품을 만들 수 있습니다. 이 문서에서는 Microsoft 상업적 marketplace에 대 한 SaaS 제품을 만드는 프로세스에 대해 설명 합니다.

## <a name="before-you-begin"></a>시작하기 전에

아직 수행 하지 않은 경우 [상업용 marketplace에 대 한 SaaS 제안 계획](plan-saas-offer.md)을 읽어 보세요. SaaS 앱에 대 한 기술 요구 사항 및 제품을 만들 때 필요한 정보 및 자산을 설명 합니다. 상용 marketplace에서 간단한 목록 ( **연락처** 표시 옵션)을 게시 하려는 경우가 아니면 SaaS 응용 프로그램은 인증에 대 한 기술 요구 사항을 충족 해야 합니다.

## <a name="create-a-new-saas-offer"></a>새로운 SaaS 제품 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스**  >  **개요** 를 선택 합니다.
1. **개요** 탭에서 **+ 새로 만들기 제품**  >  **소프트웨어 as a Service** 를 선택 합니다.

   :::image type="content" source="media/new-offer-saas.png" alt-text="왼쪽 탐색 메뉴와 새 제품 목록에 대해 설명 합니다.":::

1. **새 제품** 대화 상자에서 **제품 ID** 를 입력 합니다. 이 ID는 상업적 marketplace 목록 및 Azure Resource Manager 템플릿 (해당 하는 경우)의 URL에 표시 됩니다. 예를 들어이 상자에 **테스트-1** 을 입력 하는 경우 제품 웹 주소는 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` 입니다.
   + 계정의 각 제품에는 고유한 제품 ID가 있어야 합니다.
   + 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다.
   + **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

1. **제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

   + 이 이름은 상용 marketplace에서 볼 수 없으며, 제품 이름 및 고객에 게 표시 되는 다른 값과 다릅니다.
   + **만들기** 를 선택한 후에는 제품 별칭을 변경할 수 없습니다.
1. 제품을 생성 하 고 계속 하려면 **만들기** 를 선택 합니다.

## <a name="configure-your-saas-offer-setup-details"></a>SaaS 제품 설정 세부 정보 구성

**제품 설정** 탭의 **설치 세부 정보** 에서 Microsoft를 통해 제품을 판매할 지 아니면 트랜잭션을 독립적으로 관리할 지를 선택 합니다. Microsoft를 통해 판매 되는 제품을 _불가능 제품_ 이라고 합니다. 즉, microsoft는 게시자를 대신해 소프트웨어 라이선스에 대 한 비용 교환을 용이 하 게 합니다. 이러한 옵션에 대 한 자세한 내용은 [옵션 나열](plan-saas-offer.md#listing-options) 및 [게시 옵션 결정](determine-your-listing-type.md)을 참조 하세요.

1. Microsoft를 통해 판매 하 고 사용자의 트랜잭션을 용이 하 게 하려면 **예** 를 선택 합니다. [테스트 드라이브](#enable-a-test-drive-optional)를 계속 사용 하도록 설정 합니다.

1. 상업적 marketplace를 통해 제품을 나열 하 고 트랜잭션을 독립적으로 나열 하려면 **아니요** 를 선택 하 고 다음 중 하나를 수행 합니다.
   + 제안에 대 한 무료 구독을 제공 하려면 **지금 받기 (무료)** 를 선택 합니다. 그런 다음 표시 되는 **제품 url** 상자에서 고객이 [AZURE ACTIVE DIRECTORY (Azure AD)를 사용 하 여 한 번의 클릭으로 인증](azure-ad-saas.md)을 통해 평가판을 얻을 수 있는 URL ( *http* 또는 *https* 로 시작)을 입력 합니다. 예: `https://contoso.com/saas-app`.
   + 30 일 무료 평가판을 제공 하려면 **무료 평가판** 을 선택한 다음 표시 되는 **평가판 url** 상자에서 고객이 [Azure Active Directory (Azure AD)를 사용 하 여 한 번의 클릭으로 인증](azure-ad-saas.md)을 통해 무료 평가판에 액세스할 수 있는 URL ( *http* 또는 *https* 로 시작)을 입력 합니다. 예: `https://contoso.com/trial/saas-app`.
   + 잠재 고객이 제품을 구매할 수 있도록 하려면 **연락처** 를 선택 합니다.

### <a name="enable-a-test-drive-optional"></a>테스트 드라이브 사용 (선택 사항)

시험 사용은 고정 된 시간 동안 미리 구성 된 환경에 대 한 액세스 권한을 부여 하 여 잠재 고객에 게 제품을 소개 하는 좋은 방법입니다. 테스트 드라이브를 제공 하면 변환 비율이 증가 하 고 우량으로 선별 된 잠재 고객을 생성 합니다. 테스트 드라이브에 대 한 자세한 내용은 [test drive 란?](./what-is-test-drive.md)을 참조 하세요.

> [!TIP]
> 시험 사용은 평가판과 다릅니다. 시험 사용, 평가판 또는 둘 다를 제공할 수 있습니다. 둘 다 고객에 게 고정 기간에 대 한 솔루션을 제공 합니다. 그러나 테스트 드라이브에는 실제 구현 시나리오에서 설명 하는 제품의 주요 기능 및 이점에 대 한 실습 자체 둘러보기도 포함 되어 있습니다.

**테스트 드라이브를 사용 하도록 설정 하려면**
1.  **시험** **사용에서 테스트 드라이브 사용** 확인란을 선택 합니다.
1.  표시 되는 목록에서 테스트 드라이브 유형을 선택 합니다.

### <a name="configure-lead-management"></a>잠재 고객 관리 구성

고객이 관심을 표시 하거나 제품을 배포할 때 고객 연락처 정보를 받을 수 있도록 CRM (고객 관계 관리) 시스템을 상용 marketplace 제품에 연결 합니다. 제품을 만들고 나 서 언제 든 지이 연결을 수정할 수 있습니다.

> [!NOTE]
> Microsoft를 통해 제품을 판매 하 고 있거나 연락처 목록 옵션을 선택한 경우 **에** 는 리드 관리를 구성 해야 합니다. 자세한 지침은 [상용 marketplace 제품의 고객 리드](partner-center-portal/commercial-marketplace-get-customer-leads.md)를 참조 하세요.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>파트너 센터에서 연결 정보를 구성 하려면

1.  **고객 리드** 에서 **연결** 링크를 선택 합니다.
1. **연결 정보** 대화 상자의 목록에서 리드 대상을 선택 합니다.
1. 표시 되는 필드를 완료 합니다. 자세한 단계는 다음 문서를 참조 하세요.

   - [Azure 테이블로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Dynamics 365 고객 참여를 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (이전의 Dynamics CRM Online)
   - [제품을 구성 하 여 HTTPS 끝점으로 잠재 고객 보내기](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Marketo로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Salesforce로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 제공 된 구성의 유효성을 검사 하려면 **유효성 검사** 링크를 선택 합니다.
1. 대화 상자를 닫으려면 **확인** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [SaaS 제품 속성을 구성 하는 방법](create-new-saas-offer-properties.md)