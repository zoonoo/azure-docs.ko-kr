---
title: 데이터 형식 - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: KES(Knowledge Exploration Service) API의 데이터 형식을 알아봅니다.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: f17bc3c0588476fdc4f8414fb020b16718bf1d90
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854798"
---
# <a name="data-format"></a>데이터 형식

데이터 파일은 인덱싱할 개체 목록을 설명합니다.
파일의 각 줄은 UTF-8 인코딩을 사용하여 개체의 특성 값을 [JSON 형식](http://json.org/)으로 지정합니다.
[스키마](SchemaFormat.md)에 정의된 특성 외에도, 각 개체에는 개체 간의 상대 로그 확률을 지정하는 선택적 "logprob" 특성이 있습니다.
서비스에서 개체를 높은 확률에서 낮은 확률 순서로 반환할 때 "logprob"를 사용하여 일치하는 개체의 반환 순서를 나타낼 수 있습니다.
0~1 사이의 확률 *p*가 있을 때 이에 해당하는 로그 확률을 log(*p*)로 계산할 수 있으며, 여기서 log()는 자연 로그 함수입니다.
logprob의 값을 지정하지 않으면 기본값 0이 사용됩니다.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

문자열, GUID 및 Blob 특성의 경우 값이 따옴표로 묶인 JSON 문자열로 표시됩니다.  숫자 특성(Int32, Int64, Double)의 경우 값이 JSON 숫자로 표시됩니다.  복합 특성의 경우 값은 하위 특성 값을 지정하는 JSON 개체입니다.  로그 확률 감소 순으로 개체를 미리 정렬하면 보다 빠르게 인덱스를 빌드할 수 있습니다.

일반적으로 한 특성은 0개 이상의 값을 가질 수 있습니다.  특성 값이 없는 경우 JSON에서 해당 값을 삭제합니다.  특성 값이 2개 이상인 경우 JSON 개체의 특성 값 쌍을 반복할 수 있습니다.  또는 여러 값을 포함하는 JSON 배열을 특성에 할당할 수 있습니다.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
