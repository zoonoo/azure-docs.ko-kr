---
title: 매핑 데이터 흐름의 소스 변환
description: 매핑 데이터 흐름에서 소스 변환을 설정하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: e4f1369aa850a244128da470aee81b2efc2e09be
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413201"
---
# <a name="source-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 소스 변환 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

원본 변환은 데이터 흐름에 대한 데이터 원본을 구성합니다. 데이터 흐름을 디자인할 때 첫 번째 단계는 항상 소스 변환을 구성하는 것입니다. 원본을 추가하려면 데이터 흐름 캔버스에서 **소스 추가** 상자를 클릭합니다.

모든 데이터 흐름에는 하나 이상의 소스 변환이 필요하지만 데이터 변환을 완료하는 데 필요한 만큼의 소스를 추가할 수 있습니다. 조인, 조회 또는 공용 구조조정 변환과 함께 이러한 소스를 조인할 수 있습니다.

각 원본 변환은 정확히 하나의 데이터 팩터리 데이터 집합과 연결됩니다. 데이터 집합은 쓰거나 읽을 데이터의 모양과 위치를 정의합니다. 파일 기반 데이터 집합을 사용하는 경우 원본의 와일드카드 및 파일 목록을 사용하여 한 번에 두 개 이상의 파일로 작업할 수 있습니다.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>매핑 데이터 흐름에서 지원되는 소스 커넥터

데이터 흐름 매핑은 추출, 로드, 변환(ELT) 접근 방식을 따르며 Azure에 있는 모든 *스테이징* 데이터 집합에서 작동합니다. 현재 소스 변환에서 다음 데이터 집합을 사용할 수 있습니다.
    
* [Azure Blob](connector-azure-blob-storage.md#mapping-data-flow-properties) 저장소(JSON, 아브로, 텍스트, 마루)
* [Azure 데이터 레이크 저장소 Gen1(JSON,](connector-azure-data-lake-store.md#mapping-data-flow-properties) 아브로, 텍스트, 마루)
* [Azure 데이터 레이크 스토리지 Gen2(JSON,](connector-azure-data-lake-storage.md#mapping-data-flow-properties) 아브로, 텍스트, 마루)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties)
* [푸른 코스모스DB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

이러한 커넥터와 관련된 설정은 **소스 옵션** 탭에 있습니다. 

Azure Data Factory에서 [90개](connector-overview.md)이상의 기본 커넥터에 액세스할 수 있습니다. 데이터 흐름에 다른 소스의 데이터를 포함하려면 활동 복사를 사용하여 해당 데이터를 지원되는 스테이징 영역 중 하나에 로드합니다.

## <a name="source-settings"></a>원본 설정

소스를 추가한 후 **소스 설정** 탭을 통해 구성합니다. 여기에서 소스 포인트를 선택하거나 데이터 집합을 만들 수 있습니다. 데이터에 대한 스키마 및 샘플링 옵션을 선택할 수도 있습니다.

![소스 설정 탭](media/data-flow/source1.png "소스 설정 탭")

**테스트 연결:** 데이터 흐름의 스파크 서비스가 원본 데이터 집합에 사용되는 연결된 서비스에 성공적으로 연결할 수 있는지 여부를 테스트합니다. 이 기능을 사용하려면 디버그 모드가 설정되어 있어야 합니다.

**스키마 드리프트:** [스키마 드리프트는](concepts-data-flow-schema-drift.md) 컬럼 변경 사항을 명시적으로 정의할 필요 없이 데이터 흐름에서 유연한 스키마를 기본적으로 처리할 수 있는 데이터 팩터리의 기능입니다.

* 소스 열이 자주 변경되는 경우 **스키마 드리프트 허용** 상자를 선택합니다. 이 설정을 사용하면 들어오는 모든 소스 필드가 싱크로 변환을 통해 흐를 수 있습니다.

* **드리프트 열 유형을 인더페** 선택하면 데이터 팩터리에서 검색된 각 새 열에 대한 데이터 형식을 검색하고 정의할 수 있습니다. 이 기능을 끄면 드리프트된 모든 열은 형식 문자열이 됩니다.

**스키마 유효성 검사:** 스키마 유효성 검사를 선택하면 들어오는 원본 데이터가 데이터 집합의 정의된 스키마와 일치하지 않으면 데이터 흐름이 실행되지 않습니다.

**줄 수 건너뛰기:** 건너뛰기 선 개수 필드는 데이터 집합의 시작 부분에서 무시할 줄 수를 지정합니다.

**샘플링:** 샘플링을 사용하여 소스의 행 수를 제한합니다. 디버깅을 위해 원본의 데이터를 테스트하거나 샘플링할 때 이 설정을 사용합니다.

**다중 줄 행:** 원본 텍스트 파일에 여러 행에 걸쳐 있는 문자열 값(예: 값 내줄)이 포함된 경우 다중 줄 행을 선택합니다. 이 설정은 DelimitedText 데이터 집합에서만 사용할 수 있습니다.

원본이 올바르게 구성되어 있는지 확인하려면 디버그 모드를 켜고 데이터 미리 보기를 가져옵니다. 자세한 내용은 [디버그 모드를](concepts-data-flow-debug-mode.md)참조하십시오.

> [!NOTE]
> 디버그 모드가 켜져 있으면 디버그 설정의 행 제한 구성이 데이터 미리 보기 중에 소스의 샘플링 설정을 덮어씁니다.

## <a name="projection"></a>프로젝션

데이터 집합의 스키마와 마찬가지로 원본의 프로젝션은 원본 데이터의 데이터 열, 형식 및 형식을 정의합니다. SQL 및 Parquet와 같은 대부분의 데이터 집합 형식의 경우 원본의 프로젝션은 데이터 집합에 정의된 스키마를 반영하도록 고정됩니다. 원본 파일이 강력하게 입력되지 않은 경우(예: Parquet 파일대신 플랫 csv 파일) 소스 변환의 각 필드에 대한 데이터 형식을 정의할 수 있습니다.

![프로젝션 탭의 설정](media/data-flow/source3.png "프로젝션")

텍스트 파일에 정의된 스키마가 없는 경우 데이터 팩터리에서 데이터 형식을 샘플링하고 유추할 수 있도록 **데이터 형식 감지를** 선택합니다. **기본 형식 정의를** 선택하여 기본 데이터 형식을 자동으로 검색합니다.

**스키마 재설정은 프로젝션을** 참조된 데이터 집합에 정의된 것으로 재설정합니다.

다운스트림 파생 열 변환에서 열 데이터 형식을 수정할 수 있습니다. 선택 변환을 사용하여 열 이름을 수정합니다.

### <a name="import-schema"></a>스키마 가져오기

**프로젝션** 탭의 **스키마 가져오기** 단추를 사용하면 활성 디버그 클러스터를 사용하여 스키마 프로젝션을 만들 수 있습니다. 모든 소스 유형에서 사용할 수 있는 스키마를 가져오면 데이터 집합에 정의된 프로젝션이 재정의됩니다. 데이터 집합 개체는 변경되지 않습니다.

이는 복잡한 데이터 구조를 지원하는 Avro 및 CosmosDB와 같은 데이터 집합에서 데이터 집합에 존재하기 위해 스키마 정의가 필요하지 않습니다.

## <a name="optimize-the-source-transformation"></a>소스 변환 최적화

소스 변환에 대한 **최적화** 탭에 **소스** 파티션 유형이 표시될 수 있습니다. 이 옵션은 원본이 Azure SQL Database인 경우에만 사용할 수 있습니다. 이는 Data Factory가 SQL Database 원본에 대해 큰 쿼리를 실행하기 위해 연결을 병렬로 만들려고 하기 때문입니다.

![소스 파티션 설정](media/data-flow/sourcepart3.png "분할")

SQL Database 원본에서 데이터를 분할할 필요는 없지만 파티션은 큰 쿼리에 유용합니다. 열이나 쿼리를 기반으로 파티션을 만들 수 있습니다.

### <a name="use-a-column-to-partition-data"></a>열을 사용하여 데이터 분할

원본 테이블에서 파티션할 열을 선택합니다. 또한 파티션 수를 설정합니다.

### <a name="use-a-query-to-partition-data"></a>쿼리를 사용하여 데이터 분할

쿼리에 따라 연결을 분할하도록 선택할 수 있습니다. WHERE 조건자의 내용을 입력합니다. 예를 들어 1980년 > 연도를 입력합니다.

매핑 데이터 흐름 내의 최적화에 대한 자세한 내용은 [최적화 탭을](concepts-data-flow-overview.md#optimize)참조하십시오.

## <a name="next-steps"></a>다음 단계

[파생 열 변환](data-flow-derived-column.md) 및 선택 [변환](data-flow-select.md)빌드를 시작합니다.
