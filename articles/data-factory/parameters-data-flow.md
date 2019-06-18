---
title: Azure Data Factory 매핑 데이터 흐름 매개 변수
description: Data factory 파이프라인에서 매핑 데이터 흐름을 매개 변수화 하는 방법 알아보기
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: af5f421cc3802f3a7ad44bb294f5066c32569f8b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082886"
---
# <a name="mapping-data-flow-parameters"></a>데이터 흐름 매개 변수 매핑

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Data factory에서 흐름 데이터 매핑 매개 변수 사용을 지원 합니다. 식에서 사용할 수 있는 데이터 흐름 정의 내에서 매개 변수를 정의할 수 있습니다. 데이터 흐름 실행 활동을 통해 호출 파이프라인에서 매개 변수를 설정할 수 있습니다. 작업 식의 데이터 흐름에서 값을 설정 하는 데는 세 가지 옵션이 있습니다.

* 파이프라인 제어 흐름 식 언어를 사용 하 여 동적 값을 설정 하려면
* 데이터 흐름 식 언어를 사용 하 여 동적 값을 설정 하려면
* 식 언어 중 하나를 사용 하 여 정적 리터럴 값을 설정 하려면

범용, 유연 하 고 다시 사용할 수 있는 데이터 흐름을 확인 하려면이 기능을 사용 합니다. 데이터 흐름 설정 및 이러한 매개 변수를 사용 하 여 식 매개 변수화 할 수 있습니다.

> [!NOTE]
> 파이프라인 제어 흐름 식도 사용 하려면 데이터 흐름 매개 변수의 문자열 형식 이어야 합니다.

* 파이프라인 캔버스에 데이터 흐름 실행 활동을 추가 합니다.
* 데이터 흐름에 매개 변수가 입력 매개 변수 값에 각 매개 변수 옆에 있는 텍스트 상자를 클릭 매개 변수 tab.* *에서 사용 가능한 매개 변수 목록이 표시 됩니다.
* 파이프라인 제어 흐름 식 언어를 통해 매개 변수 식 또는 데이터 흐름 식에 만들 수 있습니다.

![데이터 흐름 3 매개 변수](media/data-flow/params3.png "데이터 흐름 3 매개 변수")

## <a name="create-parameters-in-data-flow"></a>데이터 흐름에서 매개 변수 만들기

![데이터 흐름 1 매개 변수](media/data-flow/params1.png "데이터 흐름 1 매개 변수")

데이터 흐름에 매개 변수를 추가, 일반 속성을 확인 하려면 데이터 흐름 캔버스의 빈 부분을 클릭 합니다. 설정 창에서 매개 변수 라는 탭이 표시 됩니다. 그런 다음 데이터 흐름으로 값을 전달 하는 파이프라인에서 설정할 수 있는 새 매개 변수를 생성 하는 새 단추를 클릭 합니다. 매개 변수 이름을 입력 하 고 각 매개 변수의 데이터 형식을 선택 합니다.

데이터 흐름 식 내의 파이프라인에서 설정 된 값을 사용 하 여 매개 변수를 사용할 수 있습니다. 매개 변수 $로 시작 되며 변경할 수 없습니다. 매개 변수 탭 아래에 있는 식 작성기 내에서 사용할 수 있는 매개 변수 목록을 찾을 있습니다. 이러한 값에 식 내에서 사용할 수 있지만 매개 변수에 새 값을 할당 하지 않을 수 있습니다.

![데이터 흐름 매개 변수 2](media/data-flow/params2.png "데이터 흐름 매개 변수 2")

## <a name="set-data-flow-parameters-from-pipeline"></a>파이프라인에서 데이터 흐름 매개 변수 설정

매개 변수를 사용 하 여 데이터 흐름을 만든 후 데이터 흐름 실행 활동을 사용 하 여 파이프라인에서 해당 데이터 흐름을 이제 실행할 수 있습니다. 파이프라인 디자인 캔버스를 해당 활동을 추가한 후 나타납니다 사용 가능한 데이터를 사용 하 여 활동의 매개 변수 설정 탭에서에서 흐름 매개 변수입니다.

![데이터 흐름 매개 변수 식 언어](media/data-flow/params4.png "데이터 흐름 식 언어 매개 변수")

입력 매개 변수 값 입력란을 클릭 하면 데이터 흐름 식 작성기를 사용 하 여 나타납니다. 여기에서 모든 식 또는 한다고와 일치 하는 매개 변수 데이터 형식의 리터럴 값을 입력할 수 있습니다. 식 작성기에서 데이터 흐름 식 및 리터럴 문자열의 예는 다음과 같습니다.

* ```toInteger(Role)```
* ```'this is my static literal string'```

프로그램 매개 변수 데이터 형식이 문자열인 경우 파이프라인 또는 데이터 흐름 식 입력을 선택할 수 있습니다. 식 파이프라인을 선택 하면 파이프라인 식 패널 표시 대신 됩니다. 문자열 보간 구문은 사용 내 파이프라인 함수를 포함 해야 합니다. ' @ {0}<expression>}', 예를 들어:

```'@{pipeline().RunId}'```

![데이터 흐름 매개 변수 예제](media/data-flow/params5.png "데이터 흐름 parameters 샘플")

## <a name="next-steps"></a>다음 단계

* [실행 데이터 흐름 작업](control-flow-execute-data-flow-activity.md)
* [제어 흐름 식도](control-flow-expression-language-functions.md)
