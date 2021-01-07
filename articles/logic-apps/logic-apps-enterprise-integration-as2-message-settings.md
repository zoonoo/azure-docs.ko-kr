---
title: AS2 메시지 설정
description: Azure Logic Apps에서 AS2 송신 및 수신 설정에 대 한 참조 가이드 엔터프라이즈 통합 팩
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74793034"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Azure Logic Apps에서 AS2 메시지 설정에 대 한 참조 엔터프라이즈 통합 팩

이 참조는 AS2 규약에서 거래 파트너 간에 송수신 된 메시지를 처리 하는 방법을 지정 하기 위해 설정할 수 있는 속성을 설명 합니다. 메시지를 교환 하는 파트너와의 규약에 따라 이러한 속성을 설정 합니다.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 수신 설정

!["수신 설정"을 선택 합니다.](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| 속성 | 필수 | Description |
|----------|----------|-------------|
| **메시지 속성 재정의** | 아니요 | 들어오는 메시지의 속성을 속성 설정으로 재정의 합니다. |
| **메시지 서명 필요** | 아니요 | 들어오는 모든 메시지를 디지털 서명 해야 하는지 여부를 지정 합니다. 서명이 필요한 경우 **인증서** 목록에서 메시지의 서명 유효성을 검사 하기 위한 기존 게스트 파트너 공용 인증서를 선택 합니다. 인증서가 없는 경우 [인증서 추가](../logic-apps/logic-apps-enterprise-integration-certificates.md)에 대해 자세히 알아보세요. |
| **메시지 암호화 필요** | 아니요 | 들어오는 모든 메시지를 암호화 해야 하는지 여부를 지정 합니다. 암호화되지 않은 메시지는 거부됩니다. 암호화가 필요한 경우 **인증서** 목록에서 들어오는 메시지의 암호를 해독 하는 데 사용할 기존 호스트 파트너 개인 인증서를 선택 합니다. 인증서가 없는 경우 [인증서 추가](../logic-apps/logic-apps-enterprise-integration-certificates.md)에 대해 자세히 알아보세요. |
| **메시지 압축 필요** | 아니요 | 들어오는 모든 메시지를 압축 해야 하는지 여부를 지정 합니다. 압축되지 않은 메시지는 거부됩니다. |
| **메시지 ID 중복 허용 안 함** | 아니요 | Id가 중복 된 메시지를 허용할지 여부를 지정 합니다. 중복 Id를 허용 하지 않는 경우 검사 사이의 일 수를 선택 합니다. 중복을 일시 중지할지 여부를 선택할 수도 있습니다. |
| **MDN 텍스트** | 아니요 | 메시지 보낸 사람에 게 보낼 기본 MDN (메시지 처리 알림)을 지정 합니다. |
| **MDN 전송** | 아니요 | 받은 메시지에 대 한 동기 Mdn를 보낼지 여부를 지정 합니다.  |
| **서명된 MDN 전송** | 아니요 | 받은 메시지에 대해 서명 된 Mdn를 보낼지 여부를 지정 합니다. 서명이 필요한 경우 **MIC 알고리즘** 목록에서 메시지 서명에 사용할 알고리즘을 선택 합니다. |
| **비동기 MDN 전송** | 아니요 | Mdn를 비동기적으로 보낼지 여부를 지정 합니다. 비동기 Mdn를 선택 하는 경우 **url** 상자에서 mdn를 보낼 url을 지정 합니다. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 송신 설정

!["설정 보내기"를 선택 합니다.](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| 속성 | 필수 | Description |
|----------|----------|-------------|
| **메시지 서명 사용** | 아니요 | 보내는 모든 메시지를 디지털 서명 해야 하는지 여부를 지정 합니다. 서명이 필요한 경우 다음 값을 선택 합니다. <p>- **서명 알고리즘** 목록에서 메시지에 서명 하는 데 사용할 알고리즘을 선택 합니다. <br>- **인증서** 목록에서 서명 메시지에 대 한 기존 호스트 파트너 개인 인증서를 선택 합니다. 인증서가 없는 경우 [인증서 추가](../logic-apps/logic-apps-enterprise-integration-certificates.md)에 대해 자세히 알아보세요. |
| **메시지 암호화 사용** | 아니요 | 나가는 모든 메시지를 암호화 해야 하는지 여부를 지정 합니다. 암호화 해야 하는 경우 다음 값을 선택 합니다. <p>- **암호화 알고리즘** 목록에서 메시지를 암호화 하는 데 사용할 게스트 파트너 공용 인증서 알고리즘을 선택 합니다. <br>- **인증서** 목록에서 나가는 메시지를 암호화 하기 위한 기존 게스트 파트너 개인 인증서를 선택 합니다. 인증서가 없는 경우 [인증서 추가](../logic-apps/logic-apps-enterprise-integration-certificates.md)에 대해 자세히 알아보세요. |
| **메시지 압축 사용** | 아니요 | 나가는 모든 메시지를 압축 해야 하는지 여부를 지정 합니다. |
| **HTTP 헤더 펼침** | 아니요 | HTTP 헤더를 `content-type` 한 줄에 배치 합니다. |
| **MIME 헤더로 파일 이름 전송** | 아니요 | MIME 헤더에 파일 이름을 포함할지 여부를 지정 합니다. |
| **MDN 요청** | 아니요 | 보내는 모든 메시지에 대해 Mdn (메시지 처리 알림)를 받을지 여부를 지정 합니다. |
| **서명된 MDN 요청** | 아니요 | 보내는 모든 메시지에 대해 서명 된 Mdn를 받을지 여부를 지정 합니다. 서명이 필요한 경우 **MIC 알고리즘** 목록에서 메시지 서명에 사용할 알고리즘을 선택 합니다. |
| **비동기 MDN 요청** | 아니요 | 비동기식으로 Mdn를 받을지 여부를 지정 합니다. 비동기 Mdn를 선택 하는 경우 **url** 상자에서 mdn를 보낼 url을 지정 합니다. |
| **NRR 사용** | 아니요 | 부인 방지 수신 (NRR)이 필요한 지 여부를 지정 합니다. 이 통신 특성은 데이터를 주소가 지정 된 것으로 받은 증거를 제공 합니다. |
| **SHA2 알고리즘 형식** | 아니요 | 나가는 AS2 메시지 또는 MDN에 대 한 헤더에 서명 하는 데 사용할 MIC 알고리즘 형식을 지정 합니다. |
||||

## <a name="next-steps"></a>다음 단계

[AS2 메시지 교환](../logic-apps/logic-apps-enterprise-integration-as2.md)
