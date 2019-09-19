---
title: 감정 분석 컨테이너에 대 한 Docker 풀
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너에 대 한 Docker pull 명령
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966797"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>감정 분석 컨테이너에 대 한 Docker 풀

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft 컨테이너 레지스트리에서 컨테이너 이미지를 다운로드합니다.

Text Analytics 컨테이너의 사용 가능한 태그에 대 한 전체 설명은 Docker 허브의 [감정 분석](https://go.microsoft.com/fwlink/?linkid=2018654) 컨테이너를 참조 하세요.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
