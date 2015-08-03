포털에서 다운로드한 서버 프로젝트에는 이미 푸시가 사용하도록 설정되어 있습니다.

ASP.NET 프로젝트에서 다음을 확인해야 합니다.

* `Microsoft.Azure.Mobile.Server.Notifications` NuGet 패키지가 설치되어 있습니다.

* WebApiConfig.cs에서 MobileAppConfiguration 개체에 대해 `UseDefaultConfiguration()` 메서드가 호출됩니다. 이 메서드는 위 NuGet 패키지에서 제공되는 `AddPushNotifications()` 확장 메서드를 호출하며,

<!---HONumber=July15_HO4-->