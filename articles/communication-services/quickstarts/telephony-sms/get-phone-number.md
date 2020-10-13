---
title: 빠른 시작 - Azure Communication Services에서 전화 번호 받기
description: Azure Portal을 사용하여 Communication Services 전화 번호를 구매하는 방법을 알아봅니다.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: e06c3720e180c1dc4fa2f227fd86d15cbbb0ff33
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91756917"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 전화 번호 받기

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Portal을 통해 전화 번호를 구매하여 Azure Communication Services를 시작합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [활성 Communication Services 리소스](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>전화 번호 받기

번호 프로비저닝을 시작하려면 [Azure Portal](https://portal.azure.com)에서 Communication Services 리소스로 이동합니다.

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

### <a name="getting-new-phone-numbers"></a>새 전화 번호 받기

리소스 메뉴에서 [전화 번호] 블레이드로 이동합니다.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

`Get` 단추를 눌러 마법사를 시작합니다. `Phone numbers` 블레이드의 마법사는 시나리오에 가장 적합한 전화 번호를 선택하는 데 도움이 되는 일련의 질문을 안내합니다. 

먼저 전화 번호를 프로비저닝하려는 `Country/region`을 선택해야 합니다. 국가/지역을 선택한 후에는 요구 사항에 가장 적합한 `use case`을 선택해야 합니다. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

### <a name="select-a-phone-plan"></a>전화 요금제 선택

전화 요금제를 선택하는 과정은 다음과 같은 두 단계로 구성됩니다. 

1. [번호 유형](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services) 선택
2. [요금제](../../concepts/telephony-sms/plan-solution.md#phone-number-plans-in-azure-communication-services) 선택

현재 `Geographic` 및 `Toll-free` 두 가지 유형의 번호가 제공됩니다. 번호 유형을 선택하면 그에 따라 여러 가지 요금에 옵션이 제공됩니다.

이 예제에서는 `Outbound calling` 및 `Inbound and Outbound SMS` 계획을 사용하여 `Toll-free` 번호 유형을 선택했습니다.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

여기서 페이지 맨 아래에 있는 `Next: Numbers` 단추를 클릭하여 프로비저닝할 전화 번호를 사용자 지정합니다.

### <a name="customizing-phone-numbers"></a>전화 번호 사용자 지정

`Numbers` 페이지에서 프로비저닝하려는 전화 번호를 사용자 지정합니다.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

> [!NOTE]
> 이 빠른 시작에서는 `Toll-free` 번호 유형의 사용자 지정 흐름을 보여줍니다. `Geographic` 번호 유형을 선택한 경우 환경이 약간 다를 수 있지만 최종 결과는 동일합니다.

사용 가능한 지역 코드 목록에서 `Area code`를 선택하고 프로비저닝하려는 수량을 입력한 다음, `Search`를 클릭하여 선택한 요구 사항을 충족하는 번호를 찾습니다. 요구 사항을 충족하는 전화 번호가 해당 월간 비용과 함께 표시됩니다.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

> [!NOTE]
> 가용성은 선택한 번호 유형, 위치 및 요금제에 따라 달라집니다.
> 번호는 트랜잭션이 만료되기 전까지 짧은 시간 동안 예약됩니다. 트랜잭션이 만료되면 번호를 다시 선택해야 합니다.

구매 요약 정보를 살펴보고 주문을 넣으려면 페이지 맨 아래에 있는 `Next: Summary` 단추를 클릭합니다.

### <a name="place-order"></a>주문하기

요약 페이지에서 번호 유형, 기능, 전화 번호 및 전화 번호를 프로비저닝하는 데 소요되는 총 월간 비용을 검토합니다.

> [!NOTE]
> 표시된 가격은 선택한 전화 번호를 사용자에게 임대하는 비용을 포함하는 **월별 정기 요금**입니다. 전화를 걸거나 받을 때 발생하는 **종량제 비용**은 이 보기에 포함되지 않습니다. 가격표는 [여기](../../concepts/pricing.md)서 확인할 수 있습니다. 이러한 비용은 번호 유형과 전화를 건 대상에 따라 다릅니다. 예를 들어, 시애틀 지역 번호에서 뉴욕 지역 번호로 전화를 걸 때 분당 요금과 같은 번호에서 영국 핸드폰 번호로 전화를 걸 때 분당 요금이 서로 다를 수 있습니다.

마지막으로 페이지 맨 아래에서 `Place order`를 클릭하여 확인합니다.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Azure Portal에서 전화 번호 찾기

다음과 같이 [Azure Portal](https://portal.azure.com)에서 Azure Communication 리소스로 이동합니다.

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

메뉴에서 [전화 번호] 블레이드를 선택하여 전화 번호를 관리합니다.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

> [!NOTE]
> 프로비저닝된 번호가 이 페이지에 표시될 때까지 몇 분 정도 걸릴 수 있습니다.

### <a name="customizing-phone-number-plans"></a>전화 번호 계획 사용자 지정
`Numbers` 페이지에서 계획을 사용자 지정할 번호를 클릭하여 전화 번호를 선택할 수 있습니다.

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

사용 가능한 통화 및 SMS 기능 목록에서 기능을 선택한 다음, `Confirm`을 클릭하여 선택 항목을 적용합니다.

## <a name="troubleshooting"></a>문제 해결

일반적인 질문 및 문제:

- 현재는 미국만 전화 번호 구매가 지원됩니다. 이는 리소스가 연결된 구독의 청구 주소를 기반으로 합니다. 지금은 리소스를 다른 구독으로 이동할 수 없습니다.

- 전화 번호가 릴리스되면 청구 주기가 끝날 때까지 해당 전화 번호를 릴리스하거나 재구매할 수 없습니다.

- Communication Services 리소스가 삭제될 경우 그 즉시 해당 리소스와 연결된 전화 번호가 자동으로 재임대됩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 전화 번호 구매
> * 전화 번호 관리
> * 전화 번호 재임대

> [!div class="nextstepaction"]
> [SMS 보내기](../telephony-sms/send.md)
> [전화 시작](../voice-video-calling/getting-started-with-calling.md)
