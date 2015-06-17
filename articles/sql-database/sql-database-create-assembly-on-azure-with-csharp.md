<properties 
	pageTitle="CSharp를 사용하여 Azure SQL 데이터베이스에서 어셈블리 만들기"
	description="긴 16진수 숫자가 포함된 문자열로 DLL 파일을 처음 인코딩한 후 Azure SQL 데이터베이스에 CREATE ASSEMBLY를 실행하는 C# 소스 코드를 제공합니다. "
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="genemi"/>


# CSharp를 사용하여 Azure SQL 데이터베이스에서 어셈블리 만들기


<!--
GeneMi , Latest edit = 2015-March-25  Wednesday  10:22am
Converting plain text "CREATE ASSEMBLY" into a link to the MSDN topic, ms189524.aspx. And ms186755.aspx for "CREATE FUNCTION".
-->


이 항목에서는 Azure SQL 데이터베이스에 [CREATE ASSEMBLY](http://msdn.microsoft.com/library/ms189524.aspx) 문을 실행하는 데 사용할 수 있는 C# 코드 샘플을 제공합니다. SQL 데이터베이스의 경우 FROM 절에는 데이터베이스를 호스트하는 로컬 컴퓨터의 경로에 단순한 형식을 사용할 수 없습니다. 따라서 먼저 16진수 숫자가 포함된 긴 문자열로 어셈블리 DLL의 이진 비트를 인코딩해야 합니다. 그런 다음 문자열을 FROM 절의 값으로 제공합니다.


### 필수 조건


이 항목을 이해하려면 다음 사항을 일부 알고 있어야 합니다.


- [CLR 테이블 값 함수](http://msdn.microsoft.com/library/ms131103.aspx)<br/>CREATE ASSEMBLY Transact-SQL 문이 온-프레미스 Microsoft SQL Server의 다른 문과 함께 작동하는 방식을 설명합니다.


## A. 전체 기술


1. 이전 실행을 정리해야 하는 경우 DROP FUNCTION 및 DROP ASSEMBLY를 실행합니다.
2. 사용자 고유의 코드에서 컴파일된 Microsoft.NET Framework 어셈블리 DLL 파일의 위치를 기억해야 합니다. 다음 단계에서 위치를 제공합니다. 
3. 이 항목에서 C# 소스 코드를 제공할 EXE를 실행합니다. EXE에 DLL 파일의 위치를 알려 줍니다.
 - 16진수 숫자가 포함된 긴 문자열로 이진 DLL을 인코딩합니다.
 - FROM 절에서 지정된 16진수 문자열과 함께 CREATE ASSEMBLY 문을 실행합니다.
4. [CREATE FUNCTION](http://msdn.microsoft.com/library/ms186755.aspx)을 실행하여 어셈블리의 메서드를 참조합니다.
5. T-SQL SELECT 문을 실행하여 함수를 호출하고 테스트합니다.


이전 목록에는 다음에 대한 언급이 없습니다.<br/>
**execute sp_configure 'clr enabled', 1;**<br/>
이는 Microsoft SQL Server에는 필요하지만 Azure SQL 데이터베이스에는 필요하지 않기 때문입니다.


다시 실행하는 데 필요한 경우 함수 및 어셈블리를 삭제하는 T-SQL 코드는 다음과 같습니다.


    DROP FUNCTION fnCompareStringsCaseSensitive;
    DROP ASSEMBLY CreateAssemblyFunctions3;


## B. 참조할 T-SQL 함수에 대한 단순 어셈블리 DLL


이 섹션의 간단한 C# 코드 샘플은 어셈블리 DLL 파일로 컴파일할 수 있습니다.


이 코드 샘플에는 나중에 T-SQL CREATE FUNCTION 문에서 참조되는 **CompareCaseSensitiveNet** 메서드가 들어 있습니다. 이 메서드는 **SqlFunction**이라는 .NET 특성으로 데코레이트됩니다. 이 특성으로 데코레이트된 메서드를 T-SQL에서 함수로 호출할 수 있습니다.


	using           System;   // C#
	using SDSqTyp = System.Data.SqlTypes;
	using MSqServ = Microsoft.SqlServer.Server;
	
	namespace CreateAssemblyFunctions3
	{
	    public class SqlFunctionMethodsForSprocs
	    {
	        /// <summary> This method is referenced
	        /// by a T-SQL CREATE FUNCTION statement. </summary>
	        [MSqServ.SqlFunction(IsDeterministic = true, IsPrecise = true)]
	        static public SDSqTyp.SqlInt32 CompareCaseSensitiveNet(string strA, string strB)
	        {
	            return String.Compare(strA, strB, false);
	        }
	    }
	}


## C. CREATE ASSEMBLY를 실행하는 EXE에 대한 C&#x23; 코드 샘플


다음 시퀀스는 이 C# 샘플에서 작성된 EXE를 실행할 때 발샹합니다.


1. EXE의 명령줄 실행에서는 **Main** 메서드를 호출합니다.
2. Main은 **ObtainHexStringOfAssembly** 메서드를 호출합니다.
 - 이 메서드는 어셈블리를 16진수로 저장하는 SqlString을 출력합니다.
3. Main은 **SubmitCreateAssemblyToAzureSqlDb** 메서드를 호출합니다.
 - 기본 입력은 SqlString입니다.
 - 출력은 Azure SQL 데이터베이스로 전송되는 CREATE ASSEMBLY 호출입니다.


			using             System;   // C#
			using SDat      = System.Data;
			using SDSClient = System.Data.SqlClient;
			using SGlo      = System.Globalization;
			using SIo       = System.IO;
			using STex      = System.Text;
			
			namespace CreateAssemblyFromHexString6
			{
			    /// <summary>
			    /// Run the Main method on your local computer console, so it can issue a
			    /// a CREATE ASSEMBLY statement to your Azure SQL Database server:
			    /// </summary>
			    class Program
			    {
			        /// <summary>
			        /// Calls the methods in the proper sequence.
			        /// </summary>
			        /// <param name="args">
			        /// Parameters for the cmd.exe command line
			        ///    run of CreateAssemblyFromHexString6.exe:
			        /// args[0] = FullDirPathFileNameOfAssembly.
			        /// args[1] = AssemblyName.
			        ///    For the CREATE ASSEMBLY assemblyName statement.
			        /// args[2] = Azure SQL Database - ServerName, including a suffix like .database.windows.net .
			        /// args[3] = Azure SQL Database - DatabaseName.
			        /// args[4] = Azure SQL Database - LoginName.
			        /// args[5] = Azure SQL Database - Password for login.
			        ///    (Better if from .config file.)
			        /// </param>
			        static int Main(string[] args)
			        {
			            int returnCode = 1; // Only 0 (zero) means Good Success.
			            string
			                fullPathNameAssemblyFile,
			                assemblyName,
			                AzureSqlDbServerName,
			                AzureSqlDbDatabaseName,
			                AzureSqlDbLoginName,
			                AzureSqlDbPassword;
			
			            try
			            {
			                fullPathNameAssemblyFile = args[0];
			                assemblyName             = args[1];
			                AzureSqlDbServerName     = args[2];
			                AzureSqlDbDatabaseName   = args[3];
			                AzureSqlDbLoginName      = args[4];
			                AzureSqlDbPassword       = args[5];
			
			                string hexStringOfAssembly = Program
			                    .ObtainHexStringOfAssembly(fullPathNameAssemblyFile);
			
			                Program.SubmitCreateAssemblyToAzureSqlDb(
			                    hexStringOfAssembly,
			                    assemblyName,
			                    AzureSqlDbServerName,
			                    AzureSqlDbDatabaseName,
			                    AzureSqlDbLoginName,
			                    AzureSqlDbPassword);
			
			                returnCode = 0;
			            }
			            catch (Exception ex)
			            {
			                Console.WriteLine("Bad, Failure.");
			                throw ex;
			            }
			            Console.WriteLine("Good, Success.");
			            return returnCode;
			        }
			
			        /// <summary> Encodes the binary bits of the assembly DLL into a 
			        /// string containing a hexadecimal number. </summary>
			        /// <param name="fullPathToAssembly"
			        /// >Full directory path plus the file name, to the .DLL file.</param>
			        /// <returns>A string containing a hexadecimal number that encodes
			        /// the binary bits of the .DLL file.</returns>
			        static private string ObtainHexStringOfAssembly
			            (string fullPathToAssembly)
			        {
			            STex.StringBuilder sbuilder = new STex.StringBuilder("0x");
			            using (SIo.FileStream fileStream = new SIo.FileStream(
			                fullPathToAssembly,
			                SIo.FileMode.Open, SIo.FileAccess.Read, SIo.FileShare.Read)
			                )
			            {
			                int byteAsInt;
			                while (true)
			                {
			                    byteAsInt = fileStream.ReadByte();
			                    if (-1 >= byteAsInt) { break; }
			                    sbuilder.Append(byteAsInt.ToString
			                        ("X2", SGlo.CultureInfo.InvariantCulture));
			                }
			            }
			            return sbuilder.ToString();
			        }
			
			        /// <summary>
			        /// Sends a Transact-SQL CREATE ASSEMBLY FROM hexString.
			        /// </summary>
			        static private void SubmitCreateAssemblyToAzureSqlDb(
			            string hexStringOfAssembly,
			            string assemblyName,
			            string AzureSqlDbServerName,
			            string AzureSqlDbDatabaseName,
			            string AzureSqlDbLoginName,
			            string AzureSqlDbPassword)
			        {
			            string sqlCreateAssembly = "CREATE ASSEMBLY [" + assemblyName
			                + "] FROM " + hexStringOfAssembly + ";";
			            STex.StringBuilder sbuilderConnection = new STex.StringBuilder();
			
			            sbuilderConnection.Append("Server=tcp:");
			            sbuilderConnection.Append(AzureSqlDbServerName);
			            sbuilderConnection.Append(",1433;");
			
			            sbuilderConnection.Append("Database=");
			            sbuilderConnection.Append(AzureSqlDbDatabaseName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Trusted_Connection=False;");
			            sbuilderConnection.Append("Connection Timeout=30;");
			
			            sbuilderConnection.Append("User ID=");
			            sbuilderConnection.Append(AzureSqlDbLoginName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Password=");
			            sbuilderConnection.Append(AzureSqlDbPassword);
			            sbuilderConnection.Append(";");
			
			            using (SDSClient.SqlConnection sqlConnection =
			                new SDSClient.SqlConnection())
			            {
			                SDSClient.SqlCommand sqlCommand;
			                sqlConnection.ConnectionString = sbuilderConnection.ToString();
			                sqlCommand = sqlConnection.CreateCommand();
			                sqlCommand.CommandType = SDat.CommandType.Text;
			                sqlCommand.CommandText = sqlCreateAssembly;
			                sqlConnection.Open();
			                sqlCommand.ExecuteNonQuery();
			            }
			            return;
			        }
			    }
			}


### C.1 컴파일 참조 및 버전


EXE 도구용 샘플 코드를 컴파일 및 테스트할 때 사용한 항목은 다음과 같습니다.


- Visual Studio 2013 업데이트 4
 - 프로젝트 템플릿 형식은 간단한 콘솔 응용 프로그램입니다.
- .NET Framework 4.5


Visual Studio 프로젝트는 컴파일을 위해 다음 어셈블리를 참조합니다.


- Microsoft.CSharp
- System
- System.Core
- System.Data
- System.Data.DataSetExtensions
- System.Xml
- System.Xml.Linq


### C.2 EXE를 실행할 명령줄


다음 코드 블록은 콘솔에서 EXE를 실행하기 위해 입력할 명령줄 예제를 표시합니다. 여기에는 가독성을 위해 명령줄의 매개 변수가 인위적으로 래핑되어 있습니다.


	CreateAssemblyFromHexString6.exe
		C:\my\bin\debug\CreateAssemblyFunctions3.dll
		CreateAssemblyFunctions3
		myazuresqldbsvr2.database.windows.net
		myazuresqldbdab4
		myazurelogin
		Mypassword123


간단히 설명하기 위해 이 예제에서는 암호를 명령줄 매개 변수로 전달합니다. 실제로는 C# 코드가 CONFIG 파일에서 암호를 가져오도록 설계하는 것이 좋습니다.


## D. CREATE FUNCTION 문 실행


어셈블리를 Azure SQL 데이터베이스 서버에 저장한 후에는 어셈블리의 메서드를 참조하는 TRANSACT-SQL CREATE FUNCTION 문을 실행해야 합니다.


다음 TRANSACT-SQL 코드 블록에는 데이터베이스 시스템에 어셈블리 및 함수에 대한 레코드가 있음을 증명하는 중요하지 않은 몇 가지 SELECT 문이 포함되어 있습니다. 마지막으로 함수를 호출하는 SELECT가 있습니다.


	SELECT a11.*, am2.*
		FROM           sys.assemblies       AS a11
		    INNER JOIN sys.assembly_modules AS am2 ON am2.assembly_id = a11.assembly_id
		WHERE a11.name = 'CreateAssemblyFunctions3';
	GO
	
	CREATE FUNCTION fnCompareStringsCaseSensitive
	    (@strA nvarchar(128), @strB nvarchar(128))
	    returns int
	    AS EXTERNAL NAME
	        CreateAssemblyFunctions3
	            .[CreateAssemblyFunctions3.SqlFunctionMethodsForSprocs]
	                .CompareCaseSensitiveNet;
	GO
	SELECT * FROM sys.objects WHERE name = 'fnCompareStringsCaseSensitive';
	
	 -- Use the new function.
	SELECT dbo.fnCompareStringsCaseSensitive('BLUE', 'blue') as returnedValue;
	GO


위의 Transact-SQL 코드 블록은 새 함수를 호출하는 SELECT 문으로 종료됩니다. 저장 프로시저에 SELECT 문을 배치할 수 있습니다.


<!-- EndOfFile -->


<!--HONumber=49--> 