<properties
   pageTitle="앱을 등록하고 코드에서 SQL 데이터베이스에 연결하기 위한 클라이언트 ID 및 키 가져오기 | Microsoft Azure"
   description="코드에서 SQL 데이터베이스에 액세스하기 위한 클라이언트 ID 및 키를 가져옵니다."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="03/15/2016"
   ms.author="sstein"/>

# 코드에서 SQL 데이터베이스에 연결하기 위한 클라이언트 ID 및 키 가져오기

코드에서 SQL 데이터베이스를 만들고 관리하려면 Azure 리소스가 만들어진 구독과 연결된 AAD(Azure Active Directory) 도메인에 앱을 등록해야 합니다. 응용 프로그램을 등록하면 Azure는 앱을 인증하기 위해 코드에 있어야 하는 클라이언트 ID 및 키를 생성합니다. 자세한 내용은 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)를 참조하세요.

## 네이티브 클라이언트 응용 프로그램 등록 및 클라이언트 ID 가져오기

새 응용 프로그램을 만들고 등록하려면 다음을 수행합니다.

1. [클래식 포털](https://manage.windowsazure.com/)에 로그인합니다(현재 응용 프로그램 등록은 클래식 포털에서 수행해야 함).
1. 메뉴에서 **Active Directory**를 찾은 후 선택합니다.

    ![AAD][1]

2. 디렉터리를 선택하여 응용 프로그램을 인증하고 해당 **이름**을 클릭합니다.

    ![디렉터리][4]

3. 디렉터리 페이지에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램][5]

4. **추가**를 클릭하여 새 응용 프로그램을 만듭니다.

    ![응용 프로그램 추가][6]

5. 앱의 **이름**을 입력하고 **네이티브 클라이언트 응용 프로그램**을 선택합니다.

    ![응용 프로그램 추가][7]

6. **리디렉션 URI**를 입력합니다. 실제 끝점일 필요는 없으며 유효한 URI이면 됩니다.

    ![응용 프로그램 추가][8]

7. 앱 만들기를 완료하고 **구성**을 클릭한 후 **클라이언트 ID**를 복사합니다(코드에 클라이언트 ID가 필요함).

    ![클라이언트 ID 가져오기][9]


1. 페이지 아래로 스크롤하여 **응용 프로그램 추가**를 클릭합니다.
1. **Microsoft 앱**을 선택합니다.
1. **Microsoft Azure Service Management API**를 선택하고 마법사를 완료합니다.
2. **다른 응용 프로그램에 대한 권한** 섹션에서 **Microsoft Azure Service Management API**를 찾은 후 **위임된 권한**을 클릭합니다.
3. **Azure 서비스 관리 액세스...**를 선택합니다.

    ![권한][2]

2. 페이지 아래쪽에서 **저장**을 클릭합니다.



## 웹앱(또는 웹 API)을 등록하고 클라이언트 ID 및 키 받기

새 응용 프로그램을 만들고 올바른 active directory에 등록하려면 다음을 수행합니다.

1. [클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.
1. 메뉴에서 **Active Directory**를 찾은 후 선택합니다.

    ![AAD][1]

2. 디렉터리를 선택하여 응용 프로그램을 인증하고 해당 **이름**을 클릭합니다.

    ![디렉터리][4]

3. 디렉터리 페이지에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램][5]

4. **추가**를 클릭하여 새 응용 프로그램을 만듭니다.

    ![응용 프로그램 추가][6]

5. 앱의 **이름**을 입력하고 **웹 응용 프로그램 및/또는 웹 API**를 선택합니다.

    ![응용 프로그램 추가][10]

6. **로그온 URL** 및 **앱 ID URI**를 제공합니다. 실제 끝점일 필요는 없으며 유효한 URI이면 됩니다.

    ![응용 프로그램 추가][11]

7. 앱 만들기를 마치면 **구성**을 클릭합니다.

    ![구성][12]

8. **키** 섹션으로 스크롤한 후 **기간 선택** 목록에서 **1년**을 선택합니다. 저장한 후에 키 값이 표시되므로 나중에 돌아와서 키를 복사합니다.

    ![키 보존 기간 설정][13]



1. 페이지 아래로 스크롤하여 **응용 프로그램 추가**를 클릭합니다.
1. **Microsoft 앱**을 선택합니다.
1. **Microsoft Azure Service Management API**를 찾아 선택하고 마법사를 완료합니다.
2. **다른 응용 프로그램에 대한 권한** 섹션에서 **Microsoft Azure Service Management API**를 찾은 후 **위임된 권한**을 클릭합니다.
3. **Azure 서비스 관리 액세스...**를 선택합니다.

    ![권한][2]

2. 페이지 아래쪽에서 **저장**을 클릭합니다.
3. 저장이 완료되면 클라이언트 ID 및 키를 찾아 저장합니다.

    ![웹앱 암호][14]



## 도메인 이름 가져오기

도메인 이름은 경우에 따라 인증 코드에 필요합니다. 적절한 도메인 이름을 식별하는 간단한 방법은 다음과 같습니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 오른쪽 위에 있는 이름 위로 마우스를 이동하고 팝업 창에 나타나는 도메인을 참고합니다.

    ![도메인 이름 식별][3]




## 예제 콘솔 응용 프로그램


Visual Studio(**도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**)의 [패키지 관리자 콘솔](http://docs.nuget.org/Consume/Package-Manager-Console)을 사용하는 다음 패키지를 설치하여 필요한 관리 라이브러리를 가져옵니다.


    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


**SqlDbAuthSample**이라는 콘솔 앱을 만들고 **Program.cs**의 내용을 다음과 같이 바꿉니다.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbAuthSample
    {
    class Program
    {
        static void Main(string[] args)
        {
            token = GetAccessTokenforNativeClient();
            // token = GetAccessTokenUsingUserCredentials(new UserCredential("<email address>"));
            // token = GetAccessTokenForWebApp();

            Console.WriteLine("Signed in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Press Enter to continue...");
            Console.ReadLine();
        }


        // authentication variables (native)
        static string nclientId = "<your client id>";
        static string nredirectUri = "<your redirect URI>";
        static string ndomainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        private static AuthenticationResult GetAccessTokenforNativeClient()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + ndomainName /* Tenant ID or AAD domain */);

            token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    nclientId,
            new Uri(nredirectUri),
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }


        // authentication variables (web)
        static string wclientId = "<your client id>";
        static string wkey = "<your key>";
        static string wdomainName = "<i.e. microsoft.onmicrosoft.com>";

        private static AuthenticationResult GetAccessTokenForWebApp()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + wdomainName /* Tenant ID or AAD domain */);

            ClientCredential cc = new ClientCredential(wclientId, wkey);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                cc);

            return token;
        }


        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + ndomainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                nclientId /* application client ID from AAD*/,
                new Uri(nredirectUri) /* redirect URI */,
                PromptBehavior.Auto,
                new UserIdentifier(userCredential.UserName, UserIdentifierType.RequiredDisplayableId));

            return token;
        }
    }
    }


Azure AD 인증과 관련한 특정 코드 예제는 MSDN의 [SQL Server 보안 블로그](http://blogs.msdn.com/b/sqlsecurity/)를 참조하세요.

## 참고 항목

- [C#으로 SQL 데이터베이스 만들기](sql-database-get-started-csharp.md)
- [Azure Active Directory 인증을 사용하여 SQL 데이터베이스에 연결](sql-database-aad-authentication.md)



<!--Image references-->
[1]: ./media/sql-database-client-id-keys/aad.png
[2]: ./media/sql-database-client-id-keys/permissions.png
[3]: ./media/sql-database-client-id-keys/getdomain.png
[4]: ./media/sql-database-client-id-keys/aad2.png
[5]: ./media/sql-database-client-id-keys/aad-applications.png
[6]: ./media/sql-database-client-id-keys/add.png
[7]: ./media/sql-database-client-id-keys/add-application.png
[8]: ./media/sql-database-client-id-keys/add-application2.png
[9]: ./media/sql-database-client-id-keys/clientid.png
[10]: ./media/sql-database-client-id-keys/add-application-web.png
[11]: ./media/sql-database-client-id-keys/add-application-app-properties.png
[12]: ./media/sql-database-client-id-keys/configure.png
[13]: ./media/sql-database-client-id-keys/key-duration.png
[14]: ./media/sql-database-client-id-keys/web-secrets.png

<!---HONumber=AcomDC_0316_2016-->