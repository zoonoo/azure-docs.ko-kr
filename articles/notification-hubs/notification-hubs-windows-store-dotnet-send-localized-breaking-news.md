<properties 
	pageTitle="알림 허브 지역화된 속보 자습서" 
	description="Azure 서비스 버스 알림 허브를 사용하여 지역화된 최신 뉴스 알림을 보내는 방법에 대해 알아봅니다." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="wesmc"/>

# 알림 허브를 사용하여 지역화된 속보 보내기

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows 스토어 C#" class="current">Windows 스토어 C#</a><a href="/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS">iOS</a>
</div>

##개요

이 항목에서는 Azure 알림 허브의 **템플릿** 기능을 사용하여 언어 및 장치별로 지역화된 속보 알림을 브로드캐스트하는 방법을 보여 줍니다. 이 자습서에서는 [알림 허브를 사용하여 속보 보내기]에서 만든 Windows 스토어 앱을 시작합니다. 이 자습서를 완료하면 관심 있는 범주를 등록하고, 알림을 받을 언어를 지정하고, 선택한 범주에 대한 푸시 알림만 해당 언어로 받을 수 있습니다.


이 시나리오는 다음과 같은 두 부분으로 구성되어 있습니다.

- Windows 스토어 앱을 사용하면 클라이언트 장치에서 언어를 지정하고, 다른 속보 범주를 구독할 수 있습니다. 

- 백 엔드에서 Azure 알림 허브의 **태그** 및 **템플릿** 기능을 사용하여 알림을 브로드캐스트합니다.



##필수 조건

[알림 허브를 사용하여 속보 보내기] 자습서를 이미 완료하고 사용 가능한 코드가 있어야 합니다. 이 자습서는 해당 코드를 기반으로 직접 빌드됩니다.

Visual Studio 2012도 필요합니다.


##템플릿 개념

[알림 허브를 사용하여 속보 보내기]에서는 **태그**를 사용하여 다른 뉴스 범주에 대한 알림을 구독하는 앱을 빌드했습니다. 하지만 대부분의 앱은 여러 시장을 대상으로 하므로 지역화해야 합니다. 즉, 알림 자체의 내용을 지역화해서 올바른 장치 집합으로 전달해야 합니다. 이 항목에서는 알림 허브의 **템플릿** 기능을 사용하여 지역화된 속보 알림을 쉽게 제공하는 방법을 보여 줍니다.

참고: 지역화된 알림을 보내는 한 가지 방법은 각 태그의 여러 버전을 만드는 것입니다. 예를 들어 영어, 프랑스어 및 북경어를 지원하려면 세계 뉴스에 대한 3가지 태그 즉, "world_en", "world_fr" 및 "world_ch"가 필요합니다. 그런 다음 이러한 각 태그로 세계 뉴스의 지역화된 버전을 보내야 합니다. 이 항목에서는 템플릿을 사용하여 태그의 확산을 방지하고 여러 메시지를 보낼 필요가 없도록 합니다.

높은 수준의 템플릿을 사용하면 특정 장치에서 알림을 받는 방법을 지정할 수 있습니다. 템플릿은 앱에서 백 엔드로 보낸 메시지에 포함된 속성을 참조하여 정확한 페이로드 형식을 지정합니다. 여기서는 지원되는 모든 언어가 포함된 로캘을 알 수 없는 메시지를 보냅니다.

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

그런 다음 올바른 속성을 참조하는 템플릿을 사용하여 장치가 등록되도록 합니다. 예를 들어 간단한 알림 메시지를 받으려는 Windows 스토어 앱에 다음 템플릿을 등록합니다.

	<toast>
	  <visual>
	    <binding template="ToastText01">
	      <text id="1">$(News_English)</text>
	    </binding>
	  </visual>
	</toast>



템플릿은 매우 강력한 기능입니다. 자세한 내용은 [알림 허브 지침] 문서를 참조하십시오. 템플릿 표현 언어에 대한 참조는 [Windows 스토어용 알림 허브 방법]을 참조하십시오.


##앱 사용자 인터페이스

이제 템플릿을 사용하여 지역화된 속보를 보내도록, [알림 허브를 사용하여 속보 보내기] 항목에서 만든 속보 앱을 수정합니다.


지역화된 메시지를 받도록 클라이언트 앱을 조정하려면 *기존* 등록(즉, 템플릿을 지정한 등록)을 템플릿 등록으로 바꾸어야 합니다.


Windows 스토어 앱에서

로캘 콤보 상자를 포함하도록 MainPage.xaml을 변경합니다.

	<Grid Margin="120, 58, 120, 80"  
			Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
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
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="Button_Click" />
    </Grid>

##Windows 스토어 클라이언트 앱 빌드

1. Notifications 클래스에서 *StoreCategoriesAndSubscribe* 및 *SubscribeToCateories* 메서드에 로캘 매개 변수를 추가합니다.

		public async Task StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            await SubscribeToCategories(locale, categories);
        }

        public async Task SubscribeToCategories(string locale, IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            var template = String.Format(@"<toast><visual><binding template=""ToastText01""><text id=""1"">$(News_{0})</text></binding></visual></toast>", locale);

            await hub.RegisterTemplateAsync(channel.Uri, template, "newsTemplate", categories);
        }

	여기서는 템플릿이 로캘에 따라 달라지는 특정 알림 형식을 등록할 것이기 때문에 *RegisterNativeAsync* 메서드를 호출하는 대신 *RegisterTemplateAsync*를 호출합니다. 또한 알림 메시지용 템플릿, 타일 알림용 템플릿 등 여러 템플릿을 등록할 수도 있으므로 템플릿의 이름("newsTemplate")을 제공합니다. 그런 다음 템플릿을 업데이트하거나 삭제하려면 해당 이름을 지정해야 합니다.

	장치에서 동일한 태그로 여러 템플릿을 등록한 경우 해당 태그를 대상으로 하는 수신 메시지는 템플릿별로 하나씩 여러 알림을 장치에 제공합니다. 이 동작은 동일한 논리 메시지로 여러 시각적 알림을 나타내야 하는 경우(예: Windows 스토어 응용 프로그램에 배지와 알림을 모두 표시해야 하는 경우)에 유용합니다.

2. 다음 메서드를 추가하여 저장된 로캘을 검색합니다.

		public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. MainPage.xaml.cs에서 아래와 같이 로캘 콤보 상자의 현재 값을 검색한 후 Notifications 클래스에 대한 호출에 값을 제공하여 단추 클릭 처리기를 업데이트합니다.

		 var locale = (string)Locale.SelectedItem;
            
         var categories = new HashSet<string>();
         if (WorldToggle.IsOn) categories.Add("World");
         if (PoliticsToggle.IsOn) categories.Add("Politics");
         if (BusinessToggle.IsOn) categories.Add("Business");
         if (TechnologyToggle.IsOn) categories.Add("Technology");
         if (ScienceToggle.IsOn) categories.Add("Science");
         if (SportsToggle.IsOn) categories.Add("Sports");

         await ((App)Application.Current).Notifications.StoreCategoriesAndSubscribe(locale, categories);

         var dialog = new MessageDialog(String .Format("Locale: {0}; Subscribed to: {1}", locale, string.Join(",", categories)));
         dialog.Commands.Add(new UICommand("OK"));
         await dialog.ShowAsync();

4. 마지막으로 App.xaml.cs 파일의 *OnLaunched* 메서드에서 알림 단일 항목에 대한 호출을 업데이트해야 합니다.

		Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());


##백 엔드에서 지역화된 알림 보내기

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]





## 다음 단계

템플릿 사용에 대한 자세한 내용은 [알림 허브를 통해 사용자에게 알림: ASP.NET], [알림 허브를 통해 사용자에게 알림: 모바일 서비스] 및 [알림 허브 지침]을 참조하세요. 템플릿 표현 언어에 대한 자세한 내용은 [Windows 스토어용 알림 허브 방법]을 참조하십시오.

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

<!-- Images. -->





















<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[알림 허브를 통해 사용자에게 알림: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[알림 허브를 통해 사용자에게 알림: 모바일 서비스]: /manage/services/notification-hubs/notify-users
[알림 허브를 사용하여 속보 보내기]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[알림 허브 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Windows 스토어용 알림 허브 방법]: http://msdn.microsoft.com/library/jj927172.aspx
 

<!---HONumber=July15_HO3-->