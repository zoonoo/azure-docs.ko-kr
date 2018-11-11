---
title: '자습서 5: 자유 형식 텍스트에 대한 pattern.any 엔터티'
titleSuffix: Azure Cognitive Services
description: pattern.any 엔터티를 사용하여 발화가 올바른 형식이고 데이터의 끝이 발화의 나머지 단어와 쉽게 혼동될 수 있는 발화에서 데이터를 추출합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 9038947ad20f63638af1830ca660de8cac802d9b
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282093"
---
# <a name="tutorial-5-extract-free-form-data"></a>자습서 5: 자유 형식 데이터 추출

이 자습서에서는 pattern.any 엔터티를 사용하여 발화가 올바른 형식이고 데이터의 끝이 발화의 나머지 단어와 쉽게 혼동될 수 있는 발화에서 데이터를 추출합니다. 

pattern.any 엔터티를 사용하면 엔터티의 표현이 발화의 나머지 부분에서 엔터티의 끝을 확인하기 어렵게 만드는 자유 형식 데이터를 찾을 수 있습니다. 

이 Human Resources 앱도 직원들이 회사 양식을 찾는 데 도움이 됩니다. 

|발화|
|--|
|Where is **HRF-123456**?|
|Who authored **HRF-123234**?|
|**HRF-456098** is published in French?|

그러나 각 양식은 `Request relocation from employee new to the company 2018 version 5`과 같이 친숙한 이름 뿐만 아니라 이전 표에서 사용된 적절한 형식의 이름도 갖습니다. 

친숙한 양식 이름을 갖는 발언은 다음과 같습니다.

|발화|
|--|
|Where is **Request relocation from employee new to the company 2018 version 5**?|
|Who authored **"Request relocation from employee new to the company 2018 version 5"**?|
|**Request relocation from employee new to the company 2018 version 5** is published in French?|

다양한 길이에는 LUIS에서 엔터티가 끝나는 위치를 혼동할 수 있는 단어가 포함됩니다. 패턴에서 Pattern.any 엔터티를 사용하면 LUIS가 양식 이름을 올바르게 추출하도록 양식 이름의 시작 및 끝을 지정할 수 있습니다.

|템플릿 발화 예제|
|--|
|Where is {FormName}[?]|
|Who authored {FormName}[?]|
|{FormName} is published in French[?]|

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 기존 자습서 앱 사용
> * 기존 엔터티에 발화 예제 추가
> * pattern.any 엔터티 만들기
> * 패턴 만들기
> * 학습
> * 새 패턴 테스트

[!INCLUDE[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>기존 앱 사용
마지막 자습서에서 만든 **HumanResources**라는 앱을 사용하여 계속 진행합니다. 

이전 자습서의 HumanResources 앱이 없으면 다음 단계를 사용합니다.

1.  [앱 JSON 파일](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-roles-HumanResources.json)을 다운로드하고 저장합니다.

2. JSON을 새 앱으로 가져옵니다.

3. **관리** 섹션의 **버전** 탭에서 버전을 복제하고 `patt-any`라는 이름을 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다.

## <a name="add-example-utterances"></a>예제 발화 추가 
FormName 엔터티를 만들고 레이블을 지정하기 어려운 경우 미리 빌드한 keyPhrase 엔터티를 제거합니다. 

1. 위쪽 탐색 영역에서 **빌드**를 선택한 후 왼쪽 탐색 영역에서 **의도**를 선택합니다.

2. 의도 목록에서 **FindForm**을 선택합니다.

3. 다음과 같은 일부 예제 발언을 추가합니다.

    |예제 발화|
    |--|
    |Where is the form **What to do when a fire breaks out in the Lab** and who needs to sign it after I read it?|
    |Where is **Request relocation from employee new to the company** on the server?|
    |Who authored "**Health and wellness requests on the main campus**" and what is the most current version?|
    |I'm looking for the form named "**Office move request including physical assets**". |

    양식 이름이 다양한 형태로 존재하므로 Pattern.any 엔터티가 없으면 LUIS에서 양식 제목이 끝나는 위치를 이해하기 어렵습니다.

## <a name="create-a-patternany-entity"></a>Pattern.any 엔터티 만들기
Pattern.any 엔터티는 다양한 길이의 엔터티를 추출합니다. 패턴이 엔터티의 시작 및 끝을 표시하므로 이 엔터티는 패턴에서만 작동합니다. Pattern.any가 포함된 패턴이 엔터티를 잘못 추출한 것을 발견하면 [명시적 목록](luis-concept-patterns.md#explicit-lists)을 사용하여 이 문제를 정정합니다. 

1. 왼쪽 탐색에서 **엔터티**를 선택합니다.

2. **새 엔터티 만들기**를 선택하고 이름 `FormName`을 입력한 후 형식으로 **Pattern.any**를 선택합니다. **완료**를 선택합니다. 

    Pattern.any는 한 패턴에서만 유효하므로 의도에서 엔터티에 레이블을 지정할 수 없습니다. 

    추출된 데이터에 number 또는 datetimeV2 등의 기타 엔터티를 포함하려면 number 및 datetimeV2 뿐만 아니라 Pattern.any를 포함하는 복합 엔터티를 만들어야 합니다.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Pattern.any를 사용하는 패턴 추가

1. 왼쪽 탐색에서 **패턴**을 선택합니다.

2. **FindForm** 의도를 선택합니다.

3. 새 엔터티를 사용하는 다음 템플릿 발언을 입력합니다.

    |템플릿 발언|
    |--|
    |Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]|
    |Where is ["]{FormName}["] on the server[?]|
    |Who authored ["]{FormName}["] and what is the most current version[?]|
    |I'm looking for the form named ["]{FormName}["][.]|

    큰따옴표 대신 작은따옴표, 물음표 대신 마침표 등과 같이 양식을 변형하려는 경우 각 변형에 대해 새로운 패턴을 만듭니다.

4. keyPhrase 엔터티를 제거한 경우 앱에 다시 추가합니다. 

## <a name="train-the-luis-app"></a>LUIS 앱 학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>자유 형식 데이터 추출의 새 패턴 테스트
1. 위쪽 막대에서 **테스트**를 선택하여 테스트 패널을 엽니다. 

2. 다음 발언을 입력합니다. 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. 결과 아래에서 **검사**를 선택하여 엔터티 및 의도의 테스트 결과를 확인합니다.

    먼저 엔터티 `FormName`이 발견된 다음, 의도를 결정하는 패턴이 발견됩니다. 엔터티가 검색되지 않은 테스트 결과가 있어서 패턴이 발견되지 않은 경우에는 의도(패턴이 아님)에 더 많은 예제 발화를 추가해야 합니다.

4. 위쪽 탐색에서 **테스트** 단추를 선택하여 테스트 패널을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 기존 의도에 발화 예제를 추가한 다음, 양식 이름으로 새 pattern.any를 만들었습니다. 그런 다음, 새 발화 예제와 엔터티를 사용하여 기존 의도에 대한 패턴을 만들었습니다. 대화형 테스트에서 엔터티가 검색되어 패턴과 해당 의도가 예측되었음을 보여 주었습니다. 

> [!div class="nextstepaction"]
> [패턴과 함께 역할을 사용하는 방법 알아보기](luis-tutorial-pattern-roles.md)