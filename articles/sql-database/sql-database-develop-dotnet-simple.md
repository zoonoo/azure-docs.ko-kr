<properties 
	pageTitle=".NET(C#)에서 SQL 데이터베이스 사용" 
	description="이 빠른 시작에 포함된 샘플 코드를 사용하여 C#으로 최신 응용 프로그램을 개발하고 Azure SQL 데이터베이스로 클라우드에서 강력한 관계형 데이터베이스를 통해 지원할 수 있습니다."
	services="sql-database" 
	documentationCenter="" 
	authors="tobbox" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="tobiast"/>


# .NET(C#)에서 SQL 데이터베이스 사용하기 


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


## 요구 사항

### .NET Framework

.NET Framework는 Windows에 사전 설치되어 있습니다. Linux 및 Mac OS X의 경우 [모노 프로젝트](http://www.mono-project.com/)에서 .NET Framework를 다운로드할 수 있습니다.

### SQL 데이터베이스

샘플 데이터베이스를 만들고 연결 문자열을 가져오는 방법을 알아보려면 [시작 페이지](sql-database-get-started.md)를 참조하세요.

## SQL 데이터베이스 연결

SQL 데이터베이스에 연결하는 데에는 [System.Data.SqlClient.SqlConnection 클래스](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx)가 사용됩니다.
	
```
using System.Data.SqlClient;

class Sample
{
  static void Main()
  {
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
	  {
		  conn.Open();	
	  }
  }
}	
```

## 쿼리를 실행하고 결과 집합을 검색합니다. 

[System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) 및 [SqlDataReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqldatareader.aspx) 클래스를 사용하여 SQL 데이터베이스에 대한 쿼리에서 결과 집합을 검색할 수 있습니다. System.Data.SqlClient의 경우 오프라인 [System.Data.DataSet](https://msdn.microsoft.com/library/system.data.dataset.aspx)으로 데이터를 검색하는 것도 지원합니다.
	
```
using System;
using System.Data.SqlClient;

class Sample
{
	static void Main()
	{
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
		{
			var cmd = conn.CreateCommand();
			cmd.CommandText = @"
					SELECT 
						c.CustomerID
						,c.CompanyName
						,COUNT(soh.SalesOrderID) AS OrderCount
					FROM SalesLT.Customer AS c
					LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID
					GROUP BY c.CustomerID, c.CompanyName
					ORDER BY OrderCount DESC;";

			conn.Open();	
		
			using(var reader = cmd.ExecuteReader())
			{
				while(reader.Read())
				{
					Console.WriteLine("ID: {0} Name: {1} Order Count: {2}", reader.GetInt32(0), reader.GetString(1), reader.GetInt32(2));
				}
			}					
		}
	}
}

```

## 행을 삽입하고, 매개 변수를 전달하며, 생성된 기본 키 값을 검색합니다. 

SQL 데이터베이스에서 [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) 속성 및 [SEQUENECE](https://msdn.microsoft.com/library/ff878058.aspx) 개체를 사용하여 [기본 키](https://msdn.microsoft.com/library/ms179610.aspx) 값을 자동으로 생성할 수 있습니다. 이 예제에서는 [insert 문](https://msdn.microsoft.com/library/ms174335.aspx)을 실행하고, [SQL 삽입](https://msdn.microsoft.com/magazine/cc163917.aspx)을 방지하는 매개 변수를 안전하게 전달하며, 자동으로 생성된 기본 키 값을 검색하는 방법을 보여줍니다.

[System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) 클래스의 [ExecuteScalar](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executescalar.aspx) 메서드를 사용하여 문을 실행하고 해당 문에서 반환되는 첫 번째 열과 행을 검색할 수 있습니다. INSERT 문의 [OUTPUT](https://msdn.microsoft.com/library/ms177564.aspx) 절을 사용하여 호출 응용 프로그램에 대한 결과 집합으로 삽입된 값을 반환할 수 있습니다. OUTPUT 절은 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx), [DELETE](https://msdn.microsoft.com/library/ms189835.aspx), [MERGE](https://msdn.microsoft.com/library/bb510625.aspx) 문에서도 지원됩니다. 둘 이상의 행을 삽입하는 경우 [ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) 메서드를 사용하여 모든 행에서 삽입된 값을 검색해야 합니다.
	
```
class Sample
{
    static void Main()
    {
		using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
        {
            var cmd = conn.CreateCommand();
            cmd.CommandText = @"
                INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) 
                OUTPUT INSERTED.ProductID
                VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP)";

            cmd.Parameters.AddWithValue("@Name", "SQL Server Express");
            cmd.Parameters.AddWithValue("@Number", "SQLEXPRESS1");
            cmd.Parameters.AddWithValue("@Cost", 0);
            cmd.Parameters.AddWithValue("@Price", 0);

            conn.Open();

            int insertedProductId = (int)cmd.ExecuteScalar();

            Console.WriteLine("Product ID {0} inserted.", insertedProductId);
        }
    }
}
```

 

<!---HONumber=July15_HO4-->