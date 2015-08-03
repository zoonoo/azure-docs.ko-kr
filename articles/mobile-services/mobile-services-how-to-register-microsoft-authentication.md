<properties 
	pageTitle="Microsoft 인증 등록 - 모바일 서비스" 
	description="Azure 모바일 서비스 응용 프로그램에서 Microsoft 인증을 위해 등록하는 방법을 알아봅니다." 
	authors="ggailey777" 
	services="mobile-services" 
	documentationCenter="Mobile" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/14/2015" 
	ms.author="glenga"/>

# 앱을 등록하여 Microsoft 계정을 인증에 사용

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

## 개요 

이 항목에서는 Azure 모바일 서비스와 함께 인증 공급자로 Microsoft 계정을 사용할 수 있도록 모바일 앱을 등록하는 방법을 보여줍니다. 동일한 단계로 Live SDK를 사용하여 서비스 지향 인증 및 클라이언트에서 지시하는 인증에 모두 적용합니다.

##Windows 개발자 센터에서 Windows 스토어 앱 등록

먼저 Windows 개발자 센터에 Windows 스토어 앱이 등록되어야 합니다.

>[AZURE.NOTE]Windows Phone 8, Windows Phone 8.1 Silverlight 및 비Windows 앱은 섹션을 건너뛸 수 있습니다.

1. 앱을 아직 등록하지 않은 경우 Windows 스토어 앱용 개발자 센터에 있는 [앱 제출 페이지]로 이동하여 Microsoft 계정으로 로그온한 후 **앱 이름**을 클릭합니다.

   	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-submit-win8-app.png)

2. **고유 이름을 예약하여 새 앱 만들기**를 선택하고 **계속**을 클릭한 다음 **앱 이름**에서 앱 이름을 입력하고 **앱 이름 예약**, **저장**을 차례로 클릭합니다.

   	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-name.png)

   	이렇게 하면 앱을 새로 Windows 스토어에 등록하게 됩니다.

3. Visual Studio 2013에서 [모바일 서비스 시작](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md) 자습서를 완료했을 때 생성된 프로젝트를 엽니다.

4. 솔루션 탐색기에서 Windows 스토어 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어**를 클릭한 후 **응용 프로그램을 스토어에 연결...**을 클릭합니다.

  	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-store-association.png)

   	**응용 프로그램을 Windows 스토어에 연결** 마법사가 나타납니다.

5. 마법사에서 **로그인**을 클릭한 다음 Microsoft 계정으로 로그인하여 2단계에서 예약한 앱 이름을 선택하고 **다음** > **연결**을 클릭합니다.

   	이렇게 하면 필요한 Windows 스토어 등록 정보가 응용 프로그램 매니페스트에 추가됩니다.

6. (선택 사항) 4~5단계를 반복하여 유니버설 Windows 앱의 Windows Phone 스토어 프로젝트도 등록합니다.

6. 새 앱의 Windows 개발자 센터 페이지로 돌아가서 **Services**를 클릭합니다.

   	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-edit-app.png)

7. Services 페이지에서 **Azure Mobile Services** 아래의 **Live Services site**를 클릭합니다.

	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-edit2-app.png)

이 응용 프로그램에 대한 Microsoft 계정 페이지를 표시합니다.

## Microsoft 계정 등록 구성 및 모바일 서비스에 연결

이 섹션의 첫 번째 단계는 Windows Phone 8, Windows Phone 8.1 Silverlight 및 비Windows 스토어 앱에만 적용됩니다. 이러한 앱의 경우, Windows 스토어 앱에만 사용할 수 있는 패키지 SID(보안 식별자)를 무시할 수도 있습니다.

1. 비Windows 스토어 앱의 경우, Microsoft 계정 개발자 센터의 <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">내 응용 프로그램</a> 페이지로 이동하여 Microsoft 계정으로 로그온하고(필요한 경우), **응용 프로그램 만들기**를 클릭한 다음 **응용 프로그램 이름**을 입력하고 **동의함**을 클릭합니다.

   	이는 Microsoft 계정으로 앱 이름을 예약하고 앱에 대한 Microsoft 계정 페이지를 표시합니다.

2. 앱에 대한 Microsoft 계정 페이지에서 **API 설정**을 클릭하고 **모바일 또는 데스크톱 클라이언트 앱** 사용을 선택하여 모바일 서비스 URL을 **대상 도메인**으로 설정하고 **리디렉션 URL**에서 `https://<mobile_service>.azure-mobile.net/login/microsoftaccount/`의 값을 지정한 다음 **저장**을 클릭합니다.

	 >[AZURE.NOTE]Visual Studio를 사용하여 Azure에 게시된 .NET 백 엔드 모바일 서비스의 경우 리디렉션 URL은 모바일 서비스 URL에 모바일 서비스를 .NET 서비스로 사용한 _signin-microsoft_ 경로를 추가한 것입니다(예: `https://todolist.azure-mobile.net/signin-microsoft`).

    ![Microsoft 계정 API 설정](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png)

	**루트 도메인**이 자동으로 채워집니다.

4. **앱 설정**을 클릭하고 **클라이언트 ID**, **클라이언트 암호** 및 **패키지 SID** 값을 기록해 둡니다.
	
   	![Microsoft 계정 앱 설정](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png)
	
	
    > [AZURE.NOTE]클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 클라이언트 암호를 공유하거나 앱과 함께 배포하지 마세요. Windows 스토어 앱 등록에서만 패키지 SID 필드가 보입니다.

4. [Azure 관리 포털]에서 모바일 서비스에 대한 **ID** 탭을 클릭하고 ID 공급자로부터 얻은 클라이언트 ID, 클라이언트 암호 및 패키지 SID를 입력한 다음 **저장**을 클릭합니다.

 	![모바일 서비스 ID 탭](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-identity-tab.png)
	
	>[AZURE.NOTE]Windows Phone 8, Windows Phone 스토어 8.1 Silverlight 또는 비Windows 앱에 대한 패키지 SID 값을 제공할 필요가 없습니다.
	
이제 모바일 서비스와 앱이 모두 Microsoft 계정으로 동작하도록 구성되었습니다.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Azure 관리 포털]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO4-->