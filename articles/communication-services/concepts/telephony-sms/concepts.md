---
title: Azure Communication Services의 SMS 개념
titleSuffix: An Azure Communication Services concept document
description: Communication Services SMS 개념에 대해 알아봅니다.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 35398d60008ac52ba16dca0a0201f8c2f2101a0f
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758560"
---
# <a name="sms-concepts"></a>SMS 개념

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Communication Services를 사용하면 Communication Services SMS 클라이언트 라이브러리를 사용하여 SMS 문자 메시지를 보내고 받을 수 있습니다. 이러한 클라이언트 라이브러리를 사용하여 고객 서비스 시나리오, 약속 미리 알림, 2단계 인증 및 기타 실시간 통신 요구를 지원할 수 있습니다. Communication Services SMS를 사용하면 캠페인을 둘러싼 배달 기능 및 응답 요금 정보를 노출하면서 메시지를 안정적으로 전송할 수 있습니다.

Azure Communication Services SMS 클라이언트 라이브러리의 주요 기능에는 다음이 포함됩니다.

-  애플리케이션에 SMS 기능을 추가하기 위한 **간단한** 설정 환경
- 미국에서 A2P(애플리케이션-사람) 사용 사례의 무료 전화 번호에 대한 **높은 속도** 메시지 지원
- 고객 지원, 경고 및 약속 미리 알림과 같은 시나리오를 지원하는 **양방향** 대화
- 애플리케이션에서 보낸 메시지에 대한 실시간 배달 보고서를 사용하여 **안정적인 배달**
- 사용 패턴과 고객 참여를 추적하는 **분석**
- 무료 전화 번호에 대한 옵트아웃을 자동으로 검색하고 존중하는 **옵트아웃** 처리 지원 미국 수신자 부담 번호에 대한 옵트아웃은 미국 이동 통신 사업자에게 의무화되어 시행됩니다.
  - STOP - 문자 메시지 수신자가 옵트아웃하려면 수신자 부담 번호로 'STOP'을 보내면 됩니다. 이동 통신 사업자는 STOP에 대해 다음과 같은 기본 응답을 보냅니다. *"네트워크 메시지: 이 번호로부터 온 모든 문자를 차단하는 "stop"이라는 단어를 회신했습니다. 메시지를 다시 받으려면 "unstop" 문자를 다시 보내세요."*
  - START/UNSTOP - 수신자가 수신자 부담 번호로부터 온 문자 메시지를 다시 수신하려면 수신자 부담 번호로 ‘START’ 또는 ‘UNSTOP’을 보내면 됩니다. 이동 통신 사업자는 START/UNSTOP에 대해 다음과 같은 기본 응답을 보냅니다. *“네트워크 메시지: “unstop”을 회신했으며 이 번호로부터 메시지를 다시 수신하기 시작합니다.”*
  - Azure Communication Services는 STOP 메시지를 탐지하여 수신자에게 보내는 모든 메시지를 차단합니다. 전달 보고서에는 "지정된 받는 사람에 대해 보낸 사람이 차단됨"이라는 상태 메시지가 포함된 전달 실패가 표시됩니다.
  - STOP, UNSTOP 및 START 메시지는 사용자에게 다시 릴레이됩니다. Azure Communication Services는 이러한 옵트아웃을 모니터링하고 구현하여 통신을 옵트아웃한 수신자에게 더 이상 메시지가 전송되지 않도록 합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [sms 전송 시작](../../quickstarts/telephony-sms/send.md)

다음 문서는 사용자에게 유용할 수 있습니다.

- [SMS 클라이언트 라이브러리](../telephony-sms/sdk-features.md) 숙지
- SMS 지원 [전화 번호](../../quickstarts/telephony-sms/get-phone-number.md) 가져오기
- [SMS 솔루션 계획](../telephony-sms/plan-solution.md)
