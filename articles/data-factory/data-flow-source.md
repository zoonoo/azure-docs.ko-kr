---
title: Azure Data Factory Mapping Data Flow 원본 변환
description: Azure Data Factory Mapping Data Flow 원본 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 38a01b4f81b76ba90a5fda4909d0e65e6307057e
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408717"
---
# <a name="mapping-data-flow-source-transformation"></a>Mapping Data Flow 원본 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

원본 변환은 데이터를 데이터 흐름에 가져오는 데 사용하려는 데이터 원본을 구성합니다. 둘 이상의 원본 변환을 단일 Data Flow에서 수행할 수 있습니다. 항상 먼저 원본으로 Data Flow를 디자인합니다.

> [!NOTE]
> 모든 Data Flow에는 하나 이상의 원본 변환이 필요합니다. 데이터 변환을 완료하는 데 필요한 만큼 추가 소스를 추가합니다. 조인 또는 결합 변환을 사용하여 이러한 원본을 함께 조인할 수 있습니다.

![원본 변환 옵션](media/data-flow/source.png "원본")

각 Data Flow 원본 변환은 쓰거나 읽을 데이터의 모양과 위치를 정의하는 정확히 하나의 Data Factory 데이터 세트에 연결되어야 합니다. 원본에 와일드카드와 파일 목록을 사용하면 한 번에 둘 이상의 파일을 사용할 수 있습니다.

## <a name="data-flow-staging-areas"></a>Data Flow 스테이징 영역

Data Flow는 모두 Azure에 있는 "스테이징" 데이터 세트에 작동합니다. 이러한 Data Flow 데이터 세트는 데이터 스테이징에서 데이터 변환이 수행되는 데 사용됩니다. Data Factory는 거의 80가지의 다른 기본 커넥터에 액세스할 수 있습니다. 댜른 원본의 데이터를 Data Flow에 포함하려면 먼저 복사 작업을 사용하여 해당 Data Flow 데이터 세트 스테이징 영역 중 하나로 해당 데이터를 스테이징합니다.

## <a name="options"></a>옵션

### <a name="allow-schema-drift"></a>스키마 드리프트 허용
원본 열이 자주 변경되는 경우 스키마 드리프트 허용을 선택합니다. 이 설정을 사용하면 원본의 모든 수신 필드가 변환을 통해 싱크로 이동될 수 있습니다.

### <a name="validate-schema"></a>스키마 유효성 검사

![공개 원본](media/data-flow/source1.png "공개 원본 1")

원본 데이터의 수신 버전이 정의된 스키마와 일치하지 않으면 Data Flow의 실행이 실패합니다.

### <a name="sampling"></a>샘플링
원본의 행의 수를 제한하려면 샘플링을 사용합니다.  이 방법은 테스트 및 디버깅 목적으로 원본 데이터의 샘플만 필요한 경우에 유용합니다.

## <a name="define-schema"></a>스키마 정의

![원본 변환](media/data-flow/source2.png "원본 2")

강력한 형식이 아닌 원본 파일 형식(예: Parquet 파일 대신 플랫 파일)의 경우, 원본 변환의 이 부분에서 각 필드의 데이터 형식을 정의해야 합니다. 나중에 선택 변환에서 열 이름을 변경하고, 파생 열 변환에서 데이터 형식을 변경할 수 있습니다. 

![원본 변환](media/data-flow/source003.png "데이터 형식")

강력한 형식의 원본의 경우 다음을 수정할 수 있습니다. 

### <a name="optimize"></a>최적화

![원본 파티션](media/data-flow/sourcepart.png "분할")

원본 변환의 최적화 탭에 "원본"이라는 추가 분할 형식이 표시됩니다. 이 형식은 원본으로 Azure SQL DB를 선택한 경우에만 표시됩니다. ADF는 연결을 병렬화하여 Azure SQL DB 원본에 대해 대규모 쿼리를 실행하려고 하기 때문입니다.

SQL DB 원본의 데이터 분할은 옵션이지만 대규모 쿼리에 유용합니다. 다음 두 가지 옵션을 사용할 수 있습니다.

### <a name="column"></a>열

원본 테이블에서 분할할 열을 선택합니다. 최대 연결 수도 설정해야 합니다.

### <a name="query-condition"></a>쿼리 조건

필요에 따라 쿼리를 기준으로 연결을 분할하도록 선택할 수 있습니다. 이 옵션에서는 WHERE 조건자의 내용에 배치하기만 하면 됩니다. (예: year > 1980)

## <a name="source-file-management"></a>원본 파일 관리
![새 원본 설정](media/data-flow/source2.png "새 설정")

* 원본 폴더에서 패턴과 일치하는 파일 시리지를 선택하기 위한 와일드카드 경로. 데이터 세트 정의에 설정한 파일은 모든 재정의됩니다.
* 파일 목록. 파일 세트와 동일합니다. 처리할 상대 경로 파일 목록을 사용하여 만든 텍스트 파일을 가리킵니다.
* 파일 이름을 저장할 열은 데이터의 열에 원본 파일의 이름을 저장합니다. 파일 이름 문자열을 저장하려면 여기에 새 이름을 입력합니다.
* 완료 후(Data Flow가 실행된 후에 원본 파일에 대해 아무 작업도 수행하지 않도록 선택할 수 있음) 원본 파일을 삭제하거나 원본 파일을 이동합니다. 이동 경로는 상대 경로입니다.

### <a name="sql-datasets"></a>SQL 데이터 세트

Azure SQL DB 또는 Azure SQL DW를 원본으로 사용하는 경우 추가 옵션이 제공됩니다.

* 쿼리: 원본의 SQL 쿼리를 입력합니다. 쿼리를 설정하면 데이터 세트에서 선택한 모든 테이블이 재정의됩니다. 여기에서 Order By 절은 지원되지 않습니다.

* 일괄 처리 크기: 일괄 처리 크기를 입력하여 큰 데이터를 일괄로 처리할 수 있는 읽기로 청크합니다.

> [!NOTE]
> 이 파일 작업 설정은 파이프 라인에서 Data Flow 실행 작업을 사용하여 파이프라인 실행(파이프라인 디버그 또는 실행)에서 Data Flow가 실행될 때만 실행됩니다. Data Flow 디버그 모드에서는 파일 작업이 실행되지 않습니다.

### <a name="projection"></a>프로젝션

![프로젝션](media/data-flow/source3.png "프로젝션")

데이터 세트의 스키마와 마찬가지로, 원본의 프로젝션은 원본 데이터의 데이터 열, 데이터 유형 및 데이터 형식을 정의합니다. 텍스트 파일에 정의된 스키마가 없는 경우 "데이터 형식 검색"을 클릭하여 ADF에 데이터 형식을 샘플링하고 유추하도록 요청합니다. "기본 형식 정의" 단추를 사용하여 자동으로 검색하도록 기본 데이터 형식을 설정할 수 있습니다. 후속 파생 열 변환에서 열 데이터 형식을 수정할 수 있습니다. 열 이름은 선택 변환을 사용하여 수정할 수 있습니다.

![기본 형식](media/data-flow/source2.png "기본 형식")

## <a name="next-steps"></a>다음 단계

[파생 열](data-flow-derived-column.md) 및 [선택](data-flow-select.md)으로 데이터 변환 구축 시작
