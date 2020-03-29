---
title: 반복 응용 프로그램 디자인 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS는 모델 변경, 발화 예제, 게시 및 엔드포인트 쿼리에서 데이터 수집의 반복 주기에서 가장 좋은 사례를 학습합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74422596"
---
# <a name="iterative-app-design-for-luis"></a>LUIS에 대한 반복 응용 프로그램 디자인

언어 이해 (LUIS) 응용 프로그램은 학습하고 반복으로 가장 효율적으로 수행한다. 일반적인 반복 주기는 다음과 같습니다.

* 새 버전 만들기
* LUIS 앱 스키마를 편집합니다. 다음 내용이 포함됩니다.
    * 예제 발언이 있는 의도
    * 엔터티
    * 기능
* 교육, 테스트 및 게시
    * 능동 학습을 위한 예측 끝점에서 테스트
* 엔드포인트 쿼리에서 데이터 수집

![제작 주기](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>LUIS 스키마 구축

앱의 스키마는 사용자가 요청하는 _항목(의도_ 또는 _의도)과_ 답변을 결정하는 데 사용되는 세부 _정보(엔터티라고_함)를 제공하는 의도를 정의합니다. 

앱 스키마는 앱 도메인에 특정해야 관련 단어와 구를 결정하고 일반적인 단어 순서를 결정해야 합니다. 

예 발언은 앱이 런타임에 기대하는 인식된 음성 또는 텍스트와 같은 사용자 입력을 나타냅니다. 

스키마에는 의도가 필요하며 _엔터티가 있어야 합니다._ 

### <a name="example-schema-of-intents"></a>의도의 예제 스키마

가장 일반적인 스키마는 의도로 구성된 의도 스키마입니다. 이러한 유형의 스키마는 LUIS를 사용하여 사용자의 의도를 확인합니다. 

LUIS가 사용자의 의도를 결정하는 데 도움이 되는 경우 의도 스키마 유형에 엔터티가 있을 수 있습니다. 예를 들어 배송 엔터티(의도에 대한 설명자)는 LUIS가 배송 의도를 결정하는 데 도움이 됩니다. 

### <a name="example-schema-of-entities"></a>엔터티의 예제 스키마

엔터티 스키마는 사용자 발언에서 추출된 데이터인 엔터티에 중점을 둡니다. 예를 들어 사용자가 "피자 세 개 주문하고 싶습니다"라고 말하는 경우 추출할 엔터티는 _세_ 개와 _피자입니다._ 이들은 주문을하는 것이었던 의도를 성취하는 데 사용됩니다. 

엔터티 스키마의 경우 발화 의도가 클라이언트 응용 프로그램에 덜 중요합니다. 

엔터티 스키마를 구성하는 일반적인 방법은 **None** 의도에 모든 예제 발언을 추가하는 것입니다. 

### <a name="example-of-a-mixed-schema"></a>혼합 스키마의 예

가장 강력하고 성숙한 스키마는 모든 범위의 엔터티 및 기능을 갖춘 의도 스키마입니다. 이 스키마는 의도 또는 엔터티 스키마로 시작하여 클라이언트 응용 프로그램에 이러한 정보가 필요하므로 둘 다의 개념을 포함하도록 증가할 수 있습니다. 

## <a name="add-example-utterances-to-intents"></a>의도에 예제 발언 추가

LUIS는 각 **의도에서**몇 가지 예제 발언이 필요합니다. 예제 발언은 발언의 의도를 결정할 수 있도록 단어 선택 및 단어 순서의 충분한 변형이 필요합니다. 

> [!CAUTION]
> 예제 발언을 일괄으로 추가하지 마십시오. 15~30개의 구체적이고 다양한 예제로 시작합니다. 

각 예제 utterance에는 **엔터티로**디자인되고 레이블이 지정된 **추출에 필요한 데이터가** 있어야 합니다. 

|Key 요소|목적|
|--|--|
|Intent|사용자 발언을 단일 의도 또는 작업으로 **분류합니다.** 예에는 `BookFlight` 및 `GetWeather`가 포함됩니다.|
|엔터티|의도를 완료하는 데 필요한 발언에서 데이터를 **추출합니다.** 예를 들면 여행 날짜와 시간, 위치 등이 있습니다.|

LUIS 앱은 **None** 의도에 발언을 할당하여 앱의 도메인과 관련이 없는 발언을 무시하도록 디자인할 수 있습니다.

## <a name="test-and-train-your-app"></a>앱 테스트 및 학습

각 의도에 15~30개의 서로 다른 예제 발언이 표시되어 있는 경우 LUIS 앱을 테스트하고 [학습해야](luis-how-to-train.md) 합니다. 

## <a name="publish-to-a-prediction-endpoint"></a>예측 끝점에 게시

LUIS 앱은 목록 [예측 끝점 영역에서](luis-reference-regions.md)사용할 수 있도록 게시되어야 합니다.

## <a name="test-your-published-app"></a>게시된 앱 테스트

HTTPS 예측 끝점에서 게시된 LUIS 앱을 테스트할 수 있습니다. 예측 끝점에서 테스트하면 LUIS가 [검토에](luis-how-to-review-endpoint-utterances.md)대한 신뢰도가 낮은 모든 발언을 선택할 수 있습니다.  

## <a name="create-a-new-version-for-each-cycle"></a>각 주기에 대한 새 버전 만들기

각 버전은 LUIS 앱의 시간에 있는 스냅샷입니다. 앱을 변경하기 전에 새 버전을 만듭니다. 이전 상태로 의도 및 발언을 제거하려고 시도하는 것보다 이전 버전으로 돌아가는 것이 더 쉽습니다.

버전 ID는 문자, 숫자 또는 ‘.’으로 구성되며 10자 이하여야 합니다.

초기 버전(0.1)은 기본 활성 버전입니다. 

### <a name="begin-by-cloning-an-existing-version"></a>기존 버전을 복제하여 시작

각 새 버전에 대한 시작점으로 사용할 기존 버전을 복제합니다. 버전을 복제하면 새 버전이 **활성** 버전이 됩니다. 

### <a name="publishing-slots"></a>게시 슬롯

스테이지 및/또는 프로덕션 슬롯에 게시할 수 있습니다. 각 슬롯에는 다른 버전 또는 동일한 버전이 있을 수 있습니다. 이 기능은 봇 또는 기타 LUIS 호출 앱에서 사용할 수 있는 프로덕션에 게시하기 전에 변경 내용을 확인하는 데 유용합니다. 

LUIS 앱의 [끝점에서](luis-glossary.md#endpoint)학습된 버전을 자동으로 사용할 수 없습니다. LUIS 앱 끝점에서 사용할 수 있도록 버전을 [게시하거나](luis-how-to-publish-app.md) 다시 게시해야 합니다. **스테이징** 및 **프로덕션에**게시하여 끝점에서 사용할 수 있는 두 가지 버전의 앱을 제공할 수 있습니다. 끝점에서 더 많은 버전의 앱을 사용해야 하는 경우 버전을 내보내고 새 앱으로 다시 가져와야 합니다. 새 앱에는 다른 앱 ID가 포함됩니다.

### <a name="import-and-export-a-version"></a>버전 가져오기 및 내보내기

앱 수준에서 버전을 가져올 수 있습니다. 해당 버전은 활성 버전이 되고 앱 `versionId` 파일의 속성에서 버전 ID를 사용합니다. 버전 수준에서 기존 앱으로 가져올 수도 있습니다. 새 버전이 활성 버전이 됩니다. 

버전도 앱 또는 버전 수준에서 내보낼 수 있습니다. 유일한 차이는 앱 수준에서 내보낸 버전이 현재 활성 버전이지만, 버전 수준에서는 **[설정](luis-how-to-manage-versions.md)** 페이지에서 내보낼 버전을 선택할 수 있다는 것입니다. 

내보낸 파일에는 다음이 포함되지 **않습니다.**

* 앱이 가져온 후 다시 학습되기 때문에 기계 학습정보
* 기여자 정보

LUIS 앱 스키마를 백업하려면 [LUIS 포털에서](https://www.luis.ai/applications)버전을 내보냅니다.

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>버전 및 기여자와 함께 기고자 변경 관리

LUIS는 Azure 리소스 수준 권한을 제공하여 앱에 대한 기여자 개념을 사용합니다. 이 개념을 버전 전환과 결합하여 대상 공동 작업을 제공합니다. 

다음 기술을 사용하여 앱에 대한 기여자 변경 사항을 관리할 수 있습니다.

### <a name="manage-multiple-versions-inside-the-same-app"></a>동일한 앱 내부에서 여러 버전 관리

각 작성자에 대한 기본 버전에서 [복제하여](luis-how-to-manage-versions.md#clone-a-version) 시작합니다. 

각 작성자가 자신의 버전의 앱을 변경합니다. 작성자가 모델에 만족하면 새 버전을 JSON 파일로 내보냅니다.  

내보낸 앱인 .json 또는 .lu 파일을 변경 사항에 대해 비교할 수 있습니다. 파일을 결합하여 새 버전의 단일 파일을 만듭니다. 새 `versionId` 병합된 버전을 나타내도록 속성을 변경합니다. 해당 버전을 원래 앱으로 가져옵니다. 

이 방법을 사용하면 하나의 활성 버전, 하나의 스테이지 버전 및 하나의 게시된 버전을 사용할 수 있습니다. [대화형 테스트 창에서](luis-interactive-test.md)활성 버전의 결과를 게시된 버전(단계 또는 프로덕션)과 비교할 수 있습니다.

### <a name="manage-multiple-versions-as-apps"></a>여러 버전을 앱으로 관리

기본 버전을 [내보냅니다](luis-how-to-manage-versions.md#export-version). 각 작성자는 버전을 가져옵니다. 앱을 가져오는 사용자는 버전의 소유자입니다. 앱 수정을 완료하면 버전을 내보냅니다. 

내보낸 앱은 JSON 형식 파일이며 기본 내보내기와 변경 내용을 비교할 수 있습니다. 파일을 결합하여 새 버전의 단일 JSON 파일을 만듭니다. JSON에서 **versionId** 속성을 변경하여 새 병합 버전을 표시합니다. 해당 버전을 원래 앱으로 가져옵니다.

[공동 작업자의](luis-how-to-collaborate.md)기여 작성에 대해 자세히 알아봅니다.

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>끝점 발언을 검토하여 새로운 반복 주기를 시작합니다.

반복 주기를 완료하면 프로세스를 반복할 수 있습니다. LUIS가 낮은 신뢰도로 표시된 [예측 끝점 발언을 검토하는 것으로](luis-how-to-review-endpoint-utterances.md) 시작합니다. 이러한 발언에서 예측된 올바른 의도와 정확하고 완전한 엔터티추출이 모두 있는지 확인합니다. 변경 내용을 검토하고 수락한 후에는 검토 목록이 비어 있어야 합니다.  

## <a name="next-steps"></a>다음 단계

[협업](luis-concept-keys.md) 개념을 알아봅니다.
