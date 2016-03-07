<properties
   pageTitle="Elasticsearch에 대한 JMeter 테스트 계획 구현 | Microsoft Azure"
   description="JMeter를 사용하여 Elasticsearch에 대한 성능 테스트를 실행하는 방법입니다."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Elasticsearch에 대한 JMeter 테스트 계획 구현

이 문서는 [시리즈의 일부](guidance-elasticsearch.md)입니다.

Elasticsearch에 대해 수행된 성능 테스트는 클러스터로 데이터 업로드 등의 작업을 수행하기 위해 JUnit 테스트로 통합된 Java 코드와 함께 JMeter 테스트 계획을 사용하여 구현되었습니다. 테스트 계획 및 JUnit 코드는 [Azure에서 Elasticsearch에 대한 데이터 수집 성능 튜닝][] 및 [Azure에서 Elasticsearch에 대한 데이터 집계 및 쿼리 성능 튜닝][]에 설명되어 있습니다.

이 문서의 목적은 이러한 테스트 계획을 생성 및 실행함으로써 얻을 수 있는 주요 경험을 요약하는 것입니다. Apache JMeter 웹 사이트의 [JMeter 모범 사례](http://jmeter.apache.org/usermanual/best-practices.html) 페이지에는 효과적인 JMeter 사용에 대한 보다 일반적인 조언이 포함되어 있습니다.

## JMeter 테스트 계획 구현

다음 목록에서는 JMeter 테스트 계획을 만들 때 고려해야 하는 항목을 요약합니다.

- 수행하려는 각 테스트에 대해 별도의 스레드 그룹을 만듭니다. 테스트는 논리 컨트롤러, 타이머, 사전 및 사후 프로세서, 샘플러 및 수신기로 구성된 여러 단계로 구성될 수 있습니다.

- 한 스레드 그룹에 너무 많은 스레드를 만들지 마세요. 스레드 수가 너무 많으면 "메모리 부족" 예외와 함께 JMeter가 실패합니다. 단일 JMeter 서버에서 많은 수의 스레드를 실행하는 것보다 적은 수의 스레드를 실행하는 JMeter 하위 서버를 더 많이 추가하는 것이 좋습니다.

![](./media/guidance-elasticsearch/jmeter-testing1.png)

- 클러스터의 성능을 평가하려면 [Perfmon Metrics Collector](http://jmeter-plugins.org/wiki/PerfMon/)(성능 모니터링 메트릭 수집기) 플러그 인을 테스트 계획에 통합합니다. 이 플러그 인은 표준 JMeter 표준 플러그 인 중 하나로 사용할 수 있는 JMeter 수신기입니다. 원시 성능 데이터를 CSV 형식의 파일 집합에 저장한 다음 테스트가 완료되면 처리합니다. 이렇게 하면 데이터를 캡처할 때마다 처리하는 것보다 더 효율적이고 JMeter의 부담이 줄어듭니다. 

Excel 등의 도구를 사용하여 데이터를 가져오고 분석을 위해 많은 그래프를 생성할 수 있습니다.

![](./media/guidance-elasticsearch/jmeter-testing2.png)

다음 정보를 캡처하는 것이 좋습니다.

- Elasticsearch 클러스터의 모든 노드에 대한 CPU 사용률입니다.

- 모든 노드에 대해 디스크에서 초당 읽은 바이트 수입니다.

- 가능한 경우 각 노드에서 I/O가 수행될 때까지 기다린 CPU 시간의 백분율입니다. Windows VM의 경우에는 불가능할 수도 있지만 Linux의 경우 다음 셸 명령을 실행하여 노드에서 *vmstat*를 호출하는 사용자 지정 메트릭(EXEC 메트릭)을 만들 수 있습니다.

```Shell
sh:-c:vmstat 1 5 | awk 'BEGIN { line=0;total=0;}{line=line+1;if(line&gt;1){total=total+\$16;}}END{print total/4}'
```

*vmstat* 출력의 필드 16에는 I/O 대기에 소요된 CPU 시간이 포함됩니다. 이 문의 사용 방법에 대한 자세한 내용은 [vmstat 명령](http://linuxcommand.org/man_pages/vmstat8.html)을 참조하세요.

- 네트워크를 통해 각 노드로 보내고 받은 바이트 수입니다.

- 개별 집계 보고서 수신기를 사용하여 성공 및 실패한 작업의 빈도와 성능을 기록합니다. 성공 및 실패 데이터를 각각 다른 파일에 수집합니다.

![](./media/guidance-elasticsearch/jmeter-testing3.png)

- 성능과 특정 테스트 작업을 직접 상호 연관지을 수 있도록 각 JMeter 테스트 사례는 되도록 단순하게 유지합니다. 복잡한 논리가 필요한 테스트 사례의 경우 이 논리를 JUnit 테스트에 캡슐화하고 JMeter의 JUnit 요청 샘플러를 사용하여 테스트를 실행합니다.

- HTTP 요청 샘플러를 사용하여 GET, POST, PUT 또는 DELETE 등의 HTTP 작업을 수행합니다. 예를 들어 POST 쿼리를 사용하고 *본문 데이터* 상자에 쿼리 세부 정보를 제공하여 Elasticsearch 검색을 실행할 수 있습니다.

![](./media/guidance-elasticsearch/jmeter-testing4.png)

- 쉽게 반복 및 재사용할 수 있도록 테스트 JMeter 테스트 계획을 매개 변수화합니다. 그런 다음 스크립팅을 사용하여 테스트 계획의 실행을 자동화할 수 있습니다.

## JUnit 테스트 구현

하나 이상의 JUnit 테스트를 만들어 복잡한 코드를 한 JMeter 테스트 계획에 통합할 수 있습니다. Eclipse와 같은 Java IDE를 사용하여 JUnit 테스트를 작성할 수 있습니다. [Elasticsearch 성능을 테스트하기 위한 JMeter JUnit 샘플러 배포][]에서는 적절한 배포 환경을 설정하는 방법에 대한 정보를 제공합니다.

다음 목록에서는 JUnit 테스트를 위해 코드를 작성할 때 따라야 하는 몇 가지 모범 사례에 대해 요약합니다.

- 초기화 매개 변수를 테스트에 전달하려면 테스트 클래스 생성자를 사용합니다. JMeter는 단일 문자열 인수를 사용하는 생성자를 사용할 수 있습니다. 다음 코드 예제에서처럼 생성자에서 이 인수를 해당 개별 요소로 구문 분석합니다.

```Java
private String hostName = "";
private String indexName = "";
private String typeName = "";
private int port = 0;
private String clusterName = "";
private int itemsPerBatch = 0;

/* JUnit test class constructor */
public ElasticsearchLoadTest2(String params) {
	/* params is a string containing a set of comma separated values for:
		hostName
		indexName
		typeName
		port
		clustername
		itemsPerBatch
	*/

    /* Parse the parameter string into an array of string items */
	String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
	String[] items = params.split(delims);

    /* Note: Parameter validation code omitted */

	/* Use the parameters to populate variables used by the test */
	hostName = items[0];
	indexName = items[1];
	typeName = items[2];
	port = Integer.parseInt(items[3]);
	clusterName = items[4];
	itemsPerBatch = Integer.parseInt(items[5]);

	if(itemsPerBatch == 0)
		itemsPerBatch = 1000;
}
```

- JUnit 테스트가 실행될 때마다 실행되므로 생성자 또는 설정 테스트 클래스에서는 비용이 많이 드는 작업 또는 I/O 작업을 수행하지 마세요. JMeter에서 실행되는 각 성능 테스트에 대해 동일한 JUnit 테스트를 수천 번 실행할 수 있습니다.

- 비용이 많이 드는 테스트 사례를 초기화하려면 일회 설정을 사용하는 것이 좋습니다.

- 테스트에 많은 수의 입력 매개 변수가 필요한 경우 별도의 구성 파일에 테스트 구성 정보를 저장하고 이 파일의 위치를 생성자에 전달합니다.

- 로드 테스트 코드에 파일 경로를 하드 코딩하지 마세요. 이 경우 Windows와 Linux 등의 운영 체제 간에 차이가 발생하여 실패할 수 있습니다.

- JMeter를 사용하여 추적하고 비즈니스 메트릭으로 사용할 수 있도록 어설션을 사용하여 JUnit에 테스트 방법의 실패를 나타냅니다. 가능한 경우 다음 코드 예제에서 굵은 글꼴로 표시된 것처럼 실패의 원인과 관련된 정보를 전달합니다.

```Java
@Test
public void bulkInsertTest() throws IOException {
	...
	BulkResponse bulkResponse = bulkRequest.execute().actionGet();
	assertFalse(
		bulkResponse.buildFailureMessage(), bulkResponse.hasFailures());
		...
}
```

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Azure에서 Elasticsearch에 대한 데이터 수집 성능 튜닝]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Elasticsearch 성능을 테스트하기 위한 JMeter JUnit 샘플러 배포]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Azure에서 Elasticsearch에 대한 데이터 집계 및 쿼리 성능 튜닝]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md

<!---HONumber=AcomDC_0224_2016-->