<properties 
	pageTitle="SQL Server 저장 프로시저 작업" 
	description="SQL Server 저장 프로시저 작업을 사용하여 데이터 팩터리 파이프라인으로 Azure SQL 데이터베이스에서 저장 프로시저를 호출해보겠습니다." 
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
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# SQL Server 저장 프로시저 작업

데이터 팩터리 [파이프라인](data-factory-create-pipelines.md)에서 SQL Server 저장 프로시저 작업을 사용하여 **Azure SQL** 데이터베이스에서 저장 프로시저를 호출할 수 있습니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서에서 작성합니다.

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
inputs | 실행할 저장 프로시저 작업에 사용할 수 있어야 하는 입력입니다('Ready' 상태). | 아니요
outputs | 저장 프로시저 작업에서 생성하는 출력입니다. 출력 테이블은 Azure SQL 데이터베이스를 데이터 팩터리에 연결하는 연결된 서비스를 사용해야 합니다. | 예
storedProcedureName | 출력 테이블에서 사용하는 연결된 서비스로 표시되는 Azure SQL 데이터베이스의 저장 프로시저 이름을 지정합니다. | 예
storedProcedureParameters | 저장 프로시저 매개 변수의 값을 지정합니다. | 아니요

> [AZURE.NOTE]저장 프로시저 활동에 대한 입력은 종속성 관리 및 다른 사용자와 작업 연결에만 사용됩니다. 저장 프로시저에서 입력을 매개 변수로 사용할 수 없습니다.
 

## 예

Azure SQL 데이터베이스에서 두 열이 있는 테이블을 만드는 예를 살펴보겠습니다.

열 | 형식
------ | ----
ID | uniqueidentifier
Datetime | 해당 ID가 생성된 날짜 및 시간

![샘플 데이터](./media/data-factory-stored-proc-activity/sample-data.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime)
	END

> [AZURE.NOTE]매개 변수(이 예에서는 DateTime)의 **이름** 및 **대/소문자**는 아래의 활동 JSON에서 지정된 매개 변수의 이름 및 대/소문자와 일치해야 합니다. 저장 프로시저 정의에서 **@**는 매개 변수에 대한 접두사로 사용되어야 합니다.

데이터 팩터리 파이프라인에서 이 저장 프로시저를 실행하려면 다음을 수행해야 합니다.

1.	저장 프로시저를 실행해야 하는 Azure SQL 데이터베이스의 연결 문자열에 등록할 [연결된 서비스](data-factory-azure-sql-connector.md/#azure-sql-linked-service-properties)를 만듭니다.
2.	Azure SQL 데이터베이스의 출력 테이블을 가리키는 [데이터 집합](data-factory-azure-sql-connector.md/#azure-sql-dataset-type-properties)을 만듭니다. 이 sprocsampleout 데이터 집합을 호출해보겠습니다 이 데이터 집합은 1단계의 연결된 서비스를 참조해야 합니다. 
3.	Azure SQL 데이터베이스에서 저장 프로시저를 만듭니다.
4.	아래 [파이프라인](data-factory-azure-sql-connector.md/#azure-sql-copy-activity-type-properties)을 SqlServerStoredProcedure 활동과 함께 만들어 Azure SQL 데이터베이스에서 저장 프로시저를 호출합니다.

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties":
		    {
		        "activities":
		        [
		            {
		            	"name": "SprocActivitySample",
		             	"type": " SqlServerStoredProcedure",
		             	"outputs": [ {"name": "sprocsampleout"} ],
		             	"typeProperties":
		              	{
		                	"storedProcedureName": "sp_sample",
			        		"storedProcedureParameters": 
		        			{
		            			"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		        			}
						}
	            	}
		        ]
		     }
		}
5.	[파이프라인](data-factory-create-pipelines.md)을 배포합니다.
6.	Data Factory 모니터링 및 관리 보기를 사용하여 [파이프라인을 모니터링](data-factory-monitor-manage-pipelines.md)합니다.

> [AZURE.NOTE]위의 예에서 SprocActivitySample에는 입력이 없습니다. 작업 업스트림과 연결하려면 업스트림 작업의 출력을 이 작업의 입력으로 사용할 수 있습니다. 이 경우 업스트림 작업이 완료되어 출력이 제공될 때까지(Ready 상태) 이 작업은 실행되지 않습니다. 입력은 저장 프로시저 작업에 대한 매개 변수로 직접 사용할 수 없습니다.
> 
> JSON 파일에서 저장 프로시저 매개 변수 이름 및 대/소문자 구분은 대상 데이터베이스에서 저장 프로시저 매개 변수의 이름과 일치해야 합니다.

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

<!---HONumber=August15_HO7-->