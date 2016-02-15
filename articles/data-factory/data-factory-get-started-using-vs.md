<properties 
	pageTitle="자습서: Visual Studio를 사용하여 복사 작업이 있는 파이프라인 만들기" 
	description="이 자습서에서는 Visual Studio를 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다." 
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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# 자습서: Visual Studio를 사용하여 데이터 팩터리 만들기 및 모니터링
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


##자습서 내용
이 자습서에서는 Visual Studio 2013을 사용하여 다음을 수행합니다.

1. 2개의 연결된 서비스 **AzureStorageLinkedService1** 및 **AzureSqlinkedService1**를 만듭니다. AzureStorageLinkedService1은 Azure 저장소를 연결하고 AzureSqlLinkedService1은 Azure SQL 데이터베이스를 데이터 팩터리 **ADFTutorialDataFactoryVS**에 연결합니다. 파이프라인에 대한 입력 데이터는 Azure Blob 저장소의 Blob 컨테이너에 있고, 출력 데이터는 Azure SQL 데이터베이스의 테이블에 저장됩니다. 따라서 이러한 두 데이터 저장소를 연결된 서비스로 데이터 팩터리에 추가합니다.
2. 데이터 저장소에 저장된 입출력 데이터를 나타내는 2개의 데이터 팩터리 테이블 **EmpTableFromBlob** 및 **EmpSQLTable**을 만듭니다. EmpTableFromBlob에 대해 원본 데이터가 있는 Blob을 포함하는 Blob 컨테이너를 지정하고, EmpSQLTable에 대해 출력 데이터를 저장할 SQL 테이블을 지정합니다. 데이터 구조, 데이터 가용성 등의 기타 속성도 지정합니다.
3. ADFTutorialDataFactoryVS에 **ADFTutorialPipeline**이라는 파이프라인을 만듭니다. 이 파이프라인에는 Azure Blob에서 출력 Azure SQL 테이블로 입력 데이터를 복사하는 **복사 작업**이 있습니다.
4. 데이터 팩터리를 만들고 연결된 서비스, 테이블 및 파이프라인을 배포합니다.    

## Visual Studio를 사용하여 데이터 팩터리 엔터티 만들기 및 배포 

### 필수 구성 요소
컴퓨터에 Visual Studio 2013이 설치되어 있어야 합니다. Visual Studio 2013용 Azure SDK를 다운로드합니다. [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)로 이동하고 **.NET** 섹션에서 **VS 2013 설치**를 클릭합니다.

### 연습

#### Visual Studio 프로젝트 만들기 
1. **Visual Studio 2013**을 실행합니다. **파일**을 클릭하고 **새로 만들기**를 가리킨 다음 **프로젝트**를 클릭합니다. **새 프로젝트** 대화 상자가 나타납니다.  
2. **새 프로젝트** 대화 상자에서 **DataFactory** 템플릿을 선택하고 **빈 데이터 팩터리 프로젝트**를 클릭합니다. DataFactory 템플릿이 보이지 않으면 Visual Studio를 닫고 Visual Studio 2013용 Azure SDK를 설치한 다음 Visual Studio를 다시 엽니다.  

	![새 프로젝트 대화 상자](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. 프로젝트의 **이름**, **위치**, **솔루션**의 이름을 입력한 다음 **확인**을 클릭합니다.

	![솔루션 탐색기](./media/data-factory-get-started-using-vs/solution-explorer.png)

#### 연결된 서비스 만들기
연결된 서비스는 데이터 저장소 또는 계산 서비스를 Azure Data Factory에 연결합니다. 데이터 저장소는 Azure 저장소, Azure SQL 데이터베이스 또는 온-프레미스 SQL Server 데이터베이스일 수 있습니다.

이 단계에서는 2개의 연결된 서비스 **AzureStorageLinkedService1** 및 **AzureSqlLinkedService1**을 만듭니다. AzureStorageLinkedService1 연결된 서비스는 Azure 저장소 계정을 연결하고, AzureSqlLinkedService는 Azure SQL 데이터베이스를 데이터 팩터리 **ADFTutorialDataFactory**에 연결합니다.

##### Azure 저장소 연결된 서비스 만들기

4. 솔루션 탐색기에서 **Linked Services**(연결된 서비스) 노드를 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다.      
5. **새 항목 추가** 대화 상자의 목록에서 **Azure 저장소 연결된 서비스**를 선택한 다음 **추가**를 클릭합니다. 

	![새 연결된 서비스](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. **accountname** 및 **accountkey**를 Azure 저장소 계정 이름 및 해당 키로 바꿉니다.

	![Azure 저장소 연결된 서비스](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. **AzureStorageLinkedService1.json** 파일을 저장합니다.

#### Azure SQL 연결된 서비스 만들기

5. **솔루션 탐색기**에서 다시 **연결된 서비스** 노드를 마우스 오른쪽 단추로 다시 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다. 
6. 이번에는 **Azure SQL 연결된 서비스**를 선택하고 **추가**를 클릭합니다. 
7. **AzureSqlLinkedService1.json 파일**에서 **servername**, **databasename**, ****username@servername** 및 **password**를 Azure SQL Server, 데이터베이스, 사용자 계정 및 암호로 바꿉니다.
8.  **AzureSqlLinkedService1.json** 파일을 저장합니다. 


### 입력 및 출력 테이블 만들기
이전 단계에서는 연결된 서비스 **AzureStorageLinkedService1** 및 **AzureSqlLinkedService1**을 만들어 Azure 저장소 계정과 Azure SQL 데이터베이스를 데이터 팩터리 **ADFTutorialDataFactory**에 연결했습니다. 이 단계에서는 각각 AzureStorageLinkedService1 및 AzureSqlLinkedService1로 참조되는 데이터 저장소에 저장된 입출력 데이터를 나타내는 **EmpTableFromBlob** 및 **EmpSQLTable** 등 2개의 데이터 팩터리 테이블을 정의합니다. EmpTableFromBlob에 대해 원본 데이터가 있는 Blob을 포함하는 Blob 컨테이너를 지정하고, EmpSQLTable에 대해 출력 데이터를 저장할 SQL 테이블을 지정합니다.

#### 입력 테이블 만들기

9. **솔루션 탐색기**에서 **테이블**을 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다.
10. **새 항목 추가** 대화 상자에서 **Azure Blob**을 선택하고 **추가**를 클릭합니다.   
10. JSON 텍스트를 다음 텍스트로 바꾸고 **AzureBlobLocation1.json** 파일을 저장합니다. 

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

#### 출력 테이블 만들기

11. **솔루션 탐색기**에서 **테이블**을 마우스 오른쪽 단추로 다시 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다.
12. **새 항목 추가** 대화 상자에서 **Azure SQL**을 선택하고 **추가**를 클릭합니다. 
13. JSON 텍스트를 다음 JSON으로 바꾸고 **AzureSqlTableLocation1.json** 파일을 저장합니다.

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

#### 파이프라인 만들기 
지금까지 입출력 연결된 서비스 및 테이블을 만들었습니다. 이제 **복사 작업**으로 파이프라인을 만들어 Azure Blob에서 데이터를 Azure SQL 데이터베이스로 복사합니다.


1. **솔루션 탐색기**에서 **파이프라인**을 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다.  
15. **새 항목 추가** 대화 상자에서 **데이터 파이프라인 복사**를 선택하고 **추가**를 클릭합니다. 
16. JSON을 다음 JSON으로 바꾸고 **CopyActivity1.json** 파일을 저장합니다.
			
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z",
		    "isPaused": false
		  }
		}

#### 데이터 팩터리 엔터티 게시/배포
  
18. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다. 
19. **Microsoft 계정에 로그인** 대화 상자가 표시되면 Azure 구독이 있는 계정의 자격 증명을 입력하고 **로그인**을 클릭합니다.
20. 다음 대화 상자가 표시됩니다.

	![게시 대화 상자](./media/data-factory-get-started-using-vs/publish.png)

21. 데이터 팩터리 구성 페이지에서 다음을 수행합니다.
	1. **새 데이터 팩터리 만들기** 옵션을 선택합니다.
	2. **이름**에 **VSTutorialFactory**를 입력합니다.  
	
		> [AZURE.NOTE]  
		Azure Data Factory 이름은 전역적으로 고유해야 합니다. 게시할 때 데이터 팩터리의 이름에 대한 오류를 받은 경우 데이터 팩터리의 이름(예: yournameVSTutorialFactory)을 변경하고 다시 게시하도록 시도합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
		> 
		> 데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.
	3. **구독** 필드에서 올바른 구독을 선택합니다. 
	4. 생성되는 데이터 팩터리의 **리소스 그룹**을 선택합니다. 
	5. 데이터 팩터리의 **하위 지역**을 선택합니다. 
	6. **다음**을 클릭하여 **항목 게시** 페이지로 전환합니다. 
23. **항목 게시** 페이지에서 모든 데이터 팩터리 엔터티가 선택되었는지 확인하고 **다음**을 클릭하여 **요약** 페이지로 전환합니다.     
24. 요약을 검토한 후 **다음**을 클릭하여 배포 프로세스를 시작하고 **배포 상태**를 봅니다.
25. **배포 상태** 페이지에 배포 프로세스의 상태가 표시됩니다. 배포가 완료되면 마침을 클릭합니다. 


## 서버 탐색기를 사용하여 데이터 팩터리 엔터티를 검토합니다.

1. **Visual Studio**의 메뉴에서 **보기**를 클릭한 다음 **서버 탐색기**를 클릭합니다.
2. 서버 탐색기 창에서 **Azure**를 확장한 다음 **데이터 팩터리**를 확장합니다. **Visual Studio에 로그인**이 표시되면 Azure 구독과 연결된 **계정**을 입력하고 **계속**을 클릭합니다. **암호**를 입력하고 **로그인**을 클릭합니다. Visual Studio에서는 구독에 있는 모든 Azure Data Factory에 대한 정보를 가져오려고 시도합니다. **데이터 팩터리 작업 목록** 창에 이 작업의 상태가 표시됩니다. ![서버 탐색기](./media/data-factory-get-started-using-vs/server-explorer.png)
3. 데이터 팩터리를 마우스 오른쪽 단추로 클릭하고 새 프로젝트로 데이터 팩터리 내보내기를 선택하여 기존 데이터 팩터리에 따라 Visual Studio 프로젝트를 만들 수 있습니다. ![VS 프로젝트로 데이터 팩터리 내보내기](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Visual Studio용 데이터 팩터리 도구 업데이트
Visual Studio용 Azure Data Factory 도구를 업데이트하려면 다음을 수행합니다.

1. 메뉴에서 **도구**를 클릭하고 **확장 및 업데이트**를 선택합니다. 
2. 왼쪽 창에서 **업데이트**를 선택한 다음 **Visual Studio 갤러리**를 선택합니다.
4. **Visual Studio용 Azure Data Factory 도구**를 선택하고 **업데이트**를 클릭합니다. 이 항목이 표시되지 않으면 이미 최신 버전의 도구가 있는 것입니다. 

Azure 포털을 사용하여 이 자습서에서 만든 파이프라인 및 데이터 집합을 모니터링하는 방법에 대한 지침은 [데이터 집합 및 파이프라인 모니터링](data-factory-get-started-using-editor.md#MonitorDataSetsAndPipeline)을 참조하세요.

<!---HONumber=AcomDC_0204_2016-->