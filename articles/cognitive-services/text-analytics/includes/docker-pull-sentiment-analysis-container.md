---
title: 감정 분석 컨테이너에 대 한 Docker 풀
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너에 대 한 Docker pull 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 549ecf940c53267db6b3caa576c4c24db414337a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906076"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>감정 분석 v3 컨테이너에 대 한 Docker 풀

감정 분석 컨테이너 v3 컨테이너는 여러 언어로 제공 됩니다. 영어 컨테이너의 컨테이너를 다운로드 하려면 아래 명령을 사용 합니다. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

다른 언어용 컨테이너를 다운로드 하려면를 `en` 아래의 언어 코드 중 하나로 바꿉니다. 

| Text Analytics 컨테이너 | 언어 코드 |
|--|--|
| 영어 | `en` |
| 스페인어 | `es` |
| 프랑스어 | `fr` |
| 이탈리아어 | `it` |
| 독일어 | `de` |
| 중국어-간체 | `zh` |
| 중국어-번체 | `zht` |
| 일본어 | `ja` |
| 포르투갈어 | `pt` |
| 네덜란드어 | `nl` |

Text Analytics 컨테이너의 사용 가능한 태그에 대 한 자세한 설명은 [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654)를 참조 하세요.