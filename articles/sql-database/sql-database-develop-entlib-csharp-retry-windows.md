<properties
	pageTitle="SQL 데이터베이스에 연결하는 EntLib 재시도 | Microsoft Azure"
	description="Enterprise Library는 일시적인 오류에 대한 재시도 논리의 통합을 비롯해 클라우드 서비스의 클라이언트 프로그램에 대한 여러 작업을 쉽게 하기 위해 설계되었습니다."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor="" />


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="annemill"/>


# 코드 샘플: C&#x23;의 Enterprise Library 6에서 SQL 데이터베이스 연결을 위한 논리를 재시도합니다.

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


이 항목에서는 Enterprise Library(EntLib)를 보여주는 전체 코드 샘플을 제공합니다. EntLib은 Azure SQL 데이터베이스와 같은 클라우드 서비스와 상호 작용하는 클라이언트 프로그램의 많은 작업을 줄여 줍니다. 여기 샘플에서는 일시적인 오류에 대한 재시도 논리를 포함하는 중요한 작업에 중점을 둡니다.


EntLib 클래스는 다음 두 범주의 런타임 오류를 구분하도록 설계되었습니다.

- 자체 수정되지 않는 오류(예: 철자가 잘못된 서버 이름)
- 일시적인 오류(예: Azure 시스템이 부하를 분산하는 동안 서버가 몇 초 동안 새로운 연결 수락을 일시 중단)


Enterprise Library 6(EntLib60)은 최신 버전이며 2013년 4월에 출시되었습니다.

- Microsoft에서 소스 코드를 공용으로 출시했습니다.
- Microsoft는 더 이상 소스 코드를 유지 관리할 계획이 없습니다.


## 필수 조건


#### .NET Framework 4.0 이상


Microsoft .NET Framework 4.0 이상이 설치되어 있어야 합니다. 본 문서 게시 시점에는 버전 4.6을 사용할 수 있으며 최신 버전을 사용하는 것이 좋습니다.


#### Visual Studio Community 버전(무료)


이 샘플의 소스 코드를 컴파일할 수 있는 방법이 필요합니다. 한 가지 방법으로 [무료 Microsoft Visual Studio *Community* 버전](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)을 설치하는 것이 있습니다.


MSDN에 메일 주소를 등록해야 할 수 있습니다. 단계는 다음과 같습니다.


1. [MSDN으로 이동](http://msdn.microsoft.com/)합니다.
2. 위쪽에 있는 **MSDN 구독**을 클릭합니다.
3. **지금 등록**을 클릭합니다.
4. 양식에 정보를 입력합니다.
5. 아래쪽의 **계정 만들기**를 클릭합니다.


#### Enterprise Library 6(EntLib60)


EntLib60을 설치하는 방법은 다음과 같습니다.


- Visual Studio의 *NuGet* 패키지 관리자 기능 사용:
 - NuGet에서 **enterpriselibrary**를 검색합니다.


- [EntLib60에 대한 홈 설명서 항목](http://msdn.microsoft.com/library/dn169621.aspx)에서 **다운로드**라는 행을 찾은 다음 [Microsoft Enterprise Library 6](http://go.microsoft.com/fwlink/?linkid=290898)을 클릭하여 이진 .DLL 어셈블리 파일을 다운로드합니다.


EntLib60에는 이름이 동일한 접두사 **Microsoft.Practices.EnterpriseLibrary.&#x2a;.dll**로 시작하는 여러 .DLL 어셈블리 파일이 있지만 이 코드 샘플에서는 다음 두 어셈블리에만 관심을 둡니다.

- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling**.dll
- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling.Data**.dll


## EntLib 클래스가 연동되는 방식


EntLib 클래스는 다른 EntLib 클래스를 생성하는 데 사용됩니다. 이 코드 샘플에서 생성 및 사용 시퀀스는 다음과 같습니다.


1. **ExponentialBackoff** 개체를 생성합니다.
2. **SqlDatabaseTransientErrorDetectionStrategy** 개체를 생성합니다.
3. **RetryPolicy** 개체를 생성합니다. 입력 매개 변수는 다음과 같습니다.
 - **ExponentialBackoff** 개체
 - **SqlDatabaseTransientErrorDetectionStrategy** 개체
4. **ReliableSqlConnection** 개체를 생성합니다. 입력 매개 변수는 다음과 같습니다.
 - **String** 개체 - 서버 이름 및 기타 연결 정보 포함
 - **RetryPolicy** 개체.
5. **RetryPolicy .ExecuteAction** 메서드를 통해 호출하여 연결합니다.
6. **ReliableSqlConnection .CreateCommand** 메서드를 호출합니다.
 - ADO.NET의 일부인 **System.SqlClient.Data.DbCommand** 개체를 반환합니다.
7. **RetryPolicy. ExecuteAction** 메서드를 통해 호출하여 쿼리합니다.


## 코드 샘플 컴파일 및 실행


Program.cs 소스 코드 샘플은 이 항목의 뒷부분에 제공됩니다. 다음 단계에 따라 샘플을 컴파일하고 실행할 수 있습니다.


1. Visual Studio에서 C# 콘솔 응용 프로그램 템플릿에서 새 프로젝트를 만듭니다.

2. 솔루션 탐색기 창에서 거의 비어 있는 Program.cs 파일을 편집합니다. 즉, 시작 콘텐츠를 이 항목 뒷부분에 제공된 Program.cs 코드로 바꿉니다.

3. Visual Studio의 빌드 > 솔루션 빌드 메뉴를 사용하여 프로젝트를 컴파일합니다.

4. cmd.exe 명령 창에서 다음에 표시된 것처럼 프로그램을 실행합니다. 실행되는 실제 출력도 표시됩니다.


```
[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>> EntLib60Retry.exe

database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535

[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>>
```


&nbsp;


## Program.cs 소스 코드


이 EntLib 샘플의 모든 소스 코드는 다음 Program.cs 파일에 포함되어 있습니다.


```
using     System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;
using Y = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;

namespace EntLib60Retry
{
   class Program
   {
      static void Main(string[] args)
      {
         Program program = new Program();

         if (program.Run(args) == false)
         {
            Console.WriteLine("Something was unable to complete.  :-( ");
         }
      }

      bool Run(string[] _args)
      {
         int retryIntervalSeconds = 10;
         bool returnBool = false;

         this.InitializeEntLibObjects();

         for (int tries = 1; tries <= 3; tries++)
         {
            if (tries > 1)
            {
               H.Thread.Sleep(1000 * retryIntervalSeconds);
               retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
            }

            try
            {
               this.reliableSqlConnection = new Y.ReliableSqlConnection(
                  this.sqlConnectionSB.ToString(),
                  this.retryPolicy, null
                  );
               this.retryPolicy.ExecuteAction(this.OpenTheConnection_action);  // Open the connection.

               this.dbCommand = this.reliableSqlConnection.CreateCommand();
               this.dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

               // We retry connection .Open after transient faults, but
               // we do not retry commands that use the connection.
               this.IssueTheQuery();  // Run the query, loop through results.

               returnBool = true;
               break;
            }

            catch (C.SqlException sqlExc)
            {
               if (false == this.sqlDatabaseTransientErrorDetectionStrategy.IsTransient(sqlExc))
               {
                  throw sqlExc;
               }
            }
         }
         return returnBool;
      }

      void OpenTheConnection()  // Called by .ExecuteAction.
      {
         this.reliableSqlConnection.Open();
      }

      void IssueTheQuery()      // Called by .ExecuteAction.
      {
         X.StringBuilder sBuilder = new X.StringBuilder(256);

         this.dataReader = this.dbCommand.ExecuteReader();

         while (this.dataReader.Read())
         {
            sBuilder.Length = 0;
            sBuilder.Append(this.dataReader.GetString(0));
            sBuilder.Append("\t");
            sBuilder.Append(this.dataReader.GetString(1));

            Console.WriteLine(sBuilder.ToString());
         }
      }

      void InitializeEntLibObjects()
      {
         this.InitializeSqlConnectionStringBuilder();

         this.exponentialBackoff = new Y.ExponentialBackoff(
            "exponentialBackoff",
             3,                    // Maximum number of times to retry, then let exception bubble up.
             TimeSpan.FromMilliseconds(10000), // Minimum interval between retries.
             TimeSpan.FromMilliseconds(30000), // Maximum interval between retries.
             TimeSpan.FromMilliseconds( 2000)  // For random differences in interval between retries.
            );
         this.sqlDatabaseTransientErrorDetectionStrategy =
            new Y.SqlDatabaseTransientErrorDetectionStrategy();
         this.retryPolicy = new Y.RetryPolicy(
               this.sqlDatabaseTransientErrorDetectionStrategy,
               this.exponentialBackoff
               );
         this.OpenTheConnection_action = delegate() { this.OpenTheConnection(); };
      }

      void InitializeSqlConnectionStringBuilder()
      {
         // Prepare the connection string to Azure SQL Database.
         this.sqlConnectionSB = new C.SqlConnectionStringBuilder();

         // Change these values to your values.
         this.sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
         this.sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
         this.sqlConnectionSB["Password"] = "MyPassword";
         this.sqlConnectionSB["Database"] = "MyDatabase";

         // Leave these values as they are.
         this.sqlConnectionSB["Trusted_Connection"] = false;
         this.sqlConnectionSB["Integrated Security"] = false;
         this.sqlConnectionSB["Encrypt"] = true;
         this.sqlConnectionSB["Connection Timeout"] = 30;
      }

      Program()   // Constructor.
      {
         int[] arrayOfTransientErrorNumbers =
                { 4060, 10928, 10929, 40197, 40501, 40613, 49918, 49919, 49920
                    //,11001   // 11001 for testing, pretend network error is transient.
                    //,18456   // 18456 for testing, purposely misspell database name.
                };
         listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
      }

      // Fields.
      private G.List<int> listTransientErrorNumbers;

      private Y.ReliableSqlConnection reliableSqlConnection;
      private Y.ExponentialBackoff exponentialBackoff;
      private Y.SqlDatabaseTransientErrorDetectionStrategy
                   sqlDatabaseTransientErrorDetectionStrategy;
      private Y.RetryPolicy retryPolicy;

      private Action OpenTheConnection_action;

      private C.SqlConnectionStringBuilder sqlConnectionSB;
      private D.IDbCommand dbCommand;
      private D.IDataReader dataReader;
   }
}
```


&nbsp;


## 관련 링크


- [Enterprise Library 6 - 2013년 4월호](http://msdn.microsoft.com/library/dn169621.aspx)는 추가 정보에 대한 다양한 링크를 제공합니다.
 - 이 항목에는 소스 코드를 보고 싶은 경우 [EntLib60 소스 코드를 다운로드](http://go.microsoft.com/fwlink/p/?LinkID=290898)하기 위한 단추가 위쪽에 있습니다.


- PDF 형식의 Microsoft 무료 전자책: [Microsoft Enterprise Library에 대한 개발자 가이드, 2판](http://www.microsoft.com/download/details.aspx?id=41145)


- [Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling 네임스페이스](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)


- [Enterprise Library 6 클래스 라이브러리 참조](http://msdn.microsoft.com/library/dn170426.aspx)


- [코드 샘플: ADO.NET을 사용하여 C#에서 SQL 데이터베이스에 연결하는 논리 재시도](sql-database-develop-csharp-retry-windows.md)


- [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=AcomDC_0316_2016-->