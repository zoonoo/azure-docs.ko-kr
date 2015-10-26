<properties 
	pageTitle="푸시 알림 내에서 이모지 이모티콘 사용" 
	description="푸시 알림 내에서 이모지 이모티콘을 사용하는 방법"					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="piyushjo" />

#푸시 알림 내에서 이모지 이모티콘 사용

푸시 알림에 이모지 이모티콘을 포함할 수 있습니다. 현재 Azure Mobile Engagement에서는 앱 내 및 외부 텍스트 알림에 대해 3바이트 이모지 이모티콘만 지원합니다. 다음 단계를 따르세요.

1.  먼저 3바이트 이모지 이모티콘 라이브러리를 찾아야 합니다. 다음 [링크](http://stackoverflow.com/questions/10153529/emoji-on-mysql-and-php-why-some-symbol-yes-other-not)에서 사용 가능한 모든 이모지 이모티콘을 찾을 수 있습니다.

	![][1]

2. Azure Mobile Engagement 포털에서 도달률 탭으로 이동합니다.

3. 푸시 알림 유형(공지, 설문 조사 등)을 선택합니다. 이 예제에서는 공지 푸시를 선택합니다.

4. 알림 텍스트에 도달할 때까지 여러 알림 필드를 지정합니다. 이 위치에서 이모지 이모티콘을 추가합니다. 제목, 메시지 또는 두 항목 모두에 이모티콘을 넣을 수 있습니다.

	![][2]

5. 이전 링크에서 사용할 이모지 이모티콘을 잘라냅니다. 제목 및/또는 메시지의 선택한 위치에 직접 붙여넣습니다.

6. 알림에 대한 다른 필드를 완료하고 저장합니다.

7. 테스트를 실행하거나 공지를 활성화할 때 지정된 대로 이모티콘이 있는 알림이 표시됩니다.

	![][3] 
	![][4]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/emoji.png
[2]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/notification_android.png
[4]: ./media/mobile-engagement-use-emoji-with-push/notification_ios.png
 

<!---HONumber=Oct15_HO3-->