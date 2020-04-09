---
title: 감정 분석 컨테이너에 대한 도커 풀
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너에 대한 도커 끌어오기 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877066"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>감정 분석 컨테이너에 대한 도커 풀

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft 컨테이너 레지스트리에서 컨테이너 이미지를 다운로드합니다.

텍스트 분석 컨테이너에 사용할 수 있는 태그에 대한 자세한 설명은 Docker Hub의 [감정 분석](https://go.microsoft.com/fwlink/?linkid=2018654) 컨테이너를 참조하십시오.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
