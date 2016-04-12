<properties
	pageTitle="C# 쿼리로 SQL 데이터베이스에 연결 | Microsoft Azure"
	description="C#에 프로그램을 작성하여 SQL 데이터베이스에 쿼리하고 연결합니다. IP 주소, 연결 문자열, 보안 로그인 및 무료 Visual Studio에 대한 정보입니다."
	services="sql-database"
	keywords="c# 데이터베이스 쿼리, c# 쿼리, 데이터베이스 연결, SQL C#"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="02/05/2016"
	ms.author="genemi"/>


# C&#x23;에 프로그램을 작성하여 SQL 데이터베이스에 쿼리하고 연결합니다.

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

C#에 프로그램을 작성하여 클라우드의 Azure SQL 데이터베이스에 쿼리하고 연결하는 방법을 알아봅니다.

이 문서에서는 Azure SQL 데이터베이스, C#, 및 ADO.NET를 처음 시도하는 사용자를 위한 모든 단계를 설명합니다. Microsoft SQL Server 및 C#를 경험해 본 다른 사용자는 일부 단계를 생략하고 SQL 데이터베이스에 특정된 항목에 초점을 맞출 수 있습니다.


## 필수 조건


C# 쿼리 코드 샘플을 실행하려면 다음이 있어야 합니다.


- Azure 계정 및 구독 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.


- Azure SQL 데이터베이스 서비스에서 **AdventureWorksLT** 데모 데이터베이스입니다.
 - 몇 분 안에 [데모 데이터베이스를 만듭니다.](sql-database-get-started.md)


- Visual Studio 2013 업데이트 4(이후) Microsoft는 이제 Visual Studio 커뮤니티를 *무료*로 제공합니다.
 - [Visual Studio Community, 다운로드](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [무료 Visual Studio에 대한 더 많은 옵션](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - 이 항목의 뒷부분에 나오는 [단계](#InstallVSForFree)는 [Azure 포털](https://portal.azure.com/)이 Visual Studio의 설치를 어떻게 안내하는지를 설명합니다.


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## 1단계: Visual Studio Community를 무료로 설치


Visual Studio를 설치해야 하는 경우 다음을 수행할 수 있습니다.

- 무료 다운로드 및 기타 옵션을 제공하는 Visual Studio 제품 웹 페이지에 브라우저를 탐색하여 Visual Studio 커뮤니티를 무료로 설치합니다.
- [Azure 포털](https://portal.azure.com/)은 다음에 설명할 다운로드 웹 페이지를 안내합니다.


### Azure 포털을 통한 Visual Studio


1. [Azure 포털](https://portal.azure.com/)(http://portal.azure.com/)을 통해 로그인합니다.

2. **모두*찾아보기** > **SQL 데이터베이스**를 클릭합니다. 블레이드는 데이터베이스에 대한 해당 검색을 엽니다.

3. 위쪽의 텍스트 상자 시작에서 **AdventureWorksLT** 데이터베이스의 이름을 입력하기 시작합니다.

4. 서버에서 데이터베이스에 대한 행을 보려면 행을 클릭합니다. 데이터베이스에 블레이드가 열립니다.

5. 편의를 위해 이전 블레이드 각각에서 최소화 컨트롤을 클릭합니다.

6. 데이터베이스 블레이드의 위쪽에서 **Visual Studio에서 열기** 단추를 클릭합니다. Visual Studio에 대한 블레이드가 링크로 열려서 Visual Studio에 위치를 설치합니다.

	![Visual Studio에서 열기 단추][20-OpenInVisualStudioButton]

7. **Community(무료)** 링크 또는 유사한 링크를 클릭합니다. 새 웹 페이지를 추가합니다.

8. 새 웹 페이지에서 링크를 사용하여 Visual Studio를 설치합니다.

9. Visual Studio를 설치한 후에 **Visual Studio에서 열기** 블레이드에서 **Visual Studio에서 열기** 단추를 클릭합니다. Visual Studio를 엽니다.

10. 해당 **SQL Server 개체 탐색기** 창의 혜택을 위해 Visual Studio가 대화 상자에서 연결 문자열 필드를 입력하도록 요청합니다.
 - **Windows 인증**이 아니라 **SQL 서버 인증**을 선택합니다.
 - **AdventureWorksLT** 데이터베이스를 지정해야 합니다.(대화 상자의 **옵션** > **연결 속성**)

11. **SQL Server 개체 탐색기**에서 데이터베이스에 대한 노드를 확장합니다.


## 2단계: Visual Studio에서 새 프로젝트 만들기


Visual Studio에서 C# > Windows > **콘솔 응용 프로그램**에 대한 기초 템플릿을 기반으로 하는 새 프로젝트를 만듭니다.


1. **파일** > **새로 만들기** > **프로젝트**를 클릭합니다. **** 대화 상자가 표시됩니다.

2. **설치됨**에서 C# 및 Windows를 확장하므로 **콘솔 응용 프로그램** 옵션이 가운데 창에 나타납니다.

	![새 프로젝트 대화 상자][30-VSNewProject]

2. **이름**에 **ConnectAndQuery\_Example**을 입력합니다. **확인**을 클릭합니다.


## 3단계: 구성 처리에 어셈블리 참조 추가


C# 샘플이 .NET Framework 어셈블리 **System.Configuration.dll**를 사용하므로 그에 대한 참조를 추가하겠습니다.


1. **솔루션 탐색기** 창에서 **참조** > **참조 추가**를 마우스 오른쪽 단추로 클릭합니다. **참조 관리자** 창이 표시됩니다.

2. **어셈블리** > **프레임 워크**를 확장합니다.

3. 스크롤한 다음 **System.Configuration**에 강조 표시를 클릭합니다. 선택한 경우 해당 확인란을 확인합니다.

4. **확인**을 클릭합니다.

5. **빌드** > **솔루션 빌드** 메뉴로 프로그램을 컴파일합니다.


## 4단계: 연결 문자열 가져오기


[Azure 포털](https://portal.azure.com/)을 사용하여 SQL 데이터베이스에 필요한 연결 문자열을 복사합니다.

첫 번째로 Azure SQL 데이터베이스 **AdventureWorksLT** 데이터베이스에 Visual Studio를 연결합니다.


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


## 5단계: 연결 문자열을 App.config 파일에 추가


1. Visual Studio의 솔루션 탐색기 창에서 App.config 파일을 엽니다.

2. 다음 예제 App.config 코드 샘플과 같이 **&#x3c;configuration&#x3e; &#x3c;/configuration&#x3e;** 요소를 추가합니다.
 - *{your\_placeholders}* 는 실제 값으로 바꿉니다.

```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>

		<connectionStrings>
			<clear />
			<add name="ConnectionString4NoUserIDNoPassword"
			connectionString="Server=tcp:{your_serverName_here}.database.windows.net,1433; Database={your_databaseName_here}; Connection Timeout=30; Encrypt=True; TrustServerCertificate=False;"
			/>
		</connectionStrings>
	</configuration>
```

3. App.config 변경 내용을 저장합니다.

4. 솔루션 탐색기 창에서 **App.config** 노드를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.

5. **출력 디렉터리로 복사**를 **항상 복사**로 설정합니다.
 - 이렇게 하면 App.config 파일의 내용을 만들어 &#x2a;.exe 파일이 빌드된 디렉터리에 있는 &#x2a;.exe.config 파일의 내용을 바꿉니다. &#x2a;.exe를 다시 컴파일할 때마다 교체가 발생합니다.
 - 샘플 C# 프로그램이 실행될 때 &#x2a;.exe.config 파일을 읽습니다.

	![출력 디렉터리로 복사 = 항상 복사][50-VSCopyToOutputDirectoryProperty]


## 6단계: 샘플 C# 코드에 붙여넣기


1. Visual Studio에서 **솔루션 탐색기** 창을 사용하여 **Program.cs** 파일을 엽니다.

	![샘플 C# 쿼리 코드에 붙여넣기][40-VSProgramCsOverlay]

2. 다음 샘플 C# 코드에 붙여넣어 Program.cs에서 모든 시작 코드를 덮어씁니다.
 - 짧은 코드 샘플을 원하는 경우 전체 연결 문자열을 변수 **SQLConnectionString**에 리터럴로 할당할 수 있습니다. **GetConnectionStringFromExeConfig** 및 **GatherPasswordFromConsole**이라는 두 개의 메서드를 지울 수 있습니다.


```
using System;  // C#
using G = System.Configuration;   // System.Configuration.dll
using D = System.Data;            // System.Data.dll
using C = System.Data.SqlClient;  // System.Data.dll
using T = System.Text;

namespace ConnectAndQuery_Example
{
	class Program
	{
		static void Main()
		{
			string connectionString4NoUserIDNoPassword,
				password, userName, SQLConnectionString;

			// Get most of the connection string from ConnectAndQuery_Example.exe.config
			// file, in the same directory where ConnectAndQuery_Example.exe resides.
			connectionString4NoUserIDNoPassword = Program.GetConnectionStringFromExeConfig
				("ConnectionString4NoUserIDNoPassword");
			// Get the user name from keyboard input.
			Console.WriteLine("Enter your User ID, without the trailing @ and server name: ");
			userName = Console.ReadLine();
			// Get the password from keyboard input.
			password = Program.GatherPasswordFromConsole();

			SQLConnectionString = "Password=" + password + ';' +
				"User ID=" + userName + ";" + connectionString4NoUserIDNoPassword;

			// Create an SqlConnection from the provided connection string.
			using (C.SqlConnection connection = new C.SqlConnection(SQLConnectionString))
			{
				// Formulate the command.
				C.SqlCommand command = new C.SqlCommand();
				command.Connection = connection;

				// Specify the query to be executed.
				command.CommandType = D.CommandType.Text;
				command.CommandText = @"
					SELECT TOP 9 CustomerID, NameStyle, Title, FirstName, LastName
					FROM SalesLT.Customer;  -- In AdventureWorksLT database.
					";
				// Open a connection to database.
				connection.Open();

				// Read data returned for the query.
				C.SqlDataReader reader = command.ExecuteReader();
				while (reader.Read())
				{
					Console.WriteLine("Values:  {0}, {1}, {2}, {3}, {4}",
						reader[0], reader[1], reader[2], reader[3], reader[4]);
				}
			}
			Console.WriteLine("View the results here, then press any key to finish...");
			Console.ReadKey(true);
		}
		//----------------------------------------------------------------------------------

		static string GetConnectionStringFromExeConfig(string connectionStringNameInConfig)
		{
			G.ConnectionStringSettings connectionStringSettings =
				G.ConfigurationManager.ConnectionStrings[connectionStringNameInConfig];

			if (connectionStringSettings == null)
			{
				throw new ApplicationException(String.Format
					("Error. Connection string not found for name '{0}'.",
					connectionStringNameInConfig));
			}
				return connectionStringSettings.ConnectionString;
		}

		static string GatherPasswordFromConsole()
		{
			T.StringBuilder passwordBuilder = new T.StringBuilder(32);
			ConsoleKeyInfo key;
			Console.WriteLine("Enter your password: ");
			do
			{
				key = Console.ReadKey(true);
				if (key.Key != ConsoleKey.Backspace)
				{
					passwordBuilder.Append(key.KeyChar);
					Console.Write("*");
				}
				else  // Backspace char was entered.
				{
					// Retreat the cursor, overlay '*' with ' ', retreat again.
					Console.Write("\b \b");
					passwordBuilder.Length = passwordBuilder.Length - 1;
				}
			}
			while (key.Key != ConsoleKey.Enter); // Enter key will end the looping.
			Console.WriteLine(Environment.NewLine);
			return passwordBuilder.ToString();
		}
	}
}
```


### 프로그램 컴파일


1. Visual Studio에서 **빌드** > **솔루션 빌드** 메뉴를 클릭하여 프로그램을 컴파일합니다.


### 샘플 프로그램에서 동작의 요약


1. 구성 파일에서 SQL 연결 문자열을 대부분 읽습니다.

2. 키보드에서 사용자 이름 및 암호를 수집하고 추가하여 연결 문자열을 완성합니다.

3. 연결 문자열 및 ADO.NET 클래스를 사용하여 SQL 데이터베이스에서 **AdventureWorksLT** 데모 데이터베이스에 연결합니다.

4. SQL **선택**을 만들어 **SalesLT** 테이블에서 읽습니다.

5. 콘솔에 반환된 행을 인쇄합니다.


C# 샘플을 간단하게 하려고 합니다. 코드를 추가하여 사용자와 같은 고객의 요청을 반영하기 위해 구성 파일을 읽습니다. 프로덕션 품질 프로그램은 .exe에서 하드 코딩된 리터럴 대신 구성 파일을 사용해야 합니다.


> [AZURE.WARNING] 코드를 간단하게 하기 위해 예외 처리에 코드를 포함하거나 이 교육적 샘플에서 논리를 다시 시도하지 않기로 합니다. 그러나 클라우드 데이터베이스와 상호 작용하는 프로덕션 프로그램은 모두를 포함해야 합니다.
>
> [여기](sql-database-develop-csharp-retry-windows.md)에 재시도 논리가 포함된 코드 샘플의 링크가 있습니다.


## 7단계: 서버 방화벽에서 허용된 IP 주소 범위 추가


클라이언트 컴퓨터의 IP 주소가 SQL 데이터베이스 방화벽에 추가될 때까지 클라이언트 C# 프로그램은 SQL 데이터베이스에 연결할 수 없습니다. 프로그램은 필요한 IP 주소를 보고하는 유용한 오류 메시지에 실패합니다.


[Azure 포털](https://portal.azure.com/) 사용하여 IP 주소를 추가할 수 있습니다.



[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]



자세한 내용은<br/>
[방법: SQL 데이터베이스에 방화벽 설정 구성](sql-database-configure-firewall-settings.md)을 참조하세요.



## 8단계: 프로그램 실행


1. Visual Studio에서 **디버그** > **디버깅 시작** 메뉴로 C# 쿼리를 실행합니다. 콘솔 창이 표시됩니다.

2. 지시에 따라 사용자 이름 및 암호를 입력합니다.
 - 몇가지 연결 도구는 사용자 이름에 추가된 "@{your\_serverName\_here}"가 있어야 하지만 ADO.NET의 경우 이 접미사는 선택 사항입니다. 접미사의 입력을 신경쓰지 마세요.

3. 데이터의 행이 표시됩니다.


## 관련 링크


- [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](sql-database-develop-quick-start-client-code-samples.md)

- Azure VM에서 클라이언트 프로그램을 실행하는 경우 1433 이외의 TCP 포트에 대해 다음에서 알아봅니다.<br/>[4\.5 ADO.NET 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md)



<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

[30-VSNewProject]: ./media/sql-database-connect-query/connqry-vs-new-project-f.png

[40-VSProgramCsOverlay]: ./media/sql-database-connect-query/connqry-vs-program-cs-overlay-g.png

[50-VSCopyToOutputDirectoryProperty]: ./media/sql-database-connect-query/connqry-vs-appconfig-copytoputputdir-h.png

<!----HONumber=AcomDC_0211_2016-->