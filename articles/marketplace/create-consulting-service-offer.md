---
title: Microsoft 상업용 마켓플레이스에서 컨설팅 서비스 제품을 만드는 방법
description: Microsoft 파트너 센터에서 상업용 마켓플레이스 프로그램을 사용하여 Microsoft AppSource 또는 Azure Marketplace에 대한 새 컨설팅 서비스 제품을 만드는 방법에 대해 알아봅니다.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754452"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>상업용 마켓플레이스에서 컨설팅 서비스 제품을 만드는 방법

이 문서에서는 파트너 센터를 사용하여 Microsoft 상업용 마켓플레이스에 대한 컨설팅 서비스 제품을 만드는 방법을 설명합니다. 

## <a name="before-you-begin"></a>시작하기 전에

컨설팅 서비스 제품을 게시하려면 특정 자격 요구 사항을 충족하여 현장에서 전문성을 발휘할 수 있음을 보여 줘야 합니다. [상업용 마켓플레이스를 위한 컨설팅 서비스 제품 계획](./plan-consulting-service-offer.md)을 아직 읽어 보지 않았다면 읽어 보세요. 여기서는 컨설팅 서비스에 대한 필수 조건과 파트너 센터를 사용하여 제품을 만들 때 필요한 자산을 설명합니다.

## <a name="create-a-new-consulting-service-offer"></a>새 컨설팅 서비스 제품 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2.  왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요** 를 선택합니다.
3.  개요 탭에서 **+ 새 제품** > **컨설팅 서비스** 를 선택합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-consulting-service.png)

4. **새 제품** 대화 상자에서 **제품 ID** 를 입력합니다. 이 ID는 상업용 마켓플레이스 목록의 URL에 표시됩니다. 예를 들어 이 입력란에 test-offer-1을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.

    * 계정의 각 제품에는 고유한 제품 ID가 있어야 합니다.
    * 소문자와 숫자만 사용할 수 있습니다. 제품 ID는 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다.
    * **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

5. **제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다. 이 이름은 온라인 스토어에는 표시되지 않으며 고객에게 표시되는 제품 이름과는 다릅니다.
6. 제품을 생성하고 계속하려면 **만들기** 를 선택합니다.

## <a name="configure-lead-management"></a>잠재 고객 관리 구성

고객이 컨설팅 서비스에 관심을 표시할 때 고객 연락처 정보를 받을 수 있도록 CRM(고객 관계 관리) 시스템을 상업용 마켓플레이스 제품에 연결합니다. 제품 생성 도중이나 생성 후에 언제든지 이 연결을 수정할 수 있습니다. 자세한 내용은 [상업용 마켓플레이스 제품의 잠재 고객](./partner-center-portal/commercial-marketplace-get-customer-leads.md)을 참조하세요.

파트너 센터에서 리드 관리를 구성하려면:

1.  파트너 센터에서 **제품 설정** 탭으로 이동합니다.
2.  **잠재 고객** 아래에서 **연결** 링크를 선택합니다.
3.  **연결 정보** 대화 상자의 목록에서 잠재 고객 대상을 선택합니다.
4.  표시되는 필드를 완성합니다. 자세한 단계는 다음 문서를 참조하세요.

    * [Azure 테이블로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Dynamics 365 Customer Engagement로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)(이전 명칭: Dynamics CRM Online)
    * [HTTPS 엔드포인트로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Marketo로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Salesforce로 잠재 고객을 보내도록 제품 구성](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  제공된 구성의 유효성을 검사하려면 **유효성 검사** 링크를 선택합니다.
6.  연결 세부 정보를 구성한 경우 **연결** 을 선택합니다.
7.  **초안 저장** 을 선택합니다.

파트너 센터에서 게시할 제품을 제출하면 연결의 유효성이 검사되고 테스트 잠재 고객을 보낼 수 있습니다. 제품을 라이브로 전환하기 전에 미리 볼 때 미리 보기 환경에서 직접 제품을 구매하여 잠재 고객 연결을 테스트합니다.

> [!TIP]
> 잠재 고객을 잃어버리는 경우가 없도록 잠재 고객 대상에 대한 연결을 업데이트된 상태로 유지해야 합니다.

## <a name="next-steps"></a>다음 단계

* [컨설팅 서비스 제품 속성을 구성하는 방법](./create-consulting-service-offer-properties.md)