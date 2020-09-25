---
title: Azure Communication Services의 SMS 개념
titleSuffix: An Azure Communication Services concept document
description: Communication Services SMS 개념에 대해 알아봅니다.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945309"
---
# <a name="sms-concepts"></a>SMS 개념

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Communication Services를 사용하면 Communication Services SMS 클라이언트 라이브러리를 사용하여 SMS 문자 메시지를 보내고 받을 수 있습니다. 이러한 클라이언트 라이브러리를 사용하여 고객 서비스 시나리오, 약속 미리 알림, 2단계 인증 및 기타 실시간 통신 요구를 지원할 수 있습니다. Communication Services SMS를 사용하면 캠페인을 둘러싼 배달 기능 및 응답 요금 정보를 노출하면서 메시지를 안정적으로 전송할 수 있습니다.

Azure Communication Services SMS 클라이언트 라이브러리의 주요 기능에는 다음이 포함됩니다.

-  애플리케이션에 SMS 기능을 추가하기 위한 **간단한** 설정 환경
- 미국에서 A2P(애플리케이션-사람) 사용 사례의 무료 전화 번호에 대한 **높은 속도** 메시지 지원
- 고객 지원, 경고 및 약속 미리 알림과 같은 시나리오를 지원하는 **양방향** 대화
- 애플리케이션에서 보낸 메시지에 대한 실시간 배달 보고서를 사용하여 **안정적인 배달**
- 사용 패턴과 고객 참여를 추적하는 **분석**
- 무료 전화 번호에 대한 옵트아웃을 자동으로 검색하고 존중하는 **옵트아웃** 처리 지원 Communication Services는 STOP 및 START 메시지를 검색하고 최종 사용자에게 다음과 같은 기본 응답을 보냅니다. 
  - STOP - *"이 번호의 메시지에 대한 구독을 취소했습니다. START를 회신하여 다시 구독합니다."*
  - START - *"이 번호의 메시지에 대해 다시 구독했습니다. STOP을 회신하여 구독을 취소합니다."*
  - STOP 및 START 메시지는 사용자에게 다시 릴레이됩니다. Azure Communication Services는 이러한 옵트아웃을 모니터링하고 구현하여 통신을 옵트아웃한 받는 사람에게 더 이상 메시지가 전송되지 않도록 합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [sms 전송 시작](../../quickstarts/telephony-sms/send.md)

다음 문서는 사용자에게 유용할 수 있습니다.

- [SMS 클라이언트 라이브러리](../telephony-sms/sdk-features.md) 숙지
- SMS 지원 [전화 번호](../../quickstarts/telephony-sms/get-phone-number.md) 가져오기
- [SMS 솔루션 계획](../telephony-sms/plan-solution.md)
