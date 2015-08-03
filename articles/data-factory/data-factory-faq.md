<properties 
	pageTitle="Azure 데이터 팩터리 - 질문과 대답" 
	description="Azure 데이터 팩터리에 대한 질문과 대답입니다." 
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
	ms.date="06/16/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 - 질문과 대답

## 일반적인 질문

### Q: Azure 데이터 팩터리란 무엇인가요?

데이터 팩터리는 개발자가 데이터 저장소, 이동 및 처리 서비스를 고가용성 및 내결함성이 있는 데이터 파이프라인으로 구성하도록 지원하는 완전히 관리되는 서비스입니다. 데이터 팩터리는 온-프레미스 및 클라우드 데이터 저장소 모두에서 작동합니다. 파이프라인은 데이터 입력, 처리 작업 및 데이터 출력으로 이루어지며, 간단한 JSON 스크립트를 사용하여 정의되고 PowerShell 명령을 통해 활성화됩니다. 활성화되면 데이터 팩터리는 사용자 대신 자동 클러스터 관리를 위한 옵션을 사용하여 HDInsight (Hadoop)에서 실행할 파이프라인을 오케스트레이션하고 예약합니다. 또한 데이터 팩터리는 하나의 대시보드에서 다양한 작동 및 서비스 상태 정보를 사용하여 모든 파이프라인을 모니터링하는 시각적 관리 및 모니터링 환경을 Azure 미리 보기 포털을 통해 제공합니다.
 
### Q: 데이터 팩터리로 해결되는 고객 문제는 무엇인가요?

Azure 데이터 팩터리는 구조화되지 않은 데이터와 함께 기존 관계형 저장소에서 다양한 데이터 저장소, 처리 및 이동 서비스를 활용하는 민첩성과 완전히 관리되는 서비스의 제어 및 모니터링 기능 사이의 균형을 유지합니다.

### Q: 데이터 팩터리의 대상 고객층은 누구인가요?


- Hadoop과 다른 시스템 간의 통합 서비스를 구축하는 데이터 개발자
	- 지속적으로 변경되고 확장되는 데이터 환경에 맞춰 확장하고 통합해야 합니다.
	- 정보 생산을 위한 사용자 지정 코드를 작성해야 하며, 이 경우 비용이 많이 들고, 유지 관리하기 어렵고, 가용성이나 내결함성이 높지 않습니다.

- IT 인프라 내에서 더 다양한 데이터를 통합하는 방법을 찾고 있는 IT 전문가
	- 조직의 모든 데이터를 살펴서 다양한 비즈니스 통찰력을 도출해야 합니다.
	- 온-프레미스와 클라우드 전반에서 비용과 규모에 맞춰 계산 및 저장소 리소스를 관리해야 합니다.
	- 다양한 원본 및 처리를 빠르게 추가하여 새로운 비즈니스 요구를 해결하고 모든 계산 및 저장소 자산의 상태를 지속적으로 파악해야 합니다.

###  Q: Azure 데이터 팩터리에 대한 가격 정보는 어디서 찾을 수 있나요?

Azure 데이터 팩터리에 대한 가격 정보는 [데이터 팩터리 가격 정보 페이지][adf-pricing-details]를 참조하세요.

### Q. Azure 데이터 팩터리를 시작하려면 어떻게 해야 하나요?

- Azure 데이터 팩터리에 대한 개요는 [Azure 데이터 팩터리 소개][adf-introduction]를 참조하세요.
- 간략한 자습서는 [Azure 데이터 팩터리 시작][adfgetstarted]을 참조하세요.
- 자세한 설명서는 [Azure 데이터 팩터리 설명서][adf-documentation-landingpage]를 참조하세요.

  
### Q: 고객이 데이터 팩터리에 액세스하려면 어떻게 해야 하나요?

고객은 [Azure Preview 포털][azure-preview-portal]을 통해 데이터 팩터리에 액세스할 수 있습니다.

### Q: 데이터 팩터리의 지역 가용성은 얼마나 되나요?

공개 미리 보기에서 데이터 팩터리는 미국 서부에서만 제공됩니다. 데이터 팩터리에서 사용되는 계산 및 저장소 서비스는 다른 지역에 있을 수 있습니다.
 
### Q: 데이터 팩터리/파이프라인/작업/데이터 집합의 수에 대한 제한은 어떻게 되나요? 


- 구독 내의 데이터 팩터리 수: 50
- 데이터 팩터리 내의 파이프라인 수: 100
- 파이프라인 내의 작업 수: 10
- 데이터 팩터리 내의 데이터 집합 수: 100

### Q: Azure 데이터 팩터리 서비스를 사용한 제작/개발자 환경이란 무엇인가요?

다음 중 하나를 사용하여 데이터 팩터리를 작성/생성할 수 있습니다.

- **Azure Preview 포털**. Azure Preview 포털의 데이터 팩터리 블레이드는 데이터 팩터리 및 연결된 서비스를 만들기 위한 풍부한 사용자 인터페이스를 제공합니다. 포털에 포함된 **데이터 팩터리 편집기**에서 이러한 아티팩트에 대한 JSON 정의를 지정하여 연결된 서비스, 테이블, 데이터 집합 및 파이프라인을 쉽게 만들 수 있습니다. 편집기 개요는 [데이터 팩터리 편집기][data-factory-editor]를 참조하고, 포털/편집기를 사용하여 데이터 팩터리를 만들고 배포하는 예제는 [데이터 팩터리 시작][datafactory-getstarted]을 참조하세요.   
- **Azure PowerShell**. PowerShell 사용자이며 포털 UI 대신 PowerShell을 사용하려는 경우 Azure PowerShell의 일부로 제공되는 Azure 데이터 팩터리 cmdlet을 사용하여 데이터 팩터리를 만들고 배포할 수 있습니다. 간단한 예제는 [Azure PowerShell을 사용하여 Azure 데이터 팩터리 만들기 및 모니터링][create-data-factory-using-powershell]을 참조하고, PowerShell cmdlet을 사용하여 데이터 팩터리를 만들고 배포하는 고급 예제는 [자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial]를 참조하세요. 데이터 팩터리 cmdlet의 포괄적인 설명서는 MSDN 라이브러리의 [데이터 팩터리 Cmdlet 참조][adf-powershell-reference] 콘텐츠를 참조하세요.  
- **.NET 클래스 라이브러리**. 데이터 팩터리 .NET SDK를 사용하여 프로그래밍 방식으로 데이터 팩터리를 만들 수 있습니다. .NET SDK를 사용하여 데이터 팩터리를 만드는 연습은 [.NET SDK를 사용하여 데이터 팩터리 만들기, 모니터링 및 관리][create-factory-using-dotnet-sdk]를 참조하세요. 데이터 팩터리 .NET SDK의 포괄적인 설명서는 [데이터 팩터리 클래스 라이브러리 참조][msdn-class-library-reference]를 참조하세요.  
- **REST API**. Azure 데이터 팩터리 서비스에 의해 노출된 REST API를 사용하여 데이터 팩터리를 만들고 배포할 수도 있습니다. 데이터 팩터리 REST API의 포괄적인 설명서는 [데이터 팩터리 REST API 참조][msdn-rest-api-reference]를 참조하세요. 

### Q: 데이터 팩터리의 이름을 바꿀 수 있습니까?
아니요. 다른 Azure 리소스와 마찬가지로 Azure 데이터 팩터리의 이름을 변경할 수 없습니다.

## 작업 - FAQ
### Q: 지원되는 데이터 원본 및 작업은 무엇인가요?

- **지원 되는 데이터 원본:** 
	- Azure 저장소(Blob 및 테이블)
	- Azure SQL
	- Azure DocumentDB
	- 온-프레미스 SQL Server
	- 온-프레미스 Oracle 
	- 온-프레미스 파일 시스템
	- 온-프레미스 MySQL
	- 온-프레미스 DB2
	- 온-프레미스 Teradata
	- 온-프레미스 Sybase
	- 온-프레미스 PostgreSQL  
- **지원되는 작업:**: 
	- 복사 작업(온-프레미스를 클라우드에, 클라우드를 온-프레미스로)
	- HDInsight 작업(Pig, Hive, MapReduce, Hadoop 스트리밍 변환)
	- Azure 기계 학습 배치 평가 작업
	- Azure SQL 저장 프로시저 작업
	- 사용자 지정 .NET 작업

### 작업은 언제 실행되나요?
출력 데이터 테이블의 **가용성** 구성 설정에 따라 작업 실행 시기가 결정됩니다. 작업은 실행을 시작하기 전에 모든 입력 데이터 종속성이 충족되었는지(즉, **Ready** 상태) 검사합니다.

## 복사 작업 - FAQ
### Q: 복사 작업에서 지원하는 지역은 어디인가요?

복사 작업은 미국 동부, 미국 동부 2, 미국 서부, 미국 중부, 미국 중북부, 미국 중남부, 북유럽, 서유럽 및 동남 아시아, 일본 동부 및 브라징 남부 지역으로 데이터 복사를 지원합니다.

위의 지역 중 하나를 사용하여 데이터를 라우팅하면 다른 지역으로도 데이터를 복사할 수 있습니다. 복사 작업에 대한 요금은 어느 지역을 통해 데이터가 라우팅되는지를 기준으로 청구됩니다.

복사 대상의 지역 | 라우팅에 사용되는 지역
-------------------------- | -----------------------
동아시아 | 동남아시아
일본 서부 | 일본 동부

### 어떻게 여러 출력 테이블로 복사할 수 있나요?
다음 예제와 같이 파이프라인에 여러 개의 출력 테이블이 있을 수 있습니다.

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### 여러 작업이 포함된 파이프라인 1개보다 각 작업에 개별 파이프라인을 사용하는 것이 더 효율적인가요? 
파이프라인은 관련 작업의 번들로 간주됩니다. 논리적으로, 작업을 연결하는 테이블이 파이프라인 외부의 다른 작업에서 사용 되지 않는 경우 파이프라인 하나에 작업을 유지할 수 있습니다. 이 경우 서로 정렬되도록 파이프라인 활성 기간을 연결할 필요가 없습니다. 또한 파이프라인을 업데이트할 때 파이프라인 내부 테이블의 데이터 무결성이 보다 완벽하게 유지됩니다. 파이프라인 업데이트는 기본적으로 파이프라인 내의 모든 작업을 중지하고 제거한 후 다시 만듭니다. 제작 관점에서는 파이프라인에 대한 하나의 JSON 파일에서 관련 작업 내의 데이터 흐름을 확인하는 것이 더 쉬울 수도 있습니다.

## HDInsight 작업 - FAQ

### Q: HDInsight에서 지원하는 지역은 어디인가요?

다음 문서의 지리적 가용성 섹션 또는 [HDInsight 가격 정보][hdinsight-supported-regions]를 참조하세요.

### Q: 주문형 HDInsight 클러스터가 사용되는 지역은 어디인가요?

주문형 HDInsight 클러스터는 클러스터에서 사용하도록 지정한 저장소가 있는 지역과 동일한 지역에 생성됩니다.

### Q: 추가 저장소 계정을 HDInsight 클러스터에 연결하려면 어떻게 해야 하나요?

사용자 고유의 HDInsight 클러스터를 사용하는 경우(BYOC - 자체 클러스터 가져오기) 다음 항목을 참조하세요.

- [대체 저장소 계정 및 메타스토어와 HDInsight 클러스터 사용][hdinsight-alternate-storage]
- [HDInsight Hive와 추가 저장소 계정 사용][hdinsight-alternate-storage-2]

데이터 팩터리 서비스에서 만든 주문형 클러스터를 사용하는 경우 데이터 팩터리 서비스가 사용자를 대신해서 등록할 수 있도록 HDInsight 연결된 서비스에 대한 추가 저장소 계정을 지정해야 합니다. 주문형 연결된 서비스에 대한 JSON 정의에서, 다음 JSON 조각과 같이 **additionalLinkedServiceNames** 속성을 사용하여 대체 저장소 계정을 지정합니다.
 
	{
	    "name": "MyHDInsightOnDemandLinkedService",
	    "properties":
	    {
	        "type": "HDInsightOnDemandLinkedService",
	        "clusterSize": 1,
	        "timeToLive": "00:01:00",
	        "linkedServiceName": "LinkedService-SampleData",
	        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
	    }
	} 

위의 예제에서 otherLinkedServiceName1 및 otherLinkedServiceName2는 HDInsight 클러스터가 대체 저장소 계정에 액세스하는 데 필요한 자격 증명이 해당 정의에 포함된 연결된 서비스를 나타냅니다.

## 저장 프로시저 작업 - FAQ
### 저장 프로시저 작업은 어떤 데이터 원본을 지원하나요?
현재 저장 프로시저 작업은 Azure SQL 데이터베이스만 지원합니다.

## 조각 - FAQ

### 어떻게 조각을 다시 실행할 수 있나요?
다음 방법 중 하나로 조각을 다시 실행할 수 있습니다.

- 포털에서 조각의 **데이터 조각** 블레이드에 대해 명령 모음의 **실행**을 클릭합니다. 
- 조각의 Status를 **PendingExecution**으로 설정하여 **Set-AzureDataFactorySliceStatus** cmdlet을 실행합니다.   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

cmdlet에 대한 자세한 내용은 [Set-AzureDataFactorySliceStatus][set-azure-datafactory-slice-status]를 참조하세요.

### 조각을 처리하는 데 얼마나 오래 걸렸나요?
1. 데이터 팩터리의 **데이터 팩터리** 블레이드에서 **데이터 집합** 타일을 클릭합니다.
2. **데이터 집합** 블레이드에서 특정 데이터 집합을 클릭합니다.
3. **테이블** 블레이드의 **최근 조각** 목록에서 관심 있는 조각을 선택합니다.
4. **데이터 조각** 블레이드의 **작업 실행** 목록에서 작업 실행을 클릭합니다. 
5. **작업 실행 세부 정보** 블레이드에서 **속성** 타일을 클릭합니다. 
6. **기간** 필드와 값이 표시됩니다. 이것이 조각을 처리하는 데 소요된 시간입니다.   

### 실행 중인 조각을 중지하려면 어떻게 해야 하나요?
파이프라인 실행을 중지해야 하는 경우 [Suspend-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) cmdlet을 사용할 수 있습니다. 현재, 파이프라인을 일시 중단해도 진행 중인 조각 실행은 중지되지 않습니다. 진행 중인 실행이 완료되면 추가 조각이 선택되지 않습니다.

모든 실행을 즉시 중지하려면 파이프라인을 삭제하고 다시 만들어야 합니다. 파이프라인을 삭제하도록 선택하는 경우 파이프라인에서 사용되는 테이블 및 연결된 서비스를 삭제할 필요는 없습니다.



[image-rerun-slice]: ./media/data-factory-faq/rerun-slice.png

[adfgetstarted]: data-factory-get-started.md
[adf-introduction]: data-factory-introduction.md
[adf-troubleshoot]: data-factory-troubleshoot.md
[data-factory-editor]: data-factory-editor.md
[datafactory-getstarted]: data-factory-get-started.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/azure/dn835095.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 

<!---HONumber=July15_HO4-->