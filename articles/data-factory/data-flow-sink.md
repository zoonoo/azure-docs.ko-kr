---
title: Azure Data Factory Mapping Data Flow 싱크 변환
description: Azure Data Factory Mapping Data Flow 싱크 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 381dc2f9f6d3a074af00ba047472719c086f5811
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408411"
---
# <a name="mapping-data-flow-sink-transformation"></a>Mapping Data Flow 싱크 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![싱크 옵션](media/data-flow/windows1.png "싱크 1")

데이터 흐름 변환이 완료되면 변환된 데이터를 대상 데이터 세트로 싱크할 수 있습니다. 싱크 변환에서 대상 출력 데이터에 사용하려는 데이터 세트 정의를 선택할 수 있습니다. 데이터 흐름에 필요한 만큼 싱크 변환을 유지할 수 있습니다.

들어오는 데이터 변경 및 스키마 드리프트를 고려하는 일반적인 방법은 출력 데이터 세트에 정의된 스키마 없이 폴더에 출력 데이터를 싱크하는 것입니다. 또한 원본에서 “스키마 드리프트 허용”을 선택하여 원본의 모든 열 변경을 추가적으로 고려한 후 링크의 모든 필드를 자동으로 매핑할 수 있습니다.

데이터 세트로 싱크할 때 데이터 흐름을 덮어쓸지, 추가할지 또는 실패하도록 할지를 선택할 수 있습니다.

들어오는 모든 필드를 싱크하도록 "자동 매핑"을 선택할 수도 있습니다. 대상으로 싱크하려는 필드를 선택하려고 하거나 대상에 있는 필드의 이름을 변경하려는 경우 "자동 매핑"에 대해 “꺼짐”을 선택하고 매핑 탭을 클릭하여 출력 필드를 매핑합니다.

![싱크 옵션](media/data-flow/sink2.png "싱크 2")

## <a name="output-to-one-file"></a>하나의 파일로 출력
Azure Storage Blob 또는 Data Lake 싱크 형식의 경우, 변환된 데이터를 폴더에 출력합니다. Spark에서는 싱크 변환에 사용되는 분할 체계를 기준으로 분할된 출력 데이터 파일을 생성합니다. "최적화" 탭을 클릭하여 파티션 구성표를 설정할 수 있습니다. 단일 파일에 출력을 병합하는 ADF를 원할 경우 "단일 파티션" 라디오 단추를 클릭합니다.

![싱크 옵션](media/data-flow/opt001.png "싱크 옵션")

### <a name="output-settings"></a>출력 설정

덮어쓰기를 수행하면 테이블이 있는 경우 해당 테이블을 자른 후 다시 만들고 데이터를 로드합니다. 추가를 수행하면 새 행이 삽입됩니다. 데이터 세트 테이블 이름의 테이블이 대상 ADW에 전혀 존재하지 않으면 데이터 흐름은 테이블을 만든 후 데이터를 로드합니다.

"자동 매핑"을 선택 취소하면 수동으로 필드를 대상 테이블에 매핑할 수 있습니다.

![ADW 싱크 옵션](media/data-flow/adw2.png "adw 싱크")

#### <a name="field-mapping"></a>필드 매핑

싱크 변환의 매핑 탭에서 들어오는(왼쪽) 열을 대상(오른쪽)에 매핑할 수 있습니다. 데이터 흐름을 파일에 싱크하는 경우 ADF는 항상 폴더에 새 파일을 씁니다. 데이터베이스 데이터 세트에 매핑하는 경우 이 스키마로 새 테이블을 생성하거나(저장 정책을 "덮어쓰기"로 설정) 기존 테이블에 새 행을 삽입하고 필드를 기존 스키마에 매핑하도록 선택할 수 있습니다.

매핑 테이블에서 다중 선택을 사용하여 클릭 한 번으로 여러 열을 연결하거나, 여러 열의 연결을 해제하거나, 여러 행을 동일한 열 이름에 매핑할 수 있습니다.

![필드 매핑](media/data-flow/multi1.png "여러 옵션")

열 매핑을 다시 설정하려는 경우 "다시 매핑" 단추를 눌러 매핑을 다시 설정합니다.

![연결](media/data-flow/maxcon.png "연결")

### <a name="updates-to-sink-transformation-for-adf-v2-ga-version"></a>ADF V2 GA 버전의 싱크 변환 업데이트

![싱크 옵션](media/data-flow/sink1.png "싱크 1")

![싱크 옵션](media/data-flow/sink2.png "싱크")

* 스키마 드리프트 허용 및 스키마 유효성 검사 옵션은 이제 싱크에서 사용할 수 있습니다. 이를 통해 ADF가 유연한 스키마 정의를 완전히 수락하거나(스키마 드리프트) 스키마가 변경된 경우 싱크가 실패하게 하도록(스키마 유효성 검사) 지시할 수 있습니다.

* 폴더를 선택 취소합니다. ADF는 해당 대상 폴더에 대상 파일을 쓰기 전에 싱크 폴더 콘텐츠를 자릅니다.

* 파일 이름 옵션

   * Default: Spark에서 PART 기본값에 따라 파일 이름을 지정하도록 허용
   * 패턴 출력 파일의 이름 입력
   * 파티션 기준: 파티션별 파일 이름 입력
   * 열의 데이터로: 출력 파일을 열 값으로 설정

> [!NOTE]
> 파일 작업은 데이터 흐름 디버그 모드가 아닌 데이터 흐름 실행 작업을 실행할 때만 실행됩니다.

SQL 싱크 형식을 사용하여 다음을 설정할 수 있습니다.

* 테이블 자르기
* 테이블 다시 만들기(삭제/만들기 수행)
* 대량 데이터 로드의 일괄 처리 크기 청크에 대한 버킷 쓰기 수를 입력합니다.

![필드 매핑](media/data-flow/sql001.png "SQL 옵션")

## <a name="next-steps"></a>다음 단계

데이터 흐름을 만들었으므로 이제 [파이프라인에 데이터 흐름 실행 작업](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview)을 추가합니다.
