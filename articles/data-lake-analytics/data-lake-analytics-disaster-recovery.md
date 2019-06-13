---
title: Azure Data Lake Analytics에 대 한 재해 복구 지침
description: Azure Data Lake Analytics 계정에 대 한 재해 복구를 계획 하는 방법에 알아봅니다.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ea1d4020aa9be23b4839690ae0b386d35bce8a23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498891"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics에 대 한 재해 복구 지침

Azure Data Lake Analytics는 빅 데이터를 간소화하는 주문형 분석 작업 서비스입니다. 하드웨어를 배포, 구성 및 조정하는 대신, 데이터를 변형하고 귀중한 통찰력을 얻기 위한 쿼리를 작성합니다. 이 분석 서비스는 필요한 전력 크기만큼 다이얼을 설정하여 어떤 크기의 작업도 즉시 처리할 수 있습니다. 실행할 때 작업 기준으로 비용이 부과되므로 비용 효과적일 수 있습니다. 이 문서에서는 드물게 발생 하는 전체 지역 가동 중단 또는 삭제 실수로 작업을 보호 하는 방법에 지침을 제공 합니다.

## <a name="disaster-recovery-guidance"></a>재해 복구 지침

Azure Data Lake Analytics를 사용 하는 경우에 고유한 재해 복구 계획을 준비 하기 위해 중요 합니다. 이 문서에서는 재해 복구 계획을 작성 하도록 안내 합니다. 에 고유한 계획을 만들 수 있는 추가 리소스:
- [Azure 애플리케이션의 오류 및 재해 복구](/azure/architecture/reliability/disaster-recovery)
- [Azure 복구력 기술 지침](/azure/architecture/reliability)

## <a name="best-practices-and-scenario-guidance"></a>모범 사례 및 시나리오 가이드

읽고 U-SQL 테이블 뿐만 아니라 구조화 되지 않은 데이터를 기록 하는 지역에서 ADLA 계정에 U-SQL 작업을 되풀이 실행할 수 있습니다.  다음이 단계를 수행 하 여 재해에 대 한 준비 합니다.

1. 가동 중단 중에 사용할 보조 지역의 ADLA 및 ADLS 계정을 만듭니다.

   > [!NOTE]
   > 계정 이름은 전역적으로 고유 하므로 계정 보조가 일관 된 명명 체계를 사용 합니다.

2. 구조화 되지 않은 데이터에 대 한 참조 [Azure 데이터 레이크 저장소 Gen1에서 데이터에 대 한 재해 복구 지침](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. ADLA 테이블 및 데이터베이스에 저장 된 구조적 데이터를 데이터베이스, 테이블, 테이블 반환 함수 및 어셈블리와 같은 메타 데이터 아티팩트는 복사본을 만듭니다. 프로덕션 환경에서 변경이 발생 하는 경우 이러한 아티팩트를 주기적으로 다시 동기화 해야 합니다. 예를 들어, 새로 삽입된 된 데이터는 데이터를 복사 하 고 보조 테이블에 삽입 하 여 보조 지역으로 복제 해야 합니다.

   > [!NOTE]
   > 이러한 개체 이름에는 보조 계정으로 범위가 지정 됩니다 및 하지 않으므로 전역적으로 고유 기본 프로덕션 계정와 같이 동일한 이름을 가질 수 있습니다.

가동 중단이 발생 하는 동안 입력된 경로 보조 끝점으로 가리키고 있으므로 스크립트를 업데이트 해야 합니다. 다음 사용자는 보조 지역에서 ADLA 계정에 해당 작업을 제출합니다. 그런 다음 작업의 출력 보조 지역에서 ADLA 및 ADLS 계정에 쓰입니다.

## <a name="next-steps"></a>다음 단계

[Azure 데이터 레이크 저장소 Gen1에서 데이터에 대 한 재해 복구 지침](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
