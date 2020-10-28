---
title: Azure Communication Services 전화 통신 및 SMS 솔루션 계획
titleSuffix: An Azure Communication Services concept document
description: 전화 번호 및 전화 통신의 사용을 효과적으로 계획하는 방법을 알아봅니다.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: 0420360b70485e49dc6cd06dbeb19400c0f73ef5
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070353"
---
# <a name="plan-your-telephony-and-sms-solution"></a>전화 통신 및 SMS 솔루션 계획

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Azure Communication Services를 사용하면 전화 번호를 사용하여 PSTN(공중 전화망)을 통해 음성 통화를 하고 SMS 메시지를 보낼 수 있습니다. 이 문서에서는 Communication Services를 사용하여 전화 통신 및 SMS 솔루션을 계획하기 위한 전화 번호 유형, 구성 옵션 및 지역 가용성을 검토합니다.

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]


## <a name="phone-number-types-in-azure-communication-services"></a>Azure Communication Services의 전화 번호 유형
 
Communication Services는 **현지** 및 **무료** 라는 두 가지 유형의 전화 번호를 제공합니다. 

### <a name="local-numbers"></a>현지 전화 번호
현지(지역) 전화 번호는 미국의 현지 지역 코드로 구성된 10자리 전화 번호입니다. 예를 들어 `+1 (206) XXX-XXXX`는 지역 코드가 `206`인 현지 전화 번호입니다. 이 지역 코드는 시애틀 시로 지정됩니다. 이러한 전화 번호는 일반적으로 개인 및 지역 비즈니스에서 사용됩니다. Azure Communication Services는 미국에서 현지 전화 번호를 제공합니다. 이러한 번호는 전화를 거는 데 사용할 수 있지만 SMS 메시지를 보내는 데는 사용할 수 없습니다. 

### <a name="toll-free-numbers"></a>무료 전화 번호
무료 전화 번호는 무료로 전화를 걸 수 있는 10자리 전화 번호(고유한 지역 코드 포함)입니다. 예를 들어 `+1 (800) XXX-XXXX`는 북아메리카 지역의 무료 전화 번호입니다. 이러한 전화 번호는 일반적으로 고객 서비스를 위해 사용됩니다. Azure Communication Services는 미국에서 무료 전화 번호를 제공합니다. 이러한 번호는 전화를 걸고 SMS 메시지를 보내는 데 사용할 수 있습니다. 무료 전화 번호는 사람이 사용할 수 없으며 애플리케이션에만 할당할 수 있습니다.

#### <a name="choosing-a-phone-number-type"></a>전화 번호 유형 선택

애플리케이션에서 전화 번호를 사용하는 경우(예: 서비스를 대신하여 전화를 걸거나 메시지를 보내기 위해) 무료 또는 현지(지역) 전화 번호를 선택할 수 있습니다. 애플리케이션에서 SMS 메시지를 보내거나 전화를 거는 경우 무료 전화 번호를 선택할 수 있습니다.

다른 사람(예: 통화 애플리케이션 사용자)이 전화 번호를 사용하는 경우 현지(지역) 전화 번호를 사용해야 합니다. 

아래 표에는 이러한 전화 번호 유형이 요약되어 있습니다. 

| 전화 번호 유형 | 예제                              | 국가별 가용성    | 전화 번호 기능 |일반적인 사용 사례                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| 현지(지역)        | +1(현지 지역 코드) XXX XX XX  | US                      | 통화(아웃바운드) | 애플리케이션에서 전화 번호를 사용자에게 할당  |
| 수신자 부담         | +1(수신자 부담 지역 *코드* ) XXX XX XX | US                      | 통화(아웃바운드), SMS(인바운드/아웃바운드)| 전화 번호를 IVR(대화형 음성 응답) 시스템/봇, SMS 애플리케이션에 할당                                        |


## <a name="phone-number-features-in-azure-communication-services"></a>Azure Communication Services의 전화 번호 기능 

대부분의 전화 번호에 대해 "a la carte(맞춤형)" 기능을 구성할 수 있습니다. 이러한 기능은 Azure Communication Services 내에서 전화 번호를 임대할 때 선택할 수 있습니다.

사용할 수 있는 기능은 운영 중인 국가, 사용 사례 및 선택한 전화 번호 유형에 따라 달라집니다. 이러한 기능은 규정 요구 사항으로 인해 국가마다 다릅니다. Azure Communication Services에서 제공하는 전화 번호 기능은 다음과 같습니다.

- **단방향 아웃바운드 SMS** 이 옵션을 사용하면 사용자에게 SMS 메시지를 보낼 수 있습니다. 이는 알림 및 2단계 인증 시나리오에서 유용할 수 있습니다. 
- **양방향 인바운드 및 아웃바운드 SMS** 이 옵션을 사용하면 전화 번호를 사용하여 사용자로부터 메시지를 보내고 받을 수 있습니다. 이는 고객 서비스 시나리오에서 유용할 수 있습니다.
- **단방향 아웃바운드 전화 통화** 이 옵션을 사용하면 사용자에게 전화를 걸고 서비스에서 전화를 거는 아웃바운드 통화에 대한 호출자 ID를 구성할 수 있습니다. 이는 고객 서비스 및 음성 알림 시나리오에서 유용할 수 있습니다.

## <a name="countryregion-availability"></a>국가/지역 가용성

다음 표에서는 전화 번호 유형과 연결된 인바운드/아웃바운드 통화 및 SMS 기능과 함께 이러한 다양한 유형의 전화 번호를 얻을 수 있는 위치를 보여 줍니다.

|전화 번호 유형| 전화 번호 획득 위치 | 통화 수신 지역                                        | 통화 발신 지역                                    |메시지 수신 지역       | 메시지 발신 지역 |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| 현지(지역)  | US                 | 미국, 캐나다, 영국, 독일, 프랑스 등*| 미국, 캐나다, 영국, 독일, 프랑스 등* |사용할 수 없음| 사용할 수 없음 |
| 수신자 부담 | US                 | US                                                   | US                                                    |US                | US |

*통화 대상 및 가격 책정에 대한 자세한 내용은 [가격 책정 페이지](../pricing.md)를 참조하세요.

## <a name="azure-subscriptions-eligibility"></a>Azure 구독 자격

전화 번호를 얻으려면 유료 Azure 구독에 있어야 합니다. 평가판 계정 또는 Azure 체험 크레딧으로는 전화 번호를 얻을 수 없습니다. 

현재 전화 번호 가용성은 미국에 청구 주소가 있는 Azure 구독으로 제한됩니다.

## <a name="next-steps"></a>다음 단계

### <a name="quickstarts"></a>빠른 시작

- [전화 번호 받기](../../quickstarts/telephony-sms/get-phone-number.md)
- [전화 걸기](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [SMS 보내기](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>개념 설명서

- [음성 및 비디오 개념](../voice-video-calling/about-call-types.md)
- [통화 흐름](../call-flows.md)
- [가격](../pricing.md)
