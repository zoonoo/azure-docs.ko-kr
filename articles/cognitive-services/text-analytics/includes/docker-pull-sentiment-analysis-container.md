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
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877066"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>감정 분석 컨테이너에 대 한 Docker 풀

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용 하 여 Microsoft Container Registry에서 컨테이너 이미지를 다운로드 합니다.

Text Analytics 컨테이너의 사용 가능한 태그에 대 한 전체 설명은 Docker 허브의 [감정 분석](https://go.microsoft.com/fwlink/?linkid=2018654) 컨테이너를 참조 하세요.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
