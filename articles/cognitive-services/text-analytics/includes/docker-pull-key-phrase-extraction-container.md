---
title: 핵심 구 추출 컨테이너에 대한 docker pull
titleSuffix: Azure Cognitive Services
description: 핵심 구 추출 컨테이너에 대한 docker pull 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906097"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>핵심 구 추출 컨테이너에 대한 docker pull

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft Container Registry에서 컨테이너 이미지를 다운로드합니다.

Text Analytics 컨테이너에 사용할 수 있는 태그에 대한 전체 설명은 Docker 허브의 [핵심 구 추출](https://go.microsoft.com/fwlink/?linkid=2018757) 컨테이너를 참조하세요.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
