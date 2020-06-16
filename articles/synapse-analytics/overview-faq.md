---
title: FAQ - Azure Synapse Analytics(작업 영역 미리 보기)
description: Azure Synapse Analytics(작업 영역 미리 보기)에 대한 FAQ
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 2c470483cee8b621475fa70dd09c80aa126173be
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609151"
---
# <a name="azure-synapse-analytics-workspaces-preview-frequently-asked-questions"></a>Azure Synapse Analytics(작업 영역 미리 보기) 질문과 대답

이 가이드에서는 Synapse Analytics에 대한 질문과 대답을 찾을 수 있습니다.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>일반

### <a name="q-what-is-azure-synapse-analytics"></a>Q: Azure Synapse Analytics는 무엇인가요?

A: Azure Synapse는 BI, AI 및 지속적인 인텔리전스를 위한 통합 데이터 플랫폼입니다. 다음과 같은 통합 방법을 제공하는 단일 플랫폼을 통해 다양한 분석 런타임(예: SQL 및 Spark)을 연결합니다.

- 네트워크를 포함하여 분석 리소스를 보호하고, 풀, 데이터 및 개발 아티팩트에 대한 Single Sign-On 액세스를 관리합니다.
- 모든 계층의 작업 영역 활동에서 발생하는 이벤트를 쉽게 모니터링하고 빠르게 최적화, 대응 및 디버그합니다.
- 엔진 전체에서 메타데이터를 관리합니다. Spark 테이블을 만듭니다. 그러면 Azure Synapse 데이터베이스에서 자동으로 사용할 수 있습니다.
- 통합 사용자 환경을 통해 데이터와 상호 작용합니다. Synapse Studio는 빅 데이터 개발자, 데이터 엔지니어, DBA, 데이터 분석가 및 데이터 과학자를 동일한 플랫폼에 제공합니다.

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>Q: Azure Synapse Analytics의 주요 구성 요소는 무엇인가요?

A: Azure Synapse의 기능은 다음과 같습니다.

- SQL 풀 또는 SQL 주문형(미리 보기)을 통해 제공되는 분석 기능(서버리스)
- Scala, Python, SparkSQL 및 C#을 완벽하게 지원하는 Apache Spark 풀(미리 보기)
- 코드가 없는 빅 데이터 변환 환경을 제공하는 데이터 흐름
- 데이터를 통합하고 모든 코드 개발을 운영화하기 위한 데이터 통합 및 오케스트레이션
- 단일 웹 UI를 통해 이러한 모든 기능에 액세스하는 스튜디오

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>Q: Azure Synapse Analytics와 Azure SQL Data Warehouse의 관계는 어떻게 되나요?

A: Azure Synapse Analytics는 Azure SQL Data Warehouse를 SQL 풀이 데이터 웨어하우스 솔루션으로 포함된 분석 플랫폼으로 발전시킨 것입니다. 이 플랫폼은 데이터 검색, 수집, 변환, 준비 및 서비스 분석 계층을 결합합니다.

## <a name="use-cases"></a>사용 사례

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>Q: Synapse SQL 풀에 적합한 사용 사례는 무엇인가요?

A: SQL 풀은 데이터 웨어하우스 요구 사항의 핵심입니다. [가격/성능](https://azure.microsoft.com/services/sql-data-warehouse/compare/) 측면에서 선도적인 데이터 웨어하우스 솔루션입니다. SQL 풀은 다음을 수행할 수 있는 업계 최고의 클라우드 데이터 웨어하우스 솔루션입니다.

- 높은 동시성과 워크로드 격리를 통해 성능에 영향을 주지 않으면서 광범위하고 다양한 워크로드를 처리합니다.
- 네트워크 보안에서 세부적인 액세스에 이르는 고급 기능을 통해 데이터를 쉽게 보호합니다.
- 광범위한 에코 시스템의 이점을 제공합니다.

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>Q: Synapse의 Spark에 적합한 사용 사례는 무엇인가요?

A: 첫 번째 목표는 일괄 처리 또는 스트림에서 레이크를 통해 데이터를 변환하는 데 유용한 데이터 엔지니어링 환경을 제공하는 것입니다. 데이터 오케스트레이션과 긴밀하고 간단하게 통합되어 개발 작업을 쉽게 운영화할 수 있습니다.

또 다른 Spark 사용 사례는 데이터 과학자가 다음을 수행하는 것입니다.

- 기능 추출
- 데이터 검색
- 모델 학습

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>Q: Synapse의 SQL 주문형에 적합한 사용 사례는 무엇인가요?

A: SQL 주문형은 데이터 레이크의 데이터에 대한 쿼리 서비스입니다. 데이터를 특수화된 저장소에 복사하거나 로드할 필요 없이 데이터를 적절히 쿼리할 수 있는 친숙한 T-SQL 구문을 제공하여 모든 데이터에 대한 액세스를 민주화할 수 있습니다.

사용 사례의 예는 다음과 같습니다.

- 기본 검색 및 탐색 - 데이터 분석가, 새로운 데이터 과학자 및 데이터 엔지니어에게 읽기 T-SQL 쿼리에 대한 스키마를 통해 해당 데이터 레이크의 데이터에 대한 첫 번째 인사이트를 쉽게 얻을 수 있는 경로를 제공합니다.
- 논리적 데이터 웨어하우스 - 데이터 분석가는 T-SQL 언어의 전체 표현을 실행하여 Azure Storage의 데이터를 직접 쿼리 및 분석하고, 친숙한 BI 도구(예: Azure Analyses Services, Power BI Premium 등)를 통해 Starlight Query 쿼리를 다시 실행하여 대시보드를 새로 고칠 수 있습니다.
- "단일 쿼리" ETL - 데이터 엔지니어가 Azure Storage 기반 데이터를 한 형식에서 다른 형식으로 변환하고, 대규모 병렬 처리 방식으로 필터, 집계 등을 수행하며, 쿼리 결과를 Azure Storage에 유지하며, Starlight Query 또는 기타 관심 있는 서비스에서 추가 처리에 즉시 사용할 수 있도록 합니다.

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>Q: Synapse의 데이터 흐름에 적합한 사용 사례는 무엇인가요?

A: 데이터 흐름을 통해 데이터 엔지니어가 코드를 작성하지 않고도 그래픽 데이터 변환 논리를 개발할 수 있습니다. 결과 데이터 흐름은 데이터 통합 및 오케스트레이션 내에서 활동으로 실행됩니다. 데이터 흐름 활동은 기존 일정, 제어, 흐름 및 모니터링 기능을 통해 운영화될 수 있습니다.

## <a name="security-and-access"></a>보안 및 액세스

A: Synapse Analytics에서 엔드투엔드 Single Sign-On 환경은 중요한 인증 프로세스입니다. 완전한 AAD 통합을 통해 ID를 관리하고 전달해야 합니다.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>Q: ADLS Gen2에서 파일 및 폴더에 액세스하는 방법

A: 파일 및 폴더에 대한 액세스는 현재 ADLS Gen2를 통해 관리됩니다. 자세한 내용은 [Data Lake Storage 액세스 제어](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)를 참조하세요.

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>Q: 타사 비즈니스 인텔리전스 도구를 사용하여 Azure Synapse Analytics에 액세스할 수 있나요?

A: 예, Tableau 및 Power BI와 같은 타사 비즈니스 애플리케이션을 사용하여 SQL 풀 및 SQL 주문형에 연결할 수 있습니다. Spark는 IntelliJ를 지원합니다.

## <a name="next-steps"></a>다음 단계

- [작업 영역 만들기](quickstart-create-workspace.md)
- [Synapse Studio 사용](quickstart-synapse-studio.md)
- [SQL 풀 만들기](quickstart-create-sql-pool-portal.md)
- [SQL 주문형 사용](quickstart-sql-on-demand.md)
- [Apache Spark 풀 만들기](quickstart-create-apache-spark-pool-portal.md) 
