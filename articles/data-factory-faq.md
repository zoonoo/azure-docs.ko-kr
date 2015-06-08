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
	ms.date="04/23/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 - 질문과 대답

## 일반적인 질문

### 질문: Azure 데이터 팩터리 란 무엇입니까?

데이터 팩터리는 개발자가 데이터 저장소, 이동 및 처리 서비스를 고가용성 및 내결함성이 있는 데이터 파이프라인으로 구성하도록 지원하는 완전히 관리되는 서비스입니다. 데이터 팩터리는 온-프레미스 및 클라우드 데이터 저장소 모두에서 작동합니다. 파이프라인은 데이터 입력, 처리 작업 및 데이터 출력으로 이루어지며, 간단한 JSON 스크립트를 사용하여 정의되고 PowerShell 명령을 통해 활성화됩니다. 활성화되면 데이터 팩터리는 사용자 대신 자동 클러스터 관리를 위한 옵션을 사용하여 HDInsight (Hadoop)에서 실행할 파이프라인을 오케스트레이션하고 예약합니다. 또한 데이터 팩터리는 하나의 대시보드에서 다양한 작동 및 서비스 상태 정보를 사용하여 모든 파이프라인을 모니터링하는 시각적 관리 및 모니터링 환경을 Azure 미리 보기 포털을 통해 제공합니다.
 
### Q: 어떤 고객 챌린지 데이터 팩터리 않습니다 해결?

Azure 데이터 팩터리는 구조화되지 않은 데이터와 함께 기존 관계형 저장소에서 다양한 데이터 저장소, 처리 및 이동 서비스를 활용하는 민첩성과 완전히 관리되는 서비스의 제어 및 모니터링 기능 사이의 균형을 유지합니다.

### Q: 누구 냐 데이터 팩터리에 대 한 대상 그룹?


- Hadoop과 다른 시스템 간의 통합 서비스를 구축하는 데이터 개발자
	- 지속적으로 변경되고 확장되는 데이터 환경에 맞춰 확장하고 통합해야 합니다.
	- 정보 생산을 위한 사용자 지정 코드를 작성해야 하며, 이 경우 비용이 많이 들고, 유지 관리하기 어렵고, 가용성이나 내결함성이 높지 않습니다.

- IT 인프라 내에서 더 다양한 데이터를 통합하는 방법을 찾고 있는 IT 전문가
	- 조직의 모든 데이터를 살펴서 다양한 비즈니스 통찰력을 도출해야 합니다.
	- 온-프레미스와 클라우드 전반에서 비용과 규모에 맞춰 계산 및 저장소 리소스를 관리해야 합니다.
	- 다양한 원본 및 처리를 빠르게 추가하여 새로운 비즈니스 요구를 해결하고 모든 계산 및 저장소 자산의 상태를 지속적으로 파악해야 합니다.

###  Azure 데이터 팩터리에 대 한 세부 정보 q: 가격 어디서 찾을 수 있습니까?

참조 [데이터 팩터리 가격 세부 정보 페이지][adf-pricing-details] Azure 데이터 팩터리에 대 한 가격 정보에 대 한 합니다.

### Q. 시작 하려면 어떻게 얻을 Azure 데이터 팩터리로?

- Azure 데이터 팩터리 개요를 참조 하십시오. [Azure 데이터 팩터리 소개][adf-introduction].
- 간단한 자습서를 참조 하십시오. [Azure 데이터 팩터리 시작][adfgetstarted].
- 포괄적인 설명서를 참조 하십시오. [Azure 데이터 팩터리 설명서][adf-documentation-landingpage].

  
### Q: 어떻게 고객 데이터 팩터리에 액세스 하려면?

고객 데이터 팩터리를 통해에 대 한 액세스를 얻을 수는 [Azure 미리 보기 포털][azure-preview-portal].

### Q: 데이터 팩터리 영역 가용성 란 무엇입니까?

공개 미리 보기에서 데이터 팩터리는 미국 서부에서만 제공됩니다. 데이터 팩터리에서 사용되는 계산 및 저장소 서비스는 다른 지역에 있을 수 있습니다.
 
### Q: 데이터 팩터리/파이프라인/활동/데이터 집합의 수에 대 한 제한을 란 무엇입니까? 


- 구독 내에서 데이터 팩터리 수: 50
- 데이터 팩터리 내에서 파이프라인 수: 100
- 파이프라인 내에서 작업 수: 10
- 데이터 팩터리와 데이터 집합의 수: 100

### 질문: Azure 데이터 팩터리 서비스와 제작 개발자 경험 란 무엇입니까?

다음 중 하나를 사용하여 데이터 팩터리를 작성/생성할 수 있습니다.

- **Azure 미리 보기 포털**. Azure Preview 포털의 데이터 팩터리 블레이드는 데이터 팩터리 및 연결된 서비스를 만들기 위한 풍부한 사용자 인터페이스를 제공합니다.  **데이터 팩터리 편집기**, 이러한 아티팩트에 대 한 JSON 정의 지정 하 여 연결 된 서비스, 테이블, 데이터 집합 및 파이프라인을 쉽게 만들 수 있습니다는 포털의 일부 이기도 합니다. 참조 [데이터 팩터리 편집기][data-factory-editor] 편집기에 대 한 개요 및 [데이터 팩터리 시작][datafactory-getstarted] 포털/편집기를 사용 하 여 만들고 데이터 팩터리 배포의 예입니다.   
- **Azure PowerShell**. PowerShell 사용자이며 포털 UI 대신 PowerShell을 사용하려는 경우 Azure PowerShell의 일부로 제공되는 Azure 데이터 팩터리 cmdlet을 사용하여 데이터 팩터리를 만들고 배포할 수 있습니다. 참조 [만들기 및 Azure PowerShell을 사용 하 여 Azure 데이터 팩터리 모니터][create-data-factory-using-powershell] 는 간단한 예 및 [자습서: 이동 하 고 데이터 팩터리를 사용 하 여 로그 파일을 처리][adf-tutorial] cmdles를 만드는 ad PowerShell을 사용 하는 고급 예에 대 한 데이터 팩터리를 배포 합니다. 참조 [데이터 팩터리 Cmdlet 참조][adf-powershell-reference] 데이터 팩터리 cmdlet의 포괄적인 설명서에 대 한 MSDN 라이브러리의 콘텐츠입니다.  
- **.NET 클래스 라이브러리**. 데이터 팩터리 .NET SDK를 사용하여 프로그래밍 방식으로 데이터 팩터리를 만들 수 있습니다. 참조 [만들기, 모니터링 및 관리.NET SDK를 사용 하 여 데이터 팩터리][create-factory-using-dotnet-sdk] .NET SDK를 사용 하 여 데이터 팩터리 만들기 연습 합니다. 참조 [데이터 팩터리 클래스 라이브러리 참조][msdn-class-library-reference] 에 대 한 데이터 팩터리.NET SDK는 포괄적인 설명서입니다.  
- **REST API**. Azure 데이터 팩터리 서비스에 의해 노출된 REST API를 사용하여 데이터 팩터리를 만들고 배포할 수도 있습니다. 참조 [데이터 팩터리 REST API 참조][msdn-rest-api-reference] 에 대 한 데이터 팩터리 REST API는 포괄적인 설명서입니다. 

## 활동-FAQ
### Q: 지원 되는 데이터 원본 및 활동 어떻게 됩니까?

- **지원 되는 데이터 원본:** Azure 저장소 (Blob 및 테이블), SQL Server, Azure SQL 데이터베이스, 파일 시스템, Oracle 데이터베이스입니다.
- **활동 지원:**: 복사 작업 (온-프레미스, 클라우드 및 온-프레미스 간 클라우드), HDInsight 활동 (Pig, Hive, MapReduce, Hadoop 스트리밍 변환), Azure 컴퓨터 학습 일괄 처리 상태 평가 활동, 저장 프로시저 활동 및 사용자 지정 C# 활동입니다.

### 활동을 실행 하는 경우
 **가용성** 활동이 실행 되 면 출력 데이터 테이블에 대 한 구성 설정을 결정 합니다. 모든 입력된 데이터 종속성 충족 하는지 여부를 검사 하는 활동 (즉, **준비** 상태) 실행을 시작 하기 전에.

## 복사 작업-FAQ
### Q: 영역 복사 작업에서 지원 됩니까?

다음 영역에 데이터를 복사는 복사 작업에서 지 원하는: 미국 동부, 미국 동부 2, 미국 서 부, 중앙 미국, 미국 중 북부, 미국 중남부, 북유럽, 서유럽, 및 동남 아시아.

다른 영역으로 데이터를 복사도 지원 됩니다 데이터 라우팅에 대 한 위의 영역 중 하나를 사용 하 여 합니다. 복사 작업에 대한 요금은 어느 지역을 통해 데이터가 라우팅되는지를 기준으로 청구됩니다.

복사 대상의 영역 | 라우팅에 사용 되는 영역
-------------------------- | -----------------------
동아시아 | 동남아시아
일본 동부 | 미국 서부
일본 서부 | 미국 서부
브라질 남부 | 미국 동부 2

### 여러 출력 테이블에 복사할 수는 방법
다음 예제와 같이 파이프라인에 여러 출력 테이블을 포함할 수 있습니다.

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### 것 보다 잘 여러 작업이 포함 된 파이프라인 나 각 활동에 대 한 별도 파이프라인? 
파이프라인 관련된 활동을 번들로 간주 됩니다. 논리적으로 테이블을 연결 하는 파이프라인의 외부에서 다른 활동에 사용 되지 않는 경우 파이프라인 하나에 활동을 유지할 수 있습니다. 이 이렇게 하지 해야 체인 파이프라인 활성 기간 서로 정렬 됩니다. 또한 파이프라인에 내부 테이블에서 데이터 무결성 더 잘 유지 됩니다 파이프라인을 업데이트할 때. 파이프라인 업데이트 기본적으로 파이프라인 내에서 모든 작업, 관리할 필요가 없어지며, 멈추고으로 다시 만듭니다. 관점을 제작에서 일 수도 쉽게 파이프라인에 대 한 JSON 파일에 관련된 활동 내에서 데이터 흐름을 볼 수 있습니다.

## HDInsight 작업-FAQ

### Q: 영역 HDInsight에서 지원 됩니까?

다음 문서에서 지리적 가용성 섹션을 참조 하십시오: 또는 [HDInsight 가격 정보][hdinsight-supported-regions].

### Q: 영역 주문형 HDInsight 클러스터에서 사용 됩니까?

주문형 HDInsight 클러스터는 클러스터에서 사용하도록 지정한 저장소가 있는 지역과 동일한 지역에 생성됩니다.

### Q: 추가 저장소 계정은 HDInsight 클러스터에 연결 하려면 어떻게 합니까?

사용자 고유의 HDInsight 클러스터 (BYOC-자체 클러스터 가져오기)을 사용 하는 경우 다음 항목을 참조 합니다.

- [HDInsight 클러스터를 사용 하 여 대체 저장소 계정 및 지정][hdinsight-alternate-storage]
- [추가 저장소 계정을 사용 하 여 HDInsight Hive로][hdinsight-alternate-storage-2]

데이터 팩터리 서비스에 의해 만들어지는 주문형 클러스터를 사용 하는 경우 데이터 팩터리 서비스 사용자를 대신해 등록할 수 있도록 서비스는 HDInsight에 대 한 추가 저장소 계정에 연결을 지정 해야 합니다. 요청 시 연결 된 서비스에 대 한 JSON 정의 사용 하 여 **additionalLinkedServiceNames** 다음 JSON 조각에 표시 된 것 처럼 다른 저장소 계정을 지정 하는 속성:
 
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

위의 예에서 otherLinkedServiceName1 및 otherLinkedServiceName2 정의가 HDInsight 클러스터는 대체 저장소 계정에 액세스 하려면 필요한 자격 증명을 포함 하는 연결 된 서비스를 나타냅니다.

## 저장된 프로시저 작업-FAQ
### 저장 프로시저 작업은 데이터 소스를 지원 합니까?
저장 프로시저 작업이 이번에 Azure SQL 데이터베이스를 지원합니다.

## 조각-FAQ

### 분할 영역을 다시 실행할 수는 방법
다음 방법 중 하나에서 조각을 다시 실행할 수 있습니다.

- 클릭 하 여 **실행** 에 명령 모음에서는 **데이터 조각을** 포털에서 조각에 대 한 블레이드 합니다. 
- 실행 **집합 AzureDataFactorySliceStatus** 로 설정 된 상태를 사용 하 여 cmdlet **PendingExecution** 는 조각에 대 한 합니다.   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

참조 [집합 AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] cmdlet에 대 한 세부 정보에 대 한 합니다.

### 얼마나 오래 걸린 조각을 처리할 수 있습니까?
1. 클릭 하 여 **데이터 집합** 타일에 **데이터 팩터리** 팩터리를 데이터에 대 한 블레이드.
2. 특정 데이터 집합에서 클릭 된 **데이터 집합** 블레이드.
3. 관심 있는 조각을 선택할는 **최근 조각** 목록에 **테이블** 블레이드입니다.
4. 실행 하는 활동을 클릭 합니다는 **활동 실행** 목록에 **데이터 조각을** 블레이드입니다. 
5. 클릭 하 여 **속성** 타일에 **작업 실행 세부 정보** 블레이드입니다. 
6. 표시 되어야는 **기간** 값이 있는 필드입니다. Slice를 처리 하는데 소요 된 시간입니다.   

### 실행 중인 슬라이스를 중지 하려면 어떻게 합니까?
사용할 수는 파이프라인에서 실행을 중지 해야하는 경우 [Suspend AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) cmdlet. 현재 파이프라인을 일시 중단 해도 진행 중인 조각 실행 중지 되지 않습니다. 진행 중인 실행 완료 되 면 없는 추가 조각 선택 되었습니다.

실제로 모든 실행을 즉시 중지 하려면 유일한 방법은 파이프라인을 삭제 하 고 다시 만들 하는 것입니다. 파이프라인을 삭제 하려는 경우 테이블 및 파이프라인에 의해 사용 되는 연결 된 서비스를 삭제할 필요가 없습니다.



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

<!---HONumber=GIT-SubDir-->