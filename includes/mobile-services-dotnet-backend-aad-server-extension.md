### (옵션) AAD 로그인에 대해 .NET 모바일 서비스 구성

> [WACOM.NOTE] 이 단계는 Azure Active Directory 로그인 공급자에게만 적용되므로 선택 사항입니다.

1.  **WindowsAzure.MobileServices.Backend.Security** NuGet 패키지를 설치합니다.

2.  Visual Studio에서 App\_Start를 확장하고 WebApiConfig.cs 파일을 엽니다. `options`가 인스턴스화되는 즉시 다음 코드를 `Register` 메서드에 추가합니다.

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));


