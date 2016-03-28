<properties
	pageTitle=".NET 백 엔드 모바일 서비스에서 사용자의 서비스 쪽 권한 부여 | Microsoft Azure"
	description=".NET 백엔드 모바일 서비스에서 사용자 권한 부여 액세스 권한을 제한하는 방법에 대해 알아보기"
	services="mobile-services"
	documentationCenter="windows"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.topic="article"
	ms.devlang="dotnet"
	ms.date="03/09/2016"
	ms.author="krisragh"/>

# 모바일 서비스에서 사용자의 서비스 쪽 권한 부여
> [AZURE.SELECTOR]
- [.NET 백 엔드](mobile-services-dotnet-backend-service-side-authorization.md)
- [JavaScript 백 엔드](mobile-services-javascript-backend-service-side-authorization.md)

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> 이 항목에 해당하는 모바일 앱 버전은 Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용 항목에서 [방법: 인증된 사용자에 대한 데이터 액세스 제한](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#authorize)을 참조하세요.

이 항목에서는 사용자에게 권한을 부여하기 위해 서버 쪽 논리를 사용하는 방법을 보여줍니다. 이 자습서에서는 테이블 컨트롤러를 수정하고 사용자 ID를 기반으로 쿼리를 필터링하며 사용자에게 자신의 데이터에 대한 액세스만 제공합니다. 사용자 ID에 따라 사용자의 쿼리 결과를 필터링하는 방식이 가장 기본적인 형태의 권한 부여입니다. 특정 시나리오에 따라, 사용자 또는 역할 테이블을 만들어 지정된 사용자에게 액세스가 허용된 끝점과 같은 보다 자세한 사용자 권한 부여 정보를 추적할 수도 있습니다.

이 자습서는 모바일 서비스 퀵 스타트 및 [기존 모바일 서비스 앱에 인증 추가] 자습서를 기반으로 합니다. [기존 모바일 서비스 앱에 인증 추가]를 먼저 완료하세요.

## <a name="register-scripts"></a>데이터 액세스 방법 수정

1. Visual Studio에서 모바일 프로젝트를 열고 DataObjects 폴더를 확장한 다음 **TodoItem.cs**를 엽니다. **TodoItem** 클래스가 데이터 개체를 정의합니다. 필터링에 사용하려면 **UserId** 속성을 추가해야 합니다. 다음과 같은 새 UserId 속성을 **TodoItem** 클래스에 추가합니다.

		public string UserId { get; set; }

	>[AZURE.NOTE] 이 데이터 모델을 변경하고 데이터베이스에서 기존 데이터를 유지하려면 [Code First 마이그레이션](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)을 사용해야 합니다.

2. Visual Studio에서 컨트롤러 폴더를 확장하고, **TodoItemController.cs**를 열고, 아래 문을 사용하여 다음 내용을 추가합니다.

		using Microsoft.WindowsAzure.Mobile.Service.Security;

3. **PostTodoItem** 메서드를 찾아 이 메서드의 시작 부분에 다음 코드를 추가합니다.

		// Get the logged in user
		var currentUser = User as ServiceUser;

		// Set the user ID on the item
		item.UserId = currentUser.Id;

	이 코드는 TodoItem 테이블에 삽입되기 전에 인증된 사용자의 사용자 ID를 항목에 추가합니다.

3. **GetAllTodoItems** 메서드를 찾아 기존 **return** 문을 다음 코드 줄로 바꿉니다.

		// Get the logged in user
		var currentUser = User as ServiceUser;

		return Query().Where(todo => todo.UserId == currentUser.Id);

	이 쿼리는 반환된 TodoItem 개체를 필터링하여 각 사용자가 자신이 삽입한 항목만 수신하도록 합니다.

4. Azure에 모바일 서비스 프로젝트를 다시 게시합니다.


## <a name="test-app"></a>앱 테스트

1. 이제 클라이언트 쪽 앱을 실행하면 이미 이전 자습서에서부터 데이터베이스에 항목이 있었을 경우에도 항목이 반환되지 않습니다. 이런 현상은 이전 항목이 사용자 ID 열 없이 삽입되었기 때문에 발생하며 이제는 null 값을 가집니다.

2. 추가 로그인 계정이 있는 경우 앱을 닫고 삭제했다가 다시 실행하여 사용자가 자신의 고유 데이터만 볼 수 있는지 확인합니다. 로그인 자격 증명 대화 상자가 표시되면 다른 로그인을 입력하고 이전 로그인에서 입력한 항목이 표시되지 않는지 확인합니다.



<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[기존 모바일 서비스 앱에 인증 추가]: mobile-services-dotnet-backend-ios-get-started-users.md

<!----HONumber=AcomDC_0316_2016-->