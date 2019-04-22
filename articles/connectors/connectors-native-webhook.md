---
title: 이벤트 기반 워크플로 또는 작업 만들기 - Azure Logic Apps | Microsoft Docs
description: 웹후크 및 Azure Logic Apps를 사용하여 이벤트 기반 워크플로 또는 작업 자동화
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.topic: article
tags: connectors
ms.date: 07/21/2016
ms.openlocfilehash: c3047000843e054e71ec1a80313118a25e7c4905
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895582"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>웹후크 및 Azure Logic Apps를 사용하여 이벤트 기반 워크플로 또는 작업 만들기

웹후크 동작 및 트리거를 사용하여 다음 작업을 수행하기 위한 흐름을 시작, 일시 중지 및 계속할 수 있습니다.

* 항목이 수신되자마자 [Azure 이벤트 허브](https://github.com/logicappsio/EventHubAPI)에서 트리거
* 워크플로를 계속하기 전에 승인을 대기합니다.

[웹후크를 지원하는 사용자 지정 API를 만드는 방법](../logic-apps/logic-apps-create-api-app.md)에 대해 자세히 알아봅니다.

## <a name="use-the-webhook-trigger"></a>웹후크 트리거 사용

[*트리거*](../connectors/apis-list.md)는 논리 앱에서 워크플로를 시작하는 이벤트입니다. 웹 후크 트리거는 이벤트를 기반으로 하는 새 항목에 대 한 폴링에 종속 되지 않습니다. 웹 후크 트리거를 통해 논리 앱을 저장 하는 경우 또는 사용 안 함 상태에서 논리 앱을 변경 하면 웹 후크 트리거 *구독* 지정한 서비스에 등록 하 여 끝점을 *콜백URL* 해당 서비스 또는 끝점을 사용 하 여 합니다. 트리거는 다음 필요에 따라 논리 앱을 실행 하는 URL을 사용 합니다. 같은 합니다 [요청 트리거](connectors-native-reqres.md), 예상한 이벤트가 발생 하면 즉시 논리 앱에서 발생 합니다. 트리거가 *구독 취소* 에서 논리 앱을 설정 하는 사용 하지 않도록 설정 하도록이 변경한 경우 또는 트리거를 제거 하 고 논리 앱을 저장 합니다.

논리 앱 디자이너에서 HTTP 트리거를 설정하는 방법을 보여 주는 예제는 다음과 같습니다. 이러한 단계에서는 [Logic Apps에서 사용되는 웹후크 구독 및 구독 취소 패턴](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)을 따라 API를 이미 배포했거나 액세스하고 있다고 가정합니다. 

**웹후크 트리거를 사용하려면**

1. **HTTP 웹후크** 트리거를 논리 앱의 첫 번째 단계로 추가합니다.
2. 웹후크 구독 및 구독 취소 호출에 대한 매개 변수를 입력합니다.

   이 단계는 [HTTP 동작](connectors-native-http.md) 형식과 동일한 패턴을 따릅니다.

     ![HTTP 트리거](./media/connectors-native-webhook/using-trigger.png)

3. 하나 이상의 동작을 추가합니다.
4. **저장**을 클릭하여 논리 앱을 게시합니다. 이 단계를 수행하면 이 Logic App을 트리거하는 데 필요한 콜백 URL을 사용하여 구독 엔드포인트가 호출됩니다.
5. 서비스에서 콜백 URL에 댛 `HTTP POST` 를 수행할 때마다 논리 앱이 실행됩니다(논리 앱에는 요청에 전달된 모든 데이터가 포함됨).

## <a name="use-the-webhook-action"></a>웹후크 동작 사용

[ *동작* ](../connectors/apis-list.md) 는 정의 된 작업 및 논리 앱 워크플로 통해 실행 합니다. 논리 앱 웹 후크 작업, 해당 작업을 실행 하는 경우 *구독* 지정한 서비스에 등록 하 여 끝점을 *콜백 URL* 해당 서비스 또는 끝점을 사용 하 여 합니다. 웹 후크 동작을 한 다음 해당 서비스가 실행 되는 논리 앱 다시 시작 하기 전에 URL을 호출 될 때까지 대기 합니다. 논리 앱 서비스 또는 이러한 경우에는 끝점에서 구독 취소: 

* 웹 후크 동작을 성공적으로 완료 되 면
* 논리 앱 실행 응답을 기다리는 동안 취소 되 면
* 앱 제한 논리 전에

예를 들어 합니다 [ **승인 전자 메일 보내기** ](connectors-create-api-office365-outlook.md) 작업은이 패턴을 따르는 웹 후크 동작의 예입니다. 이 패턴을 웹후크 동작을 통해 서비스로 확장할 수 있습니다. 

논리 앱 디자이너에서 웹후크 동작을 설정하는 방법을 보여 주는 예제는 다음과 같습니다. 이러한 단계에서는 [Logic Apps에서 사용되는 웹후크 구독 및 구독 취소 패턴](../logic-apps/logic-apps-create-api-app.md#webhook-actions)을 따라 API를 이미 배포했거나 액세스하고 있다고 가정합니다. 

**웹후크 동작을 추가하려면**

1. **다음 단계** > **동작 추가**를 선택합니다.

2. 검색 상자에 "웹후크"를 입력하여 **HTTP 웹후크** 동작을 찾습니다.

    ![쿼리 동작 선택](./media/connectors-native-webhook/using-action-1.png)

3. 웹후크 구독 및 구독 취소 호출에 대한 매개 변수를 입력합니다.

   이 단계는 [HTTP 동작](connectors-native-http.md) 형식과 동일한 패턴을 따릅니다.

     ![쿼리 동작 완료](./media/connectors-native-webhook/using-action-2.png)
   
   런타임 시 논리 앱은 해당 단계에 도달한 후 구독 엔드포인트를 호출합니다.

4. **저장**을 클릭하여 논리 앱을 게시합니다.

## <a name="technical-details"></a>기술 세부 정보

웹후크가 지원하는 트리거 및 동작에 대한 자세한 내용은 다음과 같습니다.

## <a name="webhook-triggers"></a>웹후크 트리거

| 액션(Action) | 설명 |
| --- | --- |
| HTTP 웹후크 |필요에 따라 URL을 호출할 수 있는 서비스에 대한 콜백 URL을 구독하여 논리 앱을 실행합니다. |

### <a name="trigger-details"></a>트리거 세부 정보

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
| 구독 취소 본문 |본문 |구독 취소의 HTTP 요청 본문 |
| 구독 취소 헤더 |headers |구독 취소의 HTTP 요청 헤더 |
| 구독 취소 인증 |authentication |구독 취소에 사용할 HTTP 인증 자세한 내용은 [HTTP 커넥터를 참조](connectors-native-http.md#authentication)하세요. |

**출력 세부 정보**

웹후크 요청

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| headers |object |웹후크 요청 헤더 |
| 본문 |object |웹후크 요청 개체 |
| 상태 코드 |ssNoversion |웹후크 요청 상태 코드 |

## <a name="webhook-actions"></a>웹후크 작업

| 액션(Action) | 설명 |
| --- | --- |
| HTTP 웹후크 |필요에 따라 URL을 호출할 수 있는 서비스에 대한 콜백 URL을 구독하여 워크플로 단계를 다시 시작합니다. |

### <a name="action-details"></a>작업 세부 정보

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
| 구독 취소 본문 |본문 |구독 취소의 HTTP 요청 본문 |
| 구독 취소 헤더 |headers |구독 취소의 HTTP 요청 헤더 |
| 구독 취소 인증 |authentication |구독 취소에 사용할 HTTP 인증 자세한 내용은 [HTTP 커넥터를 참조](connectors-native-http.md#authentication)하세요. |

**출력 세부 정보**

웹후크 요청

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| headers |object |웹후크 요청 헤더 |
| 본문 |object |웹후크 요청 개체 |
| 상태 코드 |ssNoversion |웹후크 요청 상태 코드 |

## <a name="next-steps"></a>다음 단계

* [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [다른 커넥터 찾기](apis-list.md)
