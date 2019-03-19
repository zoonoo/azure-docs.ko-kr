---
title: Azure Data Factory Mapping Data Flow 데이터 세트
description: Azure 데이터 팩터리 매핑 데이터 흐름에 sepecific 데이터 집합의 호환성
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: ad6cfdad519ab3901c58979970ea07439b3106e9
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726926"
---
# <a name="mapping-data-flow-datasets"></a>Mapping Data Flow 데이터 세트

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

데이터 세트는 파이프라인에서 작업 중인 데이터의 셰이프를 정의하는 Data Factory 구문입니다. Data Flow에서 행 및 열 수준 데이터에는 세부적인 데이터 세트 정의가 필요합니다. 제어 흐름 파이프라인에 사용된 데이터 세트의 경우 데이터를 이 수준까지 파악하지 않아도 됩니다.

데이터 흐름에서 데이터 집합은 원본 및 싱크 변환에 사용 됩니다. 기본 데이터 스키마 정의에 사용 됩니다. 데이터에 스키마가 없는 경우 원본 및 싱크에 대해 스키마 드리프트를 설정할 수 있습니다. 데이터 세트에서 정의된 스키마를 사용하면 연관된 연결된 서비스의 관련 데이터 형식(type), 데이터 형식(format), 파일 위치 및 연결 정보가 제공됩니다. 데이터 집합에서 메타 데이터에 소스 변환에서 "프로젝션" 원본으로 나타납니다. 데이터 집합의 스키마 소스 변환에서 프로젝션에 정의 된 이름 및 형식을 사용 하 여 데이터의 데이터 흐름 표현을 나타냅니다 실제 데이터 형식 및 셰이프를 나타냅니다.

## <a name="dataset-types"></a>데이터 세트 형식

현재 데이터 흐름에는 다음 4개의 데이터 세트 형식이 있습니다.

* Azure SQL DB
* Azure SQL DW
* Parquet (ADLS Blob에서)
* 구분 기호로 분리 된 텍스트 (ADLS 및 Blob)

별도 데이터 흐름 데이터 집합을 *원본 유형* 에서 합니다 *연결 된 서비스 연결 유형이*. 일반적으로 Data Factory에서 연결 형식(Blob, ADLS 등)을 선택한 다음, 데이터 세트의 파일 형식을 정의합니다. Data Flow 내에서 다른 연결된 서비스 연결 형식과 연결될 수 있는 원본 형식을 선택합니다.

![원본 변환 옵션](media/data-flow/dataset1.png "원본")

## <a name="data-flow-compatible-datasets"></a>Data Flow 호환 데이터 세트

새 데이터 세트를 만드는 경우 패널의 오른쪽 위에 “Data Flow 호환” 확인란이 있습니다. 해당 단추를 클릭하면 Data Flow에서 사용할 수 있는 데이터 세트만 필터링됩니다. 

## <a name="import-schemas"></a>스키마 가져오기

Data Flow 데이터 세트의 스키마를 가져오는 경우 스키마 가져오기 단추가 표시됩니다. 해당 단추를 클릭하면 원본에서 가져오거나 로컬 파일에서 가져오는 두 가지 옵션이 표시됩니다. 대부분의 경우 원본에서 직접 스키마를 가져옵니다. 그러나 기존 스키마 파일(Parquet 파일 또는 헤더가 있는 CSV)이 있는 경우 해당 로컬 파일을 가리킬 수 있으며, Data Factory는 이 스키마 파일을 기준으로 스키마를 정의합니다.

## <a name="delimited-text-dataset"></a>구분 기호로 분리 된 텍스트 데이터 집합

구분 기호로 분리 된 텍스트 데이터 집합을 설정 하거나 단일 구분 기호를 처리 하는 구분 기호 ('\t' TSV',' CSV에 대 한 ' |'...) 또는 구분 기호에 대 한 여러 문자를 사용 합니다. 헤더 행 설정/해제를 설정 하 고 데이터 형식을 자동으로 감지 소스 변환으로 이동 합니다.

## <a name="next-steps"></a>다음 단계

먼저 [새 데이터 흐름을 만드는](data-flow-create.md) 소스 변환을 추가 합니다. 원본에 대 한 데이터 집합을 구성 합니다.

사용 합니다 [복사 작업](copy-activity-overview.md) 데이터를 가져오는 모든 ADF에서 데이터 원본 및 Blob 또는 ADLS에서 데이터 흐름으로 액세스할 수 있도록 준비 합니다.

