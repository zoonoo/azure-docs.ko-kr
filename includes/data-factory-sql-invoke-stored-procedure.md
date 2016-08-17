## SQL 싱크에 대한 저장 프로시저 호출

SQL Server 또는 Azure SQL/SQL Server 데이터베이스로 데이터를 복사할 때 사용자 지정 저장 프로시저를 구성하고 추가 매개 변수로 호출할 수 있습니다.

기본 제공 메커니즘이 용도에 적합하지 않을 때는 저장 프로시저를 사용할 수 있습니다. 보통은 대상 테이블에서 원본 데이터의 최종 삽입 전에 추가 처리(열 합병, 추가 값 검색, 여러 테이블에 삽입 등)를 수행해야 할 때 저장 프로시저를 사용합니다.

선택한 저장 프로시저를 호출할 수 있습니다. 다음 샘플에서는 저장 프로시저를 사용하여 데이터베이스 내 테이블에 간단한 삽입을 수행하는 방법을 보여줍니다.

**출력 데이터 집합**

이 예에서는 형식이 SqlServerTable로 설정되어 있습니다. Azure SQL 데이터베이스에 사용하기 위해 이 항목을 AzureSqlTable로 설정합니다.

	{
	  "name": "SqlOutput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlLinkedService",
	    "typeProperties": {
	      "tableName": "Marketing"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}
	
복사 작업 JSON의 SqlSink 섹션을 다음과 같이 정의합니다. 데이터를 삽입하는 동안 저장된 프로시저를 호출하려면 SqlWriterStoredProcedureName 및 SqlWriterTableType 속성이 모두 필요합니다.

	"sink":
	{
	    "type": "SqlSink",
	    "SqlWriterTableType": "MarketingType",
	    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
	    "storedProcedureParameters":
	            {
	                "stringData": 
	                {
	                    "value": "str1"     
	                }
	            }
	}

데이터베이스에서 SqlWriterStoredProcedureName과 동일한 이름으로 저장 프로시저를 정의합니다. 지정된 원본에서 입력 데이터를 처리하고 출력 테이블로 삽입합니다. 저장 프로시저의 매개 변수 이름은 Table JSON 파일에 정의된 tableName과 동일해야 합니다.

	CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
	AS
	BEGIN
	    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
	    INSERT [dbo].[Marketing](ProfileID, State)
	    SELECT * FROM @Marketing
	END

데이터베이스에서 SqlWriterTableType과 동일한 이름으로 테이블 형식을 정의합니다. 테이블 형식의 스키마가 입력 데이터에서 반환된 스키마와 동일해야 합니다.

	CREATE TYPE [dbo].[MarketingType] AS TABLE(
	    [ProfileID] [varchar](256) NOT NULL,
	    [State] [varchar](256) NOT NULL
	)

저장된 프로시저 기능은 [테이블 값 매개 변수](https://msdn.microsoft.com/library/bb675163.aspx)을 이용합니다.

<!---HONumber=AcomDC_0803_2016-->