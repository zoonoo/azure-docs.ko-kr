<properties linkid="notification-hubs-windows-store-dotnet-send-breaking-news" pageTitle="Use Notification Hubs to send breaking news (Windows Universal)" metaKeywords="" description="Use  Azure Notification Hubs with tags in the registration to send breaking news to a universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="ricksal" solutions="" manager="" editor="" />

<properties linkid="notification-hubs-windows-store-dotnet-send-breaking-news" pagetitle="Use Notification Hubs to send breaking news (Windows Phone)" metakeywords description="Use  Azure Notification Hubs to use tag in registrations to send breaking news to a Windows Phone app." metacanonical services="notification-hubs" documentationcenter="Mobile" title="Use Notification Hubs to send breaking news" authors="glenga" solutions manager="dwrede" editor></properties>

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# 알림 허브를 사용하여 속보 보내기

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/ko-KR/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows 범용" class="current">Windows 범용</a><a href="/ko-KR/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/ko-KR/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a>
        <a href="/ko-KR/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

이 항목에서는 Azure 알림 허브를 사용하여 Windows 스토어 또는 Windows Phone 8.1(비 Silverlight) 앱에 속보 알림을 브로드캐스트하는 방법을 보여 줍니다. Windows Phone 8.1 Silverlight를 대상으로 하는 경우 [Windows Phone][1] 버전을 참조하세요. 완료하면, 관심이 있는 속보 범주를 등록하고 해당 범주의 푸시 알림만 받을 수 있습니다. 이 시나리오는 RSS 수집기, 음악 애호가를 위한 앱 등 이전에 관심을 보인 사용자 그룹에 알림을 보내야 하는 많은 앱에 공통된 패턴입니다.

브로드캐스트 시나리오를 사용하려면 알림 허브에서 등록을 만들 때 하나 이상의 *태그*를 포함하면 됩니다. 태그에 알림이 전송되면 태그에 대해 등록된 모든 장치에서 알림을 받게 됩니다. 태그는 단순히 문자열이므로 사전에 프로비전해야 할 필요가 없습니다. 태그에 대한 자세한 내용은 [알림 허브 지침][알림 허브 지침]을 참조하십시오.

이 자습서에서는 이 시나리오를 사용하기 위한 다음 기본 단계를 차례로 안내합니다.

1.  [앱에 범주 선택 추가][앱에 범주 선택 추가]
2.  [알림 등록][알림 등록]
3.  [백 엔드에서 알림 보내기][백 엔드에서 알림 보내기]
4.  [앱 실행 및 알림 생성][앱 실행 및 알림 생성]

이 항목은 [알림 허브 시작][알림 허브 시작]에서 만든 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [알림 허브 시작][알림 허브 시작]을 완료해야 합니다.

## <a name="adding-categories"></a>앱에 범주 선택 추가

첫 번째 단계는 기존의 기본 페이지에 사용자가 등록할 범주를 선택할 수 있도록 하는 UI 요소를 추가하는 것입니다. 사용자가 선택한 범주는 장치에 저장됩니다. 앱을 시작하면 장치 등록이 선택한 범주와 함께 태그로서 알림 허브에 생성됩니다.

1.  MainPage.xaml 프로젝트 파일을 열고 **Grid** 요소에서 다음 코드를 복사합니다.

        <Grid Margin="120, 58, 120, 80" >
            <Grid.RowDefinitions>
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2.  프로젝트에서 **Notifications**라는 이름의 새 클래스를 만들고, 클래스 정의에 **public** 한정자를 추가하고, 다음 **using** 문을 새 코드 파일에 추가합니다.

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;

3.  다음 코드를 새 **Notifications** 클래스에 추가합니다.

        private NotificationHub hub;

        public Notifications()
        {
            hub = new NotificationHub("<hub name>", "<connection string with listen access>");
        }

        public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            await SubscribeToCategories(categories);
        }

        public async Task SubscribeToCategories(IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await hub.RegisterNativeAsync(channel.Uri, categories);
        }

    이 클래스는 로컬 저장소를 사용하여, 이 장치에서 받아야 할 뉴스의 범주를 저장합니다. 이러한 범주를 등록하기 위한 메서드도 이 클래스에 포함됩니다.

4.  위의 코드에서 `<hub name>` 및 `<connection string with listen access>` 자리 표시자를 알림 허브 이름과 앞에서 얻은 *DefaultListenSharedAccessSignature*의 연결 문자열로 바꿔야 합니다.

    <div class="dev-callout"><strong>참고</strong> 
    <p>클라이언트 앱과 함께 배포되는 자격 증명은 일반적으로 안전하지 않기 때문에 클라이언트 앱과 함께 listen access용 키만 배포해야 합니다. Listen access를 통해 앱에서 알림을 등록할 수 있지만, 기존 등록을 수정할 수 없으며 알림을 전송할 수도 없습니다. 안전한 백 엔드 서비스에서 알림을 보내고 기존 등록을 변경하는 데에는 모든 액세스 키가 사용됩니다.</p>
</div>

5.  App.xaml.cs 프로젝트 파일에서 **App** 클래스에 다음 속성을 추가합니다.

        public Notifications notifications = new Notifications();

    이 속성은 **Notifications** 인스턴스를 만들고 액세스하는 데 사용됩니다.

6.  MainPage.xaml.cs에 다음 줄을 추가합니다.

        using Windows.UI.Popups;

7.  MainPage.xaml.cs 프로젝트 파일에 다음 메서드를 추가합니다.

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories));
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    이 메서드는 범주 목록을 만들고 **Notifications** 클래스를 사용하여, 로컬 저장소에 목록을 저장하고 알림 허브에 해당 태그를 등록합니다. 범주가 변경되면 새 범주로 등록이 다시 생성됩니다.

이제 사용자가 범주 선택을 변경할 때마다 앱은 범주 집합을 장치의 로컬 저장소에 저장하고 알림 허브에 등록할 수 있습니다.

## <a name="register"></a>알림 등록

다음 단계에서는 로컬 저장소에 저장된 범주를 사용하여 시작 시 알림 허브에 등록합니다.

<div class="dev-callout"><strong>참고</strong> 
    <p>WNS(Windows 알림 서비스)에서 할당하는 채널 URI는 언제든지 변경될 수 있으므로 알림 실패를 피하려면 알림을 자주 등록해야 합니다. 이 예제에서는 앱이 시작될 때마다 알림을 등록합니다. 자주(하루 두 번 이상) 실행되는 앱에서는 이전 등록 이후 만 하루가 지나지 않은 경우 대역폭 유지를 위한 등록을 건너뛸 수 있습니다.</p>
</div>

1.  **Notifications** 클래스에 다음 코드를 추가합니다.

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

    이렇게 하면 클래스에 정의된 범주가 반환됩니다.

2.  App.xaml.cs 파일을 열고 **async** 한정자를 **OnLaunched** 메서드에 추가합니다.

3.  **OnLaunched** 메서드에서 **InitNotificationsAsync** 메서드에 대한 기존 호출을 찾아 다음 코드 줄로 교체합니다.

        await notifications.SubscribeToCategories(notifications.RetrieveCategories());

    이제 앱이 시작될 때마다 로컬 저장소에서 범주를 검색하고, 이러한 범주에 대한 등록을 요청하게 됩니다. [알림 허브 시작][알림 허브 시작] 자습서에서 **InitNotificationsAsync** 메서드를 만들었지만 이 항목에서는 필요하지 않습니다.

4.  MainPage.xaml.cs 프로젝트 파일의 *OnNavigatedTo* 메서드에 다음 코드를 추가합니다.

        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;

    전에 저장한 범주의 상태를 기반으로 기본 페이지가 업데이트됩니다.

이제 앱이 완료되며, 사용자가 범주 선택을 변경할 때마다 알림 허브 등록에 사용된 장치의 로컬 저장소에 범주 집합을 저장할 수 있습니다. 다음에는 범주 알림을 이 앱에 보낼 수 있는 백 엔드를 정의합니다.

## <a name="send"></a><span class="short-header">알림 보내기</span>백 엔드에서 알림 보내기

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

## <a name="test-app"></a>앱 실행 및 알림 생성

1.  Visual Studio에서 F5 키를 눌러 앱을 컴파일 및 시작합니다.

    ![][0]

    앱 UI는 구독할 범주를 선택하도록 하는 토글 집합을 제공합니다.

2.  하나 이상의 범주 토글을 사용하도록 설정한 후 **구독**을 클릭합니다.

    앱은 선택한 범주를 태그로 변환하고 알림 허브에서 선택한 태그에 대한 새로운 장치 등록을 요청합니다. 등록된 범주가 반환되어 대화 상자에 표시됩니다.

    ![][2]

3.  다음 방법 중 하나로 백 엔드에서 새 알림을 보냅니다.

    -   **콘솔 앱:** 콘솔 앱을 시작합니다.

    -   **Java/PHP:** 앱/스크립트를 실행합니다.

    선택한 범주에 대한 알림이 알림 메시지로 나타납니다.

    ![][3]

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 범주별로 속보를 브로드캐스트하는 방법에 대해 알아보았습니다. 이제 기타 고급 알림 허브 시나리오를 다루는 다음 자습서 중 하나를 완료해 보십시오.

-   [알림 허브를 사용하여 지역화된 속보 브로드캐스트][알림 허브를 사용하여 지역화된 속보 브로드캐스트]

    지역화된 알림을 보낼 수 있도록 속보 앱을 확장하는 방법에 대해 알아보십시오.

-   [알림 허브를 통해 사용자에게 알림][알림 허브를 통해 사용자에게 알림]

    인증된 특정 사용자에게 알림을 푸시하는 방법에 대해 알아보십시오. 이 방법은 특정 사용자에게만 알림을 보내기 위한 훌륭한 솔루션입니다.

 
 
<!-- URLs.-->

  [Windows Phone]: /ko-KR/documentation/articles/notification-hubs-windows-phone-send-breaking-news/ "Windows Phone"
  [1]: /ko-KR/documentation/articles/notification-hubs-ios-send-breaking-news
  [알림 허브 지침]: http://msdn.microsoft.com/ko-KR/library/jj927170.aspx
  [앱에 범주 선택 추가]: #adding-categories
  [알림 등록]: #register
  [백 엔드에서 알림 보내기]: #send
  [앱 실행 및 알림 생성]: #test-app
  [알림 허브 시작]: /ko-KR/manage/services/notification-hubs/getting-started-windows-dotnet/
  [0]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png
  [2]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png
  [3]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png
  [알림 허브를 사용하여 지역화된 속보 브로드캐스트]: /ko-KR/manage/services/notification-hubs/breaking-news-localized-dotnet/
  [알림 허브를 통해 사용자에게 알림]: /ko-KR/manage/services/notification-hubs/notify-users
