

푸시 알림은 일반적으로 호환 라이브러리를 사용하는 모바일 앱 또는 ASP.NET과 같은 백 엔드 서비스에서 전송됩니다. 백 엔드에 라이브러리를 사용할 수 없는 경우 직접 REST API를 사용하여 알림 메시지를 보낼 수도 있습니다.

알림을 보내기 위해 검토할 수 있는 다른 자습서 목록은 다음과 같습니다.

- Azure 모바일 앱: 알림 허브와 통합된 모바일 앱 백 엔드에서 알림을 보내는 방법에 대한 예제는 [iOS 앱에 푸시 알림 추가](../articles/app-service-mobile/app-service-mobile-ios-get-started-push.md)를 참조하세요  
- ASP.NET: [알림 허브를 사용하여 사용자에게 푸시 알림 보내기](../articles/notification-hubs/notification-hubs-aspnet-backend-ios-apple-apns-notification.md)를 참조하세요.
- Azure 알림 허브 Java SDK: Java에서 알림을 보내는 방법은 [Java에서 알림 허브를 사용하는 방법](../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md)을 참조하세요. 이는 Eclipse for Android Development에서 테스트되었습니다.
- PHP: [PHP에서 알림 허브를 사용하는 방법](../articles/notification-hubs/notification-hubs-php-push-notification-tutorial.md)을 참조하세요.


이 자습서의 다음 섹션에서는 [알림 허브 REST 인터페이스](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)를 사용하여 앱에서 직접 알림 메시지를 보내는 방법을 알아봅니다. 장치에서 보낸 알림이 등록된 모든 장치에 수신됩니다.

<!---HONumber=AcomDC_0622_2016-->