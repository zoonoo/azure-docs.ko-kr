---
title: Azure Data Lake Analytics에 대한 재해 복구 지침
description: Azure Data Lake Analytics 계정에 대한 재해 복구를 계획하는 방법을 알아봅니다.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: ce5e96bf464eebb9b91e3ffac9312e89df9cbabe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220978"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics에 대한 재해 복구 지침

Azure Data Lake Analytics는 빅 데이터를 간소화하는 주문형 분석 작업 서비스입니다. 하드웨어를 배포, 구성 및 조정하는 대신, 데이터를 변형하고 귀중한 통찰력을 얻기 위한 쿼리를 작성합니다. 이 분석 서비스는 필요한 전력 크기만큼 다이얼을 설정하여 어떤 크기의 작업도 즉시 처리할 수 있습니다. 실행할 때 작업 기준으로 비용이 부과되므로 비용 효과적일 수 있습니다. 이 문서에서는 드물게 발생하는 지역 전체 중단 또는 삭제 실수로부터 작업을 보호하는 방법에 관한 지침을 제공합니다.

## <a name="disaster-recovery-guidance"></a>재해 복구 지침

Azure Data Lake Analytics를 사용하는 경우 자체 재해 복구 계획을 준비해야 합니다. 이 문서에서는 재해 복구 계획을 수립하는 방법을 안내합니다. 자체 계획을 세우는 데 도움이 되는 추가 리소스가 있습니다.
- [Azure 애플리케이션의 오류 및 재해 복구](/azure/architecture/reliability/disaster-recovery)
- [Azure 복구력 기술 지침](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>모범 사례 및 시나리오 지침

지역의 ADLA 계정에서 U-SQL 테이블과 비정형 데이터를 읽고 쓰는 되풀이 U-SQL 작업을 실행할 수 있습니다.  다음 단계를 수행하여 재해에 대비해 보세요.

1. 보조 지역에서 중단 중에 사용될 ADLA 및 ADLS 계정을 만듭니다.

   > [!NOTE]
   > 계정 이름은 전역적으로 고유하므로 어떤 계정이 보조 계정이지 나타내는 일관된 명명 체계를 사용합니다.

2. 비정형 데이터의 경우 [Azure Data Lake Storage Gen1의 데이터용 재해 복구 지침](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)을 참조하세요.

3. ADLA 테이블과 데이터베이스에 저장된 정형 데이터의 경우 데이터베이스, 테이블, 테이블 반환 함수, 어셈블리와 같은 메타데이터 아티팩트의 복사본을 만듭니다. 프로덕션에서 변경되면 아티팩트를 주기적으로 다시 동기화해야 합니다. 예를 들어 데이터를 복사하여 보조 테이블에 삽입해 새로 삽입된 데이터를 보조 지역에 복제해야 합니다.

   > [!NOTE]
   > 이러한 개체 이름은 보조 계정으로 범위가 지정되고 전역적으로 고유하지 않음으로 기본 프로덕션 계정 이름과 같을 수 있습니다.

중단 중에 입력 경로가 보조 엔드포인트를 가리키도록 스크립트를 업데이트해야 합니다. 그런 다음 사용자가 보조 지역의 ADLA 계정에 작업을 제출합니다. 그러면 작업의 출력이 보조 지역의 ADLA계정과 ADLS 계정에 기록됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Data Lake Storage Gen1의 데이터용 재해 복구 지침](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
