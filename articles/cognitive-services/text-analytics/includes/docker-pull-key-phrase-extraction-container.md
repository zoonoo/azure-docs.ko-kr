---
title: 키 구 추출 컨테이너에 대한 Docker 당김
titleSuffix: Azure Cognitive Services
description: 키 구 추출 컨테이너에 대한 Docker 끌어오기 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877126"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>키 구 추출 컨테이너에 대한 Docker 당김

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft 컨테이너 레지스트리에서 컨테이너 이미지를 다운로드합니다.

텍스트 분석 컨테이너에 사용할 수 있는 태그에 대한 자세한 설명은 Docker Hub의 [핵심 구 추출](https://go.microsoft.com/fwlink/?linkid=2018757) 컨테이너를 참조하십시오.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
