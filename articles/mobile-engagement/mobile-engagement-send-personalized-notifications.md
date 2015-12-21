<properties 
	pageTitle="Azure Mobile Engagement를 사용하여 개인 설정된 알림 보내기" 
	description="알림에 이름과 같은 사용자 프로필 정보를 포함하여 개인 설정된 알림을 보내는 방법"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="all" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="piyushjo" />

#사용자 이름을 포함하여 알림 개인 설정

질문에서 알림을 앱 사용자에게 보다 매력적으로 만들려면 알림을 개인 설정하는 것을 고려해야 합니다. 한 가지 강력한 방법은 선택적으로 앱 사용자 이름을 사용하여 알림을 보다 개인적으로 만드는 것입니다. 여기서 한 가지 주의 사항은 알림에 사용자 이름을 추가하는 방법에 신중해야 합니다. 이 전략을 남용하는 경우 일부 앱 사용자에게는 부정적인 인식을 줄 수 있습니다. 또한 이 방법을 사용하기 전에 사용자가 모바일 앱에서 전체 동의로 이러한 개인 정보를 귀하에게 제공하도록 선택했는지 확인해야 합니다.

기술적으로 Azure Mobile Engagement를 사용하면 아래 단계에 따라 알림 개인 설정을 수행할 수 있습니다. 아래 단계에서는 알림에 사용자 이름을 포함하는 시나리오를 사용할 것입니다. 값을 모바일 앱에 통합된 SDK나 API를 통해 전달할 수 있는 앱 정보 또는 태그 개념을 사용합니다. 이러한 앱 정보 또는 태그는 다음과 같은 용도로 사용할 수 있습니다.

1. 앱 정보 값을 기반으로 특정 사용자에게 알림을 대상 지정 
2. 알림을 해당 장치에 보내는 동안 장치/사용자에 대한 값으로 대체될 알림에 있는 자리 표시자 

> [AZURE.IMPORTANT]알림마다 앱 정보 값을 대체하는 추가 처리로 인해 알림 보내기 속도가 느려지는 것을 확인할 수 있습니다.

##Mobile Engagement 포털에서 앱 정보 등록

1) 먼저 Azure 포털에서 앱 정보 또는 태그를 등록합니다. 이를 위해 **설정** -> **태그(앱 정보)**로 이동합니다.

![][1]

2) **새 태그(앱 정보)**를 클릭하고 이름은 *user\_name*으로, 형식은 *string*으로 입력하고 **제출**을 클릭합니다.

![][2]

3) 마지막으로 이 앱 정보가 다음과 같이 등록되었음이 표시됩니다.

![][3]

##클라이언트 SDK에서 앱 정보 보내기

여기서는 Windows 유니버설 앱 예제를 사용하지만 다른 SDK를 위한 동등한 메서드도 존재합니다.

인증 후 사용자로부터 이름과 같은 프로필 정보를 가져오는 모바일 앱에 메서드가 있고 여기서 `SendAppInfo` 메서드를 호출하고 Mobile Engagement 서비스 백 엔드에 미리 등록한 `user_name` 앱 정보의 값을 채운다고 가정합니다.

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

##개인 설정된 알림 보내기

이제 이 **user\_name**을 사용하여 알림을 보낼 준비가 되었습니다.

1) **연결** 탭에서 Mobile Engagement 포털로 이동하여 알림을 만들고 이 자리 표시자를 다음 형식으로 알림 제목 또는 본문의 아무 위치에나 사용할 수 있습니다.

![][4]

> [AZURE.NOTE]user\_name 앱 정보가 설정되지 않은 모든 사용자는 어떠한 알림도 받지 못합니다.

2) Mobile Engagement에서 이 알림을 보낼 장치를 선택하면 이 앱 정보를 확인하고 자리 표시자의 값을 대체합니다. 예를 들어 사용자에 대해 `str = "Scott"`을 설정한 경우 장치 등록이 이 사용자의 **user\_name = SCOTT** 앱 정보와 연결되고 이 사용자는 다음과 같은 형식으로 앱 푸시 알림을 보게 됩니다.

![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png

<!---HONumber=AcomDC_1210_2015-->