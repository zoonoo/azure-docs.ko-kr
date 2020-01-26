---
title: 매핑 데이터 흐름 매개 변수화
description: 데이터 팩터리 파이프라인에서 매핑 데이터 흐름을 매개 변수화 하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c717d9ab44493d15589030073cd2ab260ef52e1c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760218"
---
# <a name="parameterizing-mapping-data-flows"></a>매핑 데이터 흐름 매개 변수화

Azure Data Factory의 데이터 흐름 매핑은 매개 변수 사용을 지원 합니다. 데이터 흐름 정의 내에서 매개 변수를 정의할 수 있습니다. 그러면 식 전체에서 사용할 수 있습니다. 매개 변수 값은 데이터 흐름 실행 작업을 통해 호출 파이프라인에서 설정할 수 있습니다. 데이터 흐름 활동 식의 값을 설정 하는 세 가지 옵션이 있습니다.

* 파이프라인 제어 흐름 식 언어를 사용 하 여 동적 값 설정
* 데이터 흐름 식 언어를 사용 하 여 동적 값 설정
* 식 언어를 사용 하 여 정적 리터럴 값 설정

이 기능을 사용 하 여 데이터 흐름을 범용이 고 유연 하며 재사용 가능 하 게 만들 수 있습니다. 이러한 매개 변수를 사용 하 여 데이터 흐름 설정 및 식을 매개 변수화 할 수 있습니다.

## <a name="create-parameters-in-a-mapping-data-flow"></a>매핑 데이터 흐름에서 매개 변수 만들기

데이터 흐름에 매개 변수를 추가 하려면 데이터 흐름 캔버스의 빈 부분을 클릭 하 여 일반 속성을 표시 합니다. 설정 창에 **매개 변수**라는 탭이 표시 됩니다. 새로 **만들기를 선택 하** 여 새 매개 변수를 생성 합니다. 각 매개 변수에 대해 이름을 할당 하 고, 형식을 선택 하 고, 필요에 따라 기본값을 설정 해야 합니다.

![데이터 흐름 매개 변수 만들기](media/data-flow/create-params.png "데이터 흐름 매개 변수 만들기")

## <a name="use-parameters-in-a-mapping-data-flow"></a>매핑 데이터 흐름에서 매개 변수 사용 

모든 데이터 흐름 식에서 매개 변수를 참조할 수 있습니다. 매개 변수는 $ 및로 시작 되며 변경할 수 없습니다. 식 작성기 내에서 사용할 수 있는 매개 변수 목록은 **매개 변수** 탭에서 찾을 수 있습니다.

![데이터 흐름 매개 변수 식](media/data-flow/parameter-expression.png "데이터 흐름 매개 변수 식")

**새 매개 변수** 를 선택 하 고 이름 및 유형을 지정 하 여 추가 매개 변수를 빠르게 추가할 수 있습니다.

![데이터 흐름 매개 변수 식](media/data-flow/new-parameter-expression.png "데이터 흐름 매개 변수 식")

### <a name="passing-in-a-column-name-as-a-parameter"></a>열 이름을 매개 변수로 전달

일반적인 패턴은 열 이름을 매개 변수 값으로 전달 하는 것입니다. 매개 변수와 연결 된 열을 참조 하려면 `byName()` 함수를 사용 합니다. `toString()`와 같은 캐스팅 함수를 사용 하 여 열을 적절 한 형식으로 캐스팅 해야 합니다.

예를 들어 `columnName`매개 변수를 기반으로 문자열 열을 매핑하려는 경우 파생 열 변환을 `toString(byName($columnName))`에 추가할 수 있습니다.

![열 이름을 매개 변수로 전달](media/data-flow/parameterize-column-name.png "열 이름을 paramete로 전달")

## <a name="assign-parameter-values-from-a-pipeline"></a>파이프라인에서 매개 변수 값 할당

매개 변수를 사용 하 여 데이터 흐름을 만들었으면 데이터 흐름 실행 작업을 사용 하 여 파이프라인에서 실행할 수 있습니다. 파이프라인 캔버스에 활동을 추가 하면 활동의 **매개 변수** 탭에서 사용 가능한 데이터 흐름 매개 변수가 표시 됩니다.

![데이터 흐름 매개 변수 설정](media/data-flow/parameter-assign.png "데이터 흐름 매개 변수 설정")

매개 변수 데이터 형식이 문자열인 경우 입력란을 클릭 하 여 매개 변수 값을 설정 하면 파이프라인 또는 데이터 흐름 식을 입력 하도록 선택할 수 있습니다. 파이프라인 식을 선택 하는 경우 파이프라인 식 패널이 표시 됩니다. `'@{<expression>}'`를 사용 하 여 문자열 보간 구문 내에 파이프라인 함수를 포함 해야 합니다. 예를 들면 다음과 같습니다.

```'@{pipeline().RunId}'```

매개 변수가 문자열 형식이 아닌 경우 항상 데이터 흐름 식 작성기가 표시 됩니다. 여기에서 매개 변수의 데이터 형식과 일치 하도록 하려는 식 또는 리터럴 값을 입력할 수 있습니다. 다음은 식 작성기의 데이터 흐름 식과 리터럴 문자열의 예입니다.

* ```toInteger(Role)```
* ```'this is my static literal string'```

각 매핑 데이터 흐름에는 파이프라인 및 데이터 흐름 식 매개 변수를 조합 하 여 사용할 수 있습니다. 

![데이터 흐름 매개 변수 샘플](media/data-flow/parameter-example.png "데이터 흐름 매개 변수 샘플")



## <a name="next-steps"></a>다음 단계
* [데이터 흐름 작업 실행](control-flow-execute-data-flow-activity.md)
* [제어 흐름 식](control-flow-expression-language-functions.md)
