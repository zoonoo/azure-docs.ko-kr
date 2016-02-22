
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
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Azure의 Elasticsearch 참고 자료

Elasticsearch은 확장성이 뛰어난 오픈 소스 검색 엔진 및 데이터베이스로서, 빅 데이터 집합에 저장된 정보를 신속하게 분석 및 검색해야 할 때 유용합니다. 이 참고 자료에서는 Elasticsearch 클러스터를 디자인할 때 고려해야 할 몇 가지 주요 측면을 살펴봅니다.

- **[일반 참고 자료][]**에서는 Elasticsearch의 일반적인 구조에 대해 간략히 소개하고 Azure를 사용하여 Elasticsearch 클러스터를 구현하는 방법을 설명합니다.
- **[데이터 수집 참고 자료][]**에서는 높은 비율의 데이터 수집이 예상되는 Elasticsearch 클러스터를 고려할 배포 및 구성 옵션을 설명합니다.
- **[성능 테스트 참고 자료][]**에서는 Elasticsearch 클러스터의 성능을 테스트하기 위해 환경을 설정하는 방법을 설명합니다.
- **[JMeter 참고 자료][]**에서는 데이터를 생성하고 Elasticsearch 클러스터에 업로드할 수 있는 JUnit 샘플러를 만들고 사용하는 방법을 설명합니다.
- **[JMeter에 대한 고려 사항][]**에서는 데이터 수집과 쿼리 테스트 계획의 생성 및 실행을 통해 얻는 주요 경험을 요약합니다. 

  > [AZURE.NOTE] 이 참고 자료에서는 Elasticsearch의 기본 사용에 어느 정도 익숙하다고 가정합니다. 자세한 내용을 보려면 [Elasticsearch 웹 사이트](https://www.elastic.co/products/elasticsearch)를 방문하세요.

[일반 참고 자료]: guidance-elasticsearch.md
[데이터 수집 참고 자료]: guidance-elasticsearch-data-ingestion.md
[성능 테스트 참고 자료]: guidance-elasticsearch-performance-testing-environment.md
[JMeter 참고 자료]: guidance-elasticsearch-implementing-jmeter.md
[JMeter에 대한 고려 사항]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0211_2016-->