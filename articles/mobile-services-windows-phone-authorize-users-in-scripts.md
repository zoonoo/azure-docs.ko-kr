<properties pageTitle="서비스 쪽 권한 부여(Windows Phone) | 모바일 개발자 센터" description="Azure 모바일 서비스의 JavaScript 백 엔드에서 사용자에게 권한을 부여하는 방법에 대해 알아봅니다." services="" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga"/>

# 모바일 서비스 사용자의 서비스 쪽 권한 부여

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>이 항목에서는 서버 스크립트를 사용하여 인증된 사용자에게 Windows Phone 8 앱에서 Azure 모바일 서비스의 데이터에 액세스할 수 있는 권한을 부여하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스에 스크립트를 등록하여 인증된 사용자의 userId를 기반으로 쿼리를 필터링함으로써 각 사용자가 자신의 고유 데이터만 볼 수 있도록 만듭니다.</p>
<p>이 자습서는 모바일 서비스 퀵 스타트를 기반으로 하며 이전 자습서인 <a href="/ko-kr/develop/mobile/tutorials/get-started-with-users-wp8">인증 시작</a>에 내용을 추가했습니다. 이 자습서를 시작하기 전에 먼저 <a href="/ko-kr/develop/mobile/tutorials/get-started-with-users-wp8">인증 시작</a>을 완료해야 합니다.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-scripts-for-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="dev-onpage-video"><span class="icon">비디오 재생</span></a> <span class="time">오후 3:00</span></div>
</div> 

## <a name="register-scripts"></a>스크립트 등록
퀵 스타트 앱은 데이터를 읽고 삽입하기 때문에 TodoItem 테이블에 대해 이 작업을 수행하는 스크립트를 등록해야 합니다.

1. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다. 

   	![][0]

2. **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

   	![][1]

3. **스크립트**를 클릭한 후 **삽입** 작업을 선택합니다.

   	![][2]

4. 기존 스크립트를 다음 함수로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    이 스크립트는 항목에 userId 값을 추가합니다. 이 값은 TodoItem 테이블에 삽입되기 전의 인증된 사용자의 사용자 ID입니다. 

    > [AZURE.NOTE] 이 삽입 스크립트를 처음 실행할 때는 동적 스키마를 사용하도록 설정해야 합니다. 동적 스키마를 사용하도록 설정하면 첫 번째 실행 시 모바일 서비스가 **userId** 열을 **TodoItem** 테이블에 자동으로 추가합니다. 동적 스키마는 기본적으로 새 모바일 서비스에 대해 사용하도록 설정되어 있으며 Windows Phone 스토어에 앱을 게시하기 전에 이 스키마를 사용하지 않도록 설정해야 합니다.


5. 3단계와 4단계를 반복하여 기존 **읽기** 작업을 다음 함수로 바꿉니다.

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	이 스크립트는 반환된 TodoItem 개체를 필터링하여 각 사용자가 자신이 삽입한 항목만 수신하도록 합니다.

## 앱 테스트

1. Visual Studio 2012 Express for Windows Phone에서 [인증 시작] 자습서를 완료할 때 수정한 프로젝트를 엽니다.

2. F5 키를 눌러 앱을 실행한 후 선택한 ID 공급자로 앱에 로그온합니다. 

   	이전 자습서의 TodoItem 테이블에 이미 항목이 있지만, 이번에는 항목이 반환되지 않습니다. 이전 항목이 userId 열 없이 삽입되어 지금 null 값을 가지기 때문에 이런 현상이 발생합니다.

3. 앱에서 텍스트 상자에 텍스트를 입력하고 **저장**을 클릭합니다.

   	![][3]

   	모바일 서비스에서 TodoItem 테이블의 텍스트 및 userId가 모두 삽입됩니다. 새 항목에 올바른 userId 값이 있기 때문에 값이 모바일 서비스에서 반환됩니다.

5. [관리 포털][Azure 관리 포털]의 **todoitem** 테이블로 돌아가 **찾아보기**를 클릭하고 새로 추가된 각 항목에 관련된 userId 값이 있는지 확인합니다.

## 다음 단계

이제 인증 관련 작업의 기본 사항을 설명하는 자습서를 마쳤습니다. 다음의 모바일 서비스 항목에 대해서도 자세히 알아보세요.

* [데이터 시작]
  <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

* [푸시 알림 시작] 
  <br/>앱에 기본적인 푸시 알림을 보내는 방법을 알아봅니다.

* [모바일 서비스 서버 스크립트 참조]
  <br/>서버 스크립트의 등록 및 사용에 대해 자세히 알아봅니다.

<!-- Anchors. -->
[서버 스크립트 등록]: #register-scripts
[다음 단계]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup-wp8.png

<!-- URLs. -->
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
[내 응용 프로그램 대시보드]: http://go.microsoft.com/fwlink/?LinkId=262039
[모바일 서비스 시작](영문): /ko-kr/develop/mobile/tutorials/get-started/#create-new-service
[데이터 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-wp8
[인증 시작](영문): /ko-kr/develop/mobile/tutorials/get-started-with-users-wp8
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-wp8

[Azure 관리 포털]: https://manage.windowsazure.com/


<!--HONumber=42-->
