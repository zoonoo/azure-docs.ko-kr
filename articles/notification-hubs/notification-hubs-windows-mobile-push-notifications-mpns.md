---
title: "Windows Phone 앱에 대한 Azure Notification Hubs 시작 | Microsoft Docs"
description: "이 자습서에서는 Azure Notification Hubs를 사용하여 Windows Phone 8 또는 Windows Phone 8.1 Silverlight 응용 프로그램에 푸시 알림을 보내는 방법을 알아봅니다."
services: notification-hubs
documentationcenter: windows
keywords: "푸시 알림, 푸시 알림, windows phone 푸시"
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 7d44d0a0f8683ad6ad55136ad17879e98e26498b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-windows-phone-apps"></a>Windows Phone 앱에 대한 Azure Notification Hubs 시작
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>개요
> [!NOTE]
> 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F)을 참조하세요.
> 
> 

이 자습서에서는 Azure Notification Hubs를 사용하여 Windows Phone 8 또는 Windows Phone 8.1 Silverlight 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. Windows Phone 8.1(비 Silverlight)을 대상으로 하는 경우 [Windows 범용](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 버전을 참조하세요.
이 자습서에서는 MPNS(Microsoft 푸시 알림 서비스)를 사용하여 푸시 알림을 받는 빈 Windows Phone 8 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

> [!NOTE]
> Notification Hubs Windows Phone SDK에서는 Windows Phone 8.1 Silverlight 앱에서의 WNS(Windows 푸시 알림 서비스) 사용을 지원하지 않습니다. Windows Phone 8.1 Silverlight 앱에서 MPNS 대신 WNS를 사용하려면 REST API를 사용하는 [Notification Hubs - Windows Phone Silverlight 자습서]를 따릅니다.
> 
> 

이 자습서에서는 Notification Hubs를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 사용하려면 다음이 필요합니다.

* [Visual Studio 2012 Express for Windows Phone]이상 버전

이 자습서를 완료해야 다른 모든 Windows Phone 8 앱용 Notification Hubs 자습서를 진행할 수 있습니다.

## <a name="create-your-notification-hub"></a>알림 허브 만들기
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p><b>Notification Services</b> 아래에서 <b>Windows Phone(MPNS)</b>을 선택한 다음, <b>인증되지 않은 푸시 사용</b> 확인란을 클릭합니다.</p>
</li>
</ol>

&emsp;&emsp;![Azure Portal - 인증되지 않은 푸시 알림 사용](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

이제 Windows Phone의 인증되지 않은 알림을 보낼 수 있는 허브가 생성 및 구성되었습니다.

> [!NOTE]
> 이 자습서에서는 인증되지 않은 모드로 MPNS를 사용합니다. MPNS 인증되지 않은 모드에는 각 채널로 보낼 수 있는 알림에 대한 제한이 있습니다. Notification Hubs는 인증서를 업로드할 수 있도록 하여 [MPNS 인증 모드](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx)를 지원합니다.
> 
> 

## <a name="connecting-your-app-to-the-notification-hub"></a>알림 허브에 앱 연결
1. Visual Studio에서 새 Windows Phone 8 응용 프로그램을 만듭니다.
   
       ![Visual Studio - New Project - Windows Phone App][13]
   
    Visual Studio 2013 업데이트 2 이상에서는 대신 Windows Phone Silverlight 응용 프로그램을 만듭니다.
   
    ![Visual Studio - 새 프로젝트 - 새 응용 프로그램 - Windows Phone Silverlight][11]
2. Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
   
    그러면 **NuGet 패키지 관리** 대화 상자가 표시됩니다.
3. `WindowsAzure.Messaging.Managed` 를 검색하고 **설치**를 클릭한 후 사용 약관에 동의합니다.
   
    ![Visual Studio - NuGet 패키지 관리자][20]
   
    그러면 <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet 패키지</a>를 사용하여 Windows용 Azure Messaging 라이브러리에 대한 참조가 다운로드, 설치 및 추가됩니다.
4. App.xaml.cs 파일을 열고 다음 `using` 문을 추가합니다.
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. App.xaml.cs의 **Application_Launching** 메서드 맨 위에 다음 코드를 추가합니다.
   
        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }
   
        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());
   
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
   
   > [!NOTE]
   > 값 **MyPushChannel** 은 [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) 컬렉션에서 기존 채널을 조회하는 데 사용되는 인덱스입니다. 항목이 없으면 해당 이름을 가진 새 항목을 만듭니다.
   > 
   > 
   
    허브 이름과 이전 섹션에서 얻은 **DefaultListenSharedAccessSignature** 라는 연결 문자열을 삽입해야 합니다.
    이 코드는 MPNS에서 앱의 채널 URI를 검색한 후 해당 채널 URI를 알림 허브에 등록합니다. 또한 이 코드는 응용 프로그램이 시작될 때마다 채널 URI가 알림 허브에 등록되도록 보장합니다.
   
   > [!NOTE]
   > 이 자습서에서는 알림 메시지를 장치로 보냅니다. 타일 알림을 보내는 경우 채널에서 **BindToShellTile** 메서드를 대신 호출해야 합니다. 알림 메시지와 타일 알림을 둘 다 지원하려면 **BindToShellTile** 및 **BindToShellToast**를 둘 다 호출합니다.
   > 
   > 
6. 솔루션 탐색기에서 **속성**을 확장하고 `WMAppManifest.xml` 파일을 연 후 **기능** 탭을 클릭하고 **ID_CAP_PUSH_NOTIFICATION** 기능이 선택되었는지 확인합니다.
   
       ![Visual Studio - Windows Phone App Capabilities][14]
   
       This ensures that your app can receive push notifications. Without it, any attempt to send a push notification to the app will fail.
7. `F5` 키를 눌러 앱을 실행합니다.
   
    앱에 등록 메시지가 표시됩니다.
8. 앱을 닫습니다.  
   
   > [!NOTE]
   > 푸시 알림 메시지를 받으려면 응용 프로그램이 포그라운드에서 실행되고 있지 않아야 합니다.
   >

## <a name="next-steps"></a>다음 단계
이 간단한 예제에서는 모든 Windows Phone 8 장치로 푸시 알림을 브로드캐스트했습니다. 

특정 사용자를 대상으로 하려면 [Notification Hubs를 사용하여 사용자에게 알림 푸시] (영문) 자습서를 참조하세요. 

사용자를 관심 그룹별로 분할하려면 [Notification Hubs를 사용하여 뉴스 속보 보내기](영문)를 참조하십시오. 

[Notification Hubs 지침]에서 Notification Hubs 사용 방법을 자세히 알아보십시오.

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Notification Hubs 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Notification Hubs를 사용하여 사용자에게 알림 푸시]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Notification Hubs를 사용하여 뉴스 속보 보내기]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tile catalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hubs - Windows Phone Silverlight 자습서]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

