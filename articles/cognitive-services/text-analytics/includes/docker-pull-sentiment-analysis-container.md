---
title: 감정 분석 컨테이너에 대한 docker pull
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너에 대한 docker pull 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 549ecf940c53267db6b3caa576c4c24db414337a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906076"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>감정 분석 v3 컨테이너에 대한 docker pull

감정 분석 컨테이너 v3 컨테이너는 여러 언어로 제공됩니다. 영어 컨테이너용 컨테이너를 다운로드하려면 아래 명령을 사용하세요. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

다른 언어용 컨테이너를 다운로드하려면 `en`을 아래의 언어 코드 중 하나로 바꿉니다. 

| Text Analytics 컨테이너 | 언어 코드 |
|--|--|
| 영어 | `en` |
| 스페인어 | `es` |
| 프랑스어 | `fr` |
| 이탈리아어 | `it` |
| 독일어 | `de` |
| 중국어 - 간체 | `zh` |
| 중국어 - 번체 | `zht` |
| 일본어 | `ja` |
| 포르투갈어 | `pt` |
| 네덜란드어 | `nl` |

Text Analytics 컨테이너에 사용할 수 있는 태그에 대한 전체 설명은 [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654)를 참조하세요.