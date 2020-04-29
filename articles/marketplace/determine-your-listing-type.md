---
title: Microsoft 상업적 marketplace 상점에서 게시 옵션 결정
description: 이 문서에서는 Microsoft AppSource 및 Azure Marketplace에 앱을 게시 하는 방법을 이해 하려는 파트너를 위한 자격 조건 및 게시 요구 사항에 대해 설명 합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: e91ca0334276dbe9118eb0e40d042f598e8b3c0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415732"
---
# <a name="determine-your-publishing-option"></a>게시 옵션 확인

제품에 대해 선택 하는 게시 옵션은 자격 요구 사항 및 상업적 marketplace GTM 혜택에 직접적으로 관련이 있습니다. 더 중요 한 것은 게시 옵션 및 제안 유형을 선택 하는 것으로 사용자가 상업적 marketplace 제품을 조작 하는 방법을 정의 하는 것입니다.

제품을 구성 하려면 상업적 marketplace 상점에서 제품이 표시 되는 방법 및 위치를 제어 하는 게시 옵션, 제품 유형 및 구성, 작업 호출 호출 등의 주요 상업적 marketplace 개념을 이해 해야 합니다.

이 문서에서는 다음에 대해 알아봅니다.

- 솔루션에 대한 적절한 상점을 결정하는 방법
- 각 상점에서 사용할 수 있는 CTA(calls-to-action) 및 게시 옵션
- 각 게시 옵션에 대해 사용할 수 있는 제품 유형

## <a name="commercial-marketplace-publishing-options"></a>상업적 marketplace 게시 옵션

다음 표에서는 Microsoft AppSource 및 Azure Marketplace에 제공 되는 제품 유형에 대 한 게시 옵션을 보여 줍니다.

|   | **목록 (연락처)**  | **목록 (평가판)**  | **Free** | **BYOL** | **트**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Virtual Machine** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Azure 앱 (다중 VM)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **컨테이너 이미지** |  |  | Azure Marketplace | Azure Marketplace |   |
| **IoT Edge 모듈** |  |  | Azure Marketplace | Azure Marketplace |   |
| **관리 서비스** |  |  |  | Azure Marketplace |   |
| **컨설팅 서비스** | 두 상점 |  |  |  |   |
| **SaaS 앱** | 두 상점 | 두 상점 | 두 상점 |  | 상점 * |
| **Microsoft 365 앱** | AppSource | AppSource |  |  | AppSource * *  |
| **Dynamics 365 추가 기능** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

Microsoft AppSource의 SaaS 앱 제공 &#42;은 현재 신용 카드만 제공 합니다.

&#42;&#42; Microsoft 365 제품은 무료로 설치할 수 있으며, SaaS 제품을 통해 라이선스 서비스로 수익 화할 수 있습니다. 자세한 내용은 [Microsoft 상업적 marketplace를 통해 Office 365 추가 기능 수익 창출](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace)을 참조 하세요.

## <a name="selecting-a-storefront"></a>Storefront 선택

게시 옵션을 선택 하기 전에 상업적 marketplace 솔루션, 앱 및 서비스에 대 한 storefront 자격 요구 사항을 이해 하는 것이 중요 합니다. 각 storefront는 고유한 고객 요구 사항 및 대상 관련 대상을 제공 합니다. 제품 유형, 거래 기능 및 범주 또는 업계에서는 제품을 게시할 위치를 결정 합니다.

**Microsoft AppSource** 응용 프로그램은 Azure에서 기본 제공 되는 lob (기간 업무) 솔루션으로, Dynamics 365, Office 365, Power BI 또는 Power Apps에 기본 제공 됩니다. AppSource 컨설팅 서비스는 고객이 Dynamics 365 및 Power BI 사용을 시작하거나 가속화하도록 도와주는 전문 서비스 제품입니다.

**Azure Marketplace** 응용 프로그램은 기본 제공 또는 기본 제공 되는 Azure 용 기술 "빌딩 블록" 솔루션으로, IT 또는 개발자를 대상으로 합니다. Azure Marketplace 컨설팅 서비스는 고객이 Azure 사용을 시작하거나 가속화하도록 도와주는 전문 서비스 제품입니다.

>[!Note]
>목록 간 (SaaS 앱에만 해당): 목록 또는 평가판 기반 제품이 기술 및 비즈니스 사용자 대상에 대 한 조건을 충족 하는 경우 제품은 두 상점 모두에 나열 됩니다. 아래 게시 옵션에 대해 자세히 알아봅니다.

## <a name="choose-a-publishing-option"></a>게시 옵션 선택

사용 가능한 게시 옵션은 잠재 고객 참여를 제공 하는 동시에 잠재 고객의 공유 및 [상업적 marketplace 혜택](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits)에 대 한 액세스를 제공 합니다. 게시 옵션에 해당하는 CTA(calls-to-action)를 참고합니다.

| **게시 옵션**    | **설명**  |
| :------------------- | :-------------------|
| **목록** | 상업적 marketplace 사용자가 연락 전화를 통해 고객에 **게** 연결 하도록 요청할 수 있는 응용 프로그램 또는 서비스의 간단한 목록입니다. |
| **평가판** | 상업적 marketplace를 사용 하 여 검색 기능을 향상 하 고 솔루션의 평가판 환경을 자동으로 프로 비전 하 여, 구매 하기 전에 제한 된 시간에 무료로 사용자가 SaaS, IaaS 또는 Microsoft 앱 내 경험을 사용할 수 있도록 합니다. 평가판 게시 옵션에 사용 되는 동작에 대 한 호출은 **무료 평가판** 또는 **시험**사용 중 하나입니다. |
| **BYOL** | 상업적 marketplace를 사용 하 여 검색 기능을 개선 하 고 솔루션의 프로 비전을 자동화 하며 재무 트랜잭션을 별도로 완료 하세요. BYOL 제품 형식은 온-프레미스에서 클라우드로의 마이그레이션에 적합 합니다. 이제 동작에 대 한 호출을 **가져옵니다**.
| **트** | 시장 제품은 상업적 marketplace를 통해 판매 됩니다. Microsoft는 대금 청구 및 수집을 담당 합니다. 이제 동작에 대 한 호출을 **가져옵니다**.|

> [!Note]
> Transact 게시 옵션을 사용할 때는 제품 유형을 선택하고 제품을 만들기 전에 가격, 청구, 송장 처리 및 지급 고려 사항을 이해하는 것이 중요합니다. 자세히 알아보려면 [상업적 marketplace의 기능](./marketplace-commercial-transaction-capabilities-and-considerations.md)문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [게시 옵션을 결정](./publisher-guide-by-offer-type.md)했으면 제품을 제공하는 데 사용할 제품 유형을 선택할 준비가 된 것입니다.
- 제품의 선택 및 구성을 완료하려면 제품 유형 섹션별 게시 옵션에서 자격 요구 사항을 검토합니다.
- 솔루션이 제품 유형 및 구성에 매핑되는 방법에 대한 예제는 상점별 게시 패턴을 검토합니다.
