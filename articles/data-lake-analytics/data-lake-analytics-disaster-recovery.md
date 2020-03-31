---
title: Azure 데이터 레이크 분석을 위한 재해 복구 지침
description: Azure Data Lake Analytics 계정에 대한 재해 복구를 계획하는 방법을 알아봅니다.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889773"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure 데이터 레이크 분석을 위한 재해 복구 지침

Azure Data Lake Analytics는 빅 데이터를 간소화하는 주문형 분석 작업 서비스입니다. 하드웨어를 배포, 구성 및 조정하는 대신, 데이터를 변형하고 귀중한 통찰력을 얻기 위한 쿼리를 작성합니다. 이 분석 서비스는 필요한 전력 크기만큼 다이얼을 설정하여 어떤 크기의 작업도 즉시 처리할 수 있습니다. 실행할 때 작업 기준으로 비용이 부과되므로 비용 효과적일 수 있습니다. 이 문서에서는 드문 지역 전체 의 가동 중단 또는 우발적인 삭제로부터 작업을 보호하는 방법에 대한 지침을 제공합니다.

## <a name="disaster-recovery-guidance"></a>재해 복구 지침

Azure Data Lake Analytics를 사용하는 경우 자체 재해 복구 계획을 준비하는 것이 중요합니다. 이 문서에서는 재해 복구 계획을 수립하는 데 도움이 됩니다. 나만의 계획을 세우는 데 도움이 되는 추가 리소스가 있습니다.
- [Azure 애플리케이션의 오류 및 재해 복구](/azure/architecture/reliability/disaster-recovery)
- [Azure 복구력 기술 지침](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>모범 사례 및 시나리오 지침

U-SQL 테이블과 비정형 데이터를 읽고 쓰는 리전에서 ADLA 계정에서 되풀이 U-SQL 작업을 실행할 수 있습니다.  다음 단계를 수행하여 재해에 대비하십시오.

1. 중단 중에 사용할 보조 지역에서 ADLA 및 ADLS 계정을 만듭니다.

   > [!NOTE]
   > 계정 이름은 전역적으로 고유하므로 보조 계정을 나타내는 일관된 이름 지정 체계를 사용합니다.

2. 비정형 데이터의 경우 [Azure Data Lake Storage Gen1의 데이터에 대한 재해 복구 지침을 참조합니다.](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. ADLA 테이블 및 데이터베이스에 저장된 구조화 된 데이터의 경우 데이터베이스, 테이블, 테이블 값 함수 및 어셈블리와 같은 메타데이터 아티팩트의 복사본을 만듭니다. 프로덕션 환경에서 변경 사항이 발생할 때 이러한 아티팩트를 주기적으로 다시 동기화해야 합니다. 예를 들어 새로 삽입된 데이터는 데이터를 복사하고 보조 테이블에 삽입하여 보조 영역에 복제되어야 합니다.

   > [!NOTE]
   > 이러한 개체 이름은 보조 계정으로 범위가 지정되며 전역적으로 고유하지 않으므로 기본 프로덕션 계정과 동일한 이름을 가질 수 있습니다.

중단 중에 입력 경로가 보조 끝점을 가리키도록 스크립트를 업데이트해야 합니다. 그런 다음 사용자는 보조 지역의 ADLA 계정에 작업을 제출합니다. 그러면 작업의 출력이 보조 지역의 ADLA 및 ADLS 계정에 기록됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Data Lake Storage Gen1의 데이터용 재해 복구 지침](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
