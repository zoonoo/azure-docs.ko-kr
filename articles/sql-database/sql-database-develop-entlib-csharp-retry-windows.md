<properties 
	pageTitle="코드 샘플: C#의 Enterprise Library에서 SQL 데이터베이스 연결을 위한 논리를 재시도합니다 | Microsoft Azure"
	description="Enterprise Library는 클라우드 서비스에 액세스하는 클라이언트 프로그램에 논리를 재시도하는 것을 포함한 작업을 용이하게 하도록 설계되었습니다."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="genemi"/>


# 코드 샘플: C&#x23;의 Enterprise Library에서 SQL 데이터베이스 연결을 위한 논리를 재시도합니다


이 항목에서는 Enterprise Library (EntLib)를 보여주는 전체 코드 샘플을 제공합니다. EntLib은 Microsoft Azure의 SQL 데이터베이스 클라우드 서비스와 상호작용 하는 클라이언트에 재시도 논리를 포함하는 프로그래머의 작업을 줄일 수 있습니다.


Enterprise Library 6 (EntLib60)은 최신 버전입니다.


EntLib에 대한 자세한 내용은 다음 링크를 사용하는 것을 권장합니다.


- [Enterprise Library 6 - 2013년 4월호](http://msdn.microsoft.com/library/dn169621.aspx)<br/>는 추가 정보에 대한 다양한 링크를 제공합니다.

- Microsoft 무료 전자책 PDF:<br/>[Microsoft Enterprise Library에 대한 개발자 가이드, 제 2 판](http://www.microsoft.com/download/details.aspx?id=41145).


## 필수 조건


Visual Studio 및 Microsoft.NET Framework를 설치할 때 EntLib은 포함되지 않습니다. 별도의 다운로드 작업을 수행해야 합니다.


Visual Studio를 통한 **NuGet** 시스템이 다운로드를 쉽게 해줍니다. Visual Studio 솔루션 .sln 파일이 저장되는 디렉터리와 동일한 디렉터리 아래에 packages\\로 다운로드됩니다.


EntLib 어셈블리 .dll 파일은 하위 디렉터리에 설치됩니다. 두 어셈블리 파일 이름은 다음과 같이 지정됩니다.


- `Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.dll`
- `Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.Data.dll`


## 설명된 C&#x23; 코드 파일


C# 코드 샘플은 다음 섹션에 해당 콘텐츠를 붙여 넣는 세 개의 .cs 파일로 이루어져 있습니다. 해당 파일 이름은 다음과 같습니다.


- `Program.cs`
- `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`
- `ForcePersistent_SqlCommandTransientErrorDetectionStrategy.cs`


### `try/catch` 없이 더 짧은 `Program.cs`


제공된 다른 .cs 파일은 훨씬 더 짧은 버전의 `Program.cs`입니다. 모든 `try/catch` 코드가 제거되었습니다. 이제 EntLib 호출을 더 쉽게 볼 수 있습니다.



### 테스트 .cs 파일


일시적인 결함 오류를 야기하는 명백한 이유가 없기 때문에 재시도 논리를 테스트하는 것은 까다롭습니다. 테스트 솔루션 한 가지는 임시 코드를 사용하는 것입니다.


1. 허위의 일시적인 오류를 발생시킵니다.
2. 일시적인 오류의 원인을 해결합니다.
3. 성공이 예상되는 연결 또는 쿼리를 재시도합니다.


테스트를 하도록 다른 .cs 파일이 제공되어 임시 대용할 수 있습니다.


- `Test2_TransientErrorDetectionStrategy`
 - `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`에 대한 임시 교체입니다.


이 테스트에 대한 변경 내용을 만드는 코드 위치가 문자열 `TEST.PASSWORD_FIX`을 포함하는 주석과 함께 태그됩니다.


## 코드 샘플 컴파일 및 실행


다음 단계로 샘플을 컴파일할 수 있습니다.


1. Visual Studio에서 C# 콘솔 응용 프로그램 템플릿에서 새 프로젝트를 만듭니다.

2. 프로젝트를 마우스 오른쪽 버튼으로 클릭한 후 이 항목에서 제공된 소스 코드에 대한 .cs 파일을 추가합니다.

3. `cmd.exe` 명령 창에서 다음에 표시된 것처럼 프로그램을 실행합니다. 실행되는 실제 출력도 표시됩니다.


		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>> ConsoleApplication1.exe
		
		database_firewall_rules_table   245575913
		filestream_tombstone_2073058421 2073058421
		filetable_updates_2105058535    2105058535
		
		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>>


.cs 파일에 대한 C# 소스 코드는 다음 섹션에 있습니다.


## Program.cs 파일


다음의 코드 파일 Program.cs는 오류가 발생할 때에만 실행되는 `try/catch` 블록을 포함하고 있기 때문에 깁니다. 현재 항목의 끝부분은 `Program.cs`모든 `try/catch` 줄이 제거된 훨씬 짧은 버전입니다.


`Main` 방법은 `Program.cs`에 있습니다. 호출 스택은 다음과 같이 실행됩니다.


1. `Main`은 `ConnectAndQuery`를 호출합니다.

2. `ConnectAndQuery`은 `InitializeFields`를 호출합니다.

3. `ConnectAndQuery`은 `EstablishConnection`를 호출합니다.

4. `EstablishConnection`은 `IssueQueryCommand`를 호출합니다.


&nbsp;


	using     System;  // C#
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1 
	{
	    /// <summary>
	    /// Demo sample C# program that uses Microsoft's Enterprise Library 6 to robustly
	    /// connect to and interact with Azure SQL Database.
	    /// .
	    /// The sample chooses a strategy of discarding the Sql Connection whenever
	    /// a Sql Command fails, even for a transient reason. The 'for' loop
	    /// manages this choice. We are neither recommending nor disrecommending
	    /// this particular choice, just demonstrating flexibility.
	    /// .
	    /// For an optional test mechanism manually built into this program, find all:
	    /// 'TEST.PASSWORD_FIX' (related to connection).
	    /// </summary>
	    class Program
	    {
	        // const fields, so that SqlException objects thrown from Sql Commands
	        // are tested for transience by our custom logic.
	        private const string M_progressCreatingConnection = "CreatingConnection";
	        private const string M_progressIssuingCommand     = "IssuingCommand";
	
	        // Fields, shared among methods.
	        private string progressLocation;
	        private E.ReliableSqlConnection rsConnection;
	        private E.RetryPolicy connectionRetryPolicy;
	        private E.RetryPolicy commandRetryPolicy;
	        private C.SqlConnectionStringBuilder scsBuilder;
	        private Action EstablishConnection_action;
	        private Action IssueQueryCommand_action;
	        private string sqlSelectCode;
	        private ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	            forcePersistent_SqlCommandTransientErrorDetectionStrategy;
	
	        // Also consider E.FixedInterval or E.Incremental.
	        private E.ExponentialBackoff exponentialBackoffStrategy;
	
	        // TEST.PASSWORD_FIX.  Replace with Test2_TransientErrorDetectionStrategy.
	        private Custom_SqlDatabaseTransientErrorDetectionStrategy
	            custom_SqlDatabaseTransientErrorDetectionStrategy;
	
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	
	        /// <summary>
	        /// Called from Main.
	        /// Calls a method to establish a connection, which calls
	        /// another method to issue a query through the connection. 
	        /// </summary>
	        void ConnectAndQuery()
	        {
	            int maxCountTriesConnectAndQuery = 3;  // Adjustable.
	
	            this.InitializeFields();
	
	            //-------------------------------------------------------
	            // Preparations are complete.
	            // Next is a call .ExecuteAction to invoke the method EstablishConnection.
	            // Method EstablishConnection then calls another .ExecuteAction to
	            //     invoke the method IssueQueryCommand.
	            // Then control is returned to this method.
	
	            for (int cc = 1; cc <= maxCountTriesConnectAndQuery; cc++)
	            {
	                if (cc >= 2) { Console.WriteLine("-- Another iteration of outer loop, cc=={0}. --", cc); }
	
	                try
	                {
	                    // [A.1] Connect, which proceeds to issue a query command.
	                    this.connectionRetryPolicy.ExecuteAction(this.EstablishConnection_action);
	
	                    break; // [A.2] All has gone well, so let the program end.
	                }
	                catch (C.SqlException sqlExc)
	                {
	                    bool isTransientErrorNumber = false;
	
	                    // [A.3] Call our collection of transient error numbers.
	                    isTransientErrorNumber =
	                        Custom_SqlDatabaseTransientErrorDetectionStrategy // TEST.PASSWORD_FIX.
	                        .IsTransientStatic(sqlExc);
	
	                    if (isTransientErrorNumber == false)
	                    {
	                        Console.WriteLine();
	                        Console.WriteLine("Persistent error suffered, SqlException.Number = {0}."
	                            + "  Will terminate.", sqlExc.Number);
	                        Console.WriteLine(sqlExc.ToString());
	
	                        // [A.4] Either the connection attempt or the query command attempt
	                        //     suffered a persistent SqlException.
	                        // Break the query command loop, let the hopeless program end.
	                        break;
	                    }
	
	                    if (this.progressLocation == M_progressCreatingConnection)
	                    {
	                        // [A.5] The EntLib retry mechanisms already gave the Sql Connection
	                        //     enough retries, so this block will not give connection any more tries.
	                        // This block gives retries to certain Sql Command only.
	                        break;
	                    }
	
	                    // [A.6] The SqlException identified a transient error from an attempt
	                    //     to issue a query command.
	                    // So let this method reloop and try again. However, we recommend that the new
	                    // query attempt should start at the beginning and establish a new connection.
	                    Console.WriteLine();
	                    Console.WriteLine("Transient error encountered.  SqlException.Number = {0}."
	                        + "  Program might retry by itself.", sqlExc.Number);
	                    Console.WriteLine("{0} = Attempts so far. Might retry.", cc);
	                    Console.WriteLine(sqlExc.Message);
	                }
	                catch (Exception exc)
	                {
	                    Console.WriteLine();
	                    Console.WriteLine("Unexpected exception type caught.  Will terminate.");
	                    throw exc; // [A.7] The program must end, so re-throw the unrecognized exception.
	                }
	
	
	                // [A.8] Throw an exception if transient SqlExceptions caused us
	                // to exceed our self-imposed maximum of retries.
	                if (cc > maxCountTriesConnectAndQuery)
	                {
	                    Console.WriteLine();
	                    string mesg = String.Format(
	                        "Transient errors suffered in too many retries ({0}). Will terminate.",
	                        cc - 1);
	                    Console.WriteLine(mesg);
	
	                    // [A.9] To end the program, throw a new exception of a different type.
	                    ApplicationException appExc = new ApplicationException(mesg);
	                    throw appExc;
	                }
	            } // for cc
	            return;
	        } // method ConnectAndQuery
	
	
	        void EstablishConnection()
	        {
	            try
	            {
	                // [C.1]
	                using ( this.rsConnection = new E.ReliableSqlConnection(
	                    this.scsBuilder.ToString(),
	                    this.connectionRetryPolicy,
	                    this.commandRetryPolicy )
	                    )
	                {
	                    this.progressLocation = M_progressCreatingConnection;
	                    this.rsConnection.Open(); // [C.2] Open the newly constructed reliable connection.
	
	                    // [C.3] Prepare delegate, then issue a query command.
	                    this.commandRetryPolicy.ExecuteAction(this.IssueQueryCommand_action);
	                }
	            }
	            // TEST.PASSWORD_FIX.
	                // Step_1: In InitializeFields(), set password to a wrong value.
	                // Step_2: Uncomment the following 'catch' of sqlExc.
	            //catch (C.SqlException sqlExc)  // For TESTING only.  Usually comment out this one CATCH.
	            //{
	            //    if (sqlExc.Number == 18456)  // Could mean wrong password, could not connect.
	            //    {
	            //        this.scsBuilder["Password"] = "MyCorrectPassword";
	            //        Console.WriteLine("Testing.  Password now fixed in catch.");
	            //    }
	            //    throw sqlExc;
	            //}
	            catch (Exception exc)
	            {
	                throw exc;  // [C.4] Let caller assess any exception, SqlException or any kind.
	            }
	            return;
	        } // method EstablishConnection
	
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	
	            X.StringBuilder sBuilder = new X.StringBuilder(256);
	
	            try
	            {
	                // [D.1] Use the connection to create a query command.
	                using (dbCommand = this.rsConnection.CreateCommand())
	                {
	                    dbCommand.CommandText = this.sqlSelectCode;
	
	                    // [D.2] Issue the query command through the connection.
	                    this.progressLocation = M_progressIssuingCommand;
	                    using (dReader = this.rsConnection.ExecuteCommand<D.IDataReader>(dbCommand))
	                    {
	                        Console.WriteLine();
	                        // [D.3] Loop through all returned rows, writing the data to the console.
	                        while (dReader.Read())
	                        {
	                            sBuilder.Length = 0;
	                            sBuilder.Append(dReader.GetString(0));
	                            sBuilder.Append("\t");
	                            sBuilder.Append(dReader.GetString(1));
	
	                            Console.WriteLine(sBuilder.ToString());
	                        }
	                    }
	                }
	            }
	            catch (Exception exc)
	            {
	                throw exc; // [D.4] Let caller assess any exception.
	            }
	            return;
	        } // method IssueQueryCommand
	
	
	        void InitializeFields()
	        {
	            // [B.1] Prepare the connection string to Azure SQL Database.
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";  // @yourservername suffix sometimes.
	            this.scsBuilder["Password"] = "MyCorrectPassword";  // TEST.PASSWORD_FIX.
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30; // Default is 15 seconds, too brief over Internet!
	
	            // [B.2] Prepare the Transact-SQL select statement.
	            this.sqlSelectCode =
	                @"SELECT TOP 3 name, CAST(object_id as nvarchar(32)) as [object_id]
	                  FROM sys.objects
	                  WHERE type='IT'
	                  ORDER BY name;";
	
	            // [B.3] Begin steps to create a retry policy for connecting.
	            this.exponentialBackoffStrategy = new E.ExponentialBackoff(
	                "exponentialBackoffStrategy",
	                2,                               // Maximum number of times to retry.
	                TimeSpan.FromMilliseconds(2000), // Minimum interval between retries.
	                TimeSpan.FromMilliseconds(8000), // Maximum interval between retries.
	                TimeSpan.FromMilliseconds(2000)  // Factor for random differences in interval between retries.
	                );
	
	            this.forcePersistent_SqlCommandTransientErrorDetectionStrategy =
	                new ForcePersistent_SqlCommandTransientErrorDetectionStrategy();
	
	            this.custom_SqlDatabaseTransientErrorDetectionStrategy =
	                new Custom_SqlDatabaseTransientErrorDetectionStrategy();// TEST.PASSWORD_FIX.
	
	            this.connectionRetryPolicy = new E.RetryPolicy(
	                this.custom_SqlDatabaseTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	            this.commandRetryPolicy = new E.RetryPolicy(
	                this.forcePersistent_SqlCommandTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	
	            this.EstablishConnection_action = delegate() { this.EstablishConnection(); };
	            this.IssueQueryCommand_action   = delegate() { this.IssueQueryCommand(); };
	
	            return;
	        } // method InitializeFields
	    } // class Program
	}


&nbsp;


## `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs` 파일


EntLib60은 `ReliableSqlConnection`이라는 클래스를 가집니다. 연결 인스턴스가 예외 `ITransientErrorDetectionStrategy`를 구현하는 클래스에 할당할 때 발생하는 일시적인 오류인지 결정하는 방법을 제어할 수 있습니다.

EntLib60가 클래스 `SqlDatabaseTransientErrorDetectionStrategy`을 제공합니다. 하지만 이번 항목에서는 사용자 지정 클래스 `Custom_SqlDatabaseTransientErrorDetectionStrategy`를 구현하고 사용하기로 했습니다. 사용자 지정 클래스는 `SqlException.Number`에 대하여 비교하는 값의 허용 목록을 가지고 있습니다.


	using     System;
	using G = System.Collections.Generic;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// A custom alternative to class SqlDatabaeTransientErrorDetectionStrategy.
	    /// </summary>
	    public class Custom_SqlDatabaseTransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        static private G.List<int> M_listTransientErrorNumbers;
	
	
	        /// <summary>
	        /// This method is required by ITransientErrorDetectionStrategy.
	        /// </summary>
	        public bool IsTransient(Exception exc)
	        {
	            return IsTransientStatic(exc);
	        }
	
	
	        /// <summary>
	        /// For general use beyond formal Enterprise Library classes.
	        /// </summary>
	        static public bool IsTransientStatic(Exception exc)
	        {
	            bool returnBool = false;  // Assume is a persistent error.
	            C.SqlException sqlExc;
	
	            if (exc is C.SqlException)
	            {
	                sqlExc = exc as C.SqlException;
	                if (M_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
	                {
	                    returnBool = true;  // Error is transient, not persistent.
	                }
	            }
	            return returnBool;
	        }
	
	
	        /// <summary>
	        /// Lists the SqlException.Number values that are considered
	        /// to indicate transient errors.
	        /// </summary>
	        static Custom_SqlDatabaseTransientErrorDetectionStrategy()
	        {
	            int[] arrayOfTransientErrorNumbers =
	                {4060, 10928, 10929, 40197, 40501, 40613};
	
	            M_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
	        }
	    } // class Custom_SqlDatabaseTransientErrorDetectionStrategy
	}


&nbsp;


## `ForcePersistent_SqlCommandTransientErrorDetectionStrategy.cs` 파일


`ITransientErrorDetectionStrategy`을 구현하는 이 사용자 지정 클래스는 EntLib60이 어떤 쿼리 명령 오류도 단순히 일시적인 것으로 간주하지 않도록 하는 선택을 적용합니다. 대신 전체적인 프로그램이 사용자 지정 논리의 `SqlException.Number`에 액세스하도록 설계되었습니다.


이 섹션의 클래스에서는 일시적이거나 그렇지 않은 실패 쿼리 명령을 재시도하기 전에 프로그램이 새로운 연결을 취소하고 다시 만드는 설계를 선택했습니다.


	using     System;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// Is a substitute for class E.SqlCommandTransientErrorDetectionStrategy.
	    /// Choosing to always discard the Sql Connection whenever the command fails,
	    /// regardless of whether command failure was transient.
	    /// Program try/catch and for-loop logic prefers to discard then re-construct
	    /// a new Sql Connection.
	    /// </summary>
	    public class ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        public bool IsTransient(Exception exc)
	        {
	            return false;  // Force a failure, even if is transient.  No retry of sql command.
	        }
	    }
	}


&nbsp;


## 재시도 논리 테스트


EntLib60 프로그램은 테스트하기에 까다롭습니다. 순전하게 일시적인 오류를 발생시키고 그 타이밍을 제어하기가 어렵습니다. 이 데모 코드는 일시적인 오류를 모방하기 위해 사용할 수 있는 간단한 기술을 포함하고 있습니다.


이 기술은 `Custom_SqlTransientErrorDetectionStrategy` 클래스 대신 사용자 지정 탐지기인 `Test2_TransientErrorDetectionStrategy` 클래스를 사용합니다. 이러한 방식으로 EntLib60의 `ReliableSqlConnection`가 *모든* `SqlException`의 일시적인 발생에 액세스할 수 있도록 합니다. 즉 프로그램이 첫 번째 재시도 전에 해당 문제를 스스로 해결하도록 하는 것입니다.


### 철자가 잘못된 암호 트릭


연결 암호에 대해 철자가 잘못된 값으로 시작하는 쉬운 방법입니다. 이렇게 하여 `SqlException`과 `sqlExc.Number==18456`가 발생하면 프로그램이 암호를 수정해야 합니다. 프로그램이 재시도에서 성공합니다.


데모 프로그램에서 이 테스트를 구현하려면 `TEST.PASSWORD_FIX`의 모든 발생에 대한 소스 코드를 검색합니다. `TEST.PASSWORD_FIX`의 모든 발생을 찾고 각 위치의 주석에 설명된 변경 내용을 만든 후에 다음의 변경 내용을 수행합니다.


- `InitializeFields` 메서드의 라이브 코드에서 암호 값을 잘못된 값으로 변경합니다.

- `EstablishConnection` 메서드의 주석이 있는 `//` 코드에 주석 처리된 전체 `catch` 블록...<br/>`//catch (SDSqlC.SqlException sqlExc)`<br/>이 있습니다. 전체 블록의 주석을 제거합니다.

- 새롭게 주석이 제거된 `catch` 블록에서 올바른 암호 값을 할당합니다.

- 여기서 클래스 `Custom_TransientErrorDetectionStrategy`은 코드에서 참조되고 `Test2_TransientErrorDetectionStrategy`로 참조를 대체합니다. 변수 이름을 동일하게 유지할 수 있습니다.


### `Test2_TransientErrorDetectionStrategy.cs` 파일


	using     System;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// For testing, is a substitute for class E.SqlDatabaseTransientErrorDetectionStrategy.
	    /// Change the true/false assignment to returnValue for different tests.
	    /// </summary>
	    public class Test2_TransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        public bool IsTransient(Exception exc)
	        {
	            bool returnValue = Test2_TransientErrorDetectionStrategy.IsTransientStatic(exc);
	            Console.WriteLine("Inside Test2_TransientErrorDetectionStrategy .IsTransientStatic,"
	                + " returning {0}.", returnValue);
	            return returnValue;
	        }
	        static public bool IsTransientStatic(Exception exc)
	        {
	            return true;  //false;
	        }
	    }
	}


&nbsp;


## `try/catch` 없이 더 짧은 `Program.cs`


이 섹션에서 더 짧은 코드는 컴파일하며 실행하는 경우 일반적으로 작동합니다. 하지만 더 짧은 코드는 프로덕션에서 실행되지 않도록 되어있습니다.


이 짧은 `Program.cs` 샘플의 유일한 목적은 실제 `Program.cs`에서 동일한 EntLib 호출을 보기 쉽게 하기 위한 것입니다. 모든 `try/catch` 블록을 제거하면 EntLib 호출을 더욱 쉽게 볼 수 있습니다.


	using     System;  // C#
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1_Shorter_No_TryCatch
	{
	    class Program
	    {
	        private E.ReliableSqlConnection rsConnection;
	        private E.RetryPolicy connectionRetryPolicy;
	        private E.RetryPolicy commandRetryPolicy;
	        private E.ExponentialBackoff exponentialBackoffStrategy;
	        private C.SqlConnectionStringBuilder scsBuilder;
	        private Action EstablishConnection_action;
	        private Action IssueQueryCommand_action;
	        private string sqlSelectCode;
	        private Custom_SqlDatabaseTransientErrorDetectionStrategy
	            custom_SqlDatabaseTransientErrorDetectionStrategy;
	        private ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	            forcePersistent_SqlCommandTransientErrorDetectionStrategy;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        void ConnectAndQuery()
	        {
	            this.InitializeFields();
	            this.connectionRetryPolicy.ExecuteAction(this.EstablishConnection_action);
	        } // method ConnectAndQuery
	
	        void EstablishConnection()
	        {
	            using ( this.rsConnection = new E.ReliableSqlConnection(
	                this.scsBuilder.ToString(),
	                this.connectionRetryPolicy,
	                this.commandRetryPolicy )
	                    )
	            {
	                this.rsConnection.Open();
	                this.commandRetryPolicy.ExecuteAction(this.IssueQueryCommand_action);
	            }
	        } // method EstablishConnection
	
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(256);
	
	            using (dbCommand = this.rsConnection.CreateCommand())
	            {
	                dbCommand.CommandText = this.sqlSelectCode;
	                using (dReader = this.rsConnection.ExecuteCommand<D.IDataReader>(dbCommand))
	                {
	                    while (dReader.Read())
	                    {
	                        sBuilder.Length = 0;
	                        sBuilder.Append(dReader.GetString(0));
	                        sBuilder.Append("\t");
	                        sBuilder.Append(dReader.GetString(1));
	                        Console.WriteLine(sBuilder.ToString());
	                    }
	                }
	            }
	        } // method IssueQueryCommand
	
	        void InitializeFields()
	        {
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";
	            this.scsBuilder["Password"] = "MyPassword";  // TEST.PASSWORD_FIX.
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30;
	
	            this.sqlSelectCode =
	                @"SELECT TOP 3 name, CAST(object_id as nvarchar(32)) as [object_id]
	                  FROM sys.objects
	                  WHERE type='IT'
	                  ORDER BY name;";
	
	            this.exponentialBackoffStrategy = new E.ExponentialBackoff(
	                "exponentialBackoffStrategy",
	                2,
	                TimeSpan.FromMilliseconds(2000),
	                TimeSpan.FromMilliseconds(8000),
	                TimeSpan.FromMilliseconds(2000)
	                );
	
	            this.forcePersistent_SqlCommandTransientErrorDetectionStrategy =
	                new ForcePersistent_SqlCommandTransientErrorDetectionStrategy();
	            this.custom_SqlDatabaseTransientErrorDetectionStrategy =
	                new Custom_SqlDatabaseTransientErrorDetectionStrategy();
	
	            this.connectionRetryPolicy = new E.RetryPolicy(
	                this.custom_SqlDatabaseTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	            this.commandRetryPolicy = new E.RetryPolicy(
	                this.forcePersistent_SqlCommandTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	
	            this.EstablishConnection_action = delegate() { this.EstablishConnection(); };
	            this.IssueQueryCommand_action   = delegate() { this.IssueQueryCommand(); };
	        } // method InitializeFields
	    } // class Program
	}


## 관련 링크


- [Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling 네임스페이스](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)

- [Enterprise Library 6 클래스 라이브러리](http://msdn.microsoft.com/library/dn170426.aspx)

- [코드 샘플: ADO.NET을 사용하여 C#에서 SQL 데이터베이스에 연결하는 논리 재시도](sql-database-develop-csharp-retry-windows.md)

- [SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=August15_HO6-->