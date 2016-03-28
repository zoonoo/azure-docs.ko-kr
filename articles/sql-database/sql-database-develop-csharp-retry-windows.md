<properties
	pageTitle="SQL 데이터베이스에 연결하는 C# 재시도 논리 | Microsoft Azure"
	description="C# 샘플에는 Azure SQL 데이터베이스를 조작하기 위한 안정적인 재시도 논리가 포함되어 있습니다."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="annemill"/>


# 코드 샘플: SQL 데이터베이스에 연결에 대한 C#에서 논리 다시 시도



[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]



이 항목에서는 사용자 지정 재시도 논리를 보여 주는 C# 코드 샘플을 제공합니다. 재시도 논리는 프로그램이 잠시 기다렸다가 재시도하는 경우 없어지는 경향이 있는 임시 오류 또는 *일시적인 오류*를 정상적으로 처리하도록 디자인되었습니다.


로컬 Microsoft SQL 서버에 연결하는 데 사용하는 ADO.NET 클래스는 Azure SQL 데이터베이스에 연결할 수도 있습니다. 그러나 ADO.NET 클래스 단독으로 프로덕션 사용에 필요한 모든 견고성과 안전성을 제공할 수 없습니다. 클라이언트 프로그램은 자체적으로 정상적으로 복구해야 하는 일시적인 오류가 발생할 수 있습니다.


일시적인 오류의 몇 가지 예는 다음과 같습니다.


- 인터넷을 통한 연결은 간략한 네트워크 작동 중단이 발생할 수 있으며 이후 연결을 다시 만들 수 있습니다.

- 클라우드 컴퓨팅은 연결 또는 쿼리하려는 시도를 간단하게 차단할 수 있는 부하 분산을 포함합니다.


## 일시적인 오류 식별


프로그램은 일시적인 오류와 지속적인 오류를 구별해야 합니다. 프로그램에 대상 데이터베이스 이름 맞춤법 오류가 있는 경우 "이러한 데이터베이스 발견하지 못함" 오류가 지속되며 프로그램을 다시 실행할 때마다 다시 발생합니다.


일시적인 오류로 분류되는 오류 번호 목록은 다음에서 사용 가능합니다.


- [SQL 데이터베이스 클라이언트 프로그램에 대한 오류 메시지](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - *일시적인 오류, 연결 끊김 오류*에 대한 맨 위 섹션을 참조하세요.


## C# 코드 샘플


현재 항목의 C# 코드 샘플은 일시적인 오류를 처리하기 위한 사용자 지정 감지 및 다시 시도 논리를 포함합니다. 샘플은 .NET Framework 4.5.1 이상이 설치되어 있다고 가정합니다.


코드 샘플은 Azure SQL 데이터베이스와 상호작용하는 데 사용하는 기술과 상관없이 적용되는 몇 가지 기본적인 지침 또는 권장 사항을 따릅니다. 다음에서 일반적인 권장 사항을 확인할 수 있습니다.


- [SQL 데이터베이스 연결: 링크, 모범 사례, 설계 지침](sql-database-connect-central-recommendations.md)


C# 코드 샘플은 Program.cs라는 하나의 파일로 구성됩니다. 이 코드를 다음 섹션에서 붙여넣습니다.


### 코드 샘플 캡처 및 컴파일


다음 단계로 샘플을 컴파일할 수 있습니다.


1. [무료로 제공되는 Visual Studio Community 버전](https://www.visualstudio.com/products/visual-studio-community-vs)에서 C# 콘솔 응용 프로그램 템플릿에서 새 프로젝트를 만듭니다.
 - 파일 > 새로 만들기 > 프로젝트 > 설치됨 > 템플릿 > Visual C# > Windows > 고전 바탕 화면 > 콘솔 응용 프로그램
 - 프로젝트 이름을 **RetryAdo2**로 지정합니다.

2. 솔루션 탐색기 창을 엽니다.
 - 프로젝트의 이름이 표시됩니다.
 - Program.cs 파일의 이름이 표시됩니다.

3. Program.cs 파일을 엽니다.

4. Program.cs 파일의 내용 전체를 다음 코드 블록의 코드로 바꿉니다.

5. 빌드 > 솔루션 빌드 메뉴를 클릭합니다.


#### 붙여넣을 C# 소스 코드


이 코드를 **Program.cs** 파일에 붙여넣습니다.


그런 다음 서버 이름, 암호 등에 대한 문자열을 편집해야 합니다. **GetSqlConnectionStringBuilder**라는 메서드에서 이 문자열을 찾을 수 있습니다.


```
using System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;

namespace RetryAdo2
{
	class Program
	{
		static void Main(string[] args)
		{
			Program program = new Program();
			bool returnBool;

			returnBool = program.Run(args);
			if (returnBool == false)
			{
				Console.WriteLine("Something failed.  :-( ");
			}
			return;
		}

		bool Run(string[] _args)
		{
			C.SqlConnectionStringBuilder sqlConnectionSB;
			C.SqlConnection sqlConnection;
			D.IDbCommand dbCommand;
			D.IDataReader dataReader;
			X.StringBuilder sBuilder = new X.StringBuilder(256);
			int retryIntervalSeconds = 10;
			bool returnBool = false;

			for (int tries = 1; tries <= 5; tries++)
			{
				try
				{
					if (tries > 1)
					{
						H.Thread.Sleep(1000 * retryIntervalSeconds);
						retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
					}
					this.GetSqlConnectionStringBuilder(out sqlConnectionSB);

					sqlConnection = new C.SqlConnection(sqlConnectionSB.ToString());

					dbCommand = sqlConnection.CreateCommand();
					dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

					sqlConnection.Open();
					dataReader = dbCommand.ExecuteReader();

					while (dataReader.Read())
					{
						sBuilder.Length = 0;
						sBuilder.Append(dataReader.GetString(0));
						sBuilder.Append("\t");
						sBuilder.Append(dataReader.GetString(1));

						Console.WriteLine(sBuilder.ToString());
					}
					returnBool = true;
					break;
				}

				catch (C.SqlException sqlExc)
				{
					if (this.m_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
					{ continue; }
					else
					{ throw sqlExc; }
				}
			}
			return returnBool;
		}

		void GetSqlConnectionStringBuilder(out C.SqlConnectionStringBuilder _sqlConnectionSB)
		{
			// Prepare the connection string to Azure SQL Database.
			_sqlConnectionSB = new C.SqlConnectionStringBuilder();

			// Change these values to your values.
			_sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
			_sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
			_sqlConnectionSB["Password"] = "MyPassword";
			_sqlConnectionSB["Database"] = "MyDatabase";

			// Adjust these values if you like. (.NET 4.5.1 or later.)
			_sqlConnectionSB["ConnectRetryCount"] = 3;
			_sqlConnectionSB["ConnectRetryInterval"] = 10;  // Seconds.

			// Leave these values as they are.
			_sqlConnectionSB["Trusted_Connection"] = false;
			_sqlConnectionSB["Integrated Security"] = false;
			_sqlConnectionSB["Encrypt"] = true;
			_sqlConnectionSB["Connection Timeout"] = 30;
		}

		Program()   // Constructor.
		{
			int[] arrayOfTransientErrorNumbers =
				{ 4060, 40197, 40501, 40613, 49918, 49919, 49920
					//,11001   // 11001 for testing, pretend network error is transient.
				};
			m_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
		}

		private G.List<int> m_listTransientErrorNumbers;
	}
}
```


### 프로그램 실행


**RetryAdo2.exe** 실행 파일은 매개 변수를 입력하지 않습니다. Visual Studio에서.exe를 실행하려면 다음을 실행하십시오.


1. **Main** 메서드의 **return;** 문에 중단점을 설정합니다.

2. 녹색 시작 화살표 단추를 클릭합니다. 콘솔 창이 표시됩니다.

3. 디버거가 **Main**의 끝에서 중지되면 콘솔 창으로 전환합니다.

4. 다음과 같은 3개의 행이 표시됩니다.


```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```


## 재시도 논리 테스트


재시도 논리를 편리하게 테스트하는 방법은 다음과 같습니다.


1. 일시적인 오류로 간주해야 하는 **SqlConnection.Number** 값 컬렉션에 **11001**을 임시로 추가합니다.

2. 프로그램을 다시 컴파일합니다.

3. 네트워크에서 클라이언트 컴퓨터의 연결을 끊습니다.

4. 루프에 중단점을 설정하여 디버거에서 프로그램을 실행합니다.
 - 첫 번째 루프가 실패하며 오류 11001이 나타납니다.

5. 두 번째 루프 도중에 프로그램이 중단점에서 멈춘 경우 컴퓨터를 네트워크에 다시 연결합니다.

6. 프로그램 실행을 다시 시작합니다. 두 번째 루프 중에 계속됩니다.


### 다른 테스트 옵션


명령줄 매개 변수 값 "test"를 인식하도록 프로그램에 논리를 추가하는 것도 한 가지 방법입니다. 이 매개 변수에 대한 응답으로 프로그램은 다음을 수행합니다.


1. 임시로 정크 문자를 추가하여 SQL 데이터베이스 서버 이름의 맞춤법 오류를 만듭니다.

2. 일시적인 오류 목록에 임시로 **40615**를 추가합니다.

3. 두 번째 루프, 다시 말해 첫 번째 다시 시도 루프의 시작 부분에서 프로그램은 다음을 수행합니다.
 - 서버 맞춤법 오류를 실행 취소합니다.
 - 일시적인 오류 목록에서 40615를 제거합니다.


"test" 매개 변수를 사용하여 프로그램을 실행하여 처음에 실패하지만 두 번째 루프에서는 성공하는지 확인합니다.


## 관련 링크


- [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](sql-database-develop-quick-start-client-code-samples.md)

- [SQL 데이터베이스 시도: C#을 사용하여 .NET용 SQL 데이터베이스 라이브러리로 SQL 데이터베이스 만들기](sql-database-get-started-csharp.md)

<!---HONumber=AcomDC_0316_2016-->