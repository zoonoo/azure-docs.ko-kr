<properties
	pageTitle="예약된 알림을 보내는 방법 | Microsoft Azure"
	description="이 항목에서는 Azure 알림 허브로 예약된 알림 사용에 대해 설명합니다."
	services="notification-hubs"
	documentationCenter=".net"
	keywords="푸시 알림, 푸시 알림, 푸시 알림 예약"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/11/2016"
	ms.author="wesmc"/>

# 방법: 예약된 알림 보내기


##개요

이후 특정 시점에 알림을 보내려는 시나리오가 있지만 알림을 보내는 백 엔드 코드를 다시 시작하는 간편한 방법이 없습니다. 표준 계층 알림 허브는 이후 최대 7일까지 알림을 예약할 수 있는 기능을 지원합니다.

알림을 보낼 때는 다음 예제에 표시된 대로 알림 허브 SDK에서 [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) 클래스를 사용합니다.

	Notification notification = new AppleNotification("{"aps":{"alert":"Happy birthday!"}}");
	var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

또한 해당 notificationId를 사용하여 이전에 예약된 알림을 취소할 수 있습니다.

	await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

보낼 수 있는 예약된 알림 수에는 제한이 없습니다.

<!---HONumber=AcomDC_0622_2016-->