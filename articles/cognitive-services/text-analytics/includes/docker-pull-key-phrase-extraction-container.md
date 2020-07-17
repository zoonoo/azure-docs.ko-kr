---
title: 핵심 구 추출 컨테이너에 대 한 Docker 풀
titleSuffix: Azure Cognitive Services
description: 핵심 구 추출 컨테이너에 대 한 Docker pull 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877126"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>핵심 구 추출 컨테이너에 대 한 Docker 풀

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용 하 여 Microsoft Container Registry에서 컨테이너 이미지를 다운로드 합니다.

Text Analytics 컨테이너의 사용 가능한 태그에 대 한 전체 설명은 Docker 허브의 [핵심 구 추출](https://go.microsoft.com/fwlink/?linkid=2018757) 컨테이너를 참조 하세요.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
