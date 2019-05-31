---
title: SQL에 ActiveDirectoryInteractive 연결 | Microsoft Docs
description: SqlAuthenticationMethod.ActiveDirectoryInteractive 모드를 사용하여 Azure SQL Database에 연결하는 C# 코드와 설명입니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
manager: craigg
ms.openlocfilehash: bc7274308b8a349d16866f107eac4a57e115be9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160825"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Azure Multi-factor Authentication 사용 하 여 Azure SQL Database에 연결

이 문서에서는 제공 된 C# Azure SQL Database에 연결 하는 프로그램입니다. 프로그램을 지 원하는 대화형 모드 인증을 사용 하 여 [Azure Multi-factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)합니다.

SQL 도구에 대 한 다단계 인증 지원에 대 한 자세한 내용은 참조 하세요. [SQL Server 데이터 도구 (SSDT)에서 Azure Active Directory 지원을](https://docs.microsoft.com/sql/ssdt/azure-active-directory)합니다.

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Azure SQL Database에 대 한 Multi-factor Authentication

부터.NET Framework 버전 4.7.2부터 열거형 [ `SqlAuthenticationMethod` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) 에 새 값: `ActiveDirectoryInteractive`합니다. 클라이언트에서 C# 열거형 값을 프로그램을 Azure SQL database에 연결 하려면 Multi-factor Authentication을 지 원하는 Azure Active Directory (Azure AD) 대화형 모드를 사용 하려면 체제에 지시 합니다. 그러면 프로그램을 실행하는 사용자에게 다음 대화 상자가 표시됩니다.

* Azure AD 사용자 이름을 표시하고 사용자의 암호를 요청하는 대화 상자.

   사용자의 도메인을 Azure AD와 페더레이션 되어 있는 경우 암호가 필요 하므로이 대화 상자 표시 되지 않습니다.

   Multi-factor Authentication 사용자에 적용 하는 Azure AD 정책에 다음 두 대화 상자를 표시 됩니다.

* Multi-factor Authentication을 통해 사용자가 처음으로 텍스트 메시지를 보내기 위한 휴대폰 번호를 요청 하는 대화 상자가 표시 됩니다. 각 메시지는 사용자가 그 다음 대화 상자에서 입력해야 하는 *확인 코드*를 제공합니다.

* 시스템 휴대폰에 전송한 Multi-factor Authentication 확인 코드를 요청 하는 대화 상자입니다.

Multi-factor Authentication을 요구 하도록 Azure AD를 구성 하는 방법에 대 한 정보를 참조 하세요 [클라우드에서 Azure Multi-factor Authentication을 사용 하 여 시작](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)합니다.

이러한 대화 상자 스크린샷 대해서 [SQL Server Management Studio 및 Azure AD에 대 한 다단계 인증 구성](sql-database-ssms-mfa-authentication-configure.md)합니다.

> [!TIP]
> .NET Framework Api를 사용 하 여 검색할 수 있습니다 합니다 [.NET API 브라우저 도구 페이지](https://docs.microsoft.com/dotnet/api/)합니다.
>
> 사용 하 여 직접 검색할 수도 있습니다는 [선택적? 용어 =&lt;값을 검색&gt; 매개 변수](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)합니다.

## <a name="configure-your-c-application-in-the-azure-portal"></a>Azure Portal에서 C# 애플리케이션 구성

시작하기 전에 [Azure SQL Database 서버를 만들었고](sql-database-get-started-portal.md) 사용할 수 있어야 합니다.

### <a name="register-your-app-and-set-permissions"></a>앱 등록 및 사용 권한 설정

Azure AD 인증을 사용하려면 C# 프로그램이 Azure AD 애플리케이션으로 등록해야 합니다. 앱을 등록하려면 Azure AD 관리자이거나 사용자가 할당한 Azure AD *애플리케이션 개발자* 역할이 있어야 합니다. 역할 할당에 대 한 자세한 내용은 참조 하세요. [Azure Active Directory를 사용 하 여 사용자에 게 관리자 및 비관리자 역할을 할당할](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)합니다.

앱 등록을 완료 생성 하 고 표시를 **응용 프로그램 ID**합니다. 연결하려면 프로그램이 이 ID를 포함해야 합니다.

애플리케이션에 등록하고 필요한 권한을 설정하려면

1. Azure Portal에서 **Azure Active Directory** > **앱 등록** > **새 애플리케이션 등록**을 선택합니다.

    ![앱 등록](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    앱 등록을 만든 후 합니다 **응용 프로그램 ID** 값 생성 되 고 표시 합니다.

    ![표시된 앱 ID](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. 선택 **등록 된 앱** > **설정** > **필수 권한** > **추가**합니다.

    ![등록된 앱의 권한 설정](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. 선택 **필요한 권한** > **추가** > **API 선택** > **Azure SQL Database**.

    ![Azure SQL Database에 대한 API 액세스 추가](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. 선택 **API 액세스** > **권한을 선택** > **위임 된 권한**합니다.

    ![Azure SQL Database에 대한 API에 권한 위임](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>SQL Database 서버에 대해 Azure AD 관리자 설정

에 대 한 사용자 C# 실행 프로그램에서 Azure SQL 서버 관리자에서 SQL Database 서버에 대 한 Azure AD 관리자를 할당 해야 합니다. 

에 **SQL Server** 페이지에서 **Active Directory 관리자** > **관리자 설정**합니다.

Azure SQL Database에 대 한 Azure AD 관리자 및 사용자에 대 한 자세한 내용은 참조의 스크린샷에서 [구성 및 SQL Database를 사용 하 여 Azure Active Directory 인증을 관리](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)합니다.

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>특정 데이터베이스에 관리자가 아닌 사용자 추가(선택 사항)

SQL Database 서버의 Azure AD 관리자는 C# 예제 프로그램을 실행할 수 있습니다. Azure AD 사용자는 데이터베이스에 있는 경우 해당 프로그램을 실행할 수 있습니다. Azure AD SQL 관리자 또는 데이터베이스에 이미 있으며 데이터베이스에 대해 `ALTER ANY USER` 권한이 있는 Azure AD 사용자는 사용자를 추가할 수 있습니다.

SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) 명령을 사용하여 데이터베이스에 사용자를 추가할 수 있습니다. 예는 `CREATE USER [<username>] FROM EXTERNAL PROVIDER`입니다.

자세한 내용은 [SQL Database, Managed Instance 및 SQL Data Warehouse에서 인증을 위해 Azure Active Directory 인증 사용](sql-database-aad-authentication.md)을 참조하세요.

## <a name="new-authentication-enum-value"></a>새 인증 열거형 값

C# 예제는 [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) 네임스페이스에 의존합니다. Multi-factor Authentication에 대 한 특별 한 관심은 열거형 `SqlAuthenticationMethod`, 다음 값을 가진는:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Multi-factor Authentication을 구현 하는 Azure AD 사용자 이름을 사용 하 여이 값을 사용 합니다. 이 문서에서는 이 값을 집중적으로 다룹니다. Multi-factor Authentication이이 사용자에 지정 된 경우 사용자 암호를 선택한 다음 Multi-factor Authentication 유효성 검사에 대 한 대화 상자를 표시 하 여 대화형 환경을 생성 합니다. 이 값은 .NET framework 버전 4.7.2부터 사용할 수 있습니다.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  *페더레이션된* 계정에 이 값을 사용합니다. 페더레이션된 계정에서 사용자 이름은 Windows 도메인에 알려져 있습니다. 이 인증 방법은 Multi-factor Authentication을 지원 하지 않습니다.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Azure AD 사용자 이름 및 암호가 필요한 인증에 이 값을 사용합니다. Azure SQL Database가 인증을 수행합니다. 이 메서드는 Multi-factor Authentication을 지원 하지 않습니다.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Azure Portal에서 C# 매개 변수 값 설정

C# 프로그램이 성공적으로 실행되려면 정적 필드에 적절한 값을 할당해야 합니다. 다음은 예제 값을 포함하는 필드입니다. 필요한 값을 얻을 수 있는 Azure 포털 위치는도 표시 됩니다.

| 정적 필드 이름 | 예제 값 | Azure Portal 내 위치 |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL 서버** > **이름으로 필터링** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Azure Active Directory** > **사용자** > **새 게스트 사용자** |
| Initial_DatabaseName | "myDatabase" | **SQL 서버** > **SQL 데이터베이스** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** > **앱 등록** > **이름별로 검색** > **애플리케이션 ID** |
| RedirectUri | new Uri("https://mywebserver.com/") | **Azure Active Directory** > **앱 등록** > **이름별으로 검색** > *[Your-앱-등록]*  >  **설정** > **RedirectURIs**<br /><br />이 문서에 대 한 유효한 값 이므로 한 RedirectUri를 세밀 하 게 여기 사용 되지 않습니다. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>SQL Server Management Studio를 사용 하 여 확인 합니다.

실행 하기 전에 C# 프로그램인 것 사용자 설정 및 구성 SQL Server Management Studio (SSMS)에서 정확한 지 확인 하는 것이 좋습니다. 그런 후에 C# 프로그램 오류 원인을 소스 코드로 좁혀갈 수 있습니다.

### <a name="verify-sql-database-firewall-ip-addresses"></a>SQL Database 방화벽 IP 주소 확인

C# 프로그램을 실행하려는 같은 컴퓨터, 같은 건물에서 SSMS를 실행합니다. 이 테스트에 대 한 모든 **인증** 모드가 확인 합니다. 데이터베이스 서버 방화벽에서 IP 주소를 허용하지 않는 경우 [Azure SQL Database 서버 수준 및 데이터베이스 수준 방화벽 규칙](sql-database-firewall-configure.md)을 참조하여 도움을 얻을 수 있습니다.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Azure Active Directory Multi-factor Authentication 확인

이번에는 **인증**을 **Active Directory - MFA 지원을 통한 유니버설 인증**으로 설정하고 SSMS를 다시 실행합니다. 이 옵션에는 SSMS 버전 17.5 이상이 필요합니다.

자세한 내용은 [SSMS와 Azure AD에 대 한 Multi-factor Authentication 구성](sql-database-ssms-mfa-authentication-configure.md)합니다.

> [!NOTE]
> 데이터베이스에 게스트 사용자 인 경우에 또한 데이터베이스에 대 한 Azure AD 도메인 이름이 필요 합니다. 선택 **옵션** > **AD 도메인 이름 또는 테 넌 트 ID**합니다. Azure Portal에서 도메인 이름을 찾으려면 **Azure Active Directory** > **사용자 지정 도메인 이름**을 선택합니다. C# 예제 프로그램에서는 도메인 이름을 지정하지 않아도 됩니다.

## <a name="c-code-example"></a>C# 코드 예제

예제 C# 프로그램은 [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL 어셈블리를 사용합니다.

Visual Studio에서 이 패키지를 설치하려면 **프로젝트** > **NuGet 패키지 관리**를 선택합니다. **Microsoft.IdentityModel.Clients.ActiveDirectory**를 검색하고 설치합니다.

예제입니다 C# 소스 코드입니다.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

이것이의 예는 C# 출력을 테스트 합니다.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 Azure SQL 데이터베이스에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대한 내용은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조합니다. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
