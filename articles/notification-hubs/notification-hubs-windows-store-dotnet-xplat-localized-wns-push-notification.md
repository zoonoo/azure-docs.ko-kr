---
title: "알림 허브 지역화된 속보 자습서"
description: "Azure 알림 허브를 사용하여 지역화된 최신 뉴스 알림을 보내는 방법에 대해 알아봅니다."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e864e832b4c50644bf4062dee29d34ff9fe2774e


---
# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>알림 허브를 사용하여 지역화된 속보 보내기
> [!div class="op_single_selector"]
> * [Windows 스토어 C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 
> 

## <a name="overview"></a>개요
이 항목에서는 Azure 알림 허브의 **템플릿** 기능을 사용하여 언어 및 장치별로 지역화된 속보 알림을 브로드캐스트하는 방법을 보여 줍니다. 이 자습서에서는 [알림 허브를 사용하여 속보 보내기]에서 만든 Windows 스토어 앱을 시작합니다. 이 자습서를 완료하면 관심 있는 범주를 등록하고, 알림을 받을 언어를 지정하고, 선택한 범주에 대한 푸시 알림만 해당 언어로 받을 수 있습니다.

이 시나리오는 두 부분으로 분류됩니다.

* Windows 스토어 앱을 사용하면 클라이언트 장치에서 언어를 지정하고, 다른 속보 범주를 구독할 수 있습니다.
* 백 엔드에서 Azure Notification Hubs의 **태그** 및 **템플릿** 기능을 사용하여 알림을 브로드캐스트합니다.

## <a name="prerequisites"></a>필수 조건
[알림 허브를 사용하여 속보 보내기] 자습서를 이미 완료하고 사용 가능한 코드가 있어야 합니다. 이 자습서는 해당 코드를 기반으로 직접 빌드됩니다.

Visual Studio 2012 이상도 필요합니다.

## <a name="template-concepts"></a>템플릿 개념
[알림 허브를 사용하여 속보 보내기] 에서는 **태그** 를 사용하여 다른 뉴스 범주에 대한 알림을 구독하는 앱을 빌드했습니다.
하지만 대부분의 앱은 여러 시장을 대상으로 하므로 지역화해야 합니다. 즉, 알림 자체의 내용을 지역화해서 올바른 장치 집합으로 전달해야 합니다.
이 항목에서는 알림 허브의 **템플릿** 기능을 사용하여 지역화된 속보 알림을 쉽게 제공하는 방법을 보여 줍니다.

참고: 지역화된 알림을 보내는 한 가지 방법은 각 태그의 여러 버전을 만드는 것입니다. 예를 들어 영어, 프랑스어 및 북경어를 지원하려면 세계 뉴스에 대한 3가지 태그 즉, "world_en", "world_fr" 및 "world_ch"가 필요합니다. 그런 다음 이러한 각 태그로 세계 뉴스의 지역화된 버전을 보내야 합니다. 이 항목에서는 템플릿을 사용하여 태그의 확산을 방지하고 여러 메시지를 보낼 필요가 없도록 합니다.

높은 수준의 템플릿을 사용하면 특정 장치에서 알림을 받는 방법을 지정할 수 있습니다. 템플릿은 앱에서 백 엔드로 보낸 메시지에 포함된 속성을 참조하여 정확한 페이로드 형식을 지정합니다. 여기서는 모든 지원되는 언어를 포함하는 로캘을 알 수 없는 메시지를 보냅니다.

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

그런 다음 올바른 속성을 참조하는 템플릿을 사용하여 장치가 등록되도록 합니다. 예를 들어 간단한 알림 메시지를 받으려는 Windows 스토어 앱에 해당 태그가 있는 다음 템플릿을 등록합니다.

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



템플릿은 매우 강력한 기능입니다. 자세한 내용은 [템플릿](notification-hubs-templates-cross-platform-push-messages.md) 문서를 참조하세요. 

## <a name="the-app-user-interface"></a>앱 사용자 인터페이스
이제 [알림 허브를 사용하여 속보 보내기] 항목에서 만든 속보 앱을 수정하고 템플릿을 사용하여 지역화된 속보를 보냅니다.

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
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

## <a name="building-the-windows-store-client-app"></a>Windows 스토어 클라이언트 앱 빌드
1. Notifications 클래스에서 *StoreCategoriesAndSubscribe* 및 *SubscribeToCateories* 메서드에 로캘 매개 변수를 추가합니다.
   
        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }
   
        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            if (categories == null)
            {
                categories = RetrieveCategories();
            }
   
            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);
   
            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }
   
    여기서는 템플릿이 로캘에 따라 달라지는 특정 알림 형식을 등록할 것이기 때문에 *RegisterNativeAsync* 메서드를 호출하는 대신 *RegisterTemplateAsync*를 호출합니다. 또한 알림 메시지용 템플릿, 타일 알림용 템플릿 등 여러 템플릿을 등록할 수도 있으므로 템플릿의 이름("localizedWNSTemplateExample")을 제공합니다. 그런 다음 템플릿을 업데이트하거나 삭제하려면 해당 이름을 지정해야 합니다.
   
    장치에서 동일한 태그로 여러 템플릿을 등록한 경우 해당 태그를 대상으로 하는 수신 메시지는 템플릿별로 하나씩 여러 알림을 장치에 제공합니다. 이 동작은 동일한 논리 메시지로 여러 시각적 알림을 나타내야 하는 경우(예: Windows 스토어 응용 프로그램에 배지와 알림을 모두 표시해야 하는 경우)에 유용합니다.
2. 다음 메서드를 추가하여 저장된 로캘을 검색합니다.
   
        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }
3. MainPage.xaml.cs에서 아래와 같이 로캘 콤보 상자의 현재 값을 검색한 후 Notifications 클래스에 대한 호출에 값을 제공하여 단추 클릭 처리기를 업데이트합니다.
   
        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;
   
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");
   
            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                 categories);
   
            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
                string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
4. 마지막으로 App.xaml.cs 파일에서 로캘을 검색하고 구독할 때 사용하도록 `InitNotificationsAsync` 메서드를 업데이트해야 합니다.
   
        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

## <a name="send-localized-notifications-from-your-back-end"></a>백 엔드에서 지역화된 알림 보내기
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

<!-- Anchors. -->
[템플릿 개념]: #concepts
[앱 사용자 인터페이스]: #ui
[Windows 스토어 클라이언트 앱 빌드]: #building-client
[백 엔드에서 알림 보내기]: #send
[다음 단계]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[알림 허브를 통해 사용자에게 알림: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[알림 허브를 통해 사용자에게 알림: 모바일 서비스]: /manage/services/notification-hubs/notify-users
[알림 허브를 사용하여 속보 보내기]: /manage/services/notification-hubs/breaking-news-dotnet

[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Mobile Services 시작]: /develop/mobile/tutorials/get-started/#create-new-service
[데이터 작업 시작]: /develop/mobile/tutorials/get-started-with-data-dotnet
[인증 시작]: /develop/mobile/tutorials/get-started-with-users-dotnet
[푸시 알림 시작]: /develop/mobile/tutorials/get-started-with-push-dotnet
[앱 사용자에 대한 푸시 알림]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[스크립트를 통해 사용자 권한 부여]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript 및 HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns 개체]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[알림 허브 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[iOS용 알림 허브 방법]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs Windows 스토어용 방법]: http://msdn.microsoft.com/library/jj927172.aspx



<!--HONumber=Nov16_HO3-->


