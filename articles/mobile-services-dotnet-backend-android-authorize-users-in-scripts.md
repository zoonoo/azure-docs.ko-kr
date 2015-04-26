<properties 
	pageTitle="서비스 쪽 권한 부여(Android) | 모바일 개발자 센터" 
	description="Azure 모바일 서비스의 .NET 백 엔드에서 사용자에게 권한을 부여하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="10/20/2014" 
	ms.author="glenga"/>

# 모바일 서비스 사용자의 서비스 쪽 권한 부여

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

이 항목에서는 인증된 사용자에게 Android 앱에서 Azure 모바일 서비스의 데이터에 액세스할 수 있는 권한을 부여하는 방법을 보여 줍니다. 이 자습서에서는 인증된 사용자의 userId를 기반으로 쿼리를 필터링하는 코드를 컨트롤러의 데이터 액세스 방법에 추가함으로써 각 사용자가 자신의 고유 데이터만 볼 수 있도록 만듭니다.

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 하며 이전 자습서인 [인증 시작]에 내용을 추가했습니다. 이 자습서를 시작하기 전에 먼저 [인증 시작]을 완료해야 합니다.  

## <a name="register-scripts"></a>데이터 액세스 방법 수정

[AZURE.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]


## 앱 테스트

1. Eclipse에서 [인증 시작] 자습서를 완료할 때 수정한 프로젝트를 엽니다.

2. **실행** 메뉴에서 **실행**을 클릭하여 Android 에뮬레이터에서 프로젝트를 시작합니다.

   이전 자습서의 TodoItem 테이블에 이미 항목이 있지만, 이번에는 항목이 반환되지 않습니다. 이전 항목이 userId 열 없이 삽입되어 지금 null 값을 가지기 때문에 이런 현상이 발생합니다.

3. 앱에서 **할 일 항목 추가**에 텍스트를 입력하고 **저장**을 클릭합니다.

   	![][3]

   모바일 서비스에서 TodoItem 테이블의 텍스트 및 userId가 모두 삽입됩니다. 새 항목에 올바른 userId 값이 있기 때문에 값이 모바일 서비스에서 반환되어 두 번째 열에 표시됩니다.

5. [관리 포털][Azure 관리 포털]의 **todoitem** 테이블로 돌아가 **찾아보기**를 클릭하고 새로 추가된 각 항목에 관련된 userId 값이 있는지 확인합니다.

6. (선택 사항) 추가 로그인 계정이 있는 경우 앱을 닫았다가 다시 실행하여 사용자가 자신의 고유 데이터만 볼 수 있는지 여부를 확인할 수 있습니다. 로그인 자격 증명 대화 상자가 표시되면 다른 로그인을 입력한 후 이전 계정으로 입력한 항목이 표시되지 않는지 확인합니다.

## 다음 단계

이제 인증 관련 작업의 기본 사항을 설명하는 자습서를 마쳤습니다. 다음의 모바일 서비스 항목에 대해서도 자세히 알아보세요.

* [데이터 시작]
  <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

* [푸시 알림 시작]
  <br/>앱에 기본적인 푸시 알림을 보내는 방법을 알아봅니다.

* [모바일 서비스 Android 방법 개념 참조]
  <br/>모바일 서비스를 Android와 함께 사용하는 방법을 자세히 알아보세요.

<!-- Anchors. -->
[서버 스크립트 등록]: #register-scripts
[다음 단계]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-android-authorize-users-in-scripts/mobile-quickstart-startup-android.png

<!-- URLs. -->
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push

[모바일 서비스 Android 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-android-how-to-use-client-library/



<!--HONumber=42-->
