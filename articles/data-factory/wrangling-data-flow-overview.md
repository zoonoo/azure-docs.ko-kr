---
title: Azure 데이터 팩터리에서 데이터 흐름 랭글링
description: Azure 데이터 팩터리에서 데이터 흐름 을 랭글링하는 개요
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8f91ed926c733b211443805722b6817b1ce005b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048355"
---
# <a name="what-are-wrangling-data-flows"></a>랭글링 데이터 흐름이란 무엇입니까?

조직은 매일 계속 증가하는 복잡한 데이터를 정확하게 분석하기 위해 데이터 준비와 다툼을 수행해야 합니다. 조직이 다양한 비즈니스 프로세스에서 데이터를 사용하고 가치 창출 시간을 단축할 수 있도록 데이터 준비가 필요합니다.

Azure Data Factory에서 데이터 흐름을 랭글링하면 클라우드 규모에서 코드 없는 데이터 준비를 반복적으로 수행할 수 있습니다. 랭글링 데이터 흐름은 [Power Query Online과](https://docs.microsoft.com/power-query/) 통합되며 데이터 팩터리 사용자가 Power Query M 기능을 사용할 수 있도록 합니다.

랭글링 데이터 흐름은 Power Query 온라인 매시업 편집기에서 생성된 M을 클라우드 스케일 실행을 위한 스파크 코드로 변환합니다.

데이터 흐름을 어지릅니다.

## <a name="use-cases"></a>사용 사례

### <a name="fast-interactive-data-exploration-and-preparation"></a>빠른 대화형 데이터 탐색 및 준비

여러 데이터 엔지니어와 시민 데이터 통합자가 클라우드 규모로 데이터 집합을 대화형으로 탐색하고 준비할 수 있습니다. 데이터 레이크에서 데이터의 볼륨, 다양성 및 속도가 증가함에 따라 사용자는 데이터 세트를 탐색하고 준비할 수 있는 효과적인 방법이 필요합니다. 예를 들어 '2017년부터 신규 고객을 위한 모든 고객 인구 통계학적 정보가 있는' 데이터 집합을 만들어야 할 수 있습니다. 알려진 대상에 매핑되지 않습니다. 레이크에 게시하기 전에 요구 사항을 충족하기 위해 데이터 집합을 탐색, 랭글링 및 준비중입니다. 데이터 흐름을 랭글링하는 것은 덜 공식적인 분석 시나리오에 자주 사용됩니다. 준비된 데이터 집합은 다운스트림에서 변환 및 기계 학습 작업을 수행하는 데 사용할 수 있습니다.

### <a name="code-free-agile-data-preparation"></a>코드 없는 민첩한 데이터 준비

시민 데이터 통합업체는 데이터를 찾고 준비하는 데 60% 이상을 소비합니다. 그들은 운영 생산성을 향상시키기 위해 코드없는 방식으로 그것을 찾고 있습니다. 시민 데이터 통합자가 Power Query Online과 같은 알려진 도구를 사용하여 확장 가능한 방식으로 데이터를 보강, 구성 및 게시할 수 있도록 하면 생산성이 크게 향상됩니다. Azure Data Factory의 데이터 흐름을 랭글링하면 친숙한 Power Query Online 매시업 편집기를 사용하여 시민 데이터 통합자가 오류를 신속하게 수정하고, 데이터를 표준화하고, 비즈니스 의사 결정을 지원하기 위해 고품질 데이터를 생성할 수 있습니다.

### <a name="data-validation"></a>데이터 유효성 검사

코드 없는 방식으로 데이터를 시각적으로 스캔하여 이상치, 이상 징후를 제거하고 빠른 분석을 위해 셰이프에 부합합니다.

## <a name="supported-sources"></a>지원되는 소스

| 커넥터 | 데이터 형식 | 인증 유형 |
| -- | -- | --|
| [Azure Blob 저장소](connector-azure-blob-storage.md) | CSV, 마루 | 계정 키 |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | 서비스 주체 |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, 마루 | 계정 키, 서비스 주체 |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL 인증 |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL 인증 |

## <a name="the-mashup-editor"></a>매시업 편집기

랭글링 데이터 흐름을 만들면 모든 원본 데이터 집합이 데이터 집합 쿼리가 되고 **ADFResource** 폴더에 배치됩니다. 기본적으로 UserQuery는 첫 번째 데이터 집합 쿼리를 가리킵니다. 데이터 집합 쿼리에 대한 변경 내용이 지원되지 않거나 유지되므로 모든 변환은 UserQuery에서 수행해야 합니다. 쿼리의 이름 바꾸기, 추가 및 삭제는 현재 지원되지 않습니다.

![막을](media/wrangling-data-flow/editor.png)

현재 모든 Power Query M 함수가 작성 중에 사용 가능에도 불구하고 데이터 랭글링에 대해 지원되는 것은 아닙니다. 랭글링 데이터 흐름을 작성하는 동안 함수가 지원되지 않는 경우 다음과 같은 오류 메시지가 표시됩니다.

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

지원되는 변환에 대한 자세한 내용은 [랭글링 데이터 흐름 함수를](wrangling-data-flow-functions.md)참조하십시오.

현재 랭글링 데이터 흐름은 하나의 싱크에 쓰기만 지원합니다.

## <a name="next-steps"></a>다음 단계

[랭글링 데이터 흐름을 만드는](wrangling-data-flow-tutorial.md)방법에 대해 알아봅니다.