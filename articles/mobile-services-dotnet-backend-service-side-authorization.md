<properties
	pageTitle=".NET 백엔드로 모바일 서비스 사용자의 서비스 쪽 권한 부여 | 모바일 개발자 센터"
	description="Azure 모바일 서비스의.NET 백엔드에서 사용자에게 권한을 부여하는 방법에 대해 알아봅니다."
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
- [(모든 |.NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(모든 | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

이 항목에서는 사용자에게 권한을 부여하기 위해 서버 쪽 논리를 사용하는 방법을 보여줍니다.  이 자습서에서는 .NET의 데이터 액세스 방법을 수정하고 사용자 ID를 기반으로 쿼리를 필터링하며 사용자에게 자신의 데이터에 대한 액세스만 제공합니다.

이 자습서는 모바일 서비스 빠른 시작 및 [기존 모바일 서비스 앱에 인증 추가] 자습서를 기반으로 합니다. [기존 모바일 서비스 앱에 인증 추가]를 먼저 작성하세요.

## <a name="register-scripts"></a>데이터 액세스 방법 수정

1. Visual Studio에서 모바일 프로젝트를 열고 DataObjects 폴더를 확장한 다음 **TodoItem.cs**를 엽니다. **TodoItem** 클래스가 데이터 개체를 정의하면 필터링에 사용할 **UserId** 속성을 추가해야 합니다. 다음과 같은 새 UserId 속성을 **TodoItem** 클래스에 추가합니다.

		public string UserId { get; set; }

	>[AZURE.NOTE] 이 데이터 모델을 변경하고 데이터베이스에 기존 데이터를 유지하려면 [Code First 마이그레이션](/ ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations)을 사용해야 합니다.

2. Visual Studio에서 컨트롤러 폴더를 확장하고 **TodoItemController.cs**를 엽니다. **PostTodoItem** 메서드를 찾아서 메서드 시작 부분에 다음 코드를 추가합니다. 이 코드는 TodoItem 테이블에 삽입되기 전에 인증된 사용자의 사용자 ID를 항목에 추가합니다.

			// Get the logged in user
			var currentUser = User as ServiceUser;

			// Set the user ID on the item
			item.UserId = currentUser.Id;

3. **GetAllTodoItems** 메서드를 찾아서 기존 **반환** 문을 다음 줄의 코드로 대체합니다. 이 쿼리는 반환된 TodoItem 개체를 필터링하여 각 사용자가 자신이 삽입한 항목만 수신하도록 합니다.

				// Get the logged in user
				var currentUser = User as ServiceUser;

				return Query().Where(todo => todo.UserId == currentUser.Id);

4. Azure에 모바일 서비스 프로젝트를 다시 게시합니다.


## <a name="test-app"></a>앱 테스트

1. 이제 클라이언트 쪽 앱을 실행하면 이미 이전 자습서에서부터 데이터베이스에 항목이 있었을 경우에도 항목이 반환되지 않습니다. 이런 현상은 이전 항목이 사용자 ID 열 없이 삽입되었기 때문에 발생하며 이제는 null 값을 가집니다.

2. 추가 로그인 계정이 있는 경우 앱을 닫고 삭제했다가 다시 실행하여 사용자가 자신의 고유 데이터만 볼 수 있는지 확인합니다. 로그인 자격 증명 대화 상자가 표시되면 다른 로그인을 입력하고 이전 로그인에서 입력한 항목이 표시되지 않는지 확인합니다.



<!-- Anchors. -->
[서버 스크립트 등록]: #register-scripts
[다음 단계]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[기존 모바일 서비스 앱에 인증 추가]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

<!--HONumber=45--> 
