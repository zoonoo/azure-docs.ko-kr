---
title: '자습서: 문서 개인 설정 - Custom Decision Service'
titlesuffix: Azure Cognitive Services
description: 상황에 맞는 의사 결정을 위한 문서 개인 설정의 자습서입니다.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: d8ddafe20ff93e7ae4d51e2180bbd40447729234
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55983035"
---
# <a name="tutorial-article-personalization-for-contextual-decision-making"></a>자습서: 상황에 맞는 의사 결정을 위한 문서 개인 설정

이 자습서에서는 웹 사이트의 앞 페이지에 표시되는 기사 선택을 개인 설정하는 방법에 집중합니다. 예를 들어 Custom Decision Service는 앞 페이지의 *여러* 아티클에 영향을 줍니다. 아마도 페이지는 정치 및 스포츠를 다루는 뉴스 웹 사이트입니다. 정치, 스포츠 및 최신 등 아티클의 세 가지 순위 목록을 보여줍니다.

## <a name="applications-and-action-sets"></a>애플리케이션 및 작업 집합

시나리오를 프레임워크에 맞추는 방법은 다음과 같습니다. app-politics, app-sports 및 app-recent 등 최적화된 각 목록에 하나씩 세 개의 애플리케이션이 있다고 가정합니다. 각 애플리케이션에 대한 후보 아티클을 지정하기 위해 정치 및 스포츠에 하나씩 두 개의 작업 집합이 있습니다. app-recent에 설정된 작업은 다른 두 집합의 합집합으로 자동으로 제공됩니다.

> [!TIP]
> 작업 집합은 Custom Decision Service에서 애플리케이션 간에 공유할 수 있습니다.

## <a name="prepare-action-set-feeds"></a>작업 집합 피드 준비

Custom Decision Service는 고객이 제공한 RSS 또는 Atom 피드를 통해 작업 집합을 제공합니다. 정치 및 스포츠에 하나씩 두 개의 피드를 제공합니다. `http://www.domain.com/feeds/<feed-name>`에서 제공된다고 가정합니다.

각 피드는 아티클 목록을 제공합니다. RSS에서 각각은 다음과 같이 `<item>` 요소에 의해 지정됩니다.

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

아티클 순서가 중요합니다. 아티클을 정렬해야 하는 방법에 대한 추측으로 기본 순위를 지정합니다. 기본 순위는 대시보드에서 성능 비교에 사용됩니다.

피드 형식에 대한 자세한 내용은 [API 참조](custom-decision-service-api-reference.md#action-set-api-customer-provided)를 참조하세요.

## <a name="register-a-new-app"></a>새 앱 등록

1. [Microsoft 계정](https://portal.ds.microsoft.com/)으로 로그인합니다. 리본에서 **내 포털**을 클릭합니다.

2. 새 애플리케이션을 등록하려면 **새 앱** 단추를 클릭합니다.

    ![Custom Decision Service 포털](./media/custom-decision-service-tutorial/portal.png)

3. **앱 ID** 텍스트 상자에서 애플리케이션에 고유한 이름을 입력합니다. 다른 고객이 이 이름을 이미 사용하는 경우 시스템은 다른 앱 ID를 선택하도록 요청합니다. **고급** 확인란을 선택하고, Azure 저장소 계정에 대한 [연결 문자열](../../storage/common/storage-configure-connection-string.md)을 입력합니다. 일반적으로 모든 애플리케이션에 동일한 저장소 계정을 사용합니다.

    ![새 앱 대화 상자](./media/custom-decision-service-tutorial/new-app-dialog.png)

    위의 시나리오에서 세 개의 애플리케이션을 모두 등록하면 다음과 같이 나열됩니다.

    ![앱 목록](./media/custom-decision-service-tutorial/apps.png)

    **앱** 단추를 클릭하여 이 목록으로 돌아올 수 있습니다.

4. **새 앱** 대화 상자에서 작업 피드를 지정합니다. **피드** 단추를 클릭하고 **새 피드** 단추를 클릭하여 작업 피드를 지정할 수도 있습니다. 새 피드에 **이름**을 입력하고 피드를 제공하는 **URL**을 입력하고 **새로 고침 시간**을 입력합니다. 새로 고침 시간은 Custom Decision Service가 피드를 새로 고쳐야 하는 빈도를 지정합니다.

    ![새 피드 대화 상자](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    작업 피드는 지정되는 위치에 관계 없이 모든 앱에서 사용할 수 있습니다. 시나리오에서 작업 피드를 모두 지정하면 다음과 같이 나열됩니다.

    ![피드 목록](./media/custom-decision-service-tutorial/feeds.png)

    **피드** 단추를 클릭하여 이 목록으로 돌아올 수 있습니다.

## <a name="use-the-apis"></a>API 사용

Custom Decision Service는 순위 API를 통해 아티클의 순위를 지정합니다. 이 API를 사용하려면 기본 페이지의 HTML 헤드에 다음 코드를 삽입합니다.

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

순위 API의 HTTP 응답은 JSONP 형식 문자열입니다. 예를 들어 앱 정책의 경우 문자열은 다음과 같습니다.

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

그런 다음, 브라우저는 이 문자열을 `callback()` 함수에 대한 호출로 실행합니다. `callback()` 함수의 `data` 인수에는 앱 ID 및 렌더링되어야 하는 URL의 순위가 포함되어 있습니다. 특히 `callback()`은 `data.appId`를 사용하여 세 가지 애플리케이션을 구별해야 합니다. `eventId`는 Custom Decision Service에서 내부적으로 사용되며 해당하는 항목을 클릭하여 제공된 순위와 일치시킵니다.

> [!TIP]
> `callback()`은 `lastRefresh` 필드를 사용하여 새로 고칠 각 작업 피드를 확인할 수 있습니다. 지정된 피드가 충분하지 않으면 `callback()`은 제공된 순위를 무시하고,이 피드를 직접 호출하고, 피드에서 제공하는 기본 순위를 사용할 수 있습니다.

순위 API에서 제공하는 사양 및 추가 옵션에 대한 자세한 내용은 [API 참조](custom-decision-service-api-reference.md)를 참조하세요.

보상 API를 호출하여 사용자가 선택한 상위 아티클이 반환됩니다. 상위 아티클 선택이 수신되면 첫 페이지에서 다음 코드를 호출해야 합니다.

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

클릭 처리 코드에서 `appId` 및 `eventId`를 사용하려면 주의해야 합니다. 예를 들어 다음과 같이 `callback()` 함수를 구현할 수 있습니다.

```javascript
function callback(data) {
    $.map(data.ranking, function (element) {
        //custom rendering function given content info
        render({appId:data.appId,
                article:element,
                onClick: element.id != data.rewardAction ? null :
                   function() {
                       $.ajax({
                       type: "POST",
                       url: '//ds.microsoft.com/api/v2/' + data.appId + '/reward/' + data.eventId,
                       contentType: "application/json" })}
                });
}}
```

이 예제에서는 `render()` 함수를 구현하여 지정된 애플리케이션에 지정된 아티클을 렌더링합니다. 이 함수는 순위 API의 형식으로 앱 ID 및 아티클을 입력합니다. `onClick` 매개 변수는 한 번의 클릭을 처리하기 위해 `render()`에서 호출되어야 하는 함수입니다. 클릭이 맨 위 슬롯에 적용되는지 확인합니다. 그런 다음, 적절한 앱 ID 및 이벤트 ID를 사용하여 보상 API를 호출합니다.

## <a name="next-steps"></a>다음 단계

* 제공되는 기능에 대한 자세한 내용은 [API 참조](custom-decision-service-api-reference.md)에서 확인할 수 있습니다.
