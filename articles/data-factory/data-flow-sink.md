---
title: Azure Data Factory Mapping Data Flow 싱크 변환
description: Azure Data Factory Mapping Data Flow 싱크 변환
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a56f391aa76bd1216fd51d516adb836a2093bcba
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371142"
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

## <a name="field-mapping"></a>필드 매핑

싱크 변환의 매핑 탭에서 들어오는(왼쪽) 열을 대상(오른쪽)에 매핑할 수 있습니다. 데이터 흐름을 파일에 싱크하는 경우 ADF는 항상 폴더에 새 파일을 씁니다. 데이터베이스 데이터 세트에 매핑하는 경우 이 스키마로 새 테이블을 생성하거나(저장 정책을 "덮어쓰기"로 설정) 기존 테이블에 새 행을 삽입하고 필드를 기존 스키마에 매핑하도록 선택할 수 있습니다.

매핑 테이블에 한 번의 클릭을 사용 하 여 여러 열을 연결, 여러 열을 사용 중인 또는 여러 행을 동일한 열 이름을 매핑할 다중 선택에 사용할 수 있습니다.

항상 들어오는 필드 집합을 수행 하 고으로 대상에 매핑할 수 하려는 경우-"스키마 드리프트 허용" 설정을 인 합니다.

![필드 매핑](media/data-flow/multi1.png "여러 옵션")

열 매핑을 다시 설정하려는 경우 "다시 매핑" 단추를 눌러 매핑을 다시 설정합니다.

![싱크 옵션](media/data-flow/sink1.png "싱크 1")

![싱크 옵션](media/data-flow/sink2.png "싱크")

* 스키마 드리프트 허용 및 스키마 유효성 검사 옵션은 이제 싱크에서 사용할 수 있습니다. 이를 통해 ADF가 유연한 스키마 정의를 완전히 수락하거나(스키마 드리프트) 스키마가 변경된 경우 싱크가 실패하게 하도록(스키마 유효성 검사) 지시할 수 있습니다.

* 폴더를 선택 취소합니다. ADF는 해당 대상 폴더에 대상 파일을 쓰기 전에 싱크 폴더 콘텐츠를 자릅니다.

## <a name="file-name-options"></a>파일 이름 옵션

   * Default: Spark에서 PART 기본값에 따라 파일 이름을 지정하도록 허용
   * 패턴 출력 파일에 대 한 패턴을 입력 합니다. 예를 들어 "loans [n]"는 만들 loans1.csv, loans2.csv,...
   * 파티션 기준: 파티션별 파일 이름 입력
   * 열의 데이터로: 출력 파일을 열 값으로 설정

> [!NOTE]
> 파일 작업은 데이터 흐름 디버그 모드가 아닌 데이터 흐름 실행 작업을 실행할 때만 실행됩니다.

## <a name="database-options"></a>데이터베이스 옵션

* Insert, update, delete, upsert를 허용 합니다. 삽입을 허용 하는 기본값은입니다. 업데이트, 삽입 또는 삭제 행 원한다 면 해당 특정 작업에 대 한 태그 행에는 alter 행 변환의 먼저 추가 해야 합니다. "허용 삽입"을 해제 하면 원본에서 새 행을 삽입에서 ADF 중지 됩니다.
* (대상 테이블에서 데이터 흐름을 완료 하기 전에 모든 행을 제거) 하는 테이블 자르기
* 테이블을 (대상 테이블의 드롭/생성 데이터 흐름을 완료 하기 전에 수행)를 다시 만듭니다.
* 대량 데이터 로드의 일괄 처리 크기 버킷 쓰기 청크로 숫자를 입력
* 준비를 사용 합니다. 이 싱크 데이터 집합으로 Azure Data Warehouse에 로드 하는 경우 Polybase를 사용 하는 ADF 지시

> [!NOTE]
> 데이터 흐름에서 새 테이블 이름을 가진 싱크 변환에서 데이터 집합을 설정 하 여 대상 데이터베이스에서 새 테이블 정의 만들려면 ADF를 요청할 수 있습니다. SQL 데이터 집합의 테이블 이름 아래 "편집"을 클릭 하 고 새 테이블 이름을 입력 합니다. 그런 다음 싱크 변환에서 "스키마 드리프트 허용"으로 설정 합니다. Seth "스키마 가져오기" None으로 설정 합니다.

![소스 변환 스키마](media/data-flow/dataset2.png "SQL 스키마")

![SQL 싱크 옵션](media/data-flow/alter-row2.png "SQL 옵션")

> [!NOTE]
> 를 업데이트 하거나 데이터베이스 싱크에에서 행을 삭제할 때 키 열을 설정 해야 합니다. 이 따라서 Alter 행이 DML에서 고유 행을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

데이터 흐름을 만들었으므로 이제 [파이프라인에 데이터 흐름 실행 작업](concepts-data-flow-overview.md)을 추가합니다.
