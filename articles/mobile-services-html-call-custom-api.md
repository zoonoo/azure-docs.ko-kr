<properties urlDisplayName="Call a custom API from the client" pageTitle="HTML 클라이언트에서 사용자 지정 API 호출 | 모바일 서비스" metaKeywords="" description="사용자 지정 API를 정의한 다음 Microsoft Azure 모바일 서비스를 사용하는 HTML 앱에서 이를 호출하는 방법에 대해 알아봅니다." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="jparrel" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# HTML 응용 프로그램에서 사용자 지정 API 호출

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

이 항목에서는 HTML 응용 프로그램에서 사용자 지정 API를 호출하는 방법을 보여 줍니다. 사용자 지정 API는 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능을 노출하는 사용자 지정 끝점을 정의할 수 있게 합니다. 사용자 지정 API를 사용하면 HTTP 메시지 헤더 읽기와 설정 및 JSON 이외의 메시지 본문 형식 정의를 비롯하여 더 세밀하게 메시징을 제어할 수 있습니다.

이 항목에서 만드는 사용자 지정 API는 테이블의 모든 todo 항목에 대해 완료 플래그를 `true`로 설정하는 단일 POST 요청을 보내는 기능을 제공합니다. 이 사용자 지정 API가 없으면 클라이언트는 테이블의 각 todo 항목에 대해 플래그를 업데이트하도록 개별 요청을 보내야 합니다.

[모바일 서비스 시작] 또는 [데이터 작업 시작] 자습서를 마쳤을 때 만든 앱에 이 기능을 추가합니다. 그러려면 다음 단계를 완료합니다.

1. [사용자 지정 API 정의]
2. [사용자 지정 API를 호출하도록 앱 업데이트]
3. [앱 테스트] 

이 자습서는 모바일 서비스 빠른 시작을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작] 또는 [데이터 작업 시작]을 완료해야 합니다.

## <a name="define-custom-api"></a>사용자 지정 API 정의

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

<h2><a name="update-app"></a>사용자 지정 API를 호출하도록 앱 업데이트</h2>

1. 텍스트 편집기에서 index.html 파일을 열고 `buttonRefresh`라는 **button** 요소를 찾아서 바로 오른쪽에 다음과 같은 새 요소를 추가합니다. 

		<button id="buttonCompleteAll">Complete All</button> 

	이 코드는 페이지에 새 단추를 추가합니다. 

2. page.js에서 **refreshTodoItems** 함수 뒤에 다음 코드를 추가합니다.

		var completeAllTodoItems = function () {
			// Asynchronously call the custom API using the POST method.
			client.invokeApi("completeall", {
				body: null,
				method: "post"
			}).done(function (results) {
				var message = results.result.count + " item(s) marked as complete.";
				alert(message);
				refreshTodoItems();
			}, function(error) {
				alert(error.message);
			});
		};

		$('#buttonCompleteAll').click(function () {
			completeAllTodoItems();
		});

	이 메서드는 새 단추의 **Click** 이벤트를 처리합니다. **invokeApi** 메서드가 클라이언트에서 호출되어 POST 요청을 새 사용자 지정 API로 보냅니다. 사용자 지정 API에서 반환하는 결과는 오류와 마찬가지로 메시지 대화 상자에 표시됩니다.

## <a name="test-app"></a>앱 테스트

1. 브라우저를 새로 고칩니다.

2. **TodoItem 삽입**에 텍스트를 입력하고 **저장**을 클릭합니다.

3. 목록에 todo 항목을 여러 개 추가할 때까지 이전 단계를 반복합니다.

4. **모두 완료** 단추를 클릭합니다. 완료 표시된 항목 수를 나타내는 메시지 대화 상자가 표시되고 필터링된 쿼리가 다시 실행되어 목록에서 모든 항목을 지웁니다.

## 다음 단계

지금까지 사용자 지정 API를 만들고 HTML 앱에서 호출했습니다. 이제 다음 모바일 서비스 항목에 대해 자세히 알아보세요.

* [모바일 서비스 서버 스크립트 참조]
  <br/>사용자 지정 API 만들기에 대해 자세히 알아봅니다.

<!-- Anchors. -->
[사용자 지정 API 정의]: #define-custom-api
[사용자 지정 API를 호출하도록 앱 업데이트]: #update-app
[앱 테스트]: #test-app
[다음 단계]: #next-steps

<!-- URLs. -->
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
[내 앱 대시보드]: http://go.microsoft.com/fwlink/?LinkId=262039
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-html-get-started
[데이터 작업 시작]: /ko-kr/documentation/articles/mobile-services-html-get-started-data
