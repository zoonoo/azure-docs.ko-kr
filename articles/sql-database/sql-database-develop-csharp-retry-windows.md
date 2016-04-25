<properties
	pageTitle="SQL 데이터베이스용 C# 재시도 논리 | Microsoft Azure"
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
	ms.date="04/08/2016"
	ms.author="annemill"/>


# 코드 샘플: SQL 데이터베이스에 연결에 대한 C#에서 논리 다시 시도



[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]



이 항목에서는 사용자 지정 재시도 논리를 보여 주는 C# 코드 샘플을 제공합니다. 재시도 논리는 프로그램이 잠시 기다렸다가 재시도하는 경우 없어지는 경향이 있는 임시 오류 또는 *일시적인 오류*를 정상적으로 처리하도록 디자인되었습니다.


로컬 Microsoft SQL Server에 연결하는 데 사용되는 ADO.NET 클래스는 Azure SQL 데이터베이스에 연결될 수도 있습니다. 그러나 ADO.NET 클래스 단독으로 프로덕션 사용에 필요한 모든 견고성과 안전성을 제공할 수 없습니다. 클라이언트 프로그램은 자체적으로 지속되고 정상적으로 복구해야 하는 일시적인 오류가 발생할 수 있습니다.


일시적인 오류의 몇 가지 예는 다음과 같습니다.


- 인터넷을 통한 연결은 간략한 네트워크 작동 중단이 발생할 수 있으며 이후 연결을 다시 만들 수 있습니다.
- 클라우드 컴퓨팅은 연결 또는 쿼리하려는 시도를 간단하게 차단할 수 있는 부하 분산을 포함합니다.


## A. 일시적인 오류 식별


프로그램은 일시적인 오류와 지속적인 오류를 구별해야 합니다. 프로그램에 대상 데이터베이스 이름 맞춤법 오류가 있는 경우 "이러한 데이터베이스 발견하지 못함" 오류가 지속되며 프로그램을 다시 실행할 때마다 다시 발생합니다.

일시적인 오류로 분류되는 오류 번호 목록은 다음에서 사용 가능합니다.

- [SQL 데이터베이스 클라이언트 프로그램에 대한 오류 메시지](sql-database-develop-error-messages.md#bkmk_connection_errors)
  - *일시적인 오류, 연결 끊김 오류*에 대한 맨 위 섹션을 참조하세요.


이 항목의 뒷부분에 나오는 C# 코드 샘플은 **TransientErrorNumbers**라는 필드에 일시적인 오류 번호를 나열합니다.



## B. C# 코드 샘플


현재 항목의 C# 코드 샘플은 일시적인 오류를 처리하기 위한 사용자 지정 감지 및 다시 시도 논리를 포함합니다. 샘플은 .NET Framework 4.5.1 이상이 설치되어 있다고 가정합니다.


코드 샘플은 Azure SQL 데이터베이스와 상호작용하는 데 사용하는 기술과 상관없이 적용되는 몇 가지 기본적인 지침 또는 권장 사항을 따릅니다. 다음에서 일반적인 권장 사항을 확인할 수 있습니다.


- [SQL 데이터베이스 연결: 링크, 모범 사례, 설계 지침](sql-database-connect-central-recommendations.md)


C# 코드 샘플은 Program.cs라는 하나의 파일로 구성됩니다. 해당 코드는 다음 섹션에 나와 있습니다.


### B.1 코드 샘플 캡처 및 컴파일


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
using S = System.Data.SqlClient;
using T = System.Threading;
   
namespace RetryAdo2
{
   public class Program
   {
      static public int Main(string[] args)
      {
         bool succeeded = false;
         int totalNumberOfTimesToTry = 4;
         int retryIntervalSeconds = 10;
   
         for (int tries = 1;
            tries <= totalNumberOfTimesToTry;
            tries++)
         {
            try
            {
               if (tries > 1)
               {
                  Console.WriteLine
                     ("Transient error encountered. Will begin attempt number {0} of {1} max...",
                     tries, totalNumberOfTimesToTry
                     );
                  T.Thread.Sleep(1000 * retryIntervalSeconds);
                  retryIntervalSeconds = Convert.ToInt32
                     (retryIntervalSeconds * 1.5);
               }
               AccessDatabase();
               succeeded = true;
               break;
            }
   
            catch (S.SqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred.", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (TestSqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred. (TESTING.)", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (Exception Exc)
            {
               Console.WriteLine(Exc);
               succeeded = false;
               break;
            }
         }
   
         if (succeeded == true)
         {
            return 0;
         }
         else
         {
            Console.WriteLine("ERROR: Unable to access the database!");
            return 1;
         }
      }
   
      /// <summary>
      /// Connects to the database, reads,
      /// prints results to the console.
      /// </summary>
      static public void AccessDatabase()
      {
         //throw new TestSqlException(4060); //(7654321);  // Uncomment for testing.
   
         using (var sqlConnection = new S.SqlConnection
                  (GetSqlConnectionString()))
         {
            using (var dbCommand = sqlConnection.CreateCommand())
            {
               dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";
   
               sqlConnection.Open();
               var dataReader = dbCommand.ExecuteReader();
   
               while (dataReader.Read())
               {
                  Console.WriteLine("{0}\t{1}",
                     dataReader.GetString(0),
                     dataReader.GetString(1));
               }
            }
         }
      }
   
      /// <summary>
      /// You must edit the four 'my' string values.
      /// </summary>
      /// <returns>An ADO.NET connection string.</returns>
      static private string GetSqlConnectionString()
      {
         // Prepare the connection string to Azure SQL Database.
         var sqlConnectionSB = new S.SqlConnectionStringBuilder();
   
         // Change these values to your values.
         sqlConnectionSB.DataSource = "tcp:myazuresqldbserver.database.windows.net,1433"; //["Server"]
         sqlConnectionSB.InitialCatalog = "MyDatabase"; //["Database"]
   
         sqlConnectionSB.UserID = "MyLogin";  // "@yourservername"  as suffix sometimes.
         sqlConnectionSB.Password = "MyPassword";
         sqlConnectionSB.IntegratedSecurity = false;
   
         // Adjust these values if you like. (ADO.NET 4.5.1 or later.)
         sqlConnectionSB.ConnectRetryCount = 3;
         sqlConnectionSB.ConnectRetryInterval = 10;  // Seconds.
   
         // Leave these values as they are.
         sqlConnectionSB.IntegratedSecurity = false;
         sqlConnectionSB.Encrypt = true;
         sqlConnectionSB.ConnectTimeout = 30;
   
         return sqlConnectionSB.ToString();
      }
   
      static public G.List<int> TransientErrorNumbers =
         new G.List<int> { 4060, 40197, 40501, 40613,
         49918, 49919, 49920, 11001 };
   }
   
   /// <summary>
   /// For testing retry logic, you can have method
   /// AccessDatabase start by throwing a new
   /// TestSqlException with a Number that does
   /// or does not match a transient error number
   /// present in TransientErrorNumbers.
   /// </summary>
   internal class TestSqlException : ApplicationException
   {
      internal TestSqlException(int testErrorNumber)
      { this.Number = testErrorNumber; }
   
      internal int Number
      { get; set; }
   }
}
```



## C. 프로그램 실행


**RetryAdo2.exe** 실행 파일은 매개 변수를 입력하지 않습니다. .exe를 실행하려면

1. RetryAdo2.exe 이진 파일을 컴파일한 곳으로 콘솔 창을 엽니다.
2. 입력 매개 변수 없이 RetryAdo2.exe를 실행합니다.



```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```



## D. 재시도 논리를 테스트하는 방법

일시적 오류를 시뮬레이션하여 재시도 논리를 테스트할 수 있는 여러 가지 방법이 있습니다.


### D.1 테스트 예외 throw

코드 샘플에는 다음이 포함됩니다.

- 속성 이름이 **Number**로 지정된 **TestSqlException**이라는 작은 두 번째 클래스
- 주석 처리를 제거할 수 있는 `//throw new TestSqlException(4060);`

throw의 주석 처리를 제거하고 다시 컴파일한 경우 다음에 **RetryAdo2.exe**를 실행하면 다음과 유사한 출력이 나타납니다.



```
[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>> RetryAdo2.exe
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 2 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 3 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 4 of 4 max...
4060: transient occurred. (TESTING.)
ERROR: Unable to access the database!

[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>>
```



#### 영구 오류로 다시 테스트


코드가 영구 오류를 올바르게 처리하는지 증명하려면 4060과 같은 실제 일시적 오류 번호를 사용하지 말고 이전 테스트를 다시 실행합니다. 대신 의미 없는 번호 7654321을 사용합니다. 프로그램에서는 이를 영구 오류로 간주하여 다시 시도를 무시해야 합니다.



### D.2 네트워크에서 연결 끊기

1. 네트워크에서 클라이언트 컴퓨터의 연결을 끊습니다.
  - 데스크톱의 경우 네트워크 케이블을 분리합니다.
  - 랩톱의 경우 기능 키 조합을 눌러 네트워크 어댑터를 해제합니다.
2. RetryAdo2.exe를 시작하고 콘솔에 첫 번째 일시적 오류(예: 11001)가 표시될 때까지 기다립니다.
3. RetryAdo2.exe가 계속 실행되는 동안 네트워크에 다시 연결합니다.
4. 콘솔 보고서에 후속 다시 시도에 대한 성공이 표시되는지 확인합니다.


### D.3 일시적인 서버 이름 맞춤법 오류

1. 일시적으로 40615를 **TransientErrorNumbers**에 다른 오류 번호로 추가하고 다시 컴파일합니다.
2. `new S.SqlConnectionStringBuilder()` 줄에 중단점을 설정합니다.
3. *편집하며 계속하기* 기능을 사용하여 아래 두 줄에 의도적으로 서버 이름을 잘못 입력합니다.
  - 프로그램을 실행하고 중단점으로 돌아가도록 합니다.
  - 40615 오류가 발생합니다.
4. 맞춤법 오류를 수정합니다.
5. 프로그램을 실행하고 성공적으로 완료하도록 합니다.
6. 40615를 제거하고 다시 컴파일합니다.


## E. 관련 링크

- [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](sql-database-develop-quick-start-client-code-samples.md)
- [SQL 데이터베이스 시도: C#을 사용하여 .NET용 SQL 데이터베이스 라이브러리로 SQL 데이터베이스 만들기](sql-database-get-started-csharp.md)

<!---HONumber=AcomDC_0413_2016-->