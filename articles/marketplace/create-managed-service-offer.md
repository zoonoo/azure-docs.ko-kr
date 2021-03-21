---
title: Microsoft 상업적 marketplace에서 관리 서비스 제품을 만드는 방법
description: Microsoft 파트너 센터에서 상용 마켓플레이스 프로그램을 사용 하 여 Azure Marketplace에 대 한 새 관리 서비스 제품을 만드는 방법을 알아봅니다.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97918274"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>상용 marketplace에 대 한 관리 서비스 제품을 만드는 방법

이 문서에서는 파트너 센터를 사용 하 여 Microsoft 상업적 marketplace에 대 한 관리 서비스 제품을 만드는 방법을 설명 합니다.

관리 서비스 제품을 게시 하려면 클라우드 플랫폼에서 골드 또는 실버 Microsoft 역량을 달성 해야 합니다. 아직 수행 하지 않은 경우 [상업용 marketplace에 대 한 관리 서비스 제안 계획](./plan-managed-service-offer.md)을 참조 하세요. 파트너 센터에서 제품을 만들 때 필요한 자산을 준비 하는 데 도움이 됩니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스**  >  **개요** 를 선택 합니다.
3. 개요 탭에서 **+ 새 제품**  >  **관리 서비스** 를 선택 합니다.

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="왼쪽 탐색 메뉴를 보여 줍니다.":::

4. **새 제품** 대화 상자에서 **제품 ID** 를 입력 합니다. 계정의 각 제품에 대한 고유 식별자입니다. 이 ID는 상업적 marketplace 목록 및 Azure Resource Manager 템플릿 (해당 하는 경우)의 URL에 표시 됩니다. 예를 들어이 상자에 테스트-1을 입력 하는 경우 제품 웹 주소는 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` 입니다.

    * 계정의 각 제품에는 고유한 제품 ID가 있어야 합니다.
    * 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다.
    * **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

5. **제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다. 온라인 매장에는 표시 되지 않으며 고객에 게 표시 되는 제품 이름과는 다릅니다.
6. 제품을 생성 하 고 계속 하려면 **만들기** 를 선택 합니다.

## <a name="configure-lead-management"></a>잠재 고객 관리 구성

고객이 컨설팅 서비스에 관심을 표시 하는 경우 고객 연락처 정보를 받을 수 있도록 CRM (고객 관계 관리) 시스템을 상용 marketplace 제품에 연결 합니다. 제품을 만들고 나 서 언제 든 지이 연결을 수정할 수 있습니다. 자세한 지침은 [상용 marketplace 제품의 고객 리드](./partner-center-portal/commercial-marketplace-get-customer-leads.md)를 참조 하세요.

파트너 센터에서 리드 관리를 구성 하려면:

1. 파트너 센터에서 **제품 설정** 탭으로 이동 합니다.
2. **고객 리드** 에서 **연결** 링크를 선택 합니다.
3. **연결 정보** 대화 상자의 목록에서 리드 대상을 선택 합니다.
4. 표시 되는 필드를 완료 합니다. 자세한 단계는 다음 문서를 참조 하세요.

    * [Azure 테이블로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Dynamics 365 고객 참여를 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (이전의 Dynamics CRM Online)
    * [제품을 구성 하 여 HTTPS 끝점으로 잠재 고객 보내기](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Marketo로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Salesforce로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. 제공 된 구성의 유효성을 검사 하려면 **유효성 검사 링크** 를 선택 합니다.
6. 연결 정보를 구성한 경우 **연결** 을 선택 합니다.
7. **초안 저장** 을 선택합니다.

파트너 센터에서 게시에 대 한 제품을 제출한 후에는 연결의 유효성을 검사 하 고 테스트 리드를 전송 합니다. 제품을 라이브 상태로 전환 하는 동안 미리 보기 환경에서 제품을 직접 구매 하 여 잠재 고객 연결을 테스트 합니다.

> [!TIP]
> 잠재 고객을 잃어버리는 경우가 없도록 잠재 고객 대상에 대한 연결을 업데이트된 상태로 유지해야 합니다.

## <a name="configure-offer-properties"></a>제품 속성 구성

파트너 센터의 제품에 대 한 속성 페이지에서 제품 및 법적 계약서에 적용 되는 범주를 정의 합니다. 이 정보를 통해 관리 되는 서비스가 온라인 스토어에 올바르게 표시 되 고 올바른 고객 집합에 제공 됩니다.

### <a name="select-a-category"></a>범주 선택

**범주** 에서 제품을 적절 한 상업적 marketplace 검색 영역으로 그룹화 하기 위해 하나 이상의 범주를 선택 합니다.

### <a name="provide-terms-and-conditions"></a>사용 약관 제공

**법률** 에서이 제품에 대 한 사용 약관을 제공 합니다. 고객은 제품을 사용 하기 전에 동의 해야 합니다. 사용 약관을 찾을 수 있는 URL을 제공할 수도 있습니다.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-step"></a>다음 단계

* [관리 서비스 제공 목록 구성](./create-managed-service-offer-listing.md)