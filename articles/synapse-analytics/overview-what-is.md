---
title: Azure Synapse Analytics란?
description: Azure Synapse Analytics 개요
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: be124bdde4427113d56f44f0c1fa19c600bd768e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496053"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Azure Synapse Analytics(작업 영역 미리 보기)란?

[!INCLUDE [preview](includes/note-preview.md)]

오늘날 엔터프라이즈 분석에서는 원시 데이터, 정제된 데이터 또는 고도로 선별된 데이터를 막론하고 모든 종류의 데이터를 대규모로 운영해야 합니다. 과거에 이런 종류의 분석 솔루션을 구축하려면 엔터프라이즈에서 빅 데이터와 데이터 웨어하우징 기술(예: Spark 및 SQL)을 결합해야만 했습니다. 그런 다음, 관계형 저장소와 Data Lake 전체 데이터에 작동하는 풍부한 데이터 파이프라인에 통합해야 했습니다.  

이러한 솔루션은 구축, 구성, 보안 및 유지 관리가 어렵기 때문에 인텔리전트 인사이트를 신속하게 추출하기 까지 시간이 지체되었습니다.

**Azure Synapse**는 통합 분석 서비스로써, 데이터 웨어하우스와 빅 데이터 분석 시스템 전체에서 모든 규모의 모든 데이터에 대한 인사이트를 얻는 시간을 앞당길 수 있는 서비스입니다. 엔터프라이즈 데이터 웨어하우징에 사용되는 최고의 **SQL** 기술, 빅 데이터 분석에 사용되는 **Spark** 기술 및 **파이프라인**을 결합하여 활동 및 데이터 이동을 조정합니다.

Azure Synapse에는 관리, 모니터링, 코딩 및 보안을 위한 단일 환경과 모델을 제공하는 웹 네이티브 **Studio** 사용자 환경이 제공됩니다.

Azure Synapse는 기업에서 가장 익숙한 분석 방식을 사용하여 모든 규모의 데이터에 대한 인사이트를 모을 수 있는 가장 쉽고 빠른 방법을 제공합니다. **Power BI**와 긴밀히 통합되기 때문에, 데이터 엔지니어는 엔드투엔드로 작동하여 비즈니스 인텔리전스를 제공하는 분석 솔루션을 구축할 수 있습니다.

또한, Azure Synapse를 사용하면 **AzureML**에 기본 제공되는 지원을 통해 기계 학습을 사용하여 예측 모델 구축 및 고급 분석이 가능합니다.

## <a name="key-features--benefits"></a>주요 기능 및 이점

### <a name="industry-leading-sql"></a>업계 최고의 SQL

* **Synapse SQL**은 기업에서 데이터 엔지니어에게 친숙한 표준 T-SQL 환경을 사용하여 데이터 웨어하우징 및 데이터 가상화 시나리오를 구현할 수 있는 분산 쿼리 시스템입니다. 또한 SQL의 기능을 확장하여 스트리밍 및 기계 학습 시나리오를 처리합니다.

* Synapse SQL은 **서버리스** 및 **프로비저닝된** 리소스 모델을 모두 제공하여 필요에 맞는 소비 및 청구 옵션을 제공합니다. 예측 가능한 성능 및 비용을 위해, 풀을 프로비저닝하여 SQL 테이블에 저장된 데이터를 위한 처리 성능을 예약합니다. 계획하지 않은 워크로드나 버스티 워크로드에는 항상 사용 가능한 서버리스 SQL 엔드포인트를 사용합니다.
* 기본 제공 **스트리밍** 기능을 사용하여 클라우드 데이터 원본의 데이터를 SQL 테이블로 이동합니다.
* [T-SQL PREDICT 함수](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)를 통해 데이터의 점수를 매기는 **기계 학습** 모델을 사용하여 AI와 SQL을 통합합니다.

### <a name="industry-standard-apache-spark"></a>업계 표준 Apache Spark

**Azure Synapse용 Apache Spark**는 데이터 준비, 데이터 엔지니어링, ETL 및 기계 학습에 사용되는 가장 인기있는 오픈 소스 빅 데이터 엔진인 Apache Spark를 긴밀하고 원활하게 통합합니다.

* Linux Foundation Delta Lake가 기본적으로 지원되는 Apache Spark 2.4용 AzureML 통합 및 SparkML 알고리즘이 포함된 ML 모델
* 클러스터 관리에 대해 걱정할 필요가 없는 간소화된 리소스 모델
* 신속한 Spark 시작 및 적극적인 자동 크기 조정
* Spark 애플리케이션 내에서 기존 .NET 코드와 C# 전문 지식을 활용할 수 있도록 .NET for Spark 기본 지원

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Data Lake에서 SQL과 Apache Spark 상호 운용

Azure Synapse는 SQL과 Spark를 함께 사용하는 기존의 기술 장벽을 제거합니다. 사용자의 요구 사항과 전문 지식에 맞게 원활하게 조합하여 사용할 수 있습니다.

* 공유 Hive 호환 메타데이터 시스템을 통해 Data Lake의 파일에 정의된 테이블을 Spark 또는 Hive에서 원활하게 사용할 수 있습니다.
* SQL과 Spark는 데이터 레이크에 저장된 Parquet, CSV, TSV 및 JSON 파일을 직접 검색하고 분석할 수 있습니다.
* SQL과 Spark 데이터베이스 간 데이터 이동을 위한 빠른고 확장성 있는 로드 및 언로드

### <a name="built-in-orchestration-via-pipelines"></a>파이프라인을 통한 기본 제공 오케스트레이션

Azure Synapse에는 Azure Data Factory와 동일한 데이터 통합 엔진과 환경이 기본으로 제공되기 때문에 별도의 오케스트레이션 엔진을 사용하지 않고도 풍부한 데이터 파이프라인을 만들 수 있습니다.

* Azure Synapse와 90개가 넘는 온-프레미스 데이터 원본 간에 데이터 이동
* 노트북, 파이프라인, Spark 작업, SQL 스크립트, 저장 프로시저 오케스트레이션
* 데이터 흐름 활동을 사용하는 코드 없는 ETL

### <a name="unified-management-monitoring-and-security"></a>통합 관리, 모니터링 및 보안

Azure Synapse는 엔터프라이즈에서 분석 리소스를 관리하고, 사용 및 활동을 모니터링하며, 보안을 강화할 수 있는 단일 방법을 제공합니다.

* 사용자를 역할에 할당하여 분석 리소스에 대한 액세스 간소화
* 데이터와 코드에 대한 세분화된 액세스 제어
* SQL과 Spark 전체에서 리소스, 사용 및 사용자를 모니터링할 수 있는 단일 대시보드

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio**는 데이터 엔지니어를 위해 모든 기능을 하나로 묶은 웹 네이티브 환경이기 때문에 완벽한 솔루션을 구축하는 데 필요한 모든 작업을 한 곳에서 수행할 수 있습니다.

* 엔드투엔드 분석 솔루션을 한 곳에 구축: 수집, 검색, 준비, 오케스트레이션, 시각화
* SQL이나 Spark 코드를 작성하는 데이터 엔지니어를 위한 업계 최고의 생산성: 작성, 디버깅 및 성능 최적화
* 엔터프라이즈 CI/CD 프로세스와 통합

## <a name="next-steps"></a>다음 단계

* [작업 영역 만들기](quickstart-create-workspace.md)
* [Synapse Studio 사용](quickstart-synapse-studio.md)
* [SQL 풀 만들기](quickstart-create-sql-pool-portal.md)
* [SQL 주문형 사용](quickstart-sql-on-demand.md)
* [Apache Spark 풀 만들기](quickstart-create-apache-spark-pool-portal.md)
