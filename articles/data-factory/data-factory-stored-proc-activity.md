<properties 
	pageTitle="SQL Server 저장 프로시저 작업" 
	description="SQL Server 저장 프로시저 작업을 사용하여 데이터 팩터리 파이프라인으로 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스에서 저장 프로시저를 호출하는 방법을 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/12/2015" 
	ms.author="spelluru"/>

# SQL Server 저장 프로시저 작업

데이터 팩터리 [파이프라인](data-factory-create-pipelines.md)에서 SQL Server 저장 프로시저 작업을 사용하여 **Azure SQL 데이터베이스** 또는 **Azure SQL 데이터 웨어하우스**에서 저장 프로시저를 호출할 수 있습니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서에서 작성합니다.

## 구문
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": "<name of the stored procedure>",
        	"storedProcedureParameters":  
        	{
				"param1": "param1Value"
				…
        	}
    	}
	}

## 구문 세부 정보

속성 | 설명 | 필수
-------- | ----------- | --------
name | 작업의 이름 | 예
description | 작업이 무엇에 사용되는지 설명하는 텍스트입니다. | 아니요
type | SqlServerStoredProcedure | 예
inputs | 실행할 저장 프로시저 작업에 사용할 수 있어야 하는 데이터 집합입니다('Ready' 상태). 저장 프로시저 활동에 대한 입력은 이 활동을 다른 사용자와 연결할 때 종속성 관리 역할로만 제공됩니다. 저장 프로시저에서 입력 데이터 집합을 매개 변수로 사용할 수 없습니다. | 아니요
outputs | 저장 프로시저 작업에서 생성하는 출력 데이터 집합입니다. 출력 테이블은 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스를 데이터 팩터리에 연결하는 연결된 서비스를 사용해야 합니다. 저장 프로시저 작업에서 출력은 저장 프로시저 작업의 결과를 전달하는 방법으로 제공할 수 있으며 또는 이 활동을 다른 사용자와 연결할 때 종속성 관리 역할로 제공할 수 있습니다. | 예
storedProcedureName | 출력 테이블에서 사용하는 연결된 서비스로 표시되는 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스의 저장 프로시저 이름을 지정합니다. | 예
storedProcedureParameters | 저장 프로시저 매개 변수의 값을 지정합니다. | 아니요

## 샘플 연습

### 샘플 테이블 및 저장 프로시저
1. SQL Server Management Studio 또는 익숙한 다른 도구를 사용하여 Azure SQL 데이터베이스에서 다음 **테이블**을 만듭니다. datetimestamp 열은 해당 ID가 생성된 날짜와 시간입니다. 

		CREATE TABLE dbo.sampletable
		(
			Id uniqueidentifier,
			datetimestamp nvarchar(127)
		)
		GO

		CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
		GO

	Id는 고유 식별자이며 datetimestamp 열은 해당 ID가 생성된 날짜와 시간입니다. ![샘플 데이터](./media/data-factory-stored-proc-activity/sample-data.png)

2. **sampletable**에 데이터를 삽입하는 다음 **저장 프로시저**를 만듭니다.

		CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
		AS
		
		BEGIN
		    INSERT INTO [sampletable]
		    VALUES (newid(), @DateTime)
		END

	> [AZURE.IMPORTANT]매개 변수(이 예에서는 DateTime)의 **이름** 및 **대/소문자**는 파이프라인/작업 JSON에서 지정된 매개 변수의 이름 및 대/소문자와 일치해야 합니다. 저장 프로시저 정의에서 **@**는 매개 변수에 대한 접두사로 사용되어야 합니다.
	
### 데이터 팩터리를 만듭니다.  
4. [Azure Preview 포털](http://portal.azure.com/)에 로그인한 후 다음을 수행합니다.
	1.	왼쪽 메뉴에서 **새로 만들기**를 클릭합니다. 
	2.	**만들기** 블레이드에서 **데이터 분석**을 클릭합니다.
	3.	**데이터 분석** 블레이드에서 **데이터 팩터리**를 클릭합니다.
4.	**새 데이터 팩터리** 블레이드에서 이름으로 **SProcDF**를 입력합니다. Azure Data Factory 이름은 전역적으로 고유합니다. 팩터리를 성공적으로 만들려면 데이터 팩터리의 이름의 접두사를 사용자의 이름으로 해야 합니다. 
3.	만들어 놓은 리소스 그룹이 없으면 리소스 그룹을 만들어야 합니다. 다음을 수행합니다.
	1.	**리소스 그룹 이름**을 클릭합니다.
	2.	**리소스 그룹** 블레이드에서 **새 리소스 그룹 만들기**를 선택합니다.
	3.	**리소스 그룹 만들기** 블레이드에서 **이름**으로 **ADFTutorialResourceGroup**을 입력합니다.
	4.	**확인**을 클릭합니다.
4.	리소스 그룹을 선택한 후에 데이터 팩터리를 만들려는 구독을 사용하고 있는지 확인합니다.
5.	**새 데이터 팩터리** 블레이드에서 **만들기**를 클릭합니다.
6.	Azure Preview 포털의 **시작 보드**에 생성되는 데이터 팩터리가 표시됩니다. 데이터 팩터리 만들기를 완료한 후에는 데이터 팩터리 페이지가 표시되며 여기에 데이터 팩터리의 내용이 표시됩니다.

### Azure SQL 연결된 서비스 만들기  
데이터 팩터리를 만든 후 Azure SQL 데이터베이스를 데이터 팩터리에 연결하는 Azure SQL 연결된 서비스를 만듭니다. 이 데이터베이스는 sampletable 테이블과 sp\_sample 저장 프로시저를 포함하는 데이터베이스입니다.

7.	**SProcDF**의 **데이터 팩터리** 블레이드에서 **작성 및 배포**를 클릭합니다. 데이터 팩터리 편집기가 시작됩니다. 
2.	명령 모음에서 **새 데이터 저장소**를 클릭하고 **Azure SQL**을 선택합니다. 편집기에 Azure SQL 연결된 서비스를 만들기 위한 JSON 스크립트가 표시됩니다. 
4. **servername**을 Azure SQL 데이터베이스 서버의 이름으로, **databasename**을 테이블과 저장 프로시저를 만든 데이터베이스로, ****username@servername**을 데이터베이스에 액세스할 수 있는 사용자 계정으로, **password**를 사용자 계정의 암호로 바꿉니다.
5. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.

### 출력 데이터 집합 만들기
6. 명령 모음에서 **새 데이터 집합**을 클릭하고 **Azure SQL**을 선택합니다.
7. 다음 JSON 스크립트를 복사하여 JSON 편집기에 붙여 넣습니다.

		{			    
			"name": "sprocsampleout",
			"properties": {
				"type": "AzureSqlTable",
				"linkedServiceName": "AzureSqlLinkedService",
				"typeProperties": {
					"tableName": "sampletable"
				},
				"availability": {
					"frequency": "Hour",
					"interval": 1
				}
			}
		}
7. 명령 모음에서 **배포**를 클릭하여 데이터 집합을 배포합니다. 

### SqlServerStoredProcedure 작업을 사용하여 파이프라인 만들기
이제 SqlServerStoredProcedure 작업을 사용하여 파이프라인 만들겠습니다.
 
9. 명령 모음에서 **...(줄임표)**를 클릭하고 **새 파이프라인**을 클릭합니다. 
9. 다음 JSON 코드 조각을 복사하여 붙여 넣습니다. **storedProcedureName**은 **sp\_sample**로 설정됩니다. **DateTime** 매개 변수의 이름 및 대/소문자는 저장 프로시저 정의에 있는 매개 변수의 이름 및 대/소문자와 일치해야 합니다.  

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties": {
		        "activities": [
		            {
		                "type": "SqlServerStoredProcedure",
		                "typeProperties": {
		                    "storedProcedureName": "sp_sample",
		                    "storedProcedureParameters": {
		                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		                    }
		                },
		                "outputs": [
		                    {
		                        "name": "sprocsampleout"
		                    }
		                ],
		                "scheduler": {
		                    "frequency": "Hour",
		                    "interval": 1
		                },
		                "name": "SprocActivitySample"
		            }
		        ],
		        "start": "2015-01-02T00:00:00Z",
		        "end": "2015-01-03T00:00:00Z",
		        "isPaused": false
		    }
		}
9. 도구 모음에서 **배포**를 클릭하여 파이프라인을 배포합니다.  

### 파이프라인 모니터링

6. **X**를 클릭하여 데이터 팩터리 편집기 블레이드를 닫고 데이터 팩터리 블레이드로 돌아가서 **다이어그램**을 클릭합니다.
7. 다이어그램 뷰에 파이프라인의 개요와 이 자습서에 사용된 데이터 집합이 표시됩니다. 
8. 다이어그램 뷰에서 **sprocsampleout** 데이터 집합을 두 번 클릭합니다. 준비 상태의 조각이 표시됩니다. 조각이 2015/01/02에서 2015/01/03 사이에 1시간마다 생성되기 때문에 24개의 조각이 있습니다. 
10. 조각이 **준비** 상태일 때 Azure SQL 데이터베이스에 대해 **select * from sampledata** 쿼리를 실행하여 저장 프로시저에 의해 데이터가 테이블에 삽입되었는지 확인합니다.

	![출력 데이터](./media/data-factory-stored-proc-activity/output.png)

	Azure Data Factory 파이프라인 모니터링에 대한 자세한 내용은 [파이프라인 모니터링](data-factory-monitor-manage-pipelines.md)을 참조하세요.

> [AZURE.NOTE]위의 예에서 SprocActivitySample에는 입력이 없습니다. 작업 업스트림과 연결하려면(즉, 처리 전) 업스트림 작업의 출력을 이 작업의 입력으로 사용할 수 있습니다. 이 경우 업스트림 작업이 완료되어 업스트림 작업의 출력이 제공될 때까지(Ready 상태) 이 작업은 실행되지 않습니다. 입력은 저장 프로시저 작업에 대한 매개 변수로 직접 사용할 수 없습니다.

## 정적 값 전달 
'Document sample'라는 정적 값이 포함된 테이블에 'Scenario'라는 다른 열을 추가해보겠습니다.

![샘플 데이터 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

이 작업을 수행하기 위해 Scenario 매개 변수와 저장 프로시저 작업의 값을 전달합니다. 위 샘플에서 typeproperties 섹션은 다음과 같습니다.

	"typeProperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=Oct15_HO3-->