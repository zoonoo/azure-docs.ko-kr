---
title: 패턴을 사용하여 LUIS 예측을 개선하는 방법의 자습서 - Azure | Microsoft Docs
titleSuffix: Azure
description: 이 자습서에서는 의도 패턴을 사용하여 LUIS 의도 및 엔터티 예측을 개선합니다.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ff5572366be548132b28e5ce03b9595e7f98128c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265319"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>자습서: 패턴을 사용하여 예측 개선

이 자습서에서는 패턴을 사용하여 의도 및 엔터티 예측을 확장합니다.  

> [!div class="checklist"]
* 패턴이 앱에 도움이 되는지 식별하는 방법
* 패턴을 만드는 방법 
* 패턴에서 미리 빌드된 엔터티 및 사용자 지정 엔터티를 사용하는 방법 
* 패턴 예측 개선 사항을 확인하는 방법
* 엔터티에 역할을 추가하여 컨텍스트 기반 엔터티를 찾는 방법
* Pattern.any를 추가하여 자유 형식 엔터티를 찾는 방법

이 문서에서는 LUIS 응용 프로그램을 작성하기 위해 체험 [LUIS][LUIS] 계정이 필요합니다.

## <a name="import-humanresources-app"></a>HumanResources 앱 가져오기
이 자습서에서는 HumanResources 앱을 가져옵니다. 앱에는 세 가지 의도인 None, GetEmployeeOrgChart, GetEmployeeBenefits이 포함됩니다. 앱에는 두 가지 엔터티인 미리 빌드된 number 및 Employee가 포함됩니다. Employee 엔터티는 직원의 이름을 추출하는 단순 엔터티입니다. 

1. 새 LUIS 앱 파일을 만들고 `HumanResources.json`으로 이름을 지정합니다. 

2. 다음 앱 정의를 파일에 복사합니다.

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. LUIS **앱** 페이지에서 **새 앱 가져오기**를 선택합니다. 

4. **새 앱 가져오기** 대화 상자에서 1단계에서 만든 `HumanResources.json` 파일을 선택합니다.

5. **GetEmployeeOrgChart** 의도를 선택한 다음, **엔터티 보기**에서 **토큰 보기**로 변경합니다. 여러 가지 예제 발화가 나열됩니다. 각 발화에는 Employee 엔터티인 이름이 포함됩니다. 각 이름은 서로 다르며 표현 배열이 발화별로 다르다는 것을 알 수 있습니다. 이 다양성을 기반으로 LUIS가 광범위한 발화를 학습할 수 있습니다.

    ![엔터티 보기가 토글된 의도 페이지의 스크린샷](media/luis-tutorial-pattern/utterances-token-view.png)

6. 위쪽 탐색 모음에서 **학습**을 선택하여 앱을 학습시킵니다. 녹색 성공 막대가 표시될 때까지 기다립니다.

7. 위쪽 패널에서 **테스트**를 선택합니다. `Who does Patti Owens report to?`를 입력한 다음, Enter 키를 선택합니다. 발화 아래에서 **검사**를 선택하여 테스트에 대한 자세한 정보를 확인합니다.
    
    직원 이름 Patti Owens가 아직 예제 발화에 사용되지 않았습니다. 이 테스트에서는 이 발화가 `GetEmployeeOrgChart` 의도에 관련되고 Employee 엔터티가 `Patti Owens`여야 한다는 것을 LUIS가 얼마나 잘 학습했는지 확인합니다. `GetEmployeeOrgChart` 의도의 결과는 50%(.50)여야 합니다. 의도가 올바르지만 점수가 낮습니다. 또한 Employee 엔터티가 `Patti Owens`로 올바르게 식별됩니다. 패턴은 이 초기 예측 점수를 높입니다. 

    ![테스트 패널 스크린샷](media/luis-tutorial-pattern/original-test.png)

8. 위쪽 탐색에서 **테스트** 단추를 선택하여 테스트 패널을 닫습니다. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>패턴을 사용하면 더 적은 예제로 LUIS에 일반 발화를 학습시킬 수 있습니다.
인사 관리 도메인의 특성 때문에 조직에서 직원 관계를 묻는 몇 가지 일반적인 방법이 있습니다. 예: 

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

이러한 발화가 너무 가까워서 많은 발화 예제를 제공하지 않고는 각각의 컨텍스트 고유성을 확인하기가 어렵습니다. 의도 패턴을 추가하면 LUIS는 많은 발화 예제를 제공하지 않고도 의도의 일반적인 발화 패턴을 학습합니다. 

이 의도의 예제 템플릿 발화는 다음과 같습니다.

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

패턴은 정규식 일치 및 기계 학습의 조합입니다. 다음으로 LUIS가 패턴을 학습하도록 몇 가지 템플릿 발화 예제를 제공합니다. 예를 들어, 의도 발화와 마찬가지로 의도에 맞는 발화 및 발화 내에서 엔터티가 있는 위치를 LUIS가 더 잘 이해하도록 합니다. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>템플릿 발화 추가

1. 왼쪽 탐색의 **앱 성능 개선** 아래에서 왼쪽 탐색의 **패턴**을 선택합니다.

2. **GetEmployeeOrgChart** 의도를 선택하고 다음 템플릿 발화를, 각 템플릿 발화 뒤에 Enter 키를 선택하여 한 번에 하나씩 입력합니다.

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    `{Employee}` 구문은 템플릿 발화 내의 엔터티 위치 및 엔터티 형식을 표시합니다. 

    ![의도의 템플릿 발화 입력 스크린샷입니다.](./media/luis-tutorial-pattern/enter-pattern.png)

3. 위쪽 탐색 모음에서 **학습**을 선택합니다. 녹색 성공 막대가 표시될 때까지 기다립니다.

4. 위쪽 패널에서 **테스트**를 선택합니다. 텍스트 상자에 `Who does Patti Owens report to?`를 입력합니다. Enter 키를 선택합니다. 이는 이전 섹션에서 테스트한 것과 동일한 발화입니다. `GetEmployeeOrgChart` 의도의 결과는 더 높아야 합니다. 

    이제 점수가 훨씬 더 좋습니다. LUIS는 많은 예제를 제공하지 않고도 의도에 관련된 패턴을 학습했습니다.

    ![점수가 높은 결과가 포함된 테스트 패널 스크린샷](./media/luis-tutorial-pattern/high-score.png)

    먼저 엔터티가 발견된 다음, 의도를 나타내는 패턴이 발견됩니다. 엔터티가 검색되지 않은 테스트 결과가 있어서 패턴이 발견되지 않은 경우에는 의도(패턴이 아님)에 더 많은 예제 발화를 추가해야 합니다. 

5. 위쪽 탐색에서 **테스트** 단추를 선택하여 테스트 패널을 닫습니다.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>패턴에서 역할이 있는 엔터티 사용
LUIS 앱을 사용하면 한 위치에서 다른 위치로 직원을 이동할 수 있습니다. 예제 발화는 `Move Bob Jones from Seattle to Los Colinas`입니다. 발화 내의 각 위치는 의미가 다릅니다. Seattle은 출발지 위치이고 Los Colinas는 이동의 도착지 위치입니다. 패턴에서 이러한 위치를 구별하기 위해 다음 섹션에서는 두 가지 역할인 출발지 및 도착지를 사용하여 위치의 단순 엔터티를 만듭니다. 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>사람 및 자산을 이동하기 위한 새 의도 만들기
사람 또는 자산 이동과 관련된 모든 발화의 새 의도를 만듭니다.

1. 왼쪽 탐색에서 **의도** 선택
2. **새 의도 만들기** 선택
3. 새 의도 이름을 `MoveAssetsOrPeople`로 지정
4. 예제 발화 추가:

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![MoveAssetsOrPeople 의도의 예제 발화 스크린샷](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    예제 발화의 목적은 충분한 예제를 제공하는 것입니다. 테스트의 뒷부분에서 위치 엔터티가 검색되지 않아 패턴이 검색되지 않으면 이 단계로 돌아와서 더 많은 예제를 추가합니다. 그런 다음, 학습시키고 다시 테스트합니다. 

5. 발화에서 이름, 성을 차례로 선택한 다음, 목록에서 Employee 엔터티를 선택하여 예제 발화의 엔터티를 Employee 엔터티로 표시합니다.

    ![Employee 엔터티로 표시된 MoveAssetsOrPeople의 발화 스크린샷](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. 발화 `move travis hinton from portland to orlando`에서 텍스트 `portland`를 선택합니다. 팝업 대화 상자에서 새 엔터티 이름 `Location`을 입력하고 **새 엔터티 만들기**를 선택합니다. **단순** 엔터티 형식을 선택하고 **완료**를 선택합니다.

    ![새 위치 엔터티 만들기 스크린샷](./media/luis-tutorial-pattern/create-new-location-entity.png)

    발화에서 나머지 위치 이름을 표시합니다. 

    ![표시된 모든 엔터티의 스크린샷](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    이전 이미지에서는 단어 선택 및 순서의 패턴이 분명합니다. 패턴을 사용하지 **않고** 의도의 발화에 분명한 패턴이 있는 것은 패턴을 사용 중임을 알리는 표시입니다. 

    패턴 대신에 다양한 발화가 필요한 경우, 이러한 발화는 잘못된 예제 발화입니다. 이 경우, 용어 또는 단어 선택, 발화 길이 및 엔터티 배치에서 다양한 발화가 필요합니다. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>위치 엔터티에 역할 추가 
역할은 패턴에만 사용할 수 있습니다. 위치 엔터티에 출발지 및 도착지의 역할을 추가합니다. 

1. 왼쪽 탐색에서 **엔터티**를 선택한 다음, 엔터티 목록에서 **Location**을 선택합니다.

2. 엔터티에 `Origin` 및 `Destination` 역할을 추가합니다.

    ![역할이 있는 새 엔터티의 스크린샷](./media/luis-tutorial-pattern/location-entity.png)

    의도 발화에 역할이 없으므로 MoveAssetsOrPeople 의도 페이지에 역할이 표시되지 않습니다. 역할은 패턴 템플릿 발화에만 존재합니다. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>위치 및 도착지 역할을 사용하는 템플릿 발화 추가
새 엔터티를 사용하는 템플릿 발화를 추가합니다.

1. 왼쪽 탐색에서 **패턴**을 선택합니다.

2. **MoveAssetsOrPeople** 의도를 선택합니다.

3. 새 엔터티 `Move {Employee} from {Location:Origin} to {Location:Destination}`을 사용하여 새 템플릿 발화를 입력합니다. 템플릿 발화 내부의 엔터티 및 역할의 구문은 `{entity:role}`입니다.

    ![역할이 있는 새 엔터티의 스크린샷](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. 앱에 새 의도, 엔터티 및 패턴을 학습시킵니다.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>역할 데이터 추출의 새 패턴 테스트
테스트를 사용하여 새 패턴의 유효성을 검사합니다.

1. 위쪽 패널에서 **테스트**를 선택합니다. 
2. 발화 `Move Tammi Carlson from Bellingham to Winthrop`을 입력합니다.
3. 결과 아래에서 **검사**를 선택하여 엔터티 및 의도의 테스트 결과를 확인합니다.

    ![역할이 있는 새 엔터티의 스크린샷](./media/luis-tutorial-pattern/test-with-roles.png)

    먼저 엔터티가 발견된 다음, 의도를 나타내는 패턴이 발견됩니다. 엔터티가 검색되지 않은 테스트 결과가 있어서 패턴이 발견되지 않은 경우에는 의도(패턴이 아님)에 더 많은 예제 발화를 추가해야 합니다. 

4. 위쪽 탐색에서 **테스트** 단추를 선택하여 테스트 패널을 닫습니다.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Pattern.any 엔터티를 사용하여 패턴에서 자유 형식 엔터티 찾기
이 HumanResources 앱은 직원들이 회사 양식을 찾는 데 도움이 됩니다. 대부분의 양식에는 길이가 다양한 제목이 있습니다. 구문의 길이가 다양하면 LUIS가 양식 이름이 끝나는 위치를 혼동할 수 있습니다. 패턴에서 **Pattern.any** 엔터티를 사용하면 LUIS가 양식 이름을 올바르게 추출하도록 양식 이름의 시작 및 끝을 지정할 수 있습니다. 

### <a name="create-a-new-intent-for-the-form"></a>양식의 새 의도 만들기
양식을 찾고 있는 발화의 새 의도를 만듭니다.

1. 왼쪽 탐색에서 **의도**를 선택합니다.

2. **새 의도 만들기**를 선택합니다.

3. 새 의도 이름을 `FindForm`으로 지정합니다.

4. 예제 발화를 추가합니다.

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![역할이 있는 새 엔터티의 스크린샷](./media/luis-tutorial-pattern/intent-findform.png)

    양식 제목은 `What to do when a fire breaks out in the Lab`입니다. 발화는 양식의 위치를 묻고 있고 직원이 양식을 읽는지 확인하여 양식에 서명해야 하는 사람을 묻고 있습니다. Pattern.any 엔터티가 없으면 양식 제목이 끝나는 위치를 이해하고 양식 제목을 발화의 엔터티로 추출하기가 어렵습니다.

### <a name="create-a-patternany-entity-for-the-form-title"></a>양식 제목의 Pattern.any 엔터티 만들기
Pattern.any 엔터티는 다양한 길이의 엔터티를 허용합니다. 패턴이 엔터티의 시작 및 끝을 표시하므로 이 엔터티는 패턴에서만 작동합니다. Pattern.any가 포함된 패턴이 엔터티를 잘못 추출한 것을 발견하면 [명시적 목록](luis-concept-patterns.md#explicit-lists)을 사용하여 이 문제를 정정합니다. 

1. 왼쪽 탐색에서 **엔터티**를 선택합니다.

2. **새 엔터티 만들기**를 선택합니다. 

3. 형식이 **Pattern.any**인 엔터티 이름을 `FormName`으로 지정합니다. 이 특정 자습서의 경우 엔터티에 역할을 추가할 필요가 없습니다.

    ![엔터티 이름 및 엔터티 형식의 대화 상자 이미지](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Pattern.any를 사용하는 패턴 추가

1. 왼쪽 탐색에서 **패턴**을 선택합니다.

2. **FindForm** 의도를 선택합니다.

3. 새 엔터티 `Where is the form {FormName} and who needs to sign it after I read it?`을 사용하여 템플릿 발화를 입력합니다.

    ![pattern.any 엔터티를 사용하는 템플릿 발화의 스크린샷](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. 앱에 새 의도, 엔터티 및 패턴을 학습시킵니다.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>자유 형식 데이터 추출의 새 패턴 테스트
1. 위쪽 막대에서 **테스트**를 선택하여 테스트 패널을 엽니다. 

2. 발화 `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`을 입력합니다.

3. 결과 아래에서 **검사**를 선택하여 엔터티 및 의도의 테스트 결과를 확인합니다.

    ![pattern.any 엔터티를 사용하는 템플릿 발화의 스크린샷](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    먼저 엔터티가 발견된 다음, 의도를 나타내는 패턴이 발견됩니다. 엔터티가 검색되지 않은 테스트 결과가 있어서 패턴이 발견되지 않은 경우에는 의도(패턴이 아님)에 더 많은 예제 발화를 추가해야 합니다.

4. 위쪽 탐색에서 **테스트** 단추를 선택하여 테스트 패널을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS 앱을 삭제합니다. 이렇게 하려면 앱 목록에서 앱 이름 오른쪽에 있는 3개 점 메뉴(...)를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [구문 목록을 사용하여 예측 개선](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions