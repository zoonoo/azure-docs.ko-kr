---
title: pattern.any 엔터티를 사용하여 LUIS 예측을 개선하는 방법 자습서 - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: 이 자습서에서는 pattern.any 엔터티를 사용하여 LUIS 의도 및 엔터티 예측을 개선합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: ca4ed4444cd753c0fe22c6596dfd34d033b7b0cf
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237005"
---
# <a name="tutorial-improve-app-with-patternany-entity"></a>자습서: pattern.any 엔터티를 사용하여 앱 개선

이 자습서에서는 pattern.any 엔터티를 사용하여 의도 및 엔터티 예측을 늘립니다.  

> [!div class="checklist"]
* pattern.any를 사용하는 시기 및 방법 알아보기
* pattern.any를 사용하는 패턴 만들기
* 예측 개선 여부를 확인하는 방법

이 문서에서는 LUIS 앱을 작성하기 위해 체험 [LUIS](luis-reference-regions.md) 계정이 필요합니다.

## <a name="before-you-begin"></a>시작하기 전에
[패턴 역할](luis-tutorial-pattern-roles.md) 자습서의 Human Resources 앱이 없는 경우 JSON을 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트의 새 앱으로 [가져옵니다](luis-how-to-start-new-app.md#import-new-app). 가져올 앱은 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-roles-HumanResources.json) GitHub 리포지토리에 있습니다.

원래의 인사 관리 앱을 유지하려면 [설정](luis-how-to-manage-versions.md#clone-a-version) 페이지에서 버전을 복제하고 해당 이름을 `patt-any`로 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 

## <a name="the-purpose-of-patternany"></a>pattern.any의 용도
엔터티 단어 구성 때문에 발언의 나머지 부분에서 엔터티의 끝을 확인하기 어려운 경우 pattern.any 엔터티를 사용하여 자유 형식 데이터를 찾을 수 있습니다. 

이 Human Resources 앱도 직원들이 회사 양식을 찾는 데 도움이 됩니다. 양식은 [정규식 자습서](luis-quickstart-intents-regex-entity.md)에 추가되었습니다. 해당 자습서의 양식 이름은 정규식을 사용하여 적절한 형식의 양식 이름(예: 다음 발언 표에서 굵게 표시된 양식 이름)을 추출했습니다.

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

구문의 길이가 다양하면 LUIS가 엔터티가 끝나는 위치를 혼동할 수 있습니다. 패턴에서 Pattern.any 엔터티를 사용하면 LUIS가 양식 이름을 올바르게 추출하도록 양식 이름의 시작 및 끝을 지정할 수 있습니다.

**패턴을 사용하면 몇 가지 예제 발언을 제공할 수 있지만 엔터티가 검색되지 않으면 패턴이 일치되지 않습니다.**

## <a name="add-example-utterances-to-the-existing-intent-findform"></a>기존 의도 FindForm에 예제 발언 추가 
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

5. 앱을 학습합니다.


## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>자유 형식 데이터 추출의 새 패턴 테스트
1. 위쪽 막대에서 **테스트**를 선택하여 테스트 패널을 엽니다. 

2. 다음 발언을 입력합니다. 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. 결과 아래에서 **검사**를 선택하여 엔터티 및 의도의 테스트 결과를 확인합니다.

    먼저 엔터티 `FormName`이 발견된 다음, 의도를 결정하는 패턴이 발견됩니다. 엔터티가 검색되지 않은 테스트 결과가 있어서 패턴이 발견되지 않은 경우에는 의도(패턴이 아님)에 더 많은 예제 발화를 추가해야 합니다.

4. 위쪽 탐색에서 **테스트** 단추를 선택하여 테스트 패널을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS 앱을 삭제합니다. 이렇게 하려면 앱 목록에서 앱 이름 오른쪽에 있는 줄임표(***...***)를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [패턴과 함께 역할을 사용하는 방법 알아보기](luis-tutorial-pattern-roles.md)