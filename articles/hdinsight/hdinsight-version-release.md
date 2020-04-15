---
title: HDInsight 4.0 개요 - Azure
description: HDInsight 3.6을 HDInsight 4.0 기능, 제한 사항 및 업그레이드 권장 사항과 비교합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d0fd9999abc4a67ded0f66977e1a3ba5310c87be
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383041"
---
# <a name="azure-hdinsight-40-overview"></a>Azure HDInsight 4.0 개요

Azure HDInsight는 아파치 하두롭과 아파치 스파크에 대한 기업 고객 들 사이에서 가장 인기있는 서비스 중 하나입니다. HDInsight 4.0은 아파치 하두롭 구성 요소의 클라우드 분포입니다. 이 문서에서는 가장 최근 Azure HDInsight 릴리스 및 업그레이드 방법에 대한 정보를 제공합니다.

## <a name="whats-new-in-hdinsight-40"></a>HDInsight 4.0의 새로운 기능

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>아파치 하이브 3.0 및 낮은 대기 시간 분석 처리

아파치 하이브(Apache Hive) 낮은 대기 시간 분석 처리(LLAP)는 영구 쿼리 서버와 메모리 내 캐싱을 사용합니다. 이 프로세스는 원격 클라우드 저장소의 데이터에 대한 빠른 SQL 쿼리 결과를 제공합니다. Hive LLAP는 Hive 쿼리의 조각을 실행하는 영구 데몬 집합을 사용합니다. LLAP에서 쿼리 실행은 컨테이너 대신 LLAP 디먼 내에서 실행 중인 작업자 태스크를 사용하는 LLAP 없는 Hive와 비슷합니다.

Hive LLAP의 이점은 다음과 같습니다.

* 성능과 적응성을 저하시키지 않으면서 심층적인 SQL 분석을 수행할 수 있습니다. 복잡한 조인, 하위 쿼리, 창 함수, 정렬, 사용자 정의 함수 및 복잡한 집계와 같은.

* 분석 처리를 위해 스토리지에서 다른 엔진으로 데이터를 이동할 필요 없는 데이터가 준비되는 동일한 스토리지의 데이터에 대한 대화형 쿼리

* 쿼리 결과를 캐싱하면 이전에 계산된 쿼리 결과를 다시 사용할 수 있습니다. 이 캐시는 쿼리에 필요한 클러스터 작업을 실행하는 데 소요된 시간과 리소스를 절약합니다.

### <a name="hive-dynamic-materialized-views"></a>Hive 동적 구체화된 뷰

이제 Hive는 동적 구체화된 뷰 또는 관련 요약의 사전 계산을 지원합니다. 뷰는 데이터 웨어하우스에서 쿼리 처리를 가속화합니다. 구체화된 뷰를 Hive에서 고유하게 저장할 수 있으며 LLAP 가속을 원활하게 사용할 수 있습니다.

### <a name="hive-transactional-tables"></a>Hive 트랜잭션 테이블

HDI 4.0에는 아파치 하이브 3이 포함되어 있습니다. Hive 3에는 Hive 웨어하우스에 있는 트랜잭션 테이블에 대한 원자성, 일관성, 격리 및 내구성 준수가 필요합니다. ACID 준수 테이블 및 테이블 데이터는 Hive에서 액세스 및 관리됩니다. CRUD(제거, 검색, 업데이트 및 삭제) 테이블의 데이터는 ORC(최적화된 행 열) 파일 형식이어야 합니다. 삽입 전용 테이블은 모든 파일 형식을 지원합니다.

* ACID v2에는 스토리지 형식과 실행 엔진에 성능 향상이 있습니다.

* ACID는 기본적으로 활성화되어 데이터 업데이트에 대한 전체 지원을 허용합니다.

* 향상된 ACID 기능을 통해 행 수준에서 업데이트 및 삭제할 수 있습니다.

* 성능 오버 헤드가 없습니다.

* 필요한 버킷팅이 없습니다.

* Spark는 Hive 웨어하우스 커넥터를 통해 Hive ACID 테이블을 읽고 쓸 수 있습니다.

[Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html)에 대해 자세히 알아봅니다.

### <a name="apache-spark"></a>Apache Spark

Apache Spark는 Hive Warehouse Connector를 사용하여 업데이트할 수 있는 테이블 및 ACID 트랜잭션을 가져옵니다. Hive Warehouse Connector를 사용하면 Spark에서 외부 테이블로 Hive 트랜잭션 테이블을 등록하여 전체 트랜잭션 기능에 액세스할 수 있습니다. 이전 버전은 테이블 파티션 조작에서만 지원됩니다. 하이브 웨어하우스 커넥터는 스트리밍 데이터프레임도 지원합니다.  이 프로세스는 Spark에서 트랜잭션 및 스트리밍 Hive 테이블로 읽고 씁니다.

Spark 실행기는 Hive LLAP 디먼에 직접 연결하여 Hive에서 데이터의 제어를 유지하도록 트랜잭션 방식으로 데이터를 검색하고 업데이트할 수 있습니다.

HDInsight 4.0에서 Apache Spark는 다음 시나리오를 지원합니다.

* 보고에 사용되는 동일한 트랜잭션 테이블을 통해 기계 학습 모델 교육을 실행합니다.
* ACID 트랜잭션을 사용하여 Spark ML에서 Hive 테이블로 열을 안전하게 추가합니다.
* Hive 스트리밍 테이블의 변경 피드에서 Spark 스트리밍 작업을 실행합니다.
* Spark Structured Streaming 작업에서 직접 ORC 파일을 만듭니다.

더 이상 Spark에서 직접 Hive 트랜잭션 테이블에 액세스하려고 실수로 걱정할 필요가 없습니다. 일관되지 않은 결과, 중복 된 데이터 또는 데이터 손상이 발생합니다. HDInsight 4.0에서 Spark 테이블 및 Hive 테이블은 별도 Metastore에 보관됩니다. Hive Data Warehouse Connector를 사용하여 Spark 외부 테이블로 Hive 트랜잭션 테이블을 명시적으로 등록합니다.

[Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html)에 대해 자세히 알아봅니다.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1은 다음 변경 내용으로 HDI 4.0에 포함됩니다.

* Oozie는 더 이상 Hive 작업을 실행하지 않습니다. Hive CLI는 제거되었으며 BeeLine으로 대체되었습니다.

* **job.properties** 파일에 제외 패턴을 포함하여 공유 lib에서 원치 않는 종속성을 제외할 수 있습니다.

[Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html)에 대해 자세히 알아봅니다.

## <a name="how-to-upgrade-to-hdinsight-40"></a>HDInsight 4.0으로 업그레이드하는 방법

프로덕션 환경에서 최신 버전을 구현하기 전에 구성 요소를 철저히 테스트합니다. HDInsight 4.0은 업그레이드 프로세스를 시작할 수 있습니다. HDInsight 3.6은 우발적인 사고를 방지하기 위한 기본 옵션입니다.

이전 버전의 HDInsight에서 HDInsight 4.0으로 업그레이드되는 경로는 지원되지 않습니다. 메타스토어 및 Blob 데이터 형식이 변경되었므로 4.0은 이전 버전과 호환되지 않습니다. 새로운 HDInsight 4.0 환경을 현재 프로덕션 환경과 별도로 유지하는 것이 중요합니다. HDInsight 4.0을 현재 환경에 배포하면 메타스토어가 영구적으로 업그레이드됩니다.  

## <a name="limitations"></a>제한 사항

* HDInsight 4.0은 아파치 하이브에 대한 맵Reduce를 지원하지 않습니다. 대신 Apache Tez를 사용하세요. [Apache Tez](https://tez.apache.org/)에 대해 자세히 알아봅니다.
* HDInsight 4.0은 아파치 스톰을 지원하지 않습니다.
* Hive 보기는 HDInsight 4.0에서 더 이상 사용할 수 없습니다.
* 아파치 제플린의 쉘 인터프리터는 스파크 및 대화형 쿼리 클러스터에서 지원되지 않습니다.
* LLAP는 Spark-LLAP 클러스터에서 *사용하지 않도록 설정*할 수 없으며, LLAP만 끌 수 있습니다.
* Azure 데이터 레이크 스토리지 Gen2는 Spark 클러스터에 Jupyter 노트북을 저장할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight 설명서](index.yml)
* [릴리스 정보](hdinsight-release-notes.md)
