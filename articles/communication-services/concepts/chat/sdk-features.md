---
title: Azure Communication Services에 대한 채팅 클라이언트 라이브러리 개요
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services 채팅 클라이언트 라이브러리에 대해 알아봅니다.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: fd62a7fed0c6dec818bf4e2558945fb24c9d5261
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945480"
---
# <a name="chat-client-library-overview"></a>채팅 클라이언트 라이브러리 개요

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Communication Services 채팅 클라이언트 라이브러리를 사용하여 다양한 실시간 채팅을 애플리케이션에 추가할 수 있습니다.

## <a name="chat-client-library-capabilities"></a>채팅 클라이언트 라이브러리 기능

다음 목록에서는 현재 Communication Services 채팅 클라이언트 라이브러리에서 사용할 수 있는 기능 세트를 보여 줍니다.

| 기능 그룹 | 기능                                                                                                          | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ----- | ---- | -----  |
| 핵심 기능 | 2명 이상의 사용자 간 채팅 스레드 만들기(최대 250명의 사용자)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | 채팅 스레드의 토픽 업데이트                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |
|                   | 채팅 스레드에서 멤버 추가 또는 제거                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | 새로 추가된 멤버와 채팅 메시지 기록을 공유할 것인지 여부를 선택 - *모두/없음/특정 시간까지* | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | 모든 채팅 멤버 스레드의 목록 가져오기                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | 채팅 스레드 삭제                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | 사용자의 채팅 스레드 멤버 자격 목록 가져오기                                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | 특정 채팅 스레드에 대한 정보 가져오기                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | 채팅 스레드에서 메시지 보내기 및 받기                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | 메시지를 보낸 후 메시지 내용 편집                                                                   | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | 메시지 삭제                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | 전송 시 우선 순위를 보통 또는 높음으로 메시지에 태그 설정                                               | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | 멤버가 읽은 메시지에 대한 읽음 확인 보내기 및 받기 <br/> *채팅 스레드에 20명 이상의 멤버가 있는 경우 사용할 수 없음*    | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | 멤버가 채팅 스레드에서 메시지를 활발히 입력하는 경우 입력 알림 보내기 및 받기 <br/> *채팅 스레드에 20명 이상의 멤버가 있는 경우 사용할 수 없음*      | ✔️   | ✔️   | ✔️    | ✔️    |
|                   | 채팅 스레드에서 모든 메시지 가져오기 <br/> *유니코드 이모지 지원*                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | 메시지 콘텐츠의 일부로 이모지 보내기                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|실시간 신호(독점적인 신호 패키지에서 사용하도록 설정)| 사용자가 멤버인 채팅 스레드에서 새 메시지를 받을 때 알림 받기                                     | ✔️   | ❌    | ❌  | ❌  |
|                    | 사용자가 멤버인 채팅 스레드에서 다른 멤버가 메시지를 편집할 때 알림 받기                | ✔️   | ❌    | ❌    | ❌  |
|                    | 사용자가 멤버인 채팅 스레드에서 다른 멤버가 메시지를 삭제할 때 알림 받기                | ✔️   | ❌    | ❌    | ❌  |
|                    | 다른 채팅 스레드 멤버가 입력하는 경우 알림 받기                                                             | ✔️   | ❌    | ❌    | ❌  |
|                    | 다른 멤버가 채팅 스레드에서 메시지를 읽은 경우(읽음 확인) 알림 받기                               | ✔️   | ❌    | ❌    | ❌  |
| 이벤트             | Event Grid를 사용하여 채팅 스레드에서 발생하는 사용자 활동을 구독하고 사용자 지정 알림 서비스 또는 비즈니스 논리를 통합     | ✔️   | ✔️  | ✔️    | ✔️  |
| 모니터링        | 전송된 메시지의 용어 면에서 사용량 모니터링                                                                               | ✔️   | ✔️  | ✔️    | ✔️  |
|                    | 앱에서 생성한 API 요청의 품질 및 상태를 모니터링하고 포털을 통해 경고를 구성                                                          | ✔️   | ✔️  | ✔️    | ✔️  |
|추가 기능 | [Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/)를 채팅 클라이언트 라이브러리와 함께 사용하여 *클라이언트에서 들어오는 메시지의 언어 번역 및 감정 분석, 멤버가 말하는 동안 메시지를 작성하는 음성 텍스트 변환 대화*와 같은 인텔리전트 기능을 사용하도록 설정합니다.                                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [채팅 시작](../../quickstarts/chat/get-started.md)

다음 문서는 사용자에게 유용할 수 있습니다.

- [채팅 개념](../chat/concepts.md) 숙지
