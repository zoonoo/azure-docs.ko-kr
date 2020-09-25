---
title: Azure Communication Services에 대한 SMS 클라이언트 라이브러리 개요
titleSuffix: An Azure Communication Services concept document
description: SMS 클라이언트 라이브러리 및 해당 제품에 대한 개요를 제공합니다.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 2d81749e7023bdbf5353e5c8da633674ea8e8ce9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945301"
---
# <a name="sms-client-library-overview"></a>SMS 클라이언트 라이브러리 개요

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Communication Services SMS 클라이언트 라이브러리를 사용하여 SMS 메시징을 애플리케이션에 추가할 수 있습니다.

## <a name="sms-client-library-capabilities"></a>SMS 클라이언트 라이브러리 기능

다음 목록에서는 현재 클라이언트 라이브러리에서 사용할 수 있는 기능 세트를 보여줍니다.

| 기능 그룹 | 기능                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| 핵심 기능 | SMS 메시지 보내기 및 받기 </br> *유니코드 이모지 지원*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 보낸 메시지에 대한 전달 보고서 받기                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 모든 문자 집합(언어/유니코드 지원)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 긴 메시지 지원(최대 2048자)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | 긴 메시지 자동 연결                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| 이벤트            | Event Grid를 사용하여 인바운드 메시지 및 전달 보고서를 수신하도록 웹후크 구성 | ✔️   | ✔️    | ✔️    | ✔️      |
| 전화 번호      | 수신자 부담 번호                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| 규정        | 옵트아웃 처리                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| 모니터링        | 주고 받은 메시지의 사용량 모니터링                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| PSTN 호출      | SMS 사용 가능 전화 번호에 PSTN 호출 기능 추가                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [sms 전송 시작](../../quickstarts/telephony-sms/send.md)

다음 문서는 사용자에게 유용할 수 있습니다.

- 일반적인 [SMS 개념](../telephony-sms/concepts.md) 숙지
- SMS 지원 [전화 번호](../../quickstarts/telephony-sms/get-phone-number.md) 가져오기
- [SMS 솔루션 계획](../telephony-sms/plan-solution.md)