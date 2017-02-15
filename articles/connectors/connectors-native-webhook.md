---
title: "논리 앱 웹후크 커넥터 | Microsoft Docs"
description: "배열 필터링과 같은 동작을 수행하기 위한 웹후크 동작 및 트리거의 개요입니다."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 13d0a71762d3cba05c3dccb647d0890862307327
ms.openlocfilehash: 30c4c9da56687fb90e46eb9a2830d95df6e1c2f4


---
# <a name="get-started-with-the-webhook-connector"></a>웹후크 커넥터 시작
웹후크 동작 및 트리거를 사용하여 다음을 완료하기 위한 흐름을 트리거, 일시 중지 및 다시 시작할 수 있습니다.

* [항목이 수신되자마자 Azure 이벤트 허브](https://github.com/logicappsio/EventHubAPI) 의 트리거가 수신됩니다.
* 워크플로를 계속하기 전에 승인을 대기합니다.

웹후크 구독을 지원하는 API를 만드는 방법에 대한 자세한 내용은 [리 논리 앱 커넥터 생성에 대한 이 문서](../app-service-logic/app-service-logic-create-api-app.md)를 참조하세요.

- - -
## <a name="use-the-webhook-trigger"></a>웹후크 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](connectors-overview.md)  webhook 트리거는 새 항목에 대한 폴링에 의존하지 않으므로 특히 유용합니다. 이벤트 발생 즉시 Logic App이 실행하는 [요청 트리거](connectors-native-reqres.md)가 예입니다.  필요할 때 Logic App을 실행하는 데 사용할 수 있는 서비스에 *콜백 URL*을 등록하면 이 작업이 수행됩니다.

논리 앱 디자이너에서 HTTP 트리거를 설정하는 방법의 예제 시퀀스는 다음과 같습니다.  여기서는 [논리 앱에서 사용되는 웹후크 구독 및 구독 취소 패턴](../app-service-logic/app-service-logic-create-api-app.md#webhook-triggers)을 따라 API를 이미 배포했거나 액세스하고 있다고 가정합니다.  구독 호출은 논리 앱이 새 웹후크와 함께 저장되거나 사용 안 함 상태에서 사용 상태로 전환될 때마다 수행됩니다.  구독 취소 호출은 논리 앱 웹후크 트리거가 제거 또는 저장되거나 사용 상태에서 사용 안 함 상태로 전환될 때마다 수행됩니다.

1. **HTTP 웹후크** 트리거를 논리 앱의 첫 번째 단계로 추가합니다.
2. 웹후크 구독 및 구독 취소 호출에 대한 매개 변수를 입력합니다.

   * 이 동작은 [HTTP 동작](connectors-native-http.md) 형식과 동일한 패턴을 따릅니다.

     ![HTTP 트리거](./media/connectors-native-webhook/using-trigger.png)
3. 하나 이상의 동작 추가
4. 저장을 클릭하여 Logic App을 게시합니다. 이 작업을 수행하면 이 Logic App을 트리거하는 데 필요한 콜백 URL을 사용하여 구독 끝점이 호출됩니다.
5. 서비스에서 콜백 URL에 댛 `HTTP POST` 를 수행할 때마다 논리 앱이 실행됩니다(논리 앱에는 요청에 전달된 모든 데이터가 포함됨).

## <a name="use-the-webhook-action"></a>웹후크 동작 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](connectors-overview.md)  웹후크 동작은 서비스에 *콜백 URL* 을 등록하고, 다시 시작하기 전에 URL이 호출될 때까지 대기하므로 특히 유용합니다.  ["승인 전자 메일 보내기"](connectors-create-api-office365-outlook.md) 는 이 패턴을 따르는 커넥터의 예입니다.  이 패턴을 웹후크 동작을 통해 서비스로 확장할 수 있습니다.  여기서는 [논리 앱에서 사용되는 웹후크 구독 및 구독 취소 패턴](../app-service-logic/app-service-logic-create-api-app.md#webhook-actions)을 따라 API를 이미 배포했거나 액세스하고 있다고 가정합니다.  논리 앱이 웹후크 동작을 실행할 때마다 구독 호출이 수행됩니다.  응답을 기다리는 동안 또는 논리 앱 실행이 시간 초과되기 전에 실행이 취소될 때마다 구독 취소 호출이 수행됩니다.

웹후크 동작을 추가하려면

1. **새 단계** 단추를 선택합니다.
2.  **동작 추가**
3. 동작 검색 상자에 "webhook"를 입력하여 **HTTP 웹후크** 동작을 나열합니다.

    ![쿼리 동작 선택](./media/connectors-native-webhook/using-action-1.png)
4. 웹후크 구독 및 구독 취소 호출에 대한 매개 변수를 입력합니다.

   * 이 동작은 [HTTP 동작](connectors-native-http.md) 형식과 동일한 패턴을 따릅니다.

     ![쿼리 동작 완료](./media/connectors-native-webhook/using-action-2.png)
   * 런타임 시 논리 앱은 해당 단계에 도달하면 구독 끝점을 호출합니다.
5. 도구 모음 왼쪽 위에서 저장을 클릭하면 논리 앱이 저장되고 게시됩니다(활성화).

- - -
## <a name="technical-details"></a>기술 세부 정보
웹후크가 지원하는 트리거 및 동작에 대한 세부 정보는 다음과 같습니다.

## <a name="webhook-triggers"></a>웹후크 트리거
트리거는 워크플로를 시작하기 위한 작업입니다. [트리거에 대해 자세히 알아보세요.](connectors-overview.md)  이 커넥터에는 1개의 트리거가 있습니다.

|  동작을 나열합니다. | 설명 |
| --- | --- |
| HTTP 웹후크 |필요에 따라 URL을 호출할 수 있는 서비스에 대한 콜백 URL을 구독하여 논리 앱을 실행합니다. |

### <a name="trigger-details"></a>트리거 세부 정보
웹후크 커넥터에는 1개의 가능한 트리거가 있습니다. 아래에는 동작, 동작의 필수 및 선택 사항 입력 필드, 사용과 연결된 해당 출력 세부 정보에 대한 정보가 나와 있습니다.

#### <a name="http-webhook"></a>HTTP 웹후크
필요에 따라 URL을 호출할 수 있는 서비스에 대한 콜백 URL을 구독하여 논리 앱을 실행합니다.
*는 필수 필드를 의미합니다.

| 표시 이름 | 속성 이름 | 설명 |
| --- | --- | --- |
| 구독 메서드* |메서드 |구독 요청에 사용할 HTTP 메서드 |
| 구독 URI* |uri |구독 요청에 사용할 HTTP URI |
| 구독 취소 메서드* |메서드 |구독 취소 요청에 사용할 HTTP 메서드 |
| 구독 취소 URI* |uri |구독 취소 요청에 사용할 HTTP URI |
| 구독 본문 |body |구독의 HTTP 요청 본문 |
| 구독 헤더 |headers |구독의 HTTP 요청 헤더 |
| 구독 인증 |인증 |구독에 사용할 HTTP 인증 자세한 내용은 [HTTP 커넥터를 참조](connectors-native-http.md#authentication)하세요. |
| 구독 취소 본문 |body |구독 취소의 HTTP 요청 본문 |
| 구독 취소 헤더 |headers |구독 취소의 HTTP 요청 헤더 |
| 구독 취소 인증 |authentication |구독 취소에 사용할 HTTP 인증 자세한 내용은 [HTTP 커넥터를 참조](connectors-native-http.md#authentication)하세요. |

<br>

**출력 세부 정보**

웹후크 요청

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| headers |object |웹후크 요청 헤더 |
| body |object |웹후크 요청 개체 |
| 상태 코드 |int |웹후크 요청 상태 코드 |

## <a name="webhook-actions"></a>웹후크 작업
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](connectors-overview.md)  커넥터에는 1개의 가능한 동작이 있습니다.

|  동작을 나열합니다. | 설명 |
| --- | --- |
| HTTP 웹후크 |필요에 따라 URL을 호출할 수 있는 서비스에 대한 콜백 URL을 구독하여 워크플로 단계를 다시 시작합니다. |

### <a name="action-details"></a>작업 세부 정보
웹후크 커넥터에는 1개의 가능한 동작이 있습니다. 아래에는 동작, 동작의 필수 및 선택 사항 입력 필드, 사용과 연결된 해당 출력 세부 정보에 대한 정보가 나와 있습니다.

#### <a name="http-webhook"></a>HTTP 웹후크
필요에 따라 URL을 호출할 수 있는 서비스에 대한 콜백 URL을 구독하여 워크플로 단계를 다시 시작합니다.
*는 필수 필드를 의미합니다.

| 표시 이름 | 속성 이름 | 설명 |
| --- | --- | --- |
| 구독 메서드* |메서드 |구독 요청에 사용할 HTTP 메서드 |
| 구독 URI* |uri |구독 요청에 사용할 HTTP URI |
| 구독 취소 메서드* |메서드 |구독 취소 요청에 사용할 HTTP 메서드 |
| 구독 취소 URI* |uri |구독 취소 요청에 사용할 HTTP URI |
| 구독 본문 |body |구독의 HTTP 요청 본문 |
| 구독 헤더 |headers |구독의 HTTP 요청 헤더 |
| 구독 인증 |인증 |구독에 사용할 HTTP 인증 자세한 내용은 [HTTP 커넥터를 참조](connectors-native-http.md#authentication)하세요. |
| 구독 취소 본문 |body |구독 취소의 HTTP 요청 본문 |
| 구독 취소 헤더 |headers |구독 취소의 HTTP 요청 헤더 |
| 구독 취소 인증 |authentication |구독 취소에 사용할 HTTP 인증 자세한 내용은 [HTTP 커넥터를 참조](connectors-native-http.md#authentication)하세요. |

<br>

**출력 세부 정보**

웹후크 요청

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| headers |object |웹후크 요청 헤더 |
| body |object |웹후크 요청 개체 |
| 상태 코드 |int |웹후크 요청 상태 코드 |

- - -
## <a name="next-steps"></a>다음 단계
논리 앱 및 커뮤니티를 사용하여 앞으로 이동하는 방법에 대한 세부 정보는 다음과 같습니다.

## <a name="create-a-logic-app"></a>논리 앱 만들기
지금 플랫폼을 사용해 보고 [논리 앱을 만드세요](../app-service-logic/app-service-logic-create-a-logic-app.md) . [API 목록](apis-list.md)에서 논리 앱의 사용 가능한 다른 커넥터를 확인할 수 있습니다.



<!--HONumber=Nov16_HO3-->


