
<properties
   pageTitle="자동화된 Elasticsearch 성능 테스트 실행 | Microsoft Azure"
   description="고유한 환경에서 성능 테스트를 실행할 수 있는 방법에 대한 설명입니다."
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
   
# 자동화된 Elasticsearch 성능 테스트 실행

이 문서는 [시리즈의 일부](guidance-elasticsearch.md)입니다.

[Azure에서 Elasticsearch에 대한 데이터 수집 성능 튜닝] 및 [Azure에서 Elasticsearch에 대한 데이터 집계 및 쿼리 성능 튜닝] 문서는 샘플 Elasticsearch 클러스터에 대해 실행된 다양한 성능 테스트를 설명합니다.

이러한 테스트는 자동화된 방식으로 실행될 수 있도록 스크립팅되었습니다. 이 문서에서는 고유한 환경에서 테스트를 반복할 수 있는 방법을 설명합니다.

## 필수 조건

자동화된 테스트에는 다음 항목이 필요합니다.

-  Elasticsearch 클러스터.

- [Azure에서 Elasticsearch에 대한 성능 테스트 환경 만들기] 문서에 설명된 대로 JMeter 환경 설치.

- JMeter Master VM에만 다음 소프트웨어 설치.

    [Python 3.5.1](https://www.python.org/downloads/release/python-351/)


## 테스트 원리
테스트는 JMeter를 사용하여 실행됩니다. JMeter 마스터 서버는 테스트 계획을 로드하고 실제로 테스트를 실행하는 JMeter 하위 서버의 집합에 전달합니다. JMeter 마스터 서버는 JMeter 하위 서버를 조정하고 결과를 누적합니다.

다음과 같은 테스트 계획이 제공됩니다.

* [elasticsearchautotestplan3nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan3nodes.jmx). 이 테스트 계획은 3개 노드 클러스터를 통해 수집 테스트를 실행합니다.

* [elasticsearchautotestplan6nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodes.jmx). 이 테스트 계획은 6개 노드 클러스터를 통해 수집 테스트를 실행합니다.

* [elasticsearchautotestplan6qnodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6qnodes.jmx). 이 테스트 계획은 6개 노드 클러스터를 통해 수집 및 쿼리 테스트를 실행합니다.

* [elasticsearchautotestplan6nodesqueryonly.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodesqueryonly.jmx). 이 테스트 계획은 6개 노드 클러스터를 통해 쿼리 전용 테스트를 실행합니다.


노드가 적거나 많이 필요한 경우 고유한 시나리오에 대한 기준으로 테스트 계획을 사용할 수 있습니다.

테스트 계획은 JUnit 요청 샘플러를 사용하여 테스트 데이터를 생성하고 업로드합니다. JMeter 테스트 계획은 이 샘플러를 만들고 실행하며 성능 데이터에 대한 Elasticsearch 노드 각각을 모니터링합니다. 자세한 내용은 [Azure에서 Elasticsearch에 대한 데이터 수집 성능 튜닝] 및 [Azure에서 Elasticsearch에 대한 데이터 집계 및 쿼리 성능 튜닝] 문서에 대한 부록을 참조하세요.

## JUnit JAR 및 종속성 빌드 및 배포
복원력 테스트를 실행하기 전에 성능/junitcode 폴더에 있는 JUnit 테스트를 다운로드하고 컴파일하며 배포해야 합니다. 이러한 테스트는 JMeter 테스트 계획에서 참조됩니다. 자세한 내용은 [Elasticsearch 성능을 테스트하기 위해 JMeter JUnit 샘플러 배포] 문서에서 기존 JUnit 테스트 프로젝트를 Eclipse에 가져오는 절차를 참조하세요.

두 가지 버전의 JUnit 테스트가 있습니다. - [Elasticsearch1.73](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch1.73). 수집 테스트를 수행하기 위해 이 코드를 사용합니다. 이러한 테스트는 Elasticsearch 1.73을 사용합니다. - [Elasticsearch2](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch2). 쿼리 테스트를 수행하기 위해 이 코드를 사용합니다. 이러한 테스트는 Elasticsearch 2.1 이상을 사용합니다.

JMeter 컴퓨터에 종속성의 나머지 부분과 함께 적절한 JAR 파일을 복사합니다. 과정은 [Elasticsearch 성능을 테스트하기 위한 JMeter JUnit 샘플러 배포][]에 설명됩니다.

> **중요** JUnit 테스트를 배포한 후에 JMeter를 사용하여 이 JUnit 테스트 참조하는 테스트 계획을 로드하고 구성하며 *BulkInsertLarge* 스레드 그룹이 올바른 JAR 파일, JUnit 클래스 이름 및 테스트 메서드를 참조하도록 합니다.
> 
> ![](./media/guidance-elasticsearch/performance-tests-image1.png)
> 
> 테스트를 실행하기 전에 업데이트된 테스트 계획을 저장합니다.

## 테스트 인덱스 만들기
테스트를 실행 하는 경우 각 테스트는 지정된 단일 인덱스에 대한 수집 및/또는 쿼리를 수행합니다. [Azure에서 Elasticsearch에 대한 데이터 수집 성능 튜닝] 및 [Azure에서 Elasticsearch에 대한 데이터 집계 및 쿼리 성능 튜닝] 문서에 부록에서 설명한 스키마를 사용하여 인덱스를 만들고 테스트 시나리오에 따라 구성해야 합니다(doc 값 사용/사용 안 함, 여러 복제본 등). 테스트 계획은 인덱스가 *ctip*라는 단일 형식을 구성한다고 가정합니다.

## 테스트 스크립트 매개 변수 구성
JMeter 서버 컴퓨터에 다음 테스트 스크립트 매개 변수 파일을 복사합니다.

* [run.properties](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/run.properties). 이 파일은 사용할 JMeter 테스트 스레드의 수, 테스트 기간(초), Elasticsearch 클러스터에서 노드의 IP 주소(또는 부하 분산 장치) 및 클러스터의 이름을 지정합니다.

  ```ini
  nthreads=3
  duration=300
  elasticip=<IP Address or DNS Name Here>
  clustername=<Cluster Name Here>
  ```
  
  이 파일을 편집하고 테스트 및 클러스터에 대한 적절한 값을 지정합니다.

* [query-config-win.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-win.ini) 및 [query-config-nix.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-nix.ini). 이 두 파일에는 다음과 같은 동일한 정보가 있습니다. *win* 파일은 Windows 파일 이름 및 경로 대한 형식이고 *nix* 파일은 Linux 파일 이름 및 경로에 대한 형식입니다.

  ```ini
  [DEFAULT]
  debug=true #true가 콘솔 로그를 표시하는 경우

  [실행]
  pathreports=C:\\Users\\administrator1\\jmeter\\test-results\\ #path where tests results are saved.
  jmx=C:\\Users\\administrator1\\testplan.jmx #JMeter 테스트 계획에 대한 경로
  machines=10.0.0.1,10.0.02,10.0.0.3 #쉼표로 구분된 Elasticsearch 데이터 노드의 IP.
  reports=aggr,err,tps,waitio,cpu,network,disk,response,view #쉼표로 구분된 보고서의 이름. tests=idx1 #테스트할 Elasticsearch 인덱스 이름.
  properties=run.properties #속성 파일의 이름.
  ```

  이 파일을 편집하여 테스트 결과의 위치, 실행할 JMeter 테스트 계획의 이름, Elasticsearch 데이터 노드의 IP 주소, 생성될 원시 성능 데이터를 포함하는 보고서 및 테스트 중인 인덱스의 이름(또는 이름들)을 지정합니다. *run.properties* 파일이 다른 폴더 또는 디렉터리에 위치한 경우 이 파일에 전체 경로를 지정합니다.

## 테스트 실행

* *run.properties* 및 *query-config-win.ini*(*query-config-nix.ini*) 파일과 동일한 폴더에서 [query-test.py](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-test.py) 파일을 JMeter 서버 컴퓨터에 복사합니다.

* *jmeter.bat*(Windows) 또는 *jmeter.sh*(Linux)가 환경에 대한 실행 가능한 파일 경로에 있는지 확인합니다.

* 명령줄에서 *query-test.py* 스크립트를 실행하여 테스트를 수행합니다.

  ```cmd
  py query-test.py
  ```

* 테스트가 완료되면 결과는 *query-config-win.ini*(*query-config-nix.ini*) 파일에 지정된 CSV 파일의 집합으로 저장됩니다. Excel을 사용하여 이 데이터를 분석하고 그래프로 만들 수 있습니다.


[Azure에서 Elasticsearch에 대한 데이터 수집 성능 튜닝]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Azure에서 Elasticsearch에 대한 데이터 집계 및 쿼리 성능 튜닝]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Azure에서 Elasticsearch에 대한 성능 테스트 환경 만들기]: guidance-elasticsearch-creating-performance-testing-environment.md
[Elasticsearch 성능을 테스트하기 위한 JMeter JUnit 샘플러 배포]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Elasticsearch 성능을 테스트하기 위해 JMeter JUnit 샘플러 배포]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->
