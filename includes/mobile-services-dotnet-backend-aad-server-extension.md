### (옵션) Azure Active Directory에 대해 .NET 모바일 서비스 구성

>[AZURE.NOTE]이 단계는 Azure Active Directory 로그인 공급자에게만 적용되므로 선택 사항입니다.

1. [WindowsAzure.MobileServices.Backend.Security NuGet 패키지](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend.Security)를 설치합니다.

2. Visual Studio에서 App_Start를 확장하고 WebApiConfig.cs를 엽니다. 맨 위에 다음 `using` 문을 추가합니다.

        using Microsoft.WindowsAzure.Mobile.Service.Security.Providers;

3. 또한 `options`이(가) 인스턴스화되는 즉시 WebApiConfig.cs에서 다음 코드를 `Register` 메서드에 추가합니다.

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

<!---HONumber=July15_HO4-->