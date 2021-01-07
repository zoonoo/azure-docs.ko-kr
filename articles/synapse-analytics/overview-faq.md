---
title: FAQ - Azure Synapse Analytics
description: Azure Synapse Analytics에 대한 FAQ입니다.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 671ca73cfa898be532521599d1211d2a8081eb4b
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563265"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Azure Synapse Analytics 질문과 대답

이 가이드에서는 Azure Synapse Analytics에 대한 질문과 대답을 확인할 수 있습니다.

## <a name="general"></a>일반

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>Q: RBAC 역할을 사용하여 내 작업 영역을 보호하려면 어떻게 해야 하나요?

A: Azure Synapse는 할당할 여러 역할과 범위를 도입하여 작업 영역에 대한 보안을 간소화할 수 있습니다.

Synapse RBAC 역할:
* Synapse 관리자
* Synapse SQL 관리자
* Synapse Spark 관리자
* Synapse 기여자(미리 보기)
* Synapse 아티팩트 게시자(미리 보기)
* Synapse 아티팩트 사용자(미리 보기)
* Synapse 컴퓨팅 운영자(미리 보기)
* Synapse 자격 증명 사용자(미리 보기)

Synapse 작업 영역을 보호하려면 다음 RBAC 범위에 RBAC 역할을 할당합니다.
* 작업 영역
* Spark 풀
* 통합 런타임
* 연결된 서비스
* 자격 증명

또한 전용 SQL 풀을 사용하면 사용자가 알고 있고 좋아하는 동일한 보안 기능을 모두 사용할 수 있습니다.

### <a name="q-how-do-i-control-cont-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>Q: 전용 SQL 풀, 서버리스 SQL 풀 및 서버리스 Spark 풀을 제어하려면 어떻게 해야 하나요?

A: Azure Synapse는 처음 시작할 때 Azure 구독 수준에서 사용할 수 있는 기본 제공 비용 분석 및 비용 경고와 함께 작동합니다.

- 전용 SQL 풀 - 전용 SQL 풀의 크기를 만들고 지정하므로 비용을 직접 확인하여 제어할 수 있습니다. Azure RBAC 역할을 사용하여 전용 SQL 풀을 만들거나 크기를 조정할 수 있는 사용자를 추가로 제어할 수 있습니다.

- 서버리스 SQL 풀 - 지출을 일별, 주별 및 월별 수준으로 제한할 수 있는 모니터링 및 비용 관리 제어 기능이 있습니다. 자세한 내용은 [서버리스 SQL 풀에 대한 비용 관리](./sql/data-processed.md)를 참조하세요. 

- 서버리스 Spark 풀 - Synapse RBAC 역할을 사용하여 Spark 풀을 만들 수 있는 사용자를 제한할 수 있습니다.  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>Q: Synapse 작업 영역은 GA에서 개체의 폴더 구성 및 세분성을 지원하나요?

A: Synapse 작업 영역은 사용자 정의 폴더를 지원합니다.

### <a name="q-can-i-link-more-than-one-power-bi-workspace-to-a-single-azure-synapse-workspace"></a>Q: 둘 이상의 Power BI 작업 영역을 단일 Azure Synapse 작업 영역에 연결할 수 있나요?
    
A: 현재 단일 Power BI 작업 영역만 Azure Synapse 작업 영역에 연결할 수 있습니다. 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>Q: Cosmos DB GA에 대한 Synapse Link가 있나요?

A: Apache Spark용 Synapse Link는 GA입니다. 서버리스 SQL 풀용 Synapse Link는 공개 미리 보기에 있습니다.

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>Q: Azure Synapse 작업 영역에서 CI/CD를 지원하나요? 

A: 예! 모든 파이프라인 아티팩트, Notebooks, SQL 스크립트 및 Spark 작업 정의는 Git에 있습니다. 모든 풀 정의는 ARM 템플릿으로 Git에 저장됩니다. 전용 SQL 풀 개체(스키마, 테이블, 보기 등)는 CI/CD를 지원하는 데이터베이스 프로젝트를 사용하여 관리됩니다.

## <a name="pipelines"></a>Pipelines

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>Q: 파이프라인을 실행하는 데 사용되는 자격 증명을 확인하려면 어떻게 해야 하나요? 

A: Synapse Pipeline의 각 작업은 연결된 서비스 내에 지정된 자격 증명을 사용하여 실행됩니다.

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>Q: SSIS IR은 Synapse Integrate에서 지원되나요?

A: 현재는 없습니다. 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>Q: 기존 파이프라인을 Azure Data Factory에서 Azure Synapse 작업 영역으로 마이그레이션하려면 어떻게 해야 하나요?

A: 이때 원래 파이프라인에서 JSON을 내보내고 Synapse 작업 영역으로 가져와서 Azure Data Factory 파이프라인 및 관련 아티팩트를 수동으로 다시 만들어야 합니다.

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>Q: Synapse용 Apache Spark와 Apache Spark의 차이점은 무엇인가요?

A: Synapse용 Apache Spark는 다른 서비스(AAD, AzureML 등), 추가 라이브러리(mssparktuils, Hummingbird) 및 미리 튜닝된 성능 구성과의 통합에 대한 지원이 추가된 Apache Spark입니다.

현재 Apache Spark에서 실행 중인 모든 워크로드는 변경 없이 Azure Synapse용 Apache Spark에서 실행됩니다. 

### <a name="q-what-versions-of-spark-are-available"></a>Q: 사용할 수 있는 Spark 버전은 무엇인가요?

A: Azure Synapse Apache Spark는 Spark 2.4를 완벽하게 지원합니다. 핵심 구성 요소 및 현재 지원되는 버전의 전체 목록은 [Apache Spark 버전 지원](./spark/apache-spark-version-support.md)을 참조하세요.

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>Q: Azure Synapse Spark에는 DButils와 동등한 항목이 있나요?

A: 예, Azure Synapse Apache Spark는 **mssparkutils** 라이브러리를 제공합니다. 유틸리티에 대한 전체 설명서는 [Microsoft Spark 유틸리티 소개](./spark/microsoft-spark-utilities.md)를 참조하세요.

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>Q: Apache Spark에서 세션 매개 변수를 설정하려면 어떻게 해야 하나요?

A: 세션 매개 변수를 설정하려면 %%configure magic available을 사용합니다. 매개 변수를 적용하려면 세션을 다시 시작해야 합니다. 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>Q: 서버리스 Spark 풀에서 클러스터 수준 매개 변수를 설정하려면 어떻게 해야 하나요?

A: 클러스터 수준 매개 변수를 설정하려면 Spark 풀에 대한 spark.conf 파일을 제공하면 됩니다. 그러면 이 풀에서 구성 파일의 이전 매개 변수를 허용합니다. 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>Q: Azure Synapse Analytics에서 다중 사용자 Spark 클러스터를 실행할 수 있나요?
 
A: Azure Synapse는 특정 사용 사례를 위해 특별히 만든 엔진을 제공합니다. Synapse용 Apache Spark는 클러스터 모델이 아니라 작업 서비스로 설계되었습니다. 사용자가 다중 사용자 클러스터 모델을 요청하는 두 가지 시나리오가 있습니다.

**시나리오 #1: 많은 사용자가 BI용 데이터를 제공하기 위해 클러스터에 액세스**

이 작업을 수행하는 가장 쉬운 방법은 Spark를 사용하여 데이터를 준비한 다음, Synapse SQL에서 제공하는 기능을 활용하여 Power BI를 해당 데이터 세트에 연결하는 것입니다.

**시나리오 2: 여러 개발자를 단일 클러스터에 배치하여 비용 절감**
 
이 시나리오를 충족하려면 각 개발자에게 적은 수의 Spark 리소스를 사용하도록 설정된 서버리스 Spark 풀을 제공해야 합니다. 서버리스 Spark 풀은 비용이 전혀 들지 않으므로 여러 개발자가 있으면 적극적으로 사용할 때까지 비용을 최소화할 수 있습니다. 풀은 메타데이터(Spark 테이블)를 공유하므로 서로 쉽게 작업할 수 있습니다.

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>Q: 라이브러리를 포함, 관리 및 설치하려면 어떻게 해야 하나요?

A:  Spark 풀을 만드는 동안 Synapse 작업 영역 또는 Azure Portal에서 requirements.txt 파일을 통해 외부 패키지를 설치할 수 있습니다. [Azure Synapse Analytics에서 Apache Spark용 라이브러리 관리](./spark/apache-spark-azure-portal-add-libraries.md)를 참조하세요.

## <a name="dedicated-sql-pools"></a>전용 SQL 풀

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>Q: 전용 SQL 풀과 서버리스 풀 간의 기능적 차이점은 무엇인가요?

A: 차이점에 대한 전체 목록은 [Synapse SQL의 T-SQL 기능 차이점](./sql/overview-features.md)에서 찾을 수 있습니다.

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>Q: 이제 Azure Synapse는 GA이므로 이전에 독립 실행형이었던 전용 SQL 풀을 Azure Synapse로 이동하려면 어떻게 해야 하나요? 

A: "이동" 또는 "마이그레이션"을 수행할 수 없습니다. 기존 풀에서 새 작업 영역 기능을 사용하도록 선택할 수 있습니다. 이렇게 하면 호환성이 손상되는 변경이 없고 Synapse Studio, Spark 및 서버리스 SQL 풀과 같은 새로운 기능을 사용할 수 있습니다.

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>Q: 현재 전용 SQL 풀의 기본 배포는 무엇인가요? 

A: 기본적으로 모든 새 전용 SQL 풀이 작업 영역에 배포되지만, 필요한 경우 여전히 독립 실행형 폼 팩터에서 전용 SQL 풀(이전의 SQL DW)을 만들 수 있습니다. 


### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-sql-pools"></a>Q: 전용 SQL 풀과 서버리스 SQL 풀 간의 기능적 차이점은 무엇인가요?

A: 차이점에 대한 전체 목록은 [Synapse SQL의 T-SQL 기능 차이점](./sql/overview-features.md)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse Analytics 시작](get-started.md)
* [작업 영역 만들기](quickstart-create-workspace.md)
* [서버리스 SQL 풀 사용](quickstart-sql-on-demand.md)
