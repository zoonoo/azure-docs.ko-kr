<properties pageTitle="Register for single sign-on - Azure Mobile Services" metaKeywords="" description="Learn how to register for single sign-on authentication in your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store apps to use Windows Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Windows Live Connect Single Sign-On 사용을 위해 Windows 스토어 앱 등록

이 항목에서는 Live Connect Single Sign-On을 Azure 모바일 서비스에 대한 ID 공급자로 사용할 수 있도록 Windows 스토어에 앱을 등록하는 방법을 보여 줍니다. 이 단계는 푸시 알림을 사용하는 데에도 필요합니다.

<div class="dev-callout"><b>참고</b>
<p>앱을 게시하기 전에 Microsoft 계정을 사용하여 인증하려면 Windows 스토어에 앱을 등록할 필요가 없습니다. Windows 스토어 앱에 Single Sign-On 또는 푸시 알림이 필요하지 않은 경우 Live Connect에만 앱을 등록하여 Microsoft 계정 로그인을 사용할 수 있습니다.  자세한 내용은 <a href="/ko-kr/develop/mobile/how-to-guides/register-for-microsoft-authentication">Microsoft 계정 로그인 사용을 위해 Windows 스토어 앱 등록</a>을 참조하십시오.</p>
</div>

1.  앱을 아직 등록하지 않은 경우 Windows 스토어 앱용 개발자 센터에 있는 [앱 제출 페이지][앱 제출 페이지]로 이동하여 Microsoft 계정으로 로그온한 후 **앱 이름**을 클릭합니다.

    ![][0]

2.  **앱 이름**에 앱의 이름을 입력하고 **앱 이름 예약**을 클릭한 후 **저장**을 클릭합니다.

    ![][1]

    이렇게 하면 앱을 새로 Windows 스토어에 등록하게 됩니다.

3.  Visual Studio 2012 Express for Windows 8에서 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료할 때 만든 프로젝트를 엽니다.

4.  솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어**를 클릭한 후 **응용 프로그램을 스토어에 연결...**을 클릭합니다.

    ![][2]

    **응용 프로그램을 Windows 스토어에 연결** 마법사가 나타납니다.

5.  마법사에서 **로그인**을 클릭한 후 Microsoft 계정에 로그인합니다.

6.  2단계에서 등록한 앱을 선택하고 **다음**을 클릭한 후 **연결**을 클릭합니다.

    ![][3]

    이렇게 하면 필요한 Windows 스토어 등록 정보가 응용 프로그램 매니페스트에 추가됩니다.

7.  Live Connect 개발자 센터의 [내 응용 프로그램][내 응용 프로그램] 페이지로 이동하고 **내 응용 프로그램** 목록에서 앱을 클릭합니다.

    ![][4]

8.  **설정 편집**을 클릭한 후 **API 설정**을 클릭하고 **클라이언트 암호** 값을 기록해 둡니다.

    ![][5]

    <div class="dev-callout"><b>보안 정보</b>
<p>클라이언트 암호는 중요한 보안 자격 증명입니다. 다른 사람과 클라이언트 암호를 공유하거나 앱과 함께 배포하지 마십시오.</p>
</div>

9.  **리디렉션 도메인**에서 8단계의 모바일 서비스 URL을 입력하고 **저장**을 클릭합니다.

이제 Live Connect를 사용하여 인증을 앱에 통합할 준비가 되었습니다. 모바일 서비스에서는 Live Connect를 사용하여 다음과 같은 두 가지 방법으로 사용자를 인증할 수 있습니다.

-   Windows 스토어 앱의 Single Sign-On. 이 방법에서 사용자는 Live Connect를 사용하여 응용 프로그램에서 한 번만 인증 권한을 부여하면 됩니다. 그러면 Windows에서 사용자의 기본 설정을 기반으로 자격 증명을 자동으로 관리합니다. 자세한 내용은 [Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On][Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On]을 참조하십시오.

-   기본 인증. 이 방법에서는 다양한 인증 공급자를 지원하며 사용자는 앱을 시작할 때마다 로그인해야 합니다. 자세한 내용은 [인증 시작][인증 시작](영문)을 참조하십시오.

 
 


  [앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
  [모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started
  [2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
  [3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png
  [내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [4]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
  [5]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png
  [Live Connect를 사용한 Windows 스토어 앱의 Single Sign-On]: /ko-kr/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
  [인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-dotnet
