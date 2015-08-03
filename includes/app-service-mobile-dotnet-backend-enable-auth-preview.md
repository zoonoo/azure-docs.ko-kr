포털에서 다운로드한 서버 프로젝트에는 이미 인증이 사용하도록 설정되어 있습니다.

ASP.NET 프로젝트에서 다음을 확인해야 합니다.

* `Microsoft.Azure.Mobile.Server.Authentication` NuGet 패키지가 설치되어 있습니다.

* WebApiConfig.cs에서 MobileAppConfiguration 개체에 대해 `UseDefaultConfiguration()` 메서드가 호출됩니다. 이 메서드는 위 NuGet 패키지에서 제공되는 `AddAppServiceAuthentication()` 확장 메서드를 호출하며, OWIN 시작 중에 `app.UseAppServiceAuthentication()`을 호출하여 인증에 필요한 OWIN 미들웨어를 등록합니다.

<!---HONumber=July15_HO4-->