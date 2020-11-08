---
title: Azure 응용 프로그램 제품에 대 한 계획을 만드는 방법
description: 파트너 센터에서 Azure 응용 프로그램 제품에 대 한 계획을 만드는 방법에 대해 알아봅니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: fd5ee9085cf716128c5b3ae073f963c76d2bd17a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370265"
---
# <a name="how-to-create-plans-for-your-azure-application-offer"></a>Azure 응용 프로그램 제품에 대 한 계획을 만드는 방법

Microsoft 상업적 marketplace를 통해 판매 되는 제품에는 상업적 marketplace에서 제품을 나열 하기 위한 계획이 하나 이상 있어야 합니다. 동일한 제품 내에서 다양 한 옵션을 사용 하 여 다양 한 계획을 만들 수 있습니다. 이러한 계획 (Sku 라고도 함)은 계획 유형 ( _솔루션 템플릿_ 또는 _관리 되는 응용 프로그램_ ), 수익 화 또는 대상의 측면에서 다를 수 있습니다. 요금제에 대 한 일반적인 지침은 [상용 marketplace 제품에 대 한 계획 및 가격 책정](plans-pricing.md)을 참조 하세요.

## <a name="create-a-plan"></a>계획 만들기

1. **계획 개요** 탭의 맨 위 근처에서 **+ 새 계획 만들기** 를 선택 합니다.
1. 표시 되는 대화 상자에서 **계획 id** 상자에 고유한 계획 id를 입력 합니다. 이 ID는 고객에게 제품 URL로 표시됩니다. 최대 50의 소문자 영숫자, 대시 또는 밑줄을 사용 합니다. **만들기** 를 선택한 후에는 계획 ID를 수정할 수 없습니다.
1. **계획 이름** 상자에이 계획에 대 한 고유한 이름을 입력 합니다. 제품 내에서 선택할 계획을 결정할 때 고객에 게이 이름이 표시 됩니다. 최대 50 문자를 사용 합니다.
1. **만들기** 를 선택합니다.

## <a name="define-the-plan-setup"></a>계획 설정 정의

**계획 설정** 탭에서는 계획 유형을 설정 하 고, 다른 계획의 기술 구성을 다시 사용 하는지 여부, 계획을 사용할 수 있는 Azure 지역을 설정할 수 있습니다. 이 탭에 대 한 답변은이 계획의 다른 탭에 표시 되는 필드에 영향을 줍니다.

### <a name="select-the-plan-type"></a>계획 유형 선택

- **계획 유형** 목록에서 **솔루션 템플릿** 또는 **관리 되는 응용 프로그램** 중 하나를 선택 합니다.

**솔루션 템플릿** 플랜은 고객이 완전히 관리합니다. **관리형 애플리케이션** 플랜을 사용하면 게시자가 고객 대신 애플리케이션을 관리할 수 있습니다. 이러한 두 가지 계획 유형에 대 한 자세한 내용은 [계획 유형](plan-azure-application-offer.md#types-of-plans)을 참조 하세요.

### <a name="re-use-technical-configuration-optional"></a>기술 구성 다시 사용 (선택 사항)

이 제품 내에서 동일한 유형의 계획을 두 개 이상 만들었으며 기술 구성이 동일한 경우 다른 계획의 기술 구성을 재사용할 수 있습니다. 해당 플랜이 게시된 후에는 이 설정을 변경할 수 없습니다.

#### <a name="to-re-use-technical-configuration"></a>기술 구성을 다시 사용 하려면

1. **이 계획은 동일한 유형의 다른 계획에서 기술 구성을 재사용** 합니다. 확인란을 선택 합니다.
1. 표시 되는 목록에서 원하는 기본 계획을 선택 합니다.

> [!NOTE]
> 다른 계획에서 패키지를 다시 사용 하는 경우이 계획에서 전체 **기술 구성** 탭이 사라집니다. 나중에 수행 하는 업데이트를 포함 하 여 다른 계획의 기술 구성 세부 정보는이 계획에도 사용 됩니다.

### <a name="select-azure-regions-clouds"></a>Azure 지역 선택 (클라우드)

플랜은 하나 이상의 Azure 지역에서 사용할 수 있어야 합니다. 계획을 게시 하 고 특정 Azure 지역에서 사용할 수 있게 되 면 제품에서 해당 지역을 제거할 수 없습니다.

#### <a name="azure-global-region"></a>Azure 글로벌 지역

**Azure Global** 확인란은 기본적으로 선택 되어 있습니다. 이렇게 하면 상업적 marketplace 통합이 있는 모든 Azure 글로벌 지역에서 고객이 계획을 사용할 수 있습니다. 관리 되는 응용 프로그램 계획의 경우 요금제를 사용할 수 있도록 하려는 시장에서 선택할 수 있습니다.

이 지역에서 제품을 제거 하려면 **Azure Global** 확인란의 선택을 취소 합니다.

#### <a name="azure-government-region"></a>Azure Government 지역

이 지역은 미국 연방, 주, 지방 또는 부족의 고객에 대 한 제어 된 액세스와 이러한 엔터티를 제공 하는 파트너를 제공 합니다. 게시자는 규정 준수 제어, 보안 조치 및 모범 사례를 담당합니다. Azure Government는 물리적으로 격리되어 미국에만 있는 데이터 센터와 네트워크를 사용합니다.

Azure Government 서비스는 특정 정부 규정 및 요구 사항이 적용되는 데이터를 처리합니다. 예를 들어 FedRAMP, NIST 800.171(DIB), ITAR, IRS 1075, DoD L4 및 CJIS가 있습니다. 관련 프로그램에 대한 인증을 알리기 위해 해당 프로그램을 설명하는 최대 100개의 링크를 제공할 수 있습니다. 이러한 링크는 프로그램의 목록에 직접 연결되는 링크이거나 사용자 고유의 웹 사이트에 있는 목록의 규정 준수에 대한 설명에 연결되는 링크일 수 있습니다. 이러한 링크는 Azure Government 고객에게만 표시됩니다.

##### <a name="to-select-the-azure-government-region"></a>Azure Government 영역을 선택 하려면

1. **Azure Government** 확인란을 선택 합니다.
1. **Azure Government** 인증에서 **+ 인증 추가 (최대 100)** 를 선택 합니다.
1. 표시 되는 상자에서 인증에 대 한 이름 및 링크를 제공 합니다.
1. 다른 인증을 추가 하려면 2 단계와 3 단계를 반복 합니다.

다음 탭을 계속 하기 전에 **초안 저장** 을 선택 합니다. 계획 목록.

## <a name="define-the-plan-listing"></a>계획 목록 정의

**계획 목록** 탭에서는 계획의 세부 정보 목록을 구성할 수 있습니다. 이 탭에는 동일한 제품에 있는 계획 간의 차이점을 보여 주는 특정 정보가 표시 됩니다. 계획 이름, 요약 및 설명이 상업적 marketplace에 표시 되도록 지정할 수 있습니다.

1. **계획 이름** 상자에이 계획의 앞에서 제공한 이름이 표시 됩니다. 언제 든 지 변경할 수 있습니다. 이 이름은 상용 marketplace에 제품 소프트웨어 계획의 제목으로 표시 되며 100 자로 제한 됩니다.
1. **계획 요약** 상자에 계획에 대 한 간략 한 요약을 제공 합니다 (제안이 아님). 이 요약은 100자로 제한됩니다.
1. **계획 설명** 상자에서이 소프트웨어 계획의 고유한 정의와 제품 내 다른 요금제의 차이점을 설명 합니다. 제품이 아닌 플랜을 설명해야 합니다. 이 설명에는 최대 2000 자까지 사용할 수 있습니다.
1. 계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

다음 중 하나를 수행합니다.

- 솔루션 템플릿 계획을 구성 하는 경우 [솔루션 템플릿 계획 구성](create-new-azure-apps-offer-solution.md)으로 이동 합니다.
- 관리 되는 응용 프로그램 계획을 구성 하는 경우 [관리 되는 응용 프로그램 계획 구성](create-new-azure-apps-offer-managed.md)으로 이동 합니다.
