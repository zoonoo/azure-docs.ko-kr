<properties
	pageTitle="C#을 사용한 SQL 데이터베이스 연결 및 쿼리"
	description="C# 클라이언트에서 ADO.NET을 사용하여 Azure SQL 데이터베이스 클라우드 서비스의 AdventureWorks 데이터베이스에 연결하고 상호 작용하기 위한 코드 샘플"
	services="sql-database"
	documentationCenter=""
	authors="ckarst"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="04/14/2015"
	ms.author="cakarst"/>


# C&#x23;을 사용한 SQL 데이터베이스 연결 및 쿼리


이 항목에서는 ADO.NET을 사용하여 기존 AdventureWorks SQL 데이터베이스에 연결하는 방법을 보여주는 C# 코드 샘플을 소개합니다. 이 샘플은 콘솔 응용 프로그램으로 컴파일되어 데이터베이스를 쿼리하고 결과 집합을 표시합니다.


## 필수 조건


- Azure SQL 데이터베이스의 기존 AdventureWorks 데이터베이스 [없을 경우 빠르게 만들 수 있습니다](sql-database-get-started.md).
- [.NET Framework와 Visual Studio](https://www.visualstudio.com/ko-kr/visual-studio-homepage-vs.aspx)


## 1단계: 콘솔 응용 프로그램


1. Visual Studio를 사용하여 C# 콘솔 응용 프로그램을 만듭니다.


![연결 및 쿼리](./media/sql-database-connect-query/ConnectandQuery_VisualStudio.png)


## 2단계: SQL 코드 샘플


1. 아래 코드 샘플을 복사하여 콘솔 응용 프로그램에 붙여넣습니다.


> [AZURE.WARNING]코드 샘플은 이해하기 쉽도록 가능한 한 짧게 작성되었습니다. 샘플은 프로덕션 환경에서 사용하기 위한 용도가 아닙니다.


이 코드는 프로덕션용이 아닙니다. 프로덕션 환경에서 사용할 준비가 된 코드를 구현하려면 다음과 같은 업계 모범 사례를 고려해 보십시오.


- 예외 처리.
- 일시적인 오류에 대한 논리를 다시 시도.
- 구성 파일 내의 안전한 암호 저장소.



### C# 샘플 소스 코드


이 소스 코드를 **Program.cs** 파일에 붙여넣습니다.


	using System;  // C#
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Data.SqlClient;

	namespace ConnectandQuery_Example
	{
		class Program
		{
			static void Main()
			{
				string SQLConnectionString = "[Your_Connection_String]";
				// Create a SqlConnection from the provided connection string.
				using (SqlConnection connection = new SqlConnection(SQLConnectionString))
				{
					// Begin to formulate the command.
					SqlCommand command = new SqlCommand();
					command.Connection = connection;

					// Specify the query to be executed.
					command.CommandType = System.Data.CommandType.Text;
						command.CommandText =
						@"SELECT TOP 10
						CustomerID, NameStyle, Title, FirstName, LastName
						FROM SalesLT.Customer";

					// Open connection to database.
					connection.Open();

					// Read data from the query.
					SqlDataReader reader = command.ExecuteReader();
					while (reader.Read())
					{
						// Formatting will depend on the contents of the query.
						Console.WriteLine("Value: {0}, {1}, {2}, {3}, {4}",
							reader[0], reader[1], reader[2], reader[3], reader[4]);
					}
				}
				Console.WriteLine("Press any key to continue...");
				Console.ReadKey();
			}
		}
	}


## 3단계: 데이터베이스에 대한 연결 문자열 찾기


1. [Azure Preview 포털](http://portal.azure.com/)을 엽니다.
2. **찾아보기** > **SQL 데이터베이스** > **"Adventure Works" 데이터베이스** > **속성** > **데이터베이스 연결 문자열 표시**를 클릭합니다.


![포털](./media/sql-database-connect-query/ConnectandQuery_portal.png)


데이터베이스 연결 문자열 블레이드에서 ADO.NET, ODBC, PHP 및 JDBC에 대한 적절한 연결 문자열을 확인할 수 있습니다.


## 4단계: 실제 연결 정보로 대체


- 붙여넣은 소스 코드에서 *[Your_Connection_String]* 자리 표시자를 연결 문자열로 바꾸고 *your_password_here*도 실제 암호로 바꿉니다.


## 5단계: 응용 프로그램 실행


1. 응용 프로그램을 빌드하고 실행하려면 **디버그** > **디버깅 시작**을 클릭합니다.
2. 프로그램에서 콘솔 창에 쿼리 결과를 표시합니다.
 

<!---HONumber=58_postMigration-->