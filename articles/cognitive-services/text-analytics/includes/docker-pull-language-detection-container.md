---
title: 언어 감지 컨테이너에 대한 docker pull
titleSuffix: Azure Cognitive Services
description: 언어 감지 컨테이너에 대한 docker pull 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906019"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>언어 감지 컨테이너에 대한 docker pull

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft Container Registry에서 컨테이너 이미지를 다운로드합니다.

Text Analytics 컨테이너에 사용할 수 있는 태그에 대한 전체 설명은 Docker 허브의 [언어 감지](https://go.microsoft.com/fwlink/?linkid=2018759) 컨테이너를 참조하세요.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
