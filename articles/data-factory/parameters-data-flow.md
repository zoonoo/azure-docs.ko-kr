---
title: 매핑 데이터 흐름 매개 변수화
description: 데이터 팩터리 파이프라인에서 매핑 데이터 흐름을 매개 변수화 하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 8e88e5e8a9fbe1881959c5183dc01b11ac681bdf
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780398"
---
# <a name="parameterizing-mapping-data-flows"></a>매핑 데이터 흐름 매개 변수화

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Azure Data Factory의 데이터 흐름 매핑은 매개 변수 사용을 지원 합니다. 데이터 흐름 정의 내에서 매개 변수를 정의 하 고 식 전체에서 사용 합니다. 매개 변수 값은 데이터 흐름 실행 작업을 통해 호출 파이프라인에 의해 설정 됩니다. 데이터 흐름 활동 식의 값을 설정 하는 세 가지 옵션이 있습니다.

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

## <a name="assign-parameter-values-from-a-pipeline"></a>파이프라인에서 매개 변수 값 할당

매개 변수를 사용 하 여 데이터 흐름을 만들었으면 데이터 흐름 실행 작업을 사용 하 여 파이프라인에서 실행할 수 있습니다. 파이프라인 캔버스에 활동을 추가 하면 활동의 **매개 변수** 탭에서 사용 가능한 데이터 흐름 매개 변수가 표시 됩니다.

매개 변수 값을 할당 하는 경우 spark 유형을 기반으로 하는 [파이프라인 식 언어](control-flow-expression-language-functions.md) 또는 [데이터 흐름 식 언어](data-flow-expression-functions.md) 를 사용할 수 있습니다. 각 매핑 데이터 흐름에는 파이프라인 및 데이터 흐름 식 매개 변수를 조합 하 여 사용할 수 있습니다.

![데이터 흐름 매개 변수 설정](media/data-flow/parameter-assign.png "데이터 흐름 매개 변수 설정")

### <a name="pipeline-expression-parameters"></a>파이프라인 식 매개 변수

파이프라인 식 매개 변수를 사용 하면 다른 파이프라인 작업과 유사한 시스템 변수, 함수, 파이프라인 매개 변수 및 변수를 참조할 수 있습니다. **파이프라인 식**을 클릭 하면 식 작성기를 사용 하 여 식을 입력할 수 있는 측면 탐색이 열립니다.

![데이터 흐름 매개 변수 설정](media/data-flow/parameter-pipeline.png "데이터 흐름 매개 변수 설정")

참조 되는 경우 파이프라인 매개 변수가 계산 된 다음 해당 값이 데이터 흐름 식 언어에 사용 됩니다. 파이프라인 식 유형이 데이터 흐름 매개 변수 유형과 일치 하지 않아도 됩니다. 

#### <a name="string-literals-vs-expressions"></a>문자열 리터럴 및 식

문자열 형식의 파이프라인 식 매개 변수를 할당 하는 경우 기본적으로 따옴표가 추가 되 고 값이 리터럴로 평가 됩니다. 매개 변수 값을 데이터 흐름 식으로 읽으려면 매개 변수 옆의 식 상자를 선택 합니다.

![데이터 흐름 매개 변수 설정](media/data-flow/string-parameter.png "데이터 흐름 매개 변수 설정")

데이터 흐름 매개 변수가 `stringParam` 값 `upper(column1)`이 인 파이프라인 매개 변수를 참조 하는 경우 

- Expression이 선택 된 경우 `$stringParam` 는 column1 all 대문자 값으로 평가 됩니다.
- 식이 선택 되지 않은 경우 (기본 동작)는 `$stringParam` 로 평가 됩니다.`'upper(column1)'`

#### <a name="passing-in-timestamps"></a>타임 스탬프 전달

`pipeline().TriggerTime` 파이프라인 식 언어에서 및 함수와 같은 시스템 변수는 타임 스탬프를 `utcNow()` ' yyyy-mm-dd\'T\'HH: MM: ss 형식의 문자열로 반환 합니다. SSSSSSZ '. 이러한 데이터 흐름을 타임 스탬프 형식의 데이터 흐름 매개 변수로 변환 하려면 문자열 보간을 사용 하 여 원하는 타임 스탬프 `toTimestamp()` 를 함수에 포함 합니다. 예를 들어 파이프라인 트리거 시간을 데이터 흐름 매개 변수로 변환 하려면를 사용할 `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')`수 있습니다. 

![데이터 흐름 매개 변수 설정](media/data-flow/parameter-timestamp.png "데이터 흐름 매개 변수 설정")

> [!NOTE]
> 데이터 흐름은 최대 3 밀리초 숫자만 지원할 수 있습니다. 함수 `left()` 는 추가 숫자를 잘라내는 데 사용 됩니다.

#### <a name="pipeline-parameter-example"></a>파이프라인 매개 변수 예제

문자열 형식의 파이프라인 매개 변수를 `intParam` `@pipeline.parameters.pipelineParam`참조 하는 정수 매개 변수가 있다고 가정 합니다. 

![데이터 흐름 매개 변수 설정](media/data-flow/parameter-pipeline-2.png "데이터 흐름 매개 변수 설정")

`@pipeline.parameters.pipelineParam`런타임에의 `abs(1)` 값이 할당 됩니다.

![데이터 흐름 매개 변수 설정](media/data-flow/parameter-pipeline-4.png "데이터 흐름 매개 변수 설정")

가 `$intParam` 파생 열과 같은 식에서 참조 되는 경우 반환 `abs(1)` `1`을 평가 합니다. 

![데이터 흐름 매개 변수 설정](media/data-flow/parameter-pipeline-3.png "데이터 흐름 매개 변수 설정")

### <a name="data-flow-expression-parameters"></a>데이터 흐름 식 매개 변수

**데이터 흐름 식** 선택은 데이터 흐름 식 작성기를 엽니다. 데이터 흐름 전체에서 함수, 기타 매개 변수 및 정의 된 스키마 열을 참조할 수 있습니다. 이 식은 참조 될 때 그대로 계산 됩니다.

> [!NOTE]
> 잘못 된 식을 전달 하거나 해당 변환에 존재 하지 않는 스키마 열을 참조 하는 경우 매개 변수는 null로 평가 됩니다.


### <a name="passing-in-a-column-name-as-a-parameter"></a>열 이름을 매개 변수로 전달

일반적인 패턴은 열 이름을 매개 변수 값으로 전달 하는 것입니다. 열이 데이터 흐름 스키마에 정의 되어 있는 경우이를 문자열 식으로 직접 참조할 수 있습니다. 열이 스키마에 정의 되어 있지 않은 경우 `byName()` 함수를 사용 합니다. 과 `toString()`같은 캐스팅 함수를 사용 하 여 열을 적절 한 형식으로 캐스팅 해야 합니다.

예를 들어 매개 변수 `columnName`를 기반으로 하 여 문자열 열을 매핑하려는 경우 파생 열 변환을와 동일 하 게 `toString(byName($columnName))`추가할 수 있습니다.

![열 이름을 매개 변수로 전달](media/data-flow/parameterize-column-name.png "열 이름을 매개 변수로 전달")

## <a name="next-steps"></a>다음 단계
* [데이터 흐름 작업 실행](control-flow-execute-data-flow-activity.md)
* [제어 흐름 식](control-flow-expression-language-functions.md)
