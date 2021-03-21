---
title: Azure 부서의 범위의 데이터 계보 (미리 보기)
description: 데이터 계보의 개념에 대해 설명 합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 476355f41de5e0e6aaffdedea8947cab5221767a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200740"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Azure 부서의 범위 Data Catalog 클라이언트의 데이터 계보

이 문서에서는 Azure 부서의 범위 Data Catalog의 데이터 계보에 대 한 개요를 제공 합니다. 또한 데이터 시스템을 카탈로그와 통합 하 여 데이터 계보를 캡처하는 방법을 자세히 설명 합니다. 부서의 범위는 조직의 데이터 공간 및 다음을 비롯 한 다양 한 준비 수준에서 데이터에 대 한 계보를 캡처할 수 있습니다.

- 다양 한 플랫폼에서 준비 된 완전히 원시 데이터
- 변환 되 고 준비 된 데이터
- 시각화 플랫폼에서 사용 하는 데이터입니다.

## <a name="use-cases"></a>사용 사례

데이터 계보는 데이터의 원본에 걸쳐 있는 수명 주기와 널리 이해 되며 데이터 공간에서 시간이 지남에 따라 이동 합니다. 이는 문제 해결, 데이터 파이프라인의 근본 원인 추적, 디버깅 등의 여러 가지 이전 버전의 시나리오에 사용 됩니다. 계보는 데이터 품질 분석, 규정 준수 및 "가상 분석" 시나리오에도 사용 됩니다 .이 시나리오를 종종 영향 분석 이라고 합니다. 계보는 데이터가 변환 된 방법을 포함 하 여 원본에서 대상으로 이동 하는 데이터를 표시 하기 위해 시각적으로 표시 됩니다. 대부분의 엔터프라이즈 데이터 환경에서 복잡성을 고려 하 여 이러한 보기는 주변 데이터 요소를 통합 하거나 마스킹 하지 않고도 이해 하기 어려울 수 있습니다.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Azure 부서의 범위 Data Catalog의 계보 환경

부서의 범위 Data Catalog는 다른 데이터 처리, 저장소 및 분석 시스템과 연결 하 여 계보 정보를 추출 합니다. 이 정보는 카탈로그에서 일반적인 시나리오 관련 계보 환경을 나타내는 데 결합 됩니다.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="blob 저장소에서 복사 된 데이터를 보여 주는 종단 간 계보 Power BI 대시보드를 모두 표시 합니다.":::

데이터 공간에는 데이터 추출, 변환 (ETL/ELT 시스템), 분석 및 시각화 시스템을 수행 하는 시스템이 포함 될 수 있습니다. 각 시스템은 시스템 경계 내에 있는 데이터의 상태와 품질을 설명 하는 다양 한 정적 및 작업 메타 데이터를 캡처합니다. 데이터 카탈로그의 계보 목표는 가능한 가장 낮은 수준에서 각 데이터 시스템의 이동, 변환 및 작업 메타 데이터를 추출 하는 것입니다.

다음 예제는 여러 시스템에서 데이터를 이동 하는 일반적인 사용 사례입니다. 여기에서 Data Catalog 각 계보에 대 한 각 시스템에 연결 됩니다.

- Data Factory 온-프레미스/raw 영역에서 클라우드의 방문 영역으로 데이터를 복사 합니다. 
- Synapse, Databricks 등의 데이터 처리 시스템은 노트북을 사용 하 여 방문 영역에서 큐 레이트 영역으로 데이터를 처리 하 고 변환 합니다.
- 최적의 쿼리 성능 및 집계를 위해 분석 모델로 데이터를 추가로 처리 합니다. 
- 데이터 시각화 시스템은 메타 데이터를 사용 하 고 메타 데이터를 처리 하 여 BI 대시보드, ML 실험 등을 만듭니다.

## <a name="lineage-granularity"></a>계보 세분성

이 섹션에서는 데이터 카탈로그에서 계보 정보를 수집 하는 세분성에 대 한 세부 정보에 대해 설명 합니다. 이 세분성은 데이터 시스템에 따라 달라질 수 있습니다.

### <a name="entity-level-lineage-sources--process--targets"></a>엔터티 수준 계보: 원본 > 프로세스 > 대상 

- 계보는 그래프로 표현 되며, 일반적으로 계산 시스템에서 호출 하는 프로세스에 의해 연결 되는 데이터 저장소 시스템의 원본 및 대상 엔터티를 포함 합니다. 
- 데이터 시스템은 데이터 카탈로그에 연결 하 여 기본 데이터 시스템의 물리적 개체를 참조 하는 고유 개체를 생성 하 고 보고 합니다. 예를 들어 SQL 저장 프로시저, 노트북 등이 있습니다.
- 소유권 같은 추가 메타 데이터가 포함 된 고화질 계보는 소스 & 대상 엔터티에 대해 사람이 읽을 수 있는 형식으로 계보를 표시 하기 위해 캡처됩니다. 예: 파티션 또는 파일 수준이 아닌 hive 테이블 수준의 계보

### <a name="column-or-attribute-level-lineage"></a>열 또는 특성 수준 계보

대상 엔터티에서 특성을 만들거나 파생 시키는 데 사용 되는 소스 엔터티의 특성을 식별 합니다. 원본 특성의 이름을 대상에서 유지 하거나 이름을 바꿀 수 있습니다. ADF와 같은 시스템은 온-프레미스 환경에서 클라우드로 일 대 일 복사를 수행할 수 있습니다. 예를 들어 `Table1/ColumnA -> Table2/ColumnA`을 참조하십시오.

### <a name="process-execution-status"></a>프로세스 실행 상태

근본 원인 분석 및 데이터 품질 시나리오를 지원 하기 위해 데이터 처리 시스템에서 작업의 실행 상태를 캡처합니다. 이 요구 사항은 다른 데이터 처리 시스템의 모니터링 기능을 대체 하는 것이 아니라 다른 데이터 처리 시스템의 모니터링 기능을 대체 하는 것도 아닙니다. 

## <a name="summary"></a>요약

계보는 품질, 신뢰 및 감사 시나리오를 지원 하기 위한 부서의 범위 Data Catalog의 중요 한 기능입니다. 데이터 카탈로그의 목표는 환경 내의 모든 데이터 시스템이 자연스럽 게 연결 하 고 계보를 보고할 수 있는 강력한 프레임 워크를 구축 하는 것입니다. 메타 데이터를 사용할 수 있게 되 면 데이터 카탈로그는 데이터 시스템에서 제공 하는 메타 데이터를 함께 사용 하 여 데이터를 관리 하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal에서 Azure Purview 계정 만들기](create-catalog-portal.md)
* [빠른 시작: Azure PowerShell/Azure CLI를 사용하여 Azure Purview 계정 만들기](create-catalog-powershell.md)
* [빠른 시작: 부서의 범위 Studio 사용](use-purview-studio.md)
