<properties
	pageTitle="JavaScript 백엔드로 모바일 서비스 사용자의 서비스 쪽 권한 부여 | 모바일 개발자 센터"
	description="Azure 모바일 서비스의 JavaScript 백엔드에서 사용자에게 권한을 부여하는 방법에 대해 알아봅니다."
	services="mobile-services"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.topic="article"
	ms.date="2/18/2015"
	ms.author="krisragh"/>

# 모바일 서비스에서 사용자의 서비스 쪽 권한 부여

> [AZURE.SELECTOR-LIST(플랫폼 | 백 엔드)]
- [(모든 |.NET)](/ko-kr/documentation/articles/mobile-services-dotnet-backend-service-side-authorization/)
- [(모든 | Javascript)](/ko-kr/documentation/articles/mobile-services-javascript-backend-service-side-authorization/)

이 항목에서는 사용자에게 권한을 부여하기 위해 서버 쪽 스크립트를 사용하는 방법을 보여줍니다. 이 자습서에서는 Azure 모바일 서비스로 스크립트를 등록하고 필터 쿼리 사용자 ID를 기반으로 쿼리를 필터링하며 사용자에게 자신의 데이터에 대한 액세스만 제공합니다.

이 자습서는 모바일 서비스 빠른 시작 및 [기존 모바일 서비스 앱에 인증 추가] 자습서를 기반으로 합니다. [기존 모바일 서비스 앱에 인증 추가]를 먼저 작성하세요.

## <a name="register-scripts"></a>스크립트 등록

1. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 다음 모바일 서비스를 클릭합니다. **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

2. **스크립트**를 클릭한 후 **삽입** 작업을 선택합니다.

   	![][2]

3. 기존 스크립트를 다음 함수로 바꾼 후 **저장**을 클릭합니다. 이 스크립트는 삽입하기 전에 인증된 사용자의 사용자 ID를 항목에 추가합니다.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }


    > [AZURE.NOTE] [이 작업을 하려면 동적 스키마를 설정](https://msdn.microsoft.com/library/azure/jj193175.aspx)해야 합니다. 이 설정은 새 모바일 서비스에 대해 기본적으로 설정됩니다.

5. 마찬가지로, 기존 **읽기** 작업을 다음 함수로 대체합니다. 이 스크립트는 사용자가 자신이 직접 삽입한 항목만 받을 수 있도록 반환된 TodoItem 개체를 필터링합니다.

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }




## <a name="test-app"></a>앱 테스트

1. 이제 클라이언트 쪽 앱을 실행하면 이미 이전 자습서에서부터 _TodoItem_ 테이블에 항목이 있었을 경우에도 항목이 반환되지 않습니다. 이런 현상은 이전 항목이 사용자 ID 열 없이 삽입되었기 때문에 발생하며 이제는 null 값을 가집니다. 새로 추가 된 항목의 _TodoItem_ 테이블에 관련된 userId 값이 있는지 확인합니다.

2. 추가 로그인 계정이 있는 경우 앱을 닫고 삭제했다가 다시 실행하여 사용자가 자신의 고유 데이터만 볼 수 있는지 확인합니다. 로그인 자격 증명 대화 상자가 표시되면 다른 로그인을 입력하고 이전 로그인에서 입력한 항목이 표시되지 않는지 확인합니다.

<!-- Anchors. -->
[서버 스크립트 등록]: #register-scripts
[다음 단계]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->

[Windows 푸시 알림 및 Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[내 앱 대시보드]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started/#create-new-service
[기존 앱에 모바일 서비스 추가]: /ko-kr/develop/mobile/tutorials/get-started-with-data-ios
[기존 모바일 서비스 앱에 인증 추가]: /ko-kr/develop/mobile/tutorials/get-started-with-users-ios
[기존 앱에 푸시 알림 추가]: /ko-kr/develop/mobile/tutorials/get-started-with-push-ios

[Azure 관리 포털]: https://manage.windowsazure.com/

<!--HONumber=45--> 
