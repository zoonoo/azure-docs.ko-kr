<properties urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Visual Studio 2013에서 게시 설정 파일 가져오기 | 모바일 서비스" metaKeywords="Azure import publishsettings, mobile services" description="Visual Studio 2013에서 Azure 모바일 서비스 응용 프로그램에 대한 구독 게시 설정 파일을 가져오는 방법에 대해 알아봅니다." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="mobile-services" solutions="" manager="dwrede" editor="" videoId="" scriptId="" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Visual Studio 2013에서 구독 게시 설정 파일 가져오기

모바일 서비스를 만들려면 먼저 Azure 구독 관리에서 Visual Studio로 게시 설정 파일을 가져와야 합니다. 이렇게 하면 Visual Studio를 통해 Azure에 연결하게 됩니다.  

>[WACOM.NOTE]Visual Studio 2013 업데이트 2부터는 더 이상 게시 설정 파일을 사용할 필요가 없습니다. Visual Studio에서 사용자가 제공하는 자격 증명을 사용하여 Azure에 직접 연결할 수 있습니다.

1. Visual Studio 2013에서 솔루션 탐색기를 열고 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **추가**를 클릭하고 **연결된 서비스...**를 클릭합니다. 

	![add connected service](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2. 서비스 관리자 대화 상자에서 **서비스 만들기...**를 클릭한 후 모바일 서비스 만들기 대화 상자에 있는 **구독**에서 **&lt;Import...&gt;**를 선택합니다.  

	![create a new mobile service from VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. Import Azure Subscriptions에서 **구독 파일 다운로드**를 클릭하고 필요한 경우 Azure 계정에 로그인하고 브라우저에서 파일을 저장하라고 요청하면 **저장**을 클릭합니다.

	![download subscription file in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

	<div class="dev-callout"><strong>참고</strong> <p>브라우저에 로그인 창이 표시되며, 로그인 창은 Visual Studio 창 뒤에 나타날 수도 있습니다. 다운로드한 .publishsettings 파일을 저장한 위치를 기억할 수 있도록 적어두는 것이 좋습니다. 프로젝트가 이미 Azure 구독 관리에 연결된 경우 이 단계를 건너뛸 수 있습니다.</p></div> 

4. **찾아보기**를 클릭하고 .publishsettings 파일을 저장한 위치로 이동하여 파일을 선택하고 **열기**를 클릭한 후 **가져오기**를 클릭합니다. 

	![import subscription in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

	Visual Studio가 Azure 구독 관리에 연결할 때 필요한 데이터를 가져옵니다. 사용자 구독에 하나 이상의 모바일 서비스가 있는 경우 해당 서비스 이름이 나타납니다. 

	<div class="dev-callout"><strong>보안 정보</strong> <p>다운로드한 .publishsettings 파일에는 다른 사람이 사용자 계정에 액세스할 수 있는 정보가 포함되어 있으므로 게시 설정을 가져온 후에는 파일을 삭제하는 것이 좋습니다. 연결된 다른 앱 프로젝트에 사용하기 위해 파일을 보관하려면 보안에 주의를 기울이세요.</p></div>

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On]: /ko-kr/develop/mobile/how-to-guides/register-for-single-sign-on/
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[모바일 서비스 시작](영문): /ko-kr/develop/mobile/tutorials/get-started/
[인증 시작](영문): /ko-kr/develop/mobile/tutorials/get-started-with-users-dotnet/
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-dotnet/
[스크립트를 통해 사용자 권한 부여]: /ko-kr/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript 및 HTML]: /ko-kr/develop/mobile/tutorials/get-started-with-users-js/

[Azure 관리 포털]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
