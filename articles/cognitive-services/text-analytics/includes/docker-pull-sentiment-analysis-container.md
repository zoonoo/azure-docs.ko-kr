---
title: 감정 분석 컨테이너에 대한 도커 풀
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너에 대한 도커 끌어오기 명령
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966797"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>감정 분석 컨테이너에 대한 도커 풀

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft 컨테이너 레지스트리에서 컨테이너 이미지를 다운로드합니다.

텍스트 분석 컨테이너에 사용할 수 있는 태그에 대한 자세한 설명은 Docker Hub의 [감정 분석](https://go.microsoft.com/fwlink/?linkid=2018654) 컨테이너를 참조하십시오.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
