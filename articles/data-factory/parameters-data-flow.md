---
title: 매핑 데이터 흐름 매개 변수화
description: 데이터 팩터리 파이프라인에서 매핑 데이터 흐름을 매개변수화하는 방법 알아보기
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 82660cdb4ab6523bae7608fe3b071f20cb3603f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419173"
---
# <a name="parameterizing-mapping-data-flows"></a>매핑 데이터 흐름 매개 변수화

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Azure 데이터 팩터리의 데이터 흐름 매핑은 매개 변수 사용을 지원합니다. 데이터 흐름 정의 내에서 매개 변수를 정의할 수 있으며, 이 매개 변수는 식 전체에서 사용할 수 있습니다. 매개 변수 값은 데이터 흐름 실행 활동을 통해 호출 파이프라인에 의해 설정할 수 있습니다. 데이터 흐름 활동 식의 값을 설정하는 세 가지 옵션이 있습니다.

* 파이프라인 컨트롤 흐름 식 언어를 사용하여 동적 값 설정
* 데이터 흐름 표현식 언어를 사용하여 동적 값 설정
* 두 식 언어 중 하나를 사용하여 정적 리터럴 값을 설정합니다.

이 기능을 사용하여 데이터 흐름을 범용적이고 유연하며 재사용할 수 있도록 합니다. 이러한 매개 변수를 사용하여 데이터 흐름 설정 및 식을 매개변수화할 수 있습니다.

## <a name="create-parameters-in-a-mapping-data-flow"></a>매핑 데이터 흐름에서 매개 변수 만들기

데이터 흐름에 매개 변수를 추가하려면 데이터 흐름 캔버스의 빈 부분을 클릭하여 일반 속성을 확인합니다. 설정 창에 **매개 변수라는**탭이 표시됩니다. 새 매개변수를 생성하려면 **새로** 를 선택합니다. 각 매개 변수에 대해 이름을 할당하고 형식을 선택하고 선택적으로 기본값을 설정해야 합니다.

![데이터 흐름 매개변수 만들기](media/data-flow/create-params.png "데이터 흐름 매개변수 만들기")

## <a name="use-parameters-in-a-mapping-data-flow"></a>매핑 데이터 흐름에서 매개 변수 사용 

매개 변수는 모든 데이터 흐름 식에서 참조할 수 있습니다. 매개 변수는 $로 시작하고 변경할 수 없습니다. 매개 변수 탭 에서 식 작성기 내에서 사용 가능한 매개 변수 목록을 찾을 **수 있습니다.**

![데이터 흐름 매개 변수 표현식](media/data-flow/parameter-expression.png "데이터 흐름 매개 변수 표현식")

새 매개 변수를 선택하고 이름과 유형을 지정하여 매개 **변수를** 빠르게 추가할 수 있습니다.

![데이터 흐름 매개 변수 표현식](media/data-flow/new-parameter-expression.png "데이터 흐름 매개 변수 표현식")

### <a name="passing-in-a-column-name-as-a-parameter"></a>열 이름을 매개 변수로 전달

일반적인 패턴은 열 이름을 매개 변수 값으로 전달하는 것입니다. 매개 변수와 연결된 열을 참조하려면 `byName()` 함수를 사용합니다. 열을 `toString()`와 같은 캐스팅 기능을 사용하여 해당 형식으로 캐스팅해야 합니다.

예를 들어 매개 변수를 `columnName`기반으로 문자열 열을 매핑하려는 경우 파생 된 열 `toString(byName($columnName))`변환을 와 같게 추가할 수 있습니다.

![열 이름을 매개 변수로 전달](media/data-flow/parameterize-column-name.png "열 이름을 매개 변수로 전달")

## <a name="assign-parameter-values-from-a-pipeline"></a>파이프라인에서 매개 변수 값 할당

매개 변수를 사용하여 데이터 흐름을 만든 후에는 데이터 흐름 활동 실행을 사용하여 파이프라인에서 실행할 수 있습니다. 파이프라인 캔버스에 활동을 추가하면 활동의 **매개** 변수 탭에서 사용 가능한 데이터 흐름 매개 변수가 표시됩니다.

![데이터 흐름 매개변수 설정](media/data-flow/parameter-assign.png "데이터 흐름 매개변수 설정")

매개 변수 데이터 형식이 문자열인 경우 텍스트 상자를 클릭하여 매개 변수 값을 설정할 때 파이프라인 또는 데이터 흐름 식을 입력하도록 선택할 수 있습니다. 파이프라인 식을 선택하면 파이프라인 표현식 패널이 표시됩니다. 다음과 같은 경우를 사용하여 `'@{<expression>}'`문자열 보간 구문 내에 파이프라인 함수를 포함해야 합니다.

```'@{pipeline().RunId}'```

매개 변수가 형식 문자열이 아닌 경우 항상 데이터 흐름 표현표현 작성기와 함께 표시됩니다. 여기서 매개 변수의 데이터 형식과 일치하려는 식 또는 리터럴 값을 입력할 수 있습니다. 다음은 식 작성기의 데이터 흐름 식 및 리터럴 문자열의 예입니다.

* ```toInteger(Role)```
* ```'this is my static literal string'```

각 매핑 데이터 흐름에는 파이프라인 및 데이터 흐름 식 매개 변수의 조합이 있을 수 있습니다. 

![데이터 흐름 매개 변수 샘플](media/data-flow/parameter-example.png "데이터 흐름 매개 변수 샘플")



## <a name="next-steps"></a>다음 단계
* [데이터 흐름 활동 실행](control-flow-execute-data-flow-activity.md)
* [흐름 표현식 제어](control-flow-expression-language-functions.md)
