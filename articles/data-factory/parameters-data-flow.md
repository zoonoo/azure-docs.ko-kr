---
title: Azure Data Factory 데이터 흐름 매개 변수 매핑
description: Data factory 파이프라인에서 매핑 데이터 흐름을 매개 변수화 하는 방법 알아보기
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: ef97f17bf159511ce94f90cd00623e05489acb92
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274688"
---
# <a name="mapping-data-flow-parameters"></a>데이터 흐름 매개 변수 매핑

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory의 데이터 흐름에서는 매핑 매개 변수 사용을 지원합니다. 식에서 사용할 수 있는 데이터 흐름 정의 내에서 매개 변수를 정의할 수 있습니다. 데이터 흐름 실행 활동을 통해 호출 파이프라인에서 매개 변수 값을 설정할 수 있습니다. 작업 식의 데이터 흐름에서 값을 설정 하는 것에 대 한 세 가지 옵션이 있습니다.

* 파이프라인 제어 흐름 식 언어를 사용 하 여 동적 값을 설정 하려면
* 데이터 흐름 식 언어를 사용 하 여 동적 값을 설정 하려면
* 식 언어 중 하나를 사용 하 여 정적 리터럴 값을 설정 하려면

범용, 유연 하 고 다시 사용할 수 있는 데이터 흐름을 확인 하려면이 기능을 사용 합니다. 데이터 흐름 설정 및 이러한 매개 변수를 사용 하 여 식 매개 변수화 할 수 있습니다.

> [!NOTE]
> 파이프라인 제어 흐름 식도 사용 하려면 데이터 흐름 매개 변수의 문자열 형식 이어야 합니다.

## <a name="create-parameters-in-mapping-data-flow"></a>매개 변수를 만들 데이터 흐름 매핑

데이터 흐름에 매개 변수를 추가, 일반 속성을 확인 하려면 데이터 흐름 캔버스의 빈 부분을 클릭 합니다. 설정 창에서 'Parameters' 라는 탭이 표시 됩니다. 새 매개 변수를 생성 하는 'New' 단추를 클릭 합니다. 각 매개 변수에 대 한 이름을 할당, 형식을 선택 하며 필요에 따라 기본값을 설정 합니다.

![데이터 흐름 매개 변수를 만듭니다](media/data-flow/create-params.png "매개 변수 데이터 흐름 만들기")

모든 데이터 흐름 식에서 매개 변수를 활용할 수 있습니다. 매개 변수 $로 시작 되며 변경할 수 없습니다. 'Parameters' 탭의 식 작성기 내에서 사용할 수 있는 매개 변수 목록을 찾을 수 있습니다.

![데이터 흐름 매개 변수 식을](media/data-flow/parameter-expression.png "데이터 흐름 매개 변수 식")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>파이프라인에서 데이터 흐름 매핑 매개 변수를 설정 합니다.

매개 변수를 사용 하 여 데이터 흐름을 만든 후 데이터 흐름 실행 활동을 사용 하 여 파이프라인에서 실행할 수 있습니다. 파이프라인 캔버스에 작업을 추가한 후 나타납니다 사용 가능한 데이터를 사용 하 여 활동의 '매개 변수' 탭에서 흐름 매개 변수입니다.

![데이터 흐름 매개 변수를 설정](media/data-flow/parameter-assign.png "데이터 흐름 매개 변수를 설정 합니다.")

에 매개 변수 데이터 형식이 인 경우 문자열 매개 변수 값을 설정 하려면 입력란을 클릭 하면 파이프라인 또는 데이터 흐름 식 입력을 선택할 수 있습니다. 식 파이프라인을 선택 하면 파이프라인 식 패널에 나타납니다. 문자열 보간 구문은 사용 내 파이프라인 함수를 포함 해야 합니다. ' @ {0}<expression>}', 예를 들어:

```'@{pipeline().RunId}'```

문자열 형식의 매개 변수 없는 경우 항상 나타납니다 데이터 흐름 식 작성기를 사용 합니다. 여기에서 모든 식 또는 매개 변수의 데이터 형식과 일치 하는 한다고 리터럴 값을 입력할 수 있습니다. 식 작성기에서 데이터 흐름 식 및 리터럴 문자열의 예는 다음과 같습니다.

* ```toInteger(Role)```
* ```'this is my static literal string'```

각 매핑 데이터 흐름 파이프라인 및 데이터 흐름 식 매개 변수 조합을 가질 수 있습니다. 

![데이터 흐름 매개 변수 예제](media/data-flow/parameter-example.png "데이터 흐름 parameters 샘플")



## <a name="next-steps"></a>다음 단계
* [실행 데이터 흐름 작업](control-flow-execute-data-flow-activity.md)
* [제어 흐름 식도](control-flow-expression-language-functions.md)
