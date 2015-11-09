<properties 
	pageTitle="파이프라인 만들기" 
	description="Azure 데이터 팩터리 파이프라인을 이해하며 데이터를 이동하게 만들고 통찰력을 얻는 데 사용할 수 있는 정보를 생성하게 변환하는 방법을 알아봅니다." 
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
	ms.topic="article" y
	ms.date="10/20/2015" 
	ms.author="spelluru"/>

# 파이프라인 및 활동 이해
이 문서는 Azure 데이터 팩터리의 파이프라인 및 시나리오 또는 비즈니스를 위한 활동과 종단 간 데이터 기반 워크플로 활용하는 방법을 이해하는 데 도움이 됩니다. 이 문서는 사용자가 이전의 [개요](data-factory-introduction.md) 및 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 마쳤다고 가정합니다.

## 파이프라인 정의
**파이프라인은 활동의 논리적 그룹화입니다**. 파이프라인은 여러 활동 작업을 수행하는 하나의 단위로 그룹화하기 위해 사용됩니다. 파이프라인을 더 이해하려면 먼저 활동을 이해해야 합니다.

### 활동 정의
활동은 데이터에 수행할 작업을 정의합니다. 각 활동은 0개 이상의 [데이터 집합](data-factory-create-datasets.md)을 입력 항목으로 취하며 하나 이상의 데이터 집합을 출력물로 생성합니다. **활동은 Azure 데이터 팩터리의 오케스트레이션 단위입니다.**

예를 들어 하나의 데이터 집합에서 다른 데이터 집합으로의 데이터 복사를 오케스트레이션하기 위해 복사 활동을 사용할 수 있습니다. 마찬가지로 데이터를 변환하거나 분석하기 위해서 Azure HDInsight 클러스터에서 Hive 쿼리를 실행하는 Hive 활동을 사용할 수 있습니다. Azure 데이터 팩터리는 다양한 [데이터 변환, 분석](data-factory-data-transformation-activities.md) 및 [데이터 이동 활동](data-factory-data-movement-activities.md)을 제공합니다. 또한 사용자 고유의 코드를 실행하려면 사용자 지정 .NET 활동을 만들 수 있습니다.

다음 2개의 데이터 집합을 고려해 보십시오.

**Azure SQL 데이터 집합**

'MyTable' 테이블은 데이터가 데이터베이스에 삽입된 때의 datetime을 지정하는 데 도움이 되는 'timestampcolumn' 열을 포함합니다.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Azure Blob 데이터 집합**

데이터는 시간 세분성으로 특정 날짜와 시간을 반영하는 blob에 대한 경로가 있는 새 blob로 매시간 복사됩니다.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


아래 파이프라인의 복사 작업은 Azure SQL에서 Azure Blob 저장소로 데이터를 복사합니다. Azure SQL 테이블을 시간별 빈도와 함께 입력 데이터 집합으로 인식하고 'AzureBlobOutput' 데이터 집합에서 표시되는 Azure Blob 저장소에 데이터를 작성합니다. 또한 출력 데이터 집합에는 시간별 빈도가 있습니다. 예약 및 실행 섹션을 참조하여 시간 단위를 통해 데이터를 복사하는 방법을 이해합니다. 이 파이프라인에 "2015-01-01T08:00:00"에서 "2015-01-01T11:00:00"까지 3시간의 활성 기간이 있습니다.

**파이프라인:**
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

이제 활동 정의를 간략히 이해했으므로 다시 방문 파이프라인을 방문하겠습니다.
 
파이프라인은 활동의 논리적 그룹화입니다. 파이프라인은 여러 활동 작업을 수행하는 하나의 단위로 그룹화하기 위해 사용됩니다. **또한 파이프라인은 작업에 대한 배포 및 관리 단위입니다.** 예를 들어 논리적으로 관련된 활동을 활성 또는 일시 중지된 상태에 있을 수 있는 하나의 파이프라인으로 함께 배치하려 할 수 있습니다.

파이프라인의 활동에서 출력 데이터 집합은 작업 간의 종속성을 정의하여 동일한/다른 파이프라인에서 다른 활동에 입력 데이터 집합일 수 있습니다. [예약 및 실행](#scheduling-and-execution) 섹션에서 세부 정보를 설명합니다.

Azure 데이터 팩터리에서 파이프라인을 만드는 경우 일반적인 단계입니다.

1.	(생성되지 않은 경우) 데이터 팩터리를 만듭니다.
2.	각 데이터 저장소 또는 계산에 대한 연결된 서비스를 만듭니다.
3.	입력 및 출력 데이터 집합을 만듭니다.
4.	위에 정의된 데이터 집합에서 작동하는 작업으로 파이프라인을 만듭니다.

![데이터 팩터리 엔터티](./media/data-factory-create-pipelines/entities.png)

파이프라인을 정의하는 방법에 대해 자세히 살펴보겠습니다.

## 파이프라인의 분석  

파이프라인에 대한 일반 구조는 다음과 같이 보입니다.

	{
	    "name": "PipelineName",
	    "properties": 
	    {
	        "description" : "pipeline description",
	        "activities":
	        [
	
	        ],
			"start": "<start date-time>",
			"end": "<end date-time>"
	    }
	}

**활동** 섹션에는 내부에서 정의된 하나 이상의 활동이 있을 수 있습니다. 각 활동에는 다음과 같은 최상위 수준 구조가 있습니다.

	{
	    "name": "ActivityName",
	    "description": "description", 
	    "type": "<ActivityType>",
	    "inputs":  "[]",
	    "outputs":  "[]",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties":
	    {
	
	    },
	    "policy":
	    {
	    }
	    "scheduler":
	    {
	    }
	}

다음 테이블은 활동 및 파이프라인 JSON 정의 내에서 속성을 설명합니다.

태그 | 설명 | 필수
--- | ----------- | --------
name | 작업 또는 파이프라인의 이름입니다. 작업 또는 파이프라인을 수행하도록 구성된 작업을 나타내는 이름을 지정합니다<br/><ul><li>최대 문자 수: 260개</li><li>문자 숫자 또는 밑줄(\_)로 시작해야 합니다</li><li>다음 문자는 사용할 수 없습니다. ".", "+","?", "/", "<",">", "*", "%", "&", ":","\"</li></ul>| 예 설명 | 작업 또는 파이프라인의 용도를 설명하는 텍스트 | 예 형식 | 활동의 형식을 지정합니다. 다른 활동의 형식은 [데이터 이동 활동](data-factory-data-movement-activities.md) 및 [데이터 변환 작업](data-factory-data-transformation-activities.md) 문서를 참조하십시오. | 예 입력 | 활동에서 사용하는 테이블을 입력합니다<p>//한 개의 입력 테이블<br/>"입력": [{"name": "inputtable1"}],</p><p>/ 2 개의 입력 테이블 /<br/>"입력": [{"name": "inputtable1"}, {"name": "inputtable2"}],</p>| 예 출력 | 활동에서 사용하는 테이블을 출력합니다.<p>//한 개의 출력 테이블<br/>"출력": [{"name": "outputtable1"}],</p><p>//두 개의 출력 테이블<br/>"출력": [{"name": "outputtable1"}, {"name": "outputtable2"}],</p>| 예 linkedServiceName | 활동에서 사용하는 연결된 서비스의 이름입니다. <p>활동은 필요한 계산 환경에 연결되는 연결된 서비스를 지정할 필요가 있습니다.</p>| HDInsight 활동 및 Azure 기계 학습 일괄 처리 상태 평가 활동에 예<p>다른 모든 사용자에 아니요</p>typeProperties | typeProperties 구역의 속성은 활동의 형식에 따라 달라집니다. 이에 대해 자세히 알아보려면 각 개별 활동에 대한 문서를 참조하십시오 | 정책이 없음 | 활동의 런타임 동작에 영향을 주는 정책입니다. 지정하지 않으면 기본 정책이 사용됩니다. 자세한 내용은 아래로 스크롤합니다 | 시작 없음 | 파이프라인의 시작 날짜-시간입니다. [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)에 있어야 합니다. 예: 2014-10-14T16:32:41Z. <p>시작 및 끝 속성은 함께 파이프라인의 활성 기간을 지정합니다. 출력 조각은 이 활성 기간에만 생성됩니다.</p> | 아니요<p>end 속성에 대한 값을 지정하면 start 속성에 대한 값을 지정해야 합니다.</p><p>파이프라인을 만들 때에는 시작 및 종료 시간을 비워 둘 수 있지만 파이프라인을 실행할 활성 기간을 설정하려면 둘 다 값을 지정해야 합니다. 파이프라인을 만들 때 시작 시간과 종료 시간을 지정하지 않으면 나중에 Set-AzureDataFactoryPipelineActivePeriod cmdlet을 사용하여 설정할 수 있습니다.</p> end | 파이프라인에 대한 종료 날짜-시간입니다. 지정된 경우 ISO 형식에 있어야 합니다. 예를 들어 2014-10-14T17:32:41Z입니다.<p>파이프라인을 무기한으로 실행하려면 end 속성에 대한 값으로 9999-09-09를 지정합니다.</p>| 아니요 <p>start 속성에 대한 값을 지정하면 end 속성에 대한 값을 지정해야 합니다.</p><p>**start** 속성에 대한 설명을 참조하세요.</p> isPaused | true로 설정하면 파이프라인이 실행되지 않습니다. 기본값 = false입니다. 속성을 활성화 또는 비활성화하여 사용할 수 있습니다. | 스케줄러가 아님 | "스케줄러" 속성을 작업에 원하는 예정을 정의하는 데 사용합니다. 하위 속성은 [데이터 집합에서 가용성 속성](data-factory-create-datasets.md#Availability)과 같습니다. | 아니요 | 

### 활동 형식
Azure 데이터 팩터리는 다양한 [데이터 이동](data-factory-data-movement-activities.md) 및 [데이터 변환](data-factory-data-transformation-activities.md) 활동을 제공합니다.

### 정책
정책은 특히 테이블의 조각을 처리할 때 활동의 런타임 동작에 영향을 줍니다. 다음 테이블에서는 자세한 내용을 제공합니다.

속성 | 허용된 값 | 기본값 | 설명
-------- | ----------- | -------------- | ---------------
동시성 | 정수 <p>최대값: 10</p> | 1 | 활동의 동시 실행 수입니다.<p>다른 조각에 발생할 수 있는 병렬 활동 실행 횟수를 결정합니다. 예를 들어 활동이 사용 가능한 많은 데이터 집합을 거쳐야 하는 경우 동시성을 높이면 데이터 처리가 빨라집니다.</p> 
executionPriorityOrder | NewestFirst<p>OldestFirst</p> | OldestFirst | 처리 중인 데이터 조각의 순서를 결정합니다.<p>예를 들어 2개의 조각이 있으며(각각 오후 4시 및 오후 5시에 발생) 둘 다 실행 보류 상태입니다. executionPriorityOrder를 설정하여 NewestFirst이 되도록 하면 오후 5시에 조각이 먼저 처리됩니다. 마찬가지로 executionPriorityORder를 OldestFIrst로 설정하면 오후 4시의 조각이 처리됩니다.</p> 
retry | 정수<p>최대값이 10이 될 수 있음</p> | 3 | 조각에 대한 데이터 처리 전에 다시 시도 횟수가 실패로 표시됩니다. 데이터 조각에 대한 활동 실행은 지정된 재시도 횟수까지 다시 시도됩니다. 재시도는 실패 후 가능한 한 빨리 수행합니다.
시간 제한 | TimeSpan | 00:00:00 | 활동에 대한 시간 제한입니다. 예: 00:10:00(시간 제한 10분을 의미함)<p>값을 지정하지 않거나 0으로 지정하는 경우 시간 제한이 없습니다.</p><p>조각의 데이터 처리 시간이 시간 제한 값을 초과하면 취소되고 시스템이 처리를 다시 시도합니다. 다시 시도 횟수는 다시 시도 속성에 따라 달라집니다. 시간 초과가 발생할 때 상태는 TimedOut입니다.</p>
delay | TimeSpan | 00:00:00 | 조각의 데이터 처리 작업이 시작되기 전에 지연을 지정합니다.<p>예상된 실행 시간 이후까지 지연된 후에 데이터 조각에 대한 활동이 실행되기 시작됩니다.</p><p>예: 00:10:00(10분의 지연을 의미함)</p>
longRetry | 정수<p>최대값: 10</p> | 1 | 조각 실행이 실패하기 전까지의 긴 재시도 횟수입니다.<p>longRetry 시도는 longRetryInterval에 따라 간격이 조정됩니다. 따라서 재시도 간의 시간을 지정해야 하는 경우 longRetry를 사용합니다. Retry 및 longRetry를 둘 다 지정하면 각 longRetry 시도에는 Retry 시도가 포함되고 최대 시도 횟수는 Retry * longRetry가 됩니다.</p><p>예를 들어 활동 정책에 다음 항목이 있는 경우:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/></p><p>실행할 조각이 하나 뿐이며(상태는 PendingExecution) 활동 실행이 매번 실패한다고 가정합니다. 우선 3번 연속 실행 시도를 합니다. 시도한 후에 각 조각 상태는 다시 시도입니다. 처음 3번 시도 후에 조각 상태는 LongRetry입니다.</p><p>한 시간 후에(즉, longRetryInteval의 값) 3번의 연속 실행이 다시 시도됩니다. 그 후에 조각 상태가 실패이면 다시 시도는 더 이상 시도하지 않습니다. 즉, 전체적으로 6번의 시도가 일어납니다.</p><p>참고: 모든 실행이 성공하면 조각 상태가 Ready가 되고 더 이상 다시 시도되지 않습니다.</p><p>longRetry는 종속 데이터가 명확하지 않은 시간에 도착하거나 데이터 처리가 발생하는 전체적인 환경을 신뢰할 수 없는 상황에서 사용될 수 있습니다. 이러한 경우 하나씩 다시 시도를 수행해도 도움이 되지 않을 수 있으며 특정 시간 간격 후에 시도할 경우 출력이 나타납니다.</p><p>주의: longRetry 또는 longRetryInterval에 높은 값을 설정하지 마세요. 일반적으로 더 높은 값을 지정하면 여기에서 무시되는 다른 시스템 문제가 발생합니다.</p> 
longRetryInterval | TimeSpan | 00:00:00 | 긴 다시 시도 간의 지연 

## 파이프라인 제작 및 관리
Azure 데이터 팩터리는 파이프라인을 작성하고 배포하는 다양한 메커니즘을 제공합니다.(차례로 하나 이상의 활동을 포함)

### Azure Preview 포털 사용

1. [Azure Preview 포털](https://portal.azure.com/)에 로그인합니다.
2. 파이프라인을 만들려는 Azure 데이터 팩터리 인스턴스로 이동합니다.
3. **요약** 렌즈의 **작성자 및 배포** 타일을 클릭합니다. 
 
	![작성 및 배포 타일](./media/data-factory-create-pipelines/author-deploy-tile.png)

4. 명령 모음에서 **새 파이프라인**을 클릭합니다.

	![새 파이프라인 단추](./media/data-factory-create-pipelines/new-pipeline-button.png)

5. 파이프라인 JSON 템플릿을 사용하여 편집기 창을 표시합니다.

	![파이프라인 편집기](./media/data-factory-create-pipelines/pipeline-in-editor.png)

6. 파이프라인을 작성을 마친 후에 명령 모음에서 **배포**를 클릭하여 파이프라인을 배포합니다.

	**참고:** 배포하는 동안 Azure 데이터 팩터리 서비스는 유효성 검사를 수행하여 몇 가지 일반적인 문제를 해결하는 데 도움을 줍니다. 오류가 있는 경우 해당 정보가 표시됩니다. 정정 작업을 수행하고 작성된 파이프라인을 다시 배포합니다. 파이프라인을 업데이트 및 삭제하려면 편집기를 사용할 수 있습니다.

### Visual Studio 플러그인 사용
Azure 데이터 팩터리에 파이프라인을 작성하고 배포하려면 Visual Studio를 사용할 수 있습니다. 자세한 내용은 [자습서: Azure 저장소에서 Azure SQL(Visual Studio)로 데이터 복사](data-factory-get-started-using-vs.md)를 참조하세요.

### Azure PowerShell 사용
Azure 데이터 공장에서 파이프라인을 만들려면 Azure PowerShell을 사용할 수 있습니다. 다시 말하면 c:\\DPWikisample.json에 있는 파일에 파이프라인 JSON를 정의했습니다. 다음 예제와 같이 Azure 데이터 팩터리 인스턴스로 업로드할 수 있습니다.

	New-AzureDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

이 cmdlet에 대해 자세히 알아보려면 [New-AzureDataFactoryPipeline cmdlet](https://msdn.microsoft.com/library/dn820227.aspx)을 참조하세요.

### REST API 사용
REST API를 사용하여 파이프라인을 만들고 배포할 수 있습니다. 프로그래밍 방식으로 파이프라인을 만들려면 이 메커니즘을 활용할 수 있습니다. 이에 대한 자세한 내용은 [파이프라인 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/dn906741.aspx)를 참조하세요.

### .NET SDK 사용
.NET SDK를 통해 파이프라인을 만들고 배포할 수 있습니다. 프로그래밍 방식으로 파이프라인을 만들려면 이 메커니즘을 활용할 수 있습니다. 이에 대해 자세히 알아보려면 [프로그래밍 방식으로 데이터 팩터리 만들기, 관리 및 모니터링](data-factory-create-data-factories-programmatically.md)를 참조하세요.


## 예약 및 실행
지금까지 파이프라인 및 활동 정의를 이해했습니다. 또한 Azure 데이터 팩터리에서 정의된 방식 및 활동의 상위 수준을 살펴봅니다. 이제 어떻게 실행되는지를 대해 살펴보겠습니다.

파이프라인은 시작 시간과 종료 시간 사이에서만 활성화됩니다. 시작 시간 이전 또는 종료 시간 이후에 실행되지 않습니다. 파이프라인이 일시 중지된 경우 시작 및 종료 시간에 관계 없이 실행되지 않습니다. 실행될 파이프라인의 경우 일시 중지되지 않아야 합니다.

실제로 실행되는 파이프라인이 아닙니다. 실행되는 파이프라인의 활동입니다. 그러나 파이프라인의 전체적인 맥락에서 이를 수행합니다. Azure 데이터 팩터리에서 예약 및 실행의 작동 방식을 이해하려면 [예약 및 실행](data-factory-scheduling-and-execution.md)을 참조하세요.

## 관리 및 모니터링  
파이프라인을 배포한 후에 파이프라인, 조각 및 실행 프로그램을 관리하고 모니터링할 수 있습니다. 이에 대한 자세한 내용은 [파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md)를 참조하세요.

## 다음 단계

- [Azure 데이터 팩터리에서 예약 및 실행](data-factory-scheduling-and-execution.md)을 이해합니다.  
- Azure 데이터 팩터리에서 [데이터 이동](data-factory-data-movement-activities.md) 및 [데이터 변환 기능](data-factory-data-transformation-activities.md)에 대해 읽어봅니다.
- [Azure 데이터 팩터리에서 관리 및 모니터링](data-factory-monitor-manage-pipelines.md)을 이해합니다.
- [첫 번째 파이프라인을 빌드 및 배포](data-factory-build-your-first-pipeline.md)합니다. 

## 피드백 보내기
이 문서에 대한 의견을 보내주시면 감사하겠습니다. 몇 분 정도 시간을 할애해서 [메일](mailto:adfdocfeedback@microsoft.com?subject=data-factory-create-pipelines.md)을 통해 의견을 보내주세요.
 

   













 
 


 

 

<!---HONumber=Nov15_HO1-->