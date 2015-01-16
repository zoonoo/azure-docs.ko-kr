<properties title="Azure Stream Analytics developer guide" pageTitle="스트림 분석 개발자 가이드 | Azure" description="Azure 스트림 분석 응용 프로그램을 개발하는 방법에 대해 알아봅니다." metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />


# Azure 스트림 분석 개발자 가이드 

[이 설명서는 시험판 설명서이며 향후 릴리스에서 변경될 수 있습니다.] 

Azure 스트림 분석은 완전히 관리되는 서비스로, 클라우드의 스트리밍 데이터에 대해 대기 시간이 짧고 확장성이 뛰어난 고가용성의 복합 이벤트 처리 기능을 제공합니다. 시험판 릴리스에서 스트림 분석 기능은 고객이 데이터 스트림을 분석하도록 스트리밍 작업을 설정하고 거의 실시간으로 분석할 수 있도록 해 줍니다.  

스트림 분석의 대상 시나리오:

- 고용량 및 고속 데이터에 대해 복합 이벤트 처리 수행   
- 커넥티드 카 또는 유틸리티 그리드와 같은 전역으로 분산된 자산이나 장비에서 이벤트 데이터 수집 
- 거의 실시간으로 진행되는 모니터링 및 진단을 위해 원격 분석 데이터 처리 
- 향후 처리를 위해 실시간 이벤트 캡처 및 보관

자세한 내용은 [Azure 스트림 분석 소개][stream.analytics.introduction]를 참조하세요. 

스트림 분석 작업은 하나 이상의 입력 소스, 들어오는 스트림 데이터에 대한 쿼리 및 출력 대상으로 정의됩니다.  


##이 문서에서는 다음을 수행합니다.

+ [입력](#inputs) 
+ [연결](#query)
+ [출력](#output)
+ [작업 규모 지정](#scale)
+ [작업 모니터링 및 문제 해결](#monitor)
+ [작업 관리](#manage)
+ [다음 단계](#nextsteps)



##<a name="inputs"></a>입력

### 데이터 스트림

각 스트림 분석 작업 정의는 작업에서 사용되고 변환될 하나 이상의 데이터 스트림 입력 소스를 포함해야 합니다.  [Azure Blob 저장소][azure.blob.storage] 및 [Azure 서비스 버스 이벤트 허브][azure.event.hubs]는 데이터 스트림 입력된 소스로 지원됩니다.  이벤트 허브 입력 소스는 여러 다른 장치 및 서비스에서 이벤트 스트림을 수집하는 데 사용되며, Blob 저장소는 많은 양의 데이터를 수집하기 위한 입력 소스로 사용될 수 있습니다.  Blob은 데이터를 스트리밍하지 않으므로 Blob의 레코드에 타임스탬프가 포함되지 않은 한, Blob을 통한 스트림 분석 작업은 본질적으로 일시적이지 않습니다.

### 참조 데이터

스트림 분석 기능은 또 다른 유형의 입력 소스인 참조 데이터도 지원합니다.  이 데이터는 상관 관계를 지정하고 조회를 수행하는 데 사용되는 보조 데이터로 일반적으로 정적이며 자주 변경되지 않습니다.  미리 보기 릴리스에서 Blob 저장소는 유일하게 지원되는 참조 데이터용 입력 소스입니다.

### 직렬화
쿼리가 올바르게 작동하려면 스트림 분석 기능에서 들어오는 데이터 스트림에 사용되는 serialization 형식을 인식해야 합니다. 현재 지원되는 형식은 스트리밍 데이터의 경우 JSON, CSV 및 Avro이고, 참조 데이터의 경우 CSV입니다.

### 생성된 속성
작업에 사용되는 입력 형식에 따라 일부 추가 필드가 이벤트 메타데이터와 함께 생성됩니다.  다른 입력 열과 마찬가지로 이러한 필드를 쿼리할 수 있습니다.  기존 이벤트에 아래의 속성 중 하나와 이름이 같은 필드가 있으면 입력 메타데이터로 덮어쓰입니다.

<table border="1">
	<tr>
		<th></th>
		<th>속성</th>
		<th>설명</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>Blob</strong></td>
		<td>BlobName</td>
		<td>이 이벤트가 발생된 입력 blob의 이름</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>Blob 레코드가 처리된 날짜 및 시간</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>Blob이 마지막으로 수정된 날짜 및 시간</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>입력 어댑터의 0부터 시작하는 파티션 ID</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>이벤트 허브</b></td>
		<td>EventProcessedUtcTime</td>
		<td>이벤트가 처리된 날짜 및 시간</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>이벤트 허브에서 이벤트를 받은 날짜 및 시간</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>입력 어댑터의 0부터 시작하는 파티션 ID</td>
	</tr>
</table>



###추가 리소스
입력 소스를 만드는 방법에 대한 자세한 내용은 [Azure 이벤트 허브 개발자 가이드][azure.event.hubs.developer.guide] 및 [Azure Blob 저장소 사용][azure.blob.storage.use]을 참조하세요.  



##<a name="query"></a>연결
들어오는 데이터를 필터링, 조작 및 처리하기 위한 논리는 스트림 분석 쿼리 작업에 정의됩니다.  쿼리는 임시 쿼리를 위한 일부 특정 확장이 있는 표준 T-SQL 구문에 주로 속하는 SQL 유사 언어인 스트림 분석 쿼리 언어를 사용하여 작성됩니다.

###기간 이동
기간 이동 확장을 통해 특정 기간에 속하는 이벤트 하위 집합을 집계하고 계산할 수 있습니다. 기간 이동 함수는 GROUP BY 문을 사용하여 호출됩니다. 예를 들어 다음 쿼리는 초당 받은 이벤트 개수를 계산합니다. 

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

###실행 단계
좀 더 복잡한 쿼리의 경우 표준 SQL 절 WITH를 사용하여 임시로 명명된 결과 집합을 지정할 수 있습니다.  예를 들어 다음 쿼리는 WITH를 사용하여 두 실행 단계를 통해 변환합니다.
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

쿼리 언어에 대한 자세한 내용은 [Azure 스트림 분석 쿼리 언어 참조][stream.analytics.query.language.reference]를 참조하세요. 

##<a name="output"></a>출력
출력 소스는 스트림 분석 작업의 결과를 쓸 위치입니다. 결과는 작업이 입력 이벤트를 처리할 때 출력 소스에 계속 기록됩니다.  다음 출력 소스가 지원됩니다.

- Azure 서비스 버스 이벤트 허브: 장치로 다시 명령을 실행하는 경우처럼 여러 스트리밍 파이프라인을 함께 구성해야 하는 시나리오에 대한 출력 소스로 이벤트 허브를 선택합니다.
- Azure 저장소 Blob: 출력을 장기간 보관하거나 나중에 처리하기 위해 데이터를 저장하는 데 Blob 저장소를 사용합니다.
- Azure SQL 데이터베이스: 이 출력 소스는 원래 관계형인 데이터나 데이터베이스에 호스트되고 있는 콘텐츠를 필요로 하는 응용 프로그램에 적합합니다.


##<a name="scale"></a>작업 규모 지정

스트림 분석 작업은 작업이 수신하는 처리 능력의 크기를 정의하는 스트리밍 단위를 구성하여 확장할 수 있습니다. 각 스트리밍 단위는 대략 1MB/초의 처리량에 해당합니다. 각 구독에서 해당 지역의 작업에는 지역당 12개 스트리밍 단위 할당량이 배정되어야 합니다.

자세한 내용은 [Azure 스트림 분석 작업 규모 지정][stream.analytics.scale.jobs]을 참조하세요.


##<a name="monitor"></a>작업 모니터링 및 문제 해결

###지역별 모니터링 저장소 계정

스트림 분석에서 작업 모니터링을 사용하려면 스트림 분석 작업을 포함하는 각 지역의 데이터를 모니터링을 위한 Azure 저장소 계정을 지정해야 합니다.  이 계정은 작업을 만들 때 구성됩니다.  

###메트릭
다음 메트릭은 스트림 분석 작업의 사용 현황 및 성능을 모니터링하는 데 사용할 수 있습니다.

- 들어오는 처리량: 이벤트 개수를 기준으로 스트림 분석 작업이 받은 데이터의 양
- 보내는 처리량: 이벤트 개수를 기준으로 스트림 분석 작업이 출력 소스로 전송한 데이터의 양
- 오류 수: 스트림 분석 작업에 의해 발생한 오류 메시지의 수

###작업 로그
스트림 분석 작업을 디버깅하거나 문제를 해결하는 가장 좋은 방법은 Azure 작업 로그를 사용하는 것입니다.  작업 로그는 포털의 관리 서비스 섹션에서 액세스할 수 있습니다.  작업 로그를 검사하려면 서비스 유형을 "스트림 분석"으로 설정하고 서비스 이름을 작업의 이름으로 설정합니다.


##<a name="manage"></a>작업 관리 

###작업 시작 및 중지
미리 보기 릴리스의 스트림 분석에서 작업을 중지하면 작업에 마지막으로 사용된 이벤트에 대한 상태가 전혀 보존되지 않습니다.  따라서 중지된 작업을 다시 시작해도 이벤트가 삭제되거나 데이터가 중복될 수 있습니다.  작업을 일시적으로 중지해야 할 경우 출력을 검사하고 마지막 레코드의 삽입 시간을 사용하여 작업이 중지되었을 때를 대략적으로 계산하는 것이 가장 좋은 방법입니다.  그런 후 작업이 다시 시작될 때 구성 탭의 출력 시작 설정에서 이 시간을 지정합니다.
이 시간은 일시적인 제한이며 향후 릴리스에서는 데이터 손실 없이 작업 시작 및 중지를 설정하는 방식이 최우선적으로 고려될 것입니다.  

###작업 구성
스트림 분석 작업에 대해 다음과 같은 최상위 수준 설정을 조정할 수 있습니다.

- 출력 시작: 이 작업이 결과 출력을 생성할 때를 지정합니다. 연결된 쿼리에 기간이 포함된 경우 작업은 지정된 시간에 첫 번째 출력 이벤트를 생성하기 위해 필요한 기간이 시작될 때 입력 소스에서 입력을 받기 시작합니다. 작업 시작 시간과 사용자 지정의 두 가지 옵션을 사용할 수 있습니다. 기본 설정은 작업 시작 시간입니다. 사용자 지정 옵션의 경우 날짜와 시간을 지정해야 합니다. 이 설정은 입력 소스에서 사용할 기록 데이터 양을 지정하거나 작업이 마지막으로 중지되었을 때와 같은 특정 시간부터 데이터 수집을 시작하려는 경우에 유용합니다. 
- 순서 비지정 관련 정책: 순차적으로 스트림 분석 작업에 도착하지 않는 이벤트 처리를 위한 설정입니다. 시간 임계값을 지정하여 허용 오차 기간에 포함된 이벤트의 순서를 다시 지정하고 이 기간을 벗어나는 이벤트에 대해 삭제 또는 조정 중 수행할 조치를 결정할 수 있습니다.  삭제를 선택하면 순서가 정해지지 않은 모든 이벤트가 삭제되고, 조정을 선택하면 순서가 정해지지 않은 이벤트의 System.Timestamp가 가장 최근에 수신된 순서가 지정된 이벤트의 타임스탬프로 변경됩니다.  
- 로캘: 이 설정을 사용하여 스트림 분석 작업에 대한 국제화 기본 설정을 지정할 수 있습니다. 데이터의 타임스탬프는 로캘과 관련이 없지만 여기에서 설정한 내용은 작업이 데이터를 구문 분석하고, 비교하고, 정렬하는 방식에 영향을 줍니다. 미리 보기 릴리스의 경우 en-US만 지원됩니다.


##<a name="support"></a>지원 받기
추가 지원에 대해서는[Azure 스트림 분석 포럼][stream.analytics.forum]을 참조하세요. 


##<a name="nextsteps"></a>다음 단계

- [Azure 스트림 분석 소개][stream.analytics.introduction]
- [Azure 스트림 분석을 사용하여 시작][stream.analytics.get.started]
- [Azure 스트림 분석 작업 규모 지정][stream.analytics.scale.jobs]
- [Azure 스트림 분석 제한 사항 및 알려진 문제][stream.analytics.limitations]
- [Azure 스트림 분석 쿼리 언어 참조][stream.analytics.query.language.reference]
- [Azure 스트림 분석 관리 REST API 참조][stream.analytics.rest.api.reference] 



<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/ko-kr/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/ko-kr/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/ko-kr/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/ko-kr/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
