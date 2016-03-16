
<properties
   pageTitle="Azure의 Elasticsearch 참고 자료 | Microsoft Azure"
   description="Azure의 Elasticsearch 참고 자료"
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

# Azure의 Elasticsearch 참고 자료

Elasticsearch은 확장성이 뛰어난 오픈 소스 검색 엔진 및 데이터베이스로서, 빅 데이터 집합에 저장된 정보를 신속하게 분석 및 검색해야 할 때 유용합니다. 이 참고 자료에서는 Elasticsearch 클러스터를 디자인할 때 고려해야 할 몇 가지 주요 측면을 살펴봅니다.

- **[Azure에서 Elasticsearch 실행][]**에서는 Elasticsearch의 일반적인 구조에 대해 간략히 소개하고 Azure를 사용하여 Elasticsearch 클러스터를 구현하는 방법을 설명합니다.
- **[Azure에서 Elasticsearch에 대한 데이터 수집 성능 튜닝][]**에서는 높은 비율의 데이터 수집이 예상되는 Elasticsearch 클러스터를 고려할 배포 및 구성 옵션을 설명합니다.
- **[Azure에서 Elasticsearch에 대한 데이터 집계 및 쿼리 성능 튜닝][]**에서는 시스템을 쿼리 및 검색 성능에 최적화하기 위한 가장 좋은 방법을 결정할 때 고려할 수 있는 옵션을 요약합니다.
- **[Azure의 Elasticsearch에서 복원 기능 및 복구 구성][]**에서는 Azure에서 호스트될 때 Elasticsearch에서 사용 가능한 복원 기능 및 복구 옵션을 요약합니다.
- **[Azure에서 Elasticsearch에 대한 성능 테스트 환경 만들기][]**에서는 Elasticsearch 클러스터의 성능을 테스트하기 위해 환경을 설정하는 방법을 설명합니다.
- **[Elasticsearch에 대한 JMeter 테스트 계획 구현][]**에서는 데이터를 만들어 Elasticsearch 클러스터에 업로드할 수 있는 JUnit 샘플러를 생성 및 사용하는 방법을 설명합니다.
- **[Elasticsearch 성능을 테스트하기 위한 JMeter JUnit 샘플러 배포][]**에서는 데이터 수집과 쿼리 테스트 계획의 생성 및 실행을 통해 얻는 주요 경험을 요약합니다. 
- **[자동화된 Elasticsearch 복원력 테스트 실행][]**에서는 위 문서에서 사용된 복원력 테스트 실행을 요약합니다.
- **[자동화된 Elasticsearch 성능 테스트 실행][]**에서는 위 문서에서 사용된 성능 테스트 실행을 요약합니다.

> [AZURE.NOTE] 이 참고 자료에서는 Elasticsearch의 기본 사용에 어느 정도 익숙하다고 가정합니다. 자세한 내용을 보려면 [Elasticsearch 웹 사이트](https://www.elastic.co/products/elasticsearch)를 방문하세요.

[Azure에서 Elasticsearch 실행]: guidance-elasticsearch-running-on-azure.md
[Azure에서 Elasticsearch에 대한 데이터 수집 성능 튜닝]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Azure에서 Elasticsearch에 대한 성능 테스트 환경 만들기]: guidance-elasticsearch-creating-performance-testing-environment.md
[Elasticsearch에 대한 JMeter 테스트 계획 구현]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Elasticsearch 성능을 테스트하기 위한 JMeter JUnit 샘플러 배포]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Azure에서 Elasticsearch에 대한 데이터 집계 및 쿼리 성능 튜닝]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Azure의 Elasticsearch에서 복원 기능 및 복구 구성]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[자동화된 Elasticsearch 복원력 테스트 실행]: guidance-elasticsearch-running-automated-resilience-tests.md
[자동화된 Elasticsearch 성능 테스트 실행]: guidance-elasticsearch-running-automated-performance-tests.md

<!---HONumber=AcomDC_0224_2016-->