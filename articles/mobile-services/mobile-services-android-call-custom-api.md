<properties 
	pageTitle="Android 클라이언트에서 사용자 지정 API 호출 | 모바일 개발자 센터" 
	description="사용자 지정 API를 정의한 다음 Azure 모바일 서비스를 사용하는 Android 앱에서 이를 호출하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="ricksal"/>

# 클라이언트에서 사용자 지정 API 호출

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

이 항목에서는 Android 앱에서 사용자 지정 API를 호출하는 방법을 보여 줍니다. 사용자 지정 API는 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능을 노출하는 사용자 지정 끝점을 정의할 수 있게 합니다. 사용자 지정 API를 사용하면 HTTP 메시지 헤더 읽기와 설정 및 JSON 이외의 메시지 본문 형식 정의를 비롯하여 더 효율적으로 메시징을 제어할 수 있습니다.

이 항목에서 만든 사용자 지정 API를 통해 모바일 서비스 테이블의 모든 todo 항목에 대해 *completed* 플래그를 `true`(으)로 설정하는 단일 POST 요청을 보낼 수 있습니다. 이 사용자 정의 API가 없으면 클라이언트는 테이블의 각 할 일 항목에 대해 플래그를 업데이트하도록 개별 요청을 보내야 합니다.

[모바일 서비스 시작] 또는 [데이터 시작] 자습서를 마쳤을 때 만든 앱에 이 기능을 추가합니다.


>[AZURE.NOTE]완성된 앱의 소스 코드를 참조하려는 경우 <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/CallCustomApi/Android" target="_blank">여기</a>로 이동하세요.

##필수 조건

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## <a name="define-custom-api"></a>사용자 지정 API 정의

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]


[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]


## 다음 단계

이 항목에서는 **invokeApi** 메서드를 사용하여 Android 앱에서 매우 간단한 사용자 지정 API를 호출하는 방법을 보여 줍니다. **invokeApi** 메서드 사용에 대한 자세한 내용은 게시물 [Azure 모바일 서비스의 사용자 지정 API](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx)를 참조하세요.

다음 모바일 서비스 항목에 대해서도 자세히 알아보세요.

* [모바일 서비스 서버 스크립트 참조] <br/>사용자 지정 API 만들기에 대해 알아봅니다.

* [원본 제어에 서버 스크립트 저장] <br/> 원본 제어 기능을 사용하여 더 쉽고 안전하게 사용자 지정 API 스크립트 코드를 개발하고 게시하는 방법을 알아봅니다.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[모바일 서비스 시작]: mobile-services-android-get-started.md
[데이터 시작]: mobile-services-android-get-started-data.md
[Get started with authentication]: mobile-services-android-get-started-users.md
[Get started with push notifications]: ../mobile-services-android-get-started-push.md

[원본 제어에 서버 스크립트 저장]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO3-->