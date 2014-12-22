<properties title="Scale Azure Stream Analytics jobs" pageTitle="스트림 분석 작업 규모 지정 | Azure" description="Learn how to scale Stream Analytics jobs" metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />

# Azure 스트림 분석 작업 규모 지정 

스트림 분석 작업의 *스트리밍 단위* 계산 방법과 입력 파티션을 구성하고 쿼리 정의를 조정하고 작업 스트리밍 입력을 설정하여 스트림 분석 작업의 크기를 조정하는 방법을 알아봅니다.

Azure 스트림 분석 작업 정의에는 입력, 쿼리 및 출력이 포함됩니다. 입력은 작업이 데이터 스트림을 읽는 위치이고, 출력은 작업이 작업 결과를 전송하는 위치이고, 쿼리는 입력 스트림을 변환하는 데 사용됩니다.  작업에는 하나 이상의 데이터 스트림 입력 소스가 필요합니다. Azure 서비스 버스 이벤트 허브 또는 Azure Blob 저장소가 데이터 스트림 입력 소스가 될 수 있습니다. 자세한 내용은 [Azure 스트림 분석 소개][stream.analytics.introduction], [Azure 스트림 분석 사용 시작][stream.analytics.get.started] 및 [Azure 스트림 분석 개발자 가이드][stream.analytics.developer.guide]를 참조하세요. 

스트림 분석 작업 처리에 사용할 수 있는 리소스는 스트리밍 단위로 측정됩니다. 각 스트리밍 단위는 초당 1MB 처리량까지 제공할 수 있습니다. 각 작업에는 모든 작업의 기본값인 하나의 스트리밍 단위가 적어도 필요합니다. Azure 관리 포털을 사용하여 스트림 분석 작업에 대한 최대 12개의 스트리밍 단위를 설정할 수 있습니다. 각 Azure 구독에는 특정 지역의 모든 작업에 대한 최대 12개의 스트리밍 단위가 있을 수 있습니다. 구독의 스트리밍 단위를 100개 단위까지 늘리려면 [Microsoft 기술 지원 서비스][microsoft.support]에 문의하세요.

작업이 활용할 수 있는 스트리밍 단위 수는 입력에 대한 파티션 구성과 작업에 대해 정의된 쿼리에 따라 결정됩니다. 이 문서에서는 쿼리를 계산하고 조정하여 처리량을 늘리는 방법을 보여 줍니다.

##이 문서에서는 다음을 수행합니다.
+ [작업의 최대 스트리밍 단위 계산](#calculate)
+ [스트림 분석 작업 파티션 구성](#configure)
+ [스트림 분석 작업 성능 모니터링](#monitor)
+ [다음 단계](#nextstep)


##<a name="calculate"></a>작업의 최대 스트리밍 단위 계산
스트림 분석 작업에 사용될 수 있는 스트리밍 단위의 총 수는 작업에 대해 정의된 쿼리의 단계 수와 각 단계에 대한 파티션 수에 따라 결정됩니다.

### 쿼리 단계
하나의 쿼리에는 하나 이상의 단계가 있을 수 있습니다. 각 단계는 WITH 키워드를 사용하여 정의하는 하위 쿼리입니다. WITH 키워드 밖에 있는 유일한 쿼리도 단계로 고려됩니다. 예를 들어 다음 쿼리의 SELECT 문을 살펴보세요.

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId 
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute,3), TollBoothId

앞의 쿼리에는 2개의 단계가 있습니다. 

> [WACOM.NOTE] 이 샘플 쿼리는 이 문서 뒷부분에 설명되어 있습니다.

### 단계 분할

단계를 분할하려면 다음 조건이 필요합니다.

- 입력 소스는 분할해야 합니다. [Azure 스트림 분석 개발자 가이드][stream.analytics.developer.guide] 및 [Azure 이벤트 허브 개발자 가이드][azure.event.hubs.developer.guide]를 참조하세요.
- 쿼리의 SELECT 문은 분할된 입력 소스에서 읽어와야 합니다. 
- 단계 내의 쿼리에는 Partition By 키워드가 있어야 합니다. 

쿼리를 분할되면 입력 이벤트가 처리되고 별도의 파티션 그룹에 집계되며 각 그룹에 대해 출력 이벤트가 생성됩니다. 결합된 집계가 필요한 경우 집계할 또 다른 분할되지 않은 단계를 만들어야 합니다.

Azure 스트림 분석의 미리 보기 릴리스에서는 열 이름별 분할을 지원하지 않습니다. 쿼리에서 기본 제공 필드에 해당하는 PartitionId 필드에 따라서만 분할할 수 있습니다. ParitionId 필드는 이벤트가 발생한 소스 데이터 스트림의 파티션을 나타냅니다.  자세한 내용은 [Azure 스트림 분석 제한 사항 및 알려진 문제][stream.analytics.limitations]을 참조하세요.

### 작업의 최대 스트리밍 단위 계산

하나의 스트림 분석 작업에 대해 분할되지 않은 모든 단계를 최대 6개의 스트리밍 단위로 확장할 수 있습니다. 스트리밍 단위를 더 추가하려면 단계를 분할해야 합니다. 각 파티션에는 6개의 스트리밍 단위가 있을 수 있습니다.

<table border="1">
<tr><th>작업의 쿼리</th><th>작업의 최대 스트리밍 단위</th></td>

<tr><td>
<ul>
<li>쿼리는 한 단계를 포함합니다.</li>
<li>이 단계는 분할되지 않습니다.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>입력 데이터 스트림은 3으로 분할됩니다.</li>
<li>쿼리는 한 단계를 포함합니다.</li>
<li>이 단계는 분할됩니다.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>쿼리는 두 단계를 포함합니다.</li>
<li>두 단계모두 분할되지 않습니다.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>데이터 스트림 입력은 3으로 분할됩니다.</li>
<li>쿼리는 두 단계를 포함합니다.</li>
<li>SELECT 문은 분할된 입력에서 읽어옵니다.</li>
</ul>
</td>
<td>24(분할된 단계에 대한 18 +분할되지 않은 단계에 대한 6)</td></tr>
</table>

###확장의 예
다음 쿼리는 3분의 기간 내에 세 곳의 요금 징수소가 있는 요금 스테이션을 통과하는 자동차 수를 계산합니다. 이 쿼리는 6개 스트리밍 단위까지 확장될 수 있습니다.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

쿼리에 대한 더 많은 스트리밍 단위를 사용하려면 데이터 스트림 입력 및 쿼리를 모두 분할해야 합니다. 데이터 스트림 파티션이 3으로 설정되어 있다고 가정할 경우, 다음의 수정된 쿼리를 최대 18개 스트리밍 단위로 확장할 수 있습니다.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId

쿼리를 분할되면 입력 이벤트가 처리되고 별도의 파티션 그룹에 집계되며 각 그룹에 대해 출력 이벤트가 생성됩니다. 그룹 기준 필드가 데이터 입력 스트림의 파티션 키가 아닌 상태에서 분할을 수행하면 예기치 않은 결과가 발생할 수 있습니다. 예를 들어 이전 샘플 쿼리의 TollBoothId 필드는 Input1의 파티션 키가 아닙니다. tollbooth #1의 데이터는 여러 파티션에서 분산될 수 있습니다. 각 Input1 파티션은 스트림 분석 기능에서 개별적으로 처리되며, 동일한 연속 기간 동안 동일한 요금 징수소를 통과하는 자동차 수에 대해 여러 개의 레코드가 생성됩니다. 입력 파티션 키를 변경할 수 없는 경우 비분할 단계를 추가하여 이 문제를 해결할 수 있습니다. 예를 들면 다음과 같습니다.

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

이 쿼리는 24개 스트리밍 단위까지 확장될 수 있습니다. 

>[WACOM.NOTE] 두 스트림을 조인하는 경우 스트림이 조인을 수행하는 열의 파티션 키별로 분할되고 있으며 두 스트림에 동일한 수의 파티션이 있는지 확인하세요.


##<a name="configure"></a>스트림 분석 작업 파티션 구성

**작업에 대한 스트리밍 단위를 조정하려면**

1. [관리 포털][azure.management.portal]에 로그인합니다.
2. 왼쪽의 **스트림 분석**을 클릭합니다.
3. 크기를 조정할 스트림 분석 작업을 클릭합니다.
4. 페이지 위쪽에서 **크기 조정**을 클릭합니다.

![Azure Stream Analytics configure job scale][img.stream.analytics.configure.scale]


##<a name="monitor"></a>작업 성능 모니터링

관리 포털을 사용하여 작업의 처리량(이벤트 수/초)을 추적할 수 있습니다.

![Azure Stream Analytics monitor jobs][img.stream.analytics.monitor.job]
 
작업의 예상 처리량(이벤트 수/초)을 계산합니다. 처리량이 예상보다 더 작은 경우 입력 파티션을 조정하고, 쿼리를 조정하고, 작업에 스트리밍 단위를 더 추가합니다.

##<a name="nextstep"></a> 다음 단계
이 문서에서는 스트리밍 단위를 계산하는 방법과 스트림 분석 작업의 크기를 조정하는 방법을 배웠습니다. 스트림 분석에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 스트림 분석 소개][stream.analytics.introduction]
- [Azure 스트림 분석을 사용하여 시작][stream.analytics.get.started]
- [Azure 스트림 분석 개발자 가이드][stream.analytics.developer.guide]
- [Azure 스트림 분석 제한 사항 및 알려진 문제][stream.analytics.limitations]
- [Azure 스트림 분석 쿼리 언어 참조][stream.analytics.query.language.reference]
- [Azure 스트림 분석 관리 REST API 참조][stream.analytics.rest.api.reference]

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/ko-kr/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


