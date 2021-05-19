---
title: 앱 스키마 정의
description: LUIS 앱은 `.json` 또는 `.lu`로 표시되며 모든 의도, 엔터티, 예제 발화, 기능 및 설정을 포함합니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 08/22/2020
ms.openlocfilehash: db4fd52dad82542f20e58ebb3b8526c5be7f2f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91327326"
---
# <a name="app-schema-definition"></a>앱 스키마 정의

LUIS 앱은 `.json` 또는 `.lu`로 표시되며 모든 의도, 엔터티, 예제 발화, 기능 및 설정을 포함합니다.

## <a name="format"></a>형식

앱을 가져오고 내보낼 때 `.json` 또는 `.lu` 중 하나를 선택합니다.

|형식|정보|
|--|--|
|`.json`| 표준 프로그래밍 형식|
|`.lu`|Bot Framework의 [Bot Builder 도구](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)에서 지원됩니다.|

## <a name="version-7x"></a>버전 7.x

* 버전 7.x로 이동하면 엔터티가 중첩된 기계 학습 엔터티로 표시됩니다.
* 다음 작성 API에서 `enableNestedChildren` 속성을 사용하여 중첩된 기계 학습 엔터티를 작성할 수 있습니다.
    * [레이블 추가](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [일괄 처리 레이블 추가](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [레이블 검토](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [엔터티에 대한 엔드포인트 쿼리 제안](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [의도에 대한 엔드포인트 쿼리 제안](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [
  ],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

| element                  | 의견                              |
|--------------------------|--------------------------------------|
| "hierarchicals": [],     | 사용되지 않음, [기계 학습 엔터티](luis-concept-entity-types.md)를 사용합니다.   |
| "합성": [],        | 사용되지 않음, [기계 학습 엔터티](luis-concept-entity-types.md)를 사용합니다. [복합 엔터티](reference-entity-composite.md) 참조입니다. |
| "closedLists": [],       | 주로 엔터티의 기능으로 사용되는 [목록 엔터티](reference-entity-list.md) 참조입니다.    |
| "versionId": "0.1",      | LUIS 앱의 버전입니다.|
| "name": "example-app",   | LUIS 앱의 이름입니다. |
| "desc": "",              | LUIS 앱의 선택적 설명입니다.  |
| "culture": "en-us",      | 미리 빌드된 엔터티, 기계 학습 및 토크나이저와 같은 기본 기능에 영향을 미치는 앱의 [언어](luis-language-support.md)입니다.  |
| "tokenizerVersion": "1.0.0", | [토크나이저](luis-language-support.md#tokenization)  |
| "patternAnyEntities": [],   | [Pattern.any 엔터티](reference-entity-pattern-any.md)    |
| "regex_entities": [],    |  [정규식 엔터티](reference-entity-regular-expression.md)   |
| "phraselists": [],       |  [구문 목록(기능)](luis-concept-feature.md#create-a-phrase-list-for-a-concept)   |
| "regex_features": [],    |  사용되지 않음, [기계 학습 엔터티](luis-concept-entity-types.md)를 사용합니다. |
| "patterns": [],          |  [패턴 구문](reference-pattern-syntax.md)을 사용하여 [패턴은 예측 정확도를 개선함](luis-concept-patterns.md)   |
| "설정": []           | [앱 설정](luis-reference-application-settings.md)|

## <a name="version-6x"></a>버전 6.x

* 버전 6.x로 이동하고 새 [기계 학습 엔터티](reference-entity-machine-learned-entity.md)를 사용하여 엔터티를 나타냅니다.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>버전 4.x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>다음 단계

* [V3 작성 API](luis-migration-authoring-entities.md)로 마이그레이션