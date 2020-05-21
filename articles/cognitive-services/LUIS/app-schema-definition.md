---
title: 앱 스키마 정의
description: LUIS 앱은 또는에 표시 되며 `.json` `.lu` 모든 의도, 엔터티, 예제 길이 발언, 기능 및 설정을 포함 합니다.
ms.topic: reference
ms.date: 05/05/2020
ms.openlocfilehash: dbbeb4eacbe8e07cf080b3a1527ca39c2b9a5991
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684352"
---
# <a name="app-schema-definition"></a>앱 스키마 정의

LUIS 앱은 또는에 표시 되며 `.json` `.lu` 모든 의도, 엔터티, 예제 길이 발언, 기능 및 설정을 포함 합니다.

## <a name="format"></a>서식

앱을 가져오고 내보낼 때 또는 중 하나를 선택 `.json` `.lu` 합니다.

|서식|정보|
|--|--|
|`.json`| 표준 프로그래밍 형식|
|`.lu`|Bot Framework의 [Bot Builder 도구](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)에서 지원 됩니다.|

## <a name="version-7x"></a>버전 4.x

* 버전 4.x로 이동 하면 엔터티가 중첩 된 기계 학습 엔터티로 표시 됩니다.
* 다음 제작 Api의 속성을 사용 하 여 중첩 된 machine learning 엔터티를 제작할 수 `enableNestedChildren` 있습니다.
    * [레이블 추가](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [일괄 처리 레이블 추가](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [레이블 검토](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [엔터티에 대 한 끝점 쿼리 제안](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [의도에 대 한 끝점 쿼리 제안](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

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
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-6x"></a>버전 6.x

* 버전 6.x로 이동 하 여 새 [machine learning 엔터티](reference-entity-machine-learned-entity.md) 를 사용 하 여 엔터티를 나타냅니다.

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

* [V3 제작 api](luis-migration-authoring-entities.md) 로 마이그레이션