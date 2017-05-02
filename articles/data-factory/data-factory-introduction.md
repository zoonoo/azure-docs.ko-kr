---
title: "데이터 팩터리 개요, 데이터 통합 서비스 | Microsoft Docs"
description: "Azure Data Factory가 무엇인지 알아봅니다. 데이터의 이동과 변환을 조율하고 자동화하는 클라우드 데이터 통합 서비스입니다."
keywords: "데이터 통합, 클라우드 데이터 통합, Azure 데이터 팩터리란"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/21/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 260208e7c7a08110eb3c885ef86ec4c18ff42fc9
ms.openlocfilehash: 40552b5d3cea5b04826c08e7b4b1d046a9fcefba
ms.lasthandoff: 04/23/2017


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Azure 데이터 팩터리 서비스인 클라우드의 데이터 통합 서비스 소개
## <a name="what-is-azure-data-factory"></a>Azure 데이터 팩터리란 무엇인가요?
빅 데이터의 세계에서 기존 데이터는 어떻게 비즈니스에 활용될까요? 온-프레미스 데이터 소스 또는 기타 다양한 데이터 소스의 참조 데이터를 사용하여 클라우드에서 생성된 데이터를 보강할 수 있을까요? 마약 그렇다면, 다양한 소스의 데이터를 집계하고 처리할 수 있는 플랫폼이 필요할 것입니다. Azure Data Factory는 데이터의 **이동**과 **변환**을 조율하고 자동화하는 클라우드 기반의 데이터 통합 서비스입니다. 다양한 데이터 저장소에서 데이터를 수집하고, 데이터를 변환/처리하고, 출력 데이터를 다른 데이터 저장소에 게시할 수 있는 데이터 통합 솔루션을 만들 수 있습니다. 

![다이어그램: 데이터 팩터리 개요, 데이터 통합 서비스](./media/data-factory-introduction/what-is-azure-data-factory.png)

**그림 1** 다양한 데이터 원본으로부터 데이터를 수집하고 해당 데이터를 준비하고 변환하고 분석한 다음 소비를 위해 바로 사용할 수 있는 데이터를 게시합니다.


## <a name="what-does-it-offer"></a>무엇을 제공하나요? 
전통적으로 데이터 통합 프로젝트는 다음 이미지처럼 조직 내 다양한 데이터 소스에서 데이터를 추출하고, EDW(엔터프라이즈 데이터 웨어하우스)의 대상 스키마를 준수하도록 데이터를 변환하고, EDW에 데이터를 로드하는 ETL(추출-변환-로드) 프로세스를 중심으로 돌아갔습니다. 그런 다음 BI 분석 솔루션에 필요한 단일 정보 소스로 EDW에 액세스합니다.

![기존의 ETL](media/data-factory-introduction/traditional-etl.png)
**기존의 ETL**

다음 이미지처럼 오늘날의 기업 데이터 환경은 그 볼륨, 다양성 및 복잡성이 기하급수적으로 증가하고 있습니다. 온-프레미스 및 클라우드에서 그 어느 때보다도 다양한 데이터가 온갖 형태와 속도로 생성됩니다. 데이터 처리는 모든 지역에서 발생해야 하며 오픈 소스 소프트웨어, 상용 솔루션 및 비용이 많이 들고 통합 및 유지 관리가 어려운 사용자 지정 처리 서비스의 조합을 포함하고 있습니다. 오늘날 빠르게 변화하는 빅 데이터 환경에 적응하기 위해 필요한 민첩성은 기존의 EDW를 현대 정보 생산 시스템에 필요한 기능과 결합할 수 있는 좋은 기회입니다. Azure Data Factory는 기존 EDW 및 변화하는 데이터 환경 모두에서 작동하여 기업에 데이터 중심 의사 결정에 사용 가능한 모든 데이터를 활용할 수 있는 힘을 제공하는 구성 플랫폼입니다.

![새로운 빅 데이터 환경](media/data-factory-introduction/new-big-data-landscape.png)
**새로운 빅 데이터 환경**

Azure Data Factory 서비스는 **데이터 처리, 저장 및 이동 서비스를 정보 생산 파이프라인으로 작성**하고 신뢰할 수 있는 데이터 자산을 관리할 수 있는 플랫폼을 제공함으로써 기업에 이 다양성을 활용할 수 있는 힘을 제공합니다.

Azure Data Factory 서비스를 사용하면 다음과 같은 일을 할 수 있습니다.
- **다양한 데이터 저장소 및 처리 시스템을 간편하게 사용**. 

    기업에는 서로 다른 형식의 데이터가 서로 다른 원본에 있습니다. 따라서 정보 생산 시스템을 구축하는 첫 번째 단계에서는 필요한 모든 데이터 원본과 SaaS 서비스, 파일 공유, FTP, 웹 서비스 등과 같은 처리에 연결하고 후속 처리에 필요한 데이터를 중앙의 위치로 이동합니다.

    데이터 팩터리가 없으면 기업은 사용자 지정 데이터 이동 구성 요소를 구축하거나 사용자 지정 서비스를 작성하여 이러한 데이터 원본과 처리를 통합해야 합니다. 이 경우 비용이 많이 들고 이러한 서비스를 통합 및 관리하기 어려우며, 엔터프라이즈급 모니터링 및 경고 기능과 완전히 관리되는 서비스에서 제공할 수 있는 제어 기능이 부족한 경우가 많습니다.

    Data Factory가 있으면 향후 분석에 사용할 수 있도록 데이터 파이프라인에서 복사 작업을 사용하여 온-프레미스 및 클라우드 원본 데이터 저장소에서 클라우드의 중앙 데이터 저장소로 데이터를 이동할 수 있습니다. 예를 들어 Azure Data Lake Store에서 데이터를 수집하고 나중에 Azure Data Lake Analytics 계산 서비스를 사용하여 데이터를 변환할 수 있습니다. 또는 Azure HDInsight Hadoop 클러스터를 사용하여 Azure Blob Storage에서 데이터를 수집하고 나중에 변환할 수 있습니다.
- **데이터를 신뢰할 수 있는 정보로 변환**. 

    데이터가 클라우드의 중앙 데이터 저장소에 있으면 이제 유지 관리 가능하고 제어 가능한 일정에 따라 변환된 데이터를 안정적으로 생성하여 프로덕션 환경에 신뢰할 수 있는 데이터를 공급하는 데이터 파이프라인을 작성하고 운영해야 합니다. Azure Data Factory의 데이터 변환은 Hive, Pig, MapReduce, Azure Machine Learning 배치 실행, Azure HDInsight Hadoop 클러스터/Azure Machine Learning VM/VM의 Azure 배치 풀에서 실행되는 사용자 지정 C# 활동 등의 변환 활동을 통해 수행됩니다.
- **한 곳에서 데이터 파이프라인 모니터링**.

    다양한 데이터 포트폴리오를 통해 저장소, 처리 및 데이터 이동 서비스에 대한 신뢰할 수 있고 완벽한 뷰를 마련해야 합니다. Data Factory를 통해 빠르게 종단 간 데이터 파이프라인 상태를 평가하고, 문제를 파악하고, 필요하면 수정 작업을 할 수 있습니다. 데이터 계보 및 모든 원본에서 데이터 간의 관계를 시각적으로 추적합니다. 단일 모니터링 대시보드에서 작업 실행, 시스템 상태 및 종속성의 전체 기록을 살펴봅니다.

## <a name="how-does-it-work"></a>작동 원리 
Azure Data Factory의 정보 생산 단계는 세 단계로 구성됩니다.

![세 가지 정보 생산 단계](media/data-factory-introduction/three-information-production-stages.png)

- **연결 및 수집**: 이 단계에서는 다양한 데이터 소스의 데이터를 한 장소에 수집합니다.
- **변환 및 보강**: 이 단계에서는 수집된 데이터를 처리하거나 변환합니다.
- **게시**. 이 단계에서는 BI 도구, 분석 도구 및 기타 응용 프로그램에서 사용할 수 있도록 데이터를 게시합니다.

## <a name="key-components"></a>핵심 구성 요소
Azure 구독에는 하나 이상의 Azure Data Factory 인스턴스(또는 데이터 팩터리)가 있을 수 있습니다. Azure Data Factory는 함께 작동하여 데이터 흐름에 대한 간단한 데이터 이동 및 변환 오케스트레이션부터 복잡한 데이터 이동 및 변환 오케스트레이션을 구성할 수 있는 플랫폼을 제공하는 네 가지 핵심 구성 요소로 구성됩니다.

### <a name="activity"></a>작업
활동은 데이터에 수행할 작업을 정의합니다. 예를 들어 하나의 데이터 저장소에서 다른 데이터 저장소로 데이터를 복사하는 데 복사 작업을 사용할 수 있습니다. 마찬가지로 데이터를 변환하거나 분석하기 위해서 Azure HDInsight 클러스터에서 Hive 쿼리를 실행하는 Hive 작업을 사용할 수 있습니다. Data Factory는 데이터 이동 활동 및 데이터 변환 활동이라는 두 종류의 활동을 지원합니다.

각 활동은 0개 이상의 입력 데이터 집합을 갖고 하나 이상의 출력 데이터 집합을 생성할 수 있습니다. 


### <a name="data-movement-activities"></a>데이터 이동 활동
데이터 팩터리의 복사 활동은 원본 데이터 저장소의 데이터를 싱크 데이터 저장소로 복사합니다. Data Factory는 다음과 같은 데이터 저장소를 지원합니다. 모든 소스의 데이터를 모든 싱크에 쓸 수 있습니다. 데이터 저장소를 클릭하면 해당 저장소에서/저장소로 데이터를 복사하는 방법을 확인할 수 있습니다.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

자세한 내용은 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 참조하세요.

### <a name="data-transformation-activities"></a>데이터 변환 활동
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

자세한 내용은 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서를 참조하세요.

복사 작업에서 지원하지 않는 데이터 저장소에서/로 데이터를 이동해야 하거나 사용자 고유한 논리를 사용하여 데이터를 변환해야 하는 경우 **사용자 지정 .NET 활동**을 만듭니다. 사용자 지정 활동을 만들고 사용하는 방법에 대한 자세한 내용은 [Azure Data Factory 파이프라인에서 사용자 지정 활동 사용](data-factory-use-custom-activities.md)을 참조하세요.

### <a name="pipeline"></a>파이프라인
파이프라인은 활동 그룹입니다. 파이프라인의 활동이 모여 작업을 수행합니다. 예를 들어 Azure blob에서 데이터를 수집한 다음 HDInsight 클러스터에서 Hive 쿼리를 실행하여 로그 데이터를 분할하는 활동 그룹이 파이프라인에 포함될 수 있습니다. 이 방식의 장점은 파이프라인을 통해 개별 활동 단위가 아닌 하나의 집합으로써 활동을 관리할 수 있다는 점입니다. 예를 들어 각 활동을 개별적으로 배포하고 예약하는 대신 파이프라인을 배포하고 예약할 수 있습니다.

### <a name="datasets"></a>데이터 집합
데이터 집합은 데이터 저장소 내의 데이터 구조를 나타내며, 사용자가 활동에서 입력 또는 출력으로 사용하려는 데이터를 가리키거나 참조할 뿐입니다. 예를 들어 Azure Blob 데이터 집합은 파이프라인이 데이터를 읽어야 하는 Azure Blob Storage에서 Blob 컨테이너 및 폴더를 지정합니다. 

### <a name="linked-services"></a>연결된 서비스
연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 데이터 집합은 데이터 구조를 나타내고, 연결된 서비스는 데이터 원본에 연결을 정의한다고 생각하시면 됩니다.  연결된 서비스는 데이터 팩터리 내에서 두 가지 용도로 사용됩니다.

* 온-프레미스 SQL Server, Oracle 데이터베이스, 파일 공유 또는 Azure Blob Storage 계정을 포함하지만 여기에 국한되지 않는 **데이터 저장소** 를 나타내기 위해 사용됩니다. 지원되는 데이터 저장소 목록은 [데이터 이동 활동](#data-movement-activities) 섹션을 참조하세요.
* 활동의 실행을 호스팅할 수 있는 **계산 리소스** 를 나타내기 위해 사용됩니다. 예를 들어, HDInsightHive 활동은 HDInsight Hadoop 클러스터에서 실행됩니다. 지원되는 컴퓨팅 환경 목록은 [데이터 변환 활동](#data-transformation-activities) 섹션을 참조하세요.

### <a name="relationship-between-data-factory-entities"></a>Data Factory 엔터티 간의 관계
![다이어그램: Data Factory, 클라우드 데이터 통합 서비스 - 주요 개념](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**그림 2.** 데이터 집합, 활동, 파이프라인 및 연결된 서비스 간의 관계

## <a name="supported-regions"></a>지원되는 지역
현재 **미국 서부**, **미국 동부** 및 **북유럽** 지역에서 데이터 팩터리를 만들 수 있습니다. 그러나 데이터 팩터리는 계산 서비스를 사용하여 데이터 저장소 간에 데이터를 이동하고 데이터를 처리하도록 다른 Azure 지역에서 데이터 저장소 및 계산 서비스에 액세스할 수 있습니다.

Azure 데이터 팩터리 자체는 데이터를 저장하지 않습니다. 데이터 기반 흐름을 만들어서 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 간의 데이터 이동을 조정하고 다른 지역 또는 온-프레미스 환경에서 [계산 서비스](data-factory-compute-linked-services.md)를 사용하여 데이터의 처리를 조정할 수 있습니다. 또한 프로그래밍 방식 및 UI 메커니즘을 모두 사용하여 [워크플로를 모니터링하고 관리](data-factory-monitor-manage-pipelines.md) 할 수 있습니다.

Data Factory는 **미국 서부**, **미국 동부** 및 **북유럽** 지역에서만 사용할 수 있지만, 여러 지역에서 데이터 팩터리의 데이터 이동을 지원하는 서비스를 [전역적으로](data-factory-data-movement-activities.md#global) 사용할 수 있습니다. 데이터 저장소가 방화벽 뒤에 있는 경우 온-프레미스 환경에 설치된 [데이터 관리 게이트웨이](data-factory-move-data-between-onprem-and-cloud.md) 가 대신 데이터를 이동시킵니다.

예를 들어 Azure HDInsight 클러스터 및 Azure Machine Learning과 같은 계산 환경이 서유럽 지역 외부에서 실행되고 있다고 가정해보겠습니다. 북유럽에서 Azure Data Factory 인스턴스를 만들고 사용할 수 있으며 이를 사용하여 서유럽의 계산 환경에 작업을 예약할 수 있습니다. 데이터 팩터리가 계산 환경에 작업을 트리거하는 데는 몇 밀리초가 걸리지만 사용자의 계산 환경에서 작업을 실행하는 데 걸리는 시간은 변경되지 않습니다.

나중에 Azure에서 지원하는 모든 지리적 위치에서 Azure 데이터 팩터리를 사용하게 될 예정입니다.

## <a name="next-steps"></a>다음 단계
데이터 파이프라인을 사용하여 데이터 팩터리를 빌드하는 방법을 알아보려면 다음 자습서의 단계별 지침을 따르세요.

| 자습서 | 설명 |
| --- | --- |
| [두 클라우드 데이터 저장소 간의 데이터 이동](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |이 자습서에서는 파이프라인을 포함한 데이터 팩터리를 만들어서 Blob Storage에서 SQL Database로 **데이터를 이동** 합니다. |
| [Hadoop 클러스터를 사용하여 데이터 변환](data-factory-build-your-first-pipeline.md) |이 자습서에서는 Azure HDInsight(Hadoop) 클러스터에서 Hive 스크립트를 실행하여 **데이터를 처리** 하는 데이터 파이프라인으로 첫 번째 Azure 데이터 팩터리를 구축합니다. |
| [데이터 관리 게이트웨이를 사용하여 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) |이 자습서에서는 **온-프레미스** SQL Server 데이터베이스에서 Azure Blob으로 **데이터를 이동**하는 파이프라인이 포함된 데이터 팩터리를 구축합니다. 자습서의 일부로 컴퓨터에 데이터 관리 게이트웨이를 설치하고 구성합니다. |

