<properties
	pageTitle="앱 서비스 응용 프로그램에 대해 Microsoft 계정 인증을 구성하는 방법"
	description="앱 서비스 응용 프로그램에 대해 Microsoft 계정 인증을 구성하는 방법을 알아봅니다."
	authors="mattchenderson"
	services="app-service"
	documentationCenter=""
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/24/2016"
	ms.author="mahender"/>

# Microsoft 계정 로그인을 사용하도록 앱 서비스 응용 프로그램을 구성하는 방법

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Microsoft 계정을 인증 공급자로 사용하도록 Azure 앱 서비스를 구성하는 방법을 보여 줍니다.

## <a name="register-windows-dev-center"> </a>Windows 개발자 센터에서 Windows 앱 등록

Windows 개발자 센터를 통해 유니버설 Windows 앱 및 Windows 스토어 앱을 등록해야 합니다. 이렇게 하면 나중에 훨씬 쉽게 앱에 푸시 알림을 구성할 수 있습니다.

>[AZURE.NOTE]Windows Phone 8, Windows Phone 8.1 Silverlight 및 모든 다른 비 Windows 앱은 이 섹션을 건너뜁니다. Windows 앱에 대한 푸시 알림을 이미 구성한 경우 이 섹션을 건너뛸 수도 있습니다.

1. [Azure 포털]에 로그온한 다음 응용 프로그램으로 이동합니다. **URL**을 복사합니다. 이를 사용하여 Microsoft 계정 앱으로 구성합니다.

2. 앱을 아직 등록하지 않은 경우 [Windows 개발자 센터](https://dev.windows.com/dashboard/Application/New)로 이동하여 Microsoft 계정으로 로그온한 후 앱 이름을 입력하고 해당 가용성을 확인한 다음 **앱 이름 예약**을 클릭합니다.

3. Visual Studio에서 Windows 앱 프로젝트를 열고 솔루션 탐색기에서 Windows 스토어 앱 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **스토어** > **응용 프로그램을 스토어에 연결...**을 클릭합니다.

  	![](./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-windows-store-association.png)

4. 마법사에서 **로그인**을 클릭한 다음 Microsoft 계정으로 로그인하여 예약한 앱 이름을 선택하고 **다음** > **연결**을 클릭합니다. 유니버설 Windows 8.1 앱의 경우 Windows Phone 스토어 프로젝트에 대해 4~5단계를 반복해야 합니다.

6. 새 앱의 Windows 개발자 센터 페이지로 돌아가서 **서비스** > **푸시 알림**을 클릭합니다.

7. **푸시 알림** 페이지에서 **WNS(Windows 푸시 알림 서비스) 및 Microsoft Azure 모바일 서비스** 아래의 **Live 서비스 사이트**를 클릭합니다.

다음으로 Windows 앱에 대한 Microsoft 계정 인증을 구성합니다.


## <a name="register-microsoft-account"> </a>Microsoft 계정을 사용하여 앱 등록

이전 섹션에서 이미 Windows 앱을 등록한 경우 4단계로 건너뛸 수 있습니다.

1. [Azure 포털]에 로그온한 다음 응용 프로그램으로 이동합니다. **URL**을 복사합니다. 이를 사용하여 Microsoft 계정 앱으로 구성합니다.

2. Microsoft 계정 개발자 센터의 [내 응용 프로그램] 페이지로 이동하고 필요한 경우 Microsoft 계정으로 로그온합니다.

3. **응용 프로그램 만들기**를 클릭한 후 **응용 프로그램 이름**을 입력하고 **I accept**를 클릭합니다.

4. **API 설정**을 클릭하고 **모바일 또는 데스크톱 클라이언트 앱**에 대해 **예**를 선택하고 응용 프로그램에 대한 **URL 리디렉션**을 지정한 후 **저장**을 클릭합니다.
 
	![][0]

	>[AZURE.NOTE]리디렉션 URI는 경로 _/.auth/login/microsoftaccount/callback_이 추가된 응용 프로그램의 URL입니다. 예: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback` HTTPS 체계를 사용 중인지 확인합니다.

6. **앱 설정**을 클릭하고 **클라이언트 ID** 및 **클라이언트 암호** 값을 기록해 둡니다.

    > [AZURE.IMPORTANT] 클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 클라이언트 암호를 공유하거나 클라이언트 응용 프로그램 내에 배포하지 마세요.

## <a name="secrets"> </a>앱 서비스 응용 프로그램에 Microsoft 계정 정보 추가

1. [Azure 포털]로 돌아가서 응용 프로그램으로 이동하여 **설정** > **인증/권한 부여**를 클릭합니다.

2. 인증/권한 부여 기능이 사용하도록 설정되지 않은 경우 스위치를 **켭니다**.

3. **Microsoft 계정**을 클릭합니다. 이전에 가져온 앱 ID 및 앱 암호 값을 붙여넣고 필요에 따라 응용 프로그램에 필요한 범위를 설정합니다. 그런 후 **OK**를 클릭합니다.

    ![][1]

	기본적으로 앱 서비스는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.

4. (선택 사항) Microsoft 계정에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **Microsoft 계정**에 **요청이 인증되지 않으면 수행할 동작**을 설정합니다. 이렇게 하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Microsoft 계정에 리디렉션되어야 합니다.

5. **Save**를 클릭합니다.

이제 앱에서 Microsoft 계정을 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 포털]: https://portal.azure.com/

<!---HONumber=AcomDC_0302_2016-->