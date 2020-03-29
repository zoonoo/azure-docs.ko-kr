---
title: AS2 메시지 설정
description: AS2에 대한 참조 가이드는 엔터프라이즈 통합 팩을 사용 하 고 Azure 논리 앱에서 설정을 보내고 받을
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793034"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩을 사용한 Azure 논리 앱의 AS2 메시지 설정 참조

이 참조는 AS2 계약이 거래 파트너 간에 주고받은 메시지를 처리하는 방법을 지정하기 위해 설정할 수 있는 속성을 설명합니다. 메시지를 교환하는 파트너와의 계약에 따라 이러한 속성을 설정합니다.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 수신 설정

!["설정 받기"를 선택합니다.](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| 속성 | 필수 | 설명 |
|----------|----------|-------------|
| **메시지 속성 재정의** | 예 | 속성 설정으로 들어오는 메시지의 속성을 재정의합니다. |
| **메시지 서명 필요** | 예 | 들어오는 모든 메시지를 디지털 서명해야 하는지 여부를 지정합니다. **인증서** 목록에서 서명이 필요한 경우 메시지의 서명을 확인하기 위해 기존 게스트 파트너 공용 인증서를 선택합니다. 인증서가 없는 경우 [인증서 추가에](../logic-apps/logic-apps-enterprise-integration-certificates.md)대해 자세히 알아봅니다. |
| **메시지 암호화 필요** | 예 | 들어오는 모든 메시지를 암호화해야 하는지 여부를 지정합니다. 암호화되지 않은 메시지는 거부됩니다. **암호화가** 필요한 경우 인증서 목록에서 들어오는 메시지를 해독하기 위해 기존 호스트 파트너 개인 인증서를 선택합니다. 인증서가 없는 경우 [인증서 추가에](../logic-apps/logic-apps-enterprise-integration-certificates.md)대해 자세히 알아봅니다. |
| **메시지 압축 필요** | 예 | 들어오는 모든 메시지를 압축해야 하는지 여부를 지정합니다. 압축되지 않은 메시지는 거부됩니다. |
| **메시지 ID 중복 허용 허용** | 예 | 중복 된 아이디가 있는 메시지를 허용할지 여부를 지정합니다. 중복 된 아이디를 허용하지 않는 경우 검사 사이의 일 수를 선택합니다. 중복 을 일시 중단할지 여부를 선택할 수도 있습니다. |
| **MDN 텍스트** | 예 | 메시지 보낸 사람에게 보낼 기본 메시지 처리 알림(MDN)을 지정합니다. |
| **MDN 전송** | 예 | 수신된 메시지에 대해 동기 식 MdN을 보낼지 여부를 지정합니다.  |
| **서명된 MDN 전송** | 예 | 수신된 메시지에 대해 서명된 MDN을 보낼지 여부를 지정합니다. **MIC 알고리즘** 목록에서 서명이 필요한 경우 메시지 서명에 사용할 알고리즘을 선택합니다. |
| **비동기 MDN 전송** | 예 | MdN을 비동기적으로 보낼지 여부를 지정합니다. **URL** 상자에서 비동기 MDN을 선택하는 경우 MDN을 보낼 위치에 대한 URL을 지정합니다. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 송신 설정

!["설정 보내기"를 선택합니다.](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| 속성 | 필수 | 설명 |
|----------|----------|-------------|
| **메시지 서명 사용** | 예 | 나가는 모든 메시지를 디지털 서명해야 하는지 여부를 지정합니다. 서명이 필요한 경우 다음 값을 선택합니다. <p>- **서명 알고리즘** 목록에서 메시지 서명에 사용할 알고리즘을 선택합니다. <br>- **인증서** 목록에서 메시지 서명을 위해 기존 호스트 파트너 개인 인증서를 선택합니다. 인증서가 없는 경우 [인증서 추가에](../logic-apps/logic-apps-enterprise-integration-certificates.md)대해 자세히 알아봅니다. |
| **메시지 암호화 사용** | 예 | 나가는 모든 메시지를 암호화해야 하는지 여부를 지정합니다. 암호화가 필요한 경우 다음 값을 선택합니다. <p>- **암호화 알고리즘** 목록에서 메시지 암호화에 사용할 게스트 파트너 공용 인증서 알고리즘을 선택합니다. <br>- **인증서** 목록에서 나가는 메시지를 암호화하기 위해 기존 게스트 파트너 개인 인증서를 선택합니다. 인증서가 없는 경우 [인증서 추가에](../logic-apps/logic-apps-enterprise-integration-certificates.md)대해 자세히 알아봅니다. |
| **메시지 압축 사용** | 예 | 나가는 모든 메시지를 압축해야 하는지 여부를 지정합니다. |
| **HTTP 헤더 펼침** | 예 | HTTP `content-type` 헤더를 한 줄에 놓습니다. |
| **MIME 헤더로 파일 이름 전송** | 예 | MIME 헤더에 파일 이름을 포함할지 여부를 지정합니다. |
| **MDN 요청** | 예 | 나가는 모든 메시지에 대해 메시지 처리 알림(MdN)을 받을지 여부를 지정합니다. |
| **서명된 MDN 요청** | 예 | 나가는 모든 메시지에 대해 서명된 MDN을 받을지 여부를 지정합니다. **MIC 알고리즘** 목록에서 서명이 필요한 경우 메시지 서명에 사용할 알고리즘을 선택합니다. |
| **비동기 MDN 요청** | 예 | 비동기적으로 MDN을 받을지 여부를 지정합니다. **URL** 상자에서 비동기 MDN을 선택하는 경우 MDN을 보낼 위치에 대한 URL을 지정합니다. |
| **NRR 사용** | 예 | 부인 방지 영수증(NRR)을 요구할지 여부를 지정합니다. 이 통신 특성은 데이터가 주소가 지정된 대로 수신되었다는 증거를 제공합니다. |
| **SHA2 알고리즘 형식** | 예 | 나가는 AS2 메시지 또는 MDN에 대 한 헤더에 로그인에 사용할 MIC 알고리즘 형식을 지정 합니다. |
||||

## <a name="next-steps"></a>다음 단계

[AS2 메시지 교환](../logic-apps/logic-apps-enterprise-integration-as2.md)
