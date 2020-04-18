---
title: 매핑 데이터 흐름의 싱크 변환
description: 매핑 데이터 흐름에서 싱크 변환을 구성하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 4b10a4c98abd6bec4074bf35764a9cbb85d5b157
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605976"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 싱크 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

데이터를 변환한 후 데이터를 대상 데이터 집합으로 싱크할 수 있습니다. 모든 데이터 흐름에는 하나 이상의 싱크 변환이 필요하지만 변환 흐름을 완료하는 데 필요한 만큼 싱크에 쓸 수 있습니다. 추가 싱크에 쓰려면 새 분기 및 조건부 분할을 통해 새 스트림을 만듭니다.

각 싱크 변환은 정확히 하나의 데이터 팩터리 데이터 집합과 연결됩니다. 데이터 집합은 작성할 데이터의 모양과 위치를 정의합니다.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>매핑 데이터 흐름에서 지원되는 싱크 커넥터

현재 다음 데이터 집합을 싱크 변환에 사용할 수 있습니다.
    
* [Azure Blob](connector-azure-blob-storage.md#mapping-data-flow-properties) 저장소(JSON, 아브로, 텍스트, 마루)
* [Azure 데이터 레이크 저장소 Gen1(JSON,](connector-azure-data-lake-store.md#mapping-data-flow-properties) 아브로, 텍스트, 마루)
* [Azure 데이터 레이크 스토리지 Gen2(JSON,](connector-azure-data-lake-storage.md#mapping-data-flow-properties) 아브로, 텍스트, 마루)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [푸른 코스모스DB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

이러한 커넥터와 관련된 설정은 **설정** 탭에 있습니다. 

Azure Data Factory에서 [90개](connector-overview.md)이상의 기본 커넥터에 액세스할 수 있습니다. 데이터 흐름의 다른 원본에 데이터를 작성하려면 복사 활동을 사용하여 데이터 흐름이 완료된 후 지원되는 스테이징 영역 중 하나에서 해당 데이터를 로드합니다.

## <a name="sink-settings"></a>싱크 설정

싱크를 추가한 후 **싱크** 탭을 통해 구성합니다. 여기서 싱크가 쓰는 데이터 집합을 선택하거나 만들 수 있습니다. 다음은 텍스트 구분 된 파일 형식에 대 한 다양 한 싱크 옵션설명 하는 비디오입니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

![싱크 설정](media/data-flow/sink-settings.png "싱크 설정")

**스키마 드리프트:** [스키마 드리프트는](concepts-data-flow-schema-drift.md) 컬럼 변경 사항을 명시적으로 정의할 필요 없이 데이터 흐름에서 유연한 스키마를 기본적으로 처리할 수 있는 데이터 팩터리의 기능입니다. 스키마 **드리프트 허용을** 사용하여 싱크 데이터 스키마에 정의된 내용 위에 추가 열을 작성합니다.

**스키마 유효성 검사:** 스키마 유효성 검사를 선택하면 들어오는 원본 스키마의 열이 원본 프로젝션에서 찾을 수 없거나 데이터 형식이 일치하지 않는 경우 데이터 흐름이 실패합니다. 이 설정을 사용하여 원본 데이터가 정의된 프로젝션의 계약을 충족하도록 합니다. 데이터베이스 원본 시나리오에서는 열 이름이나 형식이 변경되었다는 신호를 지정하는 데 매우 유용합니다.

## <a name="field-mapping"></a>필드 매핑

선택 변환과 마찬가지로 싱크의 **매핑** 탭에서 들어오는 열을 작성할지 결정할 수 있습니다. 기본적으로 드리프트 열을 포함한 모든 입력 열이 매핑됩니다. 이를 자동 **매핑이라고**합니다.

자동 매핑을 끄면 고정 된 열 기반 매핑 또는 규칙 기반 매핑을 추가할 수 있습니다. 규칙 기반 매핑을 사용하면 패턴 일치를 사용하여 식을 작성할 수 있으며 고정 매핑은 논리적 및 물리적 열 이름을 매핑합니다. 규칙 기반 매핑에 대한 자세한 내용은 [매핑 데이터 흐름의 열 패턴을](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)참조하십시오.

## <a name="custom-sink-ordering"></a>사용자 지정 싱크 순서

기본적으로 데이터는 결정적이지 않은 순서로 여러 싱크에 기록됩니다. 변환 논리가 완료되고 싱크 순서가 실행될 때마다 실행 엔진이 병렬로 데이터를 작성합니다. 정확한 싱크 순서를 지정하고 사용하려면 데이터 흐름의 일반 탭에서 사용자 지정 싱크 순서를 사용하도록 **설정합니다.** 활성화하면 싱크가 순차적으로 증가하여 기록됩니다.

![사용자 지정 싱크 순서](media/data-flow/custom-sink-ordering.png "사용자 지정 싱크 순서")

## <a name="data-preview-in-sink"></a>싱크의 데이터 미리 보기

디버그 클러스터에서 데이터 미리 보기를 가져올 때 싱크에 데이터가 기록되지 않습니다. 데이터의 모양에 대한 스냅샷이 반환되지만 대상에 기록된 것은 없습니다. 싱크에 데이터를 쓰는 것을 테스트하려면 파이프라인 캔버스에서 파이프라인 디버그를 실행합니다.

## <a name="next-steps"></a>다음 단계
이제 데이터 흐름을 만들었으니 [파이프라인에 데이터 흐름 활동을 추가합니다.](concepts-data-flow-overview.md)
