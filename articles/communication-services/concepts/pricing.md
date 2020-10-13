---
title: 통화(음성/영상) 및 채팅의 가격 책정 시나리오
titleSuffix: An Azure Communication Services concept document
description: Communication Services의 가격 책정 모델에 대해 알아봅니다.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4eec258cf642688c87b363ff467f1f368727a013
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761229"
---
# <a name="pricing-scenarios"></a>가격 책정 시나리오

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

Azure Communication Services의 가격은 선불 요금이 없는 종량제 모델을 기반으로 합니다. 서비스 사용량만큼만 요금이 청구됩니다.

## <a name="voicevideo-calling-and-screen-sharing"></a>음성/화상 통화 및 화면 공유

Azure Communication Services를 사용하면 애플리케이션에 음성/영상 통화 및 화면 공유를 추가할 수 있습니다. JavaScript, Objective-C(Apple), Java(Android) 또는 .NET 클라이언트 라이브러리를 사용하여 애플리케이션에 환경을 포함할 수 있습니다. [사용 가능한 클라이언트 라이브러리의 전체 목록](./sdk-options.md)을 참조하세요.

### <a name="pricing"></a>가격 책정

통화 및 화면 공유 서비스는 그룹 통화의 경우 참가자별로 분당 $0.004의 요금이 청구됩니다. 가능한 여러 통화 흐름을 이해하려면 [이 페이지](./call-flows.md)를 참조하세요.

통화의 각 참가자에게는 통화에 연결된 시간만큼 분당 요금이 청구됩니다. 사용자가 영상 통화, 음성 통화 또는 화면 공유 중 무엇을 사용하든 관계없이 적용됩니다.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>가격 책정 예: JS 및 iOS 클라이언트 라이브러리를 사용하는 그룹 음성/영상 통화

Alice는 동료 Bob 및 Charlie와 함께 그룹 통화를 했습니다. Alice와 Bob은 JS 클라이언트 라이브러리를 사용했고, Charlie는 iOS 클라이언트 라이브러리를 사용했습니다.

- 통화는 총 60분 동안 진행되었습니다.
- Alice와 Bob은 전체 통화에 참여했습니다. Alice는 5분 동안 비디오를 켰고 23분 동안 화면을 공유했습니다. Bob은 전체 통화 시간(60분) 동안 비디오를 켰고 12분 동안 화면을 공유했습니다.
- Charlie는 43분 후 통화를 종료했습니다. Charlie는 참가한 시간(43분) 동안 오디오와 비디오를 사용했습니다.

**비용 계산**

- 참가자 2명 x 60분 x 참가자별로 분당 $0.004 = $0.48[영상 통화와 음성 통화는 모두 동일한 요율로 청구됨]
- 참가자 1명 x 43분 x 참가자별로 분당 $0.004 = $0.172[영상 통화와 음성 통화는 모두 동일한 요율로 청구됨]

**그룹 통화의 총 비용**: $0.48 + $0.172 = $0.652

## <a name="chat"></a>채팅

Communication Services를 사용하면 2명 이상의 사용자 간에 채팅 메시지를 보내고 받는 기능으로 애플리케이션을 개선할 수 있습니다. 채팅 클라이언트 라이브러리는 JavaScript, .NET, Python 및 Java에서 사용할 수 있습니다. [클라이언트 라이브러리에 대해 알아보려면 이 페이지](./sdk-options.md)를 참조하세요.

### <a name="price"></a>가격

전송된 모든 채팅 메시지에 대해 $0.0008의 요금이 청구됩니다.

### <a name="pricing-example-chat-between-two-users"></a>가격 책정 예: 두 사용자 간 채팅 

Geeta는 업데이트를 공유하기 위해 Emily와 채팅 스레드를 시작하고 5개 메시지를 전송했습니다. 채팅이 10분 동안 지속되는 동안 Geeta와 Emily는 각각 15개의 메시지를 더 보냈습니다.

**비용 계산** 
- 전송된 메시지 수(5 + 15 + 15) x $0.0008 = $0.028

### <a name="pricing-example-group-chat-with-multiple-users"></a>가격 책정 예: 여러 사용자와의 그룹 채팅 

Charlie는 휴가 계획을 위해 친구 Casey 및 Jasmine과 채팅 스레드를 시작했습니다. Charlie, Casey, Jasmine은 잠시 채팅하는 동안 각각 20개, 30개, 18개의 메시지를 전송했습니다. 친구 Rose도 여행에 관심이 있을 것 같다고 생각해서 Rose를 채팅 스레드에 추가하고 모든 메시지 기록을 공유했습니다. 

Rose는 메시지를 확인하고 채팅을 시작했습니다. 잠시 후 Casey는 전화가 와서 나중에 다시 대화화기로 했습니다. Charlie, Jasmine, Rose는 여행 날짜를 결정하고 각각 30개, 25개, 35개 메시지를 더 전송했습니다.

**비용 계산** 

- 전송된 메시지 수(20 + 30 + 18 + 30 + 25 + 35) x $0.0008 = $0.1264


## <a name="telephony-and-sms"></a>전화 통신 및 SMS

## <a name="price"></a>가격 

전화 통신 서비스는 분당 가격이 책정되는 반면 SMS는 메시지별로 가격이 책정됩니다. 가격은 사용 중인 번호의 유형과 위치뿐만 아니라 통화 및 SMS 메시지의 대상에 따라 결정됩니다.

### <a name="telephone-calling"></a>전화 통화

기존 전화 통화(공용 전화망을 통해 발생하는 통화)는 미국에 기반을 둔 전화 번호에 대해 종량제 가격으로 제공됩니다. 가격은 사용된 번호 유형과 통화 대상을 기준으로 하는 분당 요금입니다. 가장 인기 있는 통화 대상에 대한 가격 정보는 아래 표에 포함되어 있습니다. 전체 대상 목록은 [상세 가격 목록](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)을 참조하세요.


#### <a name="united-states-calling-prices"></a>미국 통화 가격

다음 가격에는 2021년 6월 30일까지 필요한 통신 세금 및 요금이 포함됩니다.

|숫자 형식   |전화를 거는 대상   |전화를 받는 대상|
|--------------|-----------|------------|
|로컬     |시작가: $0.013/분       |$0.0085/분        |
|수신자 부담 |$0.013/분   |$0.0220/분 |

#### <a name="other-calling-destinations"></a>기타 통화 대상

다음 가격에는 2021년 6월 30일까지 필요한 통신 세금 및 요금이 포함됩니다.

|전화 걸기   |분당 가격|
|-----------|------------|
|캐나다     |시작가: $0.013/분   |
|영국     |시작가: $0.015/분   |
|독일     |시작가: $0.015/분   |
|프랑스     |시작가: $0.016/분   |


### <a name="sms"></a>SMS

SMS는 종량제 가격을 제공합니다. 가격은 메시지 대상을 기준으로 하는 메시지당 요금입니다. 수신자 부담 전화 번호로 메시지를 미국 내에 있는 전화 번호로 보낼 수 있습니다. 로컬(지리적) 전화 번호는 SMS 메시지를 보내는 데 사용할 수 없습니다.

다음 가격에는 2021년 6월 30일까지 필요한 통신 세금 및 요금이 포함됩니다.

|국가   |메시지 보내기|메시지 받기|
|-----------|------------|------------|
|USA(수신자 부담)    |$0.0075/msg   | $0.0075/msg |