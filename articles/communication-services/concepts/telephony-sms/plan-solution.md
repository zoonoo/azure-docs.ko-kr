---
title: Azure Communication Services 전화 통신 및 SMS 솔루션 계획
titleSuffix: An Azure Communication Services concept document
description: 전화 번호 및 전화 통신의 사용을 효과적으로 계획하는 방법을 알아봅니다.
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945312"
---
# <a name="plan-your-telephony-and-sms-solution"></a>전화 통신 및 SMS 솔루션 계획

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

이 문서에서는 Azure Communication Services에서 제공하는 다양한 전화 통신 요금제 및 번호 유형을 설명합니다. Communication Services를 통해 제공되는 음성 서비스 공급자, 전화 번호 유형, 요금제 및 기능을 선택하는 데 도움이 되는 의사 결정 흐름을 살펴보겠습니다.

## <a name="about-phone-numbers-in-azure-communications-services"></a>Azure Communication Services의 전화 번호 정보

Azure Communication Services를 통해 전화 번호를 사용하여 전화를 걸거나 SMS 메시지를 보내고 받을 수 있습니다. 이러한 전화 번호를 사용하여 서비스에서 건 발신 전화에 대해 호출자 ID를 구성할 수 있습니다.
  
Communication Services 솔루션으로 가져올 기존 전화 번호가 없는 경우 가장 간단하게 시작하는 방법은 Azure Communication Services에서 새 전화 번호를 몇 분 안에 가져오는 것입니다.

솔루션에서 계속 사용하려는 기존 전화 번호(예: 1-800–COMPANY)가 있는 경우 기존 공급자의 전화 번호를 Communication Services로 이식할 수 있습니다.

다음 다이어그램은 사용 가능한 옵션을 탐색하는 데 도움이 됩니다.

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="전화 번호와 관련하여 결정을 내리는 방법을 보여주는 다이어그램":::

이제 Communication Services를 통해 제공되는 전화 번호 유형 및 기능을 검토하겠습니다. 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>전화 번호 및 기능에 대한 Microsoft 직접 제안

Azure Communication Services는 개발자에게 뛰어난 유연성을 제공합니다. 대부분의 전화 번호에서 "단품" 요금제 세트를 구성할 수 있습니다. 수신 전화 요금제만 필요한 개발자도 있고, 수신 전화 및 발신 SMS 요금제를 모두 선택하는 개발자도 있을 것입니다. Communication Services 내에서 전화 번호를 임대 및/또는 이식할 때 이러한 요금제를 선택할 수 있습니다.

사용 가능한 요금제는 현재 속한 국가와 전화 번호 유형에 따라 다릅니다. 아래 다이어그램은 의사 결정 흐름을 나타냅니다.    사용 가능한 요금제는 현재 속한 국가와 전화 번호 유형에 따라 다릅니다.

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Example for the decision flow"::: -->

전화 번호 유형을 선택하기 전에, 국제 전화 번호 계획을 검토하겠습니다.

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>추가 자료. 국제 공용 통신 번호 계획(E.164)

> [!NOTE]
> E.164 전화 번호 계획에 익숙한 분들도 Azure Communication Services 직접 제안에서 제공하는 번호 유형과 기능을 보다 정확하게 이해할 수 있도록 이 정보를 검토하는 것이 좋습니다.

국제 공용 통신 번호 계획은 ITU(International Telecommunication Union) 권장 사항 E.164에 정의되어 있습니다. 일치하는 숫자는 최대 15 자리로 제한됩니다.

전화 번호는 다음과 같이 구성됩니다.

-   접두사 “+”
-   국제 다이얼 접두사 또는 국가/지역 코드(1, 2 또는 3자리) 
-   *(선택 사항)* 흔히 지역 코드라고 부르는 국내망 번호 또는 번호 계획 이 코드의 길이는 국가에 따라 다릅니다. 미국은 3자리입니다. 호주와 뉴질랜드는 1자리입니다. 독일, 일본, 멕시코 및 일부 국가의 지역 코드는 길이가 가변적입니다. 예를 들어 독일의 지역 코드는 2~5자리이고, 일본의 지역 코드는 1~5자리입니다.
-   구독자 번호

> [!NOTE]
> 위의 분류는 ITU-T E.164 표준에 완전히 부합하지는 않으며 간단한 설명을 위해 제공되었습니다. 예를 들어 구독자 번호는 표준 내에서 나뉩니다. 국제 번호 계획에 대해 자세히 알아보려면 [ITU E.164 표준](https://www.itu.int/rec/T-REC-E.164)부터 시작하는 것이 좋습니다.  

다음은 번호 계획을 보다 정확하게 이해하는 데 도움이 되는 몇 가지 예입니다.

미국의 지역 전화 번호:

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="미국의 지역 전화 번호 예제":::

캐나다의 지역 전화 번호:

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="캐나다의 지역 전화 번호 예제":::

북아메리카 지역의 수신자 부담 전화 번호:

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="북아메리카 지역의 수신자 부담 전화 번호 예제":::

영국의 휴대폰 번호:

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="영국의 휴대폰 번호 예제":::

다음으로, Azure Communication Services에서 사용할 수 있는 전화 번호 유형을 검토하겠습니다.

## <a name="phone-number-types-in-azure-communication-services"></a>Azure Communication Services의 전화 번호 유형

Microsoft는 해당하는 국가의 단축 코드 및 휴대폰 번호에 대한 지역 및 수신자 부담 요금제를 제공합니다.

아래 표에는 이러한 전화 번호 유형이 요약되어 있습니다. 

| 전화 번호 유형 | 예제                              | 국가별 가용성    | 일반적인 사용 사례                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| 지역          | +1(지리적 지역 코드) XXX XX XX  | 미국, 캐나다, 푸에르토리코 | 애플리케이션의 사용자에게 또는 IVR(대화형 음성 응답) 시스템/봇에 전화 번호 할당 |
| 수신자 부담         | +1(수신자 부담 지역 *코드*) XXX XX XX | 미국, 캐나다, 푸에르토리코 | IVR(대화형 음성 응답) 시스템/봇, SMS 애플리케이션에 할당                                        |

## <a name="plans"></a>계획 

전화 번호에 사용할 수 있는 기능을 살펴보겠습니다. 이러한 기능은 규정 요구 사항 때문에 국가별로 다릅니다. Azure Communication Services는 다음과 같은 기능을 제공합니다.

- **단방향 발신 SMS** - 알림 및 2단계 인증 시나리오에 유용합니다.
- **양방향 수신 및 발신 SMS** - 요금제에 SMS 보내기 및 받기가 포함된 미리 정의된 패키지입니다.
- **PSTN 전화** - 수신 전화를 선택하고 호출자 ID를 사용하여 발신 전화를 걸 수 있습니다.

## <a name="next-steps"></a>다음 단계

### <a name="quickstarts"></a>빠른 시작

- [전화 번호 받기](../../quickstarts/telephony-sms/get-phone-number.md)
- [전화 걸기](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [SMS 보내기](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>개념 설명서

- [음성 및 비디오 개념](../voice-video-calling/about-call-types.md)
- [전화 흐름 및 SMS 흐름](../call-flows.md)
- [가격](../pricing.md)
