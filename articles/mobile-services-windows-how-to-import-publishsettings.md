<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Import your publish settings file in Visual Studio 2013 | Mobile Services" metaKeywords="Azure import publishsettings, mobile services" description="Learn how to import a subscription publish settings file for your Azure Mobile Services application in Visual Studio 2013." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Visual Studio 2013에서 구독 게시 설정 파일 가져오기

모바일 서비스를 만들려면 먼저 Azure 구독 관리에서 Visual Studio로 게시 설정 파일을 가져와야 합니다. 이렇게 하면 Visual Studio를 통해 Azure에 연결하게 됩니다.

1.  Visual Studio 2013에서 솔루션 탐색기를 열어 마우스 오른쪽 단추로 프로젝트를 클릭한 후 **추가**를 클릭하고 **연결된 서비스...**를 클릭합니다.

    ![연결된 서비스 추가][연결된 서비스 추가]

2.  서비스 관리자 대화 상자에서 **Create service...**를 클릭한 후 Create Mobile Service 대화 상자에 있는 **Subscription**에서 **\<Import...\>**를 선택합니다.

    ![VS 2013에서 새 모바일 서비스 만들기][VS 2013에서 새 모바일 서비스 만들기]

3.  Import Azure Subscriptions에서 **구독 파일 다운로드**를 클릭하고 필요한 경우 Azure 계정에 로그인하고 브라우저가 파일 저장을 요청할 때 **저장**을 클릭합니다.

    ![VS에서 구독 파일 다운로드][VS에서 구독 파일 다운로드]

    <div class="dev-callout">

    **참고**
    브라우저에 로그인 창이 표시되며, 로그인 창은 Visual Studio 창 뒤에 나타날 수도 있습니다. 다운로드한 .publishsettings 파일을 저장한 위치를 기억할 수 있도록 적어두는 것이 좋습니다. 프로젝트가 이미 Azure 구독 관리에 연결된 경우 이 단계를 건너뛸 수 있습니다.

    </div>

4.  **찾아보기**를 클릭하고 .publishsettings 파일을 저장한 위치로 이동하여 해당 파일을 선택하고 **열기**를 클릭한 후 **가져오기**를 클릭합니다.

    ![VS에서 구독 가져오기][VS에서 구독 가져오기]

    Visual Studio가 Azure 구독 관리에 연결할 때 필요한 데이터를 가져옵니다. 사용자 구독에 하나 이상의 모바일 서비스가 있는 경우 해당 서비스 이름이 나타납니다.

    <div class="dev-callout">

    **보안 정보**
    다운로드한 .publishsettings 파일에는 다른 사람이 사용자 계정에 액세스할 수 있는 정보가 포함되어 있으므로 게시 설정을 가져온 후에는 파일을 삭제하는 것이 좋습니다. 연결된 다른 앱 프로젝트에 사용하기 위해 파일을 보관하려면 보안에 주의를 기울이세요.

    </div>



  [연결된 서비스 추가]: ./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png
  [VS 2013에서 새 모바일 서비스 만들기]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [VS에서 구독 파일 다운로드]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png
  [VS에서 구독 가져오기]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png
