---
title: Azure Data Factory Mapping Data Flow 식 작성기
description: Azure Data Factory Mapping Data Flow용 식 작성기
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 67a6de6d85a58f48af4761e0b5d5b0a1a4d74b1a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703404"
---
# <a name="mapping-data-flow-expression-builder"></a>Mapping Data Flow 식 작성기

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory Mapping Data Flow에는 데이터 변환 식을 입력할 수 있는 식 상자가 있습니다. 해당 상자에 데이터 흐름의 열, 필드, 변수, 매개 변수, 함수를 사용합니다. 식을 작성하려면 변환 내의 식 텍스트 상자를 클릭할 때 시작되는 식 작성기를 사용합니다. 변환에 사용할 열을 선택할 때 “계산 열” 옵션이 표시되는 경우도 있습니다. 이 옵션을 클릭해도 식 작성기가 시작됩니다.

![식 작성기](media/data-flow/xpb1.png "식 작성기")

식 작성기 도구는 기본적으로 텍스트 편집기 옵션으로 설정되어 있습니다. 자동 완성 기능은 구문 검사 및 강조 표시를 사용하여 전체 Azure Data Factory Data Flow 개체 모델에서 읽어옵니다.

![식 작성기 자동 완성](media/data-flow/expb1.png "식 작성기 자동 완성")

## <a name="build-schemas-in-output-schema-pane"></a>출력 스키마 창에서 스키마 빌드

![복합 열 추가](media/data-flow/complexcolumn.png "열") 추가

왼쪽 출력 스키마 창에 수정 하 고 스키마에 추가 하는 열이 표시 됩니다. 여기에서 간단 하 고 복잡 한 데이터 구조를 대화형으로 작성할 수 있습니다. "열 추가"를 사용 하 여 필드를 추가 하 고 "하위 열 추가"를 사용 하 여 계층 구조를 작성 합니다.

하위 ]열 추가 하위(media/data-flow/addsubcolumn.png "")열 추가

## <a name="data-preview-in-debug-mode"></a>디버그 모드의 데이터 미리 보기

![식 작성기](media/data-flow/exp4b.png "식 데이터 미리 보기")

데이터 흐름 식에 대 한 작업을 수행 하는 경우 Azure Data Factory 데이터 흐름 디자인 화면에서 디버그 모드를 전환 하 여 작성 중인 식에서의 데이터 결과에 대 한 실시간 미리 보기를 사용 하도록 설정 합니다. 식에 대해 실시간 라이브 디버깅을 사용할 수 있습니다.

![디버그 모드](media/data-flow/debugbutton.png "디버그 단추")

새로 고침 단추를 클릭 하 여 소스의 라이브 샘플에 대 한 식의 결과를 실시간으로 업데이트 합니다.

![식 작성기](media/data-flow/exp5.png "식 데이터 미리 보기")

## <a name="comments"></a>주석

한 줄 및 여러 줄 주석 구문을 사용하여 식에 주석을 추가합니다.

![주석](media/data-flow/comments.png "주석")

## <a name="regular-expressions"></a>정규식

Azure Data Factory Data Flow 식 언어([여기서 전체 참조 문서](https://aka.ms/dataflowexpressions) 참조)를 통해 정규식 구문을 포함하는 함수를 사용할 수 있습니다. 정규식 함수를 사용 하는 경우 식 작성기는 백슬래시 (\\)를 이스케이프 문자 시퀀스로 해석 하려고 합니다. 정규식에서 백슬래시를 사용 하는 경우 전체 regex를 틱 (\`)으로 묶거나 이중 백슬래시를 사용 합니다.

예제: 틱 사용

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

또는 이중 슬래시 사용

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>배열 인덱스 주소 지정

배열을 반환하는 식 함수를 사용하는 경우 대괄호 []를 사용하여 내부에서 배열 개체를 반환하는 특정 인덱스를 처리합니다. 배열은 1부터 시작합니다.

![식 작성기 배열](media/data-flow/expb2.png "식 데이터 미리 보기")

## <a name="handling-names-with-special-characters"></a>특수 문자를 사용 하 여 이름 처리

특수 문자 또는 공백을 포함 하는 열 이름이 있는 경우 이름을 중괄호로 묶습니다.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>다음 단계

[데이터 변환 식 작성 시작](data-flow-expression-functions.md)
